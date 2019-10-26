---
title: Configurar Azure Active Directory para autenticação de cliente Service Fabric | Microsoft Docs
description: Saiba como configurar o Azure Active Directory (Azure AD) para autenticar clientes para Service Fabric clusters.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2019
ms.author: atsenthi
ms.openlocfilehash: 77814d04daca0ebb649ffa2e8ff46becddec4f0f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901507"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar Azure Active Directory para autenticação de cliente

Para clusters em execução no Azure, o Azure Active Directory (Azure AD) é recomendado para proteger o acesso aos pontos de extremidade de gerenciamento. Este artigo descreve como configurar o Azure AD para autenticar clientes para um Cluster Service Fabric.

Neste artigo, o termo "aplicativo" será usado para fazer referência a [aplicativos Azure Active Directory](../active-directory/develop/developer-glossary.md#client-application), não Service Fabric aplicativos; a distinção será feita quando necessário. O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso do usuário aos aplicativos.

Um Cluster Service Fabric oferece vários pontos de entrada para sua funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] baseado na Web e o [Visual Studio][service-fabric-manage-application-in-visual-studio]. Como resultado, você criará dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo. Depois que os aplicativos forem criados, você atribuirá usuários a funções de administrador e somente leitura.

> [!NOTE]
> No Linux, você deve concluir as etapas a seguir antes de criar o cluster. No Windows, você também tem a opção de [Configurar a autenticação do Azure ad para um cluster existente](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

## <a name="prerequisites"></a>Pré-requisitos
Neste artigo, presumimos que você já tenha criado um locatário. Se você não tiver, comece lendo [como obter um locatário Azure Active Directory][active-directory-howto-tenant].

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um Cluster Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

1. [Clone o repositório](https://github.com/Azure-Samples/service-fabric-aad-helpers) em seu computador.
2. [Verifique se você tem todos os pré-requisitos](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) para os scripts instalados.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicativos do Azure AD e atribuir usuários a funções

Usaremos os scripts para criar dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo. Depois de criar aplicativos para representar o cluster, você criará usuários para as [funções com suporte pelo Service Fabric](service-fabric-cluster-security-roles.md): somente leitura e administrador.

Execute `SetupApplications.ps1`e forneça a ID do locatário, o nome do cluster e a URL de resposta do aplicativo Web como parâmetros.  Especifique também nomes de usuário e senhas para os usuários. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo, Azure governamental, Azure China, Azure Alemanha), você também deve especificar o parâmetro `-Location`.

Você pode encontrar sua *tenantid* executando o comando do PowerShell `Get-AzureSubscription`. A execução desse comando exibe a Tenantid para cada assinatura.

*ClusterName* é usado para prefixar os aplicativos do Azure ad criados pelo script. Ele não precisa corresponder exatamente ao nome do cluster real. Destina-se apenas a facilitar o mapeamento de artefatos do Azure AD para o Cluster Service Fabric ao qual estão sendo usados.

*WebApplicationReplyUrl* é o ponto de extremidade padrão que o Azure ad retorna para os usuários depois que terminam de entrar. Defina esse ponto de extremidade como o ponto de extremidade Service Fabric Explorer para o cluster. Se você estiver criando aplicativos do Azure AD para representar um cluster existente, verifique se essa URL corresponde ao ponto de extremidade do cluster existente. Se você estiver criando aplicativos para um novo cluster, planeje o ponto de extremidade que o seu cluster terá e certifique-se de não usar o ponto de extremidade de um cluster existente. Por padrão, o ponto de extremidade Service Fabric Explorer é:

https://&lt;cluster_domain&gt;: 19080/Explorer

Você será solicitado a entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script cria os aplicativos Web e nativos para representar o Cluster Service Fabric. Se você examinar os aplicativos do locatário na [portal do Azure][azure-portal], verá duas novas entradas:

   * *Clustername\_cluster*
   * *Clustername*\_cliente

O script imprime o JSON exigido pelo modelo de Azure Resource Manager quando você [cria o cluster habilitado do AAD](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), portanto, é uma boa ideia manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Ajuda para solução de problemas na configuração de Azure Active Directory
Configurar o Azure AD e usá-lo pode ser desafiador, portanto, aqui estão alguns ponteiros sobre o que você pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer solicita que você selecione um certificado
#### <a name="problem"></a>Problema
Depois de entrar com êxito no Azure AD no Service Fabric Explorer, o navegador retornará ao home page, mas uma mensagem solicitará que você selecione um certificado.

![Caixa de diálogo de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Razão
O usuário não recebe uma função no aplicativo de cluster do Azure AD. Portanto, a autenticação do Azure AD falha no Cluster Service Fabric. Service Fabric Explorer retorne à autenticação de certificado.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribuir funções de usuário. Além disso, recomendamos que você ative "atribuição de usuário necessária para acessar o aplicativo", como `SetupApplications.ps1`.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Falha na conexão com o PowerShell com um erro: "as credenciais especificadas são inválidas"
#### <a name="problem"></a>Problema
Quando você usa o PowerShell para se conectar ao cluster usando o modo de segurança "AzureActiveDirectory", depois de entrar com êxito no Azure AD, a conexão falhará com um erro: "as credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Essa solução é a mesma que a anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer retorna uma falha quando você entra: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando você tenta entrar no Azure AD em Service Fabric Explorer, a página retorna uma falha: "AADSTS50011: o endereço de resposta &lt;URL&gt; não corresponde aos endereços de resposta configurados para o aplicativo: &lt;GUID&gt;".

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Razão
O aplicativo de cluster (Web) que representa Service Fabric Explorer tenta se autenticar no Azure AD e, como parte da solicitação, ele fornece a URL de retorno de redirecionamento. Mas a URL não está listada na lista de **URL de resposta** do aplicativo do Azure AD.

#### <a name="solution"></a>Solução
Selecione "Registros de aplicativo" na página do AAD, selecione o aplicativo de cluster e, em seguida, selecione o botão **URLs de resposta** . Na página "URLs de resposta", adicione a URL de Service Fabric Explorer à lista ou substitua um dos itens na lista. Quando tiver terminado, salve sua alteração.

![URL de resposta do aplicativo Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Conectar o cluster usando a autenticação do Azure AD por meio do PowerShell
Para conectar o Cluster Service Fabric, use o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais, confira o [cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo locatário do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar a URL de Service Fabric Explorer ao seu aplicativo de cluster (Web). Caso contrário, Service Fabric Explorer não funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso de um certificado de servidor enquanto o Azure AD está habilitado?
FabricClient e FabricGateway executam uma autenticação mútua. Durante a autenticação do Azure AD, a integração do AD do Azure fornece uma identidade de cliente para o servidor e o certificado do servidor é usado pelo cliente para verificar a identidade do servidor. Para obter mais informações sobre certificados Service Fabric, consulte [certificados X. 509 e Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Passos seguintes
Depois de configurar Azure Active Directory aplicativos e definir funções para usuários, [Configure e implante um cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
