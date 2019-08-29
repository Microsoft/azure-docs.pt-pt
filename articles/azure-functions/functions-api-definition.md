---
title: OpenAPI metadados em Azure Functions | Microsoft Docs
description: Visão geral do suporte do OpenAPI no Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 00dedd069914384a1883a54bfb433857286fb49c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097582"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte a metadados do OpenAPI 2,0 no Azure Functions (versão prévia)
O suporte aos metadados OpenAPI 2,0 (anteriormente Swagger) no Azure Functions é um recurso de visualização que você pode usar para gravar uma definição de OpenAPI 2,0 dentro de um aplicativo de funções. Em seguida, você pode hospedar esse arquivo usando o aplicativo de funções.

> [!IMPORTANT]
> A funcionalidade de pré-visualização do OpenAPI está disponível atualmente apenas no runtime 1.x. [Pode encontrar aqui](./functions-versions.md#creating-1x-apps) informações sobre como criar uma aplicação de funções 1.x.

Os [metadados do openapi](https://swagger.io/) permitem que uma função que hospeda uma API REST seja consumida por uma ampla variedade de outros softwares. Este software inclui ofertas da Microsoft, como o PowerApps e o [recurso de aplicativos de API do serviço Azure app](../app-service/overview.md), ferramentas de desenvolvedor de terceiros como o [postmaster](https://www.getpostman.com/docs/importing_swagger)e [muitos outros pacotes](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>É recomendável começar com o [tutorial de introdução](./functions-api-definition-getting-started.md) e, em seguida, retornar a este documento para saber mais sobre recursos específicos.

## <a name="enable"></a>Habilitar o suporte à definição de OpenAPI
Você pode definir todas as configurações de OpenAPI na página **definição de API** nos recursos de **plataforma**do aplicativo de funções.

> [!NOTE]
> O recurso de definição de API de função não tem suporte para tempo de execução beta no momento.

Para habilitar a geração de uma definição de OpenAPI hospedada e uma definição de início rápido, defina **origem de definição de API** como **Function (versão prévia)** . A **URL externa** permite que sua função use uma definição de openapi que é hospedada em outro lugar.

## <a name="generate-definition"></a>Gerar um esqueleto do Swagger com base nos metadados da função
Um modelo pode ajudá-lo a começar a escrever sua primeira definição de OpenAPI. O recurso de modelo de definição cria uma definição de OpenAPI esparsa usando todos os metadados no arquivo function. JSON para cada uma das suas funções de gatilho HTTP. Você precisará preencher mais informações sobre a API da [especificação openapi](https://swagger.io/specification/), como modelos de solicitação e resposta.

Para obter as instruções passo a passo, consulte o [tutorial de introdução](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Modelos disponíveis

|Name| Descrição |
|:-----|:-----|
|Definição gerada|Uma definição de OpenAPI com a quantidade máxima de informações que podem ser inferidas dos metadados existentes da função.|

### <a name="quickstart-details"></a>Metadados incluídos na definição gerada

A tabela a seguir representa as configurações de portal do Azure e os dados correspondentes em function. JSON, pois eles são mapeados para o esqueleto do Swagger gerado.

|Swagger.json|Interface do usuário do portal|Function.json|
|:----|:-----|:-----|
|[Anfitrião](https://swagger.io/specification/#fixed-fields-15)|**Configurações** >  > do aplicativo de funções configurações do serviço de aplicativo URL da visão geral > |*Não presente*
|[Estruturas](https://swagger.io/specification/#paths-object-29)|Integrar > **métodos http selecionados**|Associações Encaminhar
|[Item de caminho](https://swagger.io/specification/#path-item-object-32)|Integrar > **modelo de rota**|Associações Métodos
|[Segurança](https://swagger.io/specification/#security-scheme-object-112)|**Novas**|*Não presente*|
|operationId|**Rota + verbos permitidos**|Rota + verbos permitidos|

\*A ID da operação é necessária apenas para a integração com o PowerApps e o Flow.
> [!NOTE]
> A extensão x-MS-summary fornece um nome de exibição em aplicativos lógicos, PowerApps e fluxo.
>
> Para saber mais, confira [personalizar sua definição de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Usar CI/CD para definir uma definição de API

 Você deve habilitar a hospedagem da definição de API no portal antes de habilitar o controle do código-fonte para modificar a definição de API do controle do código-fonte. Siga estas instruções:

1. Navegue até **definição de API (versão prévia)** nas configurações do aplicativo de funções.
   1. Defina a **origem da definição de API** como **Function**.
   1. Clique em **gerar modelo de definição de API** e **salve** para criar uma definição de modelo para modificar mais tarde.
   1. Anote sua URL de definição de API e a chave.
1. [Configure a integração contínua/implantação contínua (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Modificar Swagger. JSON no controle do código-\.fonte em \site\wwwroot azurefunctions\swagger\swagger.JSON.

Agora, as alterações em Swagger. JSON em seu repositório são hospedadas por seu aplicativo de funções na URL de definição de API e na chave que você anotou na etapa 1. c.

## <a name="next-steps"></a>Passos Seguintes
* [Tutorial de introdução](functions-api-definition-getting-started.md). Experimente a nossa explicação para ver uma definição de OpenAPI em ação.
* [Azure Functions repositório GitHub](https://github.com/Azure/Azure-Functions/). Confira o repositório do Functions para nos enviar comentários sobre a versão prévia de suporte de definição de API. Faça um problema do GitHub para qualquer coisa que você queira ver atualizado.
* [Azure Functions referência do desenvolvedor](functions-reference.md). Saiba mais sobre as funções de codificação e a definição de gatilhos e associações.
