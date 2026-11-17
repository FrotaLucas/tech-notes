
---
title: "Memory, Fine-Tuning, Agents e MCP"
subject: "Inteligência Artificial"
category: "AI Agents"
tags:
  - memory
  - fine-tuning
  - llm
  - ai-agents
  - tool-calling
  - mcp
  - langchain
  - llm-as-a-judge
  - mrr
  - evaluation
level: "intermediário"
created: "2026-11-17"
status: "em andamento"
---

# Memory, Fine-Tuning, Agents e MCP

# PARTE 1 — MEMORY (Memória)

## 1. O que é Memory?

Memory é a capacidade de uma aplicação de IA armazenar,
recuperar e utilizar informações de interações anteriores.

O objetivo é permitir que o agente mantenha continuidade
nas conversas e personalize suas respostas de acordo com
o histórico e as características do usuário.

Sem memória, cada conversa pode começar sem o contexto
das interações anteriores.

**Conceito fundamental:**

Memory = Personalização + Histórico

### 1.1. Tipos de memória

Podemos dividir a memória de um agente em três categorias:

#### A. Memória semântica (Semantic Memory)

Armazena fatos e informações relativamente duradouras
sobre o usuário.

Exemplos:
- Nome do cliente.
- Idioma preferido.
- Cidade onde mora.
- Preferências de atendimento.
- Produtos que costuma comprar.

Objetivo: permitir que o agente conheça informações
importantes sobre o usuário sem precisar perguntar
novamente a cada conversa.

#### B. Memória episódica (Episodic Memory)

Armazena informações relacionadas a experiências e
acontecimentos de conversas anteriores.

Normalmente, pode ser representada por resumos
de conversas ou registros de eventos importantes.

Exemplos:
- O cliente reclamou de um pedido na semana passada.
- O cliente solicitou uma devolução.
- O cliente teve problemas com uma entrega.
- O último atendimento terminou sem resolução.

Objetivo: permitir que o agente recupere o contexto
de interações anteriores.

#### C. Memória procedural (Procedural Memory)

Armazena informações sobre como o agente deve se comportar
e executar determinadas tarefas.

Pode incluir instruções, regras e padrões de interação.

Exemplos:
- Utilizar uma linguagem mais informal.
- Evitar respostas muito longas.
- Chamar o cliente pelo primeiro nome.
- Utilizar determinado vocabulário.
- Seguir um procedimento específico de atendimento.

Objetivo: manter consistência no comportamento do agente.

---

## 2. Como funciona o armazenamento da memória?

Uma arquitetura possível utiliza um banco de dados vetorial
para armazenar informações que precisam ser recuperadas
por similaridade semântica.

O processo pode funcionar da seguinte maneira:

1. O usuário interage com o agente.
2. O sistema identifica informações relevantes.
3. As informações são transformadas em embeddings,
   quando apropriado.
4. Os dados são armazenados com seus respectivos metadados.
5. Em uma nova conversa, o sistema recupera as informações
   relevantes para aquele usuário.
6. O contexto recuperado é disponibilizado ao agente.

### Exemplo de fluxo

```text
       Nova conversa
             |
             v
     Identificar usuário
             |
             v
      Recuperar memória
             |
             v
     Banco de dados vetorial
             |
             v
    Memória semântica,
    episódica e procedural
             |
             v
       Montar contexto
             |
             v
          Enviar
          para LLM
```

**Importante:** não é obrigatório armazenar todos os tipos
de memória em um banco vetorial.

Dependendo da aplicação:
- Bancos relacionais podem armazenar fatos estruturados.
- Bancos vetoriais podem armazenar embeddings e documentos.
- Sistemas de armazenamento convencionais podem guardar
  históricos e resumos.
- Instruções procedurais podem ser mantidas em prompts,
  configurações ou outros mecanismos.

A escolha depende do tipo de informação e da maneira
como ela será recuperada.

---

## 3. Quanto espaço a memória ocupa no banco de dados?

