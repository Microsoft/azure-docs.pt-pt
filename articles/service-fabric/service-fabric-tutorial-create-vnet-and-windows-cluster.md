---
title: Criar um cluster do Service Fabric em execução Windows no Azure | Documentos da Microsoft
description: Neste tutorial, saiba como implementar um cluster do Service Fabric do Windows numa rede virtual do Azure e o grupo de segurança de rede com o PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/13/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: aa44355ea86b42f8865d7791fec04ffad2b6f3ad
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313826"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Tutorial: Implementar um cluster do Service Fabric em execução Windows numa rede virtual do Azure

Este tutorial é a primeira parte de uma série. Saiba como implementar um cluster do Azure Service Fabric com o Windows numa [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) e [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) utilizando o PowerShell e um modelo. Quando tiver terminado, terá um cluster em execução na cloud para o qual pode implementar aplicações. Para criar um cluster do Linux que utiliza a CLI do Azure, veja [criar um cluster do Linux seguro no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Este tutorial descreve um cenário de produção. Se pretender criar um cluster mais pequeno para fins de teste, consulte [criar um cluster de teste](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configurar a autenticação do Azure Active Directory
> * Configurar a recolha de diagnóstico
> * Configurar o serviço de EventStore
> * Configurar os registos do Azure Monitor
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Monitorizar um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instalar o [módulo de PowerShell e SDK do Service Fabric](service-fabric-get-started.md).
* Instalar o [do Azure Powershell versão 4.1 ou superior do módulo](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Reveja os conceitos chave dos [clusters do Azure](service-fabric-azure-clusters-overview.md).

Os procedimentos seguintes criam um cluster do Service Fabric de sete nós. Utilize o [Calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/) para calcular o custo incorrido pela execução de um cluster do Service Fabric no Azure.

## <a name="download-and-explore-the-template"></a>Transferir e explorar o modelo

Transfira os ficheiros de modelo do Gestor de recursos do Azure seguintes:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Este modelo implementa um cluster seguro de sete máquinas de virtuais e três tipos de nó numa rede virtual e um grupo de segurança de rede.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [azuredeploy. JSON] [ template] implementa vários recursos, incluindo o seguinte.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, é configurado um cluster Windows com as seguintes características:

* Três tipos de nó.
* Cinco nós no tipo de nó primário (configurável nos parâmetros do modelo) e um nó em cada um dos outros tipos de dois nós.
* SO: Windows Server 2016 Datacenter com contentores (configuráveis nos parâmetros do modelo).
* Certificado protegida (configurável nos parâmetros do modelo).
* [Proxy inverso](service-fabric-reverseproxy.md) está ativada.
* [Serviço DNS](service-fabric-dnsservice.md) está ativada.
* [Nível de durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de Bronze (configurável nos parâmetros do modelo).
* [Nível de fiabilidade](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de prata (configurável nos parâmetros do modelo).
* ponto final de ligação de cliente: 19000 (configurável nos parâmetros do modelo).
* Ponto final de gateway HTTP: 19080 (configurável nos parâmetros do modelo).

### <a name="azure-load-balancer"></a>Azure Load Balancer

Na **Network/loadbalancers** recurso, um balanceador de carga está configurado. Sondas e regras são configuradas para as seguintes portas:

* ponto final de ligação de cliente: 19000
* Ponto final de gateway HTTP: 19080
* porta da aplicação: 80
* porta da aplicação: 443
* Proxy inverso do Service Fabric: 19081

Se forem necessárias outras portas da aplicação, terá de ajustar a **Network/loadbalancers** recursos e o **networksecuritygroups** recursos para permitir o tráfego no.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e o grupo de segurança de rede

Os nomes da rede virtual, da sub-rede e do grupo de segurança de rede são declarados nos parâmetros do modelo. Os espaços de endereços da rede virtual e da sub-rede também são declarados nos parâmetros do modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* espaço de endereços de rede virtual: 172.16.0.0/20
* Espaço de endereços de sub-rede do Service Fabric: 172.16.2.0/23

As seguintes regras de tráfego de entrada estão ativadas no recurso **Microsoft.Network/networkSecurityGroups**. Pode alterar os valores das portas, alterando as variáveis no modelo.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Comunicação entre nós: 1025, 1026, 1027
* Intervalo de porta efêmera: 49152 até à 65534 (precisa de um mínimo de 256 portas).
* Portas para utilização de aplicações: 80 e 443
* Intervalo de portas de aplicação: 49152 até à 65534 (utilizadas para comunicação de serviço para serviço. Outras portas não estão abertas no balanceador de carga).
* Bloquear todas as outras portas

Se forem necessárias outras portas da aplicação, terá de ajustar a **Network/loadbalancers** recursos e o **networksecuritygroups** recursos para permitir o tráfego no.

### <a name="windows-defender"></a>Windows Defender
Por predefinição, o [programa antivírus do Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) está instalado e funcional no Windows Server 2016. A interface do usuário é instalada por predefinição em alguns SKUs, mas não é necessária. Para cada escala VM/tipo de nó definida declarada no modelo, o [extensão de Antimalware de VM do Azure](/azure/virtual-machines/extensions/iaas-antimalware-windows) é utilizada para excluir os diretórios de Service Fabric e os processos:

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

O ficheiro de parâmetro [azuredeploy.parameters.json][parameters] declara vários valores utilizados para implementar o cluster e os recursos associados. Seguem-se parâmetros para modificar para a sua implementação:

**Parâmetro** | **Valor de exemplo** | **Notas** 
|---|---|---|
|adminUserName|vmadmin| O nome de utilizador administrador para as VMs do cluster. [Requisitos de nome de utilizador para a VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| A palavra-passe de utilizador administrador para as VMs do cluster. [Requisitos de palavra-passe para a VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| O nome do cluster. Pode conter apenas letras e números. O comprimento pode ter entre 3 e 23 carateres.|
|localização|southcentralus| A localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor do thumbprint SHA-1 do certificado. Por exemplo, “6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado. </p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o URL do certificado. Por exemplo, "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor no cofre de origem. Por exemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurar a autenticação de cliente do Azure Active Directory
Para clusters do Service Fabric implementados numa rede pública alojada no Azure, a recomendação para autenticação mútua do nó de cliente é:
* Utilize o Azure Active Directory para a identidade de cliente.
* Utilize um certificado para encriptação SSL de comunicação HTTP e de identidade do servidor.

Configurar o Azure Active Directory (Azure AD) para autenticar clientes para um cluster do Service Fabric deve ser feito antes [criar o cluster](#createvaultandcert). O Azure AD permite às organizações (conhecidas como inquilinos) para gerir o acesso de utilizador para aplicações. 

Um cluster do Service Fabric oferece vários pontos de entrada para sua funcionalidade de gestão, incluindo baseada na web [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Como resultado, vai criar duas aplicações do Azure AD para controlar o acesso ao cluster: aplicação web de um e um aplicativo nativo.  Depois dos aplicativos são criados, atribuir utilizadores para só de leitura e funções de administrador.

> [!NOTE]
> Tem de concluir os seguintes passos antes de criar o cluster. Porque os scripts esperam que os nomes de cluster e pontos de extremidade, os valores devem ser planeadas e não os valores que já tenha criado.

Neste artigo, partimos do princípio que já criou um inquilino. Caso contrário, comece por ler [como obter um inquilino do Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Para simplificar as etapas envolvidas na configuração do Azure AD com um cluster do Service Fabric, criamos um conjunto de scripts do Windows PowerShell. [Transferir os scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) para o seu computador.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicações do Azure AD e atribuir utilizadores a funções
Criar duas aplicações do Azure AD para controlar o acesso ao cluster: aplicação web de um e um aplicativo nativo. Depois de criar os aplicativos para representar o cluster, atribuir os seus utilizadores para o [funções com suporte para o Service Fabric](service-fabric-cluster-security-roles.md): só de leitura e administrador.

Executar `SetupApplications.ps1`e forneça o inquilino, ID, o nome do cluster e o URL de resposta do aplicativo web, como parâmetros. Especifique nomes de utilizador e palavras-passe para os utilizadores. Por exemplo:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para nuvens nacionais (por exemplo o Azure Government, Azure China, Azure Alemanha), especifique o `-Location` parâmetro.

Pode encontrar suas *TenantId*, ou o ID de diretório, na [portal do Azure](https://portal.azure.com). Selecione **do Azure Active Directory** > **propriedades** e copie o **ID do diretório** valor.

*ClusterName* é utilizado para o prefixo de aplicações do Azure AD que são criadas pelo script. Não tem de corresponder exatamente ao nome do cluster real. Isso só torna mais fácil mapear os artefactos do Azure AD para o cluster do Service Fabric em utilização.

*WebApplicationReplyUrl* é o ponto final predefinido, que retorna do Azure AD para os seus utilizadores, depois de concluir a iniciar sessão. Defina este ponto final como o ponto de final do Service Fabric Explorer para o seu cluster, o que, por predefinição, é:

https://&lt;cluster_domain&gt;:19080/Explorer

Lhe for pedido para iniciar sessão a uma conta que tenha privilégios administrativos para o inquilino do Azure AD. Depois de iniciar sessão, o script cria o web e aplicativos nativos para representar o cluster do Service Fabric. Nos aplicativos do inquilino a [portal do Azure](https://portal.azure.com), deverá ver duas novas entradas:

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

O script imprime o JSON necessário para o modelo do Resource Manager, quando criar o cluster, pelo que é uma boa idéia mantenha a janela do PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicionar configuração do Azure AD para utilizar o Azure AD para acesso de cliente
Na [azuredeploy. JSON][template], configurar o Azure AD no **Microsoft.ServiceFabric/clusters** secção. Adicionar parâmetros para o inquilino ID, o ID de aplicação do cluster e o ID de aplicação de cliente.  

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

Adicione os valores de parâmetros nos [azuredeploy] [ parameters] ficheiro de parâmetros. Por exemplo:

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

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Configurar a recolha de diagnóstico no cluster
Quando estiver a executar um cluster do Service Fabric, é uma boa idéia para recolher os registos de todos os nós numa localização central. Ter os registos numa localização central ajuda a analisar e resolver problemas no seu cluster, ou problemas em aplicações e serviços em execução nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão de diagnóstico do Azure (WAD), que carrega os registos para o armazenamento do Azure e, também tem a opção para enviar registos para o Azure Application Insights ou Hubs de eventos. Também pode utilizar um processo externo para ler os eventos de armazenamento e colocá-los num produto de plataforma de análise, como registos do Azure Monitor ou outra solução de análise de registos.

Se estiver a seguir este tutorial, a recolha de diagnóstico já está configurada no [modelo][template].

Se tiver um cluster existente que não tem o diagnóstico implementado, pode adicionar ou atualizá-lo por meio do modelo de cluster. Modificar o modelo do Resource Manager que é utilizado para criar o cluster existente ou transferir o modelo a partir do portal. Modifique o ficheiro Template JSON, efetuando as seguintes tarefas:

Adicione um novo recurso de armazenamento para a secção de recursos no modelo:
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

Em seguida, adicione parâmetros para o nome da conta de armazenamento e o tipo para a secção de parâmetros do modelo. Substitua que o nome de conta de armazenamento de texto de marcador de posição entra que aqui com o nome de armazenamento de conta que gostaria de ter.

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

Em seguida, adicione a **IaaSDiagnostics** extensão para a matriz de extensões da **VirtualMachineProfile** propriedade de cada **Compute/virtualmachinescalesets** recursos do cluster.  Se estiver a utilizar o [modelo de exemplo][template], existem três conjuntos de dimensionamento de máquinas virtuais (uma para cada tipo de nó do cluster).

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

## <a name="configure-the-eventstore-service"></a>Configurar o serviço de EventStore
O serviço de EventStore é uma opção de monitorização no Service Fabric. EventStore fornece uma forma de compreender o estado do seu cluster ou cargas de trabalho num determinado ponto no tempo. O EventStore é um serviço do Service Fabric com monitorização de estado que mantém os eventos do cluster. O evento são expostos por meio do Service Fabric Explorer, REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico sobre qualquer entidade no seu cluster e deve ser utilizado para o ajudar a:

* Diagnosticar problemas em desenvolvimento ou teste, ou onde poderá estar a utilizar um pipeline de monitorização
* Confirme que as ações de gestão que está a efetuar no seu cluster estão a ser processadas corretamente
* Obtém um "instantâneo" de como o Service Fabric está a interagir com uma entidade específica



Para ativar o serviço de EventStore no seu cluster, adicione o seguinte para o **fabricSettings** propriedade da **Microsoft.ServiceFabric/clusters** recursos:

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

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Configurar os registos do Azure Monitor para o cluster

Registos de Monitor do Azure é a nossa recomendação para monitorizar os eventos de nível de cluster. Para configurar os registos do Azure Monitor para monitorizar o seu cluster, tem de ter [diagnóstico ativado para ver eventos de nível de cluster](#configure-diagnostics-collection-on-the-cluster).  

A área de trabalho precisa de estar ligado para os dados de diagnóstico originários do seu cluster.  Estes dados de registo são armazenados no *applicationDiagnosticsStorageAccountName* conta de armazenamento, no WADServiceFabric * EventTable WADWindowsEventLogsTable e WADETWEventTable tabelas.

Adicione a área de trabalho do Log Analytics do Azure e adicionar a solução para a área de trabalho:

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

Em seguida, adicione as variáveis:
```json
"variables": {
    ...
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
}
```

Adicione o Log Analytics, extensão do agente para cada dimensionamento de máquinas virtuais definido no cluster e ligar o agente para a área de trabalho do Log Analytics. Isso permite recolher dados de diagnóstico sobre contentores, aplicativos e monitorização do desempenho. Ao adicioná-la como uma extensão para o recurso de conjunto de dimensionamento de máquina virtual, do Azure Resource Manager garante que é instalado em cada nó, mesmo quando a dimensionar o cluster.

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

Em seguida, configure a topologia de rede e implemente o cluster do Service Fabric. O [azuredeploy. JSON] [ template] modelo do Resource Manager cria uma rede virtual, uma sub-rede e um grupo de segurança de rede para o Service Fabric. O modelo também implementa um cluster com a segurança do certificado ativada. Para clusters de produção, utilize um certificado de uma autoridade de certificação como o certificado de cluster. Um certificado autoassinado pode ser utilizado para proteger clusters de teste.

O modelo neste artigo implementa um cluster que utiliza o thumbprint do certificado para identificar o certificado de cluster. Não existem dois certificados podem ter o mesmo thumbprint, o que torna mais difícil a gestão de certificados. Mudar de um cluster implementado de thumbprints de certificado para o nome comum do certificado de simplifica a gestão de certificados. Para saber como atualizar o cluster para utilizar nomes comuns do certificado para a gestão de certificados, leia [cluster de alteração para o gerenciamento de nome comum do certificado](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Criar um cluster com um certificado existente

O seguinte script utiliza o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e um modelo para implementar um novo cluster no Azure. O cmdlet cria um novo cofre de chaves no Azure e carrega o certificado.

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
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Criar um cluster com um novo certificado autoassinado

O seguinte script utiliza o cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) e um modelo para implementar um novo cluster no Azure. O cmdlet cria um novo cofre de chaves no Azure, adiciona um novo certificado autoassinado ao Cofre de chaves e transfere o ficheiro de certificado localmente.

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
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment, and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Ligar ao cluster seguro

Ligar ao cluster com o módulo do PowerShell do Service Fabric instalado com o SDK do Service Fabric.  Primeiro, instale o certificado no arquivo Pessoal (Meu) do utilizador atual no seu computador. Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Agora, está pronto para ligar ao seu cluster seguro.

O módulo **Service Fabric** do PowerShell fornece muitos cmdlets para gerir clusters, aplicações e serviços do Service Fabric. Utilize o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para ligar ao cluster seguro. Os detalhes do thumbprint SHA-1 do certificado e ponto final de ligação encontram-se nos resultados do passo anterior.

Se configurar a autenticação de cliente do Azure AD anteriormente, execute o seguinte comando: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Se não tiver configurado a autenticação de cliente do Azure AD, execute o seguinte comando:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique que está ligado e que o cluster está em bom estado, utilizando o [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar recursos

Os outros artigos nesta série de tutoriais utilizam o cluster que criou. Se não passar imediatamente para o artigo seguinte, poderá [eliminar o cluster](service-fabric-cluster-delete.md) para evitar incorrer em custos.

## <a name="next-steps"></a>Passos Seguintes

Avance para o tutorial seguinte para aprender a dimensionar o seu cluster.

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configurar a autenticação do Azure Active Directory
> * Configurar a recolha de diagnóstico
> * Configurar o serviço de EventStore
> * Configurar os registos do Azure Monitor
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Em seguida, avance para o tutorial seguinte para aprender a monitorizar o seu cluster.
> [!div class="nextstepaction"]
> [Monitorizar um Cluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
