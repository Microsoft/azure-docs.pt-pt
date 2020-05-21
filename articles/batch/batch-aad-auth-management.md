---
title: Utilize o Diretório Ativo Azure para autenticar soluções de Gestão de Lotes
description: Explore utilizando o Azure Ative Directory para autenticar a partir de aplicações que utilizam a biblioteca Batch Management .NET.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref
ms.openlocfilehash: ec9cf15f37c3ca7e4e477c628733d34cac21c141
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726898"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Soluções de Gestão de Lotes Autenticados com Diretório Ativo

Aplicações que chamam o serviço de Gestão de Lotes Azure autenticado com [Diretório Ativo Azure][aad_about] (Azure AD). A Azure AD é o serviço de diretório e gestão de identidade multi-inquilinos da Microsoft. O próprio Azure utiliza a Azure AD para a autenticação dos seus clientes, administradores de serviços e utilizadores organizacionais.

A biblioteca Batch Management .NET expõe tipos para trabalhar com contas de Lote, chaves de conta, aplicações e pacotes de aplicações. A biblioteca Batch Management .NET é um cliente fornecedor de recursos Azure, e é usada em conjunto com o [Azure Resource Manager][resman_overview] para gerir estes recursos programáticamente. A Azure AD é obrigada a autenticar pedidos feitos através de qualquer cliente do fornecedor de recursos Azure, incluindo a biblioteca Batch Management .NET, e através do [Azure Resource Manager.][resman_overview]

Neste artigo, exploramos o uso de Azure AD para autenticar a partir de aplicações que utilizam a biblioteca Batch Management .NET. Mostramos como usar a AD Azure para autenticar um administrador de subscrição ou coadministrador, utilizando a autenticação integrada. Utilizamos o projeto de amostra [De Gestão de Contas,][acct_mgmt_sample] disponível no GitHub, para passar pela utilização do Azure AD com a biblioteca Batch Management .NET.

