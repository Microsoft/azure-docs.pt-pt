---
title: Configure os ouvintes do grupo de disponibilidade & balanceador de carga (portal Azure)
description: Instruções passo a passo para criar um ouvinte para um grupo de disponibilidade Always On para O Servidor SQL em máquinas virtuais Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096329"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Configure um balancer de carga para um grupo de disponibilidade em VMs de servidor Estivado Azure
Este artigo explica como criar um balancer de carga para um grupo de disponibilidade SQL Server Always On em máquinas virtuais Azure que estão a funcionar com o Gestor de Recursos Azure. Um grupo de disponibilidade requer um equilíbrio de carga quando as instâncias do SQL Server estão em máquinas virtuais Azure. O equilibrista de carga armazena o endereço IP para o ouvinte do grupo de disponibilidade. Se um grupo de disponibilidade abrange várias regiões, cada região precisa de um equilibrador de carga.

Para completar esta tarefa, é necessário ter um grupo de disponibilidade do SQL Server implantado em máquinas virtuais Azure que estejam a funcionar com o Gestor de Recursos. Ambas as máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o modelo da [Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Gestor de Recursos. Este modelo cria automaticamente um equilibrador de carga interno para si. 

Se preferir, pode [configurar manualmente um grupo de disponibilidade](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Este artigo requer que os seus grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configure Sempre em grupos de disponibilidade em Azure VM (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ao percorrer este artigo, cria-se e configura um equilibrador de carga no portal Azure. Após a conclusão do processo, configura o cluster para utilizar o endereço IP do balanceor de carga para o ouvinte do grupo de disponibilidade.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o equilibrador de carga no portal Azure
Nesta parte da tarefa, faça o seguinte:

1. No portal Azure, crie o equilibrador de carga e configure o endereço IP.
2. Configure a piscina traseira.
3. Crie a sonda. 
4. Detete as regras de equilíbrio de carga.

> [!NOTE]
> Se os casos do SQL Server estiverem em vários grupos e regiões de recursos, execute cada passo duas vezes, uma vez em cada grupo de recursos.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Passo 1: Criar o equilibrador de carga e configurar o endereço IP
Primeiro, crie o equilibrador de carga. 

1. No portal Azure, abra o grupo de recursos que contém as máquinas virtuais Do Servidor SQL. 

2. No grupo de recursos, clique em **Adicionar**.

3. Procure um **equilibrador** de carga e, em seguida, nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.

4. Na lâmina **do Balancer de carga,** clique em **Criar**.

5. Na caixa de diálogo criar um **equilíbrio de carga,** configure o equilibrador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o equilibrador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interna**: A maioria das implementações utiliza um equilibrador de carga interno, que permite que aplicações dentro da mesma rede virtual se conectem ao grupo de disponibilidade.  </br> **Externo**: Permite que as aplicações se conectem ao grupo de disponibilidade através de uma ligação à Internet pública. |
   | **Rede virtual** |Selecione a rede virtual em que os casos do Servidor SQL estão. |
   | **Sub-rede** |Selecione a sub-rede em que as instâncias do Servidor SQL estão dentro. |
   | **Atribuição de endereçoIP** |**Estático** |
   | **Endereço IP privado** |Especifique um endereço IP disponível a partir da sub-rede. Utilize este endereço IP quando criar um ouvinte no cluster. Num script PowerShell, mais tarde neste artigo, `$ILBIP` utilize este endereço para a variável. |
   | **Assinatura** |Se tiver várias subscrições, este campo pode aparecer. Selecione a subscrição que pretende associar a este recurso. É normalmente a mesma subscrição que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos em que os casos do Servidor SQL estão. |
   | **Localização** |Selecione a localização Azure onde os casos do Servidor SQL estão. |

6. Clique em **Criar**. 

Azure cria o equilibrador de carga. O equilibrador de carga pertence a uma rede específica, subnet, grupo de recursos e localização. Depois do Azure completar a tarefa, verifique as definições do equilibrador de carga em Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Passo 2: Configure a piscina traseira
O Azure chama a piscina de back-end da piscina de endereços *traseiros.* Neste caso, o pool back-end são os endereços das duas instâncias do SQL Server no seu grupo de disponibilidade. 

1. No seu grupo de recursos, clique no equilibrador de carga que criou. 

2. Em **Definições,** clique em **piscinas backend**.

3. Nas **piscinas Backend,** clique em **Adicionar** para criar uma piscina de endereços de back-end. 

4. Em **Adicionar piscina de backend**, em **nome**, digite um nome para a piscina de back-end.

5. Em **máquinas virtuais,** clique **em Adicionar uma máquina virtual**. 

6. Em **'Escolha as máquinas virtuais',** clique em Escolher um conjunto de **disponibilidades**e, em seguida, especificar o conjunto de disponibilidade a que as máquinas virtuais do Servidor SQL pertencem.

7. Depois de ter escolhido o conjunto de disponibilidade, clique **em Escolher as máquinas virtuais,** selecione as duas máquinas virtuais que acolhem as instâncias do SQL Server no grupo de disponibilidade e, em seguida, clique em **Selecionar**. 

8. Clique **em OK** para fechar as lâminas para Escolher **máquinas virtuais**e adicionar piscina de **backend**. 

O Azure atualiza as definições para o conjunto de endereços de back-end. Agora o seu conjunto de disponibilidade tem um conjunto de duas instâncias do SQL Server.

### <a name="step-3-create-a-probe"></a>Passo 3: Criar uma sonda
A sonda define como o Azure verifica qual dos casos do SQL Server detém atualmente o ouvinte do grupo de disponibilidade. O Azure sonda o serviço com base no endereço IP de uma porta que define quando cria a sonda.

1. No balanço de carga **Configuração** da lâmina, clique em **sondas de saúde**. 

2. Na lâmina das **sondas Health,** clique em **Adicionar**.

3. Configure a sonda na lâmina da **sonda Adicionar.** Utilize os seguintes valores para configurar a sonda:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto representando a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porto** |Pode utilizar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limiar com funcionamento incorreto** |*2* |

4.  Clique em **OK**. 

> [!NOTE]
> Certifique-se de que a porta que especifica está aberta na firewall de ambas as instâncias do Servidor SQL. Ambos os casos requerem uma regra de entrada para a porta TCP que utiliza. Para mais informações, consulte [Adicionar ou Editar a Regra da Firewall](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

O Azure cria a sonda e depois usa-a para testar qual a instância do SQL Server que tem o ouvinte para o grupo de disponibilidade.

### <a name="step-4-set-the-load-balancing-rules"></a>Passo 4: Definir as regras de equilíbrio da carga
As regras de equilíbrio de carga configuram como o equilibrador de carga direciona o tráfego para as instâncias do Servidor SQL. Para este balanceor de carga, permite a devolução do servidor direto porque apenas uma das duas instâncias do SQL Server possui o recurso de ouvinte de grupo de disponibilidade de cada vez.

1. No balanço de carga **Configuração** da lâmina, clique em regras de **equilíbrio de carga**. 

2. Na lâmina de regras de **equilíbrio da carga,** clique em **Adicionar**.

3. Na lâmina de equilíbrio de **carga Adicionar,** configure a regra de equilíbrio da carga. Utilize as seguintes definições: 

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de equilíbrio da carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porto** |*1433* |
   | **Porta back-end** |*1433*. Este valor é ignorado porque esta regra utiliza **IP flutuante (retorno do servidor direto)**. |
   | **Sonda** |Use o nome da sonda que criou para este equilibrador de carga. |
   | **Persistência da sessão** |**Nenhum** |
   | **Tempo limite (minutos)** |*4* |
   | **IP flutuante (devolução do servidor direto)** |**Ativado** |

   > [!NOTE]
   > Pode ter de rolar pela lâmina para ver todas as definições.
   > 

4. Clique em **OK**. 
5. Azure configura a regra de equilíbrio de carga. Agora, o equilibrista de carga está configurado para encaminhar o tráfego para a instância SQL Server que acolhe o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um equilibrador de carga que se conecta a ambas as máquinas SQL Server. O balanceor de carga também contém um endereço IP para o ouvinte do grupo SQL Server Always On, para que qualquer uma das máquinas possa responder aos pedidos dos grupos de disponibilidade.

> [!NOTE]
> Se os seus casos de Servidor SQL estiverem em duas regiões distintas, repita os passos na outra região. Cada região requer um equilibrador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure o cluster para utilizar o endereço IP do equilibrador de carga
O próximo passo é configurar o ouvinte no cluster e colocar o ouvinte online. Faça o seguinte: 

1. Crie o ouvinte do grupo de disponibilidade no cluster failover. 

2. Traga o ouvinte on-line.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Passo 5: Criar o ouvinte do grupo de disponibilidade no cluster failover
Neste passo, cria manualmente o ouvinte do grupo de disponibilidade no Failover Cluster Manager e no SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verifique a configuração do ouvinte

Se os recursos e dependências do cluster estiverem corretamente configurados, deverá ser capaz de visualizar o ouvinte no Estúdio de Gestão de Servidores SQL. Para definir a porta do ouvinte, faça o seguinte:

1. Inicie o Estúdio de Gestão de Servidores SQL e, em seguida, ligue-se à réplica primária.

2. Vá a **AlwaysOn High Availability** > **Availability Groups** > **Availability Group Ouviros .**  
    Agora deve ver o nome do ouvinte que criou no Failover Cluster Manager. 

3. Clique no nome do ouvinte e, em seguida, clique em **Propriedades**.

4. Na caixa **do Porto,** especifique o número de porta para o ouvinte do grupo de disponibilidade utilizando o $EndpointPort utilizado anteriormente (1433 foi o padrão), e, em seguida, clique em **OK**.

Tem agora um grupo de disponibilidade em máquinas virtuais Azure em funcionamento no modo Gestor de Recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a ligação ao ouvinte
Testar a ligação fazendo o seguinte:

1. RDP para um caso De SQL Server que está na mesma rede virtual, mas não possui a réplica. Este servidor pode ser a outra instância do Servidor SQL no cluster.

2. Utilize um utilitário **sqlcmd** para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação **sqlcmd** à réplica primária através do ouvinte com autenticação Windows:
   
        sqlcmd -S <listenerName> -E

A ligação SQLCMD liga-se automaticamente à instância do Servidor SQL que acolhe a réplica primária. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Criar um endereço IP para um grupo de disponibilidade adicional

Cada grupo de disponibilidade usa um ouvinte separado. Cada ouvinte tem o seu próprio endereço IP. Utilize o mesmo equilibrador de carga para segurar o endereço IP para ouvintes adicionais. Depois de criar um grupo de disponibilidade, adicione o endereço IP ao equilibrador de carga e, em seguida, configure o ouvinte.

Para adicionar um endereço IP a um equilibrador de carga com o portal Azure, faça o seguinte:

1. No portal Azure, abra o grupo de recursos que contém o equilibrador de carga e, em seguida, clique no equilibrador de carga. 

2. Em **DEFINIÇÕES,** clique na **piscina IP frontend,** e depois clique em **Adicionar**. 

3. Em **adicionar endereço IP frontal,** atribua um nome para a extremidade frontal. 

4. Verifique se a **rede Virtual** e a **Subnet** são as mesmas que as instâncias do Servidor SQL.

5. Detete o endereço IP para o ouvinte. 
   
   >[!TIP]
   >Pode definir o endereço IP para estático e escrever um endereço que não seja utilizado atualmente na sub-rede. Em alternativa, pode definir o endereço IP para dinâmico e guardar o novo pool IP frontal. Quando o fizer, o portal Azure atribui automaticamente um endereço IP disponível à piscina. Em seguida, pode reabrir a piscina IP frontal e mudar a atribuição para estática. 

6. Guarde o endereço IP para o ouvinte. 

7. Adicione uma sonda de saúde utilizando as seguintes definições:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a sonda.
   |**Protocolo** |TCP
   |**Porto** |Uma porta TCP não utilizada, que deve estar disponível em todas as máquinas virtuais. Não pode ser usado para qualquer outro fim. Nenhum ouvinte pode usar a mesma porta de sonda. 
   |**Intervalo** |A quantidade de tempo entre as tentativas de sonda. Utilize o predefinido (5).
   |**Limiar com funcionamento incorreto** |O número de limiares consecutivos que devem falhar antes de uma máquina virtual é considerado insalubre.

8. Clique **em OK** para salvar a sonda. 

9. Crie uma regra de equilíbrio de carga. Clique em carregar regras de **equilíbrio,** e depois clique em **Adicionar**.

10. Configure a nova regra de equilíbrio de carga utilizando as seguintes definições:

    |Definição |Valor
    |:-----|:----
    |**Nome** |Um nome para identificar a regra de equilíbrio de carga. 
    |**Endereço IP de front-end** |Selecione o endereço IP que criou. 
    |**Protocolo** |TCP
    |**Porto** |Utilize a porta que as instâncias do Servidor SQL estão a utilizar. Uma instância predefinida utiliza a porta 1433, a não ser que a tenha alterado. 
    |**Porto backend** |Use o mesmo valor que o **Porto.**
    |**Piscina de backend** |A piscina que contém as máquinas virtuais com as instâncias do SQL Server. 
    |**Sonda de saúde** |Escolha a sonda que criou.
    |**Persistência da sessão** |Nenhuma
    |**Tempo limite (minutos)** |Padrão (4)
    |**IP flutuante (devolução do servidor direto)** | Ativado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configure o grupo de disponibilidade para usar o novo endereço IP

Para terminar de configurar o cluster, repita os passos que seguiu quando fez o primeiro grupo de disponibilidade. Ou seja, configure o [cluster para utilizar o novo endereço IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Depois de ter adicionado um endereço IP para o ouvinte, configure o grupo de disponibilidade adicional fazendo o seguinte: 

1. Verifique se a porta de sonda para o novo endereço IP está aberta em ambas as máquinas virtuais do SQL Server. 

2. [No Cluster Manager, adicione o ponto](#addcap)de acesso ao cliente .

3. [Configure o recurso IP para o grupo de disponibilidade](#congroup).

   >[!IMPORTANT]
   >Quando criar o endereço IP, utilize o endereço IP que adicionou ao equilibrador de carga.  

4. [Tornar o recurso do grupo de disponibilidade do SQL Server dependente do ponto](#dependencyGroup)de acesso ao cliente .

5. [Tornar o recurso do ponto](#listname)de acesso ao cliente dependente do endereço IP .
 
6. [Defina os parâmetros do cluster em PowerShell](#setparam).

Depois de configurar o grupo de disponibilidade para utilizar o novo endereço IP, configure a ligação ao ouvinte. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Adicione a regra de equilíbrio de carga para o grupo de disponibilidade distribuído

Se um grupo de disponibilidade participar num grupo de disponibilidade distribuído, o equilibrador de carga precisa de uma regra adicional. Esta regra armazena a porta utilizada pelo ouvinte do grupo de disponibilidade distribuído.

>[!IMPORTANT]
>Este passo só se aplica se o grupo de disponibilidade participar num grupo de [disponibilidade distribuído.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups) 

1. Em cada servidor que participe no grupo de disponibilidade distribuído, crie uma regra de entrada na porta TCP do grupo de disponibilidade distribuída. Em muitos exemplos, a documentação utiliza 5022. 

1. No portal Azure, clique no equilibrador de carga e clique em regras de **equilíbrio de carga,** e clique em **+Add**. 

1. Crie a regra de equilíbrio de carga com as seguintes definições:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a regra de equilíbrio de carga para o grupo de disponibilidade distribuído. 
   |**Endereço IP de front-end** |Utilize o mesmo endereço IP frontal que o grupo de disponibilidade.
   |**Protocolo** |TCP
   |**Porto** |5022 - A porta para o ouvinte final do [grupo de disponibilidade distribuído](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Pode ser qualquer porta disponível.  
   |**Porto backend** | 5022 - Use o mesmo valor que a **Porta**.
   |**Piscina de backend** |A piscina que contém as máquinas virtuais com as instâncias do SQL Server. 
   |**Sonda de saúde** |Escolha a sonda que criou.
   |**Persistência da sessão** |Nenhuma
   |**Tempo limite (minutos)** |Padrão (4)
   |**IP flutuante (devolução do servidor direto)** | Ativado

Repita estes passos para o equilibrador de carga nos outros grupos de disponibilidade que participam nos grupos de disponibilidade distribuídos.

Se estiver a restringir o acesso a um Grupo de Segurança da Rede Azure, certifique-se de que as regras de licenciamento incluem os endereços IP VM do Servidor SQL e os endereços IP flutuantes do balanceor de carga para o ouvinte AG e o endereço IP do núcleo de cluster, se aplicável.

## <a name="next-steps"></a>Passos seguintes

- [Configure um sQL Server Sempre em grupo de disponibilidade em máquinas virtuais Azure em diferentes regiões](virtual-machines-windows-portal-sql-availability-group-dr.md)
