---
title: Configure os ouvintes do grupo de disponibilidade & equilibrador de carga (portal Azure)
description: Instruções passo a passo para criar um ouvinte para um grupo de disponibilidade Always On para O Servidor SQL em máquinas virtuais Azure
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 9fa23ca2ae655a11d7aaa4be67e08a6b3fa44394
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359392"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Configure um equilibrador de carga para um sql servidor sempre em grupo de disponibilidade em Máquinas Virtuais Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Este artigo explica como criar um equilibrador de carga para um sql servidor sempre no grupo de disponibilidade em Azure Virtual Machines que estão em execução com O Gestor de Recursos Azure. Um grupo de disponibilidade requer um balanceador de carga quando as instâncias do SQL Server estão em Azure Virtual Machines. O equilibrador de carga armazena o endereço IP para o ouvinte do grupo de disponibilidade. Se um grupo de disponibilidade abrange várias regiões, cada região precisa de um equilibrador de carga.

Para completar esta tarefa, precisa de ter um grupo de disponibilidade SQL Sempre Em disponibilidade implantado em VMs Azure que estão a funcionar com o Gestor de Recursos. Ambas as máquinas virtuais SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Pode utilizar o modelo da [Microsoft](./availability-group-quickstart-template-configure.md) para criar automaticamente o grupo de disponibilidade no Gestor de Recursos. Este modelo cria automaticamente um equilibrador de carga interno para si. 

Se preferir, pode [configurar manualmente um grupo de disponibilidade.](availability-group-manually-configure-tutorial.md)

Este artigo requer que os seus grupos de disponibilidade já estejam configurados.  

Ver artigos relacionados:

