---
title: "Workshop de Infraestrutura de IA"
type: "workshop"
topic: "Infraestrutura de IA"
speaker: "Anwar Hermuche"
speaker_role: "Engenheiro de IA"
speaker_linkedin: "https://www.linkedin.com/in/anwarhermuche/en"
date: 2026-10-28
tags:
  - inteligencia-artificial
  - llm
  - infraestrutura
  - engenharia-de-prompts
  - embeddings
status: "notes"
---

# Workshop de Infraestrutura de IA

## Informações do Workshop

- **Palestrante:** Anwar Hermuche
- **Cargo:** Engenheiro de IA (AI Engineer)
- **Tema:** Infraestrutura de IA
- **LinkedIn:** [Anwar Hermuche](https://www.linkedin.com/in/anwarhermuche/en)

## 1. Reduzindo o Uso de LLMs

Ao desenvolver uma aplicação de IA, procure evitar o uso de LLMs sempre que uma solução mais simples e econômica puder atender à solicitação.

Utilize um LLM apenas quando outras abordagens não forem suficientes.

Alternativas possíveis:
- Lógica baseada em regras
- Classificadores
- Roteadores
- Código tradicional da aplicação

**Objetivo:** reduzir os custos operacionais e tornar as despesas mais previsíveis, substituindo chamadas desnecessárias a LLMs por componentes mais simples e determinísticos.

> Observação: substituir custos variáveis de inferência de LLMs por custos fixos depende da arquitetura alternativa. Infraestrutura, hospedagem e manutenção ainda podem gerar custos.

## 2. Tokens de Saída e Confiança

As respostas dos LLMs podem incluir informações sobre probabilidades dos tokens, dependendo do modelo e da API utilizada.

- **Tokens de saída:** unidades de texto geradas pelo modelo durante a resposta.
- **Probabilidades dos tokens:** alguns modelos disponibilizam log-probabilidades que indicam a probabilidade de determinados tokens na distribuição do modelo.

É importante distinguir a probabilidade de um token da correção de uma resposta inteira. Uma resposta pode parecer convincente e ainda conter informações incorretas.

## 3. Guardrails

Guardrails são mecanismos que ajudam a controlar como os usuários interagem com uma aplicação de IA e quais ações a aplicação pode executar.

Objetivos comuns:
- Impedir a divulgação não autorizada de dados sensíveis.
- Reduzir abusos e usos indevidos da aplicação.
- Restringir solicitações fora do propósito previsto para a aplicação.
- Aplicar políticas de validação de entradas e saídas.

Os guardrails podem incluir regras de validação, controles de acesso, filtros de conteúdo e verificações baseadas em modelos de IA.

## 4. Classificadores

Um classificador categoriza a solicitação do usuário para ajudar a aplicação a decidir como processá-la.

Exemplos de categorias:
- Pergunta técnica
- Solicitação de suporte ao cliente
- Solicitação envolvendo dados sensíveis
- Pergunta fora do escopo da aplicação

A classificação permite selecionar um fluxo de processamento apropriado antes de chamar um LLM.

## 5. Roteadores

Um roteador direciona cada solicitação para o componente ou modelo mais adequado.

Por exemplo:
- Perguntas simples → modelos menores ou mais baratos.
- Perguntas complexas → modelos mais avançados.
- Solicitações previsíveis → lógica determinística da aplicação.
- Solicitações restritas → fluxo de validação ou recusa.

**Objetivo:** equilibrar qualidade das respostas, latência e custos, selecionando o caminho de processamento mais adequado para cada solicitação.

## 6. Embeddings

Embeddings transformam textos em vetores numéricos em um espaço multidimensional.

Esses vetores podem ser comparados para estimar a similaridade semântica entre frases ou documentos.

Aplicações comuns:
- Busca semântica
- Retrieval-Augmented Generation (RAG)
- Localização de documentos relacionados
- Correspondência entre perguntas dos usuários e informações relevantes

Textos com significados semelhantes podem produzir vetores próximos, dependendo do modelo de embedding e da métrica de similaridade utilizada.

## 7. Histórico de Conversas com LangChain

O LangChain oferece componentes que ajudam a gerenciar o histórico de conversas e integrá-lo ao processamento de uma aplicação de IA.

O histórico permite manter o contexto das interações anteriores e produzir respostas mais relevantes para o usuário.

### Gerenciamento do uso de tokens

Conversas longas podem aumentar o tamanho do contexto enviado ao modelo e, consequentemente, os custos de inferência.

Uma estimativa aproximada, utilizada principalmente para textos em inglês, é:

`Tokens estimados ≈ número de caracteres / 4`

Essa regra é apenas uma aproximação. A quantidade real de tokens varia de acordo com o idioma, o conteúdo e o tokenizador utilizado.

Um fluxo de trabalho prático:
1. Estimar ou contar os tokens da conversa.
2. Verificar o limite de contexto do modelo.
3. Estimar o custo dos tokens de entrada e da resposta esperada.
4. Manter apenas o histórico relevante quando apropriado.
5. Resumir mensagens antigas quando a conversa ficar muito longa.

**Importante:** resumir uma conversa pode reduzir o uso de tokens, mas o resumo deve preservar preferências relevantes do usuário, decisões tomadas e informações necessárias para manter o contexto.

## 8. Arquivos de Prompts Modularizados

Armazene os prompts em arquivos separados, em vez de incorporar todas as instruções diretamente ao código da aplicação.

Isso facilita a manutenção, os testes e a personalização dos prompts para diferentes tarefas e áreas de negócio.

### Exemplo de estrutura

```text
prompts/
├── technical-assistant.txt
└── customer-support.txt
```

### Exemplos de prompts

**`technical-assistant.txt`**

Contém instruções para especializar o assistente de IA em determinado domínio técnico ou área de negócio.

**`customer-support.txt`**

Contém instruções para especializar a IA no atendimento ao cliente, incluindo tom de comunicação, diretrizes de resposta e regras de encaminhamento para atendimento humano.

A aplicação pode carregar o prompt apropriado com base na solicitação do usuário, no fluxo de trabalho selecionado ou no contexto da área de negócio.

## Principais Aprendizados

- Evitar chamadas desnecessárias a LLMs, utilizando alternativas mais simples quando possível.
- Utilizar classificadores e roteadores para selecionar o caminho de processamento adequado.
- Implementar guardrails para proteger dados e controlar o comportamento da aplicação.
- Utilizar embeddings para similaridade semântica e recuperação de informações.
- Gerenciar o histórico das conversas para controlar o tamanho do contexto e os custos.
- Armazenar prompts em arquivos modularizados para facilitar a manutenção.

## Perguntas para Aprofundamento

- Como avaliar a precisão de um classificador antes de utilizá-lo em produção?
- Qual estratégia de roteamento oferece o melhor equilíbrio entre custo e qualidade das respostas?
- Como resumir o histórico de uma conversa sem perder informações importantes?
- Como versionar, testar e avaliar prompts de forma sistemática?
