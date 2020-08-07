---
title: O que são as Aplicações Web Estáticas do Azure?
description: As principais funcionalidades e funcionalidades das Aplicações Web Estáticas Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 47140bccc8c2a1b69f083502d031a9db6d21294e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835787"
---
# <a name="what-is-azure-static-web-apps-preview"></a>O que é Azure Static Web Apps Preview?

Azure Static Web Apps é um serviço que constrói e implementa automaticamente aplicações web de stack completa para Azure a partir de um repositório GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Visão geral das aplicações web estáticas":::

O fluxo de trabalho da Azure Static Web Apps é adaptado ao fluxo de trabalho diário de um desenvolvedor. As aplicações são construídas e implementadas com base nas interações do GitHub.

Quando cria um recurso das Aplicações Web Estáticas do Azure, o Azure configura um fluxo de trabalho do GitHub Actions no repositório de código fonte da aplicação que monitoriza um ramo à sua escolha. Sempre que pressiona compromete-se ou aceita pedidos de puxar para a sucursal observada, a GitHub Action constrói e implementa automaticamente a sua app e a sua API para Azure.

As aplicações Web estáticas são normalmente compiladas com recurso a bibliotecas e arquiteturas, tais como Angular, React, Svelte ou Vue. Estas aplicações incluem HTML, CSS, JavaScript e elementos de imagem que compõem a aplicação. Com um servidor web tradicional, estes ativos são servidos a partir de um único servidor ao lado de todos os pontos finais da API necessários.

Com aplicações web estáticas, os ativos estáticos são separados de um servidor web tradicional e são servidos a partir de pontos geograficamente distribuídos em todo o mundo. Esta distribuição torna a apresentação de ficheiros muito mais rápida, uma vez que os ficheiros estão fisicamente mais próximos dos utilizadores finais. Além disso, os pontos finais da API são hospedados usando uma [arquitetura sem servidor](../azure-functions/functions-overview.md), o que evita a necessidade de um servidor back-end completo todos juntos.

## <a name="key-features"></a>Principais funcionalidades

- **Hospedagem web** para conteúdo estático como HTML, CSS, JavaScript e imagens.
- **Suporte integrado da API** fornecido pela Azure Functions.
- **Integração gitHub de primeira parte** onde alterações de repositório desencadeiam construções e implementações.
- Conteúdo estático **distribuído globalmente,** colocando o conteúdo mais próximo dos seus utilizadores.
- **Certificados SSL gratuitos,** que são automaticamente renovados.
- **Domínios personalizados** \* para fornecer personalizações de marca à sua app.
- **Modelo de segurança sem emenda** com um proxy invertido ao chamar APIs, que não requer configuração CORS.
- **Integrações de fornecedores de autenticação** com Azure Ative Directory, Facebook, Google, GitHub e Twitter.
- Definição e atribuições **de funções de função de autorização personalizável.**
- **Regras de encaminhamento de back-end** que permitem o controlo total sobre o conteúdo e rotas que serve.
- **Versões de encenação geradas alimentadas** por pedidos de puxar que permitem versões de pré-visualização do seu site antes da publicação.

## <a name="what-you-can-do-with-static-web-apps"></a>O que pode fazer com aplicações web estáticas

- **Construa aplicações JavaScript modernas** com quadros e bibliotecas como [Angular](getting-started.md#tabpanel_CeZOj-G++Q_angular), [React,](getting-started.md#tabpanel_CeZOj-G++Q_react) [Svelte,](https://docs.microsoft.com/learn/modules/publish-app-service-static-web-app-api/) [Vue](getting-started.md#tabpanel_CeZOj-G++Q_vue) com um back-end [Azure Functions.](apis.md)
- **Publique sites estáticos** com quadros como [Gatsby,](publish-gatsby.md) [Hugo,](publish-hugo.md) [VuePress.](publish-vuepress.md)
- **Implementar aplicações web** com quadros como [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

\*Os registos de domínio Apex não são suportados durante a pré-visualização.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa a sua primeira aplicação estática](getting-started.md)
