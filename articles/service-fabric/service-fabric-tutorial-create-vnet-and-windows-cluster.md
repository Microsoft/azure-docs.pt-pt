---
title: Criar um cluster de tecido de serviço executando janelas em Azure
description: Neste tutorial, aprende-se a implantar um cluster de Tecido de Serviço do Windows num grupo de rede virtual e de segurança de rede Azure utilizando o PowerShell.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a7390858e55a456ec5fb2f851be1a7443be97082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86245063"
---
# <a name="tutorial-deploy-a-service-fabric-cluster-running-windows-into-an-azure-virtual-network"></a>Tutorial: Implementar um cluster de tecido de serviço que executa o Windows numa rede virtual Azure

Este tutorial é a primeira parte de uma série. Aprende a implantar um cluster de tecido de serviço Azure que executa o Windows num grupo de segurança de rede e [rede](../virtual-network/virtual-network-vnet-plan-design-arm.md) [Azure,](../virtual-network/virtual-networks-overview.md) utilizando o PowerShell e um modelo. Quando terminar, tem um cluster a correr na nuvem para o qual pode implementar aplicações. Para criar um cluster Linux que utilize o CLI Azure, consulte [criar um cluster Linux seguro em Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Este tutorial descreve um cenário de produção. Se pretender criar um cluster menor para efeitos de teste, consulte [criar um cluster de testes](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configuração Autenticação do Diretório Ativo Azure
> * Configurar a recolha de diagnósticos
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

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale o [módulo SDK e PowerShell do tecido de serviço](service-fabric-get-started.md).
* Instalar [a Azure PowerShell](/powershell/azure/install-az-ps).
* Reveja os conceitos-chave dos [clusters Azure.](service-fabric-azure-clusters-overview.md)
* [Planeie e prepare-se](service-fabric-cluster-azure-deployment-preparation.md) para uma implantação de clusters de produção.

Os seguintes procedimentos criam um cluster de tecido de serviço de sete nós. Utilize a [Calculadora de Preços Azure](https://azure.microsoft.com/pricing/calculator/) para calcular o custo incorrido pela execução de um cluster de tecido de serviço em Azure.

## <a name="download-and-explore-the-template"></a>Transferir e explorar o modelo

Descarregue os seguintes ficheiros de modelo do Azure Resource Manager:

* [azuredeploy.js][template]
* [azuredeploy.parameters.json][parameters]

Este modelo implementa um cluster seguro de sete máquinas virtuais e três tipos de nós numa rede virtual e num grupo de segurança de rede.  Outros modelos de exemplo podem ser encontrados no [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). O [azuredeploy.jsem][template] que mobiliza uma série de recursos, incluindo os seguintes.

### <a name="service-fabric-cluster"></a>Cluster do Service Fabric

No recurso **Microsoft.ServiceFabric/clusters**, é configurado um cluster Windows com as seguintes características:

* Três tipos de nó.
* Cinco nós no tipo de nó primário (configurável nos parâmetros do modelo) e um nó em cada um dos outros dois tipos de nós.
* OS: Centro de dados do Windows Server 2016 com contentores (configuráveis nos parâmetros do modelo).
* Certificado protegido (configurável nos parâmetros do modelo).
* [O procuração invertido](service-fabric-reverseproxy.md) está ativado.
* [O serviço DNS](service-fabric-dnsservice.md) está ativado.
* [Nível](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) de durabilidade do Bronze (configurável nos parâmetros do modelo).
* [Nível](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) de fiabilidade de Prata (configurável nos parâmetros do modelo).
* Ponto final de ligação do cliente: 19000 (configurável nos parâmetros do modelo).
* Ponto final http gateway: 19080 (configurável nos parâmetros do modelo).

### <a name="azure-load-balancer"></a>Balanceador de Carga do Azure

No recurso **Microsoft.Network/loadBalancers,** um equilibrador de carga é configurado. As sondas e regras são criadas para as seguintes portas:

* Ponto final de ligação ao cliente: 19000
* ponto final de gateway HTTP: 19080
* Porta de aplicação: 80
* Porta de aplicação: 443
* Proxy inverso do Service Fabric: 19081

Se forem necessárias outras portas de aplicação, terá de ajustar o recurso **Microsoft.Network/loadBalancers** e o recurso **Microsoft.Network/networkSecurityGroups** para permitir o tráfego.

### <a name="virtual-network-subnet-and-network-security-group"></a>Rede virtual, sub-rede e o grupo de segurança de rede

Os nomes da rede virtual, da sub-rede e do grupo de segurança de rede são declarados nos parâmetros do modelo. Os espaços de endereços da rede virtual e da sub-rede também são declarados nos parâmetros do modelo e configurados no recurso **Microsoft.Network/virtualNetworks**:

* Espaço de endereço de rede virtual: 172.16.0.0/20
* espaço de endereços de sub-rede do Service Fabric: 172.16.2.0/23

As seguintes regras de tráfego de entrada estão ativadas no recurso **Microsoft.Network/networkSecurityGroups**. Pode alterar os valores das portas, alterando as variáveis no modelo.

* ClientConnectionEndpoint (TCP): 19000
* HttpGatewayEndpoint (HTTP/TCP): 19080
* SMB: 445
* Comunicação interna: 1025, 1026, 1027
* Gama portuária efémera: 49152 a 65534 (precisa de um mínimo de 256 portos).
* Portas para utilização de aplicações: 80 e 443
* Gama portuária de aplicação: 49152 a 65534 (utilizada para o serviço de comunicação de serviço. Outras portas não estão abertas no equilibrador de carga).
* Bloquear todas as outras portas

Se forem necessárias outras portas de aplicação, terá de ajustar o recurso **Microsoft.Network/loadBalancers** e o recurso **Microsoft.Network/networkSecurityGroups** para permitir o tráfego.

### <a name="windows-defender"></a>Windows Defender
Por predefinição, o [programa antivírus do Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) encontra-se instalado e funcional no Windows Server 2016. A interface do utilizador é instalada por padrão em alguns SKUs, mas não é necessária. Para cada conjunto de escala tipo de nó/VM declarado no modelo, a [extensão antimalware Azure VM](../virtual-machines/extensions/iaas-antimalware-windows.md) é utilizada para excluir os diretórios e processos do Tecido de Serviço:

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

O ficheiro de parâmetro [azuredeploy.parameters.json][parameters] declara vários valores utilizados para implementar o cluster e os recursos associados. Seguem-se os parâmetros a modificar para a sua implantação:

**Parâmetro** | **Valor de exemplo** | **Notas** 
|---|---|---|
|adminUserName|vmadmin| O nome de utilizador administrador para as VMs do cluster. [Requisitos de nome de utilizador para VM](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). |
|adminPassword|Password#1234| A palavra-passe de utilizador administrador para as VMs do cluster. [Requisitos de senha para VM](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
|clusterName|mysfcluster123| O nome do cluster. Pode conter apenas letras e números. O comprimento pode ter entre 3 e 23 carateres.|
|localização|southcentralus| A localização do cluster. |
|certificateThumbprint|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor do thumbprint SHA-1 do certificado. Por exemplo, “6190390162C988701DB5676EB81083EA608DCCF3”.</p> |
|certificateUrlValue|| <p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado. </p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o URL do certificado. Por exemplo, "https: \/ /mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>O valor deve estar vazio, se criar um certificado autoassinado ou fornecer um ficheiro de certificado.</p><p>Para utilizar um certificado existente carregado anteriormente para um cofre de chaves, preencha o valor no cofre de origem. Por exemplo, "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurar a autenticação do cliente do Azure Ative Directory
Para os clusters de tecidos de serviço implantados numa rede pública hospedada no Azure, a recomendação para a autenticação mútua cliente-nó é:
* Utilize o Azure Ative Directory para a identidade do cliente.
* Utilize um certificado para a identidade do servidor e encriptação TLS de comunicação HTTP.

A criação do Azure Ative Directory (Azure AD) para autenticar os clientes para um cluster de Tecidos de Serviço deve ser feita antes [de criar o cluster](#createvaultandcert). A Azure AD permite que as organizações (conhecidas como inquilinos) gerem o acesso do utilizador às aplicações. 

Um cluster de tecido de serviço oferece vários pontos de entrada para a sua funcionalidade de gestão, incluindo o [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) e o Visual [Studio.](service-fabric-manage-application-in-visual-studio.md) Como resultado, cria duas aplicações AD Azure para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa.  Após a criação das aplicações, atribui aos utilizadores funções apenas de leitura e administração.

> [!NOTE]
> Deve completar os seguintes passos antes de criar o cluster. Como os scripts esperam nomes de clusters e pontos finais, os valores devem ser planeados e não valores que já criou.

Neste artigo, assumimos que já criou um inquilino. Se não o fez, comece por ler [Como obter um inquilino do Azure Ative Directory.](../active-directory/develop/quickstart-create-new-tenant.md)

Para simplificar os passos envolvidos na configuração do Azure AD com um cluster de Tecido de Serviço, criámos um conjunto de scripts Windows PowerShell. [Descarregue os scripts](https://github.com/Azure-Samples/service-fabric-aad-helpers) para o seu computador.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Criar aplicações AD AZure e atribuir utilizadores a funções
Crie duas aplicações AD AZure para controlar o acesso ao cluster: uma aplicação web e uma aplicação nativa. Depois de ter criado as aplicações para representar o seu cluster, atribua os seus utilizadores às [funções suportadas pela Service Fabric](service-fabric-cluster-security-roles.md): apenas para leitura e administração.

Executar `SetupApplications.ps1` , e fornecer o ID do inquilino, nome de cluster e URL de resposta à aplicação web como parâmetros. Especifique nomes de utilizador e palavras-passe para os utilizadores. Por exemplo:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysfcluster123' -WebApplicationReplyUrl 'https://mysfcluster123.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Para as nuvens nacionais (por exemplo, Governo Azure, Azure China, Azure Alemanha), especifique o `-Location` parâmetro.

Você pode encontrar o seu *TenantId,* ou iD de diretório, no [portal Azure](https://portal.azure.com). Selecione **Azure Ative Directory**  >  **Properties** e copie o valor de ID do **Diretório.**

*ClusterName* é usado para pré-fixar as aplicações AD Azure que são criadas pelo script. Não precisa de corresponder ao nome do cluster. Só facilita o mapear artefactos AD Azure para o cluster de Tecido de Serviço em uso.

*WebApplicationReplyUrl* é o ponto final padrão que o Azure AD retorna aos seus utilizadores depois de terminarem de iniciar sessão. Desaponte este ponto final como o ponto final do Explorador de Tecidos de Serviço para o seu cluster, que por padrão é:

https:// cluster_domain &lt; &gt; :19080/Explorer

Foi solicitado que assine uma conta que tenha privilégios administrativos para o inquilino da AD Azure. Depois de iniciar sposição, o script cria aplicações web e nativas para representar o seu cluster de Tecido de Serviço. Nas candidaturas do arrendatário no [portal Azure,](https://portal.azure.com)deverá ver duas novas entradas:

   * *ClusterName* \_ Cluster
   * *ClusterName* \_ Cliente

O script imprime o JSON exigido pelo modelo de Gestor de Recursos quando cria o cluster, por isso é uma boa ideia manter a janela PowerShell aberta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Adicione a configuração AD AZure para usar Azure AD para acesso ao cliente
Na [azuredeploy.jsem][template], configurar Azure AD na secção **Microsoft.ServiceFabric/clusters.** Adicione parâmetros para o ID do inquilino, ID de aplicação de cluster e ID de aplicação do cliente.  

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

Adicione os valores dos parâmetros no [azuredeploy.parameters.jsno][parameters] ficheiro de parâmetros. Por exemplo:

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

## <a name="configure-diagnostics-collection-on-the-cluster"></a>Configurar a recolha de diagnósticos no cluster
Quando você está executando um cluster de tecido de serviço, é uma boa ideia recolher os registos de todos os nós em um local central. Ter os registos numa localização central ajuda-o a analisar e resolver problemas no seu cluster, ou problemas nas aplicações e serviços que estão a decorrer nesse cluster.

Uma forma de carregar e recolher registos é utilizar a extensão Azure Diagnostics (WAD), que faz o upload de registos para o Azure Storage, e também tem a opção de enviar registos para Azure Application Insights ou Event Hubs. Também pode utilizar um processo externo para ler os eventos a partir do armazenamento e colocá-los num produto da plataforma de análise, como registos Azure Monitor ou outra solução de registo.

Se estiver a seguir este tutorial, a recolha de diagnósticos já está configurada no [modelo.][template]

Se tiver um cluster existente que não tenha Diagnósticos implementados, pode adicioná-lo ou atualizá-lo através do modelo de cluster. Modifique o modelo de Gestor de Recursos que é usado para criar o cluster existente ou descarregar o modelo a partir do portal. Modificar a template.jsem ficheiro executando as seguintes tarefas:

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

Em seguida, adicione parâmetros para o nome da conta de armazenamento e escreva para a secção de parâmetros do modelo. Substitua o nome da conta de armazenamento de texto do espaço reservado aqui pelo nome da conta de armazenamento que você gostaria.

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

Em seguida, adicione a extensão **iaaSDiagnostics** à gama de extensões da propriedade **VirtualMachineProfile** de cada recurso **Microsoft.Compute/virtualMachineScaleSets** no cluster.  Se estiver a utilizar o modelo de [amostra,][template]existem três conjuntos de balanças de máquinas virtuais (um para cada tipo de nó no cluster).

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
O serviço EventStore é uma opção de monitorização em Tecido de Serviço. A EventStore fornece uma forma de entender o estado do seu cluster ou cargas de trabalho num dado momento. O EventStore é um serviço stateful Service Fabric que mantém eventos do cluster. O evento é exposto através do Service Fabric Explorer, REST e APIs. O EventStore consulta o cluster diretamente para obter dados de diagnóstico em qualquer entidade do seu cluster e deve ser usado para ajudar:

* Diagnosticar problemas no desenvolvimento ou teste, ou onde possa estar a usar um gasoduto de monitorização
* Confirme que as ações de gestão que está a tomar no seu cluster estão a ser processadas corretamente
* Obtenha uma "foto" de como o Service Fabric está interagindo com uma determinada entidade



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

Os registos do Azure Monitor são a nossa recomendação para monitorizar eventos de nível de cluster. Para configurar os registos do Azure Monitor para monitorizar o seu cluster, é necessário ter [diagnósticos habilitados para visualizar eventos ao nível do cluster](#configure-diagnostics-collection-on-the-cluster).  

O espaço de trabalho precisa de ser ligado aos dados de diagnóstico provenientes do seu cluster.  Estes dados de registo são armazenados na conta de armazenamento *DediagnosticsticshácticaStorageAccountName,* nas tabelas WADServiceFabric*EventTable, WADWindowsEventLogsTable e WADETWEventTable.

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

Adicione a extensão do agente Log Analytics a cada escala de máquina virtual definida no cluster e ligue o agente ao espaço de trabalho Do Log Analytics. Isto permite recolher dados de diagnóstico sobre recipientes, aplicações e monitorização de desempenho. Ao adicioná-lo como uma extensão ao recurso de conjunto de escala de máquina virtual, o Azure Resource Manager garante que ele é instalado em todos os nós, mesmo quando escala o cluster.

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

Em seguida, configure a topologia de rede e implemente o cluster do Service Fabric. O [azuredeploy.jsno][template] modelo de Gestor de Recursos cria um grupo virtual de rede, sub-rede e segurança de rede para o Tecido de Serviço. O modelo também implementa um cluster com a segurança do certificado ativada. Para os agrupamentos de produção, utilize um certificado de uma autoridade de certificados como certificado de agrupamento. Um certificado autoassinado pode ser utilizado para proteger clusters de teste.

O modelo deste artigo implementa um cluster que utiliza a impressão digital do certificado para identificar o certificado de cluster. Não há dois certificados que possam ter a mesma impressão digital, o que dificulta a gestão de certificados. Mudar um cluster implantado de impressões digitais de certificado para certificado nomes comuns simplifica a gestão de certificados. Para aprender a atualizar o cluster para utilizar os nomes comuns do certificado para a gestão de certificados, leia [change cluster para certificação gestão de nome comum](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-by-using-an-existing-certificate"></a>Criar um cluster usando um certificado existente

O seguinte script utiliza o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster em Azure. O cmdlet cria um novo cofre chave em Azure e envia o seu certificado.

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

O seguinte script utiliza o cmdlet [New-AzServiceFabricCluster](/powershell/module/az.servicefabric/New-azServiceFabricCluster) e um modelo para implantar um novo cluster em Azure. O cmdlet cria um novo cofre chave em Azure, adiciona um novo certificado auto-assinado ao cofre de chaves, e descarrega o arquivo de certificado localmente.

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

Ligue-se ao cluster utilizando o módulo PowerShell do tecido de serviço instalado com o Service Fabric SDK.  Primeiro, instale o certificado no arquivo Pessoal (Meu) do utilizador atual no seu computador. Execute o seguinte comando do PowerShell:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Está agora pronto para se ligar ao seu aglomerado seguro.

O módulo **Service Fabric** do PowerShell fornece muitos cmdlets para gerir clusters, aplicações e serviços do Service Fabric. Utilize o cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) para ligar ao cluster seguro. Os detalhes do thumbprint SHA-1 do certificado e ponto final de ligação encontram-se nos resultados do passo anterior.

Se configurar previamente a autenticação do cliente Azure AD, executar o seguinte comando: 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Se não criou a autenticação do cliente Azure AD, execute o seguinte comando:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Verifique se está conectado e se o cluster está saudável usando o [cmdlet Get-ServiceFabricClusterHealth.](/powershell/module/servicefabric/get-servicefabricclusterhealth)

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Limpar os recursos

Os outros artigos desta série tutorial usam o cluster que criaste. Se não passar imediatamente para o artigo seguinte, poderá [eliminar o cluster](./service-fabric-tutorial-delete-cluster.md) para evitar incorrer em custos.

## <a name="next-steps"></a>Passos seguintes

Avance para o seguinte tutorial para aprender a escalar o seu cluster.

> [!div class="checklist"]
> * Criar uma VNET no Azure com o PowerShell
> * Criar um cofre de chaves e carregar um certificado
> * Configuração Autenticação do Diretório Ativo Azure
> * Configurar a recolha de diagnósticos
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
