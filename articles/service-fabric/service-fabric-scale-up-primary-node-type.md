---
title: Dimensione um tipo de nó primário de tecido de serviço Azure
description: Dimensione verticalmente o seu cluster de Tecido de Serviço adicionando um novo tipo de nó e removendo o anterior.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251186"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Aumentar verticalmente um nó do tipo primário do cluster do Service Fabric

Este artigo descreve como escalar um tipo de nó primário de cluster de tecido de serviço com tempo mínimo de inatividade. A estratégia geral para a atualização de um tipo de nó de cluster de tecido de serviço é:

1. Adicione um novo tipo de nó ao seu cluster de Tecido de Serviço, apoiado pelo conjunto de escala de máquina virtual atualizado (ou modificado) SKU e configuração. Este passo envolve também a criação de um novo balanceador de carga, sub-rede e IP público para o conjunto de escalas.

1. Uma vez que os conjuntos de escala original e atualizados estejam a funcionar lado a lado, desative os casos originais do nó um de cada vez para que os serviços do sistema (ou réplicas de serviços estatais) migram para o conjunto de escala nova.

1. Verifique se o cluster e os novos nós estão saudáveis, em seguida, remova o conjunto de escala original (e recursos relacionados) e o estado do nó para os nós eliminados.

O seguinte irá acompanhá-lo através do processo de atualização do tamanho vm e sistema operativo de VMs do tipo de nó primário de um cluster de amostra com [durabilidade prateada,](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)apoiado por um conjunto de escala única com cinco nós. Vamos atualizar o tipo de nó primário:

- Do tamanho de VM *Standard_D2_V2* a *Standard D4_V2,* e
- Do sistema operativo VM *Windows Server 2016 Datacenter com Contentores* ao *Windows Server 2019 Datacenter com Contentores*.

> [!WARNING]
> Antes de tentar este procedimento num cluster de produção, recomendamos que estude os modelos da amostra e verifique o processo contra um cluster de teste. O cluster também pode estar indisponível por um curto período de tempo.
>
> Não tente um procedimento de escala do tipo de nó primário se o estado do cluster não for saudável, pois isso só irá desestabilizar ainda mais o cluster.

Aqui estão os modelos de implementação passo a passo da Azure que usaremos para completar este cenário de upgrade da amostra: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Vamos configurar o cluster inicial de testes de tecido de serviço. Em primeiro lugar, [descarregue](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) os modelos de amostra do Azure Resource Manager que usaremos para completar este cenário.

Em seguida, inscreva-se na sua conta Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Em seguida, abra a [*parameters.jsem*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) arquivo e atualize o valor para `clusterName` algo único (dentro do Azure).

