---
title: Configurar ouvintes de grupo de disponibilidade & balanceador de carga (portal do Azure)
description: Instruções passo a passo para criar um ouvinte para um grupo de disponibilidade Always On para SQL Server em máquinas virtuais do Azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039656"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Configurar um balanceador de carga para um grupo de disponibilidade em VMs SQL Server do Azure
Este artigo explica como criar um balanceador de carga para um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure que estão sendo executadas com o Azure Resource Manager. Um grupo de disponibilidade requer um balanceador de carga quando as instâncias de SQL Server estão em máquinas virtuais do Azure. O balanceador de carga armazena o endereço IP para o ouvinte do grupo de disponibilidade. Se um grupo de disponibilidade abranger várias regiões, cada região precisará de um balanceador de carga.

Para concluir essa tarefa, você precisa ter um grupo de disponibilidade SQL Server implantado em máquinas virtuais do Azure em execução com o Gerenciador de recursos. Ambas as máquinas virtuais SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gerenciador de recursos. Esse modelo cria automaticamente um balanceador de carga interno para você. 

Se preferir, você pode [configurar manualmente um grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Este artigo requer que seus grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configurar grupos de disponibilidade Always On na VM do Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurar uma conexão de VNet para VNet usando o Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ao percorrer este artigo, você cria e configura um balanceador de carga no portal do Azure. Depois que o processo for concluído, você configurará o cluster para usar o endereço IP do balanceador de carga para o ouvinte do grupo de disponibilidade.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o balanceador de carga no portal do Azure
Nesta parte da tarefa, faça o seguinte:

1. No portal do Azure, crie o balanceador de carga e configure o endereço IP.
2. Configure o pool de back-ends.
3. Crie a sonda. 
4. Defina as regras de balanceamento de carga.

> [!NOTE]
> Se as instâncias de SQL Server estiverem em vários grupos de recursos e regiões, execute cada etapa duas vezes, uma vez em cada grupo de recursos.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Etapa 1: criar o balanceador de carga e configurar o endereço IP
Primeiro, crie o balanceador de carga. 

1. No portal do Azure, abra o grupo de recursos que contém as máquinas virtuais SQL Server. 

2. No grupo de recursos, clique em **Adicionar**.

3. Pesquise o **balanceador de carga** e, em seguida, nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.

4. Na folha **Load Balancer** , clique em **criar**.

5. Na caixa de diálogo **criar balanceador de carga** , configure o balanceador de carga da seguinte maneira:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interno**: a maioria das implementações usa um balanceador de carga interno, que permite que os aplicativos na mesma rede virtual se conectem ao grupo de disponibilidade.  </br> **Externo**: permite que os aplicativos se conectem ao grupo de disponibilidade por meio de uma conexão de Internet pública. |
   | **Rede virtual** |Selecione a rede virtual na qual as instâncias do SQL Server estão. |
   | **Sub-rede** |Selecione a sub-rede na qual as instâncias de SQL Server estão. |
   | **Atribuição de endereço IP** |**Auto-estática** |
   | **Endereço IP privado** |Especifique um endereço IP disponível da sub-rede. Use esse endereço IP ao criar um ouvinte no cluster. Em um script do PowerShell, mais adiante neste artigo, use esse endereço para a variável `$ILBIP`. |
   | **Subscrição** |Se você tiver várias assinaturas, esse campo poderá ser exibido. Selecione a assinatura que você deseja associar a este recurso. Normalmente, é a mesma assinatura que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual as instâncias do SQL Server estão. |
   | **Localização** |Selecione o local do Azure no qual as instâncias do SQL Server estão. |

6. Clique em **Criar**. 

O Azure cria o balanceador de carga. O balanceador de carga pertence a uma rede, sub-rede, grupo de recursos e local específicos. Depois que o Azure concluir a tarefa, verifique as configurações do balanceador de carga no Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Etapa 2: configurar o pool de back-ends
O Azure chama o *pool de back*-end do pool de endereços de back-ends. Nesse caso, o pool de back-ends é o endereço das duas instâncias de SQL Server em seu grupo de disponibilidade. 

1. Em seu grupo de recursos, clique no balanceador de carga que você criou. 

2. Em **configurações**, clique em **pools de back-end**.

3. Em **pools de back-end**, clique em **Adicionar** para criar um pool de endereços de back-end. 

4. Em **Adicionar pool de back-end**, em **nome**, digite um nome para o pool de back-ends.

5. Em **máquinas virtuais**, clique em **Adicionar uma máquina virtual**. 

6. Em **escolher máquinas virtuais**, clique em **escolher um conjunto de disponibilidade**e especifique o conjunto de disponibilidade ao qual as máquinas virtuais SQL Server pertencem.

7. Depois de ter escolhido o conjunto de disponibilidade, clique em **escolher as máquinas virtuais**, selecione as duas máquinas virtuais que hospedam as instâncias de SQL Server no grupo de disponibilidade e, em seguida, clique em **selecionar**. 

8. Clique em **OK** para fechar as folhas para **escolher máquinas virtuais**e **Adicionar pool de back-end**. 

O Azure atualiza as configurações para o pool de endereços de back-end. Agora seu conjunto de disponibilidade tem um pool de duas instâncias de SQL Server.

### <a name="step-3-create-a-probe"></a>Etapa 3: criar uma investigação
A investigação define como o Azure verifica qual das instâncias de SQL Server atualmente possui o ouvinte do grupo de disponibilidade. O Azure investiga o serviço com base no endereço IP em uma porta que você define ao criar a investigação.

1. Na folha **configurações** do balanceador de carga, clique em **investigações de integridade**. 

2. Na folha **investigações de integridade** , clique em **Adicionar**.

3. Configure a investigação na folha **Adicionar investigação** . Use os seguintes valores para configurar a investigação:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a investigação. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Você pode usar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limite não íntegro** |*2* |

4.  Clique em **OK**. 

> [!NOTE]
> Verifique se a porta que você especificou está aberta no firewall de ambas as instâncias de SQL Server. Ambas as instâncias exigem uma regra de entrada para a porta TCP que você usa. Para obter mais informações, consulte [Adicionar ou editar regra de firewall](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

O Azure cria a investigação e a usa para testar qual SQL Server instância tem o ouvinte para o grupo de disponibilidade.

### <a name="step-4-set-the-load-balancing-rules"></a>Etapa 4: definir as regras de balanceamento de carga
As regras de balanceamento de carga configuram como o balanceador de carga roteia o tráfego para as instâncias de SQL Server. Para esse balanceador de carga, você habilita o retorno de servidor direto porque apenas uma das duas instâncias de SQL Server possui o recurso de ouvinte do grupo de disponibilidade por vez.

1. Na folha **configurações** do balanceador de carga, clique em **regras de balanceamento de carga**. 

2. Na folha **regras de balanceamento de carga** , clique em **Adicionar**.

3. Na folha **adicionar regras de balanceamento de carga** , configure a regra de balanceamento de carga. Use as seguintes configurações: 

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta de back-end** |*1433*. esse valor é ignorado porque essa regra usa **IP flutuante (retorno de servidor direto)** . |
   | **Provas** |Use o nome da investigação que você criou para esse balanceador de carga. |
   | **Persistência da sessão** |**Nenhum** |
   | **Tempo limite de ociosidade (minutos)** |*4* |
   | **IP flutuante (retorno de servidor direto)** |**Ativado** |

   > [!NOTE]
   > Talvez seja necessário rolar a folha para baixo para exibir todas as configurações.
   > 

4. Clique em **OK**. 
5. O Azure configura a regra de balanceamento de carga. Agora o balanceador de carga está configurado para rotear o tráfego para a instância de SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um balanceador de carga que se conecta a ambos os computadores SQL Server. O balanceador de carga também contém um endereço IP para o SQL Server Always On ouvinte do grupo de disponibilidade, para que qualquer computador possa responder às solicitações dos grupos de disponibilidade.

> [!NOTE]
> Se suas instâncias de SQL Server estiverem em duas regiões separadas, repita as etapas na outra região. Cada região requer um balanceador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para usar o endereço IP do balanceador de carga
A próxima etapa é configurar o ouvinte no cluster e colocar o ouvinte online. Faça o seguinte: 

1. Crie o ouvinte do grupo de disponibilidade no cluster de failover. 

2. Coloque o ouvinte online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Etapa 5: criar o ouvinte do grupo de disponibilidade no cluster de failover
Nesta etapa, você cria manualmente o ouvinte do grupo de disponibilidade em Gerenciador de Cluster de Failover e SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verificar a configuração do ouvinte

Se os recursos e as dependências do cluster estiverem configurados corretamente, você poderá exibir o ouvinte no SQL Server Management Studio. Para definir a porta do ouvinte, faça o seguinte:

1. Inicie o SQL Server Management Studio e, em seguida, conecte-se à réplica primária.

2. Vá para **alta disponibilidade AlwaysOn** > **grupos de disponibilidade** > **ouvintes do grupo de disponibilidade**.  
    Agora você deve ver o nome do ouvinte que você criou em Gerenciador de Cluster de Failover. 

3. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

4. Na caixa **porta** , especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e clique em **OK**.

Agora você tem um grupo de disponibilidade em máquinas virtuais do Azure em execução no modo do Gerenciador de recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte
Teste a conexão fazendo o seguinte:

1. RDP para uma instância de SQL Server que está na mesma rede virtual, mas não possui a réplica. Esse servidor pode ser a outra instância de SQL Server no cluster.

2. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:
   
        sqlcmd -S <listenerName> -E

A conexão SQLCMD se conecta automaticamente à instância de SQL Server que hospeda a réplica primária. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Criar um endereço IP para um grupo de disponibilidade adicional

Cada grupo de disponibilidade usa um ouvinte separado. Cada ouvinte tem seu próprio endereço IP. Use o mesmo balanceador de carga para manter o endereço IP para ouvintes adicionais. Depois de criar um grupo de disponibilidade, adicione o endereço IP ao balanceador de carga e, em seguida, configure o ouvinte.

Para adicionar um endereço IP a um balanceador de carga com o portal do Azure, faça o seguinte:

1. No portal do Azure, abra o grupo de recursos que contém o balanceador de carga e clique no balanceador de carga. 

2. Em **configurações**, clique em **pool de IPS de front-end**e clique em **Adicionar**. 

3. Em **Adicionar endereço IP de frontend**, atribua um nome para o front-end. 

4. Verifique se a **rede virtual** e a **sub-rede** são as mesmas que as instâncias de SQL Server.

5. Defina o endereço IP para o ouvinte. 
   
   >[!TIP]
   >Você pode definir o endereço IP como estático e digitar um endereço que não esteja sendo usado atualmente na sub-rede. Como alternativa, você pode definir o endereço IP como dinâmico e salvar o novo pool de IPS de front-end. Quando você fizer isso, a portal do Azure atribuirá automaticamente um endereço IP disponível ao pool. Em seguida, você pode reabrir o pool de IPS de front-end e alterar a atribuição para estático. 

6. Salve o endereço IP do ouvinte. 

7. Adicione uma investigação de integridade usando as seguintes configurações:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a investigação.
   |**Protocolo** |TCP
   |**Porta** |Uma porta TCP não usada, que deve estar disponível em todas as máquinas virtuais. Ele não pode ser usado para nenhuma outra finalidade. Dois ouvintes não podem usar a mesma porta de investigação. 
   |**Intervalo** |A quantidade de tempo entre as tentativas de investigação. Use o padrão (5).
   |**Limite não íntegro** |O número de limites consecutivos que devem falhar antes que uma máquina virtual seja considerada não íntegra.

8. Clique em **OK** para salvar a investigação. 

9. Crie uma regra de balanceamento de carga. Clique em **regras de balanceamento de carga**e clique em **Adicionar**.

10. Configure a nova regra de balanceamento de carga usando as seguintes configurações:

    |Definição |Valor
    |:-----|:----
    |**Nome** |Um nome para identificar a regra de balanceamento de carga. 
    |**Endereço IP de front-end** |Selecione o endereço IP que você criou. 
    |**Protocolo** |TCP
    |**Porta** |Use a porta que as instâncias de SQL Server estão usando. Uma instância padrão usa a porta 1433, a menos que você a tenha alterado. 
    |**Porta de back-end** |Use o mesmo valor que a **porta**.
    |**Pool de back-end** |O pool que contém as máquinas virtuais com as instâncias de SQL Server. 
    |**Investigação de integridade** |Escolha a investigação que você criou.
    |**Persistência da sessão** |Nenhum
    |**Tempo limite de ociosidade (minutos)** |Padrão (4)
    |**IP flutuante (retorno de servidor direto)** | Ativado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configurar o grupo de disponibilidade para usar o novo endereço IP

Para concluir a configuração do cluster, repita as etapas que você seguiu quando fez o primeiro grupo de disponibilidade. Ou seja, configure o [cluster para usar o novo endereço IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Depois de adicionar um endereço IP para o ouvinte, configure o grupo de disponibilidade adicional fazendo o seguinte: 

1. Verifique se a porta de investigação para o novo endereço IP está aberta em ambas as máquinas virtuais SQL Server. 

2. [No Gerenciador de cluster, adicione o ponto de acesso para cliente](#addcap).

3. [Configure o recurso de IP para o grupo de disponibilidade](#congroup).

   >[!IMPORTANT]
   >Ao criar o endereço IP, use o endereço IP que você adicionou ao balanceador de carga.  

4. [Torne o recurso de grupo de disponibilidade SQL Server dependente do ponto de acesso para cliente](#dependencyGroup).

5. [Torne o recurso de ponto de acesso para cliente dependente do endereço IP](#listname).
 
6. [Defina os parâmetros de cluster no PowerShell](#setparam).

Depois de configurar o grupo de disponibilidade para usar o novo endereço IP, configure a conexão com o ouvinte. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Adicionar regra de balanceamento de carga para o grupo de disponibilidade distribuído

Se um grupo de disponibilidade participar de um grupo de disponibilidade distribuído, o balanceador de carga precisará de uma regra adicional. Essa regra armazena a porta usada pelo ouvinte do grupo de disponibilidade distribuído.

>[!IMPORTANT]
>Esta etapa se aplica somente se o grupo de disponibilidade participar de um [grupo de disponibilidade distribuído](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Em cada servidor que participa do grupo de disponibilidade distribuído, crie uma regra de entrada na porta TCP do ouvinte do grupo de disponibilidade distribuído. Em muitos exemplos, a documentação usa o 5022. 

1. No portal do Azure, clique no balanceador de carga e clique em **regras de balanceamento de carga**e, em seguida, clique em **+ Adicionar**. 

1. Crie a regra de balanceamento de carga com as seguintes configurações:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a regra de balanceamento de carga para o grupo de disponibilidade distribuído. 
   |**Endereço IP de front-end** |Use o mesmo endereço IP de front-end que o grupo de disponibilidade.
   |**Protocolo** |TCP
   |**Porta** |5022-a porta do [ouvinte de ponto de extremidade do grupo de disponibilidade distribuído](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Pode ser qualquer porta disponível.  
   |**Porta de back-end** | 5022-use o mesmo valor que a **porta**.
   |**Pool de back-end** |O pool que contém as máquinas virtuais com as instâncias de SQL Server. 
   |**Investigação de integridade** |Escolha a investigação que você criou.
   |**Persistência da sessão** |Nenhum
   |**Tempo limite de ociosidade (minutos)** |Padrão (4)
   |**IP flutuante (retorno de servidor direto)** | Ativado

Repita essas etapas para o balanceador de carga nos outros grupos de disponibilidade que participam dos grupos de disponibilidade distribuídos.

Se você estiver restringindo o acesso com um grupo de segurança de rede do Azure, verifique se as regras de permissão incluem os endereços IP de VM de back-end SQL Server e os endereços IP flutuantes do balanceador de carga para o ouvinte AG e o endereço IP principal do cluster, se aplicável.

## <a name="next-steps"></a>Passos seguintes

- [Configurar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md)