Para saber mais sobre a utilização da biblioteca Batch Management .NET e da amostra De Gestão de Contas, consulte gerir contas e quotas de Lote com a biblioteca de clientes da [Batch Management para .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registe a sua candidatura na Azure AD

A Biblioteca de [Autenticação de Diretórios Ativos][aad_adal] Azure (ADAL) fornece uma interface programática à Azure AD para utilização dentro das suas aplicações. Para ligar para a ADAL a partir da sua candidatura, deve registar a sua candidatura num inquilino da AD Azure. Ao registar a sua candidatura, fornece à Azure AD informações sobre a sua aplicação, incluindo um nome para o mesmo dentro do inquilino da AD Azure. A Azure AD fornece então um ID de aplicação que utiliza para associar a sua aplicação ao Azure AD em tempo de execução. Para saber mais sobre o ID da aplicação, consulte [os objetos principais de aplicação e serviço no Diretório Ativo azure](../active-directory/develop/app-objects-and-service-principals.md).

Para registar a aplicação da amostra De Gestão de Contas, siga os passos na secção [adicionar uma aplicação](../active-directory/develop/quickstart-register-app.md) na integração de [aplicações com o Diretório Ativo azure][aad_integrate]. Especifique a **Aplicação do Cliente Nativo** para o tipo de aplicação. O padrão da indústria OAuth 2.0 URI para o **Redirect URI** é `urn:ietf:wg:oauth:2.0:oob` . No entanto, pode especificar qualquer URI válido `http://myaccountmanagementsample` (como) para o **Redirect URI,** uma vez que não precisa de ser um verdadeiro ponto final:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Assim que concluir o processo de registo, verá o ID da aplicação e o ID do objeto (principal de serviço) listado para a sua aplicação.

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceda ao Gestor de Recursos Azure acesso à sua aplicação

Em seguida, terá de delegar acesso à sua candidatura à API do Gestor de Recursos Azure. O identificador Azure AD para o Gestor de Recursos API é a API de **Gestão de Serviços Windows Azure**.

Siga os passos abaixo no portal do Azure:

1. No painel de navegação à esquerda do portal Azure, escolha **Todos os serviços,** clique em Registos de **Aplicações,** e clique em **Adicionar**.
2. Procure o nome da sua aplicação na lista de registos de aplicações:

    ![Procure o seu nome de candidatura](./media/batch-aad-auth-management/search-app-registration.png)

3. Exiba a lâmina **Definições.** Na secção **Acesso API,** selecione **permissões necessárias**.
4. Clique em **Adicionar** para adicionar uma nova permissão necessária.
5. No passo 1, introduza a API de Gestão de **Serviços Windows Azure,** selecione essa API da lista de resultados e clique no botão **Select.**
6. No passo 2, selecione a caixa de verificação ao lado do **modelo de implementação clássico**do Access Azure como utilizadores da organização , e clique no botão **Select.**
7. Clique no botão **Done.**

A lâmina **de permissões necessárias** mostra agora que as permissões à sua aplicação são concedidas tanto às APIs aADAL como ao Gestor de Recursos. As permissões são concedidas à ADAL por padrão quando regista a sua aplicação pela primeira vez com o Azure AD.

![Autorizações de delegados à API gestora de recursos do Azure](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Pontos finais da AD Azure

Para autenticar as suas soluções de Gestão de Lotes com a AD Azure, vai precisar de dois pontos finais bem conhecidos.

- O **ponto final comum da AD Azure** fornece uma interface de reunião de credenciais genéricas quando não é fornecido um inquilino específico, como no caso da autenticação integrada:

    `https://login.microsoftonline.com/common`

- O ponto final do Gestor de **Recursos Azure** é utilizado para adquirir um símbolo para autenticação de pedidos ao serviço de gestão de Lotes:

    `https://management.core.windows.net/`

A aplicação da amostra De Gestão de Contas define constantes para estes pontos finais. Deixe estas constantes inalteradas:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referenciar o seu ID de aplicação

A sua aplicação de cliente utiliza o ID da aplicação (também referido como ID do cliente) para aceder à AD Azure em tempo de execução. Depois de ter registado a sua aplicação no portal Azure, atualize o seu código para utilizar o ID de aplicação fornecido pela Azure AD para a sua aplicação registada. Na aplicação da amostra De Gestão de Contas, copie o seu ID de aplicação do portal Azure para a constante adequada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Copie também o URI redirecionado que especificou durante o processo de registo. O URI redirecionado especificado no seu código deve corresponder ao URI redirecionado que forneceu quando registou a aplicação.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um símbolo de autenticação Azure AD

Depois de registar a amostra de Gestão de Contas no inquilino da AD Azure e atualizar o código fonte da amostra com os seus valores, a amostra está pronta para autenticar utilizando a AD Azure. Quando executa a amostra, a ADAL tenta adquirir um símbolo de autenticação. Neste passo, solicita-lhe as credenciais da Microsoft:

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

Depois de fornecer as suas credenciais, o pedido de amostra pode proceder à emissão de pedidos autenticados ao serviço de gestão do Lote.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a execução da aplicação da [amostra De Gestão][acct_mgmt_sample]de Contas, consulte gerir contas e quotas de Lote com a biblioteca de clientes da Batch Management para [.NET](batch-management-dotnet.md).

Para saber mais sobre o Azure AD, consulte a Documentação de [Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/) Exemplos aprofundados que mostram como utilizar o ADAL estão disponíveis na biblioteca De amostras de [código azul.](https://azure.microsoft.com/resources/samples/?service=active-directory)

Para autenticar aplicações de serviço batch utilizando a AD Azure, consulte soluções de [serviço Authenticate Batch com Diretório Ativo](batch-aad-auth.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Ative Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação para AD Azure"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integração de Aplicações com Diretório Ativo Azure"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
