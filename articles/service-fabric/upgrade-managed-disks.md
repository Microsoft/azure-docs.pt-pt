---
title: Atualizar os nosdes de cluster para usar discos geridos a Azure
description: Eis como atualizar um cluster de Tecido de Serviço existente para utilizar discos geridos Azure com pouco ou nenhum tempo de inatividade do seu cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 152bdaea121e65de8332fcde8543b8158ff11714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88717528"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Atualizar os nosdes de cluster para usar discos geridos a Azure

[Os discos geridos Azure](../virtual-machines/managed-disks-overview.md) são a oferta recomendada de armazenamento de discos para uso com máquinas virtuais Azure para armazenamento persistente de dados. Pode melhorar a resiliência das cargas de trabalho do seu Tecido de Serviço, atualizando os conjuntos de escala de máquinas virtuais que estão na base dos seus tipos de nós para utilizar discos geridos. Eis como atualizar um cluster de Tecido de Serviço existente para utilizar discos geridos Azure com pouco ou nenhum tempo de inatividade do seu cluster.

A estratégia geral para atualizar um nó de cluster de tecido de serviço para utilizar discos geridos é:

1. Desloque um conjunto de escala de máquina virtual de outra forma duplicado desse tipo de nó, mas com o objeto [geridoDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) adicionado à `osDisk` secção do modelo de implantação da balança de máquina virtual. O conjunto de nova escala deve ligar-se ao mesmo balanceador de carga/IP que o original, para que os seus clientes não experimentem uma paragem de serviço durante a migração.

2. Uma vez que os conjuntos de escala original e atualizados estejam a funcionar lado a lado, desative os casos originais do nó um de cada vez para que os serviços do sistema (ou réplicas de serviços estatais) migram para o conjunto de escala nova.

3. Verifique se o cluster e os novos nós estão saudáveis e, em seguida, remova o conjunto de escala original e o estado do nó para os nós eliminados.

Este artigo irá acompanhá-lo através dos passos de atualização do tipo de nó primário de um conjunto de exemplo para utilizar discos geridos, evitando ao mesmo tempo qualquer tempo de inatividade do cluster (ver nota abaixo). O estado inicial do agrupamento de ensaios de exemplo consiste num tipo de nó de [durabilidade](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)prateada, apoiado por um conjunto de escala única com cinco nós.

> [!NOTE]
> As limitações de um balanceador de carga Basic SKU impedem a adição de uma escala adicional definida. Recomendamos a utilização do balanceador de carga Standard SKU. Para mais [informações, consulte uma comparação dos dois SKUs.](/azure/load-balancer/skus)

> [!CAUTION]
> Só irá experimentar uma paragem com este procedimento se tiver dependências do cluster DNS (como ao aceder ao [Service Fabric Explorer).](service-fabric-visualizing-your-cluster.md) As [melhores práticas arquitetónicas para serviços front-end](/azure/architecture/microservices/design/gateway) é ter algum tipo de [equilibrador](/azure/architecture/guide/technology-choices/load-balancing-overview) de carga na frente dos seus tipos de nó para tornar possível a troca de nó sem uma paragem.

