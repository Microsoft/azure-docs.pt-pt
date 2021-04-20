---
title: Integrar Insights de Aplicação ao portal do desenvolvedor
titleSuffix: Azure API Management
description: Saiba como integrar o Application Insights no seu portal de desenvolvimento gerido ou auto-hospedado.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741897"
---
# <a name="integrate-application-insights-to-developer-portal"></a>Integrar Insights de Aplicação ao portal do desenvolvedor

Uma característica popular do Azure Monitor é a Application Insights. É um serviço extensível de Gestão de Desempenho de Aplicações (APM) para desenvolvedores e profissionais de DevOps. Utilize-o para monitorizar o portal do desenvolvedor e detetar anomalias de desempenho. O Application Insights inclui ferramentas de análise poderosas para ajudá-lo a aprender o que os utilizadores realmente fazem durante a visita ao portal do seu programador.

## <a name="add-application-insights-to-your-portal"></a>Adicionar Insights de Aplicação ao seu portal

Siga estes passos para ligar o Application Insights no seu portal de desenvolvedores gerido ou auto-hospedado.

> [!IMPORTANT]
> Os passos 1 e 2 não são necessários para portais geridos. Se tiver um portal gerido, salte para o passo 4.

1. Crie um [ambiente local](developer-portal-self-host.md#step-1-set-up-local-environment) para o mais recente lançamento do portal de desenvolvedores.

1. Instale o pacote **npm** para adicionar [Paperbits para Azure](https://github.com/paperbits/paperbits-azure):

    ```console
    npm install @paperbits/azure --save
    ```

1. No ficheiro da `startup.publish.ts` `src` pasta, importa e regista o módulo Application Insights:

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. Recupere a configuração do portal:

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Alargar a configuração do site a partir do passo anterior com a configuração de Insights de Aplicação:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o portal de desenvolvedores:

- [Descrição geral do portal do programador da Gestão de API do Azure](api-management-howto-developer-portal.md)
- [Automatizar as implementações do portal](automate-portal-deployments.md)
- [Auto-anfitrião do portal de desenvolvedores](developer-portal-self-host.md)