* [Configure sempre em grupos de disponibilidade em Azure VM (GUI)](availability-group-manually-configure-tutorial.md)   
* [Configurar uma ligação VNet a VNet com o Azure Resource Manager e o PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ao percorrer este artigo, cria-se e configura um equilibrador de carga no portal Azure. Após o processo estar concluído, configura o cluster para utilizar o endereço IP do equilibrador de carga para o ouvinte do grupo de disponibilidade.

## <a name="create--configure-load-balancer"></a>Criar & configurar o balançador de carga 

Nesta parte da tarefa, faça os seguintes passos:

1. No portal Azure, crie o equilibrador de carga e configugue o endereço IP.
2. Configure a piscina traseira.
3. Cria a sonda. 
4. Desabrague as regras de equilíbrio de carga.

> [!NOTE]
> Se as instâncias do SQL Server estiverem em vários grupos de recursos e regiões, execute cada passo duas vezes, uma vez em cada grupo de recursos.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Passo 1: Criar o equilibrador de carga e configurar o endereço IP

Primeiro, crie o equilibrador de carga. 

1. No portal Azure, abra o grupo de recursos que contém as máquinas virtuais SQL Server. 

2. No grupo de recursos, **selecione Adicionar**.

3. Procure o **equilibrador de carga.** Escolha **o Balanceador de Carga** (publicado pela **Microsoft)** nos resultados da pesquisa.

4. Na lâmina do **balançador de carga,** selecione **Criar**.

5. Na caixa de diálogo do balançador de **carga Create,** configurar o balançador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o equilibrador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interna**: A maioria das implementações utiliza um equilibrador de carga interno, que permite que aplicações dentro da mesma rede virtual se conectem ao grupo de disponibilidade.  </br> **Externo**: Permite que as aplicações se conectem ao grupo de disponibilidade através de uma ligação pública à Internet. |
   | **SKU** |**Básico**: Opção predefinição. Só é válido se as instâncias do SQL Server estiverem no mesmo conjunto de disponibilidade. </br> **Norma**: Preferido. Válido se as instâncias do SQL Server estiverem no mesmo conjunto de disponibilidade. Necessário se as suas instâncias do SQL Server estiverem em diferentes zonas de disponibilidade. |
   | **Rede virtual** |Selecione a rede virtual em que se encontram as instâncias do SQL Server. |
   | **Sub-rede** |Selecione a sub-rede em que se encontram as instâncias do SQL Server. |
   | **Atribuição de endereços IP** |**Estático** |
   | **Endereço IP privado** |Especifique um endereço IP disponível a partir da sub-rede. Utilize este endereço IP quando criar um ouvinte no cluster. Num script PowerShell, mais tarde neste artigo, utilize este endereço para a `$ILBIP` variável. |
   | **Subscrição** |Se tiver várias subscrições, este campo pode aparecer. Selecione a subscrição que pretende associar a este recurso. Normalmente é a mesma subscrição que todos os recursos para o grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos em que se encontram as instâncias do SQL Server. |
   | **Localização** |Selecione a localização Azure em que se encontram as instâncias do SQL Server. |

6. Selecione **Criar**. 

Azure cria o equilibrador de carga. O equilibrador de carga pertence a uma rede específica, sub-rede, grupo de recursos e localização. Depois de Azure completar a tarefa, verifique as definições do balançador de carga no Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Passo 2: Configurar a piscina traseira

Azure chama a piscina de *backend* de endereço back-end . Neste caso, o pool back-end são os endereços das duas instâncias sql Server no seu grupo de disponibilidade. 

1. No seu grupo de recursos, selecione o equilibrador de carga que criou. 

2. Nas **Definições**, selecione **piscinas backend**.

3. Nas **piscinas backend**, selecione **Adicionar** para criar um pool de endereços back-end. 

4. Na **piscina de backend**, em **Nome,** escreva um nome para a piscina traseira.

5. Em **máquinas virtuais**, selecione **Adicionar uma máquina virtual**. 

6. Em **Escolha máquinas virtuais**, selecione Escolha um conjunto de **disponibilidade** e, em seguida, especifique o conjunto de disponibilidade a que as máquinas virtuais SQL Server pertencem.

7. Depois de ter escolhido o conjunto de disponibilidade, **selecione Escolha as máquinas virtuais,** selecione as duas máquinas virtuais que acolhem as instâncias do SQL Server no grupo de disponibilidade e, em seguida, escolha **Select**. 

8. Selecione **OK** para fechar as lâminas para **Escolher máquinas virtuais** e **Adicione o pool backend**. 

O Azure atualiza as definições para o conjunto de endereços back-end. Agora o seu conjunto de disponibilidade tem um conjunto de duas instâncias SQL Server.

### <a name="step-3-create-a-probe"></a>Passo 3: Criar uma sonda

A sonda define como o Azure verifica quais das instâncias do SQL Server detém atualmente o ouvinte do grupo de disponibilidade. O Azure sonda o serviço com base no endereço IP numa porta que define quando cria a sonda.

1. Na lâmina do balançador **de** carga, selecione **sondas de saúde**. 

2. Na lâmina **das sondas Health,** selecione **Adicionar**.

3. Configure a sonda na lâmina da **sonda Add.** Utilize os seguintes valores para configurar a sonda:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto representando a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Pode utilizar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limiar com funcionamento incorreto** |*2* |

4.  Selecione **OK**. 

> [!NOTE]
> Certifique-se de que a porta especifica está aberta na firewall de ambas as instâncias do SQL Server. Ambas as instâncias requerem uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou editar a regra de Firewall](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

O Azure cria a sonda e depois usa-a para testar qual a instância do SQL Server que tem o ouvinte para o grupo de disponibilidade.

### <a name="step-4-set-the-load-balancing-rules"></a>Passo 4: Definir as regras de equilíbrio de carga

As regras de equilíbrio de carga configuram como o balanceador de carga encaminha o tráfego para as instâncias do SQL Server. Para este equilibrador de carga, ativa a devolução do servidor direto porque apenas uma das duas instâncias do SQL Server possui o recurso de ouvinte do grupo de disponibilidade de cada vez.

1. Na lâmina do balançador de carga, selecione **regras** **de equilíbrio de carga**. 

2. Na lâmina das **regras de equilíbrio de carga,** selecione **Adicionar**.

3. Na lâmina **das regras de equilíbrio** de carga adicionar, configuure a regra de equilíbrio de carga. Utilize as seguintes definições: 

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de equilíbrio de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porto Backend** |*1433*. Este valor é ignorado porque esta regra utiliza **IP flutuante (retorno direto do servidor)**. |
   | **Teste** |Utilize o nome da sonda que criou para este equilibrador de carga. |
   | **Persistência da sessão** |**Nenhuma** |
   | **Tempo de 20 minutos (minutos)** |*4* |
   | **IP flutuante (retorno direto do servidor)** |**Ativado** |

   > [!NOTE]
   > Pode ter de rolar para baixo da lâmina para ver todas as definições.
   > 

4. Selecione **OK**. 

5. Azure configura a regra de equilíbrio de carga. Agora, o equilibrador de carga está configurado para encaminhar o tráfego para a instância do SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um equilibrador de carga que se conecta a ambas as máquinas sql Server. O equilibrador de carga também contém um endereço IP para o ouvinte do grupo SQL Server Always On availability, para que qualquer uma das máquinas possa responder aos pedidos dos grupos de disponibilidade.

> [!NOTE]
> Se as suas instâncias do SQL Server estiverem em duas regiões distintas, repita os passos na outra região. Cada região requer um equilibrador de carga. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure o cluster para utilizar o endereço IP do balançador de carga

O próximo passo é configurar o ouvinte no agrupamento e colocar o ouvinte online. Faça os seguintes passos: 

1. Crie o ouvinte do grupo de disponibilidade no cluster failover. 

2. Traga o ouvinte on-line.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Passo 5: Criar o ouvinte do grupo de disponibilidade no cluster failover

Neste passo, cria manualmente o ouvinte do grupo de disponibilidade no Failover Cluster Manager e no SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verifique a configuração do ouvinte

Se os recursos e dependências do cluster estiverem corretamente configurados, deverá ser capaz de ver o ouvinte no SQL Server Management Studio. Para definir a porta do ouvinte, faça os seguintes passos:

1. Inicie o SQL Server Management Studio e, em seguida, ligue-se à réplica primária.

2. Vá ao **AlwaysOn High**  >  **Availability Availability**  >  **Groups Disponibilidade Grupos Ouvintes** do grupo .  

    Deve agora ver o nome do ouvinte que criou no Failover Cluster Manager. 

3. Clique com o botão direito no nome do ouvinte e, em seguida, selecione **Propriedades**.

4. Na caixa **de porta,** especifique o número da porta para o ouvinte do grupo de disponibilidade utilizando o $EndpointPort que usou anteriormente (1433 foi o predefinido), e, em seguida, selecione **OK**.

Tem agora um grupo de disponibilidade em máquinas virtuais Azure em funcionamento no modo Gestor de Recursos. 

## <a name="test-the-connection-to-the-listener"></a>Teste a ligação ao ouvinte

Testar a ligação fazendo os seguintes passos:

1. Utilize o protocolo de ambiente de trabalho remoto (RDP) para ligar a uma instância do SQL Server que está na mesma rede virtual, mas que não possui a réplica. Este servidor pode ser a outra instância do SQL Server no cluster.

2. Utilize **serviços sqlcmd** para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação **sqlcmd** à réplica primária através do ouvinte com autenticação do Windows:

    ```console
    sqlcmd -S <listenerName> -E
    ```

A ligação SQLCMD liga-se automaticamente à instância SQL Server que acolhe a réplica primária. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Criar um endereço IP para um grupo de disponibilidade adicional

Cada grupo de disponibilidade utiliza um ouvinte separado. Cada ouvinte tem o seu próprio endereço IP. Utilize o mesmo equilibrador de carga para segurar o endereço IP para ouvintes adicionais. Depois de criar um grupo de disponibilidade, adicione o endereço IP ao balançador de carga e, em seguida, configuure o ouvinte.

Para adicionar um endereço IP a um equilibrador de carga com o portal Azure, faça os seguintes passos:

1. No portal Azure, abra o grupo de recursos que contém o equilibrador de carga e, em seguida, selecione o equilibrador de carga. 

2. Em **DEFINIÇÕES**, selecione **o pool IP frontend** e, em seguida, selecione **Adicionar**. 

3. No **endereço IP frontend,** atribua um nome para a parte frontal. 

4. Verifique se a **rede Virtual** e a **Sub-rede** são as mesmas que as instâncias do SQL Server.

5. Desagrafe o endereço IP para o ouvinte. 
   
   >[!TIP]
   >Pode configurar o endereço IP para estática e escrever um endereço que não seja utilizado atualmente na sub-rede. Em alternativa, pode definir o endereço IP para dinâmico e guardar o novo pool IP frontal. Ao fazê-lo, o portal Azure atribui automaticamente um endereço IP disponível à piscina. Em seguida, pode reabrir o pool IP frontal e alterar a atribuição para estática. 

6. Guarde o endereço IP para o ouvinte. 

7. Adicione uma sonda de saúde utilizando as seguintes definições:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a sonda.
   |**Protocolo** |TCP
   |**Porta** |Uma porta TCP nãousada, que deve estar disponível em todas as máquinas virtuais. Não pode ser usado para qualquer outro fim. Nenhum ouvinte pode usar a mesma porta de sonda. 
   |**Intervalo** |A quantidade de tempo entre tentativas de sonda. Utilize o predefinido (5).
   |**Limiar com funcionamento incorreto** |O número de limiares consecutivos que devem falhar antes de uma máquina virtual é considerado insalubre.

8. Selecione **OK** para salvar a sonda. 

9. Criar uma regra de equilíbrio de carga. Selecione **regras de equilíbrio de** carga e, em seguida, selecione **Adicionar**.

10. Configure a nova regra de equilíbrio de carga utilizando as seguintes definições:

    |Definição |Valor
    |:-----|:----
    |**Nome** |Um nome para identificar a regra de equilibrar a carga. 
    |**Endereço IP de front-end** |Selecione o endereço IP criado. 
    |**Protocolo** |TCP
    |**Porta** |Utilize a porta que as instâncias do SQL Server estão a usar. Um caso padrão usa a porta 1433, a menos que a tenha alterado. 
    |**Porta de back-end** |Use o mesmo valor que a **Porta.**
    |**Conjunto de back-end** |A piscina que contém as máquinas virtuais com as instâncias do SQL Server. 
    |**Sonda de estado de funcionamento** |Escolha a sonda que criou.
    |**Persistência da sessão** |Nenhum
    |**Tempo de 20 minutos (minutos)** |Predefinição (4)
    |**IP flutuante (retorno direto do servidor)** | Ativado

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configure o grupo de disponibilidade para utilizar o novo endereço IP

Para terminar a configuração do cluster, repita os passos que seguiu quando fez o primeiro grupo de disponibilidade. Ou seja, configurar o [cluster para utilizar o novo endereço IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Depois de ter adicionado um endereço IP para o ouvinte, configuure o grupo de disponibilidade adicional fazendo os seguintes passos: 

1. Verifique se a porta de sonda para o novo endereço IP está aberta em ambas as máquinas virtuais do SQL Server. 

2. [No Cluster Manager, adicione o ponto de acesso ao cliente.](#addcap)

3. [Configure o recurso IP para o grupo de disponibilidade](#congroup).

   >[!IMPORTANT]
   >Quando criar o endereço IP, utilize o endereço IP que adicionou ao equilibrador de carga.  

4. [Tornar o recurso de grupo de disponibilidade SQL dependente do ponto](#dependencyGroup)de acesso do cliente .

5. [Tornar o recurso do ponto de acesso do cliente dependente do endereço IP](#listname).
 
6. [Desa estale os parâmetros do cluster no PowerShell](#setparam).

Depois de configurar o grupo de disponibilidade para utilizar o novo endereço IP, configuure a ligação ao ouvinte. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Adicione a regra de equilíbrio de carga para o grupo de disponibilidade distribuído

Se um grupo de disponibilidade participar num grupo de disponibilidade distribuído, o balançador de carga precisa de uma regra adicional. Esta regra armazena a porta utilizada pelo ouvinte do grupo de disponibilidade distribuído.

>[!IMPORTANT]
>Este passo só se aplica se o grupo de disponibilidade participar num [grupo de disponibilidade distribuído.](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups) 

1. Em cada servidor que participa no grupo de disponibilidade distribuído, crie uma regra de entrada na porta TCP do grupo de disponibilidade distribuída. Em muitos exemplos, a documentação utiliza o 5022. 

1. No portal Azure, selecione no equilibrador de carga e selecione **regras de equilíbrio de carga**, e, em seguida, selecione **+Adicionar**. 

1. Criar a regra de equilíbrio de carga com as seguintes definições:

   |Definição |Valor
   |:-----|:----
   |**Nome** |Um nome para identificar a regra de equilíbrio de carga para o grupo de disponibilidade distribuído. 
   |**Endereço IP de front-end** |Utilize o mesmo endereço IP frontal que o grupo de disponibilidade.
   |**Protocolo** |TCP
   |**Porta** |5022 - A porta para o [ouvinte de ponto final do grupo de disponibilidade distribuído.](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)</br> Pode ser qualquer porto disponível.  
   |**Porta de back-end** | 5022 - Use o mesmo valor que a **Porta.**
   |**Conjunto de back-end** |A piscina que contém as máquinas virtuais com as instâncias do SQL Server. 
   |**Sonda de estado de funcionamento** |Escolha a sonda que criou.
   |**Persistência da sessão** |Nenhum
   |**Tempo de 20 minutos (minutos)** |Predefinição (4)
   |**IP flutuante (retorno direto do servidor)** | Ativado

Repita estes passos para o equilibrador de carga nos outros grupos de disponibilidade que participam nos grupos de disponibilidade distribuídos.

Se tiver um Grupo de Segurança da Rede Azure para restringir o acesso, certifique-se de que as regras de permitir incluem:
- Os endereços IP do servidor sql backend
- Os endereços IP flutuantes do balançador de carga para o ouvinte AG
- O endereço IP do núcleo do cluster, se aplicável.

## <a name="next-steps"></a>Passos seguintes

- [Configure um SQL Server Always On availability group em máquinas virtuais Azure em diferentes regiões](availability-group-manually-configure-multiple-regions.md)