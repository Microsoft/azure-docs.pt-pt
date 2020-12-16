---
title: Configure um ouvinte de grupo de disponibilidade para SQL Server em máquinas virtuais RHEL em Azure - Máquinas virtuais Linux Microsoft Docs
description: Saiba como configurar um ouvinte de grupo de disponibilidade no SQL Server em máquinas virtuais RHEL em Azure
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 83fde9e957cb2011ce585603e51d331be171bc08
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586232"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Configurar um ouvinte de grupo de disponibilidade para o SQL Server em máquinas virtuais RHEL em Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> O tutorial apresentado está em **pré-visualização pública.** 
>
> Utilizamos o SQL Server 2017 com RHEL 7.6 neste tutorial, mas é possível utilizar o SQL Server 2019 em RHEL 7 ou RHEL 8 para configurar alta disponibilidade. Os comandos para configurar recursos do grupo de disponibilidade mudaram em RHEL 8, e você vai querer olhar para o artigo [Criar recursos de grupo de disponibilidade](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) e recursos RHEL 8 para mais informações sobre os comandos corretos.

Este tutorial irá analisar etapas sobre como criar um ouvinte de grupo de disponibilidade para os seus Servidores SQL em máquinas virtuais RHEL (VMs) em Azure. Vai aprender a:

> [!div class="checklist"]
> - Criar um equilibrador de carga no portal Azure
> - Configure a piscina traseira para o equilibrador de carga
> - Criar uma sonda para o equilibrador de carga
> - Definir as regras de equilíbrio de carga
> - Criar o recurso do equilibrador de carga no cluster
> - Criar o ouvinte do grupo de disponibilidade
> - Teste de ligação ao ouvinte
> - Testar uma falha

## <a name="prerequisite"></a>Pré-requisito

