---
title: Configurar o balanceador de carga para o serviço de escuta VNN do AG
description: Aprenda a configurar um Azure Load Balancer para encaminhar o tráfego para o nome de rede virtual (VNN) para o seu grupo de disponibilidade com SQL Server em VMs Azure para alta disponibilidade e recuperação de desastres (HADR).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2d89759438cb625a0e220af10ab6b287096f6390
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359885"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>Configurar o balanceador de carga para o serviço de escuta VNN do AG
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Nas Máquinas Virtuais Azure, os clusters usam um equilibrador de carga para conter um endereço IP que precisa de estar num nó de cluster de cada vez. Nesta solução, o equilibrador de carga detém o endereço IP para o ouvinte de rede virtual (VNN) para o grupo de disponibilidade Always On (AG). 

Este artigo ensina-o a configurar um equilibrador de carga utilizando o serviço Azure Load Balancer. O equilibrador de carga irá encaminhar o tráfego para o seu [ouvinte do grupo de disponibilidade (AG)](availability-group-overview.md) com o SQL Server em VMs Azure para uma elevada disponibilidade e recuperação de desastres (HADR). 

Para uma opção de conectividade alternativa para clientes que estão no SQL Server 2019 CU8 e mais tarde, considere um [ouvinte DNN](availability-group-vnn-azure-load-balancer-configure.md) em vez de configuração simplificada e melhor falha.  



## <a name="prerequisites"></a>Pré-requisitos

Antes de completar os passos deste artigo, já deve ter:

