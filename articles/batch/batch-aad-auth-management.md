---
title: Utilize o Diretório Ativo Azure para autenticar soluções de gestão de lotes
description: Explore a utilização do Azure Ative Directory para autenticar a partir de aplicações que utilizam a biblioteca Batch Management .NET.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930218"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autenticar soluções de gestão de lotes com Diretório Ativo

As aplicações que chamam o serviço de Gestão de Lotes Azure autenticam-se com [o Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD é o serviço de gestão de identidade e diretório multi-inquilino da Microsoft. A própria Azure utiliza a Azure AD para a autenticação dos seus clientes, administradores de serviços e utilizadores organizacionais.

A biblioteca Batch Management .NET expõe tipos para trabalhar com contas Batch, chaves de conta, aplicações e pacotes de aplicações. A biblioteca Batch Management .NET é um cliente fornecedor de recursos Azure, e é usada juntamente com [a Azure Resource Manager](../azure-resource-manager/management/overview.md) para gerir estes recursos programáticamente. O Azure AD é obrigado a autenticar pedidos escorriam por qualquer cliente fornecedor de recursos Azure, incluindo a biblioteca Batch Management .NET e através do Azure Resource Manager.

Neste artigo, exploramos a utilização do Azure AD para autenticar a partir de aplicações que utilizam a biblioteca Batch Management .NET. Mostramos como usar a Azure AD para autenticar um administrador de subscrição ou coadministrador, utilizando a autenticação integrada. Utilizamos o projeto de amostra [de gestão de conta,](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) disponível no GitHub, para passear através do Azure AD com a biblioteca Batch Management .NET.

Para saber mais sobre a utilização da biblioteca Batch Management .NET e da amostra de Gestão de Contas, consulte [contas e quotas do Lote com a biblioteca de clientes de Gestão de Lotes para .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registe a sua candidatura com Azure AD

A [Azure Ative Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) fornece uma interface programática ao Azure AD para utilização dentro das suas aplicações. Para ligar para a ADAL a partir do seu pedido, deve registar o seu pedido num inquilino da AD Azure. Quando regista a sua candidatura, fornece à Azure AD informações sobre a sua candidatura, incluindo um nome para ele dentro do inquilino Azure AD. A Azure AD fornece então um ID de aplicação que utiliza para associar a sua aplicação à Azure AD no tempo de execução. Para saber mais sobre o ID da aplicação, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure.](../active-directory/develop/app-objects-and-service-principals.md)

Para registar o pedido de amostra de Gestão de Contas, siga os passos na secção [adicionar uma aplicação](../active-directory/develop/quickstart-register-app.md) na [integração de aplicações com o Azure Ative Directory](../active-directory/develop/quickstart-register-app.md). Especifique **a Aplicação do Cliente Nativo** para o tipo de aplicação. O padrão da indústria OAuth 2.0 URI para o **Redirect URI** é `urn:ietf:wg:oauth:2.0:oob` . No entanto, pode especificar qualquer URI válido (como `http://myaccountmanagementsample` ) para o **URI de redirecionamento,** uma vez que não precisa de ser um verdadeiro ponto final.

![Adicionar uma aplicação](./media/batch-aad-auth-management/app-registration-management-plane.png)

Assim que concluir o processo de registo, verá o ID da aplicação e o iD do objeto (principal serviço) listado para a sua candidatura.

![Processo de registo concluído](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceda ao Gestor de Recursos AZure acesso à sua aplicação

Em seguida, terá de delegar o acesso à sua candidatura à API do Gestor de Recursos Azure. O identificador AZure AD para o Gestor de Recursos API é **a API de Gestão de Serviços Windows Azure**.

Siga os passos abaixo no portal do Azure:

1. No painel de navegação à esquerda do portal Azure, escolha **Todos os serviços,** clique em **Registos de Aplicações** e clique em **Adicionar**.
2. Procure o nome da sua aplicação na lista de registos de aplicações:

    ![Procure o nome da sua candidatura](./media/batch-aad-auth-management/search-app-registration.png)

3. Mostrar a lâmina **de definições.** Na secção **de Acesso API,** selecione **permissões necessárias**.
4. Clique **em Adicionar** para adicionar uma nova permissão necessária.
5. No passo 1, insira a API de Gestão de **Serviços do Windows Azure,** selecione a API da lista de resultados e clique no botão **Select.**
6. No passo 2, selecione a caixa de verificação ao lado do **modelo de implementação clássica do Access Azure como utilizadores** da organização e clique no botão **Select.**
7. Clique no botão **'Fazer'.**

A lâmina **de permissões necessárias** mostra agora que as permissões à sua aplicação são concedidas tanto às APIs do ADAL como do Gestor de Recursos. As permissões são concedidas à ADAL por padrão quando regista a sua aplicação pela primeira vez com a Azure AD.

![Autorizações de delegado à Azure Resource Manager API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Pontos finais da AD AZure

Para autenticar as suas soluções de Gestão de Lotes com Azure AD, necessitará de dois pontos finais bem conhecidos.

- O **ponto final comum Azure AD** fornece uma interface de recolha de credenciais genéricas quando um inquilino específico não é fornecido, como no caso de autenticação integrada:

    `https://login.microsoftonline.com/common`

- O **ponto final do Azure Resource Manager** é utilizado para adquirir um símbolo para autenticar pedidos ao serviço de gestão batch:

    `https://management.core.windows.net/`

A aplicação da amostra de Gestão de Contas define constantes para estes pontos finais. Deixe estas constantes inalteradas:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referenciar o seu ID de aplicação

A sua aplicação de cliente utiliza o ID da aplicação (também referido como ID do cliente) para aceder à Azure AD em tempo de execução. Depois de ter registado a sua candidatura no portal Azure, atualize o seu código para utilizar o ID da aplicação fornecido pela Azure AD para a sua aplicação registada. Na aplicação de amostra de Gestão de Contas, copie o ID da sua aplicação do portal Azure para a constante apropriada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Copie também o URI de redirecionamento que especificou durante o processo de registo. O URI de redirecionamento especificado no seu código deve coincidir com o URI de redirecionamento que forneceu quando registou a aplicação.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação AD AZure

Depois de registar a amostra de Gestão de Conta no inquilino Azure AD e atualizar o código fonte da amostra com os seus valores, a amostra está pronta para autenticar usando Azure AD. Quando executam a amostra, o ADAL tenta adquirir um token de autenticação. Neste passo, solicita-lhe as suas credenciais microsoft:

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Depois de fornecer as suas credenciais, o pedido de amostra pode proceder à emissão de pedidos autenticados ao serviço de gestão batch.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a execução do [pedido de amostra de Gestão de Contas,](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement)consulte gerir contas e quotas do Lote com a biblioteca de [clientes de Gestão de Lotes para .NET](batch-management-dotnet.md).
- Para saber mais sobre a Azure AD, consulte a [Documentação do Diretório Ativo Azure](../active-directory/index.yml).
- Exemplos aprofundados que mostram como utilizar o ADAL estão disponíveis na biblioteca [Azure Code Samples.](https://azure.microsoft.com/resources/samples/?service=active-directory)
- Para autenticar aplicações de serviço batch utilizando Azure AD, consulte [soluções de serviço Authenticate Batch com Diretório Ativo.](batch-aad-auth.md)
