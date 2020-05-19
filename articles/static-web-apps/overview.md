---
title: O que é Azure Static Web Apps?
description: As principais funcionalidades e funcionalidades das Aplicações Web Estáticas do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: d416044599535e8acd363d09099e8667bba59a0a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599968"
---
# <a name="what-is-azure-static-web-apps-preview"></a>O que é a pré-visualização de aplicações web estáticas do Azure?

As Web Apps Estáticas azure é um serviço que automaticamente constrói e implementa aplicações web de stack completa para O Azure a partir de um repositório GitHub.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Visão geral de aplicações web estáticas":::

O fluxo de trabalho das Aplicações Web Estáticas Azure é adaptado ao fluxo de trabalho diário de um desenvolvedor. As aplicações são construídas e implementadas com base nas interações do GitHub.

Ao criar um recurso Azure Static Web Apps, o Azure configura um fluxo de trabalho gitHub Actions no repositório de código fonte da aplicação que monitoriza um ramo da sua escolha. Sempre que pressiona os compromissos ou aceita pedidos de puxar para o ramo vigiado, a GitHub Action constrói e implanta automaticamente a sua app e a sua API para o Azure.

As aplicações web estáticas são comumente construídas usando bibliotecas e estruturas como Angular, Reagir, Svelte ou Vue. Estas aplicações incluem HTML, CSS, JavaScript e ativos de imagem que compõem a aplicação. Com um servidor web tradicional, estes ativos são servidos a partir de um único servidor ao lado de quaisquer pontos finais API necessários.

Com aplicações web estáticas, os ativos estáticos são separados de um servidor web tradicional e são servidos a partir de pontos distribuídos geograficamente em todo o mundo. Esta distribuição torna os ficheiros de serviço muito mais rápidos, uma vez que os ficheiros estão fisicamente mais próximos dos utilizadores finais. Além disso, os pontos finais da API são hospedados usando uma [arquitetura sem servidores](../azure-functions/functions-overview.md), o que evita a necessidade de um servidor completo de back-end todos juntos.

## <a name="key-features"></a>Principais funcionalidades

- **Hospedagem gratuita na web** para conteúdo estático como HTML, CSS, JavaScript e imagens.
- **Suporte integrado da API** fornecido pelas Funções Azure.
- **Integração gitHub de primeira parte** onde as mudanças de repositório desencadeiam construções e implantações.
- Conteúdo estático **distribuído globalmente,** colocando conteúdo mais próximo dos seus utilizadores.
- **Certificados SSL gratuitos,** que são automaticamente renovados.
- **Domínios personalizados** \* para fornecer personalizações de marca para a sua aplicação.
- Modelo de **segurança sem emenda** com um proxy invertido ao ligar para APIs, que não requer configuração CORS.
- **Integrações** de fornecedores de autenticação com o Azure Ative Directory, Facebook, Google, GitHub e Twitter.
- **Definição** de funções de autorização personalizável e atribuições.
- **Regras de encaminhamento de back-end** que permitem o controlo total sobre o conteúdo e as rotas que serve.
- **Geradas versões de encenação alimentadas** por pedidos de pull que permitem versões de pré-visualização do seu site antes de publicar.

## <a name="what-you-can-do-with-static-web-apps"></a>O que pode fazer com aplicações web estáticas

- **Construa aplicações javaScript modernas** com estruturas e bibliotecas como [Angular,](https://angular.io/) [Reagir,](https://reactjs.org/) [Svelte,](https://svelte.dev/) [Vue](https://vuejs.org/) com uma back-end de [funções Azure.](https://azure.microsoft.com/services/functions/)
- **Publique sites estáticos** com estruturas como [Gatsby,](publish-gatsby.md) [Hugo,](publish-hugo.md) [VuePress.](publish-vuepress.md)
- **Implemente aplicações web** com estruturas como [Next.js](deploy-nextjs.md) e [Nuxt.js](deploy-nuxtjs.md).

\*As inscrições de domínio Apex não são suportadas durante a pré-visualização.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Construa a sua primeira aplicação estática](getting-started.md)
