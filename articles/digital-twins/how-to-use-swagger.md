---
title: Compreender como utilizar a referência de duplos Digital do Azure Swagger | Documentos da Microsoft
description: Noções básicas sobre como utilizar o Swagger do Azure Digital duplos documentação de referência.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/29/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 0b8c2b50e00c8e9727b09a454504d214a3060fe4
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502705"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentação de referência Swagger de duplos Digital do Azure

Cada instância de duplos Digital do Azure aprovisionada inclui a sua própria documentação de referência do Swagger gerada automaticamente.

[Swagger](https://swagger.io/), ou [OpenAPI](https://www.openapis.org/), une as informações de API complexas num recurso de referência interativa e de linguagem desconhecida. Swagger fornece o material de referência essencial sobre qual conteúdo JSON, métodos HTTP e pontos de extremidade específicos a utilizar para efetuar operações de uma API.

## <a name="swagger-summary"></a>Resumo do swagger

Swagger fornece um resumo interativo da sua API, que inclui:

* Informações de modelo de API e o objeto.
* Pontos finais da REST API que especificam o payloads de pedido necessária, cabeçalhos, parâmetros, caminhos de contexto e métodos HTTP.
* Teste de funcionalidades de API.
* Informações de resposta de exemplo utilizadas para validar e confirmar as respostas HTTP.
* Informações de código de erro.

Swagger é uma ferramenta conveniente para ajudá-lo em desenvolvimento e testes chamadas feitas para as APIs de gestão de duplos Digital do Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Material de referência

O material de referência de Swagger gerado automaticamente fornece uma visão geral de conceitos importantes, pontos finais da API de gestão disponíveis e uma descrição de cada modelo de objeto para o ajudar a desenvolvimento e teste.

Um resumo conciso descreve a API.

[![Parte superior do swagger](media/how-to-use-swagger/swagger_management_top.PNG)](media/how-to-use-swagger/swagger_management_top.PNG#lightbox)

Modelos de objeto de API de gestão também estão listados.

[![Modelos de swagger](media/how-to-use-swagger/swagger_management_models.PNG)](media/how-to-use-swagger/swagger_management_models.PNG#lightbox)

Pode selecionar cada modelo de objeto listadas para obter um resumo mais detalhado de atributos de chaves.

[![Modelo de swagger](media/how-to-use-swagger/swagger_management_model.PNG)](media/how-to-use-swagger/swagger_management_model.PNG#lightbox)

Os modelos de objeto do Swagger gerados são convenientes ver todos os gémeos de Digital do Azure disponíveis [objetos e APIs](./concepts-objectmodel-spatialgraph.md). Os desenvolvedores podem usar este recurso quando eles criam soluções no duplos Digital do Azure.

## <a name="endpoint-summary"></a>Ponto final de resumo

Swagger também fornece uma visão geral completa de todos os pontos finais que compõem as APIs de gestão.

Cada ponto de extremidade listado também inclui as informações de solicitação necessários, como o:

* Parâmetros necessários.
* Tipos de dados de parâmetro necessário.
* Método HTTP para aceder ao recurso.

[![Pontos de extremidade do swagger](media/how-to-use-swagger/swagger_management_endpoints.PNG)](media/how-to-use-swagger/swagger_management_endpoints.PNG#lightbox)

Para ver uma descrição mais detalhada, selecione cada recurso.

## <a name="use-swagger-to-test-endpoints"></a>Utilize o Swagger para pontos finais de teste

É uma das funcionalidades poderosas que swagger fornece a capacidade de testar um ponto final de API diretamente através da documentação da interface do Usuário.

Depois de selecionar um ponto de extremidade específico, consulte **experimentá-lo**.

[![Tente do swagger](media/how-to-use-swagger/swagger_management_try.PNG)](media/how-to-use-swagger/swagger_management_try.PNG#lightbox)

Expanda essa secção para aparecer em campos de entrada para cada um dos parâmetros obrigatório e opcional. Introduza os valores corretos e selecione **Execute**.

[![Tentei do swagger](media/how-to-use-swagger/swagger_management_tried.PNG)](media/how-to-use-swagger/swagger_management_tried.PNG#lightbox)

Depois de executar o teste, pode validar os dados de resposta.

## <a name="swagger-response-data"></a>Dados de resposta do swagger

Cada ponto de extremidade listado também inclui dados de corpo de resposta para validar o seu desenvolvimento e testes. Estes exemplos incluem os códigos de estado e o JSON que pretende ver os pedidos HTTP com êxito.

[![Resposta do swagger](media/how-to-use-swagger/swagger_management_response.PNG)](media/how-to-use-swagger/swagger_management_response.PNG#lightbox)

Os exemplos incluem também os códigos de erro para o ajudar a depurar ou melhorar a falhar de testes.

## <a name="swagger-oauth-20-authorization"></a>Autorização de OAuth 2.0 de swagger

> [!NOTE]
> * O principal de utilizador que criou o recurso de duplos Digital do Azure terão uma atribuição de função de administrador de espaço e será capaz de criar atribuições de função adicionais para outros utilizadores. Tais usuários e as respetivas funções podem ser autorizadas a chamar as APIs.

1. Siga os passos em [neste início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad) ou [registar a aplicação de duplos Digital do Azure no legado do Azure Active Directory](./how-to-use-legacy-aad.md) para criar e configurar uma aplicação do Azure AD. Em alternativa, pode reutilizar um registo de aplicação existente.

1. Adicione o seguinte url de resposta para o registo de aplicações:

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Name  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | O URL de documentação da API REST da gestão encontrado no portal do  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Copie o ID da sua aplicação do Azure AD.

Depois de concluir o registo do Azure Active Directory:

1. Selecione o **autorizar** botão na página do swagger.

    [![Selecione o Swagger autorizar botão](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Cole o ID da aplicação para o **client_id** campo.

    [![Campo de client_id do swagger](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Em seguida, será redirecionado para o sucesso seguinte modal.

    [![Modal de redirecionamento do swagger](media/how-to-use-swagger/swagger_auth_redirect.png)](media/how-to-use-swagger/swagger_auth_redirect.png#lightbox)

Para saber mais sobre testar interativamente os pedidos protegidos pelo OAuth 2.0, consulte a [documentação oficial](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Passos Seguintes

- Leia mais sobre modelos de objetos duplos Digital do Azure e o gráfico de inteligência espaciais, leia [modelos de objeto de compreender duplos de Digital do Azure](./concepts-objectmodel-spatialgraph.md).

- Para saber como autenticar com a API de gestão, leia [autenticar com as APIs](./security-authenticating-apis.md).