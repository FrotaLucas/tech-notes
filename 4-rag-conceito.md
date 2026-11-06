---
title: "RAG - Retrieval-Augmented Generation"
subject: "Inteligência Artificial"
category: "AI Agents"
tags:
  - rag
  - retrieval-augmented-generation
  - llm
  - embeddings
  - vector-database
  - chunking
  - semantic-search
  - etl
level: "intermediário"
created: "2026-11-06"
status: "em andamento"
---

# RAG - Retrieval-Augmented Generation

## 1. O que é RAG?

RAG significa **Retrieval-Augmented Generation** (Geração
Aumentada por Recuperação).

É uma técnica utilizada para fornecer informações relevantes
ao LLM antes de ele gerar uma resposta.

Em vez de enviar documentos inteiros para o modelo, o sistema
busca apenas os trechos que possuem maior relação com a pergunta
do usuário.

### Como funciona?

O processo pode ser dividido em três etapas principais:

1. **Retrieval (Recuperação):** buscar os trechos de documentos
   que possuem maior relação com a pergunta do usuário.
2. **Augmentation (Aumento):** adicionar os trechos recuperados
   ao prompt, junto com a pergunta original.
3. **Generation (Geração):** enviar o prompt ao LLM para que ele
   produza uma resposta utilizando o contexto recuperado.

### Fluxo simplificado

```text
Pergunta do usuário
        |
        v
Busca de informações relevantes
        |
        v
Recuperação dos melhores trechos
        |
        v
Adicionar trechos ao prompt
        |
        v
Enviar prompt para o LLM
        |
        v
Gerar resposta
```

**Conceito fundamental:**

O RAG permite que um LLM utilize informações externas e
específicas de uma aplicação, sem precisar enviar todo o
conteúdo de uma base de conhecimento a cada solicitação.

---

## 2. Processo de indexação (Indexing)

A indexação é uma etapa de preparação dos documentos para
que possam ser pesquisados posteriormente.

Ela pode ser entendida como um processo semelhante a um ETL
(Extract, Transform, Load).

O objetivo é transformar documentos em representações vetoriais
que possam ser armazenadas e pesquisadas em um banco de dados
vetorial.

### 2.1. Etapas da indexação

O processo pode ser dividido em quatro etapas:

**1. Extract (Extração)**

Obter os documentos que serão utilizados como fonte de conhecimento.

Exemplo:
- Política de devolução de uma loja.
- Política de garantia.
- Manual de produtos.
- FAQ de atendimento.

**2. Transform (Transformação)**

Preparar os documentos e dividir seu conteúdo em partes menores,
chamadas de `chunks`.

Exemplo: uma política de loja pode ser dividida em:

- Política de celulares.
- Política de telas.
- Prazo de devolução.
- Política de carregadores.
- Condições para devolução.

**3. Embedding (Vetorização)**

Cada chunk é enviado para um modelo de embedding, que transforma
seu conteúdo em uma representação numérica chamada vetor.

**4. Load (Armazenamento)**

Os vetores gerados são armazenados em um banco de dados vetorial,
normalmente acompanhados do texto original e de seus metadados.

### Fluxo de indexação

```text
       Documento original
        (Política da loja)
               |
               v
           Extração
               |
               v
         Divisão em chunks
               |
       +-------+-------+
       |       |       |
       v       v       v
     Chunk  Chunk   Chunk
       1       2       3
       |       |       |
       v       v       v
     Embedding Model
       |       |       |
       v       v       v
    Vetor 1 Vetor 2 Vetor 3
       |       |       |
       +-------+-------+
               |
               v
      Banco de dados vetorial
```

**Importante:** a indexação normalmente acontece antes
das perguntas dos usuários. Assim, os documentos já estão
preparados quando uma consulta é realizada.

---

## 3. O que são Chunks?

Chunks são pequenos segmentos de texto extraídos de um documento.

A divisão dos documentos em chunks é uma das etapas mais
importantes de uma implementação RAG.

### Exemplo prático

Imagine que uma loja possui a seguinte política:

"Os clientes podem devolver celulares em até 30 dias após
a compra, desde que o produto esteja em boas condições.
Carregadores possuem um prazo de devolução de 15 dias.
Produtos com telas danificadas não são elegíveis para
devolução, exceto em casos de defeito de fabricação."

Esse documento poderia ser dividido em três chunks:

**Chunk 1 - Celulares**

"Os clientes podem devolver celulares em até 30 dias após
a compra, desde que o produto esteja em boas condições."

**Chunk 2 - Carregadores**

"Carregadores possuem um prazo de devolução de 15 dias."

**Chunk 3 - Telas**

"Produtos com telas danificadas não são elegíveis para
devolução, exceto em casos de defeito de fabricação."

### Por que dividir os documentos?

