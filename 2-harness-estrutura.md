---
title: "Harness e LangGraph"
subject: "Inteligência Artificial"
category: "AI Agents"
tags:
  - harness
  - llm
  - ai-agents
  - langgraph
  - state-management
  - human-in-the-loop
  - postgres
level: "intermediário"
created: "2026-11-06"
status: "em andamento"
---

# Harness e LangGraph

## 1. O que é um Harness?

**Harness** é a estrutura que envolve o LLM e fornece funcionalidades
necessárias para que ele possa operar dentro de uma aplicação.

Em outras palavras:

> Harness é tudo aquilo que o LLM não faz sozinho.

O LLM é responsável principalmente por interpretar informações e
gerar respostas em linguagem natural. O Harness controla o ambiente,
o fluxo de execução e a interação do modelo com outros componentes.

### Responsabilidades do Harness

- **Tomada de decisão sobre o fluxo:** determina qual caminho a aplicação
  deve seguir com base nas respostas do LLM e nas regras definidas.
- **Memória e histórico:** armazena e recupera informações e histórico
  das conversas com o usuário.
- **Busca de informações:** consulta bancos de dados, APIs e outras
  fontes externas quando o LLM não possui as informações necessárias.
- **Guardrails:** verifica e controla o que entra e sai do modelo,
  aplicando regras de segurança e validação.
- **Orquestração:** coordena ferramentas, serviços e etapas da execução.

### Conceito fundamental

Um agente de IA pode ser entendido, de forma simplificada, como:

**AI Agent = LLM + Harness**

O LLM gera e interpreta conteúdo, enquanto o Harness fornece
memória, ferramentas, controle e capacidade de execução.

---

## 2. Identificação das conversas: thread_id

Cada conversa possui um identificador único chamado `thread_id`.

Esse identificador permite reconhecer uma sessão específica e
recuperar seu histórico e estado.

### Como funciona?

1. O usuário inicia uma conversa.
2. A aplicação cria ou recebe um `thread_id`.
3. As mensagens e os estados são associados a esse identificador.
4. Quando o usuário retorna, a aplicação utiliza o `thread_id`
   para recuperar o contexto anterior.
5. O atendimento pode continuar de onde parou.

O conteúdo das conversas pode ser persistido em um banco de dados,
como PostgreSQL.

**Importante:** o `thread_id` funciona como uma chave de identificação
da conversa. Ele não armazena as mensagens por si só.

---

## 3. LangGraph e fluxo de estados

O LangGraph é um framework utilizado para construir aplicações
de IA baseadas em grafos e fluxos de execução com estado.

Em vez de executar apenas uma sequência linear de instruções,
a aplicação pode tomar diferentes caminhos dependendo do estado
atual e das condições definidas.

### 3.1. Conceito de State Graph

Um grafo de estados é composto principalmente por:

- **State (Estado):** contém os dados e informações disponíveis
  durante a execução.
- **Nodes (Nós):** representam etapas ou operações executadas
  pelo sistema.
- **Edges (Arestas):** definem as conexões e os caminhos possíveis
  entre os nós.
- **Conditional Edges:** permitem escolher dinamicamente o próximo
  nó com base em condições do estado.

### Exemplo conceitual

```text
          Início
             |
             v
      Identificar intenção
             |
       +-----+------+
       |            |
       v            v
    Pedidos      Devolução
       |            |
       v            v
  Consultar      Verificar
   pedido       elegibilidade
       |            |
       +-----+------+
             |
             v
       Responder usuário
             |
             v
             Fim
```

---

## 4. Vantagens do LangGraph para E-commerce

### 4.1. Human-in-the-Loop (Intervenção Humana)

Permite interromper a execução automática e solicitar a intervenção
de um operador humano.

**Exemplo:**

Um cliente tenta informar o endereço de entrega, mas o chatbot
não consegue compreendê-lo após duas tentativas.

O sistema pode:

1. Incrementar o contador `tentativas` no State.
2. Verificar se o limite de tentativas foi atingido.
3. Executar um `interrupt` para pausar o fluxo.
4. Transferir o atendimento para um operador humano.
5. Aguardar a resolução do problema.
6. Retomar a execução utilizando o mesmo estado.

Isso também pode ser utilizado quando o cliente demonstra
insatisfação ou solicita atendimento humano.

### 4.2. Persistência e Checkpoints

O LangGraph permite salvar checkpoints do estado durante
a execução do grafo.

**Exemplo:**

Um cliente adiciona produtos ao carrinho, inicia o checkout,
mas fecha o navegador antes de concluir a compra.

Quando retornar três horas depois:

1. O sistema identifica a conversa pelo `thread_id`.
2. Recupera o checkpoint persistido.
3. Restaura o estado anterior.
4. Recupera as informações do carrinho.
5. Continua o processo de checkout.

