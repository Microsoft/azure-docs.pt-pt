---
title: Tutorial - máquinas virtuais do Azure - de grupos de disponibilidade do SQL Server | Documentos da Microsoft
description: Este tutorial mostra como criar um SQL Server grupo de Disponibilidade AlwaysOn em máquinas de virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: d86538fca907f7181bf58ff236bba8de186641fb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003445"
---
# <a name="tutorial-configure-always-on-availability-group-in-azure-vm-manually"></a>Tutorial: Configurar grupo de Disponibilidade AlwaysOn na VM do Azure manualmente

Este tutorial mostra como criar um SQL Server grupo de Disponibilidade AlwaysOn em máquinas de virtuais do Azure. O tutorial completo cria um grupo de disponibilidade com uma réplica de base de dados em dois servidores SQL.

**Estimativa de tempo**: Demora cerca de 30 minutos a concluir depois dos pré-requisitos são cumpridos.

O diagrama ilustra o que criar no tutorial.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Pré-requisitos

O tutorial parte do princípio de que tem uma compreensão básica do SQL Server grupos de Disponibilidade AlwaysOn. Se precisar de mais informações, veja [descrição geral de grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

A tabela seguinte lista os pré-requisitos que tem de concluir antes de começar este tutorial:

|  |Requisito |Descrição |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dois servidores SQL | -Num conjunto de disponibilidade do Azure <br/> -Num único domínio <br/> -Com a funcionalidade de Clustering de ativação pós-falha instalada |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Partilha de ficheiros de testemunho de cluster |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço do SQL Server | Conta do domínio |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço do SQL Server Agent | Conta do domínio |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Abrir portas de firewall | – O SQL Server: **1433** para a instância predefinida <br/> -Ponto final de espelhamento da base de dados: **5022** ou qualquer porta disponível <br/> -Grupo de disponibilidade sonda de estado de funcionamento do endereço IP do Balanceador de carga: **59999** ou qualquer porta disponível <br/> -Core sonda Balanceador de carga IP endereço estado de funcionamento do cluster: **58888** ou qualquer porta disponível |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adicionar a funcionalidade de Clustering de ativação pós-falha | Esta funcionalidade necessitam de ambos os servidores SQL |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de domínio de instalação | -Administrador local em cada servidor de SQL <br/> -Membro da função de servidor fixa sysadmin do SQL Server para cada instância do SQL Server  |


Antes de iniciar o tutorial, precisa [conclua os pré-requisitos para a criação de grupos de Disponibilidade AlwaysOn em máquinas de virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Se estes pré-requisitos forem concluídos já, pode ir para [criar clusters](#CreateCluster).

  >[!NOTE]
  > Muitos das etapas fornecidas neste tutorial agora podem ser automatizados com [CLI de VM do SQL do Azure](virtual-machines-windows-sql-availability-group-cli.md) e [modelos de início rápido do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Criar o cluster

Depois de concluir os pré-requisitos, a primeira etapa é criar um Cluster de ativação pós-falha do Windows Server que inclui dois SQL Servers e um servidor-testemunha.

1. RDP para o primeiro servidor de SQL com uma conta de domínio que seja um administrador no SQL Servers e o servidor-testemunha.

   >[!TIP]
   >Se tiver seguido os [documento dos pré-requisitos](virtual-machines-windows-portal-sql-availability-group-prereq.md), que criou uma conta chamada **CORP\Install**. Utilize esta conta.

2. Na **Gestor de servidores** dashboard, selecione **ferramentas**e, em seguida, clique em **Gestor de clusters de ativação pós-falha**.
3. No painel esquerdo, clique com botão direito **Gestor de clusters de ativação pós-falha**e, em seguida, clique em **criar um Cluster**.
   ![Criar Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. No Assistente para criar clusters, crie um cluster de um nó cumprindo as páginas com as definições na tabela a seguir:

   | Página | Definições |
   | --- | --- |
   | Antes de começar |Utilizar predefinições |
   | Selecionar servidores |Escreva o nome do SQL Server primeiro na **introduza o nome do servidor** e clique em **Add**. |
   | Aviso de validação |Selecione **não. eu não necessitar de suporte da Microsoft para este cluster e, portanto, não desejaremos executar os testes de validação. Quando eu clicar em Avançar, pode continuar a criar o cluster**. |
   | Ponto de acesso para administrar o Cluster |Escreva um nome de cluster, por exemplo **SQLAGCluster1** na **nome do Cluster**.|
   | Confirmação |Utilize as predefinições, exceto se estiver a utilizar espaços de armazenamento. Consulte a nota após esta tabela. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Definir o servidor de Windows endereço IP do cluster de ativação pós-falha

1. Na **Gestor de clusters de ativação pós-falha**, desloque para baixo até **recursos principais do Cluster** e expandir os detalhes do cluster. Deverá ver ambos os **nome** e o **endereço IP** recursos no **falhou** estado. O recurso de endereço IP não pode ser colocado online, porque o cluster é atribuído o mesmo endereço IP que a máquina em si, pelo que é um endereço duplicado.

2. Com o botão direito a falhadas **endereço IP** recursos e clique em **propriedades**.

   ![Propriedades do cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecione **endereço IP estático** e especifique um endereço disponível a partir da mesma sub-rede como suas máquinas virtuais.

4. Na **recursos principais do Cluster** secção, clique no nome do cluster e clique em **colocar Online**. Em seguida, aguarde até que ambos os recursos estão online. Quando o recurso de nome de cluster online, ele atualiza o servidor de controlador de domínio com uma nova conta de computador do AD. Utilize esta conta do AD para executar o serviço de grupo de disponibilidade em cluster mais tarde.

### <a name="addNode"></a>Adicionar o servidor de SQL ao cluster

Adicione outro SQL Server para o cluster.

1. Na árvore do browser, faça duplo clique no cluster e clique em **adicionar nó**.

    ![Adicionar nó ao Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Na **Assistente de adicionar nó**, clique em **próxima**. Na **selecionar servidores** página, adicione o segundo servidor de SQL. Escreva o nome do servidor no **introduza o nome do servidor** e, em seguida, clique em **Add**. Quando tiver terminado, clique em **seguinte**.

1. Na **aviso de validação** página, clique em **não** (num cenário de produção deve executar os testes de validação). Clique depois em **Seguinte**.

8. Na **confirmação** página, se estiver a utilizar espaços de armazenamento, desmarque a caixa de seleção rotulada como **adicionar todo o armazenamento elegível ao cluster.**

   ![Adicionar a confirmação de nó](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Se estiver a utilizar espaços de armazenamento e não desmarque **adicionar todo o armazenamento elegível ao cluster**, Windows desliga os discos virtuais durante o processo de clustering. Como resultado, eles não serão apresentados no Gestor de discos ou Explorer até que os espaços de armazenamento são removidos do cluster e voltar a ligar com o PowerShell. Espaços de armazenamento agrupa vários discos para agrupamentos de armazenamento. Para obter mais informações, consulte [espaços de armazenamento](https://technet.microsoft.com/library/hh831739).

1. Clique em **Seguinte**.

1. Clique em **Concluir**.

   Gestor de clusters de ativação pós-falha mostra que o cluster tem um novo nó e lista-na **nós** contentor.

10. Termine a sessão de área de trabalho remota.

### <a name="add-a-cluster-quorum-file-share"></a>Adicionar uma partilha de ficheiros de quórum do cluster

Neste exemplo o cluster do Windows utiliza uma partilha de ficheiros para criar um quórum de cluster. Este tutorial utiliza um quórum de nós e maioria de partilha de ficheiros. Para obter mais informações, consulte [Noções sobre configurações de quórum num Cluster de ativação pós-falha](https://technet.microsoft.com/library/cc731739.aspx).

1. Ligar ao servidor de membro de testemunho de partilha de ficheiros com uma sessão de área de trabalho remoto.

1. No **Gestor de servidores**, clique em **ferramentas**. Open **gestão de computadores**.

1. Clique em **pastas compartilhadas**.

1. Com o botão direito **partilhas**e clique em **nova partilha...** .

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Uso **criar um Assistente de pasta partilhada** para criar uma partilha.

1. No **caminho da pasta**, clique em **procurar** e localizar ou criar um caminho para a pasta partilhada. Clique em **Seguinte**.

1. Na **nome, descrição e as definições de** Certifique-se de que o nome da partilha e o caminho. Clique em **Seguinte**.

1. No **permissões de pastas partilhadas** definir **personalizar permissões**. Clique em **personalizado...** .

1. No **personalizar permissões**, clique em **adicionar...** .

1. Certifique-se de que a conta utilizada para criar o cluster tem controlo total.

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Clique em **OK**.

1. Na **permissões de pastas partilhadas**, clique em **concluir**. Clique em **concluir** novamente.  

1. Termine a sessão do servidor

### <a name="configure-cluster-quorum"></a>Configurar o quórum do cluster

Em seguida, configure o quórum do cluster.

1. Ligar ao primeiro nó de cluster com o ambiente de trabalho remoto.

1. Na **Gestor de clusters de ativação pós-falha**, clique com o botão direito do cluster, aponte para **mais ações**e clique em **configurar definições de quórum do Cluster...** .

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. Na **configurar Assistente de quórum de Cluster**, clique em **próxima**.

1. Na **selecionar opção de configuração de quórum**, escolha **selecione o testemunho de quórum**e clique em **seguinte**.

1. No **selecionar testemunho de quórum**, clique em **configurar um testemunho de partilha de ficheiros**.

   >[!TIP]
   >Windows Server 2016 suporta um testemunho de nuvem. Se escolher este tipo de testemunho, não é necessário um ficheiro de testemunho de partilha. Para obter mais informações, consulte [implementar um testemunho de nuvem para um Cluster de ativação pós-falha](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial utiliza um testemunho de partilha de ficheiros, que é suportado pelos sistemas operacionais anteriores.

1. No **configurar testemunho de partilha de ficheiros**, escreva o caminho para a partilha que criou. Clique em **Seguinte**.

1. Verifique as definições no **confirmação**. Clique em **Seguinte**.

1. Clique em **Concluir**.

Os recursos principais do cluster estão configurados com um testemunho de partilha de ficheiros.

## <a name="enable-availability-groups"></a>Ativar grupos de disponibilidade

Em seguida, ativar a **grupos de Disponibilidade AlwaysOn** funcionalidade. Execute estes passos em ambos os servidores SQL.

1. Do **começar** ecrã, inicie **Gestor de configuração do SQL Server**.
2. Na árvore do browser, clique em **SQL Server Services**, em seguida, clique com botão direito a **SQL Server (MSSQLSERVER)** serviço e clique em **propriedades**.
3. Clique nas **elevada disponibilidade do AlwaysOn** separador, em seguida, selecione **ative os grupos de Disponibilidade AlwaysOn**, da seguinte forma:

    ![Ativar grupos de Disponibilidade AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Clique em **Aplicar**. Clique em **OK** na caixa de diálogo pop-up.

5. Reinicie o serviço do SQL Server.

Repita estes passos no outro servidor SQL.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Criar uma base de dados no primeiro servidor de SQL

1. Inicie o ficheiro RDP para o primeiro servidor de SQL com uma conta de domínio que seja membro da função de servidor fixa de sysadmin.
1. Abra o SQL Server Management Studio e ligue-se para o primeiro servidor de SQL.
7. Na **Object Explorer**, clique com botão direito **bases de dados** e clique em **nova base de dados**.
8. Na **nome da base de dados**, tipo **MyDB1**, em seguida, clique em **OK**.

### <a name="backupshare"></a> Criar uma partilha de cópia de segurança

1. No primeiro servidor SQL no **Gestor de servidores**, clique em **ferramentas**. Open **gestão de computadores**.

1. Clique em **pastas compartilhadas**.

1. Com o botão direito **partilhas**e clique em **nova partilha...** .

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Uso **criar um Assistente de pasta partilhada** para criar uma partilha.

1. No **caminho da pasta**, clique em **procurar** e localizar ou criar um caminho para a pasta partilhada cópia de segurança da base de dados. Clique em **Seguinte**.

1. Na **nome, descrição e as definições de** Certifique-se de que o nome da partilha e o caminho. Clique em **Seguinte**.

1. No **permissões de pastas partilhadas** definir **personalizar permissões**. Clique em **personalizado...** .

1. No **personalizar permissões**, clique em **adicionar...** .

1. Certifique-se de que as contas de serviço do SQL Server e SQL Server Agent para ambos os servidores têm controle total.

   ![Nova partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Clique em **OK**.

1. Na **permissões de pastas partilhadas**, clique em **concluir**. Clique em **concluir** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Realizar um backup do banco de dados completo

Terá de criar cópias de segurança nova base de dados para inicializar a cadeia de registos. Se não efetuar uma cópia de segurança da base de dados nova, não pode ser incluído num grupo de disponibilidade.

1. Na **Object Explorer**, clique com o botão direito na base de dados, aponte para **tarefas...** , clique em **cópia de segurança**.

1. Clique em **OK** para realizar um backup completo para a localização de cópia de segurança predefinida.

## <a name="create-the-availability-group"></a>Criar o grupo de disponibilidade
Está agora pronto para configurar um grupo de disponibilidade utilizando os seguintes passos:

* Crie uma base de dados no primeiro servidor de SQL.
* Faça uma cópia de segurança completa e uma cópia de segurança do registo de transações da base de dados
* Restaurar o completo e backups de log para o segundo servidor de SQL com o **NORECOVERY** opção
* Criar o grupo de disponibilidade (**AG1**) com consolidação síncrona, a ativação pós-falha automática e réplicas secundárias legíveis

### <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade:

1. Na sessão de ambiente de trabalho remoto para o primeiro servidor de SQL. Na **Object Explorer** no SSMS, clique com botão direito **elevada disponibilidade do AlwaysOn** e clique em **Assistente de novo grupo de disponibilidade**.

    ![Iniciar o Assistente de novo grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na **introdução** página, clique em **próxima**. Na **Especificar nome do grupo de disponibilidade** página, escreva um nome para o grupo de disponibilidade, por exemplo **AG1**, na **nome do grupo de disponibilidade**. Clique em **Seguinte**.

    ![Novo Assistente de AG, especifique o nome AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na **selecionar bases de dados** página, selecione a sua base de dados e clique em **próxima**.

   >[!NOTE]
   >A base de dados cumpre os pré-requisitos para um grupo de disponibilidade como efetuou, pelo menos, uma cópia de segurança completa na réplica primária pretendida.

   ![Novo Assistente de AG, selecione as bases de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na **especificar réplicas** página, clique em **Adicionar réplica**.

   ![Novo Assistente de AG, especificar réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. O **ligar ao servidor** aparece a caixa de diálogo. Escreva o nome do segundo servidor na **nome do servidor**. Clique em **Ligar**.

   De volta a **especificar réplicas** página, deverá ver agora o segundo servidor listado no **réplicas de disponibilidade**. Configure as réplicas da seguinte forma.

   ![Novo Assistente de AG, especificar réplicas (completa)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Clique em **pontos de extremidade** para ver o ponto final de espelhamento para este grupo de disponibilidade. Utilize a mesma porta que utilizou quando configurou o [regra de firewall para pontos finais de espelhamento da base de dados](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Novo Assistente de AG, selecione a sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na **selecionar sincronização de dados inicial** , selecione **completo** e especifique uma localização de rede partilhada. Para a localização, utilize o [partilha de cópia de segurança que criou](#backupshare). No exemplo tenha sido,  **\\ \\ \<primeiro servidor de SQL\>\Backup\\**. Clique em **Seguinte**.

   >[!NOTE]
   >Sincronização completa usa uma cópia de segurança completa da base de dados na primeira instância do SQL Server e restaurá-lo a segunda instância. Sincronização completa não é recomendada para grandes bancos de dados, porque poderá demorar muito tempo. Pode reduzir esse tempo, manualmente fazer uma cópia de segurança da base de dados e o Restaurei com `NO RECOVERY`. Se a base de dados já está a ser restaurado com `NO RECOVERY` no SQL Server segundo antes de configurar o grupo de disponibilidade, escolha **apenas junção**. Se pretender efetuar a cópia de segurança depois de configurar o grupo de disponibilidade, escolha **ignorar sincronização de dados inicial**.

    ![Novo Assistente de AG, selecione a sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na **validação** página, clique em **próxima**. Esta página deve ser semelhante à imagem seguinte:

    ![Novo Assistente de AG, validação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Há um aviso para a configuração do serviço de escuta porque não tiver configurado um serviço de escuta do grupo de disponibilidade. Pode ignorar este aviso, porque nas máquinas virtuais do Azure cria o serviço de escuta depois de criar o Balanceador de carga do Azure.

10. Na **resumo** página, clique em **concluir**, em seguida, aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na **progresso** página, pode clicar em **obter mais detalhes** para ver o progresso detalhado. Quando o assistente for concluído, Inspecione o **resultados** página para verificar se o grupo de disponibilidade é criado com êxito.

     ![Novo Assistente de AG, os resultados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Clique em **fechar** para sair do assistente.

### <a name="check-the-availability-group"></a>Verifique o grupo de disponibilidade

1. Na **Object Explorer**, expanda **elevada disponibilidade do AlwaysOn**, em seguida, expanda **grupos de disponibilidade**. Agora, deverá ver o novo grupo de disponibilidade neste contentor. O grupo de disponibilidade com o botão direito e clique em **Mostrar Dashboard**.

   ![Mostrar o Dashboard de AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Sua **painel do AlwaysOn** deve ser semelhante ao seguinte.

   ![Dashboard de AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Pode ver as réplicas, o modo de ativação pós-falha de cada réplica e o estado de sincronização.

2. Na **Gestor de clusters de ativação pós-falha**, clique em seu cluster. Selecione **funções**. O nome do grupo de disponibilidade que utilizou é uma função no cluster. Esse grupo de disponibilidade não tem um endereço IP para ligações de cliente, porque não tiver configurado um serviço de escuta. Depois de criar um balanceador de carga do Azure, que irá configurar o serviço de escuta.

   ![AG no Gestor de clusters de ativação pós-falha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente fazer a ativação pós-falha o grupo de disponibilidade do Gestor de clusters de ativação pós-falha. Todas as operações de ativação pós-falha devem ser efetuadas de dentro **painel do AlwaysOn** no SSMS. Para obter mais informações, consulte [restrições no usando a ativação pós-falha Gestor de clusters com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Neste ponto, tem um grupo de disponibilidade com réplicas em duas instâncias do SQL Server. Pode mover o grupo de disponibilidade entre instâncias. Não consegue ligar ao grupo de disponibilidade ainda porque não tem um serviço de escuta. Em máquinas virtuais do Azure, o serviço de escuta requer um balanceador de carga. A próxima etapa é criar o Balanceador de carga no Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Em máquinas virtuais do Azure, um grupo de disponibilidade do SQL Server requer um balanceador de carga. O Balanceador de carga contém os endereços IP para os serviços de escuta do grupo de disponibilidade e o Cluster de ativação pós-falha do Windows Server. Esta secção resume como criar o Balanceador de carga no portal do Azure.

Um balanceador de carga do Azure pode ser um balanceador de carga Standard ou um balanceador de carga básico. Balanceador de carga standard tem mais recursos do que o Balanceador de carga básico. Para um grupo de disponibilidade, o Balanceador de carga Standard é necessário se utilizar uma zona de disponibilidade (em vez de um conjunto de disponibilidade). Para obter detalhes sobre a diferença entre os tipos de Balanceador de carga, veja [comparação de SKU do Balanceador de carga](../../../load-balancer/load-balancer-overview.md#skus).

1. No portal do Azure, vá para o grupo de recursos onde os seus servidores SQL estão e clique em **+ adicionar**.
1. Procure **Balanceador de carga**. Escolha o Balanceador de carga, publicado pela Microsoft.

   ![AG no Gestor de clusters de ativação pós-falha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Clique em **Criar**.
1. Configure os parâmetros seguintes para o Balanceador de carga.

   | Definição | Campo |
   | --- | --- |
   | **Nome** |Utilizar um nome de texto para o Balanceador de carga, por exemplo **sqlLB**. |
   | **Tipo** |Interno |
   | **Rede virtual** |Utilize o nome da rede virtual do Azure. |
   | **Sub-rede** |Utilize o nome da sub-rede que a máquina virtual está no.  |
   | **Atribuição de endereços IP** |Estático |
   | **Endereço IP** |Utilize um endereço disponível da sub-rede. Utilize este endereço de seu serviço de escuta do grupo de disponibilidade. Tenha em atenção que isto é diferente do seu endereço IP do cluster.  |
   | **Subscrição** |Utilize a mesma subscrição que a máquina virtual. |
   | **Localização** |Utilize a mesma localização que a máquina virtual. |

   O painel do portal do Azure deve ter este aspeto:

   ![Criar Balanceador de carga](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Clique em **criar**, para criar o Balanceador de carga.

Para configurar o Balanceador de carga, terá de criar um conjunto de back-end, uma sonda e defina a regras de balanceamento de carga. Fazer isso com o portal do Azure.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Adicionar conjunto de back-end para o serviço de escuta do grupo de disponibilidade

1. No portal do Azure, aceda ao seu grupo de disponibilidade. Poderá ter de atualizar a vista para ver o Balanceador de carga criado recentemente.

   ![Localizar o Balanceador de carga no grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Clique no balanceador de carga, clique em **conjuntos de back-end**e clique em **+ adicionar**.

1. Escreva um nome para o conjunto de back-end.

1. Associe o conjunto de back-end com o conjunto de disponibilidade que contém as VMs.

1. Sob **configurações de IP de rede de destino**, verifique **máquina VIRTUAL** e escolher as máquinas virtuais que irão alojar réplicas do grupo de disponibilidade. Não inclua o servidor de testemunho de partilha de ficheiros.

   >[!NOTE]
   >Se ambas as máquinas virtuais não forem especificadas, as ligações só serão bem sucedida para a réplica primária.

1. Clique em **OK** para criar o conjunto de back-end.

### <a name="set-the-probe"></a>Definir a sonda

1. Clique no balanceador de carga, clique em **sondas de estado de funcionamento**e clique em **+ adicionar**.

1. Defina a sonda de estado de funcionamento do serviço de escuta da seguinte forma:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Selecione TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 59999 |
   | **Intervalo**  | A quantidade de tempo entre tentativas da sonda em segundos |5 |
   | **Limiar de mau estado de funcionamento** | O número de falhas consecutivas da sonda que têm de ocorrer para uma máquina virtual ser considerado em mau estado de funcionamento  | 2 |

1. Clique em **OK** para definir a sonda de estado de funcionamento.

### <a name="set-the-load-balancing-rules"></a>Definir a regras de balanceamento de carga

1. Clique no balanceador de carga, clique em **regras de balanceamento de carga**e clique em **+ adicionar**.

1. Defina a forma de regras de balanceamento de carga de serviço de escuta.

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP de front-end** | Escolha um endereço |Utilize o endereço que criou quando criou o Balanceador de carga. |
   | **Protocolo** | Selecione TCP |TCP |
   | **Porta** | Utilizar a porta para o serviço de escuta do grupo de disponibilidade | 1433 |
   | **Porta de back-end** | Este campo não é utilizado quando o IP flutuante está definida para direta do servidor retorno | 1433 |
   | **Sonda** |O nome que especificou para a sonda | SQLAlwaysOnEndPointProbe |
   | **Persistência da sessão** | Na lista pendente | **Nenhum** |
   | **Tempo limite de inatividade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **Vírgula flutuante (devolução direta do servidor) de IP** | |Ativado |

   > [!WARNING]
   > Devolução direta do servidor é definida durante a criação. Não pode ser alterado.

1. Clique em **OK** para definir a regras de balanceamento de carga do serviço de escuta.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Adicionar o endereço IP de núcleo de cluster para o Cluster de ativação pós-falha do Windows Server (WSFC)

O endereço IP do WSFC também tem de ser no balanceador de carga.

1. No portal, no mesmo Balanceador de carga do Azure, clique em **configuração do IP de front-end** e clique em **+ adicionar**. Utilize o endereço IP que configurou para o WSFC nos recursos principais do cluster. Defina o endereço IP estático como.

1. No balanceador de carga, clique em **sondas de estado de funcionamento**e clique em **+ adicionar**.

1. Defina a sonda de estado de funcionamento de endereço IP do WSFC cluster core da seguinte forma:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPointProbe |
   | **Protocolo** | Selecione TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 58888 |
   | **Intervalo**  | A quantidade de tempo entre tentativas da sonda em segundos |5 |
   | **Limiar de mau estado de funcionamento** | O número de falhas consecutivas da sonda que têm de ocorrer para uma máquina virtual ser considerado em mau estado de funcionamento  | 2 |

1. Clique em **OK** para definir a sonda de estado de funcionamento.

1. Defina a regras de balanceamento de carga. Clique em **regras de balanceamento de carga**e clique em **+ adicionar**.

1. Defina a carga de endereço do IP do cluster principais regras de balanceamento da seguinte forma.

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPoint |
   | **Endereço IP de front-end** | Escolha um endereço |Utilize o endereço que criou quando configurou o endereço IP do WSFC. Isso é diferente do endereço IP do serviço de escuta |
   | **Protocolo** | Selecione TCP |TCP |
   | **Porta** | Utilize a porta para o endereço IP do cluster. Esta é uma porta disponível que não é utilizada para a porta de sonda do serviço de escuta. | 58888 |
   | **Porta de back-end** | Este campo não é utilizado quando o IP flutuante está definida para direta do servidor retorno | 58888 |
   | **Sonda** |O nome que especificou para a sonda | WSFCEndPointProbe |
   | **Persistência da sessão** | Na lista pendente | **Nenhum** |
   | **Tempo limite de inatividade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **Vírgula flutuante (devolução direta do servidor) de IP** | |Ativado |

   > [!WARNING]
   > Devolução direta do servidor é definida durante a criação. Não pode ser alterado.

1. Clique em **OK** para definir a regras de balanceamento de carga.

## <a name="configure-listener"></a> Configurar o serviço de escuta

A próxima coisa a fazer é configurar um serviço de escuta do grupo de disponibilidade no cluster de ativação pós-falha.

> [!NOTE]
> Este tutorial mostra como criar um único serviço de escuta - com um endereço IP do ILB. Para criar um ou mais serviços de escuta com um ou mais endereços IP, consulte [Balanceador de carga e do serviço de escuta do grupo de disponibilidade criar | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Porta do serviço de escuta de conjunto

No SQL Server Management Studio, defina a porta do serviço de escuta.

1. Inicie o SQL Server Management Studio e ligue-se para a réplica primária.

1. Navegue para **AlwaysOn elevada disponibilidade** | **grupos de disponibilidade** | **serviços de escuta do grupo de disponibilidade**.

1. Agora, deverá ver o nome do serviço de escuta que criou no Gestor de clusters de ativação pós-falha. O nome do serviço de escuta com o botão direito e clique em **propriedades**.

1. Na **porta** caixa, especifique o número de porta para o serviço de escuta do grupo de disponibilidade. 1433 é a predefinição, em seguida, clique em **OK**.

Agora tem um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure em execução no modo Resource Manager.

## <a name="test-connection-to-listener"></a>Testar a ligação ao serviço de escuta

Para testar a ligação:

1. RDP para um SQL Server que está na mesma rede virtual, mas não é proprietário a réplica. Pode utilizar outro servidor SQL no cluster.

1. Uso **sqlcmd** utilitário para testar a ligação. Por exemplo, o seguinte script estabelece uma **sqlcmd** ligação para a réplica primária por meio do serviço de escuta com a autenticação do Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Se o serviço de escuta é utilizar uma porta diferente da predefinição da porta (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd liga a um serviço de escuta na porta 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

A ligação de SQLCMD liga-se automaticamente para qualquer instância do SQL Server aloja a réplica primária.

> [!TIP]
> Certifique-se de que a porta especificada está aberta na firewall de ambos os servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou Editar regra de Firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Passos Seguintes

- [Adicionar um endereço IP a um balanceador de carga para um segundo grupo de disponibilidade](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