Os seguintes comandos irão guiá-lo através da geração de um novo certificado auto-assinado e da implantação do cluster de teste. Se já tiver um certificado que gostaria de utilizar, salte para [utilizar um certificado existente para implantar o cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gere um certificado auto-assinado e implantar o cluster

Em primeiro lugar, atribua as variáveis necessárias para a implantação do cluster de tecido de serviço. Ajuste os valores `resourceGroupName`  `certSubjectName` para, `parameterFilePath` e para a sua conta e ambiente `templateFilePath` específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Certifique-se de que existe a `certOutputFolder` localização na sua máquina local antes de executar o comando para implantar um novo cluster de Tecido de Serviço.

Em seguida, implante o cluster de teste de tecido de serviço:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Uma vez concluída a implantação, localize o ficheiro *.pfx* `$certPfx` () na sua máquina local e importe-o para a sua loja de certificados:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação devolverá a impressão digital do certificado, que agora pode utilizar para [ligar ao novo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificar o seu estado de saúde. (Saltar a seguinte secção, que é uma abordagem alternativa à implantação do cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Utilize um certificado existente para implantar o cluster

Alternadamente, pode utilizar um certificado Azure Key Vault existente para implantar o cluster de teste. Para isso, terá de [obter referências ao seu Cofre-Chave](#obtain-your-key-vault-references) e à impressão digital de certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Em seguida, designe um nome de grupo de recursos para o cluster e desaprote o `templateFilePath` e `parameterFilePath` locais:

> [!NOTE]
> O grupo de recursos designado já deve existir e estar localizado na mesma região que o seu Cofre-Chave.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Por último, executar o seguinte comando para implantar o cluster de teste inicial:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Ligue-se ao novo cluster e verifique o estado de saúde

Conecte-se ao cluster e certifique-se de que todos os seus cinco nós são saudáveis (substitua as `clusterName` `thumb` variáveis e variáveis pelos seus próprios valores):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Com isso, estamos prontos para iniciar o procedimento de atualização.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Implementar um novo tipo de nó primário com conjunto de escala atualizado

Para atualizar (verticalmente) um tipo de nó, primeiro precisamos de implementar um novo tipo de nó apoiado por um novo conjunto de escala e recursos de suporte. O conjunto de nova escala será marcado como primário ( `isPrimary: true` ), tal como o conjunto de escala original (a menos que esteja a fazer uma atualização do tipo de nó não primário). Os recursos criados na secção seguinte acabarão por se tornar o novo tipo de nó primário no seu cluster, e os recursos originais do tipo nó primário serão eliminados.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Atualize o modelo de cluster com o conjunto de escala atualizado

Aqui estão as modificações de secção por secção do modelo original de implantação do cluster para adicionar um novo tipo de nó primário e recursos de suporte.

As alterações necessárias para este passo já foram feitas para si no [*Step1-AddPrimaryNodeType.jsno*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) ficheiro do modelo, e as seguintes explicarão estas alterações em detalhe. Se preferir, pode ignorar a explicação e continuar a [obter as referências do Key Vault](#obtain-your-key-vault-references) e implementar o modelo [atualizado](#deploy-the-updated-template) que adiciona um novo tipo de nó primário ao seu cluster.

> [!Note]
> Certifique-se de que utiliza nomes exclusivos do tipo de nó original, conjunto de escala, balanceador de carga, IP público e sub-rede do tipo de nó primário original, uma vez que estes recursos serão eliminados posteriormente no processo.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Criar uma nova sub-rede na rede virtual existente

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Criar um novo IP público com um domínio únicoNameLabel

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Criar um novo equilibrador de carga para o IP público

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Criar um novo conjunto de escala de máquina virtual (com VM e OS SKUs atualizados)

Nó Tipo Ref

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU da VM

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

OS SKU

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Além disso, certifique-se de que inclui quaisquer extensões adicionais necessárias para a sua carga de trabalho.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Adicione um novo tipo de nó primário ao cluster

Agora que o novo tipo de nó (vmNodeType1Name) tem o seu próprio nome, sub-rede, IP, balançador de carga e conjunto de escala, pode reutilizar todas as outras variáveis do tipo de nó original (tais `nt0applicationEndPort` `nt0applicationStartPort` como, `nt0fabricTcpGatewayPort` e):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Depois de implementar todas as alterações nos ficheiros do seu modelo e parâmetros, dirija-se à secção seguinte para adquirir as referências do Key Vault e implemente as atualizações para o seu cluster.

### <a name="obtain-your-key-vault-references"></a>Obtenha as suas referências key Vault

Para implementar a configuração atualizada, necessitará de várias referências ao certificado de cluster armazenado no seu Cofre de Chaves. A maneira mais fácil de encontrar estes valores é através do portal Azure. Precisará:

* **O URL do Cofre chave do seu certificado de cluster.** A partir do seu Cofre chave no portal Azure, selecione **Certificados**  >  *O seu certificado*  >  **identificador secreto** pretendido:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A impressão digital do seu certificado de agrupamento.** (Provavelmente já o tem se estiver [ligado ao cluster inicial](#connect-to-the-new-cluster-and-check-health-status) para verificar o seu estado de saúde.) A partir da mesma lâmina de certificado **(Certificados**  >  *O certificado pretendido)* no portal Azure, cópia **X.509 SHA-1 Thumbprint (em hexáxia)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A identificação de recursos do seu Cofre de Chaves.** A partir do seu Cofre chave no portal Azure, selecione **O**  >  **ID de Recursos de** Propriedades :

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado

Ajuste o `templateFilePath` comando necessário e executar o seguinte comando:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Quando a colocação terminar, verifique novamente a saúde do cluster e certifique-se de que todos os nós dos dois tipos de nós estão saudáveis.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrar os nó de sementes para o novo tipo de nó

Estamos agora prontos para atualizar o tipo original de nó como não primário e começar a desativar os seus nós. À medida que os nós desativam, os serviços de sistema do cluster e os nós de sementes migram para o novo conjunto de escala.

### <a name="unmark-the-original-node-type-as-primary"></a>Desmarcar o tipo de nó original como primário

Primeiro, remova a `isPrimary` designação no modelo do tipo de nó original.

```json
{
    "isPrimary": false,
}
```

Em seguida, desloque o modelo com a atualização. Isto iniciará a migração de nós de sementes para o conjunto de nova escala.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Levará algum tempo para completar a migração do nó de sementes para o novo conjunto de escala. Para garantir a consistência dos dados, apenas um nó de sementes pode mudar de cada vez. Cada alteração do nó de sementes requer uma atualização do cluster; assim, a substituição de um nó de sementes requer duas atualizações de cluster (uma para aadição e remoção do nó). A atualização dos cinco nós de sementes neste cenário de amostra resultará em dez atualizações de cluster.

Utilize o Service Fabric Explorer para monitorizar a migração dos nós de sementes para o conjunto de novas escalas. Os nós do tipo de nó original (nt0vm) devem ser *todos falsos* na coluna **Nó de Sementes,** e os do novo tipo de nó (nt1vm) serão *verdadeiros*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Desative os nóis no conjunto de escala original do tipo nó

Uma vez migrados todos os nós de sementes para o conjunto de escala nova, pode desativar os nós do conjunto de escala original.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Utilize o Explorador de Tecidos de Serviço para monitorizar a progressão dos nós na escala original definida de *Desativar* para *Deficientes.*

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Explorador de tecido de serviço mostrando o estado dos nosdes desativados":::

Para a durabilidade de Prata e Ouro, alguns nós entrarão em estado de desativação, enquanto outros podem permanecer em estado *incapacitante.* No Service Fabric Explorer, verifique o separador **Detalhes** dos nós em estado de desativação. Se mostrarem uma *verificação* de segurança pendente de tipo *garantir o Risco de Divisão* (garantindo quórum para as divisórias de serviço de infraestrutura), então é seguro continuar.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Pode proceder à paragem dos dados e à remoção dos nós presos no estado de &quot;desativação&quot; se mostrarem uma verificação de segurança pendente do tipo &quot;Garantir a SeparaçãoQuorum&quot;.":::

Se o seu cluster for a durabilidade do Bronze, aguarde que todos os nós cheguem ao estado *de Deficientes.*

### <a name="stop-data-on-the-disabled-nodes"></a>Parar os dados dos nos nosmos com deficiência

Agora pode parar os dados dos nós desativado.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Remova o tipo original de nó e cleanup os seus recursos

Estamos prontos para remover o tipo de nó original e os seus recursos associados para concluir o procedimento de escala vertical.

### <a name="remove-the-original-scale-set"></a>Remova o conjunto de escala original

Primeiro, retire o conjunto de balança de suporte do tipo nó.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Eliminar os recursos originais do IP e do balanceador de carga

Pode agora eliminar o IP original e carregar os recursos do balanceador. Neste passo também irá atualizar o nome DNS.

> [!Note]
> Este passo é opcional se já estiver a utilizar um IP público *Standard* SKU e um equilibrador de carga. Neste caso, pode ter vários conjuntos de escala/tipos de nó sob o mesmo equilibrador de carga.

Executar os seguintes comandos, modificando o `$lbname` valor conforme necessário.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Remova o estado do nó do tipo de nó original

Os nós originais do tipo nó mostrarão agora *Erro* para o seu **Estado de Saúde.** Retire o seu estado de nó do aglomerado.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer deve agora refletir apenas os cinco nós do novo tipo de nó (nt1vm), todos com valores do Estado de Saúde de *OK*. O seu Estado de Saúde do Cluster continuará a mostrar *o Erro*. Vamos remediar isso a seguir, atualizando o modelo para refletir as últimas alterações e a reimplantação.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Atualizar o modelo de implementação para refletir o novo tipo de nó primário

As alterações necessárias para este passo já foram feitas para si no [*Step3-CleanupOriginalPrimaryNodeType.jsno*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) ficheiro do modelo, e as seguintes secções explicarão estas alterações de modelo em detalhe. Se preferir, pode ignorar a explicação e continuar a [implementar o modelo atualizado](#deploy-the-finalized-template) e completar o tutorial.

#### <a name="update-the-cluster-management-endpoint"></a>Atualizar o ponto final de gestão do cluster

Atualize o cluster `managementEndpoint` no modelo de implementação para fazer referência ao novo IP (atualizando *o vmNodeType0Name* com *vmNodeType1Name).*

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Remova a referência original do tipo nódoa

Remova a referência original do tipo nó do conjunto de serviços no modelo de implementação:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Configure políticas de saúde para ignorar os erros existentes

Apenas para silver e clusters de durabilidade mais elevados, atualizar o recurso cluster no modelo e configurar políticas de saúde para ignorar a `fabric:/System` saúde da aplicação adicionando *aplicaçõesDeltaHealthPolicies* sob propriedades de recursos de cluster como indicado abaixo. A política abaixo ignorará os erros existentes, mas não permitirá novos erros de saúde.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Remova os recursos de apoio para o tipo de nó original

Remova todos os outros recursos relacionados com o tipo de nó original do modelo ARM e do ficheiro de parâmetros. Eliminar o seguinte:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Implementar o modelo finalizado

Por fim, implemente o modelo modificado do Gestor de Recursos Azure.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Este passo vai demorar um pouco, normalmente até duas horas.

A atualização alterará as definições para o *Serviço de Infraestruturas;* portanto, é necessário reiniciar um nó. Neste caso, *o ForceRestart* é ignorado. O parâmetro `upgradeReplicaSetCheckTimeout` especifica o tempo máximo que o Service Fabric espera que uma partição esteja num estado seguro, se não já em estado seguro. Uma vez que as verificações de segurança passam para todas as divisórias num nó, o Tecido de Serviço procede com a atualização desse nó. O valor do parâmetro `upgradeTimeout` pode ser reduzido para 6 horas, mas para segurança máxima devem ser utilizados 12 horas.

Uma vez concluída a implementação, verifique no portal Azure que o Estado do recurso de tecido de serviço está *pronto*. Verifique se pode chegar ao novo ponto final do Service Fabric Explorer, o **Estado de Saúde** do Cluster está *OK*, e quaisquer aplicações implementadas funcionam corretamente.

Com isso, escalou verticalmente um tipo de nó primário de aglomerado!

## <a name="next-steps"></a>Passos seguintes

* Saiba como [adicionar um nó ao aglomerado](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escalar um aglomerado Azure dentro ou fora](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) usando o fluente Azure compute SDK.
* [Escalar um aglomerado autónomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).
