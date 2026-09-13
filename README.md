# Guia Avançado de Engenharia de Prompts e Agentes de IA

Este repositório reúne uma coleção estruturada de padrões de design de prompts, modelos de engenharia, arquiteturas de *skills* (competências) e frameworks para o desenvolvimento de agentes autônomos utilizando modelos de linguagem (LLMs).

---

## 📋 Sumário
1. [Diversos Tipos de Prompts](#1-diversos-tipos-de-prompts)
2. [Modelos Prontos para Uso](#2-modelos-prontos-para-uso)
3. [Modelos de Skills (Competências)](#3-modelos-de-skills-compet%C3%AAncias)
4. [Modelos para Agentes Autônomos](#4-modelos-para-agentes-aut%C3%B4nomos)

---

## 1. Diversos Tipos de Prompts

A engenharia de prompts evoluiu de simples instruções textuais para estruturas lógicas que guiam o comportamento macro do modelo. Abaixo estão as principais técnicas:

* **Zero-Shot Prompting:** Solicita uma tarefa diretamente ao modelo sem fornecer nenhum exemplo prévio. Ideal para tarefas comuns e diretas.
* **Few-Shot Prompting:** Fornece um ou mais exemplos de entradas e saídas esperadas antes de solicitar a resposta final. Essencial para garantir consistência de formato e tom.
* **Chain-of-Thought (CoT):** Força o modelo a quebrar o raciocínio em etapas sequenciais lógicas antes de apresentar a resposta. Reduz drasticamente alucinações em problemas matemáticos ou analíticos.
* **Self-Consistency:** Executa múltiplos caminhos de raciocínio (*Chain-of-Thought*) em paralelo e seleciona a resposta mais consistente por votação majoritária.
* **Directional-Stimulus Prompting:** Fornece pistas visíveis ou orientações sutis (ex: palavras-chave ou restrições) para guiar o modelo em direção à resposta correta sem dar a resposta diretamente.

---

## 2. Modelos Prontos para Uso

Estes templates podem ser copiados e adaptados para uso imediato em interfaces como ChatGPT, Claude ou integrações via API.

### 💡 Template: Prompt de Sistema Estruturado (Persona + Contexto)
```markdown
[ROLE/PERSONA]
Você é um Engenheiro de Software Sênior especializado em arquitetura de microsserviços e segurança.

[CONTEXTO]
Estamos migrando um monolito legado para uma arquitetura distribuída na AWS. O foco atual é a resiliência do sistema.

[TAREFA]
Analise o trecho de código fornecido pelo usuário e identifique pontos únicos de falha.

[RESTRICÕES]
1. Responda apenas com soluções baseadas em padrões nativos da nuvem (ex: Circuit Breaker, Retry, Bulkhead).
2. Não utilize bibliotecas de terceiros que não sejam amplamente consolidadas no mercado.
3. Limite a explicação teórica a um parágrafo por solução.

[FORMATO DE SAÍDA]
- **Problema Identificado:** [Breve descrição]
- **Padrão Recomendado:** [Nome do Padrão]
- **Exemplo de Implementação (Pseudocódigo):**
```

### 💡 Template: Chain-of-Thought (CoT) Prático
```markdown
Resolva o seguinte problema de lógica de negócios de forma detalhada:

Problema: [Inserir o problema complexo aqui]

Instruções passo a passo:
1. Primeiro, identifique todas as variáveis de entrada e restrições mencionadas.
2. Em seguida, trace o impacto de cada variável no fluxo financeiro trimestral.
3. Calcule o pior e o melhor cenário detalhando os cálculos matemáticos passo a passo.
4. Por fim, formule sua recomendação com base nas etapas anteriores.

Mostre seu raciocínio explicitamente para cada uma das etapas antes de apresentar a conclusão.
```

---

## 3. Modelos de Skills (Competências)

*Skills* são blocos de funções modulares ou capacidades específicas que você injeta em um modelo ou agente para permitir que ele interaja com o mundo real ou execute tarefas determinísticas.

### 🔧 Estrutura de Skill: Chamada de API (Tool Calling / Function Calling)
Este modelo define como estruturar as instruções para que o LLM entenda como e quando usar uma ferramenta externa.

```json
{
  "name": "obter_cotacao_acao",
  "description": "Busca o preço atual de uma ação específica no mercado financeiro em tempo real.",
  "parameters": {
    "type": "object",
    "properties": {
      "ticker": {
        "type": "string",
        "description": "O símbolo da ação na bolsa (ex: AAPL, PETR4.SA)."
      },
      "moeda": {
        "type": "string",
        "description": "A moeda de exibição (ex: USD, BRL). O padrão é USD.",
        "enum": ["USD", "BRL", "EUR"]
      }
    },
    "required": ["ticker"]
  }
}
```

### 📝 Prompt de Injeção de Skill (Contexto para o LLM)
```markdown
Você tem acesso às seguintes ferramentas de sistema (skills). Você deve decidir se precisa usar uma ferramenta para responder à pergunta do usuário ou se pode responder diretamente.

Se decidir usar uma ferramenta, retorne estritamente no formato:
`TOOL: [nome_da_ferramenta] ARGUMENTOS: [json_de_parametros]`

Ferramentas Disponíveis:
- **pesquisar_web(query):** Usa para buscar fatos ou eventos atuais na internet.
- **calcular_expressao(math_string):** Resolve fórmulas matemáticas avançadas de forma exata.
```

---

## 4. Modelos para Agentes Autônomos

Agentes são sistemas onde o LLM atua como o motor de decisão (cérebro), utilizando loops de feedback, memória e ferramentas para atingir um objetivo de longo prazo sem intervenção humana constante.

### 🤖 Padrão ReAct (Reason + Act)
Este é o padrão de agente mais utilizado no mercado (base do LangChain e Semantic Kernel).

```markdown
Você é um agente autônomo de resolução de problemas. Siga o ciclo estrito de PENSAMENTO, AÇÃO e OBSERVAÇÃO para resolver o objetivo do usuário.

Objetivo do Usuário: [Inserir objetivo]

Ciclo de Execução:

Pensamento 1: Preciso descobrir qual é a população atual de São Paulo para calcular a demanda proporcional de água.
Ação 1: pesquisar_web("população atual de São Paulo 2026")
Observação 1: [O sistema retornará o resultado aqui]

Pensamento 2: Agora que sei que a população é de aproximadamente X milhões, preciso aplicar a fórmula de consumo médio diário.
Ação 2: calcular_expressao("X * 150")
Observação 2: [O sistema retornará o resultado aqui]

Pensamento 3: Tenho todos os dados necessários. Vou formular a resposta final.
Resposta Final: [Sua resposta estruturada ao usuário]
```

### 👥 Modelo de Agentes Multi-Agente (Hierarquia / Orquestração)
Para tarefas muito complexas, divida o escopo em múltiplos agentes especializados orientados por prompts de persona complementares:

1. **Agente Planejador (Supervisor):** Recebe o escopo global, divide a tarefa em subtarefas menores e distribui para os agentes especialistas.
2. **Agente Executor/Pesquisador:** Focado puramente na coleta de dados brutas e execução técnica usando *skills*.
3. **Agente Revisor (Quality Assurance):** Não executa nenhuma tarefa técnica; sua única função é validar se a saída do Executor cumpre todos os critérios de aceitação do Planejador.

---

## 🚀 Como Contribuir
Sinta-se à vontade para abrir uma *Issue* ou enviar um *Pull Request* adicionando novas técnicas de engenharia de prompts ou arquiteturas de agentes à medida que o ecossistema evolui!
