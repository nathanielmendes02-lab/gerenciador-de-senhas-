# Gerenciador de Senhas de Atendimento Clínico

Programa em C que cadastra pacientes, classifica cada um em uma categoria de atendimento, emite senhas e controla a ordem de chamada.

**aluno:** Nathaniel Mendes de Moraes — Engenharia de Software, UDF
**Proposta escolhida:** Questão 02 — Sistema de Gerenciamento de Senhas de Atendimento Clínico

> As regras de classificação são didáticas, definidas para esta atividade. Não substituem a legislação de atendimento prioritário nem a avaliação clínica.

## Objetivo

Organizar a fila de uma clínica garantindo que pacientes com prioridade sejam chamados primeiro.

## Funcionalidades

- Cadastro de pacientes (gênero, idade, gestante, lactante, necessidade especial e obesidade) com emissão automática de senha.
- Numeração independente por categoria: `U001`, `P001`, `N001`...
- Consulta da próxima senha, chamada do paciente, identificação da senha em atendimento e finalização do atendimento.
- Painel de chamada: senha atual, categoria, próximas senhas e quantidade em espera por categoria.
- Validação de todas as entradas e tratamento de operações inválidas.

### Regras de classificação

| Código | Categoria | Condição |
|---|---|---|
| U | Urgente | Idade superior a 80 anos |
| P | Preferencial | Gestante, lactante, necessidade especial, obesidade, mulher com 60 anos ou mais, homem com 65 anos ou mais |
| N | Normal | Nenhuma das condições acima |

Ordem de chamada: **Urgente > Preferencial > Normal**. Dentro de cada categoria, vale a ordem de emissão (FIFO). Senhas já atendidas não voltam para a fila.

## Compilação e execução

```bash
gcc -o senhas gerenciador_senhas.c
./senhas
```

No Windows: `gcc -o senhas.exe gerenciador_senhas.c` e depois `senhas.exe`. Se os acentos aparecerem errados no terminal, rode `chcp 65001` antes.

## Funções implementadas

| Função | O que faz |
|---|---|
| `main` | Mostra o menu e chama a função da opção escolhida |
| `mostrarMenu` | Exibe o menu |
| `limparBuffer` | Descarta o resto da linha digitada |
| `lerInteiro` | Lê um número dentro de um intervalo, repetindo se for inválido |
| `lerSimNao` | Lê e valida S/N |
| `lerGenero` | Lê e valida F/M |
| `classificar` | Define a categoria (Urgente, Preferencial ou Normal) |
| `cadastrarPaciente` | Coleta os dados, classifica e emite a senha |
| `buscarProxima` | Encontra a próxima senha a ser chamada, respeitando a prioridade |
| `consultarProxima` | Mostra a próxima senha sem chamá-la |
| `chamarPaciente` | Chama a próxima senha e a tira da fila |
| `mostrarEmAtendimento` | Mostra a senha em atendimento |
| `finalizarAtendimento` | Conclui o atendimento atual |
| `contarEspera` | Conta quantos aguardam em uma categoria |
| `mostrarPainel` | Exibe o painel de chamada |

## Exemplos de utilização

**Emissão de senhas**

```
Opção: 1
Gênero (F ou M): M
Idade: 85
Possui necessidade especial? (S/N): n
Possui obesidade? (S/N): n

Senha emitida: U001 (Urgente)
```

**Validação**

```
Gênero (F ou M): X
Gênero inválido! Digite F ou M.
Idade: -3
Valor inválido! Digite um número de 0 a 120.
```

**Painel (cadastrados: N001, P001, P002, U001, N002)**

```
===== PAINEL DE CHAMADA =====
Senha chamada: nenhuma
Próximas senhas: U001 P001 P002 N001 N002
Em espera - Urgente: 1 | Preferencial: 2 | Normal: 2
=============================
```

**Chamada e finalização**

```
Opção: 3
Chamando a senha U001 (Urgente)!
Opção: 3
A senha U001 ainda está em atendimento. Finalize-a primeiro.
Opção: 5
Atendimento da senha U001 finalizado.
Opção: 3
Chamando a senha P001 (Preferencial)!
```

## Observações

- Há um guichê de atendimento: só é possível chamar a próxima senha depois de finalizar a atual.
- Gestante e lactante só são perguntadas quando o gênero é feminino.
- A fila comporta até 200 senhas aguardando ao mesmo tempo.
