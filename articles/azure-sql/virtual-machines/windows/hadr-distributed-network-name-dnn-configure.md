---
title: Configurar o nome da rede distribuída (DNN)
description: Saiba como configurar um nome de rede distribuído (DNN) para encaminhar o tráfego para o seu SQL Server em instância de cluster failover (FCI) do Azure VM.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ce3261eca8697ae1fabc07785353a4e845dadeba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317009"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>Configurar um nome de rede distribuído para um FCI 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Nas Máquinas Virtuais Azure, o nome de rede distribuído (DNN) é utilizado para encaminhar o tráfego para o recurso agrupado apropriado. Fornece uma forma mais fácil de ligar ao exemplo de cluster failover do SQL Server (FCI) do que o nome de rede virtual (VNN), sem a necessidade de Azure Load Balancer. Esta funcionalidade encontra-se atualmente em pré-visualização e está disponível apenas para o SQL Server 2019 CU2 e mais tarde e Windows Server 2016 e posteriormente. 

Este artigo ensina-o a configurar um DNN para encaminhar o tráfego para as suas FCIs com SQL Server em VMs Azure para uma elevada disponibilidade e recuperação de desastres (HADR). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de completar os passos deste artigo, já deve ter:

- Decidiu que o nome da rede distribuída é a opção de conectividade adequada [para a sua solução HADR](hadr-cluster-best-practices.md#connectivity).
- Configurado os seus [casos de cluster de failover](failover-cluster-instance-overview.md). 
- Instalou a versão mais recente do [PowerShell.](/powershell/azure/install-az-ps) 

## <a name="create-dnn-resource"></a>Criar recurso DNN 

O recurso DNN é criado no mesmo grupo de cluster que o SQL Server FCI. Utilize o PowerShell para criar o recurso DNN dentro do grupo de cluster FCI. 

O seguinte comando PowerShell adiciona um recurso DNN ao grupo de cluster SQL Server FCI com um nome de recurso de `<dnnResourceName>` . O nome do recurso é usado para identificar exclusivamente um recurso. Use um que faça sentido para si e seja único em todo o cluster. O tipo de recurso deve ser `Distributed Network Name` . 

O `-Group` valor deve ser o nome do grupo de cluster que corresponde ao SQL Server FCI onde pretende adicionar o nome de rede distribuído. Para um caso padrão, o formato típico é `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Por exemplo, para criar o seu recurso DNN `dnn-demo` para um SQL Server FCI predefinido, utilize o seguinte comando PowerShell:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Conjunto cluster DNN DNS nome

Desaprova o nome DNS para o recurso DNN no cluster. Em seguida, o cluster usa este valor para encaminhar o tráfego para o nó que está atualmente a hospedar o SQL Server FCI. 
 
Os clientes usam o nome DNS para se conectarem ao SQL Server FCI. Pode escolher um valor único. Ou, se já tem uma FCI existente e não quer atualizar as cadeias de ligação do cliente, pode configurar o DNN para utilizar o VNN atual que os clientes já estão a usar. Para tal, é necessário [mudar o nome do VNN](#rename-the-vnn) antes de definir o DNN em DNS.


Utilize este comando para definir o nome DNS para o seu DNN: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

O `DNSName` valor é o que os clientes usam para se conectarem ao SQL Server FCI. Por exemplo, para os clientes se `FCIDNN` ligarem, utilize o seguinte comando PowerShell:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Os clientes entrarão agora `FCIDNN` na sua cadeia de ligação quando se ligarem ao SQL Server FCI. 

   > [!WARNING]
   > Não elimine o nome de rede virtual atual (VNN) uma vez que é um componente necessário da infraestrutura FCI. 


### <a name="rename-the-vnn"></a>Mude o nome da VNN 

Se tiver um nome de rede virtual existente e pretender que os clientes continuem a utilizar este valor para ligar ao SQL Server FCI, deve mudar o nome do VNN atual para um valor de espaço reservado. Depois de o VNN atual ser renomeado, pode definir o valor do nome DNS para o DNN para o VNN. 

Algumas restrições aplicam-se para renomear o VNN. Para mais informações, consulte [renomear um FCI.](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance)

Se utilizar o VNN atual não é necessário para o seu negócio, salte esta secção. Depois de renomear o VNN, em seguida, [definir o nome DNN DNS do cluster](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Definir recurso DNN on-line

Depois de o seu recurso DNN ter sido devidamente nomeado, e definir o valor do nome DNS no cluster, utilize o PowerShell para definir o recurso DNN on-line no cluster: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Por exemplo, para iniciar o seu recurso `dnn-demo` DNN, utilize o seguinte comando PowerShell: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Configure possíveis proprietários

Por predefinição, o cluster liga o nome DNN DNS a todos os nós do cluster. No entanto, os nós no cluster que não fazem parte do SQL Server FCI devem ser excluídos da lista de possíveis proprietários do DNN. 

Para atualizar possíveis proprietários, siga estes passos:

1. Aceda ao seu recurso DNN no Failover Cluster Manager. 
1. Clique com o botão direito no recurso DNN e selecione **Propriedades.** 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Menu de atalho para o recurso DNN, com o comando Propriedades em destaque.":::
1. Limpe a caixa de verificação para quaisquer nós que não participem na instância do cluster de failover. A lista de possíveis proprietários para o recurso DNN deve corresponder à lista de possíveis proprietários para o recurso de instância sql Server. Por exemplo, assumindo que os Dados3 não participam na FCI, a imagem a seguir é um exemplo de remoção de Dados3 da lista de possíveis proprietários para o recurso DNN: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Menu de atalho para o recurso DNN, com o comando Propriedades em destaque.":::

1. Selecione **OK** para guardar as suas definições. 


## <a name="restart-sql-server-instance"></a>Reiniciar a instância do Servidor SQL 

Utilize o Gestor de Cluster Failover para reiniciar a instância do SQL Server. Siga estes passos:

1. Aceda ao seu recurso SQL Server no Failover Cluster Manager.
1. Clique com o botão direito no recurso SQL Server e desative-o. 
1. Depois de todos os recursos associados estão offline, clique com o botão direito no recurso SQL Server e volte a on-lo. 

## <a name="update-connection-string"></a>Cadeia de ligação de atualização

Para garantir uma conectividade rápida após o failover, adicione `MultiSubnetFailover=True` à cadeia de ligação se a versão do cliente SQL for anterior a 4.6.1. 

Além disso, se o DNN não estiver a utilizar os clientes VNN originais, os clientes SQL que se ligarem ao SQL Server FCI terão de atualizar a sua cadeia de ligação ao nome DNN DNS. Para evitar este requisito, pode atualizar o valor do nome DNS para ser o nome do VNN. Mas terá de [substituir o VNN existente](#rename-the-vnn) primeiro por um espaço reservado. 

## <a name="test-failover"></a>Ativação pós-falha de teste

Teste falha no recurso agrupado para validar a funcionalidade do cluster. 

Para testar o failover, siga estes passos: 

1. Ligue-se a um dos nós de cluster sql com recurso a RDP.
1. Open **Failover Cluster Manager**. Selecione **Funções**. Note qual o nó que detém a função SQL Server FCI.
1. Clique com o botão direito no papel SQL Server FCI. 
1. Selecione **Mover**e, em seguida, selecione **O Melhor Nó Possível**.

**O Failover Cluster Manager** mostra o papel e os seus recursos ficam offline. Os recursos movem-se e voltam a estar on-line no outro nó.

## <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inscreva-se noutra máquina virtual na mesma rede virtual. Abra **o SQL Server Management Studio** e ligue-se ao SQL Server FCI utilizando o nome DNN DNS.

Se precisar, pode [baixar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="avoid-ip-conflict"></a>Evitar conflitos ip

Este é um passo opcional para evitar que o endereço IP virtual (VIP) utilizado pelo recurso FCI seja atribuído a outro recurso em Azure como uma duplicata. 

Embora os clientes utilizem agora o DNN para se conectarem ao SQL Server FCI, o nome da rede virtual (VNN) e IP virtual não podem ser eliminados, uma vez que são componentes necessários da infraestrutura FCI. No entanto, uma vez que já não existe um equilibrador de carga que reserva o endereço IP virtual em Azure, existe o risco de outro recurso na rede virtual ser atribuído o mesmo endereço IP que o endereço IP virtual utilizado pelo FCI. Isto pode potencialmente conduzir a um problema duplicado de conflitos IP. 

Configurar um endereço APIPA ou um adaptador de rede dedicado para reservar o endereço IP. 

### <a name="apipa-address"></a>Endereço APIPA

Para evitar a utilização de endereços IP duplicados, configuure um endereço APIPA (também conhecido como endereço link-local). Para tal, execute o seguinte comando:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

Neste comando, "endereço IP virtual" é o nome do recurso de endereço VIP agrupado, e "169.254.1.1" é o endereço APIPA escolhido para o endereço VIP. Escolha o endereço que melhor se adequa ao seu negócio. Configurado `OverrideAddressMatch=1` para permitir que o endereço IP esteja em qualquer rede, incluindo o espaço de endereço APIPA. 

### <a name="dedicated-network-adapter"></a>Adaptador de rede dedicado

Em alternativa, configurar um adaptador de rede em Azure para reservar o endereço IP utilizado pelo recurso de endereço IP virtual. No entanto, isto consome o endereço no espaço do endereço da sub-rede, e existe a sobrecarga adicional para garantir que o adaptador de rede não seja utilizado para qualquer outro fim.

## <a name="limitations"></a>Limitações

- Atualmente, um DNN é suportado apenas para SQL Server 2019 CU2 e mais tarde no Windows Server 2016. 
- Atualmente, um DNN é suportado apenas para casos de cluster de failover com SQL Server em Azure VMs. Utilize o nome de rede virtual com o Azure Load Balancer para os ouvintes do grupo de disponibilidade.
- Pode haver mais considerações quando estiver a trabalhar com outras funcionalidades do SQL Server e um FCI com um DNN. Para obter mais informações, consulte [a FCI com interoperabilidade da DNN.](failover-cluster-instance-dnn-interoperability.md) 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades HADR do SQL Server no Azure, consulte [os grupos Availability](availability-group-overview.md) e [o caso do cluster Failover](failover-cluster-instance-overview.md). Você também pode aprender [as melhores práticas](hadr-cluster-best-practices.md) para configurar o seu ambiente para alta disponibilidade e recuperação de desastres. 

Pode haver requisitos de configuração adicionais para algumas funcionalidades específicas do SQL Server quando usados com o DNN e FCI. Consulte [o FCI com a interoperabilidade da DNN](failover-cluster-instance-dnn-interoperability.md) para saber mais. 