Uma forma de estimar o tamanho do conteúdo textual
é utilizar a quantidade de tokens.

Uma aproximação bastante utilizada é:

**1 token ≈ 4 caracteres em inglês.**

Entretanto, essa proporção varia de acordo com o idioma,
o vocabulário e o conteúdo.

Em português, por exemplo, a quantidade de tokens por
caractere pode ser diferente.

### Exemplo de estimativa

Imagine que o sistema armazene:

- 100 clientes.
- Cada cliente possui 2.000 tokens de memória.

Total:

100 × 2.000 = 200.000 tokens.

Essa quantidade representa o conteúdo textual aproximado,
não o espaço total ocupado no banco de dados.

### O que influencia o armazenamento?

Além do texto, precisamos considerar:

- Dimensionalidade dos embeddings.
- Tipo numérico utilizado para armazenar os vetores.
- Metadados associados.
- Índices vetoriais.
- Estruturas internas do banco de dados.
- Histórico e versões das memórias.

**Exemplo de cálculo de embeddings:**

Imagine um embedding com 1.536 dimensões, utilizando
4 bytes por dimensão (float32).

1.536 × 4 = 6.144 bytes por vetor.

Ou seja, aproximadamente 6 KB por embedding,
sem considerar metadados e índices.

Por isso, a quantidade de tokens ajuda a estimar
o tamanho do texto, mas não representa sozinha
o custo total de armazenamento.

---

# PARTE 2 — MEMORY vs RAG vs FINE-TUNING

## 4. Qual é a diferença entre Memory, RAG e Fine-Tuning?

Essas três técnicas podem ser utilizadas em conjunto,
mas resolvem problemas diferentes.

### 4.1. Memory

Utilizada para armazenar informações sobre o usuário
e suas interações anteriores.

Exemplo:

O cliente prefere receber respostas em português
e costuma comprar produtos eletrônicos.

O agente recupera essas informações e personaliza
o atendimento.

### 4.2. RAG (Retrieval-Augmented Generation)

Utilizado para recuperar informações externas e
disponibilizar conhecimento ao LLM durante a geração
da resposta.

Exemplo:

O cliente pergunta qual é a política de devolução
de um produto.

O sistema consulta a documentação atualizada da loja
e fornece os trechos relevantes ao LLM.

### 4.3. Fine-Tuning

É o processo de adaptar um modelo por meio de treinamento
adicional, atualizando seus parâmetros (pesos).

O objetivo é modificar ou especializar o comportamento
do modelo para determinadas tarefas.

Exemplo:

Uma empresa deseja que seu modelo siga um padrão específico
de classificação de reclamações e utilize uma determinada
estrutura de resposta.

Pode utilizar exemplos de treinamento para adaptar
o comportamento do modelo.

### Comparação

| Técnica | Principal objetivo |
|---|---|
| Memory | Personalização e histórico |
| RAG | Conhecimento externo e atualizado |
| Fine-Tuning | Adaptação do comportamento do modelo |

### Quando utilizar cada técnica?

**Utilize Memory quando:**
- Precisar lembrar preferências dos usuários.
- Precisar recuperar informações de conversas anteriores.
- Quiser personalizar o atendimento.

**Utilize RAG quando:**
- Precisar consultar documentos externos.
- As informações mudarem frequentemente.
- Quiser responder utilizando uma base de conhecimento.

**Utilize Fine-Tuning quando:**
- Precisar adaptar o comportamento do modelo.
- Quiser melhorar o desempenho em uma tarefa específica.
- Precisar padronizar formatos e estilos de resposta.
- Possuir exemplos de treinamento representativos.

### As técnicas podem trabalhar juntas?

Sim!

Uma arquitetura pode utilizar as três simultaneamente:

```text
             Pergunta
                |
                v
          Recuperar Memory
                |
                v
           Consultar RAG
                |
                v
       Construir contexto
                |
                v
       Modelo Fine-Tuned
                |
                v
             Resposta
```

