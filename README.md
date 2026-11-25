# Gerenciador de Senhas de Atendimento Clínico

Sistema em **linguagem C** para informatizar a triagem e a organização das filas de uma clínica de especialidades médicas. O atendente cadastra o paciente, o sistema classifica automaticamente a categoria de atendimento, emite uma senha sequencial e controla a ordem de chamada respeitando as prioridades.

> **Aluno:** Nathaniel Mendes de Moraes — Engenharia de Software, UDF
> **Proposta escolhida:** Questão 02 — Sistema de Gerenciamento de Senhas de Atendimento Clínico

> ⚠️ As categorias e faixas etárias são **regras didáticas** definidas para esta atividade. Não reproduzem integralmente a legislação de atendimento prioritário nem substituem a avaliação clínica de risco.

---

## Objetivo

Registrar pacientes, classificá-los conforme as regras de prioridade da atividade e controlar a fila de atendimento, garantindo que:

- senhas **urgentes** sejam chamadas antes das **preferenciais**, e estas antes das **normais**;
- dentro de cada categoria seja respeitada a ordem de emissão (**FIFO**);
- uma senha já atendida **nunca volte** para a fila.

## Funcionalidades

| Módulo | O que faz |
|---|---|
| 1 – Registro de senhas | Coleta gênero, idade, gestante (se feminino), lactante (se feminino), necessidade especial e obesidade; classifica e emite a senha. |
| 2 – Classificação | Define a categoria (Urgente / Preferencial / Normal) e gera numeração **independente** por categoria (`U001`, `P001`, `N001`...). |
| 3 – Registro de atendimento | Consulta a próxima senha, chama o paciente, identifica a senha em atendimento, finaliza o atendimento e atualiza a fila. |
| 4 – Painel de chamada | Mostra a senha chamada, a categoria, as próximas senhas e a quantidade de pacientes em espera por categoria (além dos atendidos). |

### Regras de classificação

| Código | Categoria | Condição |
|---|---|---|
| `U` | Urgente | Idade **superior a 80 anos** |
| `P` | Preferencial | Gestante, lactante, necessidade especial, obesidade, **mulher com 60 anos ou mais** ou **homem com 65 anos ou mais** |
| `N` | Normal | Nenhuma das condições acima |

**Precedência:** Urgente > Preferencial > Normal. Se o paciente atende a mais de uma regra, vale a categoria de maior prioridade (ex.: gestante de 90 anos recebe senha `U`).

### Decisões de projeto

- As perguntas de **gestante** e **lactante** só são feitas quando o gênero informado é feminino ("quando aplicável").
- Há **um guichê de atendimento**: não é possível chamar outra senha enquanto a atual não for finalizada.
- Cada categoria emite até **999 senhas** por execução (`001` a `999`); ao atingir o limite, o sistema avisa e não emite nova senha dessa categoria.
- A idade aceita vai de 0 a 130 anos. Gênero aceita `F` ou `M`; as respostas de sim/não aceitam `S` ou `N` (maiúsculas ou minúsculas).

## Estrutura do repositório

```
gerenciador-senhas-clinica/
├── src/
│   └── gerenciador_senhas.c     # código-fonte completo
├── exemplos/
│   ├── entrada_demo.txt         # respostas usadas na demonstração
│   └── saida_demo.txt           # saída gerada pela demonstração
├── testes/
│   └── rodar_testes.sh          # 29 testes automatizados
├── Makefile
├── .gitignore
└── README.md
```

## Compilação e execução

Requisito: um compilador C (GCC/MinGW, Clang...). O código segue o padrão C11 e compila sem avisos com `-Wall -Wextra -pedantic`.

### Linux / macOS / WSL

```bash
git clone https://github.com/SEU-USUARIO/gerenciador-senhas-clinica.git
cd gerenciador-senhas-clinica

make                # compila  -> ./gerenciador_senhas
./gerenciador_senhas
```

Sem `make`:

```bash
gcc -std=c11 -Wall -Wextra -pedantic -o gerenciador_senhas src/gerenciador_senhas.c
./gerenciador_senhas
```

### Windows (MinGW / GCC)

```bat
gcc -std=c11 -Wall -Wextra -pedantic -o gerenciador_senhas.exe src/gerenciador_senhas.c
gerenciador_senhas.exe
```

O programa ativa UTF-8 no console automaticamente para exibir os acentos. Em IDEs como Code::Blocks, basta abrir o arquivo `src/gerenciador_senhas.c` e compilar.

### Demonstração e testes

```bash
make demo    # roda uma sessão completa com as entradas de exemplos/entrada_demo.txt
make test    # executa os testes automatizados
make clean   # remove os executáveis
```

## Como usar

Ao iniciar, o programa exibe o menu:

```
==========================================
   GERENCIAMENTO DE SENHAS - CLÍNICA
==========================================
 1 - Registrar paciente (emitir senha)
 2 - Consultar próxima senha
 3 - Chamar próximo paciente
 4 - Identificar senha em atendimento
 5 - Finalizar atendimento
 6 - Exibir painel de chamada
 0 - Encerrar
------------------------------------------
```

Fluxo típico: **1** (repetir para cada paciente) → **3** (chamar) → **4** (conferir quem está em atendimento) → **5** (finalizar) → **3** de novo, e assim por diante. A opção **6** mostra o painel a qualquer momento.

## Funções implementadas

