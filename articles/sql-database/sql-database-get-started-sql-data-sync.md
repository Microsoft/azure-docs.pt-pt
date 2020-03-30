---
title: Configurar o Sincronizado de Dados
description: Este tutorial mostra-lhe como configurar o Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 019ddbac1900856666b958d90b4395f25eb5ee84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268967"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Tutorial: Configurar sincronização de dados SQL entre a Base de Dados Azure SQL e o Servidor SQL no local

Neste tutorial, aprende-se a configurar o Azure SQL Data Sync criando um grupo de sincronização que contém tanto as instâncias de Base de Dados SQL do Azure como do Servidor SQL. O grupo de sincronização é configurado sob medida e sincroniza na programação que definiu.

O tutorial assume que tem pelo menos alguma experiência anterior com base de dados SQL e Servidor SQL.

Para uma visão geral do SQL Data Sync, consulte os dados do Sync através da nuvem e das bases de dados no local com o [Azure SQL Data Sync](sql-database-sync-data.md).

Para exemplos de PowerShell sobre como configurar o SQL Data Sync, consulte como sincronizar entre bases de [dados Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md) ou uma Base de [Dados Azure SQL e uma base de dados](scripts/sql-database-sync-data-between-azure-onprem.md) no local do SQL Server

> [!IMPORTANT]
> O Azure SQL Data Sync **não** suporta a Instância Gerida pela Base de Dados Azure SQL neste momento.

## <a name="create-sync-group"></a>Criar grupo de sincronização

