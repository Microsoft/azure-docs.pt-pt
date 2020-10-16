---
title: Configurar Sincronização de Dados SQL
description: Este tutorial mostra-lhe como configurar o SQL Data Sync para O Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 90f1e068bf816ecf72bcc8a3ba9439883e69a069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91616680"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Tutorial: Configurar o SQL Data Sync entre bases de dados na Base de Dados Azure SQL e no SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, aprende-se a configurar o SQL Data Sync criando um grupo de sincronização que contém instâncias de Base de Dados SQL Azure e SQL Server. O grupo de sincronização é configurado sob medida e sincroniza-se no horário definido.

O tutorial pressupõe que tem pelo menos alguma experiência anterior com a SQL Database e o SQL Server.

Para obter uma visão geral do SQL Data Sync, consulte [os dados do Sync nas bases de dados de cloud e no local com o SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md).

Para exemplos de PowerShell sobre como configurar o SQL Data Sync, consulte [como sincronizar entre bases de dados na Base de Dados SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md) ou [entre bases de dados na Base de Dados SQL do Azure e no SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> O SQL Data Sync **não** suporta a Azure SQL Managed Instance neste momento.

## <a name="create-sync-group"></a>Criar grupo de sincronização

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar a sua base de dados na Base de Dados SQL. Procure e selecione **bases de dados SQL.**

    ![Pesquisa de bases de dados, portal Microsoft Azure](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Selecione a base de dados que pretende utilizar como base de dados do hub para Data Sync.

    ![Selecione a partir da lista de bases de dados, portal Microsoft Azure](./media/sql-data-sync-sql-server-configure/select-sql-database.png)

    > [!NOTE]
    > A base de dados do hub é o ponto final central de uma topologia de sincronização, na qual um grupo de sincronização tem vários pontos finais de base de dados. Todas as bases de dados dos outros membros com pontos finais no grupo de sincronização, sincronizam-se com a base de dados do hub.

1. No menu de **base de dados SQL** para a base de dados selecionada, selecione **Sync para outras bases de dados.**

    ![Sincronizar com outras bases de dados, portal Microsoft Azure](./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png)

1. Na página **Sync para outras bases de dados,** selecione **New Sync Group**. A nova página **do grupo sync** abre com o grupo de **sincronização Create (passo 1)** em destaque.

   ![Definições do passo 1](./media/sql-data-sync-sql-server-configure/stepone.png)

   Na página **'Criar Grupo de Sincronização de Dados',** altere as seguintes definições:

   | Definições                        | Descrição |
   | ------------------------------ | ------------------------------------------------- |
   | **Nome do grupo de sincronização** | Insira um nome para o novo grupo de sincronização. Este nome é distinto do nome da própria base de dados. |
   | **Base de Dados de Metadados sincronizado** | Opte por criar uma base de dados (recomendada) ou utilizar uma base de dados existente.<br/><br/>Se escolher **Nova base de dados,** selecione Criar uma nova base de **dados.** Em seguida, na página **sql Database,** nome e configurar a nova base de dados e selecione **OK**.<br/><br/>Se escolher **Utilizar a base de dados existente,** selecione a base de dados da lista. |
   | **Sincronização automática** | Selecione **Ligado** ou **Desligado**.<br/><br/>Se escolher **On**, introduza um número e selecione **Segundos**, **Minutos,** **Horas**ou **Dias** na secção Frequência **de Sincronização.**<br/> A primeira sincronização começa após o período de intervalo selecionado decorrer a partir do momento em que a configuração é guardada.|
   | **Resolução de Conflitos** | Selecione **o Hub ganhe** ou o Membro **ganhe**.<br/><br/>**A vitória** do hub significa que quando ocorrem conflitos, os dados na base de dados do hub substituem dados contraditórios na base de dados dos membros.<br/><br/>**A vitória do membro** significa que quando ocorrem conflitos, os dados na base de dados dos membros substituem dados contraditórios na base de dados do hub. |

   > [!NOTE]
   > A Microsoft recomenda a criação de uma nova base de dados vazia para utilização como **Base de Dados de Metadados de Sincronização**. O Data Sync cria tabelas nesta base de dados e executa uma carga de trabalho frequente. Esta base de dados é partilhada como base **de dados de metríduo sincronizado** para todos os grupos sincronizados numa região selecionada e subscrição. Não é possível alterar a base de dados ou o nome sem remover todos os grupos de sincronização e agentes sincronizados da região.

   Selecione **OK** e aguarde que o grupo de sincronização seja criado e implantado.

## <a name="add-sync-members"></a>Adicionar membros sincronizados

Após a criação e implantação do novo grupo de sincronização, **os membros do Add Sync (passo 2)** são destacados na página do **grupo New Sync.**

Na secção **Base de Dados do Hub,** introduza as credenciais existentes para o servidor no qual se encontra a base de dados do hub. Não introduza *novas* credenciais nesta secção.

![Definições do passo 2](./media/sql-data-sync-sql-server-configure/steptwo.png)

### <a name="to-add-a-database-in-azure-sql-database"></a>Para adicionar uma base de dados na Base de Dados Azure SQL

Na secção **Base de Dados** dos Membros, adicione opcionalmente uma base de dados na Base de Dados Azure SQL ao grupo de sincronização selecionando **Adicionar uma Base de Dados Azure SQL**. A página **Configure Azure SQL Database** abre.

  ![Passo 2 - base de dados de configuração](./media/sql-data-sync-sql-server-configure/steptwo-configure.png)

  Na página **Configure Azure SQL Database,** altere as seguintes definições:

  | Definições                       | Descrição |
  | ----------------------------- | ------------------------------------------------- |
  | **Nome do membro do sync** | Forneça um nome para o novo membro da sincronização. Este nome é distinto do próprio nome da base de dados. |
  | **Subscrição** | Selecione a subscrição Azure associada para efeitos de faturação. |
  | **Azure SQL Server** | Selecione o servidor existente. |
  | **Base de Dados SQL do Azure** | Selecione a base de dados existente na Base de Dados SQL. |
  | **Direções de Sincronização** | Selecione **Sincronização bidional**, **para o hub**, ou a partir do **hub**. |
  | **Nome de utilizador** e **senha** | Introduza as credenciais existentes para o servidor no qual a base de dados dos membros está localizada. Não introduza *novas* credenciais nesta secção. |

  Selecione **OK** e aguarde que o novo membro da sincronização seja criado e implantado.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Para adicionar uma base de dados do SQL Server

Na secção **Base de Dados** dos Membros, adicione opcionalmente uma base de dados sql server ao grupo de sincronização selecionando **Adicionar uma Base de Dados de Instalações**. A página **Configure On-Premises** abre onde pode fazer as seguintes coisas:

1. **Selecione Escolha o Portal do Agente de Sincronização**. A página **Select Sync Agent** abre.

   ![Criar um agente de sincronização](./media/sql-data-sync-sql-server-configure/steptwo-agent.png)

1. Na página **Escolha o Agente de Sincronização,** escolha se usa um agente existente ou se cria um agente.

   Se escolher **os agentes existentes,** selecione o agente existente da lista.

   Se escolher **Criar um novo agente,** faça as seguintes coisas:

   1. Descarregue o agente de sincronização de dados a partir do link fornecido e instale-o no computador onde está localizado o SQL Server. Também pode descarregar o agente diretamente a partir do [Azure SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Tem de abrir a porta TCP de saída 1433 na firewall para permitir que o agente cliente comunique com o servidor.

   1. Insira um nome para o agente.

   1. Selecione **Criar e Gerar Tecla** e copiar a chave do agente para a área de transferência.

   1. Selecione **OK** para fechar a página **Select Sync Agent.**

1. No computador SQL Server, localize e execute a aplicação 'Agente sincronizado' de clientes.

   ![A aplicação de agente de cliente sincronizado de dados](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. Na aplicação sync agent, **selecione Envie a Chave do Agente de Submissão**. Abre-se a caixa de diálogo de **configuração da base de dados de metadados de sincronização.**

    1. Na caixa de diálogo de **configuração de configuração de metadados de sincronização,** cole na tecla de agente copiada do portal Azure. Forneça também as credenciais existentes para o servidor no qual se encontra a base de dados de metadados. (Se criou uma base de dados de metadados, esta base de dados está no mesmo servidor que a base de dados do hub.) Selecione **OK** e aguarde que a configuração termine.

        ![Introduza as credenciais da chave do agente e do servidor](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se obter um erro de firewall, crie uma regra de firewall no Azure para permitir a entrada de tráfego a partir do computador SQL Server. Pode criar a regra manualmente no portal ou no SQL Server Management Studio (SSMS). Em SSMS, ligue-se à base de dados do hub em Azure, introduzindo o seu nome como <hub_database_name>.database.windows.net.

    1. Selecione **Registar-se** para registar uma base de dados do SQL Server com o agente. Abre-se a caixa de diálogo **de configuração do servidor SQL.**

        ![Adicionar e configurar uma base de dados do SQL Server](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. Na caixa de diálogo de configuração do **servidor SQL,** opte por ligar utilizando a autenticação do SQL Server ou a autenticação do Windows. Se escolher a autenticação do SQL Server, insira as credenciais existentes. Forneça o nome do SQL Server e o nome da base de dados que pretende sincronizar e selecionar **a ligação de teste** para testar as suas definições. Em seguida, **selecione Save** e a base de dados registada aparece na lista.

        ![A base de dados do SQL Server está agora registada](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Feche a aplicação Client Sync Agent.

1. No portal, na página **Configurar As instalações,** **selecione Selecione a Base de Dados**.

1. Na página **'Selecionar base de dados',** no campo **Sync Member Name,** forneça um nome para o novo membro sincronizado. Este nome é distinto do nome da própria base de dados. Selecione a base de dados da lista. No campo **Sync Directions,** selecione **Sync Bi-direccional**, **Para o Hub**ou A partir **do Hub**.

    ![Selecione a base de dados de instalações](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Selecione **OK** para fechar a página **'Selecionar base de dados'.** Em seguida, selecione **OK** para fechar a página **Configure On-Premis** e aguarde que o novo membro da sincronização seja criado e implantado. Por fim, selecione **OK** para fechar a página **de membros sync Select.**

> [!NOTE]
> Para ligar ao SQL Data Sync e ao agente local, adicione o seu nome de utilizador à função *DataSync_Executor*. O Data Sync cria esta função na instância do SQL Server.

## <a name="configure-sync-group"></a>Grupo de sincronização configure

Após a criação e implantação dos novos membros do grupo sincronizado, o **grupo de sincronização Configure (passo 3)** é destacado na página do **grupo New Sync.**

![Definições do passo 3](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Na página **Tabelas,** selecione uma base de dados da lista de membros do grupo de sincronização e selecione **Refresh schema**.

1. Na lista, selecione as tabelas que pretende sincronizar. Por predefinição, todas as colunas são selecionadas, por isso desative a caixa de verificação para as colunas que não pretende sincronizar. Certifique-se de que deixa a coluna de chave primária selecionada.

1. Selecione **Guardar**.

1. Por predefinição, as bases de dados não são sincronizadas até que seja programado ou executado manualmente. Para executar uma sincronização manual, navegue na sua base de dados na Base de Dados SQL no portal Azure, selecione **Sync para outras bases de dados**e selecione o grupo de sincronização. A página **Data Sync** abre. Selecione **Sincronizar**.

    ![Sincronização manual](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>FAQ

**Com que frequência o Data Sync pode sincronizar os meus dados?**

A duração mínima entre sincronizações é de cinco minutos.

**O SQL Data Sync cria tabelas totalmente?**

Se existirem tabelas do esquema de sincronização em falta na base de dados de destino, a Sincronização de Dados SQL cria-as com as colunas que selecionou. No entanto, isto não resulta num esquema de fidelidade total pelas seguintes razões:

- Apenas as colunas que selecionar são criadas na tabela de destino. As colunas não selecionadas são ignoradas.
- Apenas os índices de colunas selecionadas são criados na tabela de destino. Para as colunas não selecionadas, esses índices são ignorados.
- Os índices nas colunas do tipo XML não são criados.
- Os constrangimentos do CHECK não são criados.
- Os gatilhos nas tabelas de origem não são criados.
- Não são criadas vistas e procedimentos armazenados.

Devido a estas limitações, recomendamos os seguintes procedimentos:

- Para ambientes de produção, crie o esquema de fidelidade total.
- Ao experimentar o serviço, utilize a função de fornecimento automático.

**Por que vejo mesas que não criei?**

O Data Sync cria tabelas adicionais na base de dados para o rastreio de alterações. Não elimine estes ou o Data Sync deixa de funcionar.

**Os meus dados são convergentes depois de uma sincronização?**

Não necessariamente. Tome um grupo de sincronização com um hub e três raios (A, B e C) onde as sincronizações são Hub para A, Hub a B e Hub a C. Se for efetuada uma alteração na base de dados A *após* a sincronização do Hub para A, essa alteração não será escrita para a base de dados B ou base de dados C até à próxima tarefa de sincronização.

**Como posso fazer com que o esquema se transforme num grupo de sincronização?**

Faça e propague todos os esquemas muda manualmente.

1. Replique o esquema muda manualmente para o hub e para todos os membros sincronizados.
1. Atualize o esquema de sincronização.

Para adicionar novas tabelas e colunas:

Novas tabelas e colunas não afetam a sincronização atual e o Data Sync ignora-as até serem adicionadas ao esquema de sincronização. Ao adicionar novos objetos de base de dados, siga a sequência:

1. Adicione novas tabelas ou colunas ao centro e a todos os membros sincronizados.
1. Adicione novas tabelas ou colunas ao esquema de sincronização.
1. Comece a inserir valores nas novas tabelas e colunas.

Para alterar o tipo de dados de uma coluna:

Quando altera o tipo de dados de uma coluna existente, o Data Sync continua a funcionar desde que os novos valores se encaixem no tipo de dados original definido no esquema de sincronização. Por exemplo, se alterar o tipo na base de dados de origem de **int** para **bigint,** o Data Sync continua a funcionar até inserir um valor demasiado grande para o tipo de dados **int.** Para completar a alteração, replique o esquema muda manualmente para o hub e para todos os membros sincronizados e, em seguida, atualize o esquema de sincronização.

**Como posso exportar e importar uma base de dados com Data Sync?**

Depois de exportar uma base de dados como ficheiro *.bacpac* e importar o ficheiro para criar uma base de dados, faça o seguinte para utilizar o Data Sync na nova base de dados:

1. Limpe os objetos de Sincronização de Dados e tabelas adicionais na nova base de dados utilizando [este script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). O script elimina todos os objetos de Sincronização de Dados necessários da base de dados.
1. Recrie o grupo de sincronização com a nova base de dados. Se já não precisar do antigo grupo de sincronização, elimine-o.

**Onde posso encontrar informações sobre o agente cliente?**

Para perguntas frequentes sobre o agente cliente, consulte [o Agente FAQ.](sql-data-sync-agent-overview.md#agent-faq)

## <a name="next-steps"></a>Passos seguintes

Parabéns! Criou um grupo de sincronização que inclui uma instância sql database e uma base de dados SQL Server.

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

- [Agente de sincronização de dados para Azure SQL Data Sync](sql-data-sync-agent-overview.md)
- [Boas práticas](sql-data-sync-best-practices.md) e [como resolver problemas com o Azure SQL Data Sync](sql-data-sync-troubleshoot.md)
- [Monitor SQL Data Sync com registos do Monitor Azure](sql-data-sync-monitor-sync.md)
- [Atualizar o esquema de sincronização com Transact-SQL](sql-data-sync-update-sync-schema.md) ou [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

- [Descrição Geral da Base de Dados SQL](sql-database-paas-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