O modelo pode ser previamente ajustado por Fine-Tuning,
receber memórias do usuário e consultar documentos externos
por meio de RAG.

**Conclusão:**

Não é necessário escolher obrigatoriamente apenas uma técnica.
A escolha depende do problema que a aplicação precisa resolver.

---

# PARTE 3 — AGENTS & TOOL CALLING

## 5. O que é um AI Agent?

Um AI Agent é um sistema que utiliza um modelo de linguagem
para interpretar objetivos, tomar decisões e coordenar
a execução de tarefas por meio de ferramentas.

Uma forma simplificada de representar sua arquitetura é:

**AI Agent = LLM + Harness**

O LLM interpreta informações e decide quais ferramentas
podem ser necessárias.

O Harness controla a execução, as ferramentas disponíveis,
as permissões e o fluxo de trabalho.

### 5.1. O que é Tool Calling?

Tool Calling é um mecanismo que permite ao LLM solicitar
a execução de funções ou ferramentas externas.

O LLM não executa diretamente as operações externas.
Ele gera uma solicitação estruturada indicando qual
ferramenta deve ser chamada e quais argumentos utilizar.

A aplicação recebe essa solicitação, executa a função
e devolve o resultado ao modelo.

### Exemplo de funcionamento

Imagine que o cliente pergunte:

"Qual é o status do meu pedido 12345?"

O fluxo pode ser:

1. O usuário envia a pergunta.
2. O LLM identifica que precisa consultar um pedido.
3. O LLM solicita a ferramenta `consultar_pedido`.
4. O sistema executa a função e consulta a API ou o banco.
5. O resultado é devolvido ao LLM.
6. O LLM interpreta o resultado e responde ao cliente.

```text
        Usuário
           |
           v
          LLM
           |
           v
    Solicita ferramenta
    consultar_pedido
           |
           v
    Executar função/API
           |
           v
     Resultado da API
           |
           v
          LLM
           |
           v
     Resposta ao usuário
```

---

## 6. Criando ferramentas com LangChain

O LangChain disponibiliza o decorator `@tool`,
que permite transformar funções Python em ferramentas
que podem ser utilizadas por um agente.

### Exemplo

```python
from langchain.tools import tool

@tool
def consultar_pedido(pedido_id: str) -> str:
    """
    Consulta o status de um pedido utilizando seu ID.
    """
    # Aqui seria realizada a consulta à API ou ao banco.
    return f"Pedido {pedido_id}: em transporte"
```

A função pode ser disponibilizada ao agente.

O LLM recebe informações como:
- Nome da ferramenta.
- Descrição.
- Argumentos necessários.
- Tipos dos argumentos.

Quando identifica que precisa consultar um pedido,
ele pode solicitar a execução dessa ferramenta.

### 6.1. Importância de nomes e descrições das ferramentas

É fundamental deixar claro para o modelo quando
cada ferramenta deve ser utilizada.

O nome e a descrição das funções ajudam o LLM
a selecionar corretamente as ferramentas.

**Exemplo de nomes inadequados:**

```python
def executar_query():
    pass
```

O nome não deixa claro qual operação será realizada.

**Exemplo de nomes adequados:**

```python
def consultar_pedido(pedido_id: str):
    pass
```

O nome comunica claramente a finalidade da função.

### 6.2. Argumentos das ferramentas

Os argumentos devem ser objetivos e bem definidos.

Exemplo:

```python
@tool
def emitir_reembolso(
    pedido_id: str,
    motivo: str
) -> str:
    """
    Solicita um reembolso para um pedido.

    Args:
        pedido_id: Identificador do pedido.
        motivo: Motivo informado pelo cliente.
    """
    return "Solicitação de reembolso registrada."
```

Nesse exemplo, o agente pode extrair o motivo
da solicitação a partir da mensagem do usuário
e preencher o argumento correspondente.

Entretanto, a aplicação deve validar os argumentos
e verificar se o usuário realmente possui autorização
para solicitar o reembolso.

---

