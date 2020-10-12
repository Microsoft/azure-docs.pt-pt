---
title: Configurar o Azure Ative Directy para a autenticação do cliente
description: Saiba como configurar o Azure Ative Directory (Azure AD) para autenticar clientes para clusters de Tecidos de Serviço.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 537a81a090828d3fcc9dde6032f1d4eb2df9b4e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258779"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Ative Directy para a autenticação do cliente

Para clusters em execução em Azure, o Azure Ative Directory (Azure AD) é recomendado para garantir o acesso aos pontos finais de gestão. Este artigo descreve como configurar a Azure AD para autenticar clientes para um cluster de Tecido de Serviço.

Neste artigo, o termo "aplicação" será utilizado para se referir às [aplicações do Azure Ative Directory](../active-directory/develop/developer-glossary.md#client-application), e não às aplicações de Service Fabric; a distinção será feita sempre que necessário. A Azure AD permite que as organizações (conhecidas como inquilinos) gerem o acesso do utilizador às aplicações.

Um cluster de tecido de serviço oferece vários pontos de entrada para a sua funcionalidade de gestão, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e o Visual [Studio.][service-fabric-manage-application-in-visual-studio] Como resultado, irá criar duas aplicações AD Azure para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa. Após a criação das aplicações, irá atribuir aos utilizadores funções apenas de leitura e administração.

> [!NOTE]
> No Linux, tem de completar os seguintes passos antes de criar o cluster. No Windows, também tem a opção de configurar a [autenticação AZure AD para um cluster existente.](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md)

> [!NOTE]
> É um [problema conhecido](https://github.com/microsoft/service-fabric/issues/399) que aplicações e nós em clusters habilitados para a AAD Linux não podem ser vistos no Portal Azure.



## <a name="prerequisites"></a>Pré-requisitos
Neste artigo, assumimos que já criou um inquilino. Se não tiver, comece por ler [Como obter um inquilino do Azure Ative Directory.][active-directory-howto-tenant]

Para simplificar alguns dos passos envolvidos na configuração do AZure AD com um cluster de Tecido de Serviço, criámos um conjunto de scripts Windows PowerShell.

1. [Clone o repo](https://github.com/Azure-Samples/service-fabric-aad-helpers) para o seu computador.
2. [Certifique-se de que tem todos os pré-requisitos](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) para os scripts instalados.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicações AD AZure e atribuir utilizadores a funções

Usaremos os scripts para criar duas aplicações AD Azure para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa. Depois de criar aplicações para representar o seu cluster, irá criar utilizadores para as [funções suportadas pelo Service Fabric](service-fabric-cluster-security-roles.md): apenas para leitura e administração.

Executar `SetupApplications.ps1` , e fornecer o ID do inquilino, nome de cluster e URL de resposta à aplicação web como parâmetros.  Especificar também nomes de utilizador e palavras-passe para os utilizadores. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para as nuvens nacionais (por exemplo, Governo Azure, Azure China, Azure Alemanha), também deve especificar o `-Location` parâmetro.

Pode encontrar o seu *TenantId* executando o comando PowerShell `Get-AzureSubscription` . A execução deste comando exibe o TenantId para cada subscrição.

*ClusterName* é usado para pré-fixar as aplicações AD Azure que são criadas pelo script. Não precisa de corresponder exatamente ao nome do cluster. Destina-se apenas a facilitar o mapear artefactos AD AZure para o cluster de Tecido de Serviço com o qual estão a ser usados.

*WebApplicationReplyUrl* é o ponto final padrão que o Azure AD retorna aos seus utilizadores depois de terminarem de iniciar sessão. Desaponte este ponto final como ponto final do Explorador de Tecidos de Serviço para o seu cluster. Se estiver a criar aplicações AD Azure para representar um cluster existente, certifique-se de que este URL corresponde ao ponto final do cluster existente. Se estiver a criar aplicações para um novo cluster, planeie o ponto final que o seu cluster terá e certifique-se de não utilizar o ponto final de um cluster existente. Por predefinição, o ponto final do Explorador de Tecidos de Serviço é:

https:// cluster_domain &lt; &gt; :19080/Explorer

É solicitado que assine uma conta que tenha privilégios administrativos para o inquilino da AD Azure. Depois de iniciar sposição, o script cria aplicações web e nativas para representar o seu cluster de Tecido de Serviço. Se olhar para as candidaturas do inquilino no [portal Azure,][azure-portal]deverá ver duas novas entradas:

   * *ClusterName* \_ Cluster
   * *ClusterName* \_ Cliente

O script imprime o JSON exigido pelo modelo Azure Resource Manager quando [cria o seu cluster ativado por AAD](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), por isso é uma boa ideia manter a janela PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>A resolução de problemas ajuda a configurar o Azure Active Directory
Configurar a Azure AD e usá-lo pode ser um desafio, por isso aqui estão algumas dicas sobre o que pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>O Explorador de Tecidos de Serviço pede-lhe que selecione um certificado
#### <a name="problem"></a>Problema
Depois de assinar com sucesso no Azure AD no Service Fabric Explorer, o navegador regressa à página inicial, mas uma mensagem pede-lhe que selecione um certificado.

![Diálogo de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Razão
O utilizador não é atribuído a um papel na aplicação de cluster AD Azure. Assim, a autenticação AZure AD falha no cluster de Tecido de Serviço. O Explorador de Tecidos de Serviço volta a ser autenticador de certificados.

#### <a name="solution"></a>Solução
Siga as instruções para configurar o Azure AD e atribua as funções do utilizador. Além disso, recomendamos que ligue "Atribuição de utilizadores necessária para aceder à app", assim como `SetupApplications.ps1` faz.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A ligação com o PowerShell falha com um erro: "As credenciais especificadas são inválidas"
#### <a name="problem"></a>Problema
Quando utiliza o PowerShell para ligar ao cluster utilizando o modo de segurança "AzureActiveDirectory", depois de ter assinado com sucesso no Azure AD, a ligação falha com um erro: "As credenciais especificadas são inválidas".

#### <a name="solution"></a>Solução
Esta solução é a mesma que a anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer devolve uma falha ao iniciar sposição: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando tenta entrar no Azure AD no Service Fabric Explorer, a página devolve uma falha: "AADSTS50011: O url do endereço de resposta &lt; &gt; não corresponde aos endereços de resposta configurados para a aplicação: &lt; guia &gt; ."

![O endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Razão
A aplicação cluster (web) que representa o Service Fabric Explorer tenta autenticar contra a Azure AD, e como parte do pedido fornece o URL de retorno de redirecionamento. Mas o URL não está listado na lista **de URL de resposta de** pedido azure.

#### <a name="solution"></a>Solução
Na página de registo da aplicação AZure AD para o seu cluster, selecione **Autenticação**, e na secção **URIs de redirecionamento,** adicione o URL do Explorador de Tecido de Serviço à lista. Poupe o troco.

![URL de resposta a aplicações web][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>A ligação ao cluster utilizando a autenticação AD AD azure via PowerShell dá um erro ao iniciar sação: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando tenta ligar-se a um cluster de Tecido de Serviço utilizando a Azure AD via PowerShell, a página de inscrição retorna uma falha: "AADSTS50011: O url de resposta especificado no pedido não corresponde aos urls de resposta configurados para a aplicação: &lt; guia &gt; ."

#### <a name="reason"></a>Razão
À semelhança do problema anterior, a PowerShell tenta autenticar contra a Azure AD, que fornece um URL de redirecionamento que não está listado na lista **de URLs de resposta a pedidos** Azure.  

#### <a name="solution"></a>Solução
Utilize o mesmo processo que na edição anterior, mas o URL deve ser configurado para `urn:ietf:wg:oauth:2.0:oob` , um redirecionamento especial para a autenticação da linha de comando.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ligue o cluster utilizando a autenticação AD Azure via PowerShell
Para ligar o cluster de tecido de serviço, utilize o seguinte exemplo de comando PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais, consulte [o cmdlet Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo inquilino AD AD em vários aglomerados?
Sim. Mas lembre-se de adicionar o URL do Service Fabric Explorer à sua aplicação cluster (web). Caso contrário, o Service Fabric Explorer não funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Porque é que ainda preciso de um certificado de servidor enquanto o Azure AD está ativado?
FabricClient e FabricGateway realizam uma autenticação mútua. Durante a autenticação Azure AD, a integração AZure AD fornece uma identidade do cliente ao servidor, e o certificado de servidor é utilizado pelo cliente para verificar a identidade do servidor. Para obter mais informações sobre os certificados de Tecido de Serviço, consulte [os certificados X.509 e o Tecido de Serviço.][x509-certificates-and-service-fabric]

## <a name="next-steps"></a>Passos seguintes
Depois de configurar aplicações do Azure Ative Directory e definir funções para os [utilizadores, configurar e implementar um cluster](service-fabric-cluster-creation-via-arm.md).


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
