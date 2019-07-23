---
title: Criar um Cluster Service Fabric executando o Windows no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a implantar um cluster do Windows Service Fabric em uma rede virtual do Azure e um grupo de segurança de rede usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 3e98b159443cec868040298d76e87a8de6b507ae
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385090"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Tutorial: Implantar um Cluster Service Fabric executando o Windows em uma rede virtual do Azure

Este tutorial é a primeira parte de uma série. Você aprende a implantar um cluster de Service Fabric do Azure executando o Windows em uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) e um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) usando o PowerShell e um modelo. Quando tiver terminado, você terá um cluster em execução na nuvem para o qual você pode implantar aplicativos. Para criar um cluster do Linux que usa o CLI do Azure, consulte [criar um cluster do Linux seguro no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Este tutorial descreve um cenário de produção. Se você quiser criar um cluster menor para fins de teste, consulte [criar um cluster de teste](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Instalação Azure Active Directory autenticação
> * Configurar coleta de diagnóstico
> * Configurar o serviço EventStore
> * Configurar logs de Azure Monitor
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale o [SDK do Service Fabric e o módulo do PowerShell](service-fabric-get-started.md).
* Instale o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Examine os principais conceitos dos [clusters do Azure](service-fabric-azure-clusters-overview.md).
* [Planeje e prepare-se](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de cluster de produção.

Os procedimentos a seguir criam um cluster de sete nós Service Fabric. Use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) para calcular os custos incorridos executando um Cluster Service Fabric no Azure.

## <a name="download-and-explore-the-template"></a>Transferir e explorar o modelo

Baixe os seguintes arquivos de modelo de Azure Resource Manager:

* [azuredeploy. JSON][template]
* [azuredeploy. Parameters. JSON][parameters]

Este modelo implanta um cluster seguro de sete máquinas virtuais e três tipos de nós em uma rede virtual e um grupo de segurança de rede.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [azuredeploy. JSON][template] implanta vários recursos, incluindo o seguinte.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, é configurado um cluster Windows com as seguintes características:

* Três tipos de nó.
* Cinco nós no tipo de nó primário (configuráveis nos parâmetros de modelo) e um nó em cada um dos outros dois tipos de nó.
* SO: Windows Server 2016 datacenter com contêineres (configuráveis nos parâmetros do modelo).
* Certificado protegido (configurável nos parâmetros de modelo).
* O [proxy reverso](service-fabric-reverseproxy.md) está habilitado.
* O [serviço DNS](service-fabric-dnsservice.md) está habilitado.
* [Nível](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de durabilidade de bronze (configurável nos parâmetros de modelo).
* [Nível de confiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de prata (configurável nos parâmetros de modelo).
* Ponto de extremidade de conexão do cliente: 19000 (configurável nos parâmetros de modelo).
* Ponto de extremidade do gateway HTTP: 19080 (configurável nos parâmetros de modelo).

### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure

No recurso **Microsoft. Network/** balancers, um balanceador de carga é configurado. As investigações e as regras são configuradas para as seguintes portas:

* Ponto de extremidade de conexão do cliente: 19000
* Ponto de extremidade do gateway HTTP: 19080
* Porta do aplicativo: 80
* Porta do aplicativo: 443
* Service Fabric proxy reverso: 19081

Se outras portas de aplicativo forem necessárias, você precisará ajustar o recurso **Microsoft. Network/** balancers e o recurso **Microsoft. Network/networkSecurityGroups** para permitir o tráfego no.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e o grupo de segurança de rede

Os nomes da rede virtual, da sub-rede e do grupo de segurança de rede são declarados nos parâmetros do modelo. Os espaços de endereços da rede virtual e da sub-rede também são declarados nos parâmetros do modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* Espaço de endereço de rede virtual: 172.16.0.0/20
* Espaço de endereço de sub-rede Service Fabric: 172.16.2.0/23

As seguintes regras de tráfego de entrada estão ativadas no recurso **Microsoft.Network/networkSecurityGroups**. Pode alterar os valores das portas, alterando as variáveis no modelo.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internodecommunication: 1025, 1026, 1027
* Intervalo de portas efêmeras: 49152 a 65534 (precisa de um mínimo de 256 portas).
* Portas para uso do aplicativo: 80 e 443
* Intervalo de portas do aplicativo: 49152 a 65534 (usada para comunicação de serviço a serviço. Outras portas não são abertas no balanceador de carga).
* Bloquear todas as outras portas

Se outras portas de aplicativo forem necessárias, você precisará ajustar o recurso **Microsoft. Network/** balancers e o recurso **Microsoft. Network/networkSecurityGroups** para permitir o tráfego no.

### <a name="windows-defender"></a>Windows Defender
Por padrão, o [programa Windows Defender antivírus](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) está instalado e funcionando no Windows Server 2016. A interface do usuário é instalada por padrão em alguns SKUs, mas não é necessária. Para cada tipo de nó/conjunto de dimensionamento de VM declarado no modelo, a extensão de antimalware de [VM do Azure](/azure/virtual-machines/extensions/iaas-antimalware-windows) é usada para excluir os diretórios e processos de Service Fabric:

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Definir parâmetros de modelo

O arquivo de parâmetros [azuredeploy. Parameters. JSON][parameters] declara muitos valores usados para implantar o cluster e os recursos associados. Veja a seguir os parâmetros a serem modificados para sua implantação:

**Parâmetro** | **Valor de exemplo** | **Notas** 
|---|---|---|
|adminUserName|vmadmin| O nome de utilizador administrador para as VMs do cluster. [Requisitos de nome de usuário para a VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| A palavra-passe de utilizador administrador para as VMs do cluster. [Requisitos de senha para a VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| O nome do cluster. Pode conter apenas letras e números. O comprimento pode ter entre 3 e 23 carateres.|
|location|southcentralus| A localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor do thumbprint SHA-1 do certificado. Por exemplo, “6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado. </p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o URL do certificado. Por exemplo, "https:\//mykeyvault.Vault.Azure.net:443/Secrets/myCertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor no cofre de origem. Por exemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurar Azure Active Directory autenticação de cliente
Para Service Fabric clusters implantados em uma rede pública hospedada no Azure, a recomendação para a autenticação mútua de cliente para nó é:
* Use Azure Active Directory para a identidade do cliente.
* Use um certificado para a identidade do servidor e a criptografia SSL da comunicação HTTP.

A configuração do Azure Active Directory (Azure AD) para autenticar clientes para um Cluster Service Fabric deve ser feita antes [de criar o cluster](#createvaultandcert). O Azure AD permite que as organizações (conhecidas como locatários) gerenciem o acesso do usuário aos aplicativos. 

Um Cluster Service Fabric oferece vários pontos de entrada para sua funcionalidade de gerenciamento, incluindo o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) baseado na Web e o [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Como resultado, você cria dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo.  Depois que os aplicativos são criados, você atribui usuários a funções de administrador e somente leitura.

> [!NOTE]
> Você deve concluir as etapas a seguir antes de criar o cluster. Como os scripts esperam nomes de cluster e pontos de extremidade, os valores devem ser planejados e não os valores que você já criou.

Neste artigo, presumimos que você já criou um locatário. Se você ainda não fez isso, comece lendo [como obter um locatário de Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Para simplificar as etapas envolvidas na configuração do Azure AD com um Cluster Service Fabric, criamos um conjunto de scripts do Windows PowerShell. [Baixe os scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) em seu computador.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicativos do Azure AD e atribuir usuários a funções
Crie dois aplicativos do Azure AD para controlar o acesso ao cluster: um aplicativo Web e um aplicativo nativo. Depois de criar os aplicativos para representar o cluster, atribua os usuários às [funções com suporte pelo Service Fabric](service-fabric-cluster-security-roles.md): somente leitura e administrador.

Execute `SetupApplications.ps1`e forneça a ID do locatário, o nome do cluster e a URL de resposta do aplicativo Web como parâmetros. Especifique nomes de usuário e senhas para os usuários. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo, Azure governamental, Azure China, Azure Alemanha), especifique `-Location` o parâmetro.

Você pode encontrar sua ID de *locatário*, ou de diretório, no [portal do Azure](https://portal.azure.com). Selecione**Propriedades** de **Azure Active Directory** > e copie o valor da **ID de diretório** .

*ClusterName* é usado para prefixar os aplicativos do Azure ad criados pelo script. Ele não precisa corresponder exatamente ao nome do cluster real. Ele só facilita o mapeamento de artefatos do Azure AD para o Cluster Service Fabric em uso.

*WebApplicationReplyUrl* é o ponto de extremidade padrão que o Azure ad retorna para os usuários depois que terminam de entrar. Defina esse ponto de extremidade como o ponto de extremidade Service Fabric Explorer para o cluster, que por padrão é:

https://&lt;cluster_domain&gt;:19080/Explorer

Você será solicitado a entrar em uma conta que tenha privilégios administrativos para o locatário do Azure AD. Depois de entrar, o script cria os aplicativos Web e nativos para representar o Cluster Service Fabric. Nos aplicativos do locatário no [portal do Azure](https://portal.azure.com), você deverá ver duas novas entradas:

   * *Cluster ClusterName*\_
   * *ClusterName*\_Client

O script imprime o JSON exigido pelo modelo do Resource Manager quando você cria o cluster, portanto, é uma boa ideia manter a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar a configuração do Azure AD para usar o Azure AD para acesso de cliente
No [azuredeploy. JSON][template], configure o Azure ad na seção **Microsoft. perfabric/clusters** . Adicione parâmetros para a ID do locatário, a ID do aplicativo de cluster e a ID do aplicativo cliente.  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Adicione os valores de parâmetro no arquivo de parâmetros [azuredeploy. Parameters. JSON][parameters] . Por exemplo:

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```
<a id="configurediagnostics" name="configurediagnostics_anchor"></a>

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Configurar a coleta de diagnóstico no cluster
Quando você estiver executando um Cluster Service Fabric, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda você a analisar e solucionar problemas no cluster ou problemas nos aplicativos e serviços em execução nesse cluster.

Uma maneira de carregar e coletar logs é usar a extensão Diagnóstico do Azure (WAD), que carrega os logs no armazenamento do Azure e também tem a opção de enviar logs para Aplicativo Azure insights ou hubs de eventos. Você também pode usar um processo externo para ler os eventos do armazenamento e colocá-los em um produto da plataforma de análise, como logs de Azure Monitor ou outra solução de análise de log.

Se você estiver seguindo este tutorial, a coleta de diagnóstico já estará configurada no [modelo][template].

Se você tiver um cluster existente que não tenha o diagnóstico implantado, poderá adicioná-lo ou atualizá-lo por meio do modelo de cluster. Modifique o modelo do Resource Manager que é usado para criar o cluster existente ou baixe o modelo do Portal. Modifique o arquivo template. JSON executando as seguintes tarefas:

Adicione um novo recurso de armazenamento à seção recursos no modelo:
```json
"resources": [
...
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "sku": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
...
]
```

Em seguida, adicione parâmetros para o nome da conta de armazenamento e digite à seção de parâmetros do modelo. Substitua o texto do espaço reservado nome da conta de armazenamento aqui pelo nome da conta de armazenamento que você deseja.

```json
"parameters": {
...
"applicationDiagnosticsStorageAccountType": {
    "type": "string",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
    "description": "Replication option for the application diagnostics storage account"
    }
},
"applicationDiagnosticsStorageAccountName": {
    "type": "string",
    "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
    "metadata": {
    "description": "Name for the storage account that contains application diagnostics data from the cluster"
    }
},
...
}
```

Em seguida, adicione a extensão **IaaSDiagnostics** à matriz de extensões da propriedade **VirtualMachineProfile** de cada recurso **Microsoft. Compute/virtualMachineScaleSets** no cluster.  Se você estiver usando o [modelo de exemplo][template], haverá três conjuntos de dimensionamento de máquinas virtuais (um para cada tipo de nó no cluster).

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
                    "properties": {
                        "type": "IaaSDiagnostics",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                        "storageAccountEndPoint": "https://core.windows.net/"
                        },
                        "publisher": "Microsoft.Azure.Diagnostics",
                        "settings": {
                        "WadCfg": {
                            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                            "EtwProviders": {
                                "EtwEventSourceProviderConfiguration": [
                                {
                                    "provider": "Microsoft-ServiceFabric-Actors",
                                    "scheduledTransferKeywordFilter": "1",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableActorEventTable"
                                    }
                                },
                                {
                                    "provider": "Microsoft-ServiceFabric-Services",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                                    }
                                }
                                ],
                                "EtwManifestProviderConfiguration": [
                                {
                                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                    "scheduledTransferLogLevelFilter": "Information",
                                    "scheduledTransferKeywordFilter": "4611686018427387904",
                                    "scheduledTransferPeriod": "PT5M",
                                    "DefaultEvents": {
                                    "eventDestination": "ServiceFabricSystemEventTable"
                                    }
                                }
                                ]
                            }
                            }
                        },
                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                        },
                        "typeHandlerVersion": "1.5"
                    }
                }
            ...
            ]
        }
    }
}
```
<a id="configureeventstore" name="configureeventstore_anchor"></a>

## <a name="configure-the-eventstore-service"></a>Configurar o serviço EventStore
O serviço EventStore é uma opção de monitoramento no Service Fabric. O EventStore fornece uma maneira de entender o estado do cluster ou das cargas de trabalho em um determinado momento. O EventStore é um serviço de Service Fabric com estado que mantém eventos do cluster. O evento é exposto por meio do Service Fabric Explorer, REST e APIs. O EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade no cluster e deve ser usado para ajudar:

* Diagnosticar problemas no desenvolvimento ou teste, ou onde você pode estar usando um pipeline de monitoramento
* Confirmar que as ações de gerenciamento que você está executando no cluster estão sendo processadas corretamente
* Obter um "instantâneo" de como Service Fabric está interagindo com uma entidade específica



Para habilitar o serviço EventStore em seu cluster, adicione o seguinte à propriedade **fabricSettings** do recurso **Microsoft. Service Fabric/clusters** :

```json
"apiVersion": "2018-02-01",
"type": "Microsoft.ServiceFabric/clusters",
"name": "[parameters('clusterName')]",
"properties": {
    ...
    "fabricSettings": [
        ...
        {
            "name": "EventStoreService",
            "parameters": [
                {
                "name": "TargetReplicaSetSize",
                "value": "3"
                },
                {
                "name": "MinReplicaSetSize",
                "value": "1"
                }
            ]
        }
    ]
}
```
<a id="configureloganalytics" name="configureloganalytics_anchor"></a>

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Configurar logs de Azure Monitor para o cluster

Azure Monitor logs é nossa recomendação para monitorar eventos no nível do cluster. Para configurar logs de Azure Monitor para monitorar o cluster, você precisa ter o [diagnóstico habilitado para exibir eventos no nível do cluster](#configure-diagnostics-collection-on-the-cluster).  

A área de trabalho precisa de estar ligado para os dados de diagnóstico originários do seu cluster.  Esses dados de log são armazenados na conta de armazenamento *applicationDiagnosticsStorageAccountName* , nas tabelas WADServiceFabric * eventtable, WADWindowsEventLogsTable e WADETWEventTable.

Adicione o espaço de trabalho Log Analytics do Azure e adicione a solução ao espaço de trabalho:

```json
"resources": [
    ...
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Memory",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Available MBytes"
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "type": "datasources",
                "name": "sampleWindowsPerfCounter2",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsPerformanceCounter",
                "properties": {
                    "objectName": "Service Fabric Service",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "counterName": "Average milliseconds per request"
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
]
```

Em seguida, adicione parâmetros
```json
"parameters": {
    ...
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "mysfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "West Europe",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
}
```

Em seguida, adicione variáveis:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Adicione a extensão do agente de Log Analytics a cada conjunto de dimensionamento de máquinas virtuais no cluster e conecte o agente ao espaço de trabalho Log Analytics. Isso permite coletar dados de diagnóstico sobre contêineres, aplicativos e monitoramento de desempenho. Ao adicioná-lo como uma extensão ao recurso do conjunto de dimensionamento de máquinas virtuais, o Azure Resource Manager garante que ele seja instalado em todos os nós, mesmo ao dimensionar o cluster.

```json
"apiVersion": "2018-10-01",
"type": "Microsoft.Compute/virtualMachineScaleSets",
"name": "[variables('vmNodeType1Name')]",
"properties": {
    ...
    "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "MicrosoftMonitoringAgent",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
                        }
                    }
                }
            ...
            ]
        }
    }
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Implementar a rede virtual e o cluster

Em seguida, configure a topologia de rede e implemente o cluster do Service Fabric. O modelo do Gerenciador de recursos [azuredeploy. JSON][template] cria uma rede virtual, uma sub-rede e um grupo de segurança de rede para Service Fabric. O modelo também implementa um cluster com a segurança do certificado ativada. Para clusters de produção, use um certificado de uma autoridade de certificação como o certificado de cluster. Um certificado autoassinado pode ser utilizado para proteger clusters de teste.

O modelo neste artigo implanta um cluster que usa a impressão digital do certificado para identificar o certificado do cluster. Dois certificados não podem ter a mesma impressão digital, o que torna o gerenciamento de certificados mais difícil. Alternar um cluster implantado de impressões digitais de certificado para nomes comuns de certificado simplifica o gerenciamento de certificados. Para saber como atualizar o cluster para usar nomes comuns de certificado para o gerenciamento de certificados, leia [alterar cluster para gerenciamento de nome comum de certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Criar um cluster usando um certificado existente

O script a seguir usa o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster no Azure. O cmdlet cria um novo cofre de chaves no Azure e carrega seu certificado.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # Must match the clustername parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Criar um cluster usando um certificado novo e autoassinado

O script a seguir usa o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster no Azure. O cmdlet cria um novo cofre de chaves no Azure, adiciona um novo certificado autoassinado ao cofre de chaves e baixa o arquivo de certificado localmente.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # Must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Sign in to your Azure account and select your subscription
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Ligar ao cluster seguro

Conecte-se ao cluster usando o módulo Service Fabric PowerShell instalado com o SDK do Service Fabric.  Primeiro, instale o certificado no arquivo Pessoal (Meu) do utilizador atual no seu computador. Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Agora você está pronto para se conectar ao seu cluster seguro.

O módulo **Service Fabric** do PowerShell fornece muitos cmdlets para gerir clusters, aplicações e serviços do Service Fabric. Utilize o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para ligar ao cluster seguro. Os detalhes do thumbprint SHA-1 do certificado e ponto final de ligação encontram-se nos resultados do passo anterior.

Se você configurou anteriormente a autenticação de cliente do Azure AD, execute o seguinte comando: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Se você não tiver configurado a autenticação de cliente do Azure AD, execute o seguinte comando:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se você está conectado e se o cluster está íntegro usando o cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) .

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar recursos

Os outros artigos nesta série de tutoriais usam o cluster que você criou. Se não passar imediatamente para o artigo seguinte, poderá [eliminar o cluster](service-fabric-cluster-delete.md) para evitar incorrer em custos.

## <a name="next-steps"></a>Passos seguintes

Avance para o tutorial a seguir para saber como dimensionar o cluster.

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Instalação Azure Active Directory autenticação
> * Configurar coleta de diagnóstico
> * Configurar o serviço EventStore
> * Configurar logs de Azure Monitor
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Em seguida, avance para o tutorial a seguir para saber como monitorar o cluster.
> [!div class="nextstepaction"]
> [Monitorar um cluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
