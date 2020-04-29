---
title: Atualizar nós de cluster para usar discos geridos pelo Azure
description: Aqui está como atualizar um cluster de tecido de serviço existente para usar discos geridos azure com pouco ou nenhum tempo de inatividade do seu cluster.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991216"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Atualizar nós de cluster para usar discos geridos pelo Azure

Os [discos geridos pelo Azure](../virtual-machines/windows/managed-disks-overview.md) são a oferta recomendada de armazenamento de discos para utilização com máquinas virtuais Azure para armazenamento persistente de dados. Pode melhorar a resiliência das cargas de trabalho do seu Tecido de Serviço, atualizando os conjuntos de escala de máquinavirtual que estão na parte de baixo dos tipos do nó para utilizar discos geridos. Aqui está como atualizar um cluster de tecido de serviço existente para usar discos geridos azure com pouco ou nenhum tempo de inatividade do seu cluster.

A estratégia geral para a atualização de um nó de cluster de tecido de serviço para utilizar discos geridos é:

1. Desloque um conjunto de escala de máquina virtual duplicado desse tipo `osDisk` de nó, mas com o objeto Disk [gerido](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) adicionado à secção do modelo de implementação de conjunto de escala de máquina virtual. O novo conjunto de escala deve ligar-se ao mesmo balancedor/IP de carga que o original, para que os seus clientes não experimentem uma paragem de serviço durante a migração.

2. Uma vez que os conjuntos de escala original e atualizado estejam a correr lado a lado, desative as instâncias originais do nó um de cada vez para que os serviços do sistema (ou réplicas de serviços estatais) migram para o novo conjunto de escala.

3. Verifique se o cluster e os novos nós estão saudáveis, em seguida, remova o conjunto de escala original e o estado do nó para os nós apagados.

Este artigo irá acompanhá-lo através dos passos de atualização do tipo de nó primário de um cluster de exemplo para usar discos geridos, evitando ao mesmo tempo qualquer tempo de inatividade de cluster (ver nota abaixo). O estado inicial do agrupamento de testes de exemplo consiste num tipo de tipo de [durabilidade](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)prateada, apoiado por um conjunto de escala única com cinco nós.

> [!CAUTION]
> Só sentirá uma falha com este procedimento se tiver dependências do cluster DNS (como o acesso ao [Service Fabric Explorer).](service-fabric-visualizing-your-cluster.md) As melhores práticas arquitetónicas [para os serviços front-end](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) é ter algum tipo de [equilibrista](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) de carga na frente dos tipos de nó para tornar possível a troca de nó sem uma paragem.

