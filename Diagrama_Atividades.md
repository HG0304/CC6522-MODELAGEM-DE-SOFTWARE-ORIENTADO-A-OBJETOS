# 🧩 Projeto  – Diagrama de Atividades

### 📘 Contexto Geral

O sistema **Urna Eletrônica (UE)** foi desenvolvido como parte do projeto de **Modelagem de Software Orientada a Objetos (CC6522)**.
O sistema é composto por dois módulos principais:

* **UEg (Unidade Eleitoral de Gerenciamento):** responsável pela administração, cadastro e totalização dos votos.
* **UEv (Unidade Eleitoral Virtual):** responsável pela execução da votação, autenticação de eleitores e registro de votos.

O objetivo dos **diagramas de atividades** é representar de forma **visual e lógica** o **fluxo de execução das ações**, as **decisões** e os **pontos de interação** entre os atores (Administrador e Eleitor) e o sistema.

Cada diagrama está associado a um **caso de uso (UC)** definido previamente no projeto.

---

## 🧱 Estrutura do Projeto

| Módulo               | Descrição                             | Responsável   | Atores Principais |
| -------------------- | ------------------------------------- | ------------- | ----------------- |
| **UEg**              | Gerenciamento e controle do sistema   | Administrador | Administrador     |
| **UEv**              | Execução da votação                   | Eleitor       | Eleitor           |
| **Voto / Resultado** | Armazenamento e totalização dos votos | Sistema       | Administrador     |

---

```mermaid
flowchart LR
    subgraph Administrador
        A1[Solicitar operações admin]
    end
    subgraph UEg
        U1[Processar solicitação]
        U2[Gerenciar metadata das UEv]
    end
    subgraph UEv
        V1[Executar votação]
        V2[Registrar votos]
    end
    subgraph Eleitor
        E1[Autenticar]
        E2[Votar]
    end

    A1 --> U1
    U1 --> U2
    U2 --> V1
    E1 --> V1
    V1 --> V2
    V2 --> U1
```

---

## UC_01 — Cadastrar UEv (Diagrama Completo)

```mermaid
flowchart TD
  subgraph Administrador
    A_Start([Início])
    A_Open["Acessa módulo de cadastro (UEg)"]
    A_Fill["Preenche dados da UEv"]
  end

  subgraph Sistema_UEg [UEg]
    G_Validate["Validar dados da UEv"]
    G_Save["Salvar UEv no repositório"]
    G_Confirm["Confirmar sucesso p/ Administrador"]
    G_Error["Registrar erro / log"]
  end

  A_Start --> A_Open --> A_Fill --> G_Validate
  G_Validate -- "dados válidos" --> G_Save --> G_Confirm --> A_End([Fim])
  G_Validate -- "dados inválidos" --> G_Error --> A_Notify["Exibir erro p/ administrador"] --> A_End
```
**Descrição:**

* O administrador acessa o módulo de cadastro no UEg.
* Insere os dados da nova unidade virtual (UEv).
* O sistema valida as informações e realiza o salvamento.
* Caso os dados estejam incorretos, uma mensagem de erro é exibida.


---

## UC_02 — Votar (Diagrama Completo com Swimlanes: Eleitor / UEv / Voto / UEg)

```mermaid
flowchart TD
  subgraph Eleitor
    E_Start([Início])
    E_Insert["Inserir nº inscrição / autenticar"]
    E_Select["Selecionar candidato / optar branco/nulo"]
    E_Confirm["Confirmar escolha"]
  end

  subgraph UEv
    V_Auth["Autenticar eleitor"]
    V_CheckVote["Verificar se já votou"]
    V_ProcessChoice["Validar número do candidato"]
    V_Register["Criar objeto Voto (emEdição)"]
    V_Save["Registrar voto (Voto.registrar)"]
    V_Reply["Confirmar p/ eleitor"]
  end

  subgraph Resultado
    R_Process["(opcional) Bufferizar voto p/ envio ao UEg"]
  end

  E_Start --> E_Insert --> V_Auth
  V_Auth -- "autenticado" --> V_CheckVote
  V_Auth -- "não autenticado" --> E_Error["Exibir erro de autenticação"] --> E_End([Fim])

  V_CheckVote -- "já votou" --> E_Error2["Bloquear voto duplicado"] --> E_End
  V_CheckVote -- "não votou" --> E_Select --> V_ProcessChoice

  V_ProcessChoice -- "válido" --> V_Register --> E_Confirm --> V_Save --> V_Reply --> E_End
  V_ProcessChoice -- "branco" --> V_RegisterBranco["Voto.tipo = 'branco'"] --> V_Save
  V_ProcessChoice -- "nulo" --> V_RegisterNulo["Voto.tipo = 'nulo'"] --> V_Save

  V_Save --> R_Process
  R_Process --> UEg_Buffer["(envio assíncrono ou em lote) UEg.recebeVotos"]
```

**Descrição:**