## 7. Princípio do menor privilégio (Least Privilege)

Um agente deve possuir somente as permissões necessárias
para executar suas tarefas.

**Nunca disponibilize mais poderes ao agente do que ele
realmente precisa.**

Exemplo:

Um agente responsável por consultar pedidos não precisa
ter acesso para excluir pedidos ou realizar transferências
financeiras.

Boas práticas:

- Limitar as ferramentas disponíveis.
- Definir permissões específicas.
- Validar os argumentos recebidos.
- Restringir o acesso aos dados dos clientes.
- Exigir aprovação humana para operações sensíveis.
- Registrar as operações executadas.

### Exemplo de separação de permissões

| Ferramenta | Permissão |
|---|---|
| `consultar_pedido` | Leitura |
| `consultar_cliente` | Leitura |
| `solicitar_devolucao` | Escrita controlada |
| `emitir_reembolso` | Operação restrita |

O princípio é reduzir os possíveis danos causados
por uma decisão incorreta do agente.

---

# PARTE 4 — MCP (MODEL CONTEXT PROTOCOL)

## 8. O que é MCP?

MCP significa **Model Context Protocol**.

É um protocolo aberto que padroniza a comunicação entre
aplicações de IA e fontes externas de dados e ferramentas.

Uma boa analogia é o HTTP.

Assim como o HTTP padroniza a comunicação entre clientes
e servidores na Web, o MCP padroniza a maneira como
aplicações de IA descobrem e utilizam ferramentas
e recursos externos.

### 8.1. Qual problema o MCP resolve?

Imagine que uma empresa deseja construir um agente
capaz de interagir com:

- Google Drive.
- PostgreSQL.
- GitHub.
- Azure.
- Sistemas internos.
- APIs de terceiros.

Sem um padrão de integração, seria necessário desenvolver
integrações específicas para cada ferramenta e aplicação.

O MCP oferece uma interface padronizada para disponibilizar
essas capacidades aos clientes de IA.

Assim, diferentes aplicações compatíveis com MCP podem
utilizar servidores MCP sem precisar implementar novamente
toda a lógica de comunicação com cada sistema.

### 8.2. Arquitetura do MCP

Os principais componentes são:

**MCP Host:** aplicação que utiliza as capacidades do MCP,
como um ambiente de IA.

**MCP Client:** componente responsável pela conexão
com um servidor MCP.

**MCP Server:** disponibiliza ferramentas, recursos
e outros elementos para o cliente.

### Fluxo simplificado

```text
          Aplicação de IA
             (Host)
                |
                v
            MCP Client
                |
                v
            MCP Server
                |
        +-------+-------+
        |       |       |
        v       v       v
      GitHub PostgreSQL API
```

### 8.3. Como o agente utiliza as ferramentas?

O servidor MCP pode disponibilizar uma ferramenta
com nome, descrição e parâmetros.

Por exemplo:

```text
Nome: consultar_pedido

Descrição:
Consulta o status de um pedido pelo identificador.

Argumentos:
- pedido_id: string
```

O modelo recebe a descrição da ferramenta e pode solicitar
sua utilização quando necessário.

O cliente MCP encaminha a solicitação ao servidor,
que executa a operação e devolve o resultado.

**Importante:** MCP não é um modelo de IA e não substitui
o LLM, o Harness ou o Tool Calling.

Ele é um protocolo de comunicação e integração.

---

## 9. Boas práticas no uso de MCP e Tool Calling

Uma integração eficiente depende de ferramentas bem definidas.

### Recomendações

1. Utilizar nomes claros e objetivos.
2. Escrever descrições que expliquem quando a ferramenta
   deve ser utilizada.
3. Definir argumentos com tipos e descrições precisas.
4. Limitar as permissões de cada ferramenta.
5. Evitar disponibilizar ferramentas desnecessárias.
6. Validar operações sensíveis.
7. Monitorar as chamadas e seus resultados.

### Exemplo de descrição

**Ruim:**

