---
title: Suporte API em Azure Static Web Apps com Funções Azure
description: Saiba o que a API apresenta Azure Static Web Apps suporta
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 1fc5e1e6982686e7042e5b8ad55d72a4560b6aee
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737483"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Suporte API em Azure Static Web Apps Preview com Funções Azure

A azure Static Web Apps fornece pontos finais API sem servidor através de [Funções Azure](../azure-functions/functions-overview.md). Ao alavancar as Funções Azure, as APIs escalam dinamicamente com base na procura e incluem as seguintes funcionalidades:

- **Segurança integrada** com acesso direto à autenticação do utilizador e dados [de autorização baseados em funções.](user-information.md)
- **Encaminhamento sem emenda** que torna a rota _api_ disponível para a aplicação web de forma segura sem exigir regras CORS personalizadas.
- **OS gatilhos HTTP** e as ligações entrada/saída.

## <a name="configuration"></a>Configuração

Os pontos finais da API estão disponíveis para a aplicação web através da rota _da API._ Enquanto esta rota estiver fixa, tem controlo sobre a pasta e projeto onde localiza a app Azure Functions associada. Pode alterar esta localização [editando o ficheiro YAML do fluxo de trabalho](github-actions-workflow.md#build-and-deploy) localizado na pasta _.github/workflows_ do seu repositório.

## <a name="constraints"></a>Restrições

A azure Static Web Apps fornece uma API através de Funções Azure. As capacidades das Funções Azure estão focadas num conjunto específico de funcionalidades que lhe permitem criar uma API para uma aplicação web e permitir que a aplicação web se conecte à API de forma segura. Estas características vêm com alguns constrangimentos, incluindo:

- O prefixo da rota API deve ser _api_.
- A API deve ser uma aplicação Node.js 12, .NET Core 3.1 ou Python 3.8 Azure Functions.
- As regras de rota para funções API apenas [suportam redirecionamentos](routes.md#redirects) e [asseguram rotas com funções](routes.md#securing-routes-with-roles).
- Os gatilhos são limitados a [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - As [ligações de](../azure-functions/functions-triggers-bindings.md#supported-bindings) entrada e saída são suportadas.
- Os registos só estão disponíveis se adicionar [Insights de Aplicação](../azure-functions/functions-monitoring.md) à sua aplicação Funções.
- Algumas definições de aplicação são geridas pelo serviço. Não é possível configurar configurações de aplicações que começam com os seguintes prefixos: , `APPSETTING_` `AZUREBLOBSTORAGE_` `AZUREFILESSTORAGE_` `AZURE_FUNCTION_` `CONTAINER_` `DIAGNOSTICS_` `DOCKER_` `FUNCTIONS_` `IDENTITY_` `MACHINEKEY_` `MAINSITE_` `MSDEPLOY_` `SCMSITE_` `SCM_` `WEBSITES_` `WEBSITE_` `WEBSOCKET_` `AzureWeb`

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicionar uma API](add-api.md)