1. Vá ao [portal Azure](https://portal.azure.com) para encontrar a sua base de dados SQL. Procure e selecione bases de **dados SQL**.

    ![Pesquisa de bases de dados SQL, portal Microsoft Azure](media/sql-database-get-started-sql-data-sync/search-for-sql-databases.png)

1. Selecione a base de dados que pretende utilizar como base de dados de dados.

    ![Selecione na lista de bases de dados SQL, portal Microsoft Azure](media/sql-database-get-started-sql-data-sync/select-sql-database.png)

    > [!NOTE]
    > A base de dados do hub é o ponto final central de uma topologia de sincronização, na qual um grupo de sincronização tem vários pontos finais de base de dados. Todas as outras bases de dados dos membros com pontos finais no grupo de sincronização, sincronizam-se com a base de dados do hub.

1. No menu de base de **dados SQL** para a base de dados selecionada, selecione **Sync para outras bases de dados**.

    ![Sincronização com outras bases de dados, base de dados SQL, portal Microsoft Azure](media/sql-database-get-started-sql-data-sync/sync-to-other-databases.png)

1. Na página **Sync para outras bases** de dados, selecione New Sync **Group**. A página do **novo grupo sync** abre com o grupo **Desincronização (passo 1)** em destaque.

   ![Definições do passo 1](media/sql-database-get-started-sql-data-sync/stepone.png)

   Na página **Create Data Sync Group,** altere as seguintes definições:

   | Definição                        | Descrição |
   | ------------------------------ | ------------------------------------------------- |
   | **Nome do grupo sync** | Insira um nome para o novo grupo de sincronização. Este nome é distinto do nome da própria base de dados. |
   | **Base de Dados de Metadados de Sincronização** | Opte por criar uma base de dados (recomendada) ou utilizar uma base de dados existente.<br/><br/>Se escolher **Nova base de dados,** selecione Criar nova base de **dados.** Em seguida, na página base de **dados SQL,** nomee e configure a nova base de dados e selecione **OK**.<br/><br/>Se escolher Utilizar a base de **dados existente,** selecione a base de dados da lista. |
   | **Sincronização Automática** | Selecione **Ligado** ou **Desligado**.<br/><br/>Se escolher **,** introduza um número e selecione **Segundos, Minutos,** **Horas**ou **Dias** na secção **De Sincronização.** **Minutes** |
   | **Resolução de Conflitos** | Selecione **Hub ganhar** ou **ganhar membro.**<br/><br/>**A vitória** do hub significa que quando ocorrem conflitos, os dados na base de dados do hub sobreescrevem dados contraditórios na base de dados dos membros.<br/><br/>A vitória dos **membros** significa que quando ocorrem conflitos, os dados na base de dados dos membros sobreescrevem dados contraditórios na base de dados do hub. |

   > [!NOTE]
   > A Microsoft recomenda a criação de uma nova base de dados vazia para utilização como Base de Dados de **Metadados sincronizados**. Data Sync cria tabelas nesta base de dados e executa uma carga de trabalho frequente. Esta base de dados é partilhada como base de dados de **metadados sincronizados** para todos os Grupos Sync numa região selecionada e não pode alterar a base de dados ou o seu nome sem remover todos os Sync Groups e Sync Agents na região.

   Selecione **OK** e aguarde que o grupo de sincronização seja criado e implantado.

## <a name="add-sync-members"></a>Adicionar membros de sincronização

Após a criação e implantação do novo grupo de sincronização, **adicionar membros de sincronização (passo 2)** é destacado na página do **grupo New Sync.**

Na secção Base de **Dados Hub,** introduza as credenciais existentes para o servidor de base de dados SQL no qual está localizada a base de dados do hub. Não introduza *novas* credenciais nesta secção.

![Definições do passo 2](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Para adicionar uma base de dados Azure SQL

Na secção Base de **Dados dos Membros,** adicione opcionalmente uma Base de Dados SQL Azure ao grupo de sincronização selecionando Adicionar uma Base de **Dados SQL Azure**. A página de base de **dados Configure Azure SQL** abre.

  ![Passo 2 - configurar base de dados](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na página de Base de **Dados Configure Azure SQL,** altere as seguintes definições:

  | Definição                       | Descrição |
  | ----------------------------- | ------------------------------------------------- |
  | **Nome membro sincronizado** | Forneça um nome para o novo membro da sincronização. Este nome é distinto do próprio nome da base de dados. |
  | **Assinatura** | Selecione a subscrição Azure associada para efeitos de faturação. |
  | **Servidor Azure SQL** | Selecione o servidor de base de dados SQL existente. |
  | **Base de Dados SQL do Azure** | Selecione a base de dados SQL existente. |
  | **Direções de sincronização** | Selecione **Sincronização Bidirecional,** **Para o Hub,** ou **a partir do Hub**. |
  | **Nome de utilizador** e **palavra-passe** | Introduza as credenciais existentes para o servidor de base de dados SQL no qual está localizada a base de dados dos membros. Não introduza *novas* credenciais nesta secção. |

  Selecione **OK** e aguarde que o novo membro da sincronização seja criado e implementado.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Para adicionar uma base de dados de servidor SQL no local

Na secção base de dados dos **membros,** adicione opcionalmente um Servidor SQL no local ao grupo de sincronização selecionando **Adicionar uma base de dados on-premissas**. A página **Configure On-Premises** abre onde pode fazer as seguintes coisas:

1. **Selecione Escolha o Gateway do Agente Sincronizado**. A página **Select Sync Agent** abre.

   ![Criar um agente de sincronização](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na página **Escolha da Página Do Agente Sincronizado,** escolha se utiliza um agente existente ou cria um agente.

   Se escolher **os agentes existentes,** selecione o agente existente na lista.

   Se escolher **Criar um novo agente,** faça as seguintes coisas:

   1. Descarregue o agente de sincronização de dados a partir do link fornecido e instale-o no computador onde está localizado o Servidor SQL. Também pode descarregar o agente diretamente do Agente de Sincronização de [Dados SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Tem de abrir a porta TCP 1433 na firewall para permitir que o agente cliente se comunique com o servidor.

   1. Insira um nome para o agente.

   1. Selecione **Criar e Gerar Chave** e copiar a chave do agente para a área de reparação.

   1. Selecione **OK** para fechar a página **Select Sync Agent.**

1. No computador SQL Server, localize e execute a aplicação Client Sync Agent.

   ![A aplicação de agente cliente sincronizado de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. Na aplicação do agente de sincronização, selecione **Enviar chave**do agente . A caixa de diálogo de configuração de base de **dados de metadados sync metadata** abre.

    1. Na caixa de diálogo de configuração de base de dados de **metadados Sync Metadata,** cola na chave do agente copiada do portal Azure. Forneça também as credenciais existentes para o servidor de base de dados Azure SQL no qual está localizada a base de dados de metadados. (Se criou uma base de dados de metadados, esta base de dados encontra-se no mesmo servidor que a base de dados do hub.) Selecione **OK** e aguarde que a configuração termine.

        ![Introduza as credenciais da chave do agente e do servidor](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se tiver um erro de firewall, crie uma regra de firewall no Azure para permitir o tráfego de entrada a partir do computador SQL Server. Pode criar a regra manualmente no portal ou no Estúdio de Gestão de Servidores SQL (SSMS). No SSMS, ligue-se à base de dados do hub no Azure, inserindo o seu nome como <hub_database_name>.windows.net.

    1. Selecione **Registar** para registar uma base de dados do SQL Server com o agente. A caixa de diálogo de configuração do **servidor SQL** abre-se.

        ![Adicione e configure uma base de dados do Servidor SQL](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Na caixa de diálogo de configuração do **servidor SQL,** opte por se ligar utilizando a autenticação do Servidor SQL ou a autenticação do Windows. Se escolher a autenticação do Servidor SQL, introduza as credenciais existentes. Forneça o nome Do Servidor SQL e o nome da base de dados que pretende sincronizar e selecionar **a ligação** de teste para testar as suas definições. Em seguida, selecione **Guardar** e a base de dados registada aparece na lista.

        ![A base de dados do Servidor SQL está agora registada](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Feche a aplicação Client Sync Agent.

1. No portal, na página **Configurar As Instalações,** **selecione Selecione a Base de Dados**.

1. Na página **Select Database,** no campo **Sync Member Name,** forneça um nome para o novo membro da sincronização. Este nome é distinto do nome da própria base de dados. Selecione a base de dados da lista. No campo **Sync Directions,** selecione **Sincronização Bidirecional,** **Para o Hub**, ou a partir do **Hub**.

    ![Selecione a base de dados no local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Selecione **OK** para fechar a página **'Selecionar Base de Dados'.** Em seguida, selecione **OK** para fechar a página **Configure On-Premises** e aguarde que o novo membro da sincronização seja criado e implementado. Por fim, selecione **OK** para fechar a página **select sync members.**

> [!NOTE]
> Para ligar ao SQL Data Sync e ao agente local, adicione o seu nome de utilizador à função *DataSync_Executor*. Data Sync cria esta função na instância Do Servidor SQL.

## <a name="configure-sync-group"></a>Configure grupo de sincronização

Após a criação e implantação dos novos membros do grupo sync, o **grupo de sincronização Configure (passo 3)** é destacado na página do **grupo New Sync.**

![Definições do passo 3](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na página **Tabelas,** selecione uma base de dados da lista de membros do grupo sync e selecione **Refresh schema**.

1. A partir da lista, selecione as tabelas que pretende sincronizar. Por predefinição, todas as colunas são selecionadas, por isso desative a caixa de verificação das colunas que não pretende sincronizar. Certifique-se de que deixa a coluna principal selecionada.

1. Selecione **Guardar**.

1. Por predefinição, as bases de dados não são sincronizadas até que sejam executadas ou executadas manualmente. Para executar uma sincronização manual, navegue para a sua base de dados SQL no portal Azure, selecione **Sync para outras bases**de dados e selecione o grupo de sincronização. A página **Data Sync** abre. Selecione **Sincronizar**.

    ![Sincronização manual](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>FAQ

**Com que frequência o Data Sync sincroniza os meus dados?**

A duração mínima entre sincronizações é de cinco minutos.

**O SQL Data Sync cria quadros?**

Se existirem tabelas do esquema de sincronização em falta na base de dados de destino, a Sincronização de Dados SQL cria-as com as colunas que selecionou. No entanto, isto não resulta num esquema de fidelidade total pelas seguintes razões:

- Apenas as colunas que selecionar são criadas na tabela de destino. As colunas não selecionadas são ignoradas.
- Apenas os índices de colunas selecionadas são criados na tabela de destino. Para as colunas não selecionadas, esses índices são ignorados.
- Os índices nas colunas do tipo XML não são criados.
- As restrições de VERIFICAÇÃO não são criadas.
- Os gatilhos nas mesas de origem não são criados.
- Não são criadas vistas e procedimentos armazenados.

Devido a estas limitações, recomendamos os seguintes procedimentos:

- Para ambientes de produção, crie o esquema de fidelidade total.
- Ao experimentar o serviço, utilize a função de fornecimento automático.

**Por que vejo mesas que não criei?**

Data Sync cria tabelas adicionais na base de dados para rastreio de alterações. Não elimine estes ou o Data Sync deixa de funcionar.

**Os meus dados convergem depois de uma sincronização?**

Não necessariamente. Tome um grupo de sincronização com um hub e três raios (A, B e C) onde as sincronizações são Hub para A, Hub a B, e Hub a C. Se for feita uma alteração na base de dados A *após* o Registo do Hub a Uma sincronização, essa alteração não é escrita na base de dados B ou na base de dados C até à próxima tarefa de sincronização.

**Como posso fazer com que o esquema mude para um grupo de sincronização?**

Faça e propague todas as alterações de esquema manualmente.

1. Replicar o esquema muda manualmente para o centro e para todos os membros sincronizados.
1. Atualize o esquema de sincronização.

Para adicionar novas tabelas e colunas:

Novas tabelas e colunas não impactam a sincronização atual e o Data Sync ignora-as até serem adicionadas ao esquema de sincronização. Ao adicionar novos objetos de base de dados, siga a sequência:

1. Adicione novas tabelas ou colunas ao centro e a todos os membros sincronizados.
1. Adicione novas tabelas ou colunas ao esquema de sincronização.
1. Comece a inserir valores nas novas tabelas e colunas.

Para alterar o tipo de dados de uma coluna:

Ao alterar o tipo de dados de uma coluna existente, o Data Sync continua a funcionar desde que os novos valores se encaixem no tipo de dados original definido no esquema de sincronização. Por exemplo, se alterar o tipo na base de dados de origem de **int** para **bigint**, o Data Sync continua a funcionar até inserir um valor demasiado grande para o tipo de dados **int.** Para completar a alteração, replicar a alteração do esquema manualmente para o centro e para todos os membros sincronizados, em seguida, atualizar o esquema de sincronização.

**Como posso exportar e importar uma base de dados com Data Sync?**

Depois de exportar uma base de dados como um ficheiro *.bacpac* e importar o ficheiro para criar uma base de dados, faça o seguinte para utilizar o Data Sync na nova base de dados:

1. Limpe os objetos Data Sync e tabelas adicionais na nova base de dados utilizando [este script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). O script elimina todos os objetos Data Sync necessários da base de dados.
1. Recrie o grupo de sincronização com a nova base de dados. Se já não precisar do antigo grupo de sincronização, apague-o.

**Onde posso encontrar informações sobre o agente cliente?**

Para perguntas frequentes sobre o agente cliente, consulte [o Agente FAQ](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Passos seguintes

Parabéns! Criou um grupo de sincronização que inclui tanto uma instância de Base de Dados SQL como uma base de dados do SQL Server.

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

- [Agente de sincronização de dados para sincronização de dados Azure SQL](sql-database-data-sync-agent.md)
- [Boas práticas](sql-database-best-practices-data-sync.md) e [como resolver problemas com o Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
- [Monitor SQL Data Sync com registos do Monitor Azure](sql-database-sync-monitor-oms.md)
- [Atualize o esquema de sincronização com transact-SQL](sql-database-update-sync-schema.md) ou [PowerShell](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