| Função | Responsabilidade |
|---|---|
| `main` | Coordena o fluxo: exibe o menu, lê a opção e delega a execução. |
| `inicializar_sistema` | Zera filas, contadores e o atendimento atual. |
| `executar_opcao` | Direciona a opção do menu para a função correspondente. |
| **Entrada e validação** | |
| `ler_linha` | Lê uma linha com segurança (`fgets`), descarta excesso e detecta fim da entrada. |
| `normalizar_texto` | Remove espaços das pontas e converte para maiúsculas. |
| `ler_inteiro` | Lê e valida um inteiro dentro de um intervalo (repete até ser válido). |
| `ler_sim_nao` | Lê e valida resposta S/N. |
| `ler_genero` | Lê e valida o gênero (F/M). |
| **Cadastro e classificação** | |
| `coletar_dados_paciente` | Faz as perguntas do cadastro ao atendente. |
| `possui_prioridade` | Verifica as condições de atendimento preferencial. |
| `classificar_paciente` | Define a categoria respeitando a precedência U > P > N. |
| `gerar_codigo_senha` | Monta o código (`U001`, `P002`, `N003`...). |
| `descrever_motivos` | Explica por que o paciente recebeu aquela categoria. |
| `registrar_paciente` | Cadastra, classifica, emite a senha e a insere na fila. |
| **Filas (FIFO circular)** | |
| `fila_vazia` | Informa se a fila está vazia. |
| `fila_enfileirar` | Insere um paciente no fim da fila. |
| `fila_desenfileirar` | Retira o paciente do início da fila. |
| `fila_consultar` | Espia uma posição da fila sem remover. |
| **Atendimento** | |
| `localizar_proxima_categoria` | Descobre de qual fila sai a próxima senha (por prioridade). |
| `total_em_espera` | Soma os pacientes aguardando em todas as filas. |
| `consultar_proxima_senha` | Mostra a próxima senha sem chamá-la. |
| `chamar_proximo_paciente` | Registra a chamada e inicia o atendimento. |
| `identificar_senha_em_atendimento` | Exibe os dados da senha em atendimento. |
| `finalizar_atendimento` | Conclui o atendimento e atualiza a fila e o painel. |
| **Apresentação** | |
| `exibir_menu` | Mostra o menu principal. |
| `exibir_senha_emitida` | Mostra a senha recém-emitida, a categoria e o motivo. |
| `exibir_painel` | Mostra o painel de chamada. |
| `exibir_resumo_final` | Mostra o resumo ao encerrar. |

### Estruturas de dados

- `enum Categoria` — `CAT_URGENTE`, `CAT_PREFERENCIAL`, `CAT_NORMAL` (a ordem do `enum` é a ordem de prioridade).
- `struct Paciente` — código da senha, categoria, gênero, idade e as condições cadastradas.
- `struct Fila` — fila circular de capacidade fixa (início + tamanho).
- `struct Sistema` — três filas (uma por categoria), contadores de senhas emitidas e atendidas, e o paciente em atendimento.

## Exemplos de utilização

A saída completa da demonstração está em [`exemplos/saida_demo.txt`](exemplos/saida_demo.txt). Trechos:

**Emissão de senhas (classificação automática)**

```
Gênero (F = feminino, M = masculino): M
Idade (0 a 130): 70
Possui necessidade especial? (S/N): n
Possui condição de obesidade? (S/N): n

  >>> SENHA EMITIDA: P001 <<<
  Categoria: Preferencial
  Motivo: homem com 65 anos ou mais
```

```
Gênero (F = feminino, M = masculino): M
Idade (0 a 130): 85
Possui necessidade especial? (S/N): n
Possui condição de obesidade? (S/N): n

  >>> SENHA EMITIDA: U001 <<<
  Categoria: Urgente
  Motivo: idade superior a 80 anos
```

**Validação de dados**

```
Gênero (F = feminino, M = masculino): X
  [!] Gênero inválido. Digite F ou M.
Idade (0 a 130): abc
  [!] Entrada inválida. Digite um número inteiro entre 0 e 130.
Idade (0 a 130): -3
  [!] Valor fora do intervalo. Digite um número entre 0 e 130.
```

**Painel com cinco senhas na fila (a urgente é chamada primeiro)**

```
+----------------------------------------+
|           PAINEL DE CHAMADA            |
+----------------------------------------+
 Senha chamada : ---- (nenhuma)
 Categoria     : ----
 Próximas      : U001, P001, P002, N001, N002
 Em espera     : Urgente: 1 | Preferencial: 2 | Normal: 2 | Total: 5
 Atendidos     : Urgente: 0 | Preferencial: 0 | Normal: 0
+----------------------------------------+
```

**Chamada, operação inválida e finalização**

```
--- Chamada de paciente ---
Chamando a senha U001 (Urgente).
...
--- Chamada de paciente ---
  [!] A senha U001 ainda está em atendimento. Finalize-o antes de chamar o próximo.
...
--- Finalizar atendimento ---
Atendimento da senha U001 finalizado.
Fila atualizada. Próxima senha: P001
```

## Validação e testes

O script `testes/rodar_testes.sh` (`make test`) executa 29 casos automatizados, todos aprovados:

| Grupo | Casos cobertos |
|---|---|
| Classificação | limites de idade (homem 64/65, mulher 59/60, 80/81), gestante, lactante, necessidade especial, obesidade, precedência do urgente sobre o preferencial |
| Numeração | contadores independentes por categoria |
| Ordem de chamada | U antes de P e N, P antes de N, FIFO dentro da categoria, próxima senha após finalizar |
| Operações inválidas | chamar sem pacientes, chamar com atendimento aberto, finalizar sem atendimento, senha atendida não volta à fila |
| Entrada de dados | opção de menu inválida, gênero inválido, idade negativa / acima de 130 / não numérica, resposta S/N inválida, fim inesperado da entrada |

Também foram feitas execuções com `-fsanitize=address,undefined` (sem erros) e um teste de estresse emitindo 1000 senhas normais (o limite de 999 é respeitado e a fila circular funciona após dar a volta).

## Licença

Projeto acadêmico, sem fins comerciais.
