

# Diagrama de Sequência – UC_01: Cadastrar UEv

Este diagrama de sequência representa o fluxo principal e o fluxo secundário (dados inválidos ou incompletos) do caso de uso "Cadastrar UEv" do sistema de votação eletrônica.

```mermaid
sequenceDiagram
    actor adm as Administrador
    participant UEg
    participant UEv

    adm ->> UEg: cadastrarUEv(uev: UEv) : void
    activate UEg
    UEg ->> UEv: new()
    activate UEv

    UEg ->> UEv: validarUEv(uev: UEv) : bool
    alt Dados válidos
        UEv -->> UEg: true
        UEg -->> adm: sucesso : bool
    else Dados inválidos ou incompletos
        UEv -->> UEg: false
        UEg -->> adm: erro : bool
    end
    deactivate UEv
    deactivate UEg
```

## Explicação
- O Administrador solicita o cadastro de uma nova UEv ao UEg.
- O UEg instancia a UEv e solicita sua validação.
- Se os dados forem válidos, o fluxo segue normalmente e o administrador recebe confirmação de sucesso.
- Se os dados forem inválidos ou incompletos, o UEg informa o erro ao administrador.
- O diagrama utiliza o bloco `alt` para representar o fluxo alternativo (secundário).

## Diagrama de Sequência – UC_03: Gerenciar Candidatos

Este diagrama de sequência representa o fluxo principal e os fluxos secundários (dados inválidos e exclusão bloqueada) do caso de uso "Gerenciar Candidatos".

```mermaid
sequenceDiagram
    actor Administrador
    participant UEg
    participant Candidato

    Administrador ->> UEg: Administrador acessa módulo de candidatos
    UEg ->> Candidato: gerenciarCandidatos(candidato: Candidato) : void
    UEg ->> Candidato: validaCandidatos(candidato: Candidato) : candidato
    alt Dados válidos e candidato sem votos
        Candidato -->> UEg: candidato válido
        UEg -->> Administrador: Confirmação de sucesso
    else Dados inválidos
        Candidato -->> UEg: erro de validação
        UEg -->> Administrador: Erro - dados inválidos
    else Exclusão bloqueada (candidato possui votos)
        Candidato -->> UEg: bloqueio de exclusão
        UEg -->> Administrador: Erro - candidato possui votos
    end
```

### Explicação
- O Administrador acessa o módulo de candidatos no UEg.
- O UEg solicita a operação de gerenciamento e validação ao objeto Candidato.
- Se os dados forem válidos e o candidato não possuir votos, a operação é confirmada.
- Se os dados forem inválidos, o sistema retorna erro de validação.
- Se a exclusão for bloqueada (candidato possui votos), o sistema retorna erro de bloqueio ao administrador.


## Diagrama de Sequência – UC_04: Gerenciar Eleitores

Este diagrama de sequência representa o fluxo principal e os fluxos secundários (dados inválidos, exclusão bloqueada e UEv inválida) do caso de uso "Gerenciar Eleitores".

```mermaid
sequenceDiagram
    actor adm as Administrador
    participant UEg
    participant UEv
    participant Eleitor

    adm ->> UEg:  acessa módulo de eleitores
    UEg ->> UEv: gerenciarEleitores(eleitor: Eleitor) : void
    activate UEg
    UEg ->> UEv: validarUEv(uev: UEv) : bool
    alt UEv válida
        UEg ->> Eleitor: validaEleitores(eleitor: Eleitor) : eleitor
        alt Dados válidos e eleitor não votou
            Eleitor -->> UEg: eleitor válido
            UEg -->> adm: Confirmação de sucesso
        else Dados inválidos
            Eleitor -->> UEg: erro de validação
            UEg -->> adm: Erro - dados inválidos
        else Exclusão bloqueada (eleitor já votou)
            Eleitor -->> UEg: bloqueio de exclusão
            UEg -->> adm: Erro - eleitor já votou
        end
    else UEv inválida
        UEv -->> UEg: erro
        UEg -->> adm: Erro - UEv inválida
    end
    deactivate UEg
```