```text
executar_query
```

**Bom:**

```text
consultar_pedido

Consulta o status de um pedido utilizando seu ID.
Utilize esta ferramenta quando o cliente perguntar
sobre entrega, processamento ou situação de um pedido.
```

Quanto mais clara for a descrição, maior a possibilidade
de o agente selecionar corretamente a ferramenta.

---

# PARTE 5 — FLUXO FIXO VS AGENTE

## 10. Quando utilizar um fluxo determinístico ou um agente?

Nem todo problema precisa ser resolvido utilizando
um AI Agent.

Em muitos casos, um fluxo fixo é mais simples,
previsível e econômico.

A escolha depende da complexidade da tarefa
e da necessidade de tomada de decisão dinâmica.

### 10.1. Fluxo fixo (Determinístico)

É uma sequência de etapas previamente definidas.

A aplicação executa as operações seguindo uma ordem
estabelecida, independentemente de decisões do LLM.

Exemplo: processo de checkout.

```text
      Início
         |
         v
  Validar carrinho
         |
         v
  Calcular pagamento
         |
         v
  Confirmar pagamento
         |
         v
  Solicitar aprovação humana
         |
         v
         Fim
```

Vantagens:
- Maior previsibilidade.
- Fácil de testar.
- Controle sobre a execução.
- Menor complexidade.
- Menor necessidade de chamadas ao LLM.

### 10.2. Fluxo baseado em Agente

É indicado quando o sistema precisa decidir dinamicamente
quais ferramentas utilizar e em qual ordem.

Exemplo:

Um cliente entra em contato com uma loja e informa:

"Meu pedido chegou danificado, quero devolver e receber
meu dinheiro de volta."

O agente pode precisar:

1. Consultar o pedido.
2. Verificar o histórico de entrega.
3. Consultar a política de devolução.
4. Identificar a elegibilidade.
5. Solicitar informações adicionais.
6. Encaminhar a solicitação de reembolso.

A sequência pode variar conforme as informações
fornecidas pelo usuário.

### 10.3. Comparação

| Característica | Fluxo fixo | Agente |
|---|---|---|
| Ordem das etapas | Predefinida | Dinâmica |
| Tomada de decisão | Regras programadas | LLM + regras |
| Complexidade | Geralmente menor | Pode ser maior |
| Previsibilidade | Alta | Menor |
| Ferramentas | Definidas no fluxo | Selecionadas conforme necessidade |
| Custo operacional | Geralmente menor | Pode ser maior |

### 10.4. Quando escolher cada abordagem?

**Prefira fluxo fixo quando:**
- Os passos são sempre os mesmos.
- O processo é determinístico.
- As regras são bem definidas.
- Não existe necessidade de decisões complexas.

**Considere um agente quando:**
- A ordem das operações muda conforme o caso.
- Existem muitas ferramentas combinadas.
- O usuário pode solicitar tarefas variadas.
- É necessário interpretar informações não estruturadas.
- A aplicação precisa escolher dinamicamente o próximo passo.

**Observação importante:**

Nem sempre a melhor solução é utilizar um agente.

Às vezes, um fluxo fixo com uma última etapa de aprovação
humana resolve o problema com muito mais simplicidade,
segurança e previsibilidade.

---

# PARTE 6 — AVALIAÇÃO DE RESPOSTAS DE IA

## 11. O que é LLM-as-a-Judge?

LLM-as-a-Judge é uma técnica que utiliza um modelo
de linguagem para avaliar a qualidade das respostas
geradas por outro modelo ou sistema de IA.

Em vez de depender exclusivamente de avaliações humanas,
utilizamos um LLM como avaliador.

O modelo recebe uma pergunta, uma resposta gerada
e critérios de avaliação.

Com base nesses critérios, ele atribui notas
ou fornece uma avaliação estruturada.

### 11.1. Exemplo prático

Pergunta do usuário:

"Posso devolver um celular depois de 20 dias?"

Contexto correto:

