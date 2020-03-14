---
title: 'Tutorial: Configure grupo de disponibilidade'
description: Este tutorial mostra como criar um Grupo De Disponibilidade De Servidor SQL Sempre Em Disponibilidade em Máquinas Virtuais Azure.
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
ms.openlocfilehash: ed5fc923c82fb0d0e4004e18159d943564c6f55e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249857"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Tutorial: Configure grupo de disponibilidade no Azure SQL Server VM manualmente

Este tutorial mostra como criar um Grupo De Disponibilidade De Servidor SQL Sempre Em Disponibilidade em Máquinas Virtuais Azure. O tutorial completo cria um Grupo de Disponibilidade com uma réplica de base de dados em dois Servidores SQL.

**Estimativa de tempo**: Leva cerca de 30 minutos para ser concluído uma vez que os pré-requisitos são cumpridos.

O diagrama ilustra o que se constrói no tutorial.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Pré-requisitos

O tutorial assume que tem uma compreensão básica do SQL Server Always On Availability Groups. Se precisar de mais informações, consulte a [visão geral dos grupos sempre em disponibilidade (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

A tabela que se segue lista os pré-requisitos que precisa de completar antes de iniciar este tutorial:

|  |Requisito |Descrição |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dois servidores SQL | - Num conjunto de disponibilidade azure <br/> - Num único domínio <br/> - Com a funcionalidade de clustering Failover instalada |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Partilha de ficheiros para testemunha de cluster |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço SQL Server | Conta de domínio |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço do Agente de Servidor SQL | Conta de domínio |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Portas de firewall abertas | - Servidor SQL: **1433** por exemplo <br/> - Ponto final espelhado na base de dados: **5022** ou qualquer porta disponível <br/> - Disponibilidade de balanceador de carga IP address sonda de saúde: **59999** ou qualquer porta disponível <br/> - Sonda de saúde ip de endereço de base do cluster: **58888** ou qualquer porta disponível |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adicionar funcionalidade de clustering failover | Ambos os Servidores SQL requerem esta funcionalidade |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de domínio de instalação | - Administrador local em cada Servidor SQL <br/> - Membro da função de servidor fixo sysadmin a sysadmin a sysadmin a cada instância do Servidor SQL  |


Antes de iniciar o tutorial, precisa [de completar pré-requisitos para criar grupos sempre em disponibilidade em máquinas virtuais Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Se estes pré-requisitos já estiverem concluídos, pode saltar para [criar cluster](#CreateCluster).

  >[!NOTE]
  > Muitas das etapas fornecidas neste tutorial podem agora ser automatizadas com modelos [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) e [Azure Quickstart.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Criar o cluster

Após a conclusão dos pré-requisitos, o primeiro passo é criar um Cluster de Failover do Windows Server que inclua dois SQL Severs e um servidor de testemunhas.

1. RDP para o primeiro Servidor SQL usando uma conta de domínio que é um administrador tanto em Servidores SQL como no servidor de testemunhas.

   >[!TIP]
   >Se seguiu o documento de [pré-requisitos,](virtual-machines-windows-portal-sql-availability-group-prereq.md)criou uma conta chamada **CORP\Install**. Use esta conta.

2. No painel do Gestor do **Servidor,** selecione **Ferramentas**, e, em seguida, clique no **Failover Cluster Manager**.
3. No painel esquerdo, clique no **Failover Cluster Manager,** clique em Criar **um Cluster**.
   ![criar](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png) de cluster
4. No Create Cluster Wizard, crie um cluster de um nó, pisando as páginas com as definições na tabela seguinte:

   | Página | Definições |
   | --- | --- |
   | Antes de começar |Utilizar predefinições |
   | Selecione Servidores |Digite o primeiro nome do Servidor SQL no nome do **servidor Enter** e clique em **Adicionar**. |
   | Aviso de Validação |Selecione **No. Não preciso de suporte da Microsoft para este cluster e, portanto, não quero executar os testes de validação. Quando clicar em Seguinte, continue a criar o cluster**. |
   | Ponto de acesso para administração do cluster |Digite um nome de cluster, por exemplo **SQLAGCluster1** em **Nome de Cluster**.|
   | Confirmação |Utilize os predefinições a menos que esteja a utilizar espaços de armazenamento. Consulte a nota que segue esta mesa. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Detete o endereço IP do cluster do servidor windows

  > [!NOTE]
  > No Windows Server 2019, o cluster cria um Nome de **Servidor Distribuído** em vez do Nome da **Rede cluster**. Se estiver a utilizar o Windows Server 2019, ignore quaisquer passos que se refiram ao nome principal do cluster neste tutorial. Pode criar um nome de rede de cluster utilizando [powerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Reveja o blog [Failover Cluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) para obter mais informações. 

1. No **Failover Cluster Manager,** desça até cluster **Core Resources** e expanda os detalhes do cluster. Deve ver tanto os recursos de **Nome** como os recursos **ip** address no estado **falhado.** O recurso ip endereço não pode ser colocado on-line porque o cluster é atribuído o mesmo endereço IP que a própria máquina, portanto é um endereço duplicado.

2. Clique à direita no recurso **ip address** falhado e, em seguida, clique em **Propriedades**.

   ![Propriedades cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecione **Endereço IP estático** e especifique um endereço disponível a partir da mesma sub-rede que as suas máquinas virtuais.

4. Na secção **Recursos Core cluster,** clique no nome do cluster clique direito e clique em **Bring Online**. Então, espere até que ambos os recursos estejam online. Quando o recurso de nome do cluster fica on-line, atualiza o servidor DC com uma nova conta de computador AD. Utilize esta conta AD para executar o serviço clusterd Grupo de Disponibilidade mais tarde.

### <a name="addNode"></a>Adicione o outro Servidor SQL ao cluster

Adicione o outro Servidor SQL ao cluster.

1. Na árvore do navegador, clique no cluster e clique em **Adicionar Nó**.

    ![Adicione nó ao Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. No **Assistente do Nó adicionar,** clique em **Seguinte**. Na página **Select Servers,** adicione o segundo Servidor SQL. Digite o nome do servidor no nome do **servidor Enter** e, em seguida, clique em **Adicionar**. Quando terminar, clique em **Seguinte**.

1. Na página de Aviso de **Validação,** clique **em Não** (num cenário de produção deve efetuare os testes de validação). Clique depois em **Seguinte**.

8. Na página **de Confirmação** se estiver a utilizar espaços de armazenamento, limpe a caixa de verificação rotulada **Adicione todo o armazenamento elegível ao cluster.**

   ![Adicionar confirmação do nó](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Se estiver a utilizar espaços de armazenamento e não desverificar **Adicione todo o armazenamento elegível ao cluster,** o Windows desprende os discos virtuais durante o processo de agrupamento. Como resultado, não aparecem no Disk Manager ou explorer até que os espaços de armazenamento sejam removidos do cluster e recolocados usando o PowerShell. Espaços de Armazenamento agrupa vários discos em piscinas de armazenamento. Para mais informações, consulte [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739).

1. Clique em **Seguinte**.

1. Clique em **Concluir**.

   O Failover Cluster Manager mostra que o seu cluster tem um novo nó e lista-o no recipiente de **nós.**

10. Saia da sessão remota de ambiente de trabalho.

### <a name="add-a-cluster-quorum-file-share"></a>Adicione uma partilha de ficheiro quórum cluster

Neste exemplo, o cluster Windows utiliza uma partilha de ficheiros para criar um quórum de cluster. Este tutorial usa um quórum de node e partilha de ficheiros. Para obter mais informações, veja [Compreender as Configurações do Quórum num Cluster de Ativação Pós-falha](https://technet.microsoft.com/library/cc731739.aspx).

1. Ligue-se ao servidor de membro sonante de partilha de ficheiros com uma sessão remota de ambiente de trabalho.

1. No **Gestor do Servidor,** clique em **Ferramentas**. Open **Computer Management**.

1. Clique em **Pastas Partilhadas**.

1. Clique à direita **Em Ações,** clique em **New Share...** .

   ![Nova Partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Utilize **criar um Assistente** de Pasta Partilhada para criar uma parte.

1. No **Caminho das Pastas,** clique em **Navegar** e localizar ou criar um caminho para a pasta partilhada. Clique em **Seguinte**.

1. Em **Nome, Descrição e Definições** verifique o nome e o caminho da partilha. Clique em **Seguinte**.

1. Em **permissões** de pasta partilhada, as **permissões de personalizar permissões**. Clique em **Costume...** .

1. Em **Personalizar permissões,** clique em **Adicionar...** .

1. Certifique-se de que a conta utilizada para criar o cluster tem controlo total.

   ![Nova Partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Clique em **OK**.

1. Nas **permissões de pasta partilhada,** clique em **Terminar**. Clique em **Terminar** novamente.  

1. Saque do servidor

### <a name="configure-cluster-quorum"></a>Configure quórum de cluster

Em seguida, definir o quórum do cluster.

1. Ligue-se ao primeiro nó de cluster com ambiente de trabalho remoto.

1. No **Failover Cluster Manager,** clique no cluster, clique para **Mais Ações**e clique em Configurar Definições de **Quórum**de Cluster... .

   ![Nova Partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. No **Configure Cluster Quorum Wizard,** clique em **Seguinte**.

1. No **Selecionador Opção de Configuração do Quórum,** escolha **Selecione a testemunha quórum**, e clique **em Seguinte**.

1. Em **Select Quorum Witness,** clique **em Configurar uma testemunha de partilha de ficheiros**.

   >[!TIP]
   >O Windows Server 2016 suporta uma testemunha na nuvem. Se escolher este tipo de testemunha, não precisa de uma testemunha de partilha de ficheiros. Para mais informações, consulte [A implantação de uma testemunha em nuvem para um Cluster Failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial utiliza uma testemunha de partilha de ficheiros, que é suportada por sistemas operativos anteriores.

1. Em **Configure File Share Witness,** escreva o caminho para a partilha que criou. Clique em **Seguinte**.

1. Verifique as definições na **Confirmação**. Clique em **Seguinte**.

1. Clique em **Concluir**.

Os recursos do núcleo do cluster são configurados com uma testemunha de partilha de ficheiros.

## <a name="enable-availability-groups"></a>Ativar grupos de disponibilidade

Em seguida, ative a funcionalidade **SempreOn Availability Groups.** Faça estes passos em ambos os servidores SQL.

1. A partir do ecrã **Iniciar,** lance o Gestor de Configuração do **Servidor SQL**.
2. Na árvore do navegador, clique em **Serviços de Servidor SQL,** em seguida, clique à direita no serviço **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.
3. Clique no separador **AlwaysOn High Availability** e, em seguida, selecione **Ativar sempre grupos de disponibilidade,** da seguinte forma:

    ![Ativar grupos de disponibilidade alwayson](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Clique em **Aplicar**. Clique **OK** no diálogo pop-up.

5. Reinicie o serviço do SQL Server.

Repita estes passos no outro Servidor SQL.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Criar uma base de dados no primeiro Servidor SQL

1. Lance o ficheiro RDP para o primeiro Servidor SQL com uma conta de domínio que seja um membro da função de servidor fixo sysadmin.
1. Abra o Estúdio de Gestão de Servidores SQL e ligue-se ao primeiro Servidor SQL.
7. No **Object Explorer,** clique nas bases de **dados** do clique à direita e clique em Nova Base **de Dados**.
8. No **nome base**de dados , escreva **MyDB1,** em seguida, clique **OK**.

### <a name="backupshare"></a>Criar uma parte de backup

1. No primeiro Servidor SQL no **Gestor do Servidor,** clique em **Ferramentas**. Open **Computer Management**.

1. Clique em **Pastas Partilhadas**.

1. Clique à direita **Em Ações,** clique em **New Share...** .

   ![Nova Partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Utilize **criar um Assistente** de Pasta Partilhada para criar uma parte.

1. No **Caminho das Pastas,** clique em **Navegar** e localizar ou criar um caminho para a pasta partilhada de backup da base de dados. Clique em **Seguinte**.

1. Em **Nome, Descrição e Definições** verifique o nome e o caminho da partilha. Clique em **Seguinte**.

1. Em **permissões** de pasta partilhada, as **permissões de personalizar permissões**. Clique em **Costume...** .

1. Em **Personalizar permissões,** clique em **Adicionar...** .

1. Certifique-se de que as contas de serviço do Servidor SQL e do Agente do Servidor SQL para ambos os servidores têm controlo total.

   ![Nova Partilha](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Clique em **OK**.

1. Nas **permissões de pasta partilhada,** clique em **Terminar**. Clique em **Terminar** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Pegue uma cópia de segurança completa da base de dados

Precisa de fazer o reforço da nova base de dados para inicializar a cadeia de registos. Se não receber uma cópia de segurança da nova base de dados, não pode ser incluída num Grupo de Disponibilidade.

1. No **Object Explorer,** clique na base de dados, clique para **tarefas...** , clique em **Back Up**.

1. Clique em **OK** para levar uma cópia de segurança completa para a localização de reserva predefinida.

## <a name="create-the-availability-group"></a>Criar o Grupo de Disponibilidade
Está agora pronto para configurar um Grupo de Disponibilidade utilizando os seguintes passos:

* Crie uma base de dados no primeiro Servidor SQL.
* Pegue uma cópia de segurança completa e uma cópia de segurança de registo de transações da base de dados
* Restaurar as cópias de segurança completas e de registo no segundo Servidor SQL com a opção **NORECOVERY**
* Criar o Grupo de Disponibilidade **(AG1)** com compromisso sincronizado, falha automática e réplicas secundárias legíveis

### <a name="create-the-availability-group"></a>Criar o Grupo de Disponibilidade:

1. Em sessão remota de ambiente de trabalho para o primeiro Servidor SQL. No **Object Explorer** em SSMS, clique no **alwayson high availability** e clique em Novo Assistente de **Grupo de Disponibilidade**.

    ![Lançar novo assistente de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na página **introdução,** clique em **Seguinte**. Na página **'Especificar Nome do Grupo Disponibilidade',** digite um nome para o Grupo de Disponibilidade, por exemplo **AG1,** em **nome de grupo de disponibilidade**. Clique em **Seguinte**.

    ![Novo Assistente AG, especificar nome AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na página **'Selecionar Bases de Dados',** selecione a sua base de dados e clique em **Next**.

   >[!NOTE]
   >A base de dados satisfaz os pré-requisitos para um Grupo de Disponibilidade porque tomou pelo menos uma cópia de segurança completa da réplica primária pretendida.

   ![Novo Assistente AG, Selecionar Bases de Dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na página **'Especificar Réplicas',** clique em **Adicionar Réplica**.

   ![Novo Assistente AG, Especificar Réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. O diálogo **Connect to Server** aparece. Digite o nome do segundo servidor no **nome do Servidor**. Clique em **Ligar**.

   De volta à página **'Reparações'** e deverá agora ver o segundo servidor listado em **Réplicas de Disponibilidade**. Configure as réplicas da seguinte forma.

   ![Novo Assistente AG, Especificar Réplicas (Completa)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Clique em **Pontos Finais** para ver o ponto final espelhado da base de dados para este Grupo de Disponibilidade. Utilize a mesma porta que utilizou quando definiu a regra da [firewall para pontos finais espelhados](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)de base de dados .

    ![Novo Assistente AG, Selecione sincronização inicial de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na página **De Sincronização de Dados Inicial, selecione** **Full** e especifique uma localização de rede partilhada. Para a localização, utilize a [parte de reserva que criou.](#backupshare) No exemplo, **\\\\\<Primeiro Servidor SQL\>\Backup\\** . Clique em **Seguinte**.

   >[!NOTE]
   >A sincronização completa tem uma cópia de segurança completa da base de dados na primeira instância do SQL Server e restaura-a para a segunda instância. Para grandes bases de dados, a sincronização completa não é recomendada porque pode demorar muito tempo. Pode reduzir este tempo, pegando manualmente numa cópia de segurança da base de dados e restaurando-a com `NO RECOVERY`. Se a base de dados já estiver restaurada com `NO RECOVERY` no segundo Servidor SQL antes de configurar o Grupo de Disponibilidade, escolha **apenas juntar**- Se pretender obter a cópia de segurança depois de configurar o Grupo de Disponibilidade, escolha **a sincronização inicial de dados skip**.

    ![Novo Assistente AG, Selecione sincronização inicial de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na página **de Validação,** clique em **Seguinte**. Esta página deve ser semelhante à seguinte imagem:

    ![Novo Assistente AG, Validação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Existe um aviso para a configuração do ouvinte porque não configura um ouvinte do Grupo de Disponibilidade. Pode ignorar este aviso porque nas máquinas virtuais Azure cria-se o ouvinte depois de criar o equilibrador de carga Azure.

10. Na página **Resumo,** clique em **Terminar**e, em seguida, espere enquanto o assistente configura o novo Grupo de Disponibilidade. Na página **Progress,** pode clicar em **Mais detalhes** para ver o progresso detalhado. Uma vez terminado o assistente, inspecione a página **Resultados** para verificar se o Grupo de Disponibilidade é criado com sucesso.

     ![Novo Assistente AG, Resultados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Clique **perto** para sair do assistente.

### <a name="check-the-availability-group"></a>Consulte o Grupo de Disponibilidade

1. No **Object Explorer,** expanda **sempre on alta disponibilidade,** em seguida, expandir **Grupos de Disponibilidade**. Deverá agora ver o novo Grupo de Disponibilidade neste contentor. Clique no Grupo de Disponibilidade e clique em **Mostrar Dashboard**.

   ![Mostrar Painel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   O seu **Painel AlwaysOn** deve ser semelhante a este.

   ![Painel AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Pode-se ver as réplicas, o modo de falha de cada réplica e o estado de sincronização.

2. No **Failover Cluster Manager,** clique no seu cluster. Selecione **Funções**. O nome do Grupo de Disponibilidade que usou é um papel no cluster. Esse Grupo de Disponibilidade não tem um endereço IP para ligações ao cliente, porque não configuraum ouvinte. Configurará o ouvinte depois de criar um equilibrador de carga Azure.

   ![AG em Failover Cluster Manager](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente falhar o Grupo de Disponibilidade do Gestor de Cluster failover. Todas as operações de failover devem ser realizadas a partir de dentro do **Painel AlwaysOn** em SSMS. Para mais informações, consulte [restrições na utilização do Gestor de Cluster failover com grupos de disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Neste ponto, você tem um Grupo de Disponibilidade com réplicas em duas instâncias do Servidor SQL. Pode mover o Grupo de Disponibilidade entre instâncias. Ainda não é possível ligar-se ao Grupo de Disponibilidade porque não tem ouvinte. Nas máquinas virtuais Azure, o ouvinte requer um equilibrista de carga. O próximo passo é criar o equilibrista de carga em Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Nas máquinas virtuais Azure, um Grupo de Disponibilidade de ServidorEs SQL requer um equilíbrio de carga. O equilibrador de carga detém os endereços IP para os ouvintes do Grupo de Disponibilidade e o Cluster de Failover do Servidor do Windows. Esta secção resume como criar o equilibrista de carga no portal Azure.

Um balancedor de carga Azure pode ser um balanceor de carga padrão ou um equilíbrio de carga básico. O Equilíbrio de Carga Standard tem mais funcionalidades do que o Equilíbrio de Carga Básico. Para um grupo de disponibilidade, o Balancer de Carga Padrão é necessário se utilizar uma Zona de Disponibilidade (em vez de um Conjunto de Disponibilidade). Para obter mais detalhes sobre a diferença entre os tipos de equilíbrio de carga, consulte a [comparação SKU](../../../load-balancer/concepts-limitations.md#skus)do Balancer de Carga .

1. No portal Azure, vá ao grupo de recursos onde estão os seus Servidores SQL e clique **+ Adicione**.
1. Procurar o **Balancer de Carga**. Escolha o equilibrador de carga publicado pela Microsoft.

   ![AG em Failover Cluster Manager](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Clique em **Criar**.
1. Configure os seguintes parâmetros para o equilibrador de carga.

   | Definição | Campo |
   | --- | --- |
   | **Nome** |Utilize um nome de texto para o equilibrante de carga, por exemplo **sqlLB**. |
   | **Tipo** |Interno |
   | **Rede virtual** |Utilize o nome da rede virtual Azure. |
   | **Sub-rede** |Utilize o nome da sub-rede em que a máquina virtual está.  |
   | **Atribuição de endereçoIP** |Estático |
   | **Endereço IP** |Utilize um endereço disponível a partir da sub-rede. Utilize este endereço para o seu ouvinte de grupo de disponibilidade. Note que isto é diferente do seu endereço IP cluster.  |
   | **Subscrição** |Utilize a mesma subscrição que a máquina virtual. |
   | **Localização** |Utilize o mesmo local que a máquina virtual. |

   A lâmina do portal Azure deve ser assim:

   ![Criar Balanceador de carga](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Clique em **Criar,** para criar o equilibrador de carga.

Para configurar o equilibrador de carga, é necessário criar uma piscina de backend, uma sonda e definir as regras de equilíbrio de carga. Faça isto no portal Azure.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Adicione piscina de backend para o ouvinte do grupo de disponibilidade

1. No portal Azure, vá ao seu grupo de disponibilidade. Talvez seja necessário refrescar a vista para ver o recém-criado equilibrador de carga.

   ![Encontre o Balancer de Carga no Grupo de Recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Clique no equilibrador de carga, clique em **piscinas backend,** e clique em **+Adicionar**.

1. Escreva um nome para a piscina de backend.

1. Associe o pool de backend com o conjunto de disponibilidade que contém os VMs.

1. Nas configurações IP da **rede Target,** verifique a VIRTUAL **MACHINE** e escolha ambas as máquinas virtuais que irão acolher réplicas do grupo de disponibilidade. Não inclua o servidor de testemunhas de partilha de ficheiros.

   >[!NOTE]
   >Se ambas as máquinas virtuais não forem especificadas, as ligações só terão sucesso na réplica primária.

1. Clique **em OK** para criar a piscina de backend.

### <a name="set-the-probe"></a>Definir a sonda

1. Clique no equilibrador de carga, clique em **sondas de saúde,** e clique em **+Adicionar**.

1. Desloque a sonda de saúde do ouvinte da seguinte forma:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Escolha TCP | TCP |
   | **Porta** | Qualquer porto não utilizado | 59999 |
   | **Intervalo**  | A quantidade de tempo entre as tentativas da sonda em segundos |5 |
   | **Limiar insalubre** | O número de falhas de sonda consecutivas que devem ocorrer para uma máquina virtual ser considerada insalubre  | 2 |

1. Clique **em OK** para definir a sonda de saúde.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de equilíbrio de carga

1. Clique no equilibrador de carga, clique em carregar regras de **equilíbrio,** e clique em **+Adicionar**.

1. Detete as regras de equilíbrio da carga do ouvinte da seguinte forma.

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP frontend** | Escolha um endereço |Use o endereço que criou quando criou o equilibrador de carga. |
   | **Protocolo** | Escolha TCP |TCP |
   | **Porta** | Utilize a porta para o ouvinte do grupo de disponibilidade | 1433 |
   | **Porto backend** | Este campo não é utilizado quando o IP flutuante é definido para a devolução do servidor direto | 1433 |
   | **Sonda** |O nome que especificou para a sonda | SQLAlwaysOnEndPointProbe |
   | **Persistência da Sessão** | Lista de descidas | **Nenhuma.** |
   | **Timeout inativo** | Minutos para manter aberta uma ligação TCP | 4 |
   | **IP flutuante (devolução do servidor direto)** | |Ativado |

   > [!WARNING]
   > A devolução do servidor direto é definida durante a criação. Não pode ser alterado.

1. Clique **em OK** para definir as regras de equilíbrio de carga do ouvinte.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Adicione o endereço IP do núcleo do cluster para o Cluster de Falha do Servidor do Windows (WSFC)

O endereço IP wSFC também precisa estar no equilibrador de carga.

1. No portal, no mesmo equilibrador de carga Azure, clique na **configuração IP frontend** e clique em **+Add**. Utilize o Endereço IP configurado para o WSFC nos recursos centrais do cluster. Desloque o endereço IP como estático.

1. No equilibrador de carga, clique em **sondas de saúde,** e clique em **+Adicionar**.

1. Desloque a sonda de saúde ip do núcleo de cluster WSFC da seguinte forma:

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPointProbe |
   | **Protocolo** | Escolha TCP | TCP |
   | **Porta** | Qualquer porto não utilizado | 58888 |
   | **Intervalo**  | A quantidade de tempo entre as tentativas da sonda em segundos |5 |
   | **Limiar insalubre** | O número de falhas de sonda consecutivas que devem ocorrer para uma máquina virtual ser considerada insalubre  | 2 |

1. Clique **em OK** para definir a sonda de saúde.

1. Detete as regras de equilíbrio de carga. Clique em **carregar regras**de equilíbrio e clique em **+Adicionar**.

1. Detete as regras de equilíbrio da carga de endereço IP do núcleo do cluster da seguinte forma.

   | Definição | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPoint |
   | **Endereço IP frontend** | Escolha um endereço |Utilize o endereço que criou quando configurao o endereço IP WSFC. Isto é diferente do endereço IP do ouvinte |
   | **Protocolo** | Escolha TCP |TCP |
   | **Porta** | Utilize a porta para o endereço IP do cluster. Esta é uma porta disponível que não é utilizada para a porta de sonda de escuta. | 58888 |
   | **Porto backend** | Este campo não é utilizado quando o IP flutuante é definido para a devolução do servidor direto | 58888 |
   | **Sonda** |O nome que especificou para a sonda | WSFCEndPointProbe |
   | **Persistência da Sessão** | Lista de descidas | **Nenhuma.** |
   | **Timeout inativo** | Minutos para manter aberta uma ligação TCP | 4 |
   | **IP flutuante (devolução do servidor direto)** | |Ativado |

   > [!WARNING]
   > A devolução do servidor direto é definida durante a criação. Não pode ser alterado.

1. Clique **em OK** para definir as regras de equilíbrio de carga.

## <a name="configure-listener"></a>Configure o ouvinte

A próxima coisa a fazer é configurar um ouvinte do Grupo de Disponibilidade no cluster failover.

> [!NOTE]
> Este tutorial mostra como criar um único ouvinte - com um endereço IP ILB. Para criar um ou mais ouvintes usando um ou mais endereços IP, consulte [Create Availability Group listener e load balancer  Azure.](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Definir porta de escuta

No Estúdio de Gestão de Servidores SQL, detete a porta do ouvinte.

1. Lance o Estúdio de Gestão de Servidores SQL e ligue-se à réplica primária.

1. Navegue para **sempre Em grupos** de disponibilidade | de disponibilidade | **ouvintes** do grupo de **disponibilidade.**

1. Agora deve ver o nome do ouvinte que criou no Failover Cluster Manager. Clique no nome do ouvinte e clique em **Propriedades**.

1. Na caixa **do Porto,** especifique o número de porta para o ouvinte do Grupo de Disponibilidade. 1433 é o padrão, em seguida, clique **OK**.

Tem agora um Grupo de Disponibilidade de ServidorEs SQL em máquinas virtuais Azure em funcionamento no modo Gestor de Recursos.

## <a name="test-connection-to-listener"></a>Ligação de teste ao ouvinte

Para testar a ligação:

1. RDP para um Servidor SQL que está na mesma rede virtual, mas não possui a réplica. Pode utilizar o outro Servidor SQL no cluster.

1. Utilize um utilitário **sqlcmd** para testar a ligação. Por exemplo, o seguinte script estabelece uma ligação **sqlcmd** à réplica primária através do ouvinte com autenticação Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Se o ouvinte estiver a utilizar uma porta diferente da porta predefinida (1433), especifique a porta na cadeia de ligação. Por exemplo, o seguinte comando sqlcmd conecta-se a um ouvinte na porta 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

A ligação SQLCMD liga-se automaticamente a qualquer instância do SQL Server que acolhe a réplica primária.

> [!TIP]
> Certifique-se de que a porta que especifica está aberta na firewall de ambos os Servidores SQL. Ambos os servidores requerem uma regra de entrada para a porta TCP que utiliza. Para mais informações, consulte [Adicionar ou Editar a Regra da Firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Passos seguintes

- [Adicione um endereço IP a um equilibrador](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP)de carga para um segundo grupo de disponibilidade .
