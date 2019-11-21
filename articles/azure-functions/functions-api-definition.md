---
title: OpenAPI metadata in Azure Functions
description: Overview of OpenAPI support in Azure Functions
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: a80079574dc29c54de89f5275c65637b205742d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227403"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>OpenAPI 2.0 metadata support in Azure Functions (preview)
OpenAPI 2.0 (formerly Swagger) metadata support in Azure Functions is a preview feature that you can use to write an OpenAPI 2.0 definition inside a function app. You can then host that file by using the function app.

> [!IMPORTANT]
> A funcionalidade de pré-visualização do OpenAPI está disponível atualmente apenas no runtime 1.x. [Pode encontrar aqui](./functions-versions.md#creating-1x-apps) informações sobre como criar uma aplicação de funções 1.x.

[OpenAPI metadata](https://swagger.io/) allows a function that's hosting a REST API to be consumed by a wide variety of other software. This software includes Microsoft offerings like PowerApps and the [API Apps feature of Azure App Service](../app-service/overview.md), third-party developer tools like [Postman](https://www.getpostman.com/docs/importing_swagger), and [many more packages](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>We recommend starting with the [getting started tutorial](./functions-api-definition-getting-started.md) and then returning to this document to learn more about specific features.

## <a name="enable"></a>Enable OpenAPI definition support
You can configure all OpenAPI settings on the **API Definition** page in your function app's **Platform features**.

> [!NOTE]
> Function API definition feature is not supported for beta runtime currently.

To enable the generation of a hosted OpenAPI definition and a quickstart definition, set **API definition source** to **Function (Preview)** . **External URL** allows your function to use an OpenAPI definition that's hosted elsewhere.

## <a name="generate-definition"></a>Generate a Swagger skeleton from your function's metadata
A template can help you start writing your first OpenAPI definition. The definition template feature creates a sparse OpenAPI definition by using all the metadata in the function.json file for each of your HTTP trigger functions. You'll need to fill in more information about your API from the [OpenAPI specification](https://swagger.io/specification/), such as request and response templates.

For step-by-step instructions, see the [getting started tutorial](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Available templates

|Nome| Descrição |
|:-----|:-----|
|Generated Definition|An OpenAPI definition with the maximum amount of information that can be inferred from the function's existing metadata.|

### <a name="quickstart-details"></a>Included metadata in the generated definition

The following table represents the Azure portal settings and corresponding data in function.json as it is mapped to the generated Swagger skeleton.

|Swagger.json|Portal UI|Function.json|
|:----|:-----|:-----|
|[Anfitrião](https://swagger.io/specification/#fixed-fields-15)|**Function app settings** > **App Service settings** > **Overview** > **URL**|*Not present*
|[Paths](https://swagger.io/specification/#paths-object-29)|**Integrate** > **Selected HTTP methods**|Bindings: Route
|[Path Item](https://swagger.io/specification/#path-item-object-32)|**Integrate** > **Route template**|Bindings: Methods
|[Segurança](https://swagger.io/specification/#security-scheme-object-112)|**Keys**|*Not present*|
|operationID*|**Route + Allowed verbs**|Route + Allowed Verbs|

\*The operation ID is required only for integrating with PowerApps and Flow.
> [!NOTE]
> The x-ms-summary extension provides a display name in Logic Apps, PowerApps, and Flow.
>
> To learn more, see [Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Use CI/CD to set an API definition

 You must enable API definition hosting in the portal before you enable source control to modify your API definition from source control. Follow these instructions:

1. Browse to **API Definition (preview)** in your function app settings.
   1. Set **API definition source** to **Function**.
   1. Click **Generate API definition template** and then **Save** to create a template definition for modifying later.
   1. Note your API definition URL and key.
1. [Set up continuous integration/continuous deployment (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Modify swagger.json in source control at \site\wwwroot\.azurefunctions\swagger\swagger.json.

Now, changes to swagger.json in your repository are hosted by your function app at the API definition URL and key that you noted in step 1.c.

## <a name="next-steps"></a>Passos seguintes
* [Getting started tutorial](functions-api-definition-getting-started.md). Try our walkthrough to see an OpenAPI definition in action.
* [Azure Functions GitHub repository](https://github.com/Azure/Azure-Functions/). Check out the Functions repository to give us feedback on the API definition support preview. Make a GitHub issue for anything you want to see updated.
* [Azure Functions developer reference](functions-reference.md). Learn about coding functions and defining triggers and bindings.