- Evita enviar documentos inteiros ao LLM.
- Reduz a quantidade de tokens utilizados.
- Facilita encontrar informações específicas.
- Melhora a relevância do contexto recuperado.
- Reduz custos de processamento.

### Cuidados com a divisão

A estratégia de chunking influencia diretamente a qualidade
das respostas.

É importante considerar:

- Tamanho adequado de cada chunk.
- Separação por assunto e contexto.
- Preservação de informações relacionadas.
- Possibilidade de sobreposição entre chunks (overlap).
- Preservação de títulos e referências ao documento original.

Chunks muito grandes podem trazer informações desnecessárias.
Chunks muito pequenos podem perder o contexto necessário
para responder corretamente a uma pergunta.

---

## 4. O que são Embeddings?

Embeddings são representações numéricas de informações,
como textos, imagens ou outros tipos de conteúdo.

No contexto do RAG, um modelo de embedding transforma um texto
em um vetor numérico.

Esse vetor representa características semânticas do conteúdo,
permitindo comparar o significado aproximado de diferentes textos.

### Exemplo conceitual

Considere duas frases:

- "Qual é o prazo para devolver um celular?"
- "Por quantos dias posso solicitar a devolução de um smartphone?"

Embora utilizem palavras diferentes, as frases possuem
significados semelhantes.

Os embeddings permitem representar essas frases em um espaço
vetorial, onde suas posições podem ficar próximas.

### Representação simplificada

```text
Texto original:
"Qual é o prazo para devolver um celular?"

             |
             v

       Modelo de Embedding

             |
             v

Vetor numérico:
[0.21, 0.85, -0.32, 0.14, ...]

             |
             v

       Banco vetorial
```

O vetor acima é apenas ilustrativo. Na prática, os modelos
podem gerar vetores com centenas ou milhares de dimensões.

---

## 5. Banco de dados vetorial (Vector Database)

Um banco de dados vetorial armazena e permite pesquisar vetores
com base em sua proximidade em um espaço multidimensional.

No RAG, ele é utilizado para encontrar os chunks que possuem
maior similaridade semântica com a pergunta do usuário.

### Como funciona a busca?

1. A pergunta do usuário é transformada em um embedding.
2. O vetor da pergunta é enviado ao banco de dados vetorial.
3. O banco compara esse vetor com os vetores armazenados.
4. São retornados os vetores mais próximos.
5. O sistema recupera os textos originais associados aos vetores.

### Similaridade vetorial

A proximidade entre os vetores pode ser calculada utilizando
métricas como:

- Cosine Similarity (similaridade de cosseno).
- Euclidean Distance (distância euclidiana).
- Dot Product (produto escalar).

Essas métricas ajudam a determinar quais documentos possuem
maior relação semântica com a pergunta.

---

## 6. Processo de Retrieval (Recuperação)

Depois que os documentos foram indexados, o sistema está pronto
para receber perguntas dos usuários.

O processo de recuperação utiliza os embeddings para encontrar
os chunks mais relevantes.

### Exemplo prático de E-commerce

O usuário pergunta:

"Posso devolver meu celular depois de 20 dias?"

**Etapa 1 - Gerar embedding da pergunta**

A pergunta é enviada ao mesmo modelo de embedding utilizado
durante a indexação.

**Etapa 2 - Consultar o banco vetorial**

O vetor da pergunta é utilizado para pesquisar os vetores
armazenados.

**Etapa 3 - Recuperar os chunks relevantes**

O banco retorna, por exemplo, o seguinte conteúdo:

"Os clientes podem devolver celulares em até 30 dias após
a compra, desde que o produto esteja em boas condições."

**Etapa 4 - Recuperar o texto original**

O sistema obtém o conteúdo original associado ao vetor.

**Etapa 5 - Construir o prompt**

O trecho recuperado é adicionado à pergunta original.

Exemplo:

```text
Você é um assistente de atendimento de uma loja.

Utilize o contexto abaixo para responder à pergunta.
Se a informação não estiver disponível no contexto,
informe que não foi possível localizar a resposta.

CONTEXTO:
Os clientes podem devolver celulares em até 30 dias
após a compra, desde que o produto esteja em boas condições.

PERGUNTA:
Posso devolver meu celular depois de 20 dias?
```

**Etapa 6 - Enviar para o LLM**

O LLM recebe o prompt e gera uma resposta baseada
nas informações recuperadas.

Resposta esperada:

"Sim! De acordo com nossa política, você pode devolver
seu celular em até 30 dias após a compra, desde que
o produto esteja em boas condições."

---

## 7. Otimização de um sistema RAG

Existem diversos pontos que influenciam a qualidade,
o desempenho e o custo de uma implementação RAG.

### 7.1. Escolha do modelo de Embedding

O modelo de embedding é responsável por transformar
os chunks e as perguntas em vetores.

Um cuidado importante é utilizar modelos compatíveis
durante a indexação e a recuperação.

