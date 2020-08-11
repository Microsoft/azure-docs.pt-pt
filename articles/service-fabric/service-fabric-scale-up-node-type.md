---
title: Dimensione um nó de nó de tecido de serviço Azure
description: Aprenda a escalar um cluster de tecido de serviço adicionando um conjunto de balança de máquina virtual.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a8a8a432d4a11427f2c4f545a0907540af3112bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056487"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-virtual-machine-scale-set"></a>Dimensione um tipo de nó primário de cluster de tecido de serviço adicionando um conjunto de escala de máquina virtual
Este artigo descreve como escalar um tipo de nó primário de cluster de tecido de serviço, aumentando os recursos da máquina virtual. Um cluster de tecido de serviço é um conjunto de máquinas virtuais ou físicas ligadas à rede em que os seus microserviços são implantados e geridos. Uma máquina ou VM que faz parte de um aglomerado é chamada de nó. Os conjuntos de escala de máquinas virtuais são um recurso computacional Azure que utiliza para implementar e gerir uma coleção de máquinas virtuais como conjunto. Todos os tipos de nó definidos num cluster Azure [são configurado como um conjunto de escala separada](service-fabric-cluster-nodetypes.md). Cada tipo de nó pode então ser gerido separadamente. Depois de criar um cluster de Tecido de Serviço, pode escalar um nó de cluster verticalmente (alterar os recursos dos nós) ou atualizar o sistema operativo dos VMs do tipo nó.  Pode escalar o cluster a qualquer momento, mesmo quando as cargas de trabalho estão a funcionar no cluster.  À medida que o cluster escala, as suas aplicações também escalam automaticamente.

> [!WARNING]
> Não tente um procedimento de escala do tipo de nó primário se o estado do cluster não for saudável, pois isso só irá desestabilizar ainda mais o cluster.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Processo para atualizar o tamanho e o sistema operativo dos VMs do tipo de nó primário
Aqui está o processo de atualização do tamanho e sistema operativo VM dos VM do tipo de nó primário.  Após a atualização, os VMs do tipo de nó primário são o tamanho Standard D4_V2 e executar o Centro de Dados do Windows Server 2016 com contentores.

> [!WARNING]
> Antes de tentar este procedimento num cluster de produção, recomendamos que estude os modelos da amostra e verifique o processo contra um cluster de teste. O cluster também está indisponível por um tempo. Não pode fazer alterações em vários VMSS declarados como o mesmo NodeType em paralelo; terá de efetuar operações de implantação separadas para aplicar alterações a cada NodeType VMSS individualmente.

1. Coloque o cluster inicial com dois tipos de nós e dois conjuntos de escala (um conjunto de escala por tipo de nó) utilizando estes ficheiros [de modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) de amostra e [parâmetros.](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)  Ambos os conjuntos de escala são tamanho Standard D2_V2 e executar Windows Server 2012 R2 Datacenter.  Aguarde que o cluster complete a atualização da linha de base.   
2. Opcional: coloque uma amostra imponente no aglomerado.
3. Depois de decidir atualizar os VMs do tipo de nó primário, adicione um novo conjunto de escala ao tipo de nó primário utilizando estes ficheiros [de modelo](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) e [parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) de amostra, de modo que o tipo de nó primário agora tem dois conjuntos de escala.  Os serviços de sistema e as aplicações dos utilizadores são capazes de migrar entre VMs nos dois conjuntos de escala diferentes.  Os novos VMs de escala são tamanho Standard D4_V2 e executam o Centro de Dados 2016 do Windows Server com contentores.  Um novo equilibrador de carga e endereço IP público também são adicionados com o novo conjunto de escala.  
    Para encontrar a nova escala definida no modelo, procure o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro *vmNodeType2Name.*  O novo conjunto de escala é adicionado ao tipo de nó primário utilizando a extensão virtual >virtualMachineProfile->extensõesProfile->extensões >configurações >>definições noTypeRef.
4. Verifique a saúde do aglomerado e verifique se todos os nós estão saudáveis.
5. Desative os nós no conjunto de escala antiga do tipo nó primário com a intenção de remover o nó. Pode desativar tudo de uma vez e as operações estão em fila. Aguarde até que todos os nós estejam desactivdos, o que pode levar algum tempo.  À medida que os nós mais antigos do tipo nó são desativados, os serviços do sistema e os nós de sementes migram para os VMs da nova escala definida no tipo de nó primário.
6. Retire o conjunto de escala mais antiga do tipo de nó primário. (Depois de os nós serem desativados como no passo 5, na lâmina de balança de máquina virtual no portal Azure, transborde os nós do antigo nó do tipo um a um.)
7. Retire o balançador de carga associado ao conjunto de balanças antigas. O cluster não está disponível enquanto o novo endereço IP público e o balanceador de carga estão configurados para o novo conjunto de escala.  
8. Guarde as definições de DNS do endereço IP público associado à antiga escala de tipo nó primário definida numa variável e remova esse endereço IP público.
9. Substitua as definições de DNS do endereço IP público associado à nova escala de tipo de nó primário definida pelas definições DNS do endereço IP público eliminado.  O aglomerado está agora acessível novamente.
10. Retire o estado do nó dos nós do aglomerado.  Se o nível de durabilidade do conjunto de escala antiga era prateado ou dourado, este passo é feito automaticamente pelo sistema.
11. Se implementou a aplicação imponente num passo anterior, verifique se a aplicação está funcional.

