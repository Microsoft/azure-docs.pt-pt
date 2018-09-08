---
title: Metadados de OpenAPI nas funções do Azure | Documentos da Microsoft
description: Descrição geral do suporte de OpenAPI nas funções do Azure
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 68077e576fe3451627d8a5c8e1ff1b26d06d96b7
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091851"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte de metadados de OpenAPI 2.0 nas funções do Azure (pré-visualização)
O OpenAPI 2.0 (anteriormente Swagger) suporte a metadados nas funções do Azure é uma funcionalidade de pré-visualização que pode utilizar para escrever uma definição de OpenAPI 2.0 dentro de uma aplicação de funções. Em seguida, pode alojar esse ficheiro com a aplicação de função.

[Metadados de OpenAPI](http://swagger.io/) permite que uma função que está a alojar uma API REST para serem consumidos por uma grande variedade de outros programas de software. Este software contém as ofertas da Microsoft, como o PowerApps e o [funcionalidade aplicações API do serviço de aplicações do Azure](../app-service/app-service-web-overview.md), como ferramentas de programação de terceiros [Postman](https://www.getpostman.com/docs/importing_swagger), e [muitos mais pacotes](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Recomendamos que comece com o [tutorial de introdução](./functions-api-definition-getting-started.md) e, em seguida, retornar a este documento para obter mais informações sobre funcionalidades específicas.

## <a name="enable"></a>Ativar o suporte de definição de OpenAPI
Pode configurar todas as definições de OpenAPI no **definição de API** página na sua aplicação function app **recursos da plataforma**.

> [!NOTE]
> Recurso de definição de API de função não é suportado atualmente para o beta runtime.

Para ativar a geração de uma definição de OpenAPI alojada e uma definição de início rápido, defina **origem de definição de API** ao **função (pré-visualização)**. **URL externo** permite que sua função para utilizar uma definição de OpenAPI tem alojado noutro local.

## <a name="generate-definition"></a>Gerar um esqueleto do Swagger a partir dos metadados da sua função
Um modelo pode ajudá-lo a começar a escrever a sua primeira definição de OpenAPI. O recurso de modelo de definição de cria uma definição de OpenAPI dispersa com todos os metadados no ficheiro Function para cada uma das suas funções de Acionador HTTP. Precisará preencher mais informações sobre a sua API do [especificação de OpenAPI](http://swagger.io/specification/), tais como modelos de pedido e resposta.

Para obter instruções passo a passo, consulte a [tutorial de introdução](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Modelos disponíveis

|Nome| Descrição |
|:-----|:-----|
|Definição gerada|Uma definição de OpenAPI com a quantidade máxima de informações que podem ser inferidas a partir de metadados de existente da função.|

### <a name="quickstart-details"></a>Metadados incluídos na definição do gerado

A tabela seguinte representa as definições do portal do Azure e os dados correspondentes na Function conforme está mapeado para o esqueleto de Swagger gerado.

|Swagger.json|IU do portal|Function.json|
|:----|:-----|:-----|
|[Anfitrião](http://swagger.io/specification/#fixed-fields-15)|**Definições da aplicação de função** > **definições do serviço de aplicações** > **descrição geral** > **URL**|*Não está presente*
|[Caminhos](http://swagger.io/specification/#paths-object-29)|**Integre** > **métodos HTTP selecionados**|Enlaces: rota
|[Item do caminho](http://swagger.io/specification/#path-item-object-32)|**Integre** > **modelo de rota**|Enlaces: métodos
|[Segurança](http://swagger.io/specification/#security-scheme-object-112)|**chaves**|*Não está presente*|
|operationID *|**Rota + verbos permitidos**|Rota + verbos permitidos|

\*O ID de operação é necessário apenas para a integração com o PowerApps e Flow.
> [!NOTE]
> A extensão de x-ms-summary fornece um nome a apresentar no Logic Apps, PowerApps e Flow.
>
> Para obter mais informações, consulte [personalizar a sua definição de Swagger para o PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Utilize o CI/CD para definir uma definição de API

 Tem de ativar a definição de API de hospedagem no portal do antes de ativar o controlo de origem modificar a definição de API de controlo de origem. Siga estas instruções:

1. Navegue até **definição de API (pré-visualização)** nas suas definições de aplicação de função.
  1. Definir **origem de definição de API** ao **função**.
  1. Clique em **modelo de definição de API de gerar** e, em seguida **guardar** para criar uma definição de modelo para modificar mais tarde.
  1. Tenha em atenção que o URL de definição de API e a chave.
1. [Configurar a implementação de integração contínua/contínua (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Modifique o swagger no controle de origem em \site\wwwroot\.azurefunctions\swagger\swagger.json.

Agora, as alterações para swagger no seu repositório estão alojadas pela sua aplicação de função na API do URL de definição e a chave que anotou no passo 1.c.

## <a name="next-steps"></a>Passos Seguintes
* [Tutorial de introdução](functions-api-definition-getting-started.md). Experimente o nosso passo a passo para ver uma definição de OpenAPI em ação.
* [Repositório de GitHub das funções do Azure](https://github.com/Azure/Azure-Functions/). Veja o repositório de funções para dar-nos comentários sobre a pré-visualização de suporte de definição de API. Tornar um problema do GitHub para tudo que quiser ver atualizado.
* [Referência para programadores do funções do Azure](functions-reference.md). Saiba mais sobre as funções de codificação e definir acionadores e enlaces.
