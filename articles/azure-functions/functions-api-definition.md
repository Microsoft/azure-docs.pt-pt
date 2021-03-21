---
title: Metadados OpenAPI em Funções Azure
description: Visão geral do suporte do OpenAPI em Funções Azure
ms.topic: conceptual
ms.date: 03/23/2017
ms.openlocfilehash: 499d4f685e3802fcc37c8a3050ae367207f192d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000215"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte de metadados OpenAPI 2.0 em Funções Azure (pré-visualização)
O suporte de metadados OpenAPI 2.0 (anteriormente Swagger) em Funções Azure é uma funcionalidade de pré-visualização que pode utilizar para escrever uma definição OpenAPI 2.0 dentro de uma aplicação de função. Em seguida, pode hospedar esse ficheiro utilizando a aplicação de função.

> [!IMPORTANT]
> A funcionalidade de pré-visualização do OpenAPI está disponível atualmente apenas no runtime 1.x. [Pode encontrar aqui](./functions-versions.md#creating-1x-apps) informações sobre como criar uma aplicação de funções 1.x.

[Os metadados OpenAPI](https://swagger.io/) permitem que uma função que está hospedando uma API REST seja consumida por uma grande variedade de outros softwares. Este software inclui ofertas da Microsoft como PowerApps e a [funcionalidade API Apps do Azure App Service](../app-service/overview.md), ferramentas de desenvolvedores de terceiros como o [Carteiro](https://www.getpostman.com/docs/importing_swagger), e [muitos mais pacotes.](https://swagger.io/tools/)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Recomendamos começar com o [tutorial de início](./functions-openapi-definition.md) e, em seguida, voltar a este documento para saber mais sobre funcionalidades específicas.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Ativar o suporte de definição OpenAPI
Pode configurar todas as definições do OpenAPI na página **API Definition** nas funcionalidades da Plataforma da sua aplicação **de funções.**

> [!NOTE]
> A função API definition feature não é suportada para o tempo de execução beta atualmente.

Para permitir a geração de uma definição OpenAPI hospedada e uma definição de arranque rápido, deteta **a fonte de definição de API** para **função (Pré-visualização)**. **O URL externo** permite que a sua função utilize uma definição OpenAPI que esteja hospedada em outro lugar.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Gere um esqueleto swagger a partir dos metadados da sua função
Um modelo pode ajudá-lo a começar a escrever a sua primeira definição OpenAPI. A função de modelo de definição cria uma definição abreAPI escassa utilizando todos os metadados do function.jsno ficheiro para cada uma das suas funções de gatilho HTTP. Você precisará preencher mais informações sobre a sua API a partir da [especificação OpenAPI](https://swagger.io/specification/), como modelos de pedido e resposta.

Para obter instruções passo a passo, consulte o [tutorial de início.](./functions-openapi-definition.md)

### <a name="available-templates"></a><a name="templates"></a>Modelos disponíveis

|Nome| Descrição |
|:-----|:-----|
|Definição Gerada|Uma definição OpenAPI com a quantidade máxima de informação que pode ser deduzida dos metadados existentes da função.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Metadados incluídos na definição gerada

A tabela seguinte representa as definições do portal Azure e os dados correspondentes em function.jsà medida que é mapeado para o esqueleto swagger gerado.

|Swagger.jsem|Portal UI|Function.jsem|
|:----|:-----|:-----|
|[Anfitrião](https://swagger.io/specification/#fixed-fields-15)|**Definições de aplicativos de função**  >  **Definições de Serviço de Aplicações**  >  **Visão geral**  >  **URL**|*Não presente*
|[Caminhos](https://swagger.io/specification/#paths-object-29)|**Integrar**  >  **Métodos HTTP selecionados**|Encadernações: Rota
|[Item do caminho](https://swagger.io/specification/#path-item-object-32)|**Integrar**  >  **Modelo de rota**|Encadernações: Métodos
|[Segurança](https://swagger.io/specification/#security-scheme-object-112)|**Chaves**|*Não presente*|
|operationID*|**Rota + Verbos permitidos**|Rota + Verbos Permitidos|

\*O ID de operação é necessário apenas para integração com PowerApps e Flow.
> [!NOTE]
> A extensão x-ms-resumo fornece um nome de exibição em Apps Lógicas, PowerApps e Flow.
>
> Para saber mais, consulte [Personalizar a sua definição de Swagger para PowerApps.](/connectors/custom-connectors/openapi-extensions)

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>Utilize CI/CD para definir uma definição de API

 Tem de ativar a definição de API no portal antes de permitir que o controlo de origem modifique a sua definição de API a partir do controlo de origem. Siga estas instruções:

1. Navegue pela **Definição de API (pré-visualização)** nas definições da aplicação de funções.
   1. Definir **fonte de definição de API** para **função**.
   1. Clique **no modelo de definição de API** gerar e, em seguida, **guarde** para criar uma definição de modelo para modificar mais tarde.
   1. Note o URL e a chave de definição API.
1. [Configurar a integração contínua/implantação contínua (CI/CD)](./functions-continuous-deployment.md#requirements-for-continuous-deployment).
2. Modifique swagger.jsno controlo de origem em \site\wwwroot \.azurefunctions\swagger\swagger.jsligado.

Agora, as alterações ao swagger.jsno seu repositório são hospedadas pela sua aplicação de função no URL de definição API e na chave que notou no passo 1.c.

## <a name="next-steps"></a>Passos seguintes
* [Começar tutorial.](./functions-openapi-definition.md) Experimente a nossa passagem para ver uma definição OpenAPI em ação.
* [Funções Azure GitHub repositório](https://github.com/Azure/Azure-Functions/). Consulte o repositório de funções para nos dar feedback sobre a pré-visualização do suporte de definição API. Faça um problema do GitHub para tudo o que quiser ver atualizado.
* [Referência do desenvolvedor de Funções Azure](functions-reference.md). Saiba mais sobre funções de codificação e definição de gatilhos e encadernações.