"A loja permite a devolução de celulares em até 30 dias,
desde que o produto esteja em boas condições."

Resposta do chatbot:

"Sim, você pode devolver o celular em até 30 dias,
desde que esteja em boas condições."

O LLM-as-a-Judge pode avaliar:

| Critério | Avaliação |
|---|---|
| Relevância | A resposta atende à pergunta? |
| Precisão | A informação está correta? |
| Fidelidade | A resposta respeita o contexto fornecido? |
| Clareza | A resposta é fácil de compreender? |
| Completude | A resposta contém as informações necessárias? |

### 11.2. Como funciona?

```text
       Pergunta do usuário
               |
               v
       Resposta do chatbot
               |
               v
        Contexto original
               |
               v
        LLM-as-a-Judge
               |
               v
       Avaliação da resposta
               |
        +------+------+
        |             |
        v             v
      Nota        Justificativa
```

O avaliador pode retornar, por exemplo:

```json
{
  "relevance": 5,
  "accuracy": 5,
  "faithfulness": 5,
  "clarity": 5,
  "reason": "A resposta está correta e utiliza
  adequadamente as informações do contexto."
}
```

As notas são ilustrativas e podem utilizar
uma escala definida pela aplicação.

### 11.3. Limitações do LLM-as-a-Judge

Apesar de ser útil, essa técnica possui limitações:

- O avaliador também pode cometer erros.
- Pode apresentar vieses.
- Pode favorecer respostas mais longas.
- Pode interpretar critérios de maneira inconsistente.
- Pode sofrer influência da forma como o prompt é escrito.

Por isso, é importante utilizar critérios claros,
conjuntos de testes e validação humana.

---

## 12. O que é MRR (Mean Reciprocal Rank)?

MRR significa **Mean Reciprocal Rank**.

É uma métrica utilizada para avaliar a qualidade
da posição em que um resultado relevante aparece
em uma lista de resultados recuperados.

É especialmente útil em mecanismos de busca,
sistemas de recuperação de documentos e pipelines RAG.

### 12.1. Como funciona?

Imagine que um chatbot realiza uma busca em uma base
de conhecimento para encontrar a resposta de uma pergunta.

O sistema retorna uma lista de documentos ordenados
por relevância.

O MRR verifica em qual posição aparece o primeiro
documento considerado relevante.

Quanto mais alta for a posição desse documento,
melhor será o resultado.

### 12.2. Fórmula do MRR

\[
MRR = \frac{1}{N}
\sum_{i=1}^{N}\frac{1}{rank_i}
\]

Onde:

- `N`: quantidade de consultas avaliadas.
- `rank_i`: posição do primeiro resultado relevante
  para a consulta `i`.
- `1 / rank_i`: Reciprocal Rank (posição recíproca).

### 12.3. Exemplo prático

Imagine três perguntas realizadas ao sistema RAG:

| Pergunta | Posição do primeiro resultado relevante |
|---|---:|
| Pergunta 1 | 1 |
| Pergunta 2 | 2 |
| Pergunta 3 | 4 |

Calculando o Reciprocal Rank:

**Pergunta 1:**

\[
RR_1 = \frac{1}{1} = 1
\]

**Pergunta 2:**

\[
RR_2 = \frac{1}{2} = 0.5
\]

**Pergunta 3:**

\[
RR_3 = \frac{1}{4} = 0.25
\]

Calculando a média:

\[
MRR = \frac{1 + 0.5 + 0.25}{3}
\]

\[
MRR = 0.5833
\]

Resultado:

**MRR = 0,5833 (58,33%)**

### 12.4. Como interpretar o resultado?

O MRR varia entre 0 e 1.

| MRR | Interpretação |
|---|---|
| 1.00 | O primeiro resultado é relevante em todas as consultas |
| 0.80 | Excelente posicionamento médio |
| 0.50 | Os primeiros resultados podem precisar de otimização |
| 0.20 | Os resultados relevantes aparecem em posições mais baixas |
| 0.00 | Nenhuma consulta encontrou resultado relevante |

