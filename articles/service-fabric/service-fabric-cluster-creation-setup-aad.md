---
title: Configurar o Diretório Ativo Azure para autenticação do cliente
description: Saiba como criar o Azure Ative Directory (Azure AD) para autenticar clientes para clusters de Tecido de Serviço.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193390"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurar o Diretório Ativo Azure para autenticação do cliente

Para clusters em execução em Azure, o Azure Ative Directory (Azure AD) é recomendado para garantir o acesso aos pontos finais de gestão. Este artigo descreve como configurar a Azure AD para autenticar clientes para um cluster de Tecido de Serviço.

Neste artigo, o termo "aplicação" será utilizado para se referir às [aplicações do Diretório Ativo do Azure,](../active-directory/develop/developer-glossary.md#client-application)e não às aplicações do Service Fabric; a distinção será feita sempre que necessário. A Azure AD permite que as organizações (conhecidas como inquilinos) gerem o acesso dos utilizadores às aplicações.

Um cluster de tecido de serviço oferece vários pontos de entrada para a sua funcionalidade de gestão, incluindo o [Service Fabric Explorer][service-fabric-visualizing-your-cluster] e o Visual [Studio.][service-fabric-manage-application-in-visual-studio] Como resultado, irá criar duas aplicações Azure AD para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa. Após a criação das aplicações, irá atribuir aos utilizadores funções de leitura e administração.

> [!NOTE]
> Em Linux, deve completar os seguintes passos antes de criar o cluster. No Windows, também tem a opção de configurar a [autenticação de Anúncio Saque para um cluster existente](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> É uma [questão conhecida](https://github.com/microsoft/service-fabric/issues/399) que as aplicações e nós em clusters ativados por AAD linux não podem ser vistos no Portal Azure.



## <a name="prerequisites"></a>Pré-requisitos
Neste artigo, assumimos que já criou um inquilino. Se não o fizer, comece por ler [Como obter um inquilino azure Ative Directory.][active-directory-howto-tenant]

Para simplificar alguns dos passos envolvidos na configuração do Azure AD com um cluster de Tecido de Serviço, criámos um conjunto de scripts Windows PowerShell.

1. [Clone o repo](https://github.com/Azure-Samples/service-fabric-aad-helpers) para o seu computador.
2. [Certifique-se de que tem todos os pré-requisitos](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) para os scripts instalados.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicações ad iae e atribuir utilizadores a funções

Usaremos os scripts para criar duas aplicações Azure AD para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa. Depois de criar aplicações para representar o seu cluster, irá criar utilizadores para as [funções suportadas pelo Service Fabric](service-fabric-cluster-security-roles.md): apenas leitura e administrador.

Executar, `SetupApplications.ps1`e fornecer o ID do inquilino, nome do cluster e resposta de aplicação web URL como parâmetros.  Especifique também os nomes de utilizador e as palavras-passe para os utilizadores. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para as nuvens nacionais (por exemplo, Governo Azure, Azure `-Location` China, Azure Alemanha), deve também especificar o parâmetro.

Pode encontrar o seu *TenantId* executando o comando `Get-AzureSubscription`PowerShell . A execução deste comando exibe o TenantId para cada subscrição.

*ClusterName* é usado para pré-fixar as aplicações Azure AD que são criadas pelo script. Não precisa de corresponder exatamente ao nome real do cluster. Destina-se apenas a facilitar o mapa dos artefactos Azure AD para o cluster De Tecido de Serviço com o qual estão a ser utilizados.

*WebApplicationReplyUrl* é o ponto final padrão que o Azure AD devolve aos seus utilizadores depois de terminar em sessão. Detete este ponto final como ponto final do Explorador de Tecidode Serviço para o seu cluster. Se estiver a criar aplicações Azure AD para representar um cluster existente, certifique-se de que este URL corresponde ao ponto final do cluster existente. Se estiver a criar aplicações para um novo cluster, planeie o ponto final que o seu cluster terá e certifique-se de não utilizar o ponto final de um cluster existente. Por defeito, o ponto final do Explorador de Tecidos de Serviço é:

https://&lt;&gt;cluster_domain :19080/Explorer

É solicitado que assine uma conta que tenha privilégios administrativos para o inquilino da AD Azure. Depois de iniciar sessão, o script cria as aplicações web e nativas para representar o seu cluster De Serviço Tecido. Se olhar para as candidaturas do inquilino no [portal Azure,][azure-portal]deverá ver duas novas entradas:

   * *ClusterName*\_Cluster
   * *Cliente ClusterName*\_

O script imprime o JSON exigido pelo modelo de Gestor de Recursos Azure quando cria o seu cluster ativado por [AAD,](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access)por isso é uma boa ideia manter a janela PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>A resolução de problemas ajuda a configurar o Azure Active Directory
Configurar o Azure AD e usá-lo pode ser um desafio, por isso aqui estão algumas dicas sobre o que pode fazer para desinviar o problema.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer pede-lhe para selecionar um certificado
#### <a name="problem"></a>Problema
Depois de iniciar sessão com sucesso no Azure AD no Service Fabric Explorer, o navegador regressa à página inicial, mas uma mensagem pede-lhe para selecionar um certificado.

![Diálogo de certificado SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Razão
O utilizador não tem um papel na aplicação de cluster Azure AD. Assim, a autenticação Azure AD falha no cluster Service Fabric. Service Fabric Explorer volta à autenticação do certificado.

#### <a name="solution"></a>Solução
Siga as instruções para a configuração do AD Azure e atribua as funções do utilizador. Além disso, recomendamos que ligue a "atribuição `SetupApplications.ps1` do utilizador necessária para aceder à app", assim como.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>A ligação com a PowerShell falha com um erro: "As credenciais especificadas são inválidas"
#### <a name="problem"></a>Problema
Quando utiliza o PowerShell para se ligar ao cluster utilizando o modo de segurança "AzureActiveDirectory", depois de iniciar sessão com sucesso no Azure AD, a ligação falha com um erro: "As credenciais especificadas são inválidas."

#### <a name="solution"></a>Solução
Esta solução é a mesma que a anterior.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer devolve uma falha ao iniciar sessão: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando tenta iniciar sessão no Azure AD no Service Fabric Explorer, a página devolve uma falha: "AADSTS50011: O &lt;&gt;url &lt;&gt; do endereço de resposta não corresponde aos endereços de resposta configurados para a aplicação: guia ."

![Endereço de resposta SFX não corresponde][sfx-reply-address-not-match]

#### <a name="reason"></a>Razão
A aplicação cluster (web) que representa o Service Fabric Explorer tenta autenticar contra a AD Azure, e como parte do pedido fornece o URL de retorno redirecionado. Mas o URL não está listado na lista de **URL** resposta da aplicação AD Azure.

#### <a name="solution"></a>Solução
Na página de registo da aplicação Azure AD para o seu cluster, **selecione Autenticação**, e sob a secção **REDirect URIs,** adicione o URL do Explorador de Tecidos de Serviço à lista. Poupe o seu troco.

![URL de resposta a aplicação web][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Ligar-se ao cluster utilizando a autenticação Azure AD via PowerShell dá um erro ao iniciar sessão: "AADSTS50011"
#### <a name="problem"></a>Problema
Quando tenta ligar-se a um cluster de tecido de serviço utilizando o Azure AD via PowerShell, a página de entrada devolve uma falha: "AADSTS50011: O url de resposta especificado no pedido não corresponde aos urls de resposta configurados para a aplicação: &lt;guia."&gt;

#### <a name="reason"></a>Razão
À semelhança do número anterior, a PowerShell tenta autenticar contra a AD Azure, que fornece um URL de redirecionamento que não consta da lista de **URLs** de resposta à aplicação AD Azure.  

#### <a name="solution"></a>Solução
Utilize o mesmo processo que no edição anterior, `urn:ietf:wg:oauth:2.0:oob`mas o URL deve ser definido para um redirecionamento especial para autenticação da linha de comando.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ligue o cluster utilizando a autenticação Azure AD via PowerShell
Para ligar o cluster De Serviço Tecido, utilize o seguinte exemplo de comando PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Para saber mais, consulte [Connect-ServiceFabricCluster cmdlet](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Posso reutilizar o mesmo inquilino azure em vários aglomerados?
Sim. Mas lembre-se de adicionar o URL do Service Fabric Explorer à sua aplicação cluster (web). Caso contrário, o Service Fabric Explorer não funciona.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Porque é que ainda preciso de um certificado de servidor enquanto o Azure AD está ativado?
FabricClient e FabricGateway realizam uma autenticação mútua. Durante a autenticação da AD Azure, a integração da AD Azure fornece uma identidade de cliente ao servidor, e o certificado de servidor é usado pelo cliente para verificar a identidade do servidor. Para mais informações sobre certificados de Tecido de Serviço, consulte [certificados X.509 e Tecido de Serviço][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Passos seguintes
Depois de configurar as aplicações do Azure Ative Directory e definir funções para os utilizadores, [configure e implemente um cluster](service-fabric-cluster-creation-via-arm.md).


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
