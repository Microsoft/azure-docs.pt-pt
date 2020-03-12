---
title: Configure o ouvinte do grupo de disponibilidade para o SQL Server em máquinas virtuais RHEL em Máquinas Virtuais Azure - Linux [ Máquinas Virtuais Linux ] Microsoft Docs
description: Saiba sobre a criação de um ouvinte de grupo de disponibilidade no SQL Server em máquinas virtuais RHEL em Azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096587"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Configure o ouvinte do grupo de disponibilidade para o SQL Server em máquinas virtuais RHEL em Azure

> [!NOTE]
> O tutorial apresentado está em **pré-visualização pública.** 
>
> Utilizamos o SQL Server 2017 com RHEL 7.6 neste tutorial, mas é possível utilizar o SQL Server 2019 em RHEL 7 ou RHEL 8 para configurar ha. Os comandos para configurar os recursos do grupo de disponibilidade mudaram no RHEL 8, e você vai querer olhar para o artigo, [Criar recursos](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) de grupo de disponibilidade e recursos RHEL 8 para mais informações sobre os comandos corretos.

Este tutorial irá analisar como criar um ouvinte de grupo de disponibilidade para os seus Servidores SQL em máquinas virtuais RHEL em Azure. Vai aprender a:

> [!div class="checklist"]
> - Criar um equilibrador de carga no portal Azure
> - Configure a piscina traseira para o equilibrador de carga
> - Crie uma sonda para o equilibrador de carga
> - Definir as regras de equilíbrio de carga
> - Criar o recurso de equilíbrio de carga no cluster
> - Criar o ouvinte do grupo de disponibilidade
> - Teste de ligação ao ouvinte
> - Testar uma falha

## <a name="prerequisite"></a>Pré-requisito

