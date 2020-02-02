---
title: Como usar a documentação do Swagger de referência-Azure digital gêmeos | Microsoft Docs
description: Noções básicas sobre como usar a documentação de referência do Azure digital gêmeos Swagger.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 86b7a1b373d80f0e6bb7f40f57f71d20e396a6c1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934602"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentação de referência do Swagger do Azure digital gêmeos

Cada instância do Azure digital gêmeos provisionada inclui sua própria documentação de referência do Swagger gerada automaticamente.

O [Swagger](https://swagger.io/), ou [openapi](https://www.openapis.org/), une informações de API complexas em um recurso de referência interativo e independente de linguagem. O Swagger fornece material de referência crítica sobre quais cargas JSON, métodos HTTP e pontos de extremidade específicos usar para executar operações em uma API.

## <a name="swagger-summary"></a>Resumo do Swagger

O Swagger fornece um resumo interativo de sua API, que inclui:

* Informações de API e modelo de objeto.
* Pontos de extremidade da API REST que especificam as cargas de solicitação, os cabeçalhos, os parâmetros, os caminhos de contexto e os métodos HTTP necessários.
* Teste de funcionalidades de API.
* Informações de resposta de exemplo usadas para validar e confirmar respostas HTTP.
* Informações do código de erro.

O Swagger é uma ferramenta conveniente para auxiliar nas chamadas de desenvolvimento e teste feitas para as APIs de gerenciamento de gêmeos digital do Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Material de referência

O material de referência do Swagger gerado automaticamente fornece uma visão geral rápida de conceitos importantes, pontos de extremidade da API de gerenciamento disponíveis e uma descrição de cada modelo de objeto para ajudar no desenvolvimento e no teste.

Um resumo conciso descreve a API.

[![resumo swagger e informações de visão geral da API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Os modelos de objeto de API de gerenciamento também são listados.

[modelos ![Swagger listados no fundo da Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Você pode selecionar cada modelo de objeto listado para obter um resumo mais detalhado dos principais atributos.

[![modelos Swagger expandidos para ler o conteúdo dos modelos](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Os modelos de objeto do Swagger gerados são convenientes para ler todos os [objetos e APIs](./concepts-objectmodel-spatialgraph.md)do Azure digital gêmeos disponíveis. Os desenvolvedores podem usar esse recurso quando criarem soluções no Azure digital gêmeos.

## <a name="endpoint-summary"></a>Resumo do ponto de extremidade

O Swagger também fornece uma visão geral completa de todos os pontos de extremidade que compõem as APIs de gerenciamento.

Cada ponto de extremidade listado também inclui as informações de solicitação necessárias, como:

* Parâmetros necessários.
* Tipos de dados de parâmetro necessários.
* Método HTTP para acessar o recurso.

[![pontos finais swagger exibidos na Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Selecione cada recurso para exibir seu conteúdo adicional para obter uma visão geral mais detalhada.

## <a name="use-swagger-to-test-endpoints"></a>Usar o Swagger para testar pontos de extremidade

Uma das poderosas funcionalidades que o Swagger fornece é a capacidade de testar um ponto de extremidade de API diretamente por meio da interface do usuário da documentação.

Depois de selecionar um ponto de extremidade específico, o botão **experimentar** será exibido.

[![Swagger Experimente o botão](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Expanda essa seção para exibir os campos de entrada para cada parâmetro obrigatório e opcional. Insira os valores corretos e selecione **executar**.

[![Swagger Experimente o exemplo do resultado](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Depois de executar o teste, você pode validar os dados de resposta.

## <a name="swagger-response-data"></a>Dados de resposta do Swagger

Cada ponto de extremidade listado também inclui dados de corpo de resposta para validar o desenvolvimento e os testes. Esses exemplos incluem os códigos de status e o JSON para solicitações HTTP bem-sucedidas.

[![Swagger JSON exemplo de resposta](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Os exemplos também incluem códigos de erro para ajudar a depurar ou melhorar os testes com falha.

## <a name="swagger-oauth-20-authorization"></a>Autorização de OAuth 2,0 do Swagger

> [!NOTE]
> * A entidade de usuário que criou o recurso de gêmeos digital do Azure terá uma atribuição de função de administrador de espaço e poderá criar atribuições de função adicionais para outros usuários. Tais usuários e suas funções podem ser autorizados a chamar as APIs.

1. Siga as etapas neste guia de [início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) para criar e configurar um aplicativo do Azure AD. Como alternativa, você pode reutilizar um registro de aplicativo existente.

1. Adicione a seguinte **URL de redirecionamento** ao registro do aplicativo do Azure AD:

    [![Register Swagger redireciona url em AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nome  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | A URL da documentação da API REST de gerenciamento encontrada no portal  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Copie a **ID do cliente** do seu aplicativo do Azure AD.

Depois de concluir o registro de Azure Active Directory:

1. Selecione o botão **autorizar** na sua página do Swagger.

    [![Selecione o botão de autorização swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Colar o ID da aplicação no campo **client_id.**

    [campo de client_id ![Swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Em seguida, você será redirecionado para o seguinte modal com êxito.

    [![Swagger redireciona modal](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Para saber mais sobre as solicitações de teste interativamente protegidas pelo OAuth 2,0, leia a [documentação oficial](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Passos seguintes

- Para ler mais sobre os modelos de objetos do Azure digital gêmeos e o grafo de inteligência espacial, leia [entender os modelos de objetos do Azure digital gêmeos](./concepts-objectmodel-spatialgraph.md).

- Para saber como autenticar com sua API de gerenciamento, leia [autenticar com APIs](./security-authenticating-apis.md).
