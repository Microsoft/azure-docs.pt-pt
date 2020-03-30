---
title: Metadados OpenAPI em Funções Azure
description: Visão geral do suporte da OpenAPI em Funções Azure
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: a80079574dc29c54de89f5275c65637b205742d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227403"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte de metadados OpenAPI 2.0 em Funções Azure (pré-visualização)
O suporte de metadados OpenAPI 2.0 (ex-Swagger) em Funções Azure é uma funcionalidade de pré-visualização que pode utilizar para escrever uma definição OpenAPI 2.0 dentro de uma aplicação de função. Em seguida, pode hospedar esse ficheiro utilizando a aplicação de função.

> [!IMPORTANT]
> A funcionalidade de pré-visualização do OpenAPI está disponível atualmente apenas no runtime 1.x. [Pode encontrar aqui](./functions-versions.md#creating-1x-apps) informações sobre como criar uma aplicação de funções 1.x.

[Os metadados OpenAPI](https://swagger.io/) permitem que uma função que acolhe uma API REST seja consumida por uma grande variedade de outros softwares. Este software inclui ofertas da Microsoft como o PowerApps e a [funcionalidade de Aplicações API do Azure App Service,](../app-service/overview.md)ferramentas de desenvolvimento de terceiros como [o Postman,](https://www.getpostman.com/docs/importing_swagger)e [muitos mais pacotes.](https://swagger.io/tools/)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Recomendamos começar com o [tutorial de início](./functions-api-definition-getting-started.md) e, em seguida, voltar a este documento para saber mais sobre funcionalidades específicas.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Ativar suporte de definição OpenAPI
Pode configurar todas as definições do OpenAPI na página **De Definição API** nas **funcionalidades**da Plataforma da sua aplicação de função .

> [!NOTE]
> A função Afuncionalidade de definição API não é suportada para o tempo de execução beta atualmente.

Para permitir a geração de uma definição OpenAPI hospedada e uma definição de início rápido, deset a fonte de **definição API** para **função (Pré-visualização)**. **O URL externo** permite que a sua função utilize uma definição OpenAPI que está alojada noutro lugar.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Gere um esqueleto Swagger a partir dos metadados da sua função
Um modelo pode ajudá-lo a começar a escrever a sua primeira definição OpenAPI. A função de modelo de definição cria uma definição aberta escassa utilizando todos os metadados no ficheiro function.json para cada uma das suas funções de gatilho HTTP. Terá de preencher mais informações sobre a sua API a partir da [especificação OpenAPI](https://swagger.io/specification/), como modelos de pedido e resposta.

Para instruções passo a passo, consulte o [tutorial de início.](./functions-api-definition-getting-started.md)

### <a name="available-templates"></a><a name="templates"></a>Modelos disponíveis

|Nome| Descrição |
|:-----|:-----|
|Definição Gerada|Uma definição OpenAPI com a quantidade máxima de informação que pode ser deduzida dos metadados existentes da função.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Metadados incluídos na definição gerada

A tabela seguinte representa as definições do portal Azure e os dados correspondentes em função.json, uma vez que é mapeado para o esqueleto swagger gerado.

|Swagger.json|Portal UI|Função.json|
|:----|:-----|:-----|
|[Anfitrião](https://swagger.io/specification/#fixed-fields-15)|**Definições** > de aplicativode**função Configurações** > de aplicativo > **seleção** **de visão geral**URL|*Não estar presente.*
|[Caminhos](https://swagger.io/specification/#paths-object-29)|**Integrar** > **métodos HTTP selecionados**|Encadernações: Rota
|[Item do Caminho](https://swagger.io/specification/#path-item-object-32)|**Integrar** > **modelo de rota**|Encadernações: Métodos
|[Segurança](https://swagger.io/specification/#security-scheme-object-112)|**Chaves**|*Não estar presente.*|
|operaçãoID*|**Rota + Verbos permitidos**|Rota + Verbos Permitidos|

\*O ID de funcionamento é necessário apenas para integrar com PowerApps e Flow.
> [!NOTE]
> A extensão x-ms-resumo fornece um nome de exibição em Aplicações Lógicas, PowerApps e Flow.
>
> Para saber mais, consulte [Personalizar a sua definição de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Utilize CI/CD para definir uma definição de API

 Tem de ativar o alojamento da definição API no portal antes de permitir que o controlo de origem modifique a definição de API a partir do controlo de origem. Siga estas instruções:

1. Navegue na **Definição API (pré-visualização)** nas definições da sua aplicação de função.
   1. Definir a fonte de **definição API** para **funcionar**.
   1. Clique em gerar modelo de **definição de API** e, em seguida, **guarde** para criar uma definição de modelo para modificar mais tarde.
   1. Note o URL de definição de API e a chave.
1. [Configurar a integração contínua/implantação contínua (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment).
2. Modificar swagger.json no controlo de fonte\.em \site\wwwroot azurefunctions\swagger\swagger.json.

Agora, as alterações para swagger.json no seu repositório são hospedadas pela sua aplicação de função no URL de definição API e chave que você observou no passo 1.c.

## <a name="next-steps"></a>Passos seguintes
* [Começar tutorial.](functions-api-definition-getting-started.md) Experimente a nossa passagem para ver uma definição OpenAPI em ação.
* [Funções Azure Repositório GitHub](https://github.com/Azure/Azure-Functions/). Consulte o repositório funções para nos dar feedback sobre a pré-visualização de suporte de definição API. Faça um problema gitHub para qualquer coisa que queira ver atualizado.
* Referência do [desenvolvedor de Funções Azure.](functions-reference.md) Aprenda sobre funções de codificação e definindo gatilhos e encadernações.