- Decidiu que o Azure Load Balancer é a opção de conectividade adequada [para a sua solução HADR](hadr-cluster-best-practices.md#connectivity).
- Configurar o seu [ouvinte de grupo de disponibilidade](availability-group-overview.md).
- Instalou a versão mais recente do [PowerShell.](/powershell/azure/install-az-ps) 


## <a name="create-load-balancer"></a>Criar um balanceador de carga

Utilize o [portal Azure](https://portal.azure.com) para criar o equilibrador de carga:

1. No portal Azure, vá ao grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Procure no Mercado Azure para **o balanceador de carga.** Selecione **o balanceador de carga**.

1. Selecione **Criar**.

1. Configurar o balançador de carga utilizando os seguintes valores:

   - **Subscrição**: A sua subscrição Azure.
   - **Grupo de recursos**: O grupo de recursos que contém as suas máquinas virtuais.
   - **Nome**: Um nome que identifica o esquilibrador de carga.
   - **Região**: A localização azul que contém as suas máquinas virtuais.
   - **Tipo**: Público ou privado. Um equilibrador de carga privado pode ser acedido a partir da rede virtual. A maioria das aplicações Azure pode usar um equilibrador de carga privado. Se a sua aplicação necessitar de acesso ao SQL Server diretamente através da internet, utilize um equilibrador de carga público.
   - **SKU**: Padrão.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: Estática. 
   - **Endereço IP privado**: O endereço IP que atribuiu ao recurso de rede agrupado.

   A imagem a seguir mostra o **UI do equilibrador de carga Create:**

   ![Configurar o equilibrador de carga](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Piscina de backend configurada

1. Volte ao grupo de recursos Azure que contém as máquinas virtuais e localize o novo equilibrador de carga. Pode ser necessário refrescar a vista sobre o grupo de recursos. Selecione o equilibrador de carga.

1. Selecione **piscinas backend** e, em seguida, selecione **Adicionar**.

1. Associe o pool de backend com o conjunto de disponibilidade que contém os VMs.

1. Nas **configurações IP da rede Target**, selecione VIRTUAL **MACHINE** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que irão acolher o FCI ou grupo de disponibilidade.

1. Selecione **OK** para criar a piscina de backend.

## <a name="configure-health-probe"></a>Configure sonda de saúde

1. No painel de balanço de carga, selecione **sondas de saúde**.

1. Selecione **Adicionar**.

1. No painel de sonda <span id="probe"></span> **saúde Add,** desa estale os seguintes parâmetros da sonda de saúde:

   - **Nome**: Um nome para a sonda de saúde.
   - **Protocolo**: TCP.
   - **Porto**: A porta que criou na firewall para a sonda de saúde [ao preparar o VM](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). Neste artigo, o exemplo utiliza a porta `59999` TCP.
   - **Intervalo:** 5 Segundos.
   - **Limiar pouco saudável:** 2 falhas consecutivas.

1. Selecione **OK**.

## <a name="set-load-balancing-rules"></a>Definir regras de equilíbrio de carga

1. No painel de balanço de carga, selecione **as regras de equilíbrio de carga**.

1. Selecione **Adicionar**.

1. Definir os parâmetros de regra de equilíbrio de carga:

   - **Nome**: Um nome para as regras de equilíbrio de carga.
   - **Endereço IP frontend**: O endereço IP para as FCIs do Servidor SQL ou o recurso de rede ag do ouvinte ag.
   - **Porta**: A porta TCP do sql Server. A porta de instância padrão é 1433.
   - **Porta de backend**: A mesma porta que o valor da **porta** quando ativa o **IP flutuante (retorno direto do servidor)**.
   - **Piscina de backend**: O nome da piscina de backend que configuraste anteriormente.
   - **Sonda de saúde**: A sonda de saúde que configuraste anteriormente.
   - **Persistência da sessão**: Nenhuma.
   - **Tempo de 20 minutos ( minutos):** 4.
   - **IP flutuante (retorno do servidor direto)**: Ativado.

1. Selecione **OK**.

## <a name="configure-cluster-probe"></a>Sonda de cluster de configuração

Desa estale o parâmetro da porta da sonda de cluster no PowerShell.

Para definir o parâmetro da porta da sonda de cluster, atualize as variáveis no seguinte script com valores do seu ambiente. Retire os suportes angulares `<` `>` (e) do script.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

A tabela a seguir descreve os valores que precisa de atualizar:


|**Valor**|**Descrição**|
|---------|---------|
|`Cluster Network Name`| O nome do Cluster failover do servidor do Windows para a rede. Nas Redes **de Gestores de Cluster Failover,**  >  clique à direita na rede e selecione **Propriedades.** O valor correto está no **nome** no separador **Geral.**|
|`AG listener IP Address Resource Name`|O nome de recurso para o endereço IP do SQL Server FCI ou do ouvinte AG. Em **Funções de Gestor de Cluster Failover**  >  , sob a função SQL Server FCI, em Nome **do Servidor,** clique com o botão direito no recurso de endereço IP e selecione **Propriedades**. O valor correto está no **nome** no separador **Geral.**|
|`ILBIP`|O endereço IP do esquilibrador interno de carga (ILB). Este endereço está configurado no portal Azure como o endereço frontal do ILB. Este é também o endereço IP do SQL Server FCI. Pode encontrá-lo no **Failover Cluster Manager** na mesma página de propriedades onde localiza o `<AG listener IP Address Resource Name>` .|
|`nnnnn`|A porta da sonda que configuraste na sonda de saúde do equilibrador de carga. Qualquer porta TCP não utilizada é válida.|
|"SubnetMask"| A máscara de sub-rede para o parâmetro do cluster. Deve ser o endereço de emissão IP da TCP: `255.255.255.255` .| 


Depois de definir a sonda de cluster, pode ver todos os parâmetros de cluster no PowerShell. Executar este script:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Ativação pós-falha de teste


Teste falha no recurso agrupado para validar a funcionalidade do cluster. 


Siga estes passos:

1. Abra [o SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) e ligue-se ao seu ouvinte de grupo de disponibilidade. 
1. Expandir **sempre no grupo de disponibilidade** no Explorador de **Objetos.** 
1. Clique com o botão direito no grupo de disponibilidade e selecione **Failover**. 
1. Siga as instruções do assistente para falhar sobre o grupo de disponibilidade para uma réplica secundária. 

A falha tem sucesso quando as réplicas trocam as funções e são ambas sincronizadas. 


## <a name="test-connectivity"></a>Testar conectividade

Para testar a conectividade, inscreva-se noutra máquina virtual na mesma rede virtual. Abra **o SQL Server Management Studio** e ligue-se ao ouvinte do grupo de disponibilidade.

>[!NOTE]
>Se precisar, pode [baixar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades HADR do SQL Server no Azure, consulte [os grupos Availability](availability-group-overview.md) e [o caso do cluster Failover](failover-cluster-instance-overview.md). Você também pode aprender [as melhores práticas](hadr-cluster-best-practices.md) para configurar o seu ambiente para alta disponibilidade e recuperação de desastres. 