Essas interpretações são apenas referências ilustrativas.
Não existem limites universais para classificar um MRR
como bom ou ruim.

### 12.5. MRR aplicado ao RAG

Imagine que um usuário pergunte:

"Qual é o prazo para devolver um celular?"

O sistema recupera cinco chunks:

```text
1. Política de devolução de carregadores
2. Política de garantia de celulares
3. Prazo de devolução de celulares
4. Política de devolução de telas
5. Política de entrega
```

O primeiro resultado relevante aparece na posição 3.

Nesse caso:

\[
RR = \frac{1}{3} = 0.3333
\]

O objetivo da otimização é fazer com que os documentos
mais relevantes apareçam nas primeiras posições.

Isso pode ser melhorado por meio de:

- Estratégias de chunking.
- Modelos de embedding.
- Ajustes na busca vetorial.
- Filtros de metadados.
- Re-ranking dos resultados.
- Ajustes no número de resultados recuperados (Top-K).

**Importante:** MRR avalia o posicionamento do primeiro
resultado relevante. Ele não mede diretamente a qualidade
da resposta final produzida pelo LLM.

---

# PARTE 7 — RESUMO GERAL

## 13. Principais conceitos da aula

| Conceito | Responsabilidade |
|---|---|
| Memória semântica | Armazenar fatos duradouros sobre o usuário |
| Memória episódica | Registrar experiências e conversas anteriores |
| Memória procedural | Armazenar padrões e instruções de comportamento |
| RAG | Recuperar conhecimento externo |
| Fine-Tuning | Adaptar os pesos e o comportamento do modelo |
| AI Agent | Modelo integrado a um Harness |
| Tool Calling | Solicitar execução de ferramentas |
| MCP | Padronizar a comunicação com ferramentas e recursos |
| Fluxo fixo | Executar etapas predefinidas |
| LLM-as-a-Judge | Avaliar respostas utilizando um LLM |
| MRR | Medir o posicionamento do primeiro resultado relevante |

---

## 14. Perguntas para revisão

### Memory e Fine-Tuning

1. Qual é a diferença entre memória semântica, episódica e procedural?
2. Por que uma aplicação precisa recuperar memórias ao iniciar uma nova conversa?
3. Como podemos estimar o tamanho de uma memória?
4. Qual é a diferença entre Memory, RAG e Fine-Tuning?
5. Por que Fine-Tuning não substitui necessariamente RAG?

### Agents e Tool Calling

6. O que significa AI Agent = LLM + Harness?
7. Qual é a função do Tool Calling?
8. O LLM executa diretamente as ferramentas?
9. Qual é a finalidade do decorator `@tool` no LangChain?
10. Por que devemos utilizar nomes claros para as funções?
11. O que significa o princípio do menor privilégio?

### MCP e arquitetura

12. O que é MCP?
13. Qual problema o MCP resolve?
14. Qual é a diferença entre MCP e Tool Calling?
15. Quais são os componentes principais da arquitetura MCP?

### Fluxos e avaliação

16. Qual é a diferença entre um fluxo determinístico e um agente?
17. Quando um fluxo fixo pode ser melhor do que um agente?
18. O que é LLM-as-a-Judge?
19. O que é MRR?
20. Como podemos melhorar o MRR de um sistema RAG?
21. Por que um MRR elevado não garante que a resposta final do chatbot esteja correta?

---

## 15. Referências

- [LangChain - Memory](https://docs.langchain.com/)
- [LangChain - Tools](https://docs.langchain.com/oss/python/langchain/tools)
- [Model Context Protocol - Documentation](https://modelcontextprotocol.io/docs/getting-started/intro)
- [OpenAI - Fine-Tuning](https://platform.openai.com/docs/guides/fine-tuning)
- [OpenAI - Evaluation](https://platform.openai.com/docs/guides/evals)
- [LangSmith - Evaluation](https://docs.langchain.com/langsmith/evaluation)
