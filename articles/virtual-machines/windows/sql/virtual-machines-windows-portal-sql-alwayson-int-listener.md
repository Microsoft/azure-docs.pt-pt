---
title: Criar um serviço de escuta do grupo de disponibilidade do SQL Server em máquinas virtuais do Azure | Documentos da Microsoft
description: Instruções passo a passo para a criação de um serviço de escuta para um grupo de disponibilidade Always On do SQL Server em máquinas virtuais do Azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: 3b90ae3e9808b22b6d6c41e3ac11bec0293bd4bf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107887"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configurar um balanceador de carga para um grupo de disponibilidade Always On no Azure
Este artigo explica como criar um balanceador de carga para um grupo de disponibilidade Always On do SQL Server em máquinas virtuais do Azure que estejam a executar com o Azure Resource Manager. Um grupo de disponibilidade necessita de um balanceador de carga quando são de instâncias do SQL Server em máquinas virtuais do Azure. O Balanceador de carga armazena o endereço IP para o serviço de escuta do grupo de disponibilidade. Se a um grupo de disponibilidade se estende por várias regiões, cada região tem um balanceador de carga.

Para concluir essa tarefa, tem de ter um grupo de disponibilidade do SQL Server implementado em máquinas virtuais do Azure que estejam a executar com o Resource Manager. Ambas as máquinas virtuais do SQL Server têm de pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o [modelo de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gestor de recursos. Este modelo cria automaticamente um balanceador de carga interno para. 

Se preferir, pode [configurar manualmente um grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Este artigo requer que os seus grupos de disponibilidade já estão configurados.  

Tópicos relacionados incluem:

* [Configurar grupos de disponibilidade Always On na VM do Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Examinando neste artigo, pode criar e configurar um balanceador de carga no portal do Azure. Depois do processo estiver concluído, configura o cluster para utilizar o endereço IP do Balanceador de carga para o serviço de escuta do grupo de disponibilidade.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o Balanceador de carga no portal do Azure
Nessa parte da tarefa, faça o seguinte:

1. No portal do Azure, crie o Balanceador de carga e configurar o endereço IP.
2. Configure o conjunto de back-end.
3. Crie a sonda. 
4. Defina a regras de balanceamento de carga.

> [!NOTE]
> Se a instâncias do SQL Server estão em vários grupos de recursos e regiões, de executar cada passo duas vezes, uma vez em cada grupo de recursos.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Passo 1: Criar o Balanceador de carga e configurar o endereço IP
Primeiro, crie o Balanceador de carga. 

1. No portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. 

2. No grupo de recursos, clique em **adicionar**.

3. Procure **Balanceador de carga** e, em seguida, nos resultados da pesquisa, selecione **Balanceador de carga**, que é publicado pela **Microsoft**.

4. Sobre o **Balanceador de carga** painel, clique em **criar**.

5. Na **criar Balanceador de carga** diálogo caixa, configurar o Balanceador de carga da seguinte forma:

   | Definição | Value |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o Balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interno**: A maioria das implementações utilizar um balanceador de carga interno, que permite que aplicativos dentro da mesma rede virtual ligar ao grupo de disponibilidade.  </br> **Externo**: Permite que os aplicativos para se ligar ao grupo de disponibilidade por meio de uma ligação de Internet pública. |
   | **Rede virtual** |Selecione a rede virtual que as instâncias do SQL Server estão em. |
   | **Sub-rede** |Selecione a sub-rede que instâncias do SQL Server estão em. |
   | **Atribuição de endereços IP** |**Estático** |
   | **Endereço IP privado** |Especifique um endereço IP disponível na sub-rede. Utilize este endereço IP quando criar um serviço de escuta no cluster. Num script do PowerShell, neste artigo, utilize este endereço para o `$ILBIP` variável. |
   | **Subscrição** |Se tiver várias subscrições, este campo poderá ser apresentada. Selecione a subscrição que pretende associar este recurso. Normalmente é a mesma subscrição que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos que as instâncias do SQL Server estão em. |
   | **Localização** |Selecione a localização do Azure que as instâncias do SQL Server estão em. |

6. Clique em **Criar**. 

O Azure cria o Balanceador de carga. O Balanceador de carga pertence a uma rede específica, sub-rede, grupo de recursos e localização. Depois do Azure concluir a tarefa, verifique se as definições do Balanceador de carga no Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Passo 2: Configurar o conjunto de back-end
Azure chama o conjunto de endereços de back-end *conjunto back-end*. Neste caso, o conjunto de back-end é os endereços das duas instâncias do SQL Server no seu grupo de disponibilidade. 

1. No seu grupo de recursos, clique o Balanceador de carga que criou. 

2. No **configurações**, clique em **conjuntos back-end**.

3. No **conjuntos de back-end**, clique em **Add** para criar um conjunto de endereços de back-end. 

4. No **adicionar conjunto de back-end**, em **nome**, escreva um nome para o conjunto de back-end.

5. Sob **máquinas virtuais**, clique em **adicionar uma máquina virtual**. 

6. Sob **escolher máquinas virtuais**, clique em **escolher um conjunto de disponibilidade**e, em seguida, especifique o conjunto de disponibilidade que as máquinas virtuais do SQL Server pertencem a.

7. Depois de escolher o conjunto de disponibilidade, clique em **escolha as máquinas virtuais**, selecione as duas máquinas virtuais que alojam instâncias do SQL Server no grupo de disponibilidade e, em seguida, clique em **selecione**. 

8. Clique em **OK** para fechar os painéis para **escolher máquinas virtuais**, e **adicionar conjunto de back-end**. 

Azure atualiza as definições para o conjunto de endereços de back-end. Agora, o seu conjunto de disponibilidade tem um conjunto de duas instâncias do SQL Server.

### <a name="step-3-create-a-probe"></a>Passo 3: Criar uma sonda
A sonda define como o Azure verifica quais das instâncias do SQL Server atualmente possui o serviço de escuta do grupo de disponibilidade. Azure sondas de serviço com base no endereço IP numa porta que definem quando criar a sonda.

1. No balanceador de carga **configurações** painel, clique em **sondas de estado de funcionamento**. 

2. Sobre o **sondas de estado de funcionamento** painel, clique em **Add**.

3. Configurar a sonda no **adicionar sonda** painel. Utilize os seguintes valores para configurar a sonda:

   | Definição | Value |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Pode utilizar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limiar de mau estado de funcionamento** |*2* |

4.  Clique em **OK**. 

> [!NOTE]
> Certifique-se de que a porta especificada está aberta na firewall de ambas as instâncias do SQL Server. Ambas as instâncias requerem uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou Editar regra de Firewall](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

O Azure cria a pesquisa e, em seguida, utiliza-o para testar a instância do SQL Server tem o serviço de escuta do grupo de disponibilidade.

### <a name="step-4-set-the-load-balancing-rules"></a>Passo 4: Definir a regras de balanceamento de carga
As regras de balanceamento de carga configurar como o Balanceador de carga encaminha o tráfego para instâncias do SQL Server. Para este Balanceador de carga, habilitar a devolução direta do servidor porque apenas uma das duas instâncias de SQL Server é o proprietário do recurso de serviço de escuta do grupo de disponibilidade de cada vez.

1. No balanceador de carga **configurações** painel, clique em **regras de balanceamento de carga**. 

2. Sobre o **regras de balanceamento de carga** painel, clique em **Add**.

3. Sobre o **adicionar regras de balanceamento de carga** painel, configurar regra de balanceamento de carga. Utilize as seguintes definições: 

   | Definição | Value |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta de back-end** |*1433*. Este valor é ignorado porque esta regra utiliza **IP flutuante (devolução direta do servidor)**. |
   | **Sonda** |Utilize o nome da sonda que criou para este Balanceador de carga. |
   | **Persistência da sessão** |**Nenhum** |
   | **Tempo limite de inatividade (minutos)** |*4* |
   | **Vírgula flutuante (devolução direta do servidor) de IP** |**Ativado** |

   > [!NOTE]
   > Poderá ter de deslocar para baixo do painel para ver todas as definições.
   > 

4. Clique em **OK**. 
5. O Azure configura a regra de balanceamento de carga. Agora, o Balanceador de carga está configurado para encaminhar o tráfego para a instância do SQL Server que aloja o serviço de escuta do grupo de disponibilidade. 

Neste momento, o grupo de recursos tem um balanceador de carga que se liga a ambas as máquinas do SQL Server. O Balanceador de carga também contém um endereço IP para a SQL Server Always On escuta do grupo disponibilidade, para que a máquina pode responder a pedidos para os grupos de disponibilidade.

> [!NOTE]
> Se as instâncias do SQL Server estão em duas regiões separadas, repita os passos na outra região. Cada região requer um balanceador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurar o cluster para utilizar o endereço IP do Balanceador de carga
A próxima etapa é configurar o serviço de escuta no cluster e colocar o serviço de escuta online. Faça o seguinte: 

1. Crie o serviço de escuta do grupo de disponibilidade no cluster de ativação pós-falha. 

2. Coloque online o serviço de escuta.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Passo 5: Criar o serviço de escuta do grupo de disponibilidade no cluster de ativação pós-falha
Neste passo, vai criar manualmente o serviço de escuta do grupo de disponibilidade no Gestor de clusters de ativação pós-falha e o SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verificar a configuração do serviço de escuta

Se os recursos do cluster e as dependências estão configuradas corretamente, deve conseguir ver o serviço de escuta no SQL Server Management Studio. Para definir a porta do serviço de escuta, faça o seguinte:

1. Inicie o SQL Server Management Studio e, em seguida, ligue para a réplica primária.

2. Aceda a **AlwaysOn elevada disponibilidade** > **grupos de disponibilidade** > **serviços de escuta do grupo de disponibilidade**.  
    Agora, deverá ver o nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha. 

3. O nome do serviço de escuta com o botão direito e, em seguida, clique em **propriedades**.

4. Na **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade com o $EndpointPort que utilizou anteriormente (era a predefinição de 1433) e, em seguida, clique em **OK**.

Agora tem um grupo de disponibilidade em máquinas virtuais do Azure em execução no modo Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação ao serviço de escuta
Teste a ligação ao fazer o seguinte:

1. RDP para uma instância do SQL Server que está na mesma rede virtual, mas não é proprietário a réplica. Este servidor pode ser outra instância do SQL Server no cluster.

2. Uso **sqlcmd** utilitário para testar a ligação. Por exemplo, o seguinte script estabelece uma **sqlcmd** ligação para a réplica primária por meio do serviço de escuta com a autenticação do Windows:
   
        sqlcmd -S <listenerName> -E

A ligação de SQLCMD liga-se automaticamente à instância do SQL Server que aloja a réplica primária. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Criar um endereço IP para um grupo de disponibilidade adicionais

Cada grupo de disponibilidade utiliza um serviço de escuta separado. Cada serviço de escuta tem seu próprio endereço IP. Utilize o mesmo Balanceador de carga para manter o endereço IP para as escutas adicionais. Depois de criar um grupo de disponibilidade, adicione o endereço IP para o Balanceador de carga e, em seguida, configure o serviço de escuta.

Para adicionar um endereço IP a um balanceador de carga com o portal do Azure, efetue o seguinte:

1. No portal do Azure, abra o grupo de recursos que contém o Balanceador de carga e, em seguida, clique no balanceador de carga. 

2. Sob **configurações**, clique em **conjunto IP de front-end**e, em seguida, clique em **Add**. 

3. Sob **adicione o endereço IP de front-end**, atribuir um nome para o front-end. 

4. Certifique-se de que o **rede Virtual** e o **sub-rede** são as mesmas que as instâncias do SQL Server.

5. Defina o endereço IP para o serviço de escuta. 
   
   >[!TIP]
   >Pode definir o endereço IP estático e escreva um endereço que não é atualmente utilizado na sub-rede. Em alternativa, pode definir o endereço IP dinâmico e guardar o novo conjunto IP Front-end. Ao fazê-lo, o portal do Azure atribui automaticamente um endereço IP disponível para o conjunto. Pode, em seguida, volte a abrir o conjunto IP Front-end e alterar a atribuição para estático. 

6. Guarde o endereço IP para o serviço de escuta. 

7. Adicione uma sonda de estado de funcionamento, utilizando as seguintes definições:

   |Definição |Value
   |:-----|:----
   |**Nome** |Um nome para identificar a sonda.
   |**Protocolo** |TCP
   |**Porta** |Uma porta não utilizada TCP, que tem de estar disponível em todas as máquinas virtuais. Ele não pode ser usado para qualquer outra finalidade. Não existem dois serviços de escuta podem utilizar a mesma porta de sonda. 
   |**Intervalo** |O período de tempo entre tentativas da sonda. Utilize a predefinição (5).
   |**Limiar de mau estado de funcionamento** |O número de limiares consecutivas que devem falhar antes de uma máquina virtual é considerada em mau estado de funcionamento.

8. Clique em **OK** para guardar a sonda. 

9. Crie uma regra de balanceamento de carga. Clique em **regras de balanceamento de carga**e, em seguida, clique em **Add**.

10. Configure a carga de nova regra de balanceamento utilizando as seguintes definições:

    |Definição |Value
    |:-----|:----
    |**Nome** |Um nome para identificar a regra de balanceamento de carga. 
    |**Endereço IP de front-end** |Selecione o endereço IP que criou. 
    |**Protocolo** |TCP
    |**Porta** |Utilize a porta que instâncias do SQL Server estiver a utilizar. Uma instância predefinida utiliza a porta 1433, a menos que tenha alterado. 
    |**Porta de back-end** |Utilizar o mesmo valor que **porta**.
    |**Conjunto back-end** |O conjunto que contém as máquinas virtuais com as instâncias do SQL Server. 
    |**Sonda de estado de funcionamento** |Escolha a sonda que criou.
    |**Persistência da sessão** |Nenhuma
    |**Tempo limite de inatividade (minutos)** |Padrão (4)
    |**Vírgula flutuante (devolução direta do servidor) de IP** | Ativado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configurar o grupo de disponibilidade para utilizar o novo endereço IP

Para concluir a configuração do cluster, repita os passos que seguiu quando efetuou o primeiro grupo de disponibilidade. Ou seja, configura a [cluster para utilizar o novo endereço IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Depois de ter adicionado um endereço IP para o serviço de escuta, configure o grupo de disponibilidade adicional efetuando o seguinte procedimento: 

1. Certifique-se de que a porta de sonda para o novo endereço IP está aberta em ambas as máquinas virtuais do SQL Server. 

2. [No Gestor de clusters, adicionar o ponto de acesso de cliente](#addcap).

3. [Configure o recurso IP para o grupo de disponibilidade](#congroup).

   >[!IMPORTANT]
   >Ao criar o endereço IP, utilize o endereço IP que adicionou ao balanceador de carga.  

4. [Tornar o recurso do grupo de disponibilidade do SQL Server dependentes no ponto de acesso de cliente](#dependencyGroup).

5. [Marca o recurso de ponto de acesso de cliente dependente no endereço IP](#listname).
 
6. [Definir os parâmetros de cluster no PowerShell](#setparam).

Depois de configurar o grupo de disponibilidade para utilizar o novo endereço IP, configure a ligação ao serviço de escuta. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Adicionar regra de grupo de disponibilidade distribuída de balanceamento de carga

Se um grupo de disponibilidade participa de um grupo de disponibilidade distribuída, o Balanceador de carga tem uma regra adicional. Esta regra armazena a porta usada pelo serviço de escuta do grupo de disponibilidade distribuída.

>[!IMPORTANT]
>Este passo aplica-se apenas se o grupo de disponibilidade participa de um [grupo de disponibilidade distribuída](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Em cada servidor que participa no grupo de disponibilidade distribuída, crie uma regra de entrada sobre o serviço de escuta de grupo de disponibilidade distribuída a porta TCP. Em muitos exemplos, documentação utiliza 5022. 

1. No portal do Azure, clique no balanceador de carga e clique em **regras de balanceamento de carga**e, em seguida, clique em **+ adicionar**. 

1. Crie a balanceamento de carga regra com as seguintes definições:

   |Definição |Value
   |:-----|:----
   |**Nome** |Um nome para identificar a regra para o grupo de disponibilidade distribuída de balanceamento de carga. 
   |**Endereço IP de front-end** |Utilize o mesmo endereço IP de front-end como o grupo de disponibilidade.
   |**Protocolo** |TCP
   |**Porta** |5022 - a porta para o [serviço de escuta de ponto final de grupo de disponibilidade distribuída](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Pode ser qualquer porta disponível.  
   |**Porta de back-end** | 5022 - utilize o mesmo valor como **porta**.
   |**Conjunto back-end** |O conjunto que contém as máquinas virtuais com as instâncias do SQL Server. 
   |**Sonda de estado de funcionamento** |Escolha a sonda que criou.
   |**Persistência da sessão** |Nenhuma
   |**Tempo limite de inatividade (minutos)** |Padrão (4)
   |**Vírgula flutuante (devolução direta do servidor) de IP** | Ativado

Repita estes passos para o Balanceador de carga em outros grupos de disponibilidade que participam de grupos de disponibilidade distribuída.

Se estiver a restrição do acesso com um grupo de segurança de rede do Azure, certifique-se de que as regras de permissão incluem os endereços de IP da VM do SQL Server back-end e o Balanceador de carga IP flutuante endereços para o serviço de escuta de AG e o endereço IP de núcleo de cluster, se aplicável.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar um grupo de disponibilidade Always On do SQL Server em máquinas virtuais do Azure em regiões diferentes](virtual-machines-windows-portal-sql-availability-group-dr.md)