Tutorial [ **concluído: Configure grupos de disponibilidade para SQL Server em máquinas virtuais RHEL em Azure**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Crie o equilibrador de carga no portal Azure

As seguintes instruções levam-no através dos passos 1 a 4 do [Create e configurar o equilibrador](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) de carga na secção do portal Azure do artigo [do portal Load - Azure.](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

### <a name="create-the-load-balancer"></a>Criar o balanceador de carga

1. No portal Azure, abra o grupo de recursos que contém as máquinas virtuais Do Servidor SQL. 

2. No grupo de recursos, clique em **Adicionar**.

3. Procure um **equilibrador** de carga e, em seguida, nos resultados da pesquisa, selecione **Load Balancer**, que é publicado pela **Microsoft**.

4. Na lâmina **do Balancer de carga,** clique em **Criar**.

5. Na caixa de diálogo criar um **equilíbrio de carga,** configure o equilibrador de carga da seguinte forma:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o equilibrador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interno** |
   | **Rede virtual** |O VNet padrão que foi criado deve chamar-se **VM1VNET**. |
   | **Sub-rede** |Selecione a sub-rede em que as instâncias do Servidor SQL estão dentro. A predefinição deve ser **VM1Subnet**.|
   | **Atribuição de endereçoIP** |**Estática** |
   | **Endereço IP privado** |Utilize o endereço IP `virtualip` que foi criado no cluster. |
   | **Subscrição** |Utilize a subscrição que foi utilizada para o seu grupo de recursos. |
   | **Grupo de recursos** |Selecione o grupo de recursos em que os casos do Servidor SQL estão. |
   | **Localização** |Selecione a localização Azure onde os casos do Servidor SQL estão. |

### <a name="configure-the-back-end-pool"></a>Configure a piscina de back-end
O Azure chama a piscina de back-end da piscina de endereços *traseiros.* Neste caso, o pool back-end são os endereços das três instâncias do SQL Server no seu grupo de disponibilidade. 

1. No seu grupo de recursos, clique no equilibrador de carga que criou. 

2. Em **Definições,** clique em **piscinas backend**.

3. Nas **piscinas Backend,** clique em **Adicionar** para criar uma piscina de endereços de back-end. 

4. Em **Adicionar piscina de backend**, em **nome**, digite um nome para a piscina de back-end.

5. Sob **associado para**selecionar máquina **virtual.** 

6. Selecione cada máquina virtual no ambiente e associe o endereço IP apropriado a cada seleção.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Adicione piscina de backend":::

7. Clique em **Adicionar**. 

### <a name="create-a-probe"></a>Criar uma sonda

A sonda define como o Azure verifica qual dos casos do SQL Server detém atualmente o ouvinte do grupo de disponibilidade. O Azure sonda o serviço com base no endereço IP de uma porta que define quando cria a sonda.

1. No balanço de carga **Configuração** da lâmina, clique em **sondas de saúde**. 

2. Na lâmina das **sondas Health,** clique em **Adicionar**.

3. Configure a sonda na lâmina da **sonda Adicionar.** Utilize os seguintes valores para configurar a sonda:

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto representando a sonda. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Pode utilizar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limiar insalubre** |*2* |

4.  Clique em **OK**. 

5. Inicie sessão em todas as suas máquinas virtuais e abra a porta da sonda utilizando os seguintes comandos:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

O Azure cria a sonda e depois usa-a para testar qual a instância do SQL Server que tem o ouvinte para o grupo de disponibilidade.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de equilíbrio de carga

As regras de equilíbrio de carga configuram como o equilibrador de carga direciona o tráfego para as instâncias do Servidor SQL. Para este balanceor de carga, permite a devolução do servidor direto porque apenas uma das três instâncias do SQL Server possui o recurso de ouvinte de grupo de disponibilidade de cada vez.

1. No balanço de carga **Configuração** da lâmina, clique em regras de **equilíbrio de carga**. 

2. Na lâmina de regras de **equilíbrio da carga,** clique em **Adicionar**.

3. Na lâmina de equilíbrio de **carga Adicionar,** configure a regra de equilíbrio da carga. Utilize as seguintes definições: 

   | Definição | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de equilíbrio da carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porto backend** |*1433*. Este valor é ignorado porque esta regra utiliza **IP flutuante (retorno do servidor direto)** . |
   | **Sonda** |Use o nome da sonda que criou para este equilibrador de carga. |
   | **Persistência da sessão** |**Nenhuma.** |
   | **Tempo limite (minutos)** |*4* |
   | **IP flutuante (devolução do servidor direto)** |**Ativado** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Adicionar regra de equilíbrio de carga":::

4. Clique em **OK**. 
5. Azure configura a regra de equilíbrio de carga. Agora, o equilibrista de carga está configurado para encaminhar o tráfego para a instância SQL Server que acolhe o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um equilibrador de carga que se conecta a todas as máquinas Do Servidor SQL. O balanceor de carga também contém um endereço IP para o ouvinte do grupo SQL Server Always On, para que qualquer máquina possa responder aos pedidos dos grupos de disponibilidade.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Criar o recurso de equilíbrio de carga no cluster

1. Entre na máquina virtual primária. Precisamos de criar o recurso para ativar a porta de sonda azure balancer (59999 é usada no nosso exemplo). Execute o seguinte comando:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Crie um grupo que contenha o recurso `virtualip` e `azure_load_balancer`:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Adicionar constrangimentos

1. Deve ser configurada uma restrição de co-localização para garantir que o endereço IP do equilíbrio de carga Azure e o recurso AG estão a funcionar no mesmo nó. Execute o seguinte comando:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Crie um constrangimento de encomenda para garantir que o recurso AG está a funcionar antes do endereço IP do equilíbrio de carga Azure. Embora a restrição de co-localização implique uma restrição de ordenação, isto a impõe.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Para verificar os constrangimentos, execute o seguinte comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Deverá ver o resultado seguinte:

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

1. No nó principal, executar o seguinte comando em SQLCMD ou SSMS:

    - Substitua o endereço IP utilizado abaixo pelo endereço IP `virtualip`.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Inicie sessão em cada nó VM. Utilize o seguinte comando para abrir o ficheiro dos anfitriões e configurar a resolução de nome supérbio para o `ag1-listener` em cada máquina.

    ```
    sudo vi /etc/hosts
    ```

    No **vi** editor, introduza `i` para inserir texto e, numa linha em branco, adicione o IP do `ag1-listener`. Em seguida, adicione `ag1-listener` depois de um espaço junto ao IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Para sair do **editor vi,** primeiro acerte na chave **Esc,** e depois insira o comando `:wq` para escrever o ficheiro e desistir. Faça isso em cada nó.

## <a name="test-the-listener-and-a-failover"></a>Teste o ouvinte e uma falha

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Teste de login no Servidor SQL utilizando o ouvinte do grupo de disponibilidade

1. Utilize o SQLCMD para iniciar sessão no nó principal do Servidor SQL utilizando o nome de ouvinte do grupo de disponibilidade:

    - Utilize um login que tenha sido previamente criado e substitua `<YourPassword>` pela palavra-passe correta. O exemplo abaixo utiliza o login `sa` que foi criado com o Servidor SQL.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Verifique o nome do servidor a que está ligado. Executar o seguinte comando em SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    A sua saída deve mostrar o nó primário atual. Isto deve ser `VM1` se nunca testou uma falha.

    Saia da sessão SQL digitando o comando `exit`.

### <a name="test-a-failover"></a>Teste uma falha

1. Executar o seguinte comando para falhar manualmente a réplica primária para `<VM2>` ou outra réplica. Substitua `<VM2>` pelo valor do seu nome de servidor.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Se verificar os seus constrangimentos, verá que outra restrição foi adicionada devido à falha manual:

    ```bash
    sudo pcs constraint list --full
    ```

    Verá que foi acrescentado um constrangimento com a `cli-prefer-ag_cluster-master` de identificação.

1. Retire a restrição com `cli-prefer-ag_cluster-master` de identificação utilizando o seguinte comando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Verifique os seus recursos de cluster utilizando o comando `sudo pcs resource`, e deve ver que a instância primária está agora `<VM2>`.

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

    - Utilize um login que tenha sido previamente criado e substitua `<YourPassword>` pela palavra-passe correta. O exemplo abaixo utiliza o login `sa` que foi criado com o Servidor SQL.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Verifique o servidor a que está ligado. Executar o seguinte comando em SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Devias ver que agora estás ligado ao VM a que falhaste.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os equilibradores de carga em Azure, consulte:

> [!div class="nextstepaction"]
> [Configure um balancer de carga para um grupo de disponibilidade em VMs de servidor Estivado Azure](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