**Regra fundamental:**

Os documentos e as perguntas devem ser vetorizados em
um espaço de embeddings compatível.

Isso significa utilizar o mesmo modelo e a mesma configuração
de vetorização para ambos os processos.

Não é suficiente escolher modelos com quantidades semelhantes
de parâmetros.

É necessário observar:

- Compatibilidade entre os modelos.
- Dimensionalidade dos vetores.
- Espaço vetorial utilizado.
- Configurações específicas do modelo.
- Qualidade da representação semântica.

A quantidade de parâmetros de um modelo pode ser relevante
para avaliar seu tamanho e capacidade, mas não determina,
sozinha, sua compatibilidade com outro modelo.

### 7.2. Estratégia de Chunking

A maneira como os documentos são divididos influencia
diretamente os resultados da busca.

Uma estratégia inadequada pode fazer com que o sistema
recupere informações incompletas ou irrelevantes.

Boas práticas:

- Dividir documentos respeitando a estrutura e o assunto.
- Evitar separar informações que dependem umas das outras.
- Utilizar títulos e metadados para preservar o contexto.
- Testar diferentes tamanhos de chunks.
- Avaliar o uso de overlap entre segmentos.

**Exemplo:**

Uma política de devolução deve preservar a relação entre
o tipo de produto, o prazo e as condições necessárias
para realizar a devolução.

Não adianta recuperar apenas o prazo de 30 dias se a
condição de devolução estiver em outro chunk e for necessária
para responder corretamente.

### 7.3. Redução de custos

Uma das principais vantagens do RAG é evitar que documentos
inteiros sejam enviados ao LLM a cada pergunta.

Em vez disso, o sistema recupera somente os trechos relevantes.

Isso pode proporcionar:

- Menor consumo de tokens.
- Redução dos custos de inferência.
- Menor quantidade de contexto enviado ao LLM.
- Respostas mais focadas.
- Melhor escalabilidade.

---

## 8. Arquitetura completa de um RAG

Podemos dividir a arquitetura em dois grandes processos:

### Processo A - Indexação (offline)

```text
Documentos
    |
    v
Extração de conteúdo
    |
    v
Chunking
    |
    v
Modelo de Embedding
    |
    v
Vetores + Metadados
    |
    v
Banco de dados vetorial
```

### Processo B - Consulta (online)

```text
Pergunta do usuário
    |
    v
Modelo de Embedding
    |
    v
Vetor da pergunta
    |
    v
Banco de dados vetorial
    |
    v
Busca por similaridade
    |
    v
Recuperação dos chunks
    |
    v
Construção do prompt
    |
    v
LLM
    |
    v
Resposta ao usuário
```

---

## 9. RAG versus envio de documentos completos

Imagine que uma loja possui um documento de política
com 100 páginas.

Uma abordagem simples seria enviar o documento inteiro
ao LLM junto com cada pergunta.

Entretanto, essa estratégia apresenta alguns problemas:

- Consumo elevado de tokens.
- Aumento dos custos.
- Maior quantidade de informações irrelevantes.
- Possibilidade de exceder a janela de contexto do modelo.

Com RAG, o sistema recupera apenas os trechos relacionados
à pergunta.

| Característica | Documento completo | RAG |
|---|---|---|
| Contexto enviado | Documento inteiro | Trechos relevantes |
| Consumo de tokens | Potencialmente alto | Geralmente menor |
| Busca semântica | Não necessariamente | Sim |
| Escalabilidade | Mais limitada | Melhor para grandes bases |
| Preparação inicial | Menor | Exige indexação |

**Conclusão:** RAG é uma abordagem eficiente para conectar
LLMs a bases de conhecimento externas e reduzir a necessidade
de enviar grandes volumes de informação a cada interação.

---

## 10. Perguntas para revisão

1. O que significa RAG?
2. Quais são as três etapas fundamentais do RAG?
3. O que acontece durante o processo de indexação?
4. Por que a indexação pode ser considerada um processo semelhante a ETL?
5. O que são chunks e por que precisamos deles?
6. Qual é a função de um modelo de embedding?
7. O que é um banco de dados vetorial?
8. Como o sistema encontra os trechos mais relevantes?
9. Qual é a diferença entre indexação e retrieval?
10. Por que é importante utilizar modelos de embedding compatíveis?
11. Quais problemas podem surgir ao dividir documentos em chunks inadequados?
12. Como o RAG ajuda a reduzir os custos de utilização de LLMs?

---

## 11. Referências

- [LangChain - Retrieval](https://docs.langchain.com/oss/python/langchain/retrieval)
- [LangChain - RAG](https://docs.langchain.com/oss/python/langchain/rag)
- [OpenAI - Embeddings](https://platform.openai.com/docs/guides/embeddings)
- [Pinecone - What is a Vector Database?](https://www.pinecone.io/learn/vector-database/)