Tutorial [Concluído: Configurar grupos de disponibilidade para SQL Server em máquinas virtuais RHEL em Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o equilibrador de carga no portal Azure

As seguintes instruções levam-no através dos passos 1 a 4 a partir do Create e configurar o balançador de carga na secção [porta-cargaS do](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) [equilibrador](../windows/availability-group-load-balancer-portal-configure.md) de carga - artigo do portal Azure.

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

1. No portal Azure, abra o grupo de recursos que contém as máquinas virtuais SQL Server. 

2. No grupo de recursos, clique em **Adicionar**.

3. Procure o **equilibrador de carga** e, em seguida, nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.

4. Na lâmina do **balançador de** carga, clique em **Criar**.

5. Na caixa de diálogo do balançador de **carga Create,** configurar o balançador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o equilibrador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interno** |
   | **Rede virtual** |A rede virtual padrão que foi criada deve ser denominada **VM1VNET**. |
   | **Sub-rede** |Selecione a sub-rede em que se encontram as instâncias do SQL Server. O predefinido deve ser **VM1Subnet**.|
   | **Atribuição de endereços IP** |**Estático** |
   | **Endereço IP privado** |Utilize o `virtualip` endereço IP que foi criado no cluster. |
   | **Subscrição** |Utilize a subscrição que foi utilizada para o seu grupo de recursos. |
   | **Grupo de recursos** |Selecione o grupo de recursos em que se encontram as instâncias do SQL Server. |
   | **Localização** |Selecione a localização Azure em que se encontram as instâncias do SQL Server. |

### <a name="configure-the-back-end-pool"></a>Configure a piscina traseira
Azure chama a piscina de *backend* de endereço back-end . Neste caso, o pool back-end são os endereços das três instâncias sql Server no seu grupo de disponibilidade. 

1. No seu grupo de recursos, clique no equilibrador de carga que criou. 

2. Nas **Definições,** clique em **pools backend**.

3. Nas **piscinas backend,** clique **em Adicionar** para criar uma piscina de endereços back-end. 

4. Na **piscina de backend**, em **Nome,** escreva um nome para a piscina traseira.

5. Em **'Associado' a**, selecione **Máquina Virtual**. 

6. Selecione cada máquina virtual no ambiente e associe o endereço IP apropriado a cada seleção.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Adicionar piscina de backend":::

7. Clique em **Adicionar**. 

### <a name="create-a-probe"></a>Criar uma sonda

A sonda define como o Azure verifica quais das instâncias do SQL Server detém atualmente o ouvinte do grupo de disponibilidade. O Azure sonda o serviço com base no endereço IP numa porta que define quando cria a sonda.

1. Na lâmina do balançador **de** carga, clique nas **sondas Health**. 

2. Na lâmina das **sondas Health,** clique em **Adicionar**.

3. Configure a sonda na lâmina da **sonda Add.** Utilize os seguintes valores para configurar a sonda:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto representando a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Pode utilizar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limiar com funcionamento incorreto** |*2* |

4.  Clique em **OK**. 

5. Inicie sessão em todas as suas máquinas virtuais e abra a porta da sonda utilizando os seguintes comandos:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

O Azure cria a sonda e depois usa-a para testar qual a instância do SQL Server que tem o ouvinte para o grupo de disponibilidade.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de equilíbrio de carga

As regras de equilíbrio de carga configuram como o balanceador de carga encaminha o tráfego para as instâncias do SQL Server. Para este equilibrador de carga, ativa a devolução do servidor direto porque apenas uma das três instâncias do SQL Server possui o recurso de ouvinte do grupo de disponibilidade de cada vez.

1. Na lâmina do balançador de carga, clique nas **regras** **de equilíbrio de carga**. 

2. Na lâmina das **regras de equilíbrio de carga,** clique em **Adicionar**.

3. Na lâmina **das regras de equilíbrio** de carga adicionar, configuure a regra de equilíbrio de carga. Utilize as seguintes definições: 

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de equilíbrio de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta de back-end** |*1433*. Este valor é ignorado porque esta regra utiliza **IP flutuante (retorno direto do servidor)**. |
   | **Teste** |Utilize o nome da sonda que criou para este equilibrador de carga. |
   | **Persistência da sessão** |**Nenhuma** |
   | **Tempo de 20 minutos (minutos)** |*4* |
   | **IP flutuante (retorno direto do servidor)** |**Ativado** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Adicionar regra de equilíbrio de carga":::

4. Clique em **OK**. 
5. Azure configura a regra de equilíbrio de carga. Agora, o equilibrador de carga está configurado para encaminhar o tráfego para a instância do SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um equilibrador de carga que se conecta a todas as máquinas do SQL Server. O equilibrador de carga também contém um endereço IP para o ouvinte do grupo SQL Server Always On availability, para que qualquer máquina possa responder aos pedidos dos grupos de disponibilidade.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Criar o recurso do equilibrador de carga no cluster

1. Faça login na máquina virtual primária. Precisamos de criar o recurso para ativar a porta da sonda de carga Azure (59999 é usado no nosso exemplo). Execute o seguinte comando:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Criar um grupo que contenha o `virtualip` `azure_load_balancer` recurso:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Adicionar restrições

1. Deve ser configurada uma restrição de colocação para garantir que o endereço IP do balanceador de carga Azure e o recurso AG estejam a funcionar no mesmo nó. Execute o seguinte comando:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Crie uma restrição de encomenda para garantir que o recurso AG está a funcionar antes do endereço IP do balanceador de carga Azure. Embora o constrangimento de colocação implique uma restrição de encomenda, isto o impõe.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Para verificar os constrangimentos, executar o seguinte comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Deverá ver o seguinte resultado:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Criar o ouvinte do grupo de disponibilidade

1. No nó primário, executar o seguinte comando em SQLCMD ou SSMS:

    - Substitua o endereço IP utilizado abaixo pelo `virtualip` endereço IP.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Faça login em cada nó VM. Utilize o seguinte comando para abrir o ficheiro dos anfitriões e configurar a resolução do nome do anfitrião para `ag1-listener` cada máquina.

    ```
    sudo vi /etc/hosts
    ```

    No **editor vi,** `i` insira para inserir texto, e numa linha em branco, adicione o IP do `ag1-listener` . Em seguida, adicione `ag1-listener` depois de um espaço ao lado do IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Para sair do **editor vi,** primeiro bata na chave **Esc,** e depois insira o comando `:wq` para escrever o ficheiro e desistir. Faça isto em cada nó.

## <a name="test-the-listener-and-a-failover"></a>Teste o ouvinte e um failover

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Teste de login no SQL Server usando o ouvinte do grupo de disponibilidade

1. Utilize o SQLCMD para iniciar sessão no nó primário do SQL Server utilizando o nome do ouvinte do grupo de disponibilidade:

    - Utilize um login previamente criado e `<YourPassword>` substitua-o pela senha correta. O exemplo abaixo utiliza o `sa` login que foi criado com o SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Verifique o nome do servidor a que está ligado. Executar o seguinte comando em SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    A sua saída deve mostrar o nó primário atual. Isto deve ser `VM1` se nunca tiver testado um fracasso.

    Saia da sessão sql Server digitando o `exit` comando.

### <a name="test-a-failover"></a>Teste um failover

1. Executar o seguinte comando para falhar manualmente sobre a réplica primária para `<VM2>` ou outra réplica. `<VM2>`Substitua-o pelo valor do nome do seu servidor.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Se verificar os seus constrangimentos, verá que foi adicionado outro constrangimento devido à falha manual:

    ```bash
    sudo pcs constraint list --full
    ```

    Verá que foi adicionado um constrangimento com a `cli-prefer-ag_cluster-master` identificação.

1. Remover a restrição com ID `cli-prefer-ag_cluster-master` utilizando o seguinte comando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Verifique os recursos do seu cluster utilizando o `sudo pcs resource` comando, e deve ver se a instância principal é agora `<VM2>` .

    > [!NOTE]
    > Este artigo contém referências ao termo escravo, um termo que a Microsoft já não usa. Quando o termo for removido do software, vamos removê-lo deste artigo.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Utilize o SQLCMD para iniciar sessão na sua réplica primária utilizando o nome do ouvinte:

    - Utilize um login previamente criado e `<YourPassword>` substitua-o pela senha correta. O exemplo abaixo utiliza o `sa` login que foi criado com o SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Verifique o servidor ao que está ligado. Executar o seguinte comando em SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Devias ver que estás agora ligado ao VM ao qual falhaste.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os equilibradores de carga em Azure, consulte:

> [!div class="nextstepaction"]
> [Configure um saldo de carga para um grupo de disponibilidade no SQL Server em VMs Azure](../windows/availability-group-load-balancer-portal-configure.md)
