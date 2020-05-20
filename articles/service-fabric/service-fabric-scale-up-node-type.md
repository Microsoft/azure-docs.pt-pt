---
title: Dimensione um nó de tecido de serviço Azure
description: Aprenda a escalar um cluster de tecido de serviço adicionando um conjunto de escala de máquina virtual.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 5ea4f37a6c088c6f738ef05db8b5b295982c27fe
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674217"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Aumentar verticalmente um nó do tipo primário do cluster do Service Fabric
Este artigo descreve como escalar um tipo de nó primário de cluster de tecido de serviço, aumentando os recursos da máquina virtual. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede, nas quais os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamado de nó. Os conjuntos de escala de máquinas virtuais são um recurso de computação Azure que utiliza para implantar e gerir uma coleção de máquinas virtuais como conjunto. Cada tipo de nó definido num cluster Azure é [configurado como um conjunto](service-fabric-cluster-nodetypes.md)de escala separado . Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um tipo de nó de cluster verticalmente (alterar os recursos dos nós) ou atualizar o sistema operativo dos VMs do tipo nó.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a decorrer no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

> [!WARNING]
> Não comece a alterar o nóprimário VM SKU, se a saúde do cluster não for saudável. Se a saúde do cluster não for saudável, só irá desestabilizar ainda mais o cluster, se tentar alterar o VM SKU.
>
> Recomendamos que não altere o VM SKU de um tipo de conjunto/nó de escala, a menos que esteja a funcionar na [durabilidade da Prata ou superior](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Mudar o tamanho VM SKU é uma operação de infraestrutura destrutiva em dados. Sem alguma capacidade de atrasar ou monitorizar esta alteração, é possível que a operação possa causar perda de dados para serviços estatais ou causar outras questões operacionais imprevistas, mesmo para cargas de trabalho apátridas. Isto significa que o seu tipo principal de nó, que está a executar serviços de sistema de tecido de serviço audais, ou qualquer tipo de nó que esteja a executar as suas cargas de trabalho de aplicação audais.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Processo de atualização do tamanho e do sistema operativo dos VMs do nó primário
Aqui está o processo de atualização do tamanho vm e do sistema operativo dos VM do tipo principal vMs.  Após a atualização, os VMs do nó principal são o tamanho Standard D4_V2 e executando o Datacenter Do Windows Server 2016 com contentores.

> [!WARNING]
> Antes de tentar este procedimento num cluster de produção, recomendamos que estude os modelos de amostra e verifique o processo contra um cluster de teste. O cluster também está indisponível por um tempo. NÃO pode fazer alterações em múltiplos VMSS declarados como o mesmo Nó Type em paralelo; terá de efetuar operações de implantação separadas para aplicar alterações individualmente em cada VMSS no nó.

1. Desloque o cluster inicial com dois tipos de nó e dois conjuntos de escala (um conjunto de escala por tipo de nó) utilizando estes [modelos](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) de amostra e ficheiros [de parâmetros.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Ambos os conjuntos de escala são tamanho Standard D2_V2 e executando O Datacenter Do Windows Server 2012 R2.  Aguarde que o cluster complete a atualização de base.   
2. Opcional: coloque uma amostra de estado no cluster.
3. Depois de decidir atualizar os VMs do nó primário, adicione um novo conjunto de escala ao tipo de nó primário utilizando estes [modelos](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) de amostra e ficheiros de [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) para que o tipo de nó primário tenha agora dois conjuntos de escala.  Os serviços de sistema e as aplicações dos utilizadores são capazes de migrar entre VMs nos dois conjuntos de escala diferentes.  Os novos VMs conjuntos de escala são tamanho Standard D4_V2 e executam o Datacenter Do Windows Server 2016 com contentores.  Um novo equilibrador de carga e endereço IP público também são adicionados com o novo conjunto de escala.  
    Para encontrar a nova escala definida no modelo, procure o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro *vmNodeType2Name.*  O novo conjunto de escala é adicionado ao tipo de nó primário utilizando a extensão virtual >>de propriedades->>Máquinasdeperfil->de extensões->definições >>definição de nóTypeRef >definição.
4. Verifique a saúde do cluster e verifique se todos os nós estão saudáveis.
5. Desative os nódosos no conjunto de calcário antigo do tipo de nó primário com a intenção de remover o nó. Podedes desativar tudo de uma vez e as operações estão em fila. Espere até que todos os nós estejam desativados, o que pode levar algum tempo.  Como os nós mais velhos do tipo de nó são desativados, os serviços do sistema e os nós de sementes migram para os VMs da nova escala definida no tipo de nó primário.
6. Retire a balança mais antiga do tipo de nó primário. (Depois de os nós serem desativados como no passo 5, na lâmina de conjunto de máquinas virtuais no portal Azure, desalocam os nós do nó antigo tipo um por um.)
7. Retire o equilíbrio de carga associado ao conjunto de calcário antigo. O cluster não está disponível enquanto o novo endereço IP público e o equilibrador de carga estão configurados para o novo conjunto de escala.  
8. Guarde as definições de DNS do endereço IP público associado à antiga escala do tipo de nó primário definida numa variável e remova esse endereço IP público.
9. Substitua as definições de DNS do endereço IP público associado seleção do novo tipo de nó primário com as definições dNS do endereço IP público eliminado.  O aglomerado está agora acessível novamente.
10. Retire o estado do nó dos nós do aglomerado.  Se o nível de durabilidade do conjunto de escala antiga for prateado ou dourado, este passo é feito automaticamente pelo sistema.
11. Se implementou a aplicação imponente num passo anterior, verifique se a aplicação está funcional.

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Comece por descarregar os dois conjuntos de ficheiros que precisamos para este tutorial, o [modelo]() e [parâmetros]() anteriores e o [modelo]() e [parâmetros posteriores.]()

Em seguida, inscreva-se na sua conta Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Este tutorial percorre o cenário de criar um certificado auto-assinado. Para utilizar um certificado existente a partir do Cofre de Chaves Azure, salte o passo abaixo e, em vez disso, espelhe os passos na [utilização](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster)de um certificado existente para implantar o cluster .

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gerar um certificado auto-assinado e implementar o cluster

Em primeiro lugar, atribua as variáveis que necessitará para a implantação do cluster Service Fabric. Ajuste os valores `resourceGroupName` para, `certSubjectName` , e para a sua conta e ambiente `parameterFilePath` `templateFilePath` específicos:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Certifique-se de que a localização existe na sua máquina local antes de `certOutputFolder` executar o comando para implantar um novo cluster de Tecido de Serviço.

Em seguida, abra o ficheiro *Deploy-2NodeTypes-2ScaleSets.parâmetros.json* e ajuste os valores `clusterName` para e para corresponder aos `dnsName` valores dinâmicos que definiu no PowerShell e guarde as suas alterações.

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

Uma vez concluída a implantação, localize o ficheiro *.pfx* `$certPfx` () na sua máquina local e importe-o para o seu certificado:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A operação devolverá a impressão digital do certificado, que utilizará para se ligar ao novo cluster e verificar o seu estado de saúde.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Ligue-se ao novo cluster e verifique o estado de saúde

Ligue-se ao cluster e certifique-se de que todos os seus nós são saudáveis (substituindo as `clusterName` e `thumb` variáveis para o seu cluster):

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

Estamos prontos para iniciar o procedimento de atualização.

## <a name="upgrade-the-primary-node-type-vms"></a>Atualizar os VMs do nó primário tipo de

Depois de decidir atualizar os VMs do nó primário, adicione uma nova escala definida para o tipo de nó primário de modo que o tipo de nó primário agora tem dois conjuntos de escala. Foi fornecido um [modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) de amostra e ficheiros de [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) para mostrar as alterações necessárias. Os VMs do novo conjunto de escala são tamanho Standard D4_V2 e executam o Datacenter Do Windows Server 2016 com contentores. Um novo equilibrador de carga e endereço IP público também são adicionados com o novo conjunto de escala. 

Para encontrar a nova escala definida no modelo, procure o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro vmNodeType2Name. O novo conjunto de escala é adicionado ao tipo de nó primário utilizando a extensão virtual >>de propriedades->>Máquinasdeperfil->de extensões->definições >>definição de nóTypeRef >definição.

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado

Ajuste o `parameterFilePath` e `templateFilePath` conforme necessário e, em seguida, executar o seguinte comando:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Quando a implantação estiver concluída, verifique novamente a saúde do cluster e certifique-se de que todos os nós (no original e no novo conjunto de escala) estão saudáveis.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrar os nódosos para o novo conjunto de escala

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

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Remova as definições de DNS do antigo equilibrador de carga e atualize as definições de DNS

Agora, podemos remover os recursos relacionados com o antigo tipo de nó primário, começando com o equilibrante de carga e o antigo IP público. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Em seguida, atualizamos as definições de DNS do novo IP público para espelhar as definições do IP público do antigo tipo de nó primário.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Mais uma vez, verifique a saúde do cluster

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Finalmente, remova o estado do nó para cada um dos nós relacionados. Se o nível de durabilidade do conjunto de escala antiga for prateado ou dourado, isso ocorrerá automaticamente.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

O tipo principal do nó do cluster foi agora atualizado. Verifique se as aplicações implementadas funcionam corretamente e a saúde do cluster está bem.

## <a name="next-steps"></a>Passos seguintes
* Aprenda a [adicionar um tipo de nó a um cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escala um aglomerado Azure para dentro ou para fora.](service-fabric-tutorial-scale-cluster.md)
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) utilizando o fluente sdk de computação Azure.
* [Escala um aglomerado autónomo dentro ou fora.](service-fabric-cluster-windows-server-add-remove-nodes.md)

