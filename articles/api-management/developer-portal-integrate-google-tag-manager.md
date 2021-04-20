---
title: Integrar o Gestor de Etiquetas do Google no portal do programador
titleSuffix: Azure API Management
description: Saiba como ligar o Google Tag Manager ao seu portal de desenvolvimento gerido ou auto-hospedado na Azure API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c209eb782787146d947b4684d41c5d1e9bb6364e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741902"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>Integrar o Gestor de Etiquetas do Google no portal de desenvolvimento de gestão de API

[O Google Tag Manager](https://developers.google.com/tag-manager) é um sistema de gestão de tags criado pela Google. Pode usá-lo para gerir tags JavaScript e HTML utilizadas para rastreio e análise em websites. Por exemplo, pode usar o Google Tag Manager para integrar o Google Analytics, heatmaps ou chatbots como o LiveChat.

Siga os passos deste artigo para ligar o Google Tag Manager ao seu portal de desenvolvimento gerido ou auto-hospedado na Azure API Management.

## <a name="add-google-tag-manager-to-your-portal"></a>Adicione o Gestor de Etiquetas do Google ao seu portal

Siga estes passos para ligar o Google Tag Manager ao seu portal de desenvolvedores gerido ou auto-hospedado.

> [!IMPORTANT]
> Os passos 1 e 2 não são necessários para portais geridos. Se tiver um portal gerido, salte para o passo 4.

1. Crie um [ambiente local](developer-portal-self-host.md#step-1-set-up-local-environment) para o mais recente lançamento do portal de desenvolvedores.

1. Instale o pacote **npm** para adicionar [Paperbits para o Google Tag Manager](https://github.com/paperbits/paperbits-gtm):

    ```sh
    npm install @paperbits/gtm --save
    ```

1. No `startup.publish.ts` ficheiro da `src` pasta, importa e regista o módulo GTM:

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Alargar a configuração do site a partir do passo anterior com a configuração do Gestor de Etiquetas do Google:

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Passos seguintes

- [Automatizar as implementações do portal](automate-portal-deployments.md)
- [Auto-anfitrião do portal de desenvolvedores](developer-portal-self-host.md)