### Explicação
- O Administrador acessa o módulo de eleitores no UEg.
- O UEg solicita a operação de gerenciamento ao UEv e valida a UEv.
- Se a UEv for válida, o UEg solicita a validação do eleitor.
- Se os dados do eleitor forem válidos e ele não tiver votado, a operação é confirmada.
- Se os dados forem inválidos, o sistema retorna erro de validação.
- Se a exclusão for bloqueada (eleitor já votou), o sistema retorna erro de bloqueio ao administrador.
- Se a UEv for inválida, o sistema retorna erro de UEv inválida ao administrador.

## Diagrama de Sequência – UC_05: Totalizar Votos

Este diagrama de sequência representa o fluxo principal e os fluxos secundários (dados inconsistentes e falha no processamento) do caso de uso "Totalizar Votos".

```mermaid
sequenceDiagram
    actor adm as Administrador
    participant UEg
    participant UEv
    participant Resultado

    adm ->> UEg: solicita totalização
    activate UEg
    UEg ->> UEv: obterVotos() : Lista
    activate UEv
    alt Lista de votos consistente
        UEv -->> UEg: listaVotos : Lista
        UEg ->> Resultado: totalizarVotos(listaVotos)
        activate Resultado
        Resultado -> Resultado: gerarTabela() : void
        Resultado -> Resultado: gerarGrafico() : void
        Resultado -->> UEg: totalizacao : Resultado
        deactivate Resultado
        UEg -->> adm: Resultado
    else Dados inconsistentes
        UEv -->> UEg: erro de inconsistência
        UEg -->> adm: Erro - dados inconsistentes
    else Falha no processamento
        UEv -->> UEg: listaVotos : Lista
        UEg ->> Resultado: totalizarVotos(listaVotos)
        activate Resultado
        Resultado -->> UEg: erro de processamento
        deactivate Resultado
        UEg -->> adm: Erro - falha no processamento
    end
    deactivate UEv
    deactivate UEg
```

### Explicação
- O Administrador solicita a totalização ao UEg.
- O UEg solicita a lista de votos à UEv.
- Se a lista de votos estiver consistente, o UEg totaliza os votos e apresenta o resultado ao administrador.
- Se houver dados inconsistentes, o sistema retorna erro de inconsistência.
- Se houver falha no processamento, o sistema retorna erro de processamento ao administrador.


## Diagrama de Sequência – UC_06: Listar Candidatos

Este diagrama de sequência representa o fluxo principal e o fluxo alternativo (lista vazia) do caso de uso "Listar Candidatos".

```mermaid
sequenceDiagram
    actor adm as Administrador
    participant UEg
    participant UEv

    adm ->> UEg: solicitar lista de candidatos

    activate UEg
    UEg ->> UEv: obterCandidatos() : Lista
    activate UEv
    alt Lista não vazia
        UEv -->> UEg: listaCandidatos
        UEg -->> adm: Lista de Candidatos
    else Lista vazia
        UEv -->> UEg: listaVazia
        UEg -->> adm: mensagem "Lista de candidatos vazia"
    end
    deactivate UEv
    deactivate UEg
```

### Explicação
- O Administrador solicita a lista de candidatos ao UEg.
- O UEg solicita a lista à UEv.
- Se a lista não estiver vazia, o UEg apresenta a lista ao administrador.
- Se a lista estiver vazia, o UEg informa ao administrador que não há candidatos cadastrados.


## Diagrama de Sequência – UC_07: Listar Eleitores

Este diagrama de sequência representa o fluxo principal e o fluxo alternativo (lista vazia) do caso de uso "Listar Eleitores".

```mermaid
sequenceDiagram
    actor adm as Administrador
    participant UEg
    participant UEv

    adm ->> UEg: solicitar lista de eleitores

    activate UEg
    UEg ->> UEv: obterEleitores() : Lista
    activate UEv
    alt Lista não vazia
        UEv -->> UEg: listaEleitores
        UEg -->> adm: Lista de Eleitores
    else Lista vazia
        UEv -->> UEg: listaVazia
        UEg -->> adm: mensagem "Lista de eleitores vazia"
    end
    deactivate UEv
    deactivate UEg
```

### Explicação
- O Administrador solicita a lista de eleitores ao UEg.
- O UEg solicita a lista à UEv.
- Se a lista não estiver vazia, o UEg apresenta a lista ao administrador.
- Se a lista estiver vazia, o UEg informa ao administrador que não há eleitores cadastrados.