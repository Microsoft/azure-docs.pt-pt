---
title: 'Tutorial: Configurar um servidor SQL sempre no grupo de disponibilidade'
description: Este tutorial mostra como criar um sql servidor sempre on availability group em Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 30c7d525f821b828dcc4c389c32a27123b79a56b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360927"
---
# <a name="tutorial-configure-a-sql-server-availability-group-on-azure-virtual-machines-manually"></a>Tutorial: Configurar um grupo de disponibilidade de servidor SQL em Azure Virtual Machines manualmente

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tutorial mostra como criar um sql servidor sempre on availability group em Azure Virtual Machines. O tutorial completo cria um Grupo de Disponibilidade com uma réplica de base de dados em dois Servidores SQL.

**Estimativa do tempo**: Demora cerca de 30 minutos a ser concluído uma vez que os pré-requisitos são cumpridos.

O diagrama ilustra o que se constrói no tutorial.

![Grupo de Disponibilidade](./media/availability-group-manually-configure-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Pré-requisitos

O tutorial pressupõe que você tem uma compreensão básica do SQL Server Always On Availability Groups. Se precisar de mais informações, consulte [a visão geral dos grupos de disponibilidade sempre (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

A tabela a seguir enumera os pré-requisitos necessários para completar antes de iniciar este tutorial:

| Requisito |Description |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Duas instâncias do SQL Server** | - Em um conjunto de disponibilidade azure <br/> - Num único domínio <br/> - Com funcionalidade de Clustering Failover instalada |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Partilha de ficheiros para testemunha de cluster |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Conta de serviço do SQL Server** | Conta do domínio |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Conta de serviço do Agente do Servidor SQL** | Conta do domínio |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Portas de firewall abertas** | - SQL Server: **1433** por exemplo padrão <br/> - Ponto final espelhante da base de dados: **5022** ou qualquer porta disponível <br/> - Disponibilidade de carregamento de grupo de carregamento IP sonda de saúde endereço: **59999** ou qualquer porta disponível <br/> - Cluster core load balancer IP address health sonda: **58888** ou qualquer porta disponível |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Adicionar funcionalidade de clustering failover** | Ambas as instâncias do SQL Server requerem esta funcionalidade |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Conta de domínio de instalação** | - Administrador local em cada Servidor SQL <br/> - Membro do sql server sysadmin papel de servidor fixo para cada instância do SQL Server  |


Antes de iniciar o tutorial, tem de [completar os pré-requisitos para criar grupos sempre em disponibilidade em máquinas virtuais Azure.](availability-group-manually-configure-prerequisites-tutorial.md) Se estes pré-requisitos já estiverem concluídos, pode saltar para [criar cluster.](#CreateCluster)

  >[!NOTE]
  > Muitos dos passos fornecidos neste tutorial podem agora ser automatizados com [modelos Azure SQL VM CLI](availability-group-az-cli-configure.md) e [Azure Quickstart.](availability-group-quickstart-template-configure.md)


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster

Após a conclusão dos pré-requisitos, o primeiro passo é criar um Cluster de Falha do Servidor do Windows que inclua dois SQL Severs e um servidor de testemunhas.

1. Utilize o Protocolo de Ambiente de Trabalho Remoto (RDP) para ligar ao primeiro Servidor SQL. Utilize uma conta de domínio que seja um administrador tanto nos Servidores SQL como no servidor de testemunhas.

   >[!TIP]
   >Se seguiu o [documento pré-requisitos,](availability-group-manually-configure-prerequisites-tutorial.md)criou uma conta chamada **CORP\Install**. Use esta conta.

2. No painel **'Gestor do Servidor',** selecione **Ferramentas**e, em seguida, selecione **O Gestor do Cluster Failover**.
3. No painel esquerdo, clique no Gestor de **Cluster Failover**e, em seguida, selecione **Criar um Cluster**.

   ![Criar Cluster](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. No Assistente de Agrupamento criar um conjunto de nós, pisando as páginas com as definições na tabela seguinte:

   | Página | Definições |
   | --- | --- |
   | Antes de Começar |Use predefinições |
   | Selecione Servidores |Digite o primeiro nome do servidor SQL no **nome do servidor Enter** e selecione **Add**. |
   | Aviso de validação |Selecione **Nº. Não preciso de apoio da Microsoft para este cluster e, portanto, não quero executar os testes de validação. Quando eu selecionar Next, continue a criar o cluster**. |
   | Ponto de acesso para administração do Cluster |Digite um nome de cluster, por exemplo **SQLAGCluster1** em **Nome do Cluster**.|
   | Confirmação |Utilize predefinições a menos que esteja a utilizar espaços de armazenamento. Consulte a nota que se segue a esta tabela. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Desabar o endereço IP do cluster de falha do servidor do Windows

  > [!NOTE]
  > No Windows Server 2019, o cluster cria um **Nome de Servidor Distribuído** em vez do Nome da Rede de **Cluster**. Se estiver a utilizar o Windows Server 2019, ignore quaisquer passos que se refiram ao nome principal do cluster neste tutorial. Pode criar um nome de rede de cluster utilizando [o PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Reveja o blog [Failover Cluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) para obter mais informações. 

1. Em **Failover Cluster Manager**, desça até cluster core **resources** e expanda os detalhes do cluster. Deve ver tanto o **Nome** como os recursos do **endereço IP** no estado **falhado.** O recurso de endereço IP não pode ser trazido on-line porque o cluster é atribuído o mesmo endereço IP que a própria máquina, portanto é um endereço duplicado.

2. Clique com o botão direito no recurso **ip address** falhado e, em seguida, selecione **Propriedades**.

   ![Propriedades de Cluster](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Selecione **O Endereço IP Estático** e especifique um endereço disponível a partir da mesma sub-rede que as suas máquinas virtuais.

4. Na secção **Cluster Core Resources,** clique com o nome do cluster com clique direito e selecione **Bring Online**. Aguarde até que ambos os recursos estejam online. Quando o recurso de nome cluster entra on-line, atualiza o servidor do controlador de domínio (DC) com uma nova conta de computador Ative Directory (AD). Utilize esta conta AD para executar o serviço agrupamento do Grupo Availability mais tarde.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Adicione o outro SqL Server ao cluster

Adicione o outro SQL Server ao cluster.

1. Na árvore do navegador, clique com o botão direito no cluster e **selecione Add Node**.

    ![Adicione nó ao cluster](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. No **"Add Node Wizard",** selecione **Seguinte**. Na página **'Selecionar servidores',** adicione o segundo SQL Server. Digite o nome do servidor no **nome do servidor Enter** e, em seguida, selecione **Adicionar**. Quando terminar, selecione **Seguinte**.

1. Na página **'Aviso de Validação',** selecione **Nº** (num cenário de produção deverá efetuar os testes de validação). Em seguida, selecione **Seguinte**.

8. Na página **Confirmação,** se estiver a utilizar espaços de armazenamento, limpe a caixa de verificação com a etiqueta **Adicionar todo o armazenamento elegível ao cluster.**

   ![Adicionar confirmação de nó](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Se estiver a utilizar espaços de armazenamento e não desmarcar **Adicione todo o armazenamento elegível ao cluster, o**Windows destaca os discos virtuais durante o processo de agrupamento. Como resultado, não aparecem no Disk Manager ou No Explorer até que os espaços de armazenamento sejam removidos do cluster e religados usando o PowerShell. Os Espaços de Armazenamento agrulem vários discos para piscinas de armazenamento. Para mais informações, consulte [os Espaços de Armazenamento.](https://technet.microsoft.com/library/hh831739)
   >

1. Selecione **Seguinte**.

1. Selecione **Concluir**.

   O Gestor de Cluster Failover mostra que o seu cluster tem um novo nó e lista-o no recipiente **Nó.**

10. Faça login na sessão de ambiente de trabalho remoto.

### <a name="add-a-cluster-quorum-file-share"></a>Adicione uma partilha de ficheiros de quórum de cluster

Neste exemplo, o cluster Windows utiliza uma partilha de ficheiros para criar um quórum de cluster. Este tutorial utiliza um quórum de maioria de nó e partilha de ficheiros. Para obter mais informações, veja [Compreender as Configurações do Quórum num Cluster de Ativação Pós-falha](https://technet.microsoft.com/library/cc731739.aspx).

1. Conecte-se ao servidor de membros da testemunha de partilha de ficheiros com uma sessão de ambiente de trabalho remoto.

1. No **Gestor do Servidor**, selecione **Ferramentas**. **Gestão de Computadores Abertos**.

1. Selecione **pastas partilhadas**.

1. Clique com direito **Em Ações**e selecione **New Share...**.

   ![Nova Partilha](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Utilize **criar um assistente de pasta partilhada** para criar uma partilha.

1. No **Caminho da Pasta**, selecione **Procurar** e localizar ou criar um caminho para a pasta partilhada. Selecione **Seguinte**.

1. Em **Nome, Descrição e Definições verifique** o nome e o caminho da partilha. Selecione **Seguinte**.

1. Em **Permissões de Pasta Partilhadas** definir **Personalizar permissões**. Selecione **Personalizado...**. .

1. Em **Personalizar Permissões**, selecione **Adicionar...**.

1. Certifique-se de que a conta utilizada para criar o cluster tem controlo total.

   ![Nova Partilha](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Selecione **OK**.

1. Em **Permissões de Pasta Partilhadas**, selecione **Acabamento**. **Selecione Terminar** novamente.  

1. Faça login no servidor

### <a name="configure-the-cluster-quorum"></a>Configurar o quórum do cluster

Em seguida, coloque o quórum do cluster.

1. Ligue-se ao primeiro nó de cluster com um ambiente de trabalho remoto.

1. In **Failover Cluster Manager**, clique com o botão direito no cluster, aponte para Mais **Ações**, e selecione **Configurações de Quorum de Cluster Configurações de Configuração do Cluster Configure...**.

   ![Nova Partilha](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. In **Configure Cluster Quorum Wizard**, selecione **Next**.

1. Na **Opção de Configuração do Conselho Select,** escolha **Selecione o testemunho de quórum**e selecione **Seguinte**.

1. Em **Select Quorum Witness**, selecione **Configure uma testemunha de partilha de ficheiros**.

   >[!TIP]
   >O Windows Server 2016 suporta uma testemunha na nuvem. Se escolher este tipo de testemunha, não precisa de uma testemunha de partilha de ficheiros. Para obter mais informações, consulte [Implementar uma testemunha em nuvem para um Cluster failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial utiliza uma testemunha de partilha de ficheiros, que é suportada por sistemas operativos anteriores.
   >

1. Em **Configurar File Share Witness,** digite o caminho para a partilha que criou. Selecione **Seguinte**.

1. Verifique as definições da **Confirmação**. Selecione **Seguinte**.

1. Selecione **Concluir**.

Os recursos do núcleo do cluster estão configurados com uma testemunha de partilha de ficheiros.

## <a name="enable-availability-groups"></a>Ativar grupos de disponibilidade

Em seguida, ative a função **Grupos de Disponibilidade AlwaysOn.** Faça estes passos em ambos os Servidores SQL.

1. A partir do ecrã **Iniciar,** lance **o Gestor de Configuração do Servidor SQL**.
2. Na árvore do navegador, selecione **os Serviços de Servidor SQL,** clique com o botão direito no serviço **SQL Server (MSSQLSERVER)** e selecione **Propriedades**.
3. Selecione o separador **AlwaysOn High Availability** e, em seguida, selecione **Ative AlwaysOn Availability Groups**, da seguinte forma:

    ![Ativar grupos de disponibilidade alwaysOn](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Selecione **Aplicar**. Selecione **OK** no diálogo pop-up.

5. Reinicie o serviço do SQL Server.

Repita estes passos no outro SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Criar uma base de dados no primeiro SQL Server

1. Lance o ficheiro RDP para o primeiro SQL Server com uma conta de domínio que é um membro da função de servidor fixo sysadmin.
1. Abra o SQL Server Management Studio e ligue-se ao primeiro SQL Server.
7. No **Object Explorer,** clique à direita bases de **dados** e selecione **New Database**.
8. No **nome da base de dados,** digite **MyDB1,** em seguida, selecione **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Criar uma partilha de backup

1. No primeiro SqL Server in **Server Manager**, selecione **Tools**. **Gestão de Computadores Abertos**.

1. Selecione **pastas partilhadas**.

1. Clique com direito **Em Ações**e selecione **New Share...**.

   ![Nova Partilha](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Utilize **criar um assistente de pasta partilhada** para criar uma partilha.

1. No **Caminho da Pasta**, selecione **Procurar** e localizar ou criar um caminho para a pasta partilhada por backup da base de dados. Selecione **Seguinte**.

1. Em **Nome, Descrição e Definições verifique** o nome e o caminho da partilha. Selecione **Seguinte**.

1. Em **Permissões de Pasta Partilhadas** definir **Personalizar permissões**. Selecione **Personalizado...**. .

1. Em **Personalizar Permissões**, selecione **Adicionar...**.

1. Certifique-se de que o serviço SQL Server e SQL Server Agent têm controlo total.

   ![Nova Partilha](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Selecione **OK**.

1. Em **Permissões de Pasta Partilhadas**, selecione **Acabamento**. **Selecione Terminar** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Faça uma cópia de segurança completa da base de dados

É necessário fazer o reforço da nova base de dados para rubricar a cadeia de registos. Se não fizer uma cópia de segurança da nova base de dados, esta não poderá ser incluída num Grupo de Disponibilidade.

1. No **Object Explorer,** clique à direita na base de dados, aponte para **As Tarefas...**, selecione **Back Up**.

1. Selecione **OK** para levar uma cópia de segurança completa para a localização de backup predefinido.

## <a name="create-the-availability-group"></a>Criar o Grupo de Disponibilidade

Está agora pronto para configurar um Grupo de Disponibilidade utilizando os seguintes passos:

* Crie uma base de dados no primeiro SQL Server.
* Pegue uma cópia de segurança completa e uma cópia de segurança da base de dados.
* Restaurar as cópias de segurança completas e registar o segundo SQL Server com a opção **NORECOVERY.**
* Crie o Grupo disponibilidade **(AG1)** com compromisso sincronizado, falha automática e réplicas secundárias legíveis.

### <a name="create-the-availability-group"></a>Criar o Grupo disponibilidade:

1. Em sessão de ambiente de trabalho remoto para o primeiro SQL Server. No **Object Explorer** em SSMS, clique à direita Na Disponibilidade **Máxima** e selecione o Novo Assistente do Grupo **de Disponibilidade**.

    ![Lançar novo assistente do grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Na página **Introdução,** selecione **Seguinte**. Na página **'Especificar o Nome do Grupo disponibilidade',** digite um nome para o grupo Availability em **availability group name**. Por **exemplo, AG1**. Selecione **Seguinte**.

    ![Novo assistente do grupo de disponibilidade, especificar o nome do grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Na página **'Selecionar bases de dados',** selecione a base de dados e, em seguida, selecione **Seguinte**.

   >[!NOTE]
   >A base de dados satisfaz os pré-requisitos para um Grupo de Disponibilidade porque você tomou pelo menos uma cópia de segurança completa na réplica primária pretendida.
   >

   ![Novo assistente de grupo de disponibilidade, selecione bases de dados](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. Na página **'Especificar réplicas',** selecione **Adicionar Réplica.**

   ![Novo assistente do grupo de disponibilidade, especificar réplicas](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. O diálogo **"Connect to Server"** aparece. Digite o nome do segundo servidor no **nome do Servidor**. Selecione **Ligar**.

   De volta à página **'Especificar réplicas',** deverá agora ver o segundo servidor listado nas **Réplicas de Disponibilidade**. Configure as réplicas da seguinte forma.

   ![Novo assistente do grupo de disponibilidade, especificar réplicas (completa)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Selecione **Pontos de** final para ver o ponto final espelhante da base de dados para este Grupo de Disponibilidade. Utilize a mesma porta que utilizou quando definiu a regra de [firewall para pontos finais espelhadores de base de dados](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Novo assistente do grupo de disponibilidade, selecione a sincronização inicial de dados](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Na página **de sincronização de dados iniciais,** selecione **Full** e especifique uma localização de rede partilhada. Para a localização, utilize a [parte de reserva que criou.](#backupshare) No exemplo foi, ** \\ \\<Primeiro Servidor SQL \> \Backup \\ **. Selecione **Seguinte**.

   >[!NOTE]
   >A sincronização total requer uma cópia de segurança completa da base de dados na primeira instância do SQL Server e restaura-a para a segunda instância. Para grandes bases de dados, a sincronização total não é recomendada porque pode demorar muito tempo. Pode reduzir este tempo, tomando manualmente uma cópia de segurança da base de dados e restaurando-a com `NO RECOVERY` . Se a base de dados já estiver restaurada `NO RECOVERY` no segundo SQL Server antes de configurar o Grupo de Disponibilidade, escolha **apenas 'Juntar'.** Se pretender fazer a cópia de segurança depois de configurar o Grupo Disponibilidade, escolha **a sincronização inicial de dados**.
   >

   ![Novo assistente do grupo de disponibilidade, selecione a sincronização inicial de dados](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Na página **de Validação,** selecione **Seguinte**. Esta página deve ser semelhante à seguinte imagem:

    ![Novo Assistente do Grupo de Disponibilidade, Validação](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Existe um aviso para a configuração do ouvinte porque não configura um ouvinte do Grupo disponibilidade. Pode ignorar este aviso porque nas máquinas virtuais Azure cria-se o ouvinte depois de criar o equilibrador de carga Azure.

10. Na página **Resumo,** selecione **Terminar,** em seguida, aguarde enquanto o assistente configura o novo Grupo de Disponibilidade. Na página **'Progresso',** pode selecionar **Mais detalhes** para ver o progresso detalhado. Uma vez terminado o assistente, inspecione a página **Resultados** para verificar se o Grupo de Disponibilidade foi criado com sucesso.

     ![Novo assistente do grupo de disponibilidade, resultados](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Selecione **Perto** da saída do assistente.

### <a name="check-the-availability-group"></a>Consulte o Grupo disponibilidade

1. No **Object Explorer,** expanda **a disponibilidade de sempre,** e depois expanda **os Grupos de Disponibilidade**. Deverá agora consultar o novo Grupo Availability neste recipiente. Clique com o botão direito no Grupo de Disponibilidade e selecione **Mostrar Painel**.

   ![Mostrar painel de grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   O seu **Painel AlwaysOn** deve ser semelhante à seguinte imagem:

   ![Painel de instrumentos do Grupo disponibilidade](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Pode ver as réplicas, o modo de falha de cada réplica e o estado de sincronização.

2. No **Failover Cluster Manager,** selecione o seu cluster. Selecione **Funções**. O nome do Grupo Disponibilidade que usou é um papel no cluster. Este Grupo disponibilidade não tem um endereço IP para ligações ao cliente porque não configura um ouvinte. Configurará o ouvinte depois de criar um equilibrador de carga Azure.

   ![Grupo de Disponibilidade no Gestor de Cluster Failover](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente falhar sobre o Grupo Availability do Gestor de Cluster Failover. Todas as operações de failover devem ser realizadas a partir do **Painel AlwaysOn** em SSMS. Para obter mais informações, consulte [as restrições de utilização do gestor de cluster de failover com grupos de disponibilidade.](https://msdn.microsoft.com/library/ff929171.aspx)
    >

Neste momento, tem um Grupo de Disponibilidade com réplicas em duas instâncias do SQL Server. Pode mover o Grupo disponibilidade entre instâncias. Ainda não é possível ligar-se ao Grupo disponibilidade porque ainda não tem um ouvinte. Nas máquinas virtuais Azure, o ouvinte necessita de um equilibrador de carga. O próximo passo é criar o equilibrador de carga em Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Nas máquinas virtuais Azure, um Grupo de Disponibilidade de Servidor SQL requer um equilibrador de carga. O equilibrador de carga contém os endereços IP para os ouvintes do Grupo Availability e o Cluster de Falha do Servidor do Windows. Esta secção resume como criar o equilibrador de carga no portal Azure.

Um equilibrador de carga em Azure pode ser um Balanceador de Carga Padrão ou um Balanceador de Carga Básico. O Balanceador de Carga Padrão tem mais funcionalidades do que o Balanceador de Carga Básica. Para um grupo de disponibilidade, o Balanceador de Carga Padrão é necessário se utilizar uma Zona de Disponibilidade (em vez de um Conjunto de Disponibilidade). Para obter mais informações sobre a diferença entre o balançador de carga SKUs, consulte [a comparação SKU do Balanceador de Carga](../../../load-balancer/skus.md).

1. No portal Azure, vá ao grupo de recursos onde estão os seus SqL Servers e selecione **+ Adicionar**.
1. Procurar **balanceador de carga**. Escolha o equilibrador de carga publicado pela Microsoft.

   ![Grupo de Disponibilidade no Gestor de Cluster Failover](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Selecione **Criar**.
1. Configure os seguintes parâmetros para o equilibrador de carga.

   | Definição | Campo |
   | --- | --- |
   | **Nome** |Utilize um nome de texto para o equilibrador de carga, por **exemplo, sqlLB**. |
   | **Tipo** |Interno |
   | **Rede virtual** |Utilize o nome da rede virtual Azure. |
   | **Sub-rede** |Use o nome da sub-rede em que a máquina virtual está.  |
   | **Atribuição de endereços IP** |Estático |
   | **Endereço IP** |Utilize um endereço disponível a partir da sub-rede. Utilize este endereço para o seu ouvinte de grupo de disponibilidade. Note que isto é diferente do seu endereço IP cluster.  |
   | **Subscrição** |Utilize a mesma subscrição que a máquina virtual. |
   | **Localização** |Use o mesmo local que a máquina virtual. |

   A lâmina do portal Azure deve ser assim:

   ![Criar balanceador de carga](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. **Selecione Criar,** para criar o equilibrador de carga.

Para configurar o equilibrador de carga, é necessário criar uma piscina de backend, uma sonda e definir as regras de equilíbrio de carga. Faça isto no portal Azure.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Adicione uma piscina de backend para o ouvinte do grupo de disponibilidade

1. No portal Azure, vá ao seu grupo de disponibilidade. Você pode precisar de refrescar a vista para ver o equilibrador de carga recém-criado.

   ![Encontrar balanceador de carga no Grupo de Recursos](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Selecione o equilibrador de carga, selecione **pools backend**e selecione **+Add**.

1. Digite um nome para a piscina de backend.

1. Associe o pool de backend com o conjunto de disponibilidade que contém os VMs.

1. Nas **configurações IP da rede Target**, verifique a MÁQUINA **VIRTUAL** e escolha ambas as máquinas virtuais que irão acolher réplicas de grupo de disponibilidade. Não inclua o servidor de testemunhas de partilha de ficheiros.

   >[!NOTE]
   >Se ambas as máquinas virtuais não forem especificadas, as ligações só terão sucesso na réplica primária.

1. Selecione **OK** para criar a piscina de backend.

### <a name="set-the-probe"></a>Definir a sonda

1. Selecione o equilibrador de carga, escolha **sondas health**e, em seguida, selecione **+Adicionar**.

1. Desa esta medida, a sonda de saúde do ouvinte é a seguinte:

   | Definições | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Selecione TCP | TCP |
   | **Porta** | Qualquer porto não-desuso | 59999 |
   | **Intervalo**  | A quantidade de tempo entre tentativas de sonda em segundos |5 |
   | **Limiar com funcionamento incorreto** | O número de falhas consecutivas da sonda que devem ocorrer para que uma máquina virtual seja considerada insalubre  | 2 |

1. Selecione **OK** para definir a sonda de saúde.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de equilíbrio de carga

1. Selecione o equilibrador de carga, escolha **regras de equilíbrio de carga**e selecione **+Adicionar**.

1. Desabrague as regras de equilíbrio da carga do ouvinte da seguinte forma.

   | Definições | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP de front-end** | Escolha um endereço |Utilize o endereço que criou quando criou o equilibrador de carga. |
   | **Protocolo** | Selecione TCP |TCP |
   | **Porta** | Utilize a porta para o ouvinte do grupo de disponibilidade | 1433 |
   | **Porto Backend** | Este campo não é utilizado quando o IP flutuante está definido para a devolução direta do servidor | 1433 |
   | **Sonda** |O nome especificado para a sonda | SQLAlwaysOnEndPointProbe |
   | **Persistência da Sessão** | Lista de down down | **Nenhuma** |
   | **Tempo de saída ocioso** | Minutos para manter aberta uma ligação TCP | 4 |
   | **IP flutuante (retorno direto do servidor)** | |Ativado |

   > [!WARNING]
   > A devolução direta do servidor é definida durante a criação. Não pode ser alterado.
   >

1. Selecione **OK** para definir as regras de equilíbrio da carga do ouvinte.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Adicione o endereço IP do núcleo do cluster para o Cluster de Falha do Servidor do Windows (WSFC)

O endereço IP WSFC também precisa estar no equilibrador de carga.

1. No portal Azure, vá ao mesmo equilibrador de carga Azure. Selecione **a configuração IP frontend** e selecione **+Adicionar**. Utilize o endereço IP configurado para o WSFC nos recursos fundamentais do cluster. Desaponuse o endereço IP como estático.

1. No equilibrador de carga, selecione **sondas de saúde**e, em seguida, selecione **+Add**.

1. Desaponhe a sonda de saúde do endereço IP do núcleo do WSFC da seguinte forma:

   | Definições | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPointProbe |
   | **Protocolo** | Selecione TCP | TCP |
   | **Porta** | Qualquer porto não-desuso | 58888 |
   | **Intervalo**  | A quantidade de tempo entre tentativas de sonda em segundos |5 |
   | **Limiar com funcionamento incorreto** | O número de falhas consecutivas da sonda que devem ocorrer para que uma máquina virtual seja considerada insalubre  | 2 |

1. Selecione **OK** para definir a sonda de saúde.

1. Desabrague as regras de equilíbrio de carga. Selecione **regras de equilíbrio de carga**e selecione **+Adicionar**.

1. Desabrague as regras de equilíbrio da carga do endereço IP do núcleo do cluster da seguinte forma.

   | Definições | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPoint |
   | **Endereço IP de front-end** | Escolha um endereço |Utilize o endereço que criou quando configura o endereço IP do WSFC. Isto é diferente do endereço IP do ouvinte |
   | **Protocolo** | Selecione TCP |TCP |
   | **Porta** | Utilize a porta para o endereço IP do cluster. Esta é uma porta disponível que não é utilizada para a porta da sonda ouvinte. | 58888 |
   | **Porto Backend** | Este campo não é utilizado quando o IP flutuante está definido para a devolução direta do servidor | 58888 |
   | **Sonda** |O nome especificado para a sonda | WSFCEndPointProbe |
   | **Persistência da Sessão** | Lista de down down | **Nenhuma** |
   | **Tempo de saída ocioso** | Minutos para manter aberta uma ligação TCP | 4 |
   | **IP flutuante (retorno direto do servidor)** | |Ativado |

   > [!WARNING]
   > A devolução direta do servidor é definida durante a criação. Não pode ser alterado.
   >

1. Selecione **OK** para definir as regras de equilíbrio de carga.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Configure o ouvinte

A próxima coisa a fazer é configurar um ouvinte do Grupo Availability no cluster de failover.

> [!NOTE]
> Este tutorial mostra como criar um único ouvinte, com um endereço IP ILB. Para criar um ou mais ouvintes utilizando um ou mais endereços IP, consulte [Criar O Grupo de Disponibilidade e equilibrador de carga ! Azure.](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Definir porta de ouvinte

No SQL Server Management Studio, desemote a porta do ouvinte.

1. Lance o SQL Server Management Studio e ligue-se à réplica primária.

1. Navigate to **AlwaysOn High Availability**  >  **Availability Groups**  >  **Availability Group Listeners**.

1. Deve agora ver o nome do ouvinte que criou no Failover Cluster Manager. Clique com o botão direito no nome do ouvinte e selecione **Propriedades**.

1. Na caixa **de Porta,** especifique o número da porta para o ouvinte do Grupo disponibilidade. 1433 é o padrão. Selecione **OK**.

Tem agora um Grupo de Disponibilidade de Servidor SQL em máquinas virtuais Azure em funcionamento no modo Gestor de Recursos.

## <a name="test-connection-to-listener"></a>Ligação de teste ao ouvinte

Para testar a ligação:

1. Utilize RDP para ligar a um SQL Server que esteja na mesma rede virtual, mas que não possui a réplica. Pode utilizar o outro SQL Server no cluster.

1. Utilize o utilitário **sqlcmd** para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação **sqlcmd** à réplica primária através do ouvinte com autenticação do Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Se o ouvinte estiver a utilizar uma porta diferente da porta padrão (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte `sqlcmd` comando liga-se a um ouvinte na porta 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

A ligação SQLCMD liga-se automaticamente a qualquer instância do SQL Server que acolhe a réplica primária.

> [!TIP]
> Certifique-se de que a porta especificada está aberta na firewall de ambos os Servidores SQL. Ambos os servidores requerem uma regra de entrada para a porta TCP que utiliza. Para obter mais informações, consulte [adicionar ou editar a regra de Firewall](https://technet.microsoft.com/library/cc753558.aspx).
>

## <a name="next-steps"></a>Passos seguintes

- [Adicione um endereço IP a um equilibrador de carga para um segundo grupo de disponibilidade](availability-group-listener-powershell-configure.md#Add-IP).
