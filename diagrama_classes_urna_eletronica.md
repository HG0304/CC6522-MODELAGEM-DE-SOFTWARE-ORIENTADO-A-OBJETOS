# Diagrama de Classes - Urna Eletrônica

Este documento apresenta o diagrama de classes em **Mermaid** referente ao sistema de **Urna Eletrônica (UE)**, conforme os requisitos do projeto.

## Diagrama de Classes

```mermaid
classDiagram
    class UEg {
        - id : int
        - listaUEv : List<UEv>
        - listaCandidatos : List<Candidato>
        - listaEleitores : List<Eleitor>
        + cadastrarUEv(uev: UEv) : void
        + validarUEv(uev: UEv) : bool
        + disponibilizarListas() : void
        + totalizarVotos() : Resultado
        + gerarRelatorio() : void
    }

    class UEv {
        - id : int
        - listaCandidatos : List<Candidato>
        - listaEleitores : List<Eleitor>
        - votos : List<Voto>
        + autenticarEleitor(eleitor: Eleitor) : bool
        + selecionarCandidato(num: int) : void
        + confirmarVoto() : void
        + votoBranco() : void
        + votoNulo() : void
        + enviarResultados() : void
    }

    class Eleitor {
        - documento : String
        - nome : String
        - foto : String
        - jaVotou : bool
        + autenticar(documento: String) : bool
    }

    class Candidato {
        - cargo : String
        - nome : String
        - apelido : String
        - numero : int
        - foto : String
    }

    class Voto {
        - tipo : String  <<confirmado, branco, nulo>>
        - candidato : Candidato
        - eleitor : Eleitor
        + registrar() : void
    }

    class Resultado {
        - votosCandidatos : Map<Candidato, int>
        - votosBrancos : int
        - votosNulos : int
        - ausentes : List<Eleitor>
        + gerarTabela() : void
        + gerarGrafico() : void
    }

    %% Relações
    UEg "1" --> "0..100" UEv : gerencia
    UEg "1" --> "*" Candidato : disponibiliza
    UEg "1" --> "*" Eleitor : cadastra
    UEv "1" --> "*" Candidato : carrega
    UEv "1" --> "*" Eleitor : valida
    UEv "1" --> "*" Voto : registra
    Voto "*" --> "1" Eleitor : pertence
    Voto "*" --> "0..1" Candidato : refere-se
    UEg "1" --> "1" Resultado : totaliza
```

## Descrição das Classes

### UEg (Unidade de Gerenciamento)
- **Atributos:** id, listaUEv, listaCandidatos, listaEleitores
- **Métodos:** cadastrarUEv(), validarUEv(), disponibilizarListas(), totalizarVotos(), gerarRelatorio()

### UEv (Unidade de Votação)
- **Atributos:** id, listaCandidatos, listaEleitores, votos
- **Métodos:** autenticarEleitor(), selecionarCandidato(), confirmarVoto(), votoBranco(), votoNulo(), enviarResultados()

### Eleitor
- **Atributos:** documento, nome, foto, jaVotou
- **Métodos:** autenticar()

### Candidato
- **Atributos:** cargo, nome, apelido, numero, foto

### Voto
- **Atributos:** tipo, candidato, eleitor
- **Métodos:** registrar()

### Resultado
- **Atributos:** votosCandidatos, votosBrancos, votosNulos, ausentes
- **Métodos:** gerarTabela(), gerarGrafico()
