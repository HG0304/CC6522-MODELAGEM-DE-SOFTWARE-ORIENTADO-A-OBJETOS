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
        + totalizarVotos(lista : ListaVotos) : Resultado
        + gerarRelatorio() : void
        + gerenciarCandidatos(candidato: Candidato) : void
        + gerenciarEleitores(eleitor: Eleitor) : void
        + validaCandidatos(candidato: Candidato) : Candidato
        + validaEleitores(eleitor: Eleitor) : Eleitor
        // Observação: getters de votos/candidatos/eleitores moved to UEv
    }

    class UEv {
        - id : int
        - listaCandidatos : List<Candidato>
        - listaEleitores : List<Eleitor>
        - votos : List<Voto>
        + autenticarEleitor(eleitor: Eleitor) : bool
        + iniciarVotacao() : void
        + selecionarCandidato(num: int) : void
        + confirmarVoto() : void
        + votoBranco() : void
        + votoNulo() : void
        + registrarVoto(voto: Voto) : void
        + enviarResultados() : void
        + obterVotos() : List<Voto>
        + obterCandidatos() : List<Candidato>
        + obterEleitores() : List<Eleitor>
    }

    class Eleitor {
        - documento : String
        - nome : String
        - foto : String
        - jaVotou : bool
        + autenticar(documento: String) : bool
        // Note: Eleitor não persiste voto diretamente; ele aciona confirmarVoto()
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
        // Voto.registrar() persiste o voto; UEv agrupa e envia resultados
    }

    class Resultado {
        - votosCandidatos : Map<Candidato, int>
        - votosBrancos : int
        - votosNulos : int
        - ausentes : List<Eleitor>
        + processar(lista : List<Voto>) : void
        + contabilizarBrancos() : int
        + contabilizarNulos() : int
        + identificarAusentes(listaEleitores: List<Eleitor>) : List<Eleitor>
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
    Voto "1..8" --> "1" Eleitor : pertence
    Voto "*" --> "0..1" Candidato : refere-se
    UEg "1" --> "1" Resultado : totaliza
```

## Descrição das Classes

### UEg (Unidade de Gerenciamento)
- **Atributos:** id, listaUEv, listaCandidatos, listaEleitores
- **Métodos:** cadastrarUEv(), validarUEv(), disponibilizarListas(), totalizarVotos(), gerarRelatorio()

### UEv (Unidade de Votação)
- **Atributos:** id, listaCandidatos, listaEleitores, votos
- **Métodos:** autenticarEleitor(), iniciarVotacao(), selecionarCandidato(), confirmarVoto(), votoBranco(), votoNulo(), registrarVoto(), enviarResultados(), obterVotos(), obterCandidatos(), obterEleitores()

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
- **Métodos:** processar(), contabilizarBrancos(), contabilizarNulos(), identificarAusentes(), gerarTabela(), gerarGrafico()
