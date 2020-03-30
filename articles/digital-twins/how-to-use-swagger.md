---
title: Como utilizar a documentação de referência Swagger - Azure Digital Twins [ Azure Digital Twins ] Microsoft Docs
description: Compreender como usar a documentação de referência azure Digital Twins Swagger.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 27874e5731bd6fb9821e7aeda9333adbdbb79099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023298"
---
# <a name="azure-digital-twins-swagger-reference-documentation"></a>Documentação de referência azure Digital Twins Swagger

Cada instância de Azure Digital Twins aprovisionada inclui a sua própria documentação de referência swagger gerada automaticamente.

[Swagger](https://swagger.io/), ou [OpenAPI,](https://www.openapis.org/)une informação complexa da API num recurso de referência interativo e agnóstico. A Swagger fornece material de referência crítico sobre o qual as cargas jSON, métodos HTTP e pontos finais específicos para usar para realizar operações contra uma API.

## <a name="swagger-summary"></a>Resumo swagger

A Swagger fornece um resumo interativo da sua API, que inclui:

* Informação do modelo api e objeto.
* REST Pontos finais da API que especificam as cargas de pedido, cabeçalhos, parâmetros, percursos de contexto e métodos HTTP.
* Teste de funcionalidades de API.
* Informações de resposta de exemplo utilizadas para validar e confirmar respostas HTTP.
* Informação do código de erro.

A Swagger é uma ferramenta conveniente para ajudar no desenvolvimento e teste de chamadas feitas para as APIs de Gestão de Gémeos Digitais Azure.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

## <a name="reference-material"></a>Material de referência

O material de referência swagger gerado automaticamente fornece uma visão geral rápida de conceitos importantes, pontos finais de API de Gestão disponíveis, e uma descrição de cada modelo de objeto para ajudar no desenvolvimento e teste.

Um resumo conciso descreve a API.

[![Resumo swagger e informação geral da API](media/how-to-use-swagger/swagger-management-top-img.png)](media/how-to-use-swagger/swagger-management-top-img.png#lightbox)

Os modelos de objetos API de gestão também estão listados.

[![Modelos swagger listados no fundo da Swagger UI](media/how-to-use-swagger/swagger-management-models-img.png)](media/how-to-use-swagger/swagger-management-models-img.png#lightbox)

Pode selecionar cada modelo de objeto listado para um resumo mais detalhado dos principais atributos.

[![Modelos Swagger expandidos para ler o conteúdo dos modelos](media/how-to-use-swagger/swagger-management-model-img.png)](media/how-to-use-swagger/swagger-management-model-img.png#lightbox)

Os modelos de objetos Swagger gerados são convenientes para ler todos os objetos e [APIs](./concepts-objectmodel-spatialgraph.md)disponíveis das Gémeas Digitais Azure. Os desenvolvedores podem usar este recurso quando constroem soluções em Azure Digital Twins.

## <a name="endpoint-summary"></a>Resumo do ponto final

A Swagger também fornece uma visão geral completa de todos os pontos finais que compõem as APIs de Gestão.

Cada ponto final listado também inclui as informações de pedido exigidas, tais como:

* Parâmetros necessários.
* Tipos de dados de parâmetros necessários.
* MÉTODO HTTP para aceder ao recurso.

[![Pontos finais swagger exibidos na Swagger UI](media/how-to-use-swagger/swagger-management-endpoints-img.png)](media/how-to-use-swagger/swagger-management-endpoints-img.png#lightbox)

Selecione cada recurso para exibir os seus conteúdos adicionais para obter uma visão geral mais detalhada.

## <a name="use-swagger-to-test-endpoints"></a>Use swagger para testar pontos finais

Uma das funcionalidades poderosas que a Swagger fornece é a capacidade de testar um ponto final da API diretamente através da documentação UI.

Depois de selecionar um ponto final específico, será apresentado o botão **Experimente-o.**

[![Swagger Experimente o botão](media/how-to-use-swagger/swagger-management-try-img.png)](media/how-to-use-swagger/swagger-management-try-img.png#lightbox)

Expanda esta secção para criar campos de entrada para cada parâmetro necessário e opcional. Introduza os valores corretos e selecione **Executar**.

[![Swagger Experimente o exemplo do resultado](media/how-to-use-swagger/swagger-management-tried-img.png)](media/how-to-use-swagger/swagger-management-tried-img.png#lightbox)

Depois de executar o teste, pode validar os dados de resposta.

## <a name="swagger-response-data"></a>Dados de resposta swagger

Cada ponto final listado também inclui dados do corpo de resposta para validar o seu desenvolvimento e testes. Estes exemplos incluem os códigos de estado e jSON para pedidos http bem sucedidos.

[![Exemplo de resposta swagger JSON](media/how-to-use-swagger/swagger-management-response-img.png)](media/how-to-use-swagger/swagger-management-response-img.png#lightbox)

Os exemplos também incluem códigos de erro para ajudar a depurar ou melhorar os testes de falha.

## <a name="swagger-oauth-20-authorization"></a>Autorização swagger OAuth 2.0

> [!NOTE]
> * O diretor de utilizadores que criou o recurso Azure Digital Twins terá uma atribuição de função de Administrador Espacial e será capaz de criar atribuições de funções adicionais para outros utilizadores. Esses utilizadores e as suas funções podem ser autorizados a ligar para as APIs.

1. Siga os passos [no Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) para criar e configurar uma aplicação azure Ative Directory. Em alternativa, pode reutilizar o registo de aplicações existente.

1. Adicione o seguinte **Redirect URI** ao registo da sua aplicação Azure Ative Directory:

    [![Registre a url de redirecionamento swagger em AAD](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png)](media/how-to-use-swagger/swagger-aad-redirect-url-registration.png#lightbox)

    ```plaintext
    https://YOUR_SWAGGER_URL/ui/oauth2-redirect-html
    ```
    | Nome  | Substituir | Exemplo |
    |---------|---------|---------|
    | YOUR_SWAGGER_URL | URL de documentação da Sua Gestão REST API encontrado no portal  | `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/swagger` |

1. Selecione a caixa de verificação de**fichas** de acesso **implícita** > para permitir a utilizada do fluxo de subvenção implícito OAuth 2.0. **Selecione Configurar**e, em seguida, **guardar**.

1. Copie a **identificação** do cliente da sua aplicação Azure Ative Directory.

Após a conclusão do registo do Diretório Ativo Azure:

1. Selecione o botão **Autorizar** na sua página de swagger.

    [![Selecione o botão de autorização Swagger](media/how-to-use-swagger/swagger-select-authorize-btn.png)](media/how-to-use-swagger/swagger-select-authorize-btn.png#lightbox)

1. Colar o ID da aplicação no campo **client_id.**

    [![Campo swagger client_id](media/how-to-use-swagger/swagger-auth-form.png)](media/how-to-use-swagger/swagger-auth-form.png#lightbox)

1. Em seguida, será redirecionado para o seguinte sucesso modal.

    [![Modal de redirecionamento swagger](media/how-to-use-swagger/swagger-auth-redirect-img.png)](media/how-to-use-swagger/swagger-auth-redirect-img.png#lightbox)

Para saber mais sobre os pedidos de teste interativos protegidos pela OAuth 2.0, leia a [documentação oficial](https://swagger.io/docs/specification/authentication/oauth2/).

## <a name="next-steps"></a>Passos seguintes

- Para ler mais sobre os modelos de objetos Azure Digital Twins e o gráfico de inteligência espacial, leia os modelos de [objetos De Pares Digitais Do Understand Azure](./concepts-objectmodel-spatialgraph.md).

- Para aprender a autenticar com a sua API de Gestão, leia [Authenticato com APIs](./security-authenticating-apis.md).
