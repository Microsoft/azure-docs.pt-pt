---
title: 'Tutorial: configurar o grupo de disponibilidade'
description: Este tutorial mostra como criar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: 5c4eb5241cc5e50c11c05cac6909e37557ba106d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037518"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Tutorial: configurar o grupo de disponibilidade na VM de SQL Server do Azure manualmente

Este tutorial mostra como criar um grupo de disponibilidade SQL Server Always On em máquinas virtuais do Azure. O tutorial completo cria um grupo de disponibilidade com uma réplica de banco de dados em dois SQL Servers.

**Tempo estimado**: leva cerca de 30 minutos para ser concluído depois que os pré-requisitos são atendidos.

O diagrama ilustra o que você cria no tutorial.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Pré-requisitos

O tutorial pressupõe que você tenha uma compreensão básica de SQL Server Always On grupos de disponibilidade. Se você precisar de mais informações, consulte [visão geral de Always on grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

A tabela a seguir lista os pré-requisitos que você precisa concluir antes de iniciar este tutorial:

|  |Requisito |Descrição |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dois SQL Servers | -Em um conjunto de disponibilidade do Azure <br/> -Em um único domínio <br/> -Com o recurso de clustering de failover instalado |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Compartilhamento de arquivos para testemunha de cluster |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server conta de serviço | Conta de domínio |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent conta de serviço | Conta de domínio |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Portas de firewall abertas | -SQL Server: **1433** para instância padrão <br/> -Ponto de extremidade de espelhamento de banco de dados: **5022** ou qualquer porta disponível <br/> -Investigação de integridade do endereço IP do balanceador de carga do grupo de disponibilidade: **59999** ou qualquer porta disponível <br/> -Investigação de integridade do endereço IP do balanceador de carga principal do cluster: **58888** ou qualquer porta disponível |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adicionar recurso de cluster de failover | Ambos os SQL Servers exigem esse recurso |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de domínio de instalação | -Administrador local em cada SQL Server <br/> -Membro de SQL Server função de servidor fixa sysadmin para cada instância de SQL Server  |


Antes de começar o tutorial, você precisa [concluir os pré-requisitos para criar Always on grupos de disponibilidade em máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Se esses pré-requisitos já estiverem concluídos, você poderá ir para [criar cluster](#CreateCluster).

  >[!NOTE]
  > Muitas das etapas fornecidas neste tutorial agora podem ser automatizadas com a [CLI de VM do Azure SQL](virtual-machines-windows-sql-availability-group-cli.md) e com os [modelos de início rápido do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Criar o cluster

Após a conclusão dos pré-requisitos, a primeira etapa é criar um cluster de failover do Windows Server que inclua dois servidores SQL e um servidor testemunha.

1. RDP para o primeiro SQL Server usando uma conta de domínio que seja um administrador no SQL Server e no servidor testemunha.

   >[!TIP]
   >Se você seguiu o [documento pré-requisitos](virtual-machines-windows-portal-sql-availability-group-prereq.md), você criou uma conta chamada **CORP\Install**. Use esta conta.

2. No painel **Gerenciador do servidor** , selecione **ferramentas**e, em seguida, clique em **Gerenciador de cluster de failover**.
3. No painel esquerdo, clique com o botão direito do mouse em **Gerenciador de cluster de failover**e clique em **criar um cluster**.
   ![criar](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png) de cluster
4. No Assistente para criar cluster, crie um cluster de um nó percorrendo as páginas com as configurações na tabela a seguir:

   | Página | Definições |
   | --- | --- |
   | Antes de começar |Usar padrões |
   | Selecionar servidores |Digite o nome do primeiro SQL Server em **Inserir nome do servidor** e clique em **Adicionar**. |
   | Aviso de validação |Selecione **não. não preciso de suporte da Microsoft para este cluster e, portanto, não quero executar os testes de validação. Quando eu clicar em avançar, continuará criando o cluster**. |
   | Ponto de acesso para administrar o cluster |Digite um nome de cluster, por exemplo **SQLAGCluster1** no **nome do cluster**.|
   | Confirmação |Use os padrões, a menos que você esteja usando espaços de armazenamento. Consulte a observação após esta tabela. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Definir o endereço IP do cluster de failover do Windows Server

  > [!NOTE]
  > No Windows Server 2019, o cluster cria um **nome de servidor distribuído** em vez do **nome de rede do cluster**. Se você estiver usando o Windows Server 2019, ignore todas as etapas que se referem ao nome de núcleo do cluster neste tutorial. Você pode criar um nome de rede de cluster usando o [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Examine o [objeto cluster de failover de blog: cluster de rede](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) para obter mais informações. 

1. Em **Gerenciador de cluster de failover**, role para baixo até **recursos principais do cluster** e expanda os detalhes do cluster. Você deve ver o **nome** e os recursos de **endereço IP** no estado de **falha** . O recurso de endereço IP não pode ser colocado online porque o cluster recebe o mesmo endereço IP que o próprio computador, portanto, é um endereço duplicado.

2. Clique com o botão direito do mouse no recurso **endereço IP** com falha e clique em **Propriedades**.

   ![Propriedades do cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecione **endereço IP estático** e especifique um endereço disponível na mesma sub-rede que suas máquinas virtuais.

4. Na seção **recursos principais do cluster** , clique com o botão direito do mouse em nome do cluster e clique em **colocar online**. Em seguida, aguarde até que os dois recursos estejam online. Quando o recurso de nome de cluster fica online, ele atualiza o servidor DC com uma nova conta de computador do AD. Use essa conta do AD para executar o serviço clusterizado do grupo de disponibilidade mais tarde.

### <a name="addNode"></a>Adicionar os outros SQL Server ao cluster

Adicione os outros SQL Server ao cluster.

1. Na árvore do navegador, clique com o botão direito do mouse no cluster e clique em **adicionar nó**.

    ![Adicionar nó ao cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. No **Assistente para adicionar nó**, clique em **Avançar**. Na página **selecionar servidores** , adicione o segundo SQL Server. Digite o nome do servidor em **Inserir nome do servidor** e clique em **Adicionar**. Quando terminar, clique em **Avançar**.

1. Na página **aviso de validação** , clique em **não** (em um cenário de produção, você deve executar os testes de validação). Clique depois em **Seguinte**.

8. Na página **confirmação** , se você estiver usando espaços de armazenamento, desmarque a caixa de seleção **Adicionar todo o armazenamento elegível ao cluster.**

   ![Confirmação de adição de nó](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Se você estiver usando espaços de armazenamento e não marcar a seleção **Adicionar todo o armazenamento elegível ao cluster**, o Windows desanexará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecerão no Gerenciador de discos ou no Explorer até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell. Os espaços de armazenamento agrupam vários discos em pools de armazenamento. Para obter mais informações, consulte [espaços de armazenamento](https://technet.microsoft.com/library/hh831739).

1. Clique em **Seguinte**.

1. Clique em **Concluir**.

   Gerenciador de Cluster de Failover mostra que o cluster tem um novo nó e o lista no contêiner **nós** .

10. Faça logoff da sessão da área de trabalho remota.

### <a name="add-a-cluster-quorum-file-share"></a>Adicionar um compartilhamento de arquivos de quorum de cluster

Neste exemplo, o cluster do Windows usa um compartilhamento de arquivos para criar um quorum de cluster. Este tutorial usa um quorum de maioria de compartilhamento de nó e de arquivo. Para obter mais informações, veja [Compreender as Configurações do Quórum num Cluster de Ativação Pós-falha](https://technet.microsoft.com/library/cc731739.aspx).

1. Conecte-se ao servidor membro de testemunha de compartilhamento de arquivo com uma sessão de área de trabalho remota.

1. Em **Gerenciador do servidor**, clique em **ferramentas**. Abra o **Gerenciamento do computador**.

1. Clique em **pastas compartilhadas**.

1. Clique com o botão direito do mouse em **compartilhamentos**e clique em **novo compartilhamento...** .

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Use **o assistente para criar uma pasta compartilhada** para criar um compartilhamento.

1. Em **caminho da pasta**, clique em **procurar** e localize ou crie um caminho para a pasta compartilhada. Clique em **Seguinte**.

1. Em **nome, descrição e configurações,** Verifique o nome e o caminho do compartilhamento. Clique em **Seguinte**.

1. Em **permissões de pasta compartilhada** , defina **Personalizar permissões**. Clique em **personalizado...** .

1. Em **Personalizar permissões**, clique em **Adicionar...** .

1. Certifique-se de que a conta usada para criar o cluster tenha controle total.

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Clique em **OK**.

1. Em **permissões de pasta compartilhada**, clique em **concluir**. Clique em **concluir** novamente.  

1. Fazer logoff do servidor

### <a name="configure-cluster-quorum"></a>Configurar quorum do cluster

Em seguida, defina o quorum do cluster.

1. Conecte-se ao primeiro nó de cluster com a área de trabalho remota.

1. Em **Gerenciador de cluster de failover**, clique com o botão direito do mouse no cluster, aponte para **mais ações**e clique em **definir configurações de quorum do cluster...** .

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. Em **Assistente para configurar quorum de cluster**, clique em **Avançar**.

1. Na **opção Selecionar configuração de quorum**, escolha **selecionar a testemunha de quorum**e clique em **Avançar**.

1. Em **selecionar testemunha de quorum**, clique em **Configurar uma testemunha de compartilhamento de arquivos**.

   >[!TIP]
   >O Windows Server 2016 dá suporte a uma testemunha em nuvem. Se você escolher esse tipo de testemunha, não precisará de uma testemunha de compartilhamento de arquivos. Para obter mais informações, consulte [implantar uma testemunha em nuvem para um cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial usa uma testemunha de compartilhamento de arquivos, que é compatível com sistemas operacionais anteriores.

1. Em **Configurar testemunha de compartilhamento de arquivos**, digite o caminho para o compartilhamento que você criou. Clique em **Seguinte**.

1. Verifique as configurações em **confirmação**. Clique em **Seguinte**.

1. Clique em **Concluir**.

Os recursos principais de cluster são configurados com uma testemunha de compartilhamento de arquivos.

## <a name="enable-availability-groups"></a>Habilitar grupos de disponibilidade

Em seguida, habilite o recurso **grupos de disponibilidade AlwaysOn** . Execute estas etapas em ambos os SQL Servers.

1. Na tela **Iniciar** , inicie o **SQL Server Configuration Manager**.
2. Na árvore do navegador, clique em **serviços de SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.
3. Clique na guia **alta disponibilidade AlwaysOn** e selecione **habilitar grupos de disponibilidade AlwaysOn**, da seguinte maneira:

    ![Habilitar Grupos de Disponibilidade AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Clique em **Aplicar**. Clique em **OK** na caixa de diálogo pop-up.

5. Reinicie o serviço do SQL Server.

Repita essas etapas no outro SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Criar um banco de dados na primeira SQL Server

1. Inicie o arquivo RDP no primeiro SQL Server com uma conta de domínio que seja membro da função de servidor fixa sysadmin.
1. Abra SQL Server Management Studio e conecte-se ao primeiro SQL Server.
7. No Pesquisador de **objetos**, clique com o botão direito do mouse em **bancos** de dados e clique em **New Database**.
8. Em **nome do banco de dados**, digite **MyDB1**e clique em **OK**.

### <a name="backupshare"></a>Criar um compartilhamento de backup

1. Na primeira SQL Server em **Gerenciador do servidor**, clique em **ferramentas**. Abra o **Gerenciamento do computador**.

1. Clique em **pastas compartilhadas**.

1. Clique com o botão direito do mouse em **compartilhamentos**e clique em **novo compartilhamento...** .

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Use **o assistente para criar uma pasta compartilhada** para criar um compartilhamento.

1. Em **caminho da pasta**, clique em **procurar** e localize ou crie um caminho para a pasta compartilhada de backup do banco de dados. Clique em **Seguinte**.

1. Em **nome, descrição e configurações,** Verifique o nome e o caminho do compartilhamento. Clique em **Seguinte**.

1. Em **permissões de pasta compartilhada** , defina **Personalizar permissões**. Clique em **personalizado...** .

1. Em **Personalizar permissões**, clique em **Adicionar...** .

1. Verifique se as contas de serviço de SQL Server e SQL Server Agent para ambos os servidores têm controle total.

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Clique em **OK**.

1. Em **permissões de pasta compartilhada**, clique em **concluir**. Clique em **concluir** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Faça um backup completo do banco de dados

Você precisa fazer backup do novo banco de dados para inicializar a cadeia de logs. Se você não fizer um backup do novo banco de dados, ele não poderá ser incluído em um grupo de disponibilidade.

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no banco de dados, aponte para **tarefas...** , clique em **fazer backup**.

1. Clique em **OK** para fazer um backup completo no local de backup padrão.

## <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade
Agora você está pronto para configurar um grupo de disponibilidade usando as seguintes etapas:

* Crie um banco de dados no primeiro SQL Server.
* Faça um backup completo e um backup de log de transações do banco de dados
* Restaure os backups completo e de log para a segunda SQL Server com a opção **NORECOVERY**
* Criar o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis

### <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade:

1. Na sessão da área de trabalho remota para a primeira SQL Server. No Pesquisador de **objetos** no SSMS, clique com o botão direito do mouse em **alta disponibilidade AlwaysOn** e clique em **Assistente de novo grupo de disponibilidade**.

    ![Iniciar assistente de novo grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na página **introdução** , clique em **Avançar**. Na página **especificar nome do grupo de disponibilidade** , digite um nome para o grupo de disponibilidade, por exemplo, **AG1**, no nome do **grupo de disponibilidade**. Clique em **Seguinte**.

    ![Novo assistente AG, especifique o nome do AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na página **selecionar bancos** de dados, selecione seu banco de dados e clique em **Avançar**.

   >[!NOTE]
   >O banco de dados atende aos pré-requisitos para um grupo de disponibilidade porque você fez pelo menos um backup completo na réplica primária pretendida.

   ![Novo assistente AG, selecionar bancos de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na página **especificar réplicas** , clique em **Adicionar réplica**.

   ![Novo assistente AG, especificar réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. A caixa de diálogo **conectar ao servidor** é exibida. Digite o nome do segundo servidor em **nome do servidor**. Clique em **Ligar**.

   De volta à página **especificar réplicas** , agora você deve ver o segundo servidor listado em **réplicas de disponibilidade**. Configure as réplicas da seguinte maneira.

   ![Novo assistente AG, especificar réplicas (completo)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Clique em **pontos de extremidades** para ver o ponto de extremidade de espelhamento de banco de dados para este grupo de disponibilidade. Use a mesma porta que você usou ao definir a [regra de firewall para pontos de extremidade de espelhamento de banco de dados](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Novo assistente AG, selecione sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na página **selecionar sincronização de dados inicial** , selecione **completo** e especifique um local de rede compartilhado. Para o local, use o [compartilhamento de backup que você criou](#backupshare). No exemplo, foi, **\\\\\<primeiro SQL Server\>\backup** \\. Clique em **Seguinte**.

   >[!NOTE]
   >A sincronização completa faz um backup completo do banco de dados na primeira instância do SQL Server e restaura-o para a segunda instância. Para bancos de dados grandes, a sincronização completa não é recomendada porque pode levar muito tempo. Você pode reduzir esse tempo fazendo um backup do banco de dados manualmente e restaurando-o com `NO RECOVERY`. Se o banco de dados já estiver restaurado com `NO RECOVERY` no segundo SQL Server antes de configurar o grupo de disponibilidade, escolha **somente junção**. Se você quiser fazer o backup depois de configurar o grupo de disponibilidade, escolha **ignorar sincronização de dados inicial**.

    ![Novo assistente AG, selecione sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na página **validação** , clique em **Avançar**. Esta página deve ser semelhante à imagem a seguir:

    ![Novo assistente AG, validação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Há um aviso para a configuração do ouvinte porque você não configurou um ouvinte de grupo de disponibilidade. Você pode ignorar este aviso porque nas máquinas virtuais do Azure, você cria o ouvinte depois de criar o balanceador de carga do Azure.

10. Na página **Resumo** , clique em **concluir**e aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página **progresso** , você pode clicar em **mais detalhes** para exibir o progresso detalhado. Quando o assistente for concluído, inspecione a página **resultados** para verificar se o grupo de disponibilidade foi criado com êxito.

     ![Novo assistente AG, resultados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Clique em **fechar** para sair do assistente.

### <a name="check-the-availability-group"></a>Verificar o grupo de disponibilidade

1. No Pesquisador de **objetos**, expanda **alta disponibilidade AlwaysOn**e expanda **grupos de disponibilidade**. Agora você deve ver o novo grupo de disponibilidade neste contêiner. Clique com o botão direito do mouse no grupo de disponibilidade e clique em **Mostrar painel**.

   ![Mostrar painel do AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   O **painel AlwaysOn** deve ser semelhante a este.

   ![Painel do AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.

2. Em **Gerenciador de cluster de failover**, clique em seu cluster. Selecione **funções**. O nome do grupo de disponibilidade usado é uma função no cluster. Esse grupo de disponibilidade não tem um endereço IP para conexões de cliente, pois você não configurou um ouvinte. Você configurará o ouvinte depois de criar um balanceador de carga do Azure.

   ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente fazer failover do grupo de disponibilidade do Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **painel AlwaysOn** no SSMS. Para obter mais informações, consulte [restrições sobre como usar o Gerenciador de cluster de failover com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Neste ponto, você tem um grupo de disponibilidade com réplicas em duas instâncias do SQL Server. Você pode mover o grupo de disponibilidade entre instâncias. Você não pode se conectar ao grupo de disponibilidade ainda porque não tem um ouvinte. Em máquinas virtuais do Azure, o ouvinte requer um balanceador de carga. A próxima etapa é criar o balanceador de carga no Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Em máquinas virtuais do Azure, um grupo de disponibilidade SQL Server requer um balanceador de carga. O balanceador de carga mantém os endereços IP para os ouvintes do grupo de disponibilidade e o cluster de failover do Windows Server. Esta seção resume como criar o balanceador de carga no portal do Azure.

Um Azure Load Balancer pode ser um Standard Load Balancer ou um Load Balancer básico. Standard Load Balancer tem mais recursos do que o Load Balancer básico. Para um grupo de disponibilidade, o Standard Load Balancer será necessário se você usar uma zona de disponibilidade (em vez de um conjunto de disponibilidade). Para obter detalhes sobre a diferença entre os tipos de balanceador de carga, consulte [Load Balancer a comparação de SKU](../../../load-balancer/load-balancer-overview.md#skus).

1. Na portal do Azure, vá para o grupo de recursos onde estão os servidores SQL e clique em **+ Adicionar**.
1. Procure **Load Balancer**. Escolha o balanceador de carga publicado pela Microsoft.

   ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Clique em **Criar**.
1. Configure os parâmetros a seguir para o balanceador de carga.

   | Definição | Campo |
   | --- | --- |
   | **Nome** |Use um nome de texto para o balanceador de carga, por exemplo, **sqlLB**. |
   | **Tipo** |Interno |
   | **Rede virtual** |Use o nome da rede virtual do Azure. |
   | **Sub-rede** |Use o nome da sub-rede em que a máquina virtual está.  |
   | **Atribuição de endereço IP** |Estático |
   | **Endereço IP** |Use um endereço disponível da sub-rede. Use esse endereço para o ouvinte do grupo de disponibilidade. Observe que isso é diferente do endereço IP do cluster.  |
   | **Subscrição** |Use a mesma assinatura que a máquina virtual. |
   | **Localização** |Use o mesmo local que a máquina virtual. |

   A folha portal do Azure deve ter a seguinte aparência:

   ![Criar Balanceador de carga](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Clique em **criar**para criar o balanceador de carga.

Para configurar o balanceador de carga, você precisa criar um pool de back-end, uma investigação e definir as regras de balanceamento de carga. Faça isso no portal do Azure.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Adicionar pool de back-end para o ouvinte do grupo de disponibilidade

1. No portal do Azure, vá para seu grupo de disponibilidade. Talvez seja necessário atualizar a exibição para ver o balanceador de carga criado recentemente.

   ![Localizar Load Balancer no grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Clique no balanceador de carga, clique em **pools de back-end**e clique em **+ Adicionar**.

1. Digite um nome para o pool de back-end.

1. Associe o pool de back-end ao conjunto de disponibilidade que contém as VMs.

1. Em **configurações de IP de rede de destino**, marque **máquina virtual** e escolha ambas as máquinas virtuais que hospedarão réplicas do grupo de disponibilidade. Não inclua o servidor testemunha de compartilhamento de arquivos.

   >[!NOTE]
   >Se ambas as máquinas virtuais não forem especificadas, as conexões só terão sucesso com a réplica primária.

1. Clique em **OK** para criar o pool de back-end.

### <a name="set-the-probe"></a>Definir a investigação

1. Clique no balanceador de carga, clique em **investigações de integridade**e clique em **+ Adicionar**.

1. Defina a investigação de integridade do ouvinte da seguinte maneira:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Escolher TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 59999 |
   | **Intervalo**  | A quantidade de tempo entre as tentativas de investigação em segundos |5 |
   | **Limite não íntegro** | O número de falhas de investigação consecutivas que devem ocorrer para uma máquina virtual ser considerada não íntegra  | 2 |

1. Clique em **OK** para definir a investigação de integridade.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de balanceamento de carga

1. Clique no balanceador de carga, clique em **regras de balanceamento de carga**e clique em **+ Adicionar**.

1. Defina as regras de balanceamento de carga do ouvinte da seguinte maneira.

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP de front-end** | Escolher um endereço |Use o endereço que você criou quando criou o balanceador de carga. |
   | **Protocolo** | Escolher TCP |TCP |
   | **Porta** | Usar a porta para o ouvinte do grupo de disponibilidade | 1433 |
   | **Porta de back-end** | Este campo não é usado quando o IP flutuante é definido para retorno de servidor direto | 1433 |
   | **Provas** |O nome que você especificou para a investigação | SQLAlwaysOnEndPointProbe |
   | **Persistência da sessão** | Lista suspensa | **Nenhum** |
   | **Tempo limite de ociosidade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **IP flutuante (retorno de servidor direto)** | |Ativado |

   > [!WARNING]
   > O retorno de servidor direto é definido durante a criação. Não pode ser alterado.

1. Clique em **OK** para definir as regras de balanceamento de carga do ouvinte.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Adicionar o endereço IP do núcleo do cluster para o WSFC (cluster de failover do Windows Server)

O endereço IP do WSFC também precisa estar no balanceador de carga.

1. No portal, no mesmo balanceador de carga do Azure, clique em **configuração de IP de front-end** e clique em **+ Adicionar**. Use o endereço IP que você configurou para o WSFC nos recursos principais do cluster. Defina o endereço IP como estático.

1. No balanceador de carga, clique em **investigações de integridade**e clique em **+ Adicionar**.

1. Defina a investigação de integridade do endereço IP principal do Cluster WSFC da seguinte maneira:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPointProbe |
   | **Protocolo** | Escolher TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 58888 |
   | **Intervalo**  | A quantidade de tempo entre as tentativas de investigação em segundos |5 |
   | **Limite não íntegro** | O número de falhas de investigação consecutivas que devem ocorrer para uma máquina virtual ser considerada não íntegra  | 2 |

1. Clique em **OK** para definir a investigação de integridade.

1. Defina as regras de balanceamento de carga. Clique em **regras de balanceamento de carga**e clique em **+ Adicionar**.

1. Defina as regras de balanceamento de carga do endereço IP do núcleo do cluster da seguinte maneira.

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPoint |
   | **Endereço IP de front-end** | Escolher um endereço |Use o endereço que você criou quando configurou o endereço IP do WSFC. Isso é diferente do endereço IP do ouvinte |
   | **Protocolo** | Escolher TCP |TCP |
   | **Porta** | Use a porta para o endereço IP do cluster. Esta é uma porta disponível que não é usada para a porta de investigação do ouvinte. | 58888 |
   | **Porta de back-end** | Este campo não é usado quando o IP flutuante é definido para retorno de servidor direto | 58888 |
   | **Provas** |O nome que você especificou para a investigação | WSFCEndPointProbe |
   | **Persistência da sessão** | Lista suspensa | **Nenhum** |
   | **Tempo limite de ociosidade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **IP flutuante (retorno de servidor direto)** | |Ativado |

   > [!WARNING]
   > O retorno de servidor direto é definido durante a criação. Não pode ser alterado.

1. Clique em **OK** para definir as regras de balanceamento de carga.

## <a name="configure-listener"></a>Configurar o ouvinte

A próxima etapa é configurar um ouvinte de grupo de disponibilidade no cluster de failover.

> [!NOTE]
> Este tutorial mostra como criar um único ouvinte com um endereço IP ILB. Para criar um ou mais ouvintes usando um ou mais endereços IP, consulte [criar ouvinte de grupo de disponibilidade e balanceador de carga | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Definir porta do ouvinte

Em SQL Server Management Studio, defina a porta do ouvinte.

1. Inicie o SQL Server Management Studio e conecte-se à réplica primária.

1. Navegue até **alta disponibilidade AlwaysOn** | **grupos de disponibilidade** | **ouvintes do grupo de disponibilidade**.

1. Agora você deve ver o nome do ouvinte que você criou em Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

1. Na caixa **porta** , especifique o número da porta para o ouvinte do grupo de disponibilidade. 1433 é o padrão e, em seguida, clique em **OK**.

Agora você tem um grupo de disponibilidade SQL Server em máquinas virtuais do Azure em execução no modo do Gerenciador de recursos.

## <a name="test-connection-to-listener"></a>Testar conexão com o ouvinte

Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não possui a réplica. Você pode usar os outros SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando sqlcmd a seguir se conecta a um ouvinte na porta 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server hospeda a réplica primária.

> [!TIP]
> Verifique se a porta que você especificou está aberta no firewall de ambos os SQL Servers. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Para obter mais informações, consulte [Adicionar ou editar regra de firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Passos seguintes

- [Adicione um endereço IP a um balanceador de carga para um segundo grupo de disponibilidade](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