Aqui estão os [modelos e cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) para O Gestor de Recursos Azure que usaremos para completar o cenário de upgrade. As alterações do modelo serão explicadas na [Implementação de uma escala atualizada definida para o tipo de nó primário](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  abaixo.

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Vamos configurar o cluster inicial de testes de tecido de serviço. Em primeiro lugar, [descarregue](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) os modelos de amostra do Azure Resource Manager que usaremos para completar este cenário.

Em seguida, inscreva-se na sua conta Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Os seguintes comandos irão guiá-lo através da geração de um novo certificado auto-assinado e da implantação do cluster de teste. Se já tiver um certificado que gostaria de utilizar, salte para [utilizar um certificado existente para implantar o cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gere um certificado auto-assinado e implantar o cluster

Em primeiro lugar, atribua as variáveis necessárias para a implantação do cluster de tecido de serviço. Ajuste os valores `resourceGroupName`  `certSubjectName` para, `parameterFilePath` e para a sua conta e ambiente `templateFilePath` específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Certifique-se de que existe a `certOutputFolder` localização na sua máquina local antes de executar o comando para implantar um novo cluster de Tecido de Serviço.

Em seguida, abra o [*Initial-1NodeType-UnmanagedDisks.parameters.jsno*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) ficheiro e ajuste os valores para `clusterName` e para corresponder aos `dnsName` valores dinâmicos que definiu no PowerShell e guarde as suas alterações.

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

A operação devolverá a impressão digital do certificado, que utilizará para [ligar ao novo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificar o seu estado de saúde. (Saltar a seguinte secção, que é uma abordagem alternativa à implantação do cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Utilize um certificado existente para implantar o cluster

Também pode utilizar um certificado Azure Key Vault existente para implantar o cluster de teste. Para isso, terá de [obter referências ao seu Cofre-Chave](#obtain-your-key-vault-references) e à impressão digital de certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Abra a [*Initial-1NodeType-UnmanagedDisks.parameters.jsem*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) arquivo e altere os valores para e para `clusterName` algo `dnsName` único.

Por fim, designe um nome de grupo de recursos para o cluster e desenhe os `templateFilePath` `parameterFilePath` e locais dos seus *ficheiros Inicial-1NodeType-UnmanagedDisks:*

> [!NOTE]
> O grupo de recursos designado já deve existir e estar localizado na mesma região que o seu Cofre-Chave.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Por último, executar o seguinte comando para implantar o cluster de teste inicial:

```powershell
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

Ligue-se ao cluster e certifique-se de que todos os seus cinco nós são saudáveis (substituindo as `clusterName` `thumb` variáveis e variáveis para o seu cluster):

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Implementar um conjunto de escala atualizado para o tipo de nó primário

Para atualizar, ou *verticalmente,* um tipo de nó, precisamos de implantar uma cópia do conjunto de balanças virtuais do tipo nó, que é idêntico ao conjunto de escala original (incluindo referência ao mesmo `nodeTypeRef` , e ) `subnet` `loadBalancerBackendAddressPools` exceto que inclui a atualização/alterações desejadas e a sua própria sub-rede separada e piscina de endereços NAT de entrada. Como estamos a atualizar um tipo de nó primário, o conjunto de nova escala será marcado como primário ( `isPrimary: true` ), tal como o conjunto de escala original. (Para atualizações não primárias do tipo de nó, simplesmente omita isto.)

Por conveniência, as alterações necessárias já foram feitas para si nos [ficheiros](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) e *modelos de parâmetros upgrade-1NodeType-2ScaleSets-ManagedDisks.* [parameters](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

As seguintes secções explicarão as alterações do modelo em detalhe. Se preferir, pode ignorar a explicação e continuar para [o próximo passo do procedimento de atualização](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Atualize o modelo de cluster com o conjunto de escala atualizado

Aqui estão as modificações de secção por secção do modelo original de implantação do cluster para adicionar um conjunto de escala atualizado para o tipo de nó primário.

#### <a name="parameters"></a>Parâmetros

Adicione parâmetros para o nome, contagem e tamanho do conjunto de nova escala. Note que `vmNodeType1Name` é exclusivo do conjunto de escala nova, enquanto os valores de contagem e tamanho são idênticos ao conjunto de escala original.

**Arquivo de modelo**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Arquivo de parâmetros**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Variáveis

Na secção de modelo de `variables` implantação, adicione uma entrada para o conjunto de endereços NAT de entrada do conjunto de nova escala.

**Arquivo de modelo**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Recursos

Na secção *de recursos* do modelo de implantação, adicione o novo conjunto de escala de máquina virtual, tendo em conta estas coisas:

* O conjunto de nova escala refere o mesmo tipo de nó que o original:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* O conjunto de escala nova refere o mesmo endereço de backend e sub-rede do balançador de carga (mas utiliza um pool NAT de entrada de balançador de carga diferente):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Tal como o conjunto de escala original, o conjunto de escala nova é marcado como o tipo de nó primário. (Ao atualizar os tipos de nó não primários, omita esta alteração.)

    ```json
    "isPrimary": true,
    ```

* Ao contrário do conjunto de escala original, o conjunto de escala nova é atualizado para utilizar discos geridos.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Depois de implementar todas as alterações nos ficheiros do seu modelo e parâmetros, dirija-se à secção seguinte para adquirir as referências do Key Vault e implemente as atualizações para o seu cluster.

### <a name="obtain-your-key-vault-references"></a>Obtenha as suas referências key Vault

Para implementar a configuração atualizada, primeiro obterá várias referências ao seu certificado de cluster armazenado no seu Cofre de Chaves. A maneira mais fácil de encontrar estes valores é através do portal Azure. Precisará:

* **O URL do Cofre chave do seu certificado de cluster.** A partir do seu Cofre chave no portal Azure, selecione **Certificados**  >  *O seu certificado*  >  **identificador secreto**pretendido:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A impressão digital do seu certificado de agrupamento.** (Provavelmente já o tem se estiver [ligado ao cluster inicial](#connect-to-the-new-cluster-and-check-health-status) para verificar o seu estado de saúde.) A partir da mesma lâmina de certificado **(Certificados**  >  *O certificado pretendido)* no portal Azure, cópia **X.509 SHA-1 Thumbprint (em hexáxia)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A identificação de recursos do seu Cofre de Chaves.** A partir do seu Cofre chave no portal Azure, selecione **O**  >  **ID de Recursos de**Propriedades :

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado

Ajuste o `parameterFilePath` comando `templateFilePath` e, se necessário, e, em seguida, executar o seguinte comando:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Quando a colocação terminar, verifique novamente a saúde do cluster e certifique-se de que os dez nós (cinco no original e cinco no conjunto de nova escala) estão saudáveis.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrar os nó de sementes para o conjunto de nova escala

Estamos prontos para começar a desativar os nós do conjunto de escala original. À medida que estes nós ficam desativados, os serviços de sistema e os nós de sementes migram para os VMs da nova escala definida porque também é marcado como o tipo de nó primário.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Utilize o Service Fabric Explorer para monitorizar a migração dos nós de sementes para o novo conjunto de escalas e a progressão dos nós na escala original definida de *Desativando* para o estado *de Desativação.*

![Explorador de tecido de serviço mostrando o estado dos nosdes desativados](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Pode levar algum tempo para completar a operação de desativação em todos os nós do conjunto de escala original. Para garantir a consistência dos dados, apenas um nó de sementes pode mudar de cada vez. Cada alteração do nó de sementes requer uma atualização do cluster; assim, a substituição de um nó de sementes requer duas atualizações de cluster (uma para aadição e remoção do nó). A atualização dos cinco nós de sementes neste cenário de amostra resultará em dez atualizações de cluster.

## <a name="remove-the-original-scale-set"></a>Remova o conjunto de escala original

Uma vez concluída a operação de desativação, retire o conjunto de balanças.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

No Service Fabric Explorer, os nós removidos (e, portanto, o *Estado de Saúde*do Cluster) aparecerão agora no estado *de Erro.*

![Explorador de tecido de serviço mostrando nódoas desativadas no estado de erro](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Retire os nós obsoletos do cluster de tecido de serviço para restaurar o Estado de Saúde do Cluster para *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Explorador de tecido de serviço com nódoas para baixo no estado de erro removido](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Passos seguintes

Nesta passagem, aprendeu a atualizar os conjuntos de escala de máquina virtual de um cluster de Tecido de Serviço para utilizar discos geridos, evitando falhas de serviço durante o processo. Para obter mais informações sobre temas relacionados, confira os seguintes recursos.

Aprenda a:

* [Aumentar verticalmente um nó do tipo primário do cluster do Service Fabric](service-fabric-scale-up-primary-node-type.md)

* [Converter um modelo de conjunto de escala para usar discos geridos](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Remover um tipo de nó de tecido de serviço](service-fabric-how-to-remove-node-type.md)

Veja também:

* [Amostra: Atualizar os nosdes de cluster para utilizar discos geridos a Azure](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Considerações de escala vertical](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
