---
title: Exercícios do Azure OpenAI
permalink: index.html
layout: home
---

# Desenvolver aplicativos de IA generativa no portal do Azure AI Foundry

Os exercícios a seguir foram projetados para fornecer uma experiência de aprendizado prático na qual você explorará padrões e técnicas comuns que os desenvolvedores usam para criar aplicativos de IA generativa, como "copilotos" baseados em chat, e aprenderá a implementar esses padrões usando os Serviços de IA do Azure, em específico, o Serviço OpenAI do Azure e o Estúdio de IA do Azure.

Embora você possa concluir esses exercícios por conta própria, eles foram projetados para complementar os módulos do [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/), nos quais você encontrará um aprofundamento em alguns dos conceitos subjacentes nos quais esses exercícios se baseiam.

> **Observação**: para concluir os exercícios, você precisará de uma assinatura do Azure na qual tenha permissões e cota suficientes para provisionar os recursos do Azure usados pelo Estúdio de IA do Azure e para implantar e usar modelos GPT do OpenAI do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta do Azure](https://azure.microsoft.com/free). Há uma opção de avaliação gratuita para novos usuários que inclui créditos para os primeiros 30 dias.

## Exercícios

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}