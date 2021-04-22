---
title: O que são as Aplicações Web Estáticas do Azure?
description: As principais funcionalidades e funcionalidades das Aplicações Web Estáticas Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 04/01/2021
ms.author: cshoe
ms.openlocfilehash: 945979daa3c766b737e5b312a6c14e60204a6a55
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874068"
---
# <a name="what-is-azure-static-web-apps-preview"></a>O que é Azure Static Web Apps Preview?

Azure Static Web Apps é um serviço que constrói e implementa automaticamente aplicações web de stack completa para Azure a partir de um repositório de código.

:::image type="content" source="media/overview/azure-static-web-apps-overview.png" alt-text="Diagrama de visão geral de aplicativos web estáticos Azure":::

O fluxo de trabalho da Azure Static Web Apps é adaptado ao fluxo de trabalho diário de um desenvolvedor. As aplicações são construídas e implementadas com base em alterações de código.

Quando cria um recurso Azure Static Web Apps, o Azure interage diretamente com o GitHub ou o Azure DevOps para monitorizar um ramo à sua escolha. Sempre que pressiona compromete-se ou aceita pedidos de puxar para o ramo observado, uma construção é executada automaticamente e a sua app e API são implantadas no Azure.

As aplicações web estáticas são geralmente construídas usando bibliotecas e estruturas como Angular, React, Svelte, Vue ou Blazor onde a renderização do lado do servidor não é necessária. Estas aplicações incluem HTML, CSS, JavaScript e elementos de imagem que compõem a aplicação. Com um servidor web tradicional, estes ativos são servidos a partir de um único servidor ao lado de todos os pontos finais da API necessários.

Com aplicações web estáticas, os ativos estáticos são separados de um servidor web tradicional e são servidos a partir de pontos geograficamente distribuídos em todo o mundo. Esta distribuição torna a apresentação de ficheiros muito mais rápida, uma vez que os ficheiros estão fisicamente mais próximos dos utilizadores finais. Além disso, os pontos finais da API são hospedados usando uma [arquitetura sem servidor](../azure-functions/functions-overview.md), o que evita a necessidade de um servidor back-end completo todos juntos.

## <a name="key-features"></a>Principais funcionalidades

- **Hospedagem web** para conteúdo estático como HTML, CSS, JavaScript e imagens.
- **Suporte integrado da API** fornecido pela Azure Functions.
- **Integração gitHub e Azure DevOps de primeira classe** onde alterações de repositório desencadeiam construções e implementações.
- Conteúdo estático **distribuído globalmente,** colocando o conteúdo mais próximo dos seus utilizadores.
- **Certificados SSL gratuitos,** que são automaticamente renovados.
- **Domínios personalizados** para fornecer personalizações de marca à sua aplicação.
- **Modelo de segurança sem emenda** com um proxy invertido ao chamar APIs, que não requer configuração CORS.
- **Integrações de fornecedores de autenticação** com Azure Ative Directory, Facebook, Google, GitHub e Twitter.
- Definição e atribuições **de funções de função de autorização personalizável.**
- **Regras de encaminhamento de back-end** que permitem o controlo total sobre o conteúdo e rotas que serve.
- **Versões de encenação geradas alimentadas** por pedidos de puxar que permitem versões de pré-visualização do seu site antes da publicação.

## <a name="what-you-can-do-with-static-web-apps"></a>O que pode fazer com aplicações web estáticas

- **Construa aplicações web modernas** com estruturas JavaScript e bibliotecas como [Angular](getting-started.md?tabs=angular), [React,](getting-started.md?tabs=react) [Svelte,](/learn/modules/publish-app-service-static-web-app-api/) [Vue,](getting-started.md?tabs=vue)ou usando [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) para criar aplicações WebAssembly, com um back-end [Azure Functions.](apis.md)
- **Publique sites estáticos** com quadros como [Gatsby,](publish-gatsby.md) [Hugo,](publish-hugo.md) [VuePress.](publish-vuepress.md)
- **Implementar aplicações web** com quadros como [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa a sua primeira aplicação estática](getting-started.md)
