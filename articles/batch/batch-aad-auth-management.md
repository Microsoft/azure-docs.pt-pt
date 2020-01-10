---
title: Usar Azure Active Directory para autenticar soluções de gerenciamento do lote | Microsoft Docs
description: Aplicativos criados com Azure Resource Manager e o provedor de recursos do lote são autenticados com o Azure AD.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 2fd5867d910d29533eb17a4660039cc7b9610290
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449844"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Autenticar soluções de gerenciamento do lote com o Active Directory

Os aplicativos que chamam o serviço de gerenciamento do lote do Azure se autenticam com o [Azure Active Directory][aad_about] (Azure AD). O Azure AD é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. O próprio Azure usa o Azure AD para a autenticação de seus clientes, administradores de serviço e usuários organizacionais.

A biblioteca .NET de gerenciamento do lote expõe tipos para trabalhar com contas do lote, chaves de conta, aplicativos e pacotes de aplicativos. A biblioteca .NET de gerenciamento do lote é um cliente do provedor de recursos do Azure e é usada junto com [Azure Resource Manager][resman_overview] para gerenciar esses recursos programaticamente. O Azure AD é necessário para autenticar solicitações feitas por meio de qualquer cliente do provedor de recursos do Azure, incluindo a biblioteca .NET de gerenciamento do lote e por meio de [Azure Resource Manager][resman_overview].

Neste artigo, exploraremos usando o Azure AD para autenticar de aplicativos que usam a biblioteca .NET de gerenciamento do lote. Mostramos como usar o Azure AD para autenticar um administrador de assinatura ou coadministrador, usando a autenticação integrada. Usamos o projeto de exemplo [AccountManagement][acct_mgmt_sample] , disponível no GitHub, para percorrer o uso do Azure AD com a biblioteca .net de gerenciamento do lote.

Para saber mais sobre como usar a biblioteca .NET de gerenciamento do lote e o exemplo AccountManagement, consulte [gerenciar contas e cotas do lote com a biblioteca do cliente de gerenciamento do lote para .net](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrar seu aplicativo com o Azure AD

O Adal ( [biblioteca de autenticação do Active Directory][aad_adal] do Azure) fornece uma interface programática para o Azure ad para uso em seus aplicativos. Para chamar a ADAL do seu aplicativo, você deve registrar seu aplicativo em um locatário do Azure AD. Ao registrar seu aplicativo, você fornece ao Azure AD informações sobre seu aplicativo, incluindo um nome para ele dentro do locatário do Azure AD. Em seguida, o Azure AD fornece uma ID de aplicativo que você usa para associar seu aplicativo ao Azure AD em tempo de execução. Para saber mais sobre a ID do aplicativo, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Para registrar o aplicativo de exemplo AccountManagement, siga as etapas na seção [adicionando um aplicativo](../active-directory/develop/quickstart-register-app.md) em [integrando aplicativos com o Azure Active Directory][aad_integrate]. Especifique o **aplicativo cliente nativo** para o tipo de aplicativo. O URI do OAuth 2,0 padrão do setor para o **URI de redirecionamento** é `urn:ietf:wg:oauth:2.0:oob`. No entanto, você pode especificar qualquer URI válido (como `http://myaccountmanagementsample`) para o **URI de redirecionamento**, pois ele não precisa ser um ponto de extremidade real:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Depois de concluir o processo de registro, você verá a ID do aplicativo e a ID do objeto (entidade de serviço) listada para seu aplicativo.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Conceder ao Azure Resource Manager acesso à API para seu aplicativo

Em seguida, você precisará delegar o acesso ao seu aplicativo para a API Azure Resource Manager. O identificador do Azure AD para a API do Gerenciador de recursos é o **Windows Azure API de gerenciamento de serviços**.

Siga os passos abaixo no portal do Azure:

1. No painel de navegação à esquerda da portal do Azure, escolha **todos os serviços**, clique em **registros do aplicativo**e clique em **Adicionar**.
2. Procure o nome do seu aplicativo na lista de registros do aplicativo:

    ![Pesquisar o nome do aplicativo](./media/batch-aad-auth-management/search-app-registration.png)

3. Exiba a folha **configurações** . Na **acesso à API** secção, selecione **permissões obrigatórias**.
4. Clique em **Adicionar** para adicionar uma nova permissão necessária. 
5. Na etapa 1, insira **API de gerenciamento de serviços do Windows Azure**, selecione essa API na lista de resultados e clique no botão **selecionar** .
6. Na etapa 2, marque a caixa de seleção ao lado de **acessar o modelo de implantação clássico do Azure como usuários da organização**e clique no botão **selecionar** .
7. Clique no botão **concluído** .

A folha **permissões necessárias** agora mostra que as permissões para seu aplicativo são concedidas às APIs Adal e do Gerenciador de recursos. As permissões são concedidas à ADAL por padrão quando você registra seu aplicativo pela primeira vez com o Azure AD.

![Delegar permissões para a API de Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Pontos de extremidade do Azure AD

Para autenticar suas soluções de gerenciamento do lote com o Azure AD, você precisará de dois pontos de extremidade conhecidos.

- O **ponto de extremidade comum do Azure ad** fornece uma interface de coleta de credenciais genérica quando um locatário específico não é fornecido, como no caso de autenticação integrada:

    `https://login.microsoftonline.com/common`

- O **ponto de extremidade Azure Resource Manager** é usado para adquirir um token para autenticar solicitações para o serviço de gerenciamento do lote:

    `https://management.core.windows.net/`

O aplicativo de exemplo AccountManagement define constantes para esses pontos de extremidade. Deixe essas constantes inalteradas:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Referenciar a ID do aplicativo 

Seu aplicativo cliente usa a ID do aplicativo (também conhecida como a ID do cliente) para acessar o Azure AD em tempo de execução. Depois de registrar seu aplicativo no portal do Azure, atualize seu código para usar a ID do aplicativo fornecida pelo Azure AD para seu aplicativo registrado. No aplicativo de exemplo AccountManagement, copie a ID do aplicativo do portal do Azure para a constante apropriada:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Copie também o URI de redirecionamento que você especificou durante o processo de registro. O URI de redirecionamento especificado no seu código deve corresponder ao URI de redirecionamento que você forneceu quando registrou o aplicativo.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Depois de registrar o exemplo AccountManagement no locatário do Azure AD e atualizar o código-fonte de exemplo com seus valores, o exemplo estará pronto para autenticar usando o Azure AD. Quando você executa o exemplo, a ADAL tenta adquirir um token de autenticação. Nesta etapa, ele solicitará suas credenciais da Microsoft: 

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

Depois de fornecer suas credenciais, o aplicativo de exemplo pode continuar a emitir solicitações autenticadas para o serviço de gerenciamento do lote. 

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como executar o [aplicativo de exemplo AccountManagement][acct_mgmt_sample], consulte [gerenciar contas e cotas do lote com a biblioteca de cliente de gerenciamento do lote para .net](batch-management-dotnet.md).

Para saber mais sobre o Azure AD, consulte a [documentação do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Exemplos detalhados que mostram como usar a ADAL estão disponíveis na biblioteca de [exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .

Para autenticar aplicativos de serviço de lote usando o Azure AD, consulte [autenticar soluções de serviço de lote com Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrando aplicativos com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/management/overview.md