## <a name="set-up-the-test-cluster"></a>Configurar o cluster de teste

Comece por descarregar os dois conjuntos de ficheiros que precisaremos para este tutorial, o [modelo]() e [parâmetros anteriores]() e o [modelo]() e [parâmetros posteriores.]()

Em seguida, inscreva-se na sua conta Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Este tutorial percorre o cenário de criação de um certificado auto-assinado. Para utilizar um certificado existente a partir do Cofre da Chave Azure, salte o degrau abaixo e, em vez disso, espelhe os passos [na utilização de um certificado existente para implantar o cluster](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Gere um certificado auto-assinado e implantar o cluster

Em primeiro lugar, atribua as variáveis necessárias para a implantação do cluster de tecido de serviço. Ajuste os valores `resourceGroupName` `certSubjectName` para, `parameterFilePath` e para a sua conta e ambiente `templateFilePath` específicos:

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
> Certifique-se de que existe a `certOutputFolder` localização na sua máquina local antes de executar o comando para implantar um novo cluster de Tecido de Serviço.

Em seguida, abra o *Deploy-2NodeTypes-2ScaleSets.parameters.jsno* ficheiro e ajuste os valores para `clusterName` e para corresponder aos `dnsName` valores dinâmicos que definiu no PowerShell e guarde as suas alterações.

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

A operação devolverá a impressão digital do certificado, que utilizará para ligar ao novo cluster e verificar o seu estado de saúde.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Ligue-se ao novo cluster e verifique o estado de saúde

Ligue-se ao cluster e certifique-se de que todos os seus nós são saudáveis (substituindo as `clusterName` variáveis e `thumb` variáveis para o seu cluster):

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

## <a name="upgrade-the-primary-node-type-vms"></a>Atualizar os VMs do tipo de nó primário

Depois de decidir atualizar os VMs do tipo de nó primário, adicione um novo conjunto de escala ao tipo de nó primário de modo que o tipo de nó primário tenha agora dois conjuntos de escala. Foi fornecido [um modelo de](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) amostra e [ficheiros de parâmetros](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) para mostrar as alterações necessárias. Os VMs do novo conjunto de escala são tamanho Standard D4_V2 e executam o Centro de Dados 2016 do Windows Server com contentores. Um novo equilibrador de carga e endereço IP público também são adicionados com o novo conjunto de escala. 

Para encontrar a nova escala definida no modelo, procure o recurso "Microsoft.Compute/virtualMachineScaleSets" nomeado pelo parâmetro vmNodeType2Name. O novo conjunto de escala é adicionado ao tipo de nó primário utilizando a extensão virtual >virtualMachineProfile->extensõesProfile->extensões >configurações >>definições noTypeRef.

### <a name="deploy-the-updated-template"></a>Implementar o modelo atualizado

Ajuste o `parameterFilePath` comando `templateFilePath` e, se necessário, e, em seguida, executar o seguinte comando:

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

Quando a colocação terminar, verifique novamente a saúde do cluster e certifique-se de que todos os nós (no original e no conjunto de nova escala) estão saudáveis.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrar os acenos para o conjunto de nova escala

Estamos prontos para começar a desativar os nós do conjunto de escala original. À medida que estes nós ficam desativados, os serviços de sistema e os nós de sementes migram para os VMs da nova escala definida porque também é marcado como o tipo de nó primário.

Para aumentar os tipos de nó não primários, neste passo modificaria a restrição de colocação de serviço para incluir o novo conjunto de escala/tipo de nó de máquina virtual e, em seguida, reduziria a contagem de instância definida da máquina virtual antiga para zero, um nó de cada vez (para garantir que a remoção do nó não impacta a fiabilidade do cluster).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Utilize o Service Fabric Explorer para monitorizar a migração dos nós de sementes para o novo conjunto de escalas e a progressão dos nós na escala original definida de *Desativando* para o estado *de Desativação.*

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

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Remova o antigo equilibrador de carga e atualize as definições de DNS

Agora, podemos remover os recursos relacionados com o antigo tipo de nó primário, começando com o equilibrador de carga e o antigo IP público. 

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

Por fim, remova o estado do nó para cada um dos nós relacionados. Se o nível de durabilidade do conjunto de escala antiga for prateado ou dourado, isto ocorrerá automaticamente.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

O tipo de nó primário do cluster foi agora atualizado. Verifique se as aplicações implementadas funcionam corretamente e que a saúde do cluster está bem.

## <a name="next-steps"></a>Passos seguintes
* Saiba como [adicionar um nó ao aglomerado](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Saiba mais sobre [a escalabilidade da aplicação.](service-fabric-concepts-scalability.md)
* [Escalar um aglomerado Azure dentro ou fora](service-fabric-tutorial-scale-cluster.md).
* [Dimensione um cluster Azure programáticamente](service-fabric-cluster-programmatic-scaling.md) usando o fluente Azure compute SDK.
* [Escalar um aglomerado autónomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).