Aqui estão os [modelos e cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) para o Gestor de Recursos Azure que usaremos para completar o cenário de upgrade. As alterações do modelo serão explicadas em Implementar um conjunto de [escala atualizado para o tipo de nó primário](#deploy-an-upgraded-scale-set-for-the-primary-node-type) abaixo.

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Vamos montar o cluster inicial de teste de tecido de serviço. Primeiro, [descarregue](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) os modelos de amostra do gestor de recursos Azure que usaremos para completar este cenário.

Em seguida, inscreva-se na sua conta Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Os seguintes comandos irão guiá-lo através da geração de um novo certificado auto-assinado e da implantação do cluster de teste. Se já tem um certificado que gostaria de usar, salte para [Usar um certificado existente para implantar o cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gerar um certificado auto-assinado e implementar o cluster

Em primeiro lugar, atribua as variáveis que necessitará para a implantação do cluster Service Fabric. Ajuste os `resourceGroupName`valores para, `certSubjectName`, `parameterFilePath`e `templateFilePath` para a sua conta e ambiente específicos:

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
> Certifique-se `certOutputFolder` de que a localização existe na sua máquina local antes de executar o comando para implantar um novo cluster de Tecido de Serviço.

Em seguida, abra o ficheiro [*Initial-1NodeType-UnmanagedDisks.parâmetros.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) e ajuste os valores para `clusterName` e `dnsName` para corresponder aos valores dinâmicos que definiu no PowerShell e guarde as suas alterações.

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

Uma vez concluída a implantação, localize o ficheiro *.pfx* ()`$certPfx`na sua máquina local e importe-o para o seu certificado:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação devolverá a impressão digital do certificado, que utilizará para [se ligar ao novo cluster](#connect-to-the-new-cluster-and-check-health-status) e verificar o seu estado de saúde. (Salte a secção seguinte, que é uma abordagem alternativa à implantação do cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Utilize um certificado existente para implantar o cluster

Também pode utilizar um certificado de cofre de chave Azure existente para implantar o cluster de teste. Para isso, terá de [obter referências ao seu Cofre](#obtain-your-key-vault-references) chave e impressão digital de certificado.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Abra o ficheiro [*Inicial-1NodeType-UnmanagedDisks.parâmetros.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) e `clusterName` `dnsName` altere os valores para e para algo único.

Por fim, designe um nome de `templateFilePath` `parameterFilePath` grupo de recursos para o cluster e detetete te as localizações e localizações dos seus *ficheiros DeDiscos Iniciais-1NodeType-Unmanaged:*

> [!NOTE]
> O grupo de recursos designado já deve existir e estar localizado na mesma região que o seu Cofre Chave.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Por fim, executar o seguinte comando para implantar o cluster de teste inicial:

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

Ligue-se ao cluster e certifique-se de que todos `clusterName` os `thumb` seus cinco nós são saudáveis (substituindo as e variáveis para o seu cluster):

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

Para atualizar, ou *escalar verticalmente,* um tipo de nó, teremos de implantar uma cópia do conjunto de escala virtual da máquina do tipo `nodeTypeRef`nó, que é idêntico ao conjunto de escala original (incluindo referência ao mesmo , `subnet`e `loadBalancerBackendAddressPools`), exceto que inclui a atualização/alterações desejada e a sua própria subnet separada e piscina de endereçona de entrada. Como estamos a atualizar um nó primário, o novo conjunto de`isPrimary: true`escala será marcado como primário , tal como o conjunto de escala original. (Para atualizações não primárias do tipo de nó, basta omiti-lo.)

Por conveniência, as alterações necessárias já foram feitas para si no [modelo](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) de *Upgrade-1NodeType-2ScaleSets-ManagedDisks* e nos ficheiros de [parâmetros.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

As seguintes secções explicarão as alterações do modelo em detalhe. Se preferir, pode ignorar a explicação e continuar até [ao próximo passo do procedimento de atualização](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Atualize o modelo de cluster com o conjunto de escala atualizado

Aqui estão as modificações secção-por-secção do modelo original de implantação do cluster para adicionar um conjunto de escala atualizado para o tipo de nó primário.

#### <a name="parameters"></a>Parâmetros

Adicione parâmetros para o nome, contagem e tamanho do novo conjunto de escala. Note `vmNodeType1Name` que é único para o novo conjunto de escala, enquanto os valores de contagem e tamanho são idênticos ao conjunto de escala original.

**Ficheiro de modelo**

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

Na secção `variables` do modelo de implantação, adicione uma entrada para o conjunto de endereços NAT de entrada do novo conjunto de escala.

**Ficheiro de modelo**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Recursos

Na secção de *recursos* do modelo de implementação, adicione o novo conjunto de escala de máquina virtual, tendo em conta estas coisas:

* O novo conjunto de escala refere o mesmo tipo de nó que o original:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* O novo conjunto de escala refere o mesmo endereço e subrede de endend do equilibrista de carga (mas utiliza um pool NAT de entrada de equilíbrio de carga diferente):

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

* Tal como o conjunto de escala original, o novo conjunto de escala é marcado como o tipo de nó primário. (Ao atualizar os tipos de nó não primário, omita esta alteração.)

    ```json
    "isPrimary": true,
    ```

* Ao contrário do conjunto de escala original, o novo conjunto de escala é atualizado para utilizar discos geridos.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Depois de ter implementado todas as alterações no seu modelo e ficheiros de parâmetros, dirija-se à secção seguinte para adquirir as referências do Cofre chave e implemente as atualizações para o seu cluster.

### <a name="obtain-your-key-vault-references"></a>Obtenha as referências do cofre chave

Para implementar a configuração atualizada, primeiro obterá várias referências ao seu certificado de cluster armazenado no seu Cofre de Chaves. A maneira mais fácil de encontrar estes valores é através do portal Azure. Precisa de:

* **O URL do Cofre chave do seu certificado de cluster.** A partir do seu cofre chave no portal Azure, selecione **Certificados** > *O seu certificado deidentificação* > **secreto**pretendido:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A impressão digital do seu certificado de cluster.** (Provavelmente já o tem se [estiver ligado ao cluster inicial](#connect-to-the-new-cluster-and-check-health-status) para verificar o seu estado de saúde.) A partir da mesma lâmina de certificado **(Certificados** > *O seu certificado pretendido*) no portal Azure, copiar **X.509 SHA-1 Thumbprint (in hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A identificação do recurso do seu cofre chave.** A partir do seu Cofre chave no portal Azure, selecione **Properties** > **Resource ID**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado

Ajuste `parameterFilePath` o `templateFilePath` e conforme necessário e, em seguida, executar o seguinte comando:

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

Quando a implantação estiver concluída, verifique novamente a saúde do cluster e certifique-se de que todos os dez nós (cinco no original e cinco no conjunto de escala nova) estão saudáveis.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrar os nódosos de sementes para o novo conjunto de escala

Estamos prontos para começar a desativar os nós do conjunto de escala original. À medida que estes nós ficam desativados, os serviços de sistema e os nós de sementes migram para os VMs da nova escala definida porque também é marcado como o tipo principal do nó.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Utilize o Service Fabric Explorer para monitorizar a migração de nódosos de sementes para o novo conjunto de escala e a progressão dos nódosos na escala original definida de *Desativação* para o estado *de deficientes.*

![Explorador de tecido de serviço mostrando o estado dos nódosos com deficiência](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Pode levar algum tempo para completar a operação incapacitante em todos os nós do conjunto de escala original. Para garantir a consistência dos dados, apenas um nó de sementes pode mudar de cada vez. Cada alteração do nó de sementes requer uma atualização do cluster; assim, a substituição de um nó de sementes requer duas atualizações de cluster (uma para adição e remoção do nó). A atualização dos cinco nós de sementes neste cenário de amostra resultará em dez melhorias de cluster.

## <a name="remove-the-original-scale-set"></a>Remova o conjunto de escala original

Uma vez concluída a operação de desativação, retire o conjunto de calcário.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

No Service Fabric Explorer, os nós removidos (e, portanto, o Estado de *Saúde*cluster) aparecerão agora no estado *de Erro.*

![Explorador de tecido de serviço mostrando nódosos deficientes em estado de erro](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Retire os nós obsoletos do cluster Service Fabric para restaurar o Estado de Saúde cluster para *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer com nódeos baixos em estado de erro removido](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Passos seguintes

Neste passeio, aprendeu a atualizar os conjuntos de escala de máquina virtual de um cluster de Tecido de Serviço para utilizar discos geridos, evitando interrupções de serviço durante o processo. Para obter mais informações sobre temas relacionados consulte os seguintes recursos.

Aprenda a:

* [Aumentar verticalmente um nó do tipo primário do cluster do Service Fabric](service-fabric-scale-up-node-type.md)

* [Converter um modelo de conjunto de escala para usar discos geridos](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Remova um tipo de nó de tecido de serviço](service-fabric-how-to-remove-node-type.md)

Veja também:

* [Amostra: Atualização de nós de cluster para usar discos geridos pelo Azure](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Considerações de escala vertical](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)