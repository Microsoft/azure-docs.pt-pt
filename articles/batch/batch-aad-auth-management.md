---
title: Utilizar o Azure Active Directory para autenticar soluções de gestão do Batch | Documentos da Microsoft
description: Os aplicativos criados com o Gestor de recursos do Azure e o fornecedor de recursos do Batch autenticar com o Azure AD.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 22cab5f22eccabf9176d777b1e3a3356cbf37c4f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534247"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autenticar soluções de gestão do Batch com o Active Directory

Aplicativos que chamam o serviço de gestão do Azure Batch autenticar com o [do Azure Active Directory] [ aad_about] (Azure AD). O Azure AD é o diretório de multi-inquilino com base na cloud da Microsoft e o serviço de gestão de identidade. Azure próprio utiliza o Azure AD para a autenticação dos seus clientes, os administradores de serviços e utilizadores organizacionais.

A biblioteca de gestão de lotes .NET expõe tipos para trabalhar com contas do Batch, chaves de conta, aplicativos e pacotes de aplicações. A biblioteca de gestão de lotes .NET é um cliente do fornecedor de recursos do Azure e é utilizada em conjunto com [do Azure Resource Manager] [ resman_overview] gerir estes recursos por meio de programação. O Azure AD é necessário para autenticar pedidos feitos por meio de qualquer cliente de fornecedor de recursos do Azure, incluindo a biblioteca de gestão de lotes .NET e [do Azure Resource Manager][resman_overview].

Neste artigo, vamos explorar a utilizar o Azure AD para autenticar a partir de aplicações que utilizam a biblioteca de gestão de lotes .NET. Vamos mostrar como utilizar o Azure AD para autenticar um administrador da subscrição ou coadministrador, utilizando a autenticação integrada. Vamos utilizar o [AccountManagement] [ acct_mgmt_sample] projeto de exemplo disponível no GitHub, para percorrer a utilizar o Azure AD com a biblioteca de gestão de lotes .NET.

Para saber mais sobre como utilizar a biblioteca de gestão de lotes .NET e o exemplo de AccountManagement, veja [contas do Batch de gerir e quotas com a biblioteca de cliente de gestão do Batch para .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registar a sua aplicação com o Azure AD

O Azure [Active Directory Authentication Library] [ aad_adal] (ADAL) fornece uma interface programática para o Azure AD para utilização nas suas aplicações. Para chamar ADAL a partir da sua aplicação, tem de registar a sua aplicação no inquilino do Azure AD. Quando registar a sua aplicação, forneça o Azure AD com informações sobre a sua aplicação, incluindo um nome para o mesmo no inquilino do Azure AD. Em seguida, do Azure AD fornece um ID de aplicação que utiliza para associar a sua aplicação com o Azure AD em tempo de execução. Para saber mais sobre o ID da aplicação, veja [aplicativos e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Para registar a aplicação de exemplo de AccountManagement, siga os passos a [adicionar uma aplicação](../active-directory/develop/quickstart-v1-add-azure-ad-app.md) secção [integrar aplicações com o Azure Active Directory] [ aad_integrate]. Especifique **aplicação cliente nativa** para o tipo de aplicação. O setor de URI padrão do 2.0 de OAuth para o **URI de redirecionamento** é `urn:ietf:wg:oauth:2.0:oob`. No entanto, pode especificar qualquer URI válida (por exemplo, `http://myaccountmanagementsample`) para o **URI de redirecionamento**, como ele não tem de ser um ponto final real:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Depois de concluir o processo de registro, verá o ID da aplicação e o ID de objeto (principal de serviço) listados para a sua aplicação.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceder o acesso de API do Azure Resource Manager para a sua aplicação

Em seguida, precisará delegar o acesso à sua aplicação para a API do Azure Resource Manager. É o identificador do Azure AD para a API do Resource Manager **API de gestão de serviço do Windows Azure**.

Siga estes passos no portal do Azure:

1. No painel de navegação do lado esquerdo do portal do Azure, escolha **todos os serviços**, clique em **registos das aplicações**e clique em **Add**.
2. Procure o nome da sua aplicação na lista de registos de aplicações:

    ![Procurar nome da sua aplicação](./media/batch-aad-auth-management/search-app-registration.png)

3. Apresentar o **definições** painel. Na **acesso à API** secção, selecione **permissões obrigatórias**.
4. Clique em **adicionar** para adicionar uma nova permissão necessária. 
5. Passo 1, introduza **API de gestão de serviço do Windows Azure**, selecione essa API a partir da lista de resultados e clique nas **selecione** botão.
6. Passo 2, selecione a caixa de verificação junto a **modelo de implementação clássica do Azure de acesso como utilizadores da organização**e clique nas **selecione** botão.
7. Clique nas **feito** botão.

O **permissões obrigatórias** painel mostra agora o que são concedidas permissões para a sua aplicação para a ADAL e APIs do Resource Manager. As permissões são concedidas a ADAL por padrão quando primeiro registar a aplicação com o Azure AD.

![Delegar permissões para a API do Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Pontos finais de AD do Azure

Para autenticar as suas soluções de gestão do Batch com o Azure AD, terá dois pontos de extremidade bem conhecidos.

- O **ponto final do Azure AD comuns** fornece uma credencial genérica recolha interface quando não for fornecido um inquilino específico, como no caso da autenticação integrada:

    `https://login.microsoftonline.com/common`

- O **ponto final do Azure Resource Manager** é utilizado para adquirir um token para autenticar pedidos para o serviço de gestão do Batch:

    `https://management.core.windows.net/`

O aplicativo de exemplo de AccountManagement define constantes para estes pontos finais. Deixe essas constantes inalterados:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>ID da sua aplicação de referência 

A aplicação cliente utiliza o ID da aplicação (também referido como o ID de cliente) para aceder ao Azure AD em tempo de execução. Assim que tiver registado a sua aplicação no portal do Azure, atualize seu código para utilizar o ID de aplicação fornecido pelo Azure AD para a aplicação registada. No aplicativo de exemplo de AccountManagement, copie o seu ID de aplicação do portal do Azure para a constante apropriada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Também copie o URI de redirecionamento que especificou durante o processo de registo. O URI especificado no seu código de redirecionamento deve coincidir com o URI de redirecionamento que que forneceu quando se registou a aplicação.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Depois de registar o exemplo de AccountManagement no inquilino do Azure AD e atualizar o código de origem de exemplo com os seus valores, o exemplo está pronto para se autenticar com o Azure AD. Quando executar o exemplo, a ADAL tenta adquirir um token de autenticação. Nesta etapa, ele pede-lhe as credenciais da Microsoft: 

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

Depois de fornecer as suas credenciais, o aplicativo de exemplo pode avançar para emitir pedidos autenticados para o serviço de gestão do Batch. 

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre a execução de [aplicação de exemplo de AccountManagement][acct_mgmt_sample], consulte [contas do Batch de gerir e quotas com a biblioteca de cliente de gestão do Batch para .NET](batch-management-dotnet.md) .

Para saber mais sobre o Azure AD, veja a [do Azure Active Directory Documentation](https://docs.microsoft.com/azure/active-directory/). Exemplos detalhados que mostram como utilizar a ADAL estão disponíveis no [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) biblioteca.

Para autenticar as aplicações de serviço do Batch com o Azure AD, consulte [soluções de serviço do Batch de autenticar com o Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrar aplicações com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