* O eleitor se autentica no terminal (UEv).
* Escolhe um candidato ou opta por voto branco/nulo.
* O sistema registra o voto e confirma a operação.

**Observações técnicas:**

* A autenticação deve ser extremamente rápida; a ação de registrar pode ocorrer localmente na UEv e ser enviada ao UEg para agregação.
* O sistema deve garantir **idempotência** e prevenir duplicidade em cenários distribuídos.

---

## UC_03 — Gerenciar Candidatos (Completo com decisão de exclusão)

```mermaid
flowchart TD
  subgraph Administrador
    A_Open["Acessa módulo de candidatos"]
    A_Action["Adicionar / Editar / Remover"]
  end

  subgraph UEg
    G_Validate["Validar dados do candidato"]
    G_CheckVotes["Verificar se candidato possui votos"]
    G_Save["Salvar/Atualizar cadastro"]
    G_BlockRemove["Bloquear exclusão e notificar"]
  end

  A_Open --> A_Action --> G_Validate
  G_Validate -- "inválidos" --> A_ShowError["Exibir erro ao admin"] --> End
  G_Validate -- "válidos" --> G_CheckVotes
  G_CheckVotes -- "possui votos" --> G_BlockRemove --> A_ShowError
  G_CheckVotes -- "sem votos" --> G_Save --> A_ShowSuccess["Exibir confirmação"]

  classDef endnode fill:#f9f,stroke:#333,stroke-width:1px;
  End([Fim])
```
**Descrição:**

* O administrador pode cadastrar, editar ou remover candidatos.
* O sistema realiza a validação dos dados e verifica se o candidato possui votos.
* Se o candidato já tiver votos, sua exclusão é bloqueada.
* 
**Pontos importantes:** exclusão só é permitida quando candidato sem votos; edição é permitida, mas mudanças no número que identifique o candidato podem ser restritas.

---

## UC_04 — Gerenciar Eleitores (Completo com validações e bloqueios)

```mermaid
flowchart TD
  subgraph Administrador
    A_Open["Acessa módulo de eleitores"]
    A_Action["Adicionar / Editar / Remover eleitor"]
  end

  subgraph UEg
    G_ValidateUEv["Validar UEv vinculada"]
    G_ValidateData["Validar dados do eleitor"]
    G_CheckVoted["Verificar se eleitor já votou"]
    G_Save["Persistir alterações"]
    G_BlockRemove["Bloquear exclusão e notificar admin"]
  end

  A_Open --> A_Action --> G_ValidateUEv
  G_ValidateUEv -- "inválida" --> A_ShowError["Exibir erro de UEv"] --> End
  G_ValidateUEv -- "válida" --> G_ValidateData
  G_ValidateData -- "inválidos" --> A_ShowError2["Exibir erro de validação"] --> End
  G_ValidateData -- "válidos" --> G_CheckVoted
  G_CheckVoted -- "já votou" --> G_BlockRemove --> A_ShowError3
  G_CheckVoted -- "não votou" --> G_Save --> A_ShowSuccess

  End([Fim])
```
**Descrição:**

* O administrador acessa o módulo de gerenciamento de eleitores.
* A UEv vinculada é validada antes da atualização dos registros.
* O sistema impede a exclusão de eleitores que já tenham votado.

---

## UC_05 — Totalizar Votos (Concorrência: totalizar + gerar relatórios)

```mermaid
flowchart TD
  subgraph Administrador
    Adm_Request["Solicita totalização"]
  end

  subgraph UEg
    U_GetVotes["Solicitar votos (obterVotos()) às UEv"]
    U_Verify["Verificar consistência dos dados"]
    U_Fork["Bifurcação: processar + gerar relatórios"]
    U_Join["Unir resultados e entrega" ]
    U_Error["Erro: inconsistência ou falha de processamento"]
  end

  subgraph Resultado
    R_Process["Processar tabela de contagem"]
    R_Graph["Gerar gráficos e tabelas"]
  end

  Adm_Request --> U_GetVotes
  U_GetVotes -->|lista recebida| U_Verify
  U_Verify -- "consistente" --> U_Fork
  U_Verify -- "inconsistente" --> U_Error --> Adm_NotifyError["Notificar admin"]

  U_Fork --> R_Process
  U_Fork --> R_Graph
  R_Process --> U_Join
  R_Graph --> U_Join
  U_Join --> Adm_Deliver["Entregar relatório/resultados"]

  U_Join --> End([Fim])
```

**Descrição:**

* O administrador solicita ao UEg a totalização dos votos.
* O sistema verifica se a lista de votos é consistente.
* Caso positivo, os votos são processados e o relatório é gerado.
* Em caso de inconsistência, uma mensagem de erro é exibida.


**Observação:** bifurcação demonstra execução concorrente de tarefas CPU/IO bound (processamento de contagem e geração de gráficos). A união sincroniza ambas antes da entrega final.

---

