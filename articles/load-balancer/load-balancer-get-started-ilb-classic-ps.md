---
title: Criar um balanceador de carga interno – PowerShell clássico
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga interno com o PowerShell no modelo de implementação clássica
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms:custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: ef6aac0d97c38798f826304475779ea8059875c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848560"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Introdução à criação de um balanceador de carga interno (modo clássico) com o PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Serviços cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássica](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Saiba como [executar estes passos com o modelo do Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Criar um conjunto de balanceadores de carga internos para máquinas virtuais

Para criar um conjunto de balanceadores de carga internos e os servidores que irão enviar o tráfego para o mesmo, tem de efetuar o seguinte:

1. Crie uma instância Balanceamento de Carga Interno que será o ponto final do tráfego de entrada para balanceamento de carga nos servidores de um conjunto com balanceamento de carga.
2. Adicione os pontos finais correspondentes às máquinas virtuais que irão receber o tráfego de entrada.
3. Configure os servidores que irão enviar o tráfego para balanceamento de carga para enviar o tráfego para o endereço IP virtual (VIP) da instância de Balanceamento de Carga Interno.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Passo 1: Criar uma instância de balanceamento de carga interno

Para um serviço em nuvem existente ou um serviço em nuvem implementado numa rede virtual regional, pode criar uma instância de Balanceamento de Carga Interno com os seguintes comandos do Windows PowerShell:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Tenha em atenção que esta utilização do cmdlet do Windows PowerShell [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) utiliza o conjunto de parâmetros DefaultProbe. Para obter mais informações sobre conjuntos de parâmetros adicionais, veja [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Passo 2: Adicionar pontos finais para a instância de balanceamento de carga interno

Segue-se um exemplo:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Passo 3: Configurar os servidores para enviar o tráfego para o novo ponto de final balanceamento de carga interno

Tem de configurar os servidores para cujo tráfego será efetuado o balanceamento de carga para utilizar o novo endereço IP (VIP) da instância de Balanceamento de Carga Interno. Este é o endereço no qual a instância de Balanceamento de Carga Interno está à escuta. Na maioria dos casos, apenas tem de adicionar ou modificar um registo DNS para o VIP da instância de Balanceamento de Carga Interno.

Se especificou o endereço IP durante a criação da instância de Balanceamento de Carga Interno, já tem o VIP. Caso contrário, pode ver o VIP a partir dos seguintes comandos:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Para utilizar estes comandos, preencha os valores e remova < e >. Segue-se um exemplo:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

A partir do ecrã do comando Get-AzureInternalLoadBalancer, anote o endereço IP e efetue as alterações necessárias aos seus servidores ou registos DNS para garantir que o tráfego é enviado para o VIP.

> [!NOTE]
> A plataforma Microsoft Azure utiliza um endereço IPv4 estático encaminhável publicamente para uma grande variedade de cenários administrativos. O endereço IP é 168.63.129.16. Este endereço IP não deve ser bloqueado por nenhuma firewall, porque pode causar comportamentos inesperados.
> No que respeita ao Balanceamento de Carga Interno do Azure, este endereço IP é utilizado pelas sondas de monitorização do balanceador de carga para determinar o estado de funcionamento das máquinas virtuais num conjunto com balanceamento de carga. Se for utilizado um Grupo de Segurança de Rede para restringir o tráfego para as máquinas virtuais do Azure num conjunto com balanceamento de carga interno ou se for aplicado a uma Sub-rede de Rede Virtual, certifique-se de que é adicionada uma Regra de Segurança de Rede ao permitir tráfego a partir de 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Exemplo de balanceamento de carga interno

Para percorrer o processo de criação de um conjunto com balanceamento de carga para duas configurações de exemplo, veja as secções seguintes.

### <a name="an-internet-facing-multi-tier-application"></a>Uma aplicação de várias camadas com acesso à Internet

Pretende fornecer um serviço de base de dados com balanceamento de carga para um conjunto de servidores Web com acesso à Internet. Os dois conjuntos de servidores estão alojados num único serviço em nuvem do Azure. O tráfego de servidor Web para a porta TCP 1433 tem de ser distribuído entre duas máquinas virtuais na camada de base de dados. A Figura 1 mostra a configuração.

![Conjunto com balanceamento de carga interno para a camada de base de dados](./media/load-balancer-internal-getstarted/IC736321.png)

A configuração inclui o seguinte:

* O serviço em nuvem existente que está a alojar as máquinas virtuais é denominado mytestcloud.
* Os dois servidores de base de dados existentes são denominados DB1 e DB2.
* Os servidores Web na camada Web ligam aos servidores de base de dados na camada de base de dados através do endereço IP privado. Outra opção consiste em utilizar o seu próprio DNS para a rede virtual e registar manualmente um registo A para o conjunto de balanceadores de carga internos.

Os comandos seguintes configuram uma nova instância de Balanceamento de Carga Interno com o nome **ILBset** e adicionam pontos finais às máquinas virtuais correspondentes aos dois servidores de base de dados:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Remover uma configuração de Balanceamento de Carga Interno

Para remover uma máquina virtual como um ponto final de uma instância de balanceador de carga interno, utilize os seguintes comandos:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Para utilizar estes comandos, preencha os valores e remova < e >.

Segue-se um exemplo:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Para remover uma instância de balanceador de carga interno de um serviço em nuvem, utilize os seguintes comandos:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Para utilizar estes comandos, preencha o valor e remova < e >.

Segue-se um exemplo:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Informações adicionais sobre os cmdlets do balanceador de carga interno

Para obter informações adicionais sobre os cmdlets de Balanceamento de Carga Interno, execute os seguintes comandos numa linha de comandos do Windows PowerShell:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Passos Seguintes

[Configurar um modo de distribuição de balanceador de carga com a afinidade do IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