Isso evita que o usuário precise começar novamente.

**Conceito importante:** o mecanismo de persistência permite
recuperar o estado de execução, enquanto o `thread_id` identifica
a conversa à qual esse estado pertence.

### 4.3. Resiliência a Erros (Loops de Correção)

Diferentemente de um fluxo estritamente linear, o LangGraph
permite retornar a etapas anteriores quando ocorre algum problema.

**Exemplo:**

Durante o checkout, a API de pagamento retorna um erro ou
recusa o cartão.

O fluxo pode:

1. Identificar a falha no pagamento.
2. Atualizar o estado para `payment_failed`.
3. Informar o usuário sobre o problema.
4. Redirecionar a execução para o nó de pagamento.
5. Solicitar novos dados de pagamento.
6. Tentar novamente.

O grafo pode repetir etapas até que o problema seja resolvido
ou até que uma condição de encerramento seja atingida.

---

## 5. Estrutura do State

O State é o objeto que mantém as informações compartilhadas
entre os nós durante a execução do grafo.

### Exemplo de propriedades

| Propriedade | Descrição |
|---|---|
| `cliente_id` | Identificador do cliente |
| `conversa_id` | Identificador da conversa |
| `resumo` | Resumo do histórico da conversa |
| `tentativas` | Quantidade de tentativas realizadas |
| `cliente_nome` | Nome do cliente |
| `intencao` | Intenção identificada pelo sistema |

### Exemplo de implementação conceitual

```python
from typing import TypedDict

class Estado(TypedDict):
    cliente_id: str
    conversa_id: str
    resumo: str
    tentativas: int
    cliente_nome: str
    intencao: str
```

A propriedade `intencao` pode receber valores como:

- `devolucao`
- `pedidos`
- `reclamacao`

### Por que a propriedade tentativas é importante?

O contador `tentativas` permite controlar quantas vezes
o sistema tentou executar determinada operação.

Ele é especialmente útil para implementar o conceito
de Human-in-the-Loop.

**Exemplo de regra:**

```python
if state["tentativas"] >= 2:
    # Interromper o fluxo e solicitar intervenção humana
    interrupt("Atendimento humano necessário")
```

Dessa forma, o sistema consegue identificar quando não está
conseguindo resolver uma solicitação automaticamente.

---

## 6. Persistência com PostgreSQL

O PostgreSQL pode ser utilizado para armazenar o histórico
das conversas e os checkpoints do LangGraph.

Uma arquitetura simplificada seria:

```text
        Usuário
           |
           v
       Chatbot
           |
           v
         LLM
           |
           v
        Harness
           |
           v
       LangGraph
           |
           v
      PostgreSQL
       /       \
      v         v
  Histórico  Checkpoints
  de chats   dos estados
```

### Recuperação de uma conversa

A partir do `thread_id`, a aplicação consegue localizar
os dados persistidos e recuperar o contexto necessário
para continuar a solicitação do usuário.

É importante distinguir:

- **Histórico:** mensagens trocadas entre usuário e chatbot.
- **Checkpoint:** estado da execução do grafo em determinado momento.
- **Thread ID:** identificador que permite associar esses dados
  à mesma conversa.

---

## 7. Resumo dos conceitos

| Conceito | Responsabilidade |
|---|---|
| LLM | Interpretar informações e gerar respostas |
| Harness | Controlar e orquestrar a execução do agente |
| AI Agent | LLM integrado a um Harness |
| Thread ID | Identificar uma conversa |
| State | Armazenar os dados da execução |
| Node | Executar uma etapa do fluxo |
| Edge | Conectar os nós |
| Conditional Edge | Definir caminhos condicionais |
| Checkpoint | Salvar um estado da execução |
| Human-in-the-Loop | Permitir intervenção humana |
| PostgreSQL | Persistir histórico e checkpoints |

---

## 8. Perguntas para revisão

1. Qual é a diferença entre um LLM e um Harness?
2. Por que um AI Agent precisa de um Harness?
3. Qual é a função do `thread_id`?
4. Qual é a diferença entre State, Node e Edge?
5. Como o LangGraph implementa Human-in-the-Loop?
6. O que acontece quando um usuário retorna a uma conversa
   após algumas horas?
7. Qual é a diferença entre histórico de mensagens e checkpoint?
8. Como o contador `tentativas` pode controlar o fluxo de execução?

---

## 9. Referências

- [LangGraph - Documentação oficial](https://docs.langchain.com/oss/python/langgraph/overview)
- [LangGraph - Persistence](https://docs.langchain.com/oss/python/langgraph/persistence)
- [LangGraph - Human-in-the-loop](https://docs.langchain.com/oss/python/langgraph/interrupts)
- [PostgreSQL - Documentação oficial](https://www.postgresql.org/docs/)