## UC_06 — Listar Candidatos (Simples)

```mermaid
flowchart TD
  subgraph Administrador
    Adm_Request["Solicitar lista de candidatos"]
  end

  subgraph UEv
    V_GetList["Obter lista de candidatos"]
  end

  Adm_Request --> V_GetList
  V_GetList -- "lista vazia" --> Adm_Empty["Exibir 'Sem candidatos'"] --> End
  V_GetList -- "lista não vazia" --> Adm_Show["Exibir lista ao admin"] --> End

  End([Fim])
```
**Descrição:**

* O administrador requisita a listagem de candidatos.
* A UEv retorna os dados e o sistema exibe as informações ou informa que não há candidatos cadastrados.

---

## UC_07 — Listar Eleitores (Simples)

```mermaid
flowchart TD
  subgraph Administrador
    Adm_Request["Solicitar lista de eleitores"]
  end

  subgraph UEv
    V_GetList["Obter lista de eleitores"]
  end

  Adm_Request --> V_GetList
  V_GetList -- "lista vazia" --> Adm_Empty["Exibir 'Sem eleitores'"] --> End
  V_GetList -- "lista não vazia" --> Adm_Show["Exibir lista ao admin"] --> End

  End([Fim])
```
**Descrição:**

* O administrador pode visualizar todos os eleitores cadastrados em uma UEv.
* Caso a lista esteja vazia, o sistema exibe uma mensagem de aviso.

---

## UC_08 — Gerar Relatórios (Detalhado com check de sucesso)

```mermaid
flowchart TD
  subgraph Administrador
    Adm_Request["Solicitar relatório"]
  end

  subgraph UEg
    U_Compile["Compilar dados (votos, candidatos, ausentes)"]
    U_Generate["Gerar tabelas e gráficos"]
    U_Save["Salvar/Exportar relatório (PDF/CSV)"]
    U_Error["Registrar e notificar erro"]
  end

  Adm_Request --> U_Compile --> U_Generate
  U_Generate -- "sucesso" --> U_Save --> Adm_Notify["Relatório pronto"] --> End
  U_Generate -- "falha" --> U_Error --> Adm_NotifyError --> End

  End([Fim])
```
**Descrição:**

* O administrador solicita relatórios de votação e participação.
* O sistema compila dados de candidatos e votos, gerando gráficos e tabelas.
* Caso ocorra erro de geração, o sistema exibe uma mensagem de falha.
---

## UC_09 — Contabilizar Brancos / Nulos / Ausentes (Fluxo detalhado)

```mermaid
flowchart TD
  subgraph Administrador
    Adm_Request["Solicitar contagem de categorias"]
  end

  subgraph UEg
    U_GetVotes["Obter lista de votos"]
    U_CheckEmpty["Verificar se lista vazia"]
    U_Count["Contabilizar brancos, nulos e válidos"]
    U_Match["Identificar ausentes comparando listaEleitores"]
    U_Show["Exibir contagem final"]
  end

  Adm_Request --> U_GetVotes --> U_CheckEmpty
  U_CheckEmpty -- "votos inexistentes" --> Adm_NoVotes["Exibir 'Sem votos para contabilizar'"] --> End
  U_CheckEmpty -- "votos existentes" --> U_Count --> U_Match --> U_Show --> End

  End([Fim])
```
* O sistema contabiliza votos brancos, nulos e identifica eleitores ausentes.
* Caso não haja votos registrados, uma mensagem de aviso é exibida.
---

## UC_10 — Confirmar Número de Inscrição (Eleitor)

```mermaid
flowchart TD
  subgraph Eleitor
    E_Input["Inserir nº de inscrição"]
  end

  subgraph UEv
    V_Auth["Verificar nº no cadastro"]
    V_Response["Resposta: válido / inválido"]
  end

  E_Input --> V_Auth
  V_Auth -- "válido" --> E_Confirm["Confirmar autenticidade ao eleitor"] --> End
  V_Auth -- "inválido" --> E_Error["Exibir erro de inscrição"] --> End

  End([Fim])
```
**Descrição:**

* O eleitor insere seu número de inscrição.
* O sistema valida o número e autentica o eleitor.
* Se o número for inválido, é exibida uma mensagem de erro.

---

# 🔎 Observações finais e critérios de entrega

1. **Swimlanes:** cada diagrama identifica claramente o responsável pela ação (Administrador / UEg / UEv / Eleitor / Resultado).
2. **Concorrência:** a totalização demonstra bifurcação e união (processamento paralelo + sincronização).
3. **Decisões e guardas:** todas as decisões críticas possuem guardas (ex.: "autenticado", "inválido", "consistente").
4. **Fluxos de objeto:** onde aplicável, mencionei objetos-chave (listaVotos, relatório, Voto).
5. **Requisitos não-funcionais relevantes:** idempotência no registro de votos; persistência local da UEv; limites por UEg (<=100 UEv).

---

