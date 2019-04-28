---
title: Configurar o Azure Active Directory para autenticação de cliente do Service Fabric | Documentos da Microsoft
description: Saiba como configurar o Azure Active Directory (Azure AD) para autenticar clientes para clusters do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2019
ms.author: aljo
ms.openlocfilehash: c02e38880fdf8e8f1a2229f009b343d6431af853
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125141"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Azure Active Directory para autenticação de cliente

Para clusters em execução no Azure, Azure Active Directory (Azure AD) é recomendado proteger o acesso aos pontos finais de gestão.  Este artigo descreve como a configuração do Azure AD para autenticar clientes para um cluster do Service Fabric, que deve ser feito antes [criar o cluster](service-fabric-cluster-creation-via-arm.md).  O Azure AD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações. Aplicativos estão divididos em aquelas com uma conta baseada na web, início de sessão de interface de Usuário e os que têm uma experiência de cliente nativo o limite de tempo. 

Um cluster do Service Fabric oferece vários pontos de entrada para sua funcionalidade de gestão, incluindo baseada na web [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] e [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Como resultado, vai criar duas aplicações do Azure AD para controlar o acesso ao cluster: aplicação web de um e um aplicativo nativo.  Depois dos aplicativos são criados, atribuir utilizadores para só de leitura e funções de administrador.

> [!NOTE]
> Tem de concluir os seguintes passos antes de criar o cluster. Porque os scripts esperam que os nomes de cluster e pontos de extremidade, os valores devem ser planeadas e não os valores que já tenha criado.

## <a name="prerequisites"></a>Pré-requisitos
Neste artigo, partimos do princípio que já criou um inquilino. Se não o tiver, comece por ler [como obter um inquilino do Azure Active Directory][active-directory-howto-tenant].

Para simplificar algumas das etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell.

1. [Transferir os scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) para o seu computador.
2. O ficheiro zip com o botão direito, selecione **propriedades**, selecione a **desbloqueio** caixa de verificação e, em seguida, clique em **aplicar**.
3. Extraia o ficheiro zip.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicações do Azure AD e atribuir utilizadores a funções
Criar duas aplicações do Azure AD para controlar o acesso ao cluster: aplicação web de um e um aplicativo nativo. Depois de criar os aplicativos para representar o cluster, atribuir os seus utilizadores para o [funções com suporte para o Service Fabric](service-fabric-cluster-security-roles.md): só de leitura e administrador.

Executar `SetupApplications.ps1`e forneça o inquilino, ID, o nome do cluster e o URL de resposta do aplicativo web, como parâmetros.  Também pode especifica nomes de utilizador e palavras-passe para os utilizadores.  Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo o Azure Government, Azure China, Azure Alemanha), deve também especificar o `-Location` parâmetro.

Pode encontrar suas *TenantId* executando o comando do PowerShell `Get-AzureSubscription`. Executar este comando apresenta o TenantId para cada subscrição.

*ClusterName* é utilizado para o prefixo de aplicações do Azure AD que são criadas pelo script. Não é necessário o nome de cluster real de corresponder exatamente. Destina-se apenas a tornar mais fácil mapear os artefactos do Azure AD para o cluster do Service Fabric que está a ser utilizados com.

*WebApplicationReplyUrl* é o ponto final predefinido, que retorna do Azure AD para os seus utilizadores, depois de concluir a iniciar sessão. Defina este ponto final como o ponto de final do Service Fabric Explorer para o seu cluster, o que, por predefinição, é:

https://&lt;cluster_domain&gt;:19080/Explorer

São-lhe pedido para iniciar sessão a uma conta que tenha privilégios administrativos para o inquilino do Azure AD. Depois de iniciar sessão, o script cria o web e aplicativos nativos para representar o cluster do Service Fabric. Se olhar de aplicações do inquilino no [portal do Azure][azure-portal], deverá ver duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

O script imprime o JSON necessário para o modelo Azure Resource Manager quando [criar o cluster](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), assim, é uma boa idéia mantenha a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Resolução de problemas de ajuda para configurar o Azure Active Directory
Configurar o AD do Azure e utilizá-lo podem ser um desafio, portanto, aqui estão algumas dicas sobre o que pode fazer para depurar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer solicita que selecione um certificado
#### <a name="problem"></a>Problema
Depois de iniciar sessão com êxito para o Azure AD no Service Fabric Explorer, o navegador retorna para a home page, mas uma mensagem solicita que selecione um certificado.

![Caixa de diálogo de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Razão
O utilizador não foi atribuído uma função na aplicação de cluster do Azure AD. Portanto, a autenticação do Azure AD falha no cluster do Service Fabric. Service Fabric Explorer retrocede para autenticação de certificados.

#### <a name="solution"></a>Solução
Siga as instruções para a configuração do Azure AD e atribuir funções de utilizador. Além disso, recomendamos que ative "Atribuição de utilizador necessária para aceder à aplicação," como `SetupApplications.ps1` faz.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Ligação com o PowerShell pode falhar com um erro: "As credenciais especificadas são inválidas"
#### <a name="problem"></a>Problema
Quando utilizar o PowerShell para ligar ao cluster com o modo de segurança de "AzureActiveDirectory", depois de iniciar sessão com êxito para o Azure AD, a ligação falhar com um erro: "As credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Esta solução é o mesmo que a anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer devolve uma falha ao iniciar sessão: "AADSTS50011"
#### <a name="problem"></a>Problema
Ao tentar iniciar sessão Azure AD no Service Fabric Explorer, a página devolve uma falha: "AADSTS50011: O endereço de resposta &lt;url&gt; não coincide com o endereço de resposta configurado para a aplicação: &lt;guid&gt;. "

![Não corresponde ao endereço de resposta SFX][sfx-reply-address-not-match]

#### <a name="reason"></a>Razão
A aplicação de cluster (web) que representa o Service Fabric Explorer tenta autenticar no Azure AD e como parte do pedido fornece o URL de retorno de redirecionamento. Mas o URL não está listado na aplicação do Azure AD **URL de resposta** lista.

#### <a name="solution"></a>Solução
Selecione "Registos de aplicações" na página do AAD, selecione a aplicação de cluster e, em seguida, selecione o **URLs de resposta** botão. Na página de "URLs de resposta", adicionar o URL do Service Fabric Explorer para a lista ou substituir um dos itens na lista. Quando tiver terminado, guarde as alterações.

![Url de resposta do aplicativo Web][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ligue o cluster utilizando a autenticação do Azure AD através do PowerShell
Para ligar o cluster do Service Fabric, utilize o seguinte exemplo de comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para obter mais informações, consulte [cmdlet Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo inquilino do Azure AD em vários clusters?
Sim. Mas lembre-se de adicionar o URL do Service Fabric Explorer à sua aplicação de cluster (web). Caso contrário, o Service Fabric Explorer não funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Por que eu ainda preciso um certificado de servidor enquanto o Azure AD está ativo?
FabricClient e FabricGateway executará uma autenticação mútua. Durante a autenticação do Azure AD, integração do Azure AD fornece uma identidade do cliente para o servidor e o certificado de servidor é utilizado para verificar a identidade do servidor. Para obter mais informações sobre certificados do Service Fabric, veja [certificados X.509 e o Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Passos Seguintes
Após a configuração de aplicações do Azure Active Directory e funções de definição para os utilizadores, [configurar e implementar um cluster](service-fabric-cluster-creation-via-arm.md).


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
