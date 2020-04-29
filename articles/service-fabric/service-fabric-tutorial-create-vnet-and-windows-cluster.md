---
title: Criar um cluster de tecido de serviço que executa janelas em Azure
description: Neste tutorial, aprende-se a implantar um cluster de Tecido de Serviço Windows num grupo de segurança de rede e rede azul Azure utilizando o PowerShell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 2d170057a85a8e223fa9d1bc2bfc17e0c284afcd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756049"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Tutorial: Implementar um cluster de tecido de serviço que executa o Windows numa rede virtual Azure

Este tutorial é a primeira parte de uma série. Aprende-se a implantar um cluster De Tecido de Serviço Azure que executa o Windows num grupo de [segurança](../virtual-network/virtual-networks-nsg.md) de rede e [rede virtual Azure](../virtual-network/virtual-networks-overview.md) utilizando o PowerShell e um modelo. Quando terminar, tem um cluster a funcionar na nuvem para o qual pode implementar aplicações. Para criar um cluster Linux que usa o Azure CLI, consulte [Criar um cluster Linux seguro em Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Este tutorial descreve um cenário de produção. Se quiser criar um cluster menor para fins de teste, consulte [Criar um cluster](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)de teste .

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configuração Azure Ative Diretório autenticação
> * Configure a recolha de diagnósticos
> * Configurar o serviço EventStore
> * Configurar registos do Monitor Azure
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * Criar um cluster seguro no Azure
> * [Monitorize um cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Reduzir ou aumentar um cluster horizontalmente](service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Eliminar um cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [módulo SDK e PowerShell](service-fabric-get-started.md)de tecido de serviço.
* Instale [a Powershell Azure](https://docs.microsoft.com/powershell/azure/install-Az-ps).
* Reveja os conceitos-chave dos [clusters Azure.](service-fabric-azure-clusters-overview.md)
* [Planeie e prepare-se](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de cluster de produção.

Os seguintes procedimentos criam um cluster de tecido de serviço de sete nós. Utilize a Calculadora de [Preços Azure](https://azure.microsoft.com/pricing/calculator/) para calcular o custo incorrido através da execução de um cluster de tecido de serviço em Azure.

## <a name="download-and-explore-the-template"></a>Transferir e explorar o modelo

Descarregue os seguintes ficheiros de modelo do Gestor de Recursos Do Azure:

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Este modelo implementa um conjunto seguro de sete máquinas virtuais e três tipos de nós numa rede virtual e num grupo de segurança de rede.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [azuredeploy.json][template] implanta uma série de recursos, incluindo os seguintes.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, é configurado um cluster Windows com as seguintes características:

* Três tipos de nó.
* Cinco nós no tipo de nó primário (configurável nos parâmetros do modelo), e um nó em cada um dos outros dois tipos de nó.
* OS: Windows Server 2016 Datacenter com Recipientes (configurável nos parâmetros do modelo).
* Certificado seguro (configurável nos parâmetros do modelo).
* O [procura-proxy invertido](service-fabric-reverseproxy.md) está ativado.
* [O serviço DNS](service-fabric-dnsservice.md) está ativado.
* [Nível](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) de durabilidade de Bronze (configurável nos parâmetros do modelo).
* [Nível](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) de fiabilidade da Prata (configurável nos parâmetros do modelo).
* Ponto final de ligação ao cliente: 19000 (configurável nos parâmetros do modelo).
* Ponto final do gateway HTTP: 19080 (configurável nos parâmetros do modelo).

### <a name="azure-load-balancer"></a>Azure Load Balancer

No recurso **Microsoft.Network/loadBalancers,** está configurado um balanceor de carga. São criadas sondas e regras para as seguintes portas:

* Ponto final de ligação ao cliente: 19000
* ponto final de gateway HTTP: 19080
* Porta de aplicação: 80
* Porta de aplicação: 443
* Proxy inverso do Service Fabric: 19081

Se forem necessárias outras portas de aplicação, terá de ajustar o recurso **Microsoft.Network/loadBalancers** e o recurso **Microsoft.Network/networkSecurityGroups** para permitir a colocação em vigor.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e o grupo de segurança de rede

Os nomes da rede virtual, da sub-rede e do grupo de segurança de rede são declarados nos parâmetros do modelo. Os espaços de endereços da rede virtual e da sub-rede também são declarados nos parâmetros do modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* Espaço de endereço de rede virtual: 172.16.0.0/20
* espaço de endereços de sub-rede do Service Fabric: 172.16.2.0/23

As seguintes regras de tráfego de entrada estão ativadas no recurso **Microsoft.Network/networkSecurityGroups**. Pode alterar os valores das portas, alterando as variáveis no modelo.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Internadecommunication: 1025, 1026, 1027
* Gama efémera da porta: 49152 a 65534 (necessidade de um mínimo de 256 portas).
* Portas para utilização de aplicações: 80 e 443
* Gama de portas de aplicação: 49152 a 65534 (utilizada para o serviço de comunicação de serviço. Outras portas não são abertas no equilibrista de carga).
* Bloquear todas as outras portas

Se forem necessárias outras portas de aplicação, terá de ajustar o recurso **Microsoft.Network/loadBalancers** e o recurso **Microsoft.Network/networkSecurityGroups** para permitir a colocação em vigor.

### <a name="windows-defender"></a>Windows Defender
Por predefinição, o [programa antivírus Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) está instalado e funcional no Windows Server 2016. A interface do utilizador é instalada por padrão em algumas SKUs, mas não é necessária. Para cada conjunto de escala tipo/VM do nó declarado no modelo, a [extensão Antimalware Azure VM](/azure/virtual-machines/extensions/iaas-antimalware-windows) é utilizada para excluir os diretórios e processos do Tecido de Serviço:

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

O ficheiro de parâmetro [azuredeploy.parameters.json][parameters] declara vários valores utilizados para implementar o cluster e os recursos associados. Seguem-se parâmetros a modificar para a sua implantação:

**Parâmetro** | **Valor de exemplo** | **Notas** 
|---|---|---|
|adminUserName|vmadmin| O nome de utilizador administrador para as VMs do cluster. [Requisitos](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm)de nome de utilizador para VM . |
|adminPassword|Password#1234| A palavra-passe de utilizador administrador para as VMs do cluster. [Requisitos de senha para VM](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| O nome do cluster. Pode conter apenas letras e números. O comprimento pode ter entre 3 e 23 carateres.|
|localização|southcentralus| A localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor do thumbprint SHA-1 do certificado. Por exemplo, “6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado. </p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o URL do certificado. Por exemplo, "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor no cofre de origem. Por exemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurar a autenticação do cliente do Diretório Ativo azure
Para os clusters service Fabric implantados numa rede pública alojada no Azure, a recomendação para a autenticação mútua cliente-a-nó é:
* Utilize o Diretório Ativo Azure para a identidade do cliente.
* Utilize um certificado para identidade do servidor e encriptação TLS da comunicação HTTP.

A criação do Azure Ative Directory (Azure AD) para autenticar os clientes para um cluster de tecido de serviço deve ser feita antes de [criar o cluster](#createvaultandcert). A Azure AD permite que as organizações (conhecidas como inquilinos) gerem o acesso dos utilizadores às aplicações. 

Um cluster de tecido de serviço oferece vários pontos de entrada para a sua funcionalidade de gestão, incluindo o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e o Visual [Studio.](service-fabric-manage-application-in-visual-studio.md) Como resultado, cria duas aplicações Azure AD para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa.  Após a criação das aplicações, atribui aos utilizadores funções de leitura e administração.

> [!NOTE]
> Deve completar os seguintes passos antes de criar o cluster. Como os scripts esperam nomes de cluster e pontos finais, os valores devem ser planeados e não valores que já criou.

Neste artigo, assumimos que já criou um inquilino. Se não o fez, comece por ler [Como obter um inquilino azure Ative Directory.](../active-directory/develop/quickstart-create-new-tenant.md)

Para simplificar os passos envolvidos na configuração do Azure AD com um cluster de Tecido de Serviço, criámos um conjunto de scripts Windows PowerShell. [Descarregue os scripts](https://github.com/Azure-Samples/service-fabric-aad-helpers) para o seu computador.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicações ad iae e atribuir utilizadores a funções
Crie duas aplicações Azure AD para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa. Depois de ter criado as aplicações para representar o seu cluster, atribua aos seus utilizadores as [funções suportadas pelo Service Fabric](service-fabric-cluster-security-roles.md): read-only e administrador.

Executar, `SetupApplications.ps1`e fornecer o ID do inquilino, nome do cluster e resposta de aplicação web URL como parâmetros. Especifique os nomes de utilizador e as palavras-passe para os utilizadores. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para as nuvens nacionais (por exemplo, Governo Azure, `-Location` Azure China, Azure Alemanha), especifique o parâmetro.

Pode encontrar o seu *TenantId,* ou ID de diretório, no [portal Azure.](https://portal.azure.com) Selecione **Propriedades de Diretório** > Ativo Azure**e** copie o valor de ID do **Diretório.**

*ClusterName* é usado para pré-fixar as aplicações Azure AD que são criadas pelo script. Não precisa de corresponder ao nome do cluster. Só facilita o mapa dos artefactos Azure AD para o cluster de tecido de serviço em uso.

*WebApplicationReplyUrl* é o ponto final padrão que o Azure AD devolve aos seus utilizadores depois de terminar em sessão. Detete este ponto final como ponto final do Explorador de Tecidode Serviço para o seu cluster, que por padrão é:

https://&lt;&gt;cluster_domain :19080/Explorer

Foi solicitado que assine uma conta que tenha privilégios administrativos para o inquilino da AD Azure. Depois de iniciar sessão, o script cria as aplicações web e nativas para representar o seu cluster De Serviço Tecido. Nas candidaturas do arrendatário no [portal Azure,](https://portal.azure.com)deverá ver duas novas entradas:

   * *ClusterName*\_Cluster
   * *Cliente ClusterName*\_

O script imprime o Modelo JSON exigido pelo modelo de Gestor de Recursos quando cria o cluster, por isso é uma boa ideia manter a janela PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicione a configuração da AD Azure para utilizar o Azure AD para acesso ao cliente
Na [secção azuredeploy.json,][template]configure o Azure AD na secção **Microsoft.ServiceFabric/clusters.** Adicione parâmetros para o ID do inquilino, id de aplicação do cluster e ID de aplicação do cliente.  

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

Adicione os valores do parâmetro no ficheiro [de parâmetros azuredeploy.parâmetros.json.][parameters] Por exemplo:

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

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Configure a recolha de diagnósticos no cluster
Quando se está a executar um cluster de Tecido de Serviço, é uma boa ideia recolher os registos de todos os nós num local central. Ter os registos num local central ajuda-o a analisar e resolver problemas no seu cluster, ou problemas nas aplicações e serviços que estão a decorrer nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão Azure Diagnostics (WAD), que envia registos para o Armazenamento Azure, e também tem a opção de enviar registos para insights de aplicação Azure ou Centros de Eventos. Também pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto de plataforma de análise, como registos do Monitor Azure ou outra solução de log-parsing.

Se estiver a seguir este tutorial, a recolha de diagnósticos já está configurada no [modelo][template].

Se tiver um cluster existente que não tenha Diagnósticos implementados, pode adicioná-lo ou atualizá-lo através do modelo de cluster. Modifique o modelo de Gestor de Recursos que é usado para criar o cluster existente ou descarregar o modelo a partir do portal. Modificar o ficheiro template.json executando as seguintes tarefas:

Adicione um novo recurso de armazenamento à secção de recursos no modelo:
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

Em seguida, adicione parâmetros para o nome da conta de armazenamento e escreva na secção de parâmetros do modelo. Substitua o nome da conta de armazenamento de texto do espaço reservado, com o nome da conta de armazenamento que deseja.

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

Em seguida, adicione a extensão **IaaSDiagnostics** à gama de extensões da propriedade **VirtualMachineProfile** de cada recurso **Microsoft.Compute/virtualMachineScaleSets** no cluster.  Se estiver a utilizar o modelo de [amostra,][template]existem três conjuntos de escala de máquina virtual (um para cada tipo de nó no cluster).

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

## <a name="configure-the-eventstore-service"></a>Configure o serviço EventStore
O serviço EventStore é uma opção de monitorização no Tecido de Serviço. A EventStore fornece uma forma de compreender o estado do seu cluster ou cargas de trabalho num dado momento. A EventStore é um serviço de tecido de serviço imponente que mantém eventos do cluster. O evento está exposto através do Service Fabric Explorer, REST e APIs. EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade do seu cluster e deve ser usado para ajudar:

* Diagnosticar problemas no desenvolvimento ou nos testes, ou onde pode estar a utilizar um pipeline de monitorização
* Confirme que as ações de gestão que está a tomar sobre o seu cluster estão a ser processadas corretamente
* Obtenha um "instantâneo" de como o Service Fabric está interagindo com uma determinada entidade



Para ativar o serviço EventStore no seu cluster, adicione o seguinte à propriedade **fabricSettings** do recurso **Microsoft.ServiceFabric/clusters:**

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

## <a name="set-up-azure-monitor-logs-for-the-cluster"></a>Configurar registos do Monitor Azure para o cluster

Os registos do Azure Monitor são a nossa recomendação para monitorizar os eventos de nível de cluster. Para configurar os registos do Monitor Azure para monitorizar o seu cluster, é necessário ter [diagnósticos habilitados para visualizar eventos de nível de cluster](#configure-diagnostics-collection-on-the-cluster).  

O espaço de trabalho precisa de ser ligado aos dados de diagnóstico provenientes do seu cluster.  Estes dados de registo são armazenados na conta de armazenamento do Nome de armazenamento do Nome de Depósitos de *Aplicações DiagnosticsStorageName,* nas tabelas WADServiceFabric*EventTable, WADWindowsEventLogsTable e WADETWEventTable.

Adicione o espaço de trabalho Azure Log Analytics e adicione a solução ao espaço de trabalho:

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

Em seguida, adicionar parâmetros
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

Adicione a extensão do agente Log Analytics a cada escala de máquina virtual definida no cluster e ligue o agente ao espaço de trabalho do Log Analytics. Isto permite recolher dados de diagnóstico sobre contentores, aplicações e monitorização de desempenho. Ao adicioná-lo como uma extensão ao recurso conjunto de conjunto de máquinas virtuais, o Azure Resource Manager garante que é instalado em todos os nós, mesmo ao escalonar o cluster.

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

Em seguida, configure a topologia de rede e implemente o cluster do Service Fabric. O modelo [azuredeploy.json][template] Resource Manager cria uma rede virtual, subnet e grupo de segurança de rede para o Tecido de Serviço. O modelo também implementa um cluster com a segurança do certificado ativada. Para os clusters de produção, utilize um certificado de uma autoridade de certificados como certificado de cluster. Um certificado autoassinado pode ser utilizado para proteger clusters de teste.

O modelo neste artigo implementa um cluster que utiliza a impressão digital do certificado para identificar o certificado de cluster. Não há dois certificados que podem ter a mesma impressão digital, o que dificulta a gestão de certificados. Trocar um cluster implantado de impressões digitais de certificado para denominações comuns simplifica a gestão de certificados. Para aprender a atualizar o cluster para utilizar nomes comuns de certificado para gestão de certificados, leia [alterar o cluster para a gestão de nomes comuns](service-fabric-cluster-change-cert-thumbprint-to-cn.md)de certificados .

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Criar um cluster utilizando um certificado existente

O seguinte script utiliza o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster em Azure. O cmdlet cria um novo cofre chave em Azure e faz upload do seu certificado.

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

### <a name="create-a-cluster-by-using-a-new-self-signed-certificate"></a>Criar um cluster usando um novo certificado auto-assinado

O seguinte script utiliza o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster em Azure. O cmdlet cria um novo cofre chave em Azure, adiciona um novo certificado auto-assinado ao cofre chave, e descarrega o ficheiro de certificado localmente.

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

Ligue-se ao cluster utilizando o módulo PowerShell de tecido de serviço instalado com o SDK de tecido de serviço.  Primeiro, instale o certificado no arquivo Pessoal (Meu) do utilizador atual no seu computador. Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Está pronto para se ligar ao seu aglomerado seguro.

O módulo **Service Fabric** do PowerShell fornece muitos cmdlets para gerir clusters, aplicações e serviços do Service Fabric. Utilize o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para ligar ao cluster seguro. Os detalhes do thumbprint SHA-1 do certificado e ponto final de ligação encontram-se nos resultados do passo anterior.

Se configurar previamente a autenticação do cliente Azure AD, execute o seguinte comando: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Se não configurar a autenticação do cliente Azure AD, execute o seguinte comando:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se está ligado e se o cluster está saudável utilizando o cmdlet [Get-ServiceFabricClusterHealth.](/powershell/module/servicefabric/get-servicefabricclusterhealth)

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar recursos

Os outros artigos desta série tutorial usam o cluster que criaste. Se não passar imediatamente para o artigo seguinte, poderá [eliminar o cluster](service-fabric-cluster-delete.md) para evitar incorrer em custos.

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte tutorial para aprender a escalar o seu cluster.

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configuração Azure Ative Diretório autenticação
> * Configure a recolha de diagnósticos
> * Configurar o serviço EventStore
> * Configurar registos do Monitor Azure
> * Criar um cluster do Service Fabric seguro no Azure PowerShell
> * Proteger o cluster com um certificado X.509
> * Ligar ao cluster com o PowerShell
> * Remover um cluster

Em seguida, avance para o seguinte tutorial para aprender a monitorizar o seu cluster.
> [!div class="nextstepaction"]
> [Monitorize um Cluster](service-fabric-tutorial-monitor-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
