# Sistema de Votação Eletrônica - UE (Unidade Eleitoral)

## Projeto

Sistema de votação eletrônica dividido em dois módulos:  
- **UEv** (Unidade Eleitoral Virtual) – módulo de votação  
- **UEg** (Unidade Eleitoral de Gerenciamento) – módulo de gerenciamento  

---

## Casos de Uso

### UC_01 – Cadastrar UEv

- **Função:** Cadastrar uma Unidade Eleitoral Virtual no sistema.
- **Atores:** Administrador
- **Prioridade:** Essencial
- **Pré-condição:** Administrador autenticado.
- **Pós-condição:** UEv cadastrada com sucesso.

**Fluxo Principal:**  
1. Administrador acessa módulo de cadastro.  
2. Insere dados da UEv.  
3. Sistema valida e salva dados.  
4. Confirma sucesso.

**Fluxos Secundários:**  
- Dados inválidos ou incompletos.  
- Falha na conexão com banco.

---

### UC_02 – Validar UEv

- **Função:** Validar UEv cadastrada para autenticação e funcionamento.
- **Atores:** Administrador
- **Pré-condição:** UEv cadastrada.
- **Pós-condição:** UEv validada e autorizada.

**Fluxo Principal:**  
1. Administrador seleciona UEv.  
2. Sistema verifica e valida.  
3. Confirma validação.

**Fluxos Secundários:**  
- Dados inconsistentes.  
- UEv não encontrada.

---

### UC_03 – Gerenciar Candidatos

- **Função:** Cadastrar, editar e remover candidatos.
- **Atores:** Administrador
- **Pré-condição:** UEv cadastrada e validada.
- **Pós-condição:** Candidatos atualizados.

**Fluxo Principal:**  
1. Administrador acessa módulo de candidatos.  
2. Gerencia candidatos (cargo, nome, apelido, número, foto).  
3. Sistema valida e salva.

**Fluxos Secundários:**  
- Dados inválidos.  
- Exclusão bloqueada para candidatos com votos.

---

### UC_04 – Gerenciar Eleitores

- **Função:** Cadastrar, editar e remover eleitores.
- **Atores:** Administrador
- **Pré-condição:** UEv cadastrada e validada.
- **Pós-condição:** Eleitores atualizados.

**Fluxo Principal:**  
1. Administrador acessa módulo de eleitores.  
2. Gerencia eleitores (número documento, nome, foto opcional).  
3. Sistema valida e salva.

**Fluxos Secundários:**  
- Dados inválidos.  
- Exclusão bloqueada para eleitores que já votaram.

---

### UC_05 – Totalizar Votos

- **Função:** Totalizar votos válidos, brancos, nulos e ausentes.
- **Atores:** Administrador
- **Pré-condição:** UEv com votos registrados.
- **Pós-condição:** Totalização concluída.

**Fluxo Principal:**  
1. Administrador solicita totalização.  
2. Sistema processa votos e contabiliza.  
3. Apresenta totalização.

**Fluxos Secundários:**  
- Dados inconsistentes.  
- Falha no processamento.

---

### UC_06 – Listar Candidatos

- **Função:** Exibir lista de candidatos.
- **Atores:** Administrador
- **Pré-condição:** Candidatos cadastrados.
- **Pós-condição:** Lista exibida.

**Fluxo Principal:**  
1. Sistema recupera candidatos da UEv.  
2. Apresenta lista.

**Fluxo Secundário:**  
- Lista vazia.

---

### UC_07 – Listar Eleitores

- **Função:** Exibir lista de eleitores.
- **Atores:** Administrador
- **Pré-condição:** Eleitores cadastrados.
- **Pós-condição:** Lista exibida.

**Fluxo Principal:**  
1. Sistema recupera eleitores.  
2. Apresenta lista.

**Fluxo Secundário:**  
- Lista vazia.

---

### UC_08 – Gerar Relatórios

- **Função:** Gerar relatórios de votação e participação.
- **Atores:** Administrador
- **Pré-condição:** Dados atualizados.
- **Pós-condição:** Relatórios gerados.

**Fluxo Principal:**  
1. Administrador solicita relatório.  
2. Sistema compila dados.  
3. Apresenta tabelas e gráficos.  
4. Permite salvar/imprimir.

**Fluxo Secundário:**  
- Falha na geração.

---

### UC_09 – Contabilizar Brancos / Nulos / Ausentes

- **Função:** Contabilizar votos brancos, nulos e ausentes.
- **Atores:** Administrador
- **Pré-condição:** Votos registrados.
- **Pós-condição:** Contagem disponível.

**Fluxo Principal:**  
1. Sistema processa votos.  
2. Contabiliza categorias.  
3. Disponibiliza para relatórios.

---

### UC_10 – Confirmar Número de Inscrição

- **Função:** Eleitor confirma inscrição antes de votar.
- **Atores:** Eleitor
- **Pré-condição:** Eleitor cadastrado na UEv.
- **Pós-condição:** Eleitor autenticado.

**Fluxo Principal:**  
1. Eleitor insere número de inscrição.  
2. Sistema verifica cadastro.  
3. Confirma autenticidade.

**Fluxo Secundário:**  
- Número inválido ou não cadastrado.

---

### UC_11 – Votar

- **Função:** Realizar o voto eletrônico.
- **Atores:** Eleitor
- **Pré-condição:** Eleitor autenticado.
- **Pós-condição:** Voto registrado.

**Fluxo Principal:**  
1. Eleitor seleciona candidato(s).  
2. Confirma, altera, vota em branco ou nulo.  
3. Sistema registra voto.  
4. Confirma registro.

**Fluxos Secundários:**  
- Voto duplicado bloqueado.  
- Falha no registro.

---

## Requisitos e Observações Gerais

- Cada UEg suporta até 100 UEv vinculados.  
- UEv deve funcionar autonomamente após carregada pelo UEg.  
- Impedir que eleitor vote duas vezes na mesma ou em diferentes UEv.  
- Suporte para eleições com até 8 cargos por UEv.  
- Informações dos candidatos e eleitores devem ser idênticas em todas UEv vinculadas a um UEg.  
- Resultados apresentados em tabelas e gráficos.  


