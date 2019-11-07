---
title: Configurar a Sincronização de Dados SQL do Azure
description: Este tutorial mostra como configurar o Azure Sincronização de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 01/14/2019
ms.openlocfilehash: 082721ef1436d0b6668016aca29870016038af86
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689988"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Tutorial: Configurar Sincronização de Dados SQL entre o banco de dados SQL do Azure e o SQL Server local

Neste tutorial, você aprenderá a configurar o Azure Sincronização de Dados SQL criando um grupo de sincronização que contém o banco de dados SQL do Azure e instâncias de SQL Server. O grupo de sincronização é personalizado e sincronizado no agendamento definido.

O tutorial pressupõe que você tenha pelo menos alguma experiência anterior com o banco de dados SQL e SQL Server.

Para obter uma visão geral do Sincronização de Dados SQL, consulte [sincronizar dados entre bancos de dados locais e de nuvem com o Azure sincronização de dados SQL](sql-database-sync-data.md).

Para obter exemplos do PowerShell sobre como configurar Sincronização de Dados SQL, confira [como sincronizar entre bancos de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md) ou [um Azure SQL database e um SQL Server banco de dados local](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL **não oferece suporte a** instância gerenciada do banco de dados SQL do Azure no momento.

## <a name="create-sync-group"></a>Criar grupo de sincronização

1. No navegador, navegue até a portal do Azure. Localize o banco de dados SQL no painel ou selecione o ícone bancos de dados **SQL** na barra de ferramentas e, na página bancos de dados **SQL** , selecione o banco que você deseja usar como o banco de dado de Hub para sincronização.

    > [!NOTE]
    > O banco de dados de Hub um ponto de extremidade central da topologia de sincronização, no qual um grupo de sincronização tem vários pontos de extremidade de banco de dados. Todos os outros bancos de dados de membros com pontos de extremidade no grupo de sincronização, sincronizam com o banco de dados Hub.

1. Na página do **banco de dados SQL** para o banco de dados selecionado, selecione **sincronizar com outros bancos**.

    ![Opção sincronizar com outros bancos de dados](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. Na página **sincronizar com outros bancos de dados** , selecione **novo grupo de sincronização**. A página **novo grupo de sincronização** é aberta com a **criação de grupo de sincronização (etapa 1)** realçada.

   ![Configurações da etapa 1](media/sql-database-get-started-sql-data-sync/stepone.png)

   Na página **Criar grupo de sincronização de dados** , altere as seguintes configurações:

   | Definição                        | Descrição |
   | ------------------------------ | ------------------------------------------------- |
   | **Nome do grupo de sincronização** | Insira um nome para o novo grupo de sincronização. Esse nome é diferente do nome do banco de dados em si. |
   | **Sincronizar banco de dados de metadados** | Escolha criar um banco de dados (recomendado) ou usar um banco de dados existente.<br/><br/>Se você escolher **novo banco de dados**, selecione **criar novo banco de dados.** Em seguida, na página do **banco de dados SQL** , nomeie e configure o novo banco de dados e selecione **OK**.<br/><br/>Se você escolher **usar banco de dados existente**, selecione o banco de dados na lista. |
   | **Sincronização automática** | Selecione **Ativar** ou **desativar**.<br/><br/>Se você escolher **ativado**, insira um número e selecione **segundos**, **minutos**, **horas**ou **dias** na seção **frequência de sincronização** . |
   | **Resolução de conflitos** | Selecione ganhador **Win** ou **Member Win**.<br/><br/>O **ganho de Hub** significa quando ocorrem conflitos, os dados no banco de dado Hub substitui dados conflitantes no banco de dado membro.<br/><br/>**Ganho de membro** significa quando ocorrem conflitos, os dados no banco de dado do membro substitui dados conflitantes no banco de dado Hub. |

   > [!NOTE]
   > A Microsoft recomenda criar um banco de dados novo e vazio para uso como o **banco de dados de metadados de sincronização**. A sincronização de dados cria tabelas neste banco e executa uma carga de trabalho frequente. Esse banco de dados é compartilhado como o **banco de dados de metadados de sincronização** para todos os grupos de sincronização em uma região selecionada e você não pode alterar o banco de dados ou seu nome sem remover todos os grupos de sincronização e agentes de sincronização na região.

   Selecione **OK** e aguarde até que o grupo de sincronização seja criado e implantado.

## <a name="add-sync-members"></a>Adicionar membros de sincronização

Depois que o novo grupo de sincronização for criado e implantado, **adicionar membros de sincronização (etapa 2)** será realçado na página **novo grupo de sincronização** .

Na seção **banco de dados Hub** , insira as credenciais existentes para o servidor do banco de dados SQL no qual o banco de dados do Hub está localizado. Não insira *novas* credenciais nesta seção.

![Configurações da etapa 2](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Para adicionar um banco de dados SQL do Azure

Na seção **banco de dados membro** , opcionalmente, adicione um banco de dados SQL do Azure ao grupo de sincronização selecionando **Adicionar um banco de dados SQL do Azure**. A página **configurar banco de dados SQL do Azure** é aberta.

  ![Etapa 2 – configurar o banco de dados](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Na página **configurar banco de dados SQL do Azure** , altere as seguintes configurações:

  | Definição                       | Descrição |
  | ----------------------------- | ------------------------------------------------- |
  | **Nome do membro de sincronização** | Forneça um nome para o novo membro de sincronização. Esse nome é diferente do próprio nome do banco de dados. |
  | **Subscrição** | Selecione a assinatura do Azure associada para fins de cobrança. |
  | **SQL Server do Azure** | Selecione o servidor de banco de dados SQL existente. |
  | **Base de Dados SQL do Azure** | Selecione o banco de dados SQL existente. |
  | **Sincronizar direções** | Selecione **sincronização bidirecional**, **para o Hub**ou **do Hub**. |
  | **Nome de usuário** e **senha** | Insira as credenciais existentes para o servidor do banco de dados SQL no qual o banco de dados membro está localizado. Não insira *novas* credenciais nesta seção. |

  Selecione **OK** e aguarde até que o novo membro de sincronização seja criado e implantado.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Para adicionar um banco de dados SQL Server local

Na seção **banco de dados membro** , opcionalmente, adicione uma SQL Server local ao grupo de sincronização selecionando **Adicionar um banco de dados local**. A página **Configurar local** é aberta, onde você pode fazer o seguinte:

1. Selecione **escolher o gateway do agente de sincronização**. A página **selecionar agente de sincronização** é aberta.

   ![Criando um agente de sincronização](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Na página **escolher o agente de sincronização** , escolha se deseja usar um agente existente ou criar um agente.

   Se você escolher **agentes existentes**, selecione o agente existente na lista.

   Se você escolher **criar um novo agente**, faça o seguinte:

   1. Baixe o agente de sincronização de dados do link fornecido e instale-o no computador onde o SQL Server está localizado. Você também pode baixar o agente diretamente do [SQL Azure agente de sincronização de dados](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Você precisa abrir a porta TCP de saída 1433 no firewall para permitir que o agente cliente se comunique com o servidor.

   1. Insira um nome para o agente.

   1. Selecione **criar e gerar chave** e copie a chave do agente para a área de transferência.

   1. Selecione **OK** para fechar a página **selecionar agente de sincronização** .

1. No computador SQL Server, localize e execute o aplicativo Agente de Sincronização do Cliente.

   ![O aplicativo agente cliente de sincronização de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. No aplicativo agente de sincronização, selecione **Enviar chave do agente**. A caixa de diálogo **configuração de banco de dados de metadados de sincronização** é aberta.

    1. Na caixa de diálogo **configuração do banco de dados de metadados de sincronização** , Cole a chave do agente copiada do portal do Azure. Forneça também as credenciais existentes para o servidor do banco de dados SQL do Azure no qual o banco de dados de metadados está localizado. (Se você criou um banco de dados de metadados, esse banco de dados está no mesmo servidor que o banco de dados de Hub.) Selecione **OK** e aguarde a conclusão da configuração.

        ![Insira a chave do agente e as credenciais do servidor](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se você receber um erro de firewall, crie uma regra de firewall no Azure para permitir o tráfego de entrada do computador SQL Server. Você pode criar a regra manualmente no portal ou no SQL Server Management Studio (SSMS). No SSMS, conecte-se ao banco de dados de Hub no Azure inserindo seu nome como < hub_database_name >. Database. Windows. net.

    1. Selecione **registrar** para registrar um banco de dados SQL Server com o agente. A caixa de diálogo **configuração de SQL Server** é aberta.

        ![Adicionar e configurar um banco de dados SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Na caixa de diálogo **configuração do SQL Server** , escolha se conectar usando a autenticação do SQL Server ou do Windows. Se você escolher SQL Server autenticação, insira as credenciais existentes. Forneça o nome do SQL Server e o nome do banco de dados que você deseja sincronizar e selecione **testar conexão** para testar as configurações. Em seguida, selecione **salvar** e o banco de dados registrado aparecerá na lista.

        ![SQL Server banco de dados agora está registrado](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Feche o aplicativo Agente de Sincronização do Cliente.

1. No portal, na página **Configurar local** , selecione **selecionar o banco de dados**.

1. Na página **Selecionar Banco de dados** , no campo **nome do membro de sincronização** , forneça um nome para o novo membro de sincronização. Esse nome é diferente do nome do banco de dados em si. Selecione o banco de dados na lista. No campo **direções de sincronização** , selecione **sincronização bidirecional**, **para o Hub**ou **do Hub**.

    ![Selecionar o banco de dados local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Selecione **OK** para fechar a página **Selecionar Banco de dados** . Em seguida, selecione **OK** para fechar a página **Configurar local** e aguarde até que o novo membro de sincronização seja criado e implantado. Por fim, selecione **OK** para fechar a página **selecionar membros de sincronização** .

> [!NOTE]
> Para se conectar ao Sincronização de Dados SQL e ao agente local, adicione seu nome de usuário à função *DataSync_Executor*. A sincronização de dados cria essa função na instância de SQL Server.

## <a name="configure-sync-group"></a>Configurar grupo de sincronização

Depois que os novos membros do grupo de sincronização são criados e implantados, **Configure o grupo de sincronização (etapa 3)** é realçado na página **novo grupo de sincronização** .

![Configurações da etapa 3](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Na página **tabelas** , selecione um banco de dados na lista de membros do grupo de sincronização e selecione **Atualizar esquema**.

1. Na lista, selecione as tabelas que você deseja sincronizar. Por padrão, todas as colunas são selecionadas, portanto, desabilite a caixa de seleção das colunas que você não deseja sincronizar. Certifique-se de deixar a coluna de chave primária selecionada.

1. Selecione **Guardar**.

1. Por padrão, os bancos de dados não são sincronizados até que sejam agendados ou executados manualmente. Para executar uma sincronização manual, navegue até o banco de dados SQL no portal do Azure, selecione **sincronizar com outros bancos**de dados e selecione o grupo de sincronização. A página **sincronização de dados** é aberta. Selecione **Sincronizar**.

    ![Sincronização manual](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>FAQ

**Com que frequência a sincronização de dados pode sincronizar meus dados?**

A duração mínima entre as sincronizações é de cinco minutos.

**Sincronização de Dados SQL criar tabelas totalmente?**

Se existirem tabelas do esquema de sincronização em falta na base de dados de destino, a Sincronização de Dados SQL cria-as com as colunas que selecionou. No entanto, isso não resulta em um esquema de fidelidade total pelos seguintes motivos:

- Apenas as colunas que selecionar são criadas na tabela de destino. As colunas não selecionadas são ignoradas.
- Apenas os índices de colunas selecionadas são criados na tabela de destino. Para as colunas não selecionadas, esses índices são ignorados.
- Índices em colunas de tipo XML não são criados.
- Restrições de verificação não são criadas.
- Os gatilhos nas tabelas de origem não são criados.
- Exibições e procedimentos armazenados não são criados.

Devido a estas limitações, recomendamos os seguintes procedimentos:

- Para ambientes de produção, crie o esquema de fidelidade total por conta própria.
- Ao experimentar o serviço, use o recurso de provisionamento automático.

**Por que vejo tabelas que não criei?**

A sincronização de dados cria tabelas adicionais no banco de dado para o controle de alterações. Não exclua esses ou a sincronização de dados para de funcionar.

**Meus dados são Convergent após uma sincronização?**

Não necessariamente. Pegue um grupo de sincronização com um Hub e três spokes (A, B e C) em que as sincronizações são Hub para a, Hub para B e Hub para C. Se for feita uma alteração no banco de dados A *após* o Hub para uma sincronização, essa alteração não será gravada no banco de dados B ou no banco de dados C até a próxima tarefa de sincronização.

**Como fazer obter alterações de esquema em um grupo de sincronização?**

Faça e propague todas as alterações de esquema manualmente.

1. Replique manualmente as alterações de esquema para o Hub e para todos os membros de sincronização.
1. Atualize o esquema de sincronização.

Para adicionar novas tabelas e colunas:

Novas tabelas e colunas não afetam a sincronização atual e a sincronização de dados as ignora até que elas sejam adicionadas ao esquema de sincronização. Ao adicionar novos objetos de banco de dados, siga a sequência:

1. Adicione novas tabelas ou colunas ao Hub e a todos os membros de sincronização.
1. Adicione novas tabelas ou colunas ao esquema de sincronização.
1. Comece a inserir valores nas novas tabelas e colunas.

Para alterar o tipo de dados de uma coluna:

Quando você altera o tipo de dados de uma coluna existente, a sincronização de dados continua funcionando, desde que os novos valores caibam no tipo de dados original definido no esquema de sincronização. Por exemplo, se você alterar o tipo no banco de dados de origem de **int** para **bigint**, a sincronização de dados continuará funcionando até que você insira um valor muito grande para o tipo de dados **int** . Para concluir a alteração, replique a alteração de esquema manualmente para o Hub e para todos os membros de sincronização e, em seguida, atualize o esquema de sincronização.

**Como faço para exportar e importar um banco de dados com a sincronização?**

Depois de exportar um banco de dados como um arquivo *. bacpac* e importar o arquivo para criar um banco de dados do, faça o seguinte para usar a sincronização de dados no novo banco de dado:

1. Limpe os objetos de sincronização de dados e tabelas adicionais no novo banco de dado usando [esse script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). O script exclui todos os objetos de sincronização de dados necessários do banco de dado.
1. Recrie o grupo de sincronização com o novo banco de dados. Se você não precisar mais do grupo de sincronização antigo, exclua-o.

**Onde posso encontrar informações sobre o agente cliente?**

Para perguntas frequentes sobre o agente cliente, consulte [perguntas frequentes](sql-database-data-sync-agent.md#agent-faq)sobre o agente.

## <a name="next-steps"></a>Passos seguintes

Parabéns! Você criou um grupo de sincronização que inclui uma instância do banco de dados SQL e um banco de dados SQL Server.

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

- [Agente de sincronização de dados para Sincronização de Dados SQL do Azure](sql-database-data-sync-agent.md)
- [Práticas recomendadas](sql-database-best-practices-data-sync.md) e [como solucionar problemas com o Azure sincronização de dados SQL](sql-database-troubleshoot-data-sync.md)
- [Monitorar Sincronização de Dados SQL com logs de Azure Monitor](sql-database-sync-monitor-oms.md)
- [Atualizar o esquema de sincronização com o Transact-SQL](sql-database-update-sync-schema.md) ou o [PowerShell](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
