---
title: Suporte da API em Web Apps Estáticas Azure com Funções Azure
description: Saiba o que a API apresenta Web Apps Estáticas Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 7f86ce9158b5b07b036c785c2973e8a5883ed686
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597143"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Suporte da API na pré-visualização de aplicações estáticas azure com funções azure

As Aplicações Web Estáticas Azure fornecem pontos finais aPI sem servidor através de [funções Azure](../azure-functions/functions-overview.md). Ao alavancar as Funções Azure, as APIs escalam dinamicamente com base na procura, e incluem as seguintes características:

- **Segurança integrada** com acesso direto à autenticação do utilizador e dados [de autorização baseados em papéis.](user-information.md)
- **Encaminhamento sem emenda** que disponibiliza a rota _api_ para a aplicação web de forma segura sem exigir regras cors personalizadas.
- **Funções Azure** v3 compatíveis com nó.js 12.
- **HTTP dispara** e encaderna.

## <a name="configuration"></a>Configuração

Os pontos finais da API estão disponíveis para a aplicação web através da rota _api._ Enquanto esta rota é fixa, tem controlo sobre a pasta onde localiza a aplicação de Funções Azure associada. Pode alterar esta localização [editando o ficheiro YAML](github-actions-workflow.md#build-and-deploy) de fluxo de trabalho localizado na pasta _.github/workflows_ do seu repositório.

## <a name="constraints"></a>Restrições

As Aplicações Web Estáticas Azure fornecem uma API através de Funções Azure. As capacidades das Funções Azure estão focadas num conjunto específico de funcionalidades que lhe permitem criar uma API para uma aplicação web e permitir que a aplicação web se conectem à API de forma segura. Estas características vêm com alguns constrangimentos, incluindo:

- O prefixo da rota API deve ser _api_.
- Os gatilhos e encadernações limitam-se a [HTTP](../azure-functions/functions-bindings-http-webhook.md).
  - Todas as [outras funções Azure acionam e encadernações,](../azure-functions/functions-triggers-bindings.md#supported-bindings) com exceção das encadernações de saída.
- Os registos só estão disponíveis se adicionar insights de [aplicação](../azure-functions/functions-monitoring.md) à sua aplicação Funções.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione uma API](add-api.md)
