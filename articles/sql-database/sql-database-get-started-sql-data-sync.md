---
title: Configurar a sincronização de dados SQL do Azure | Documentos da Microsoft
description: Este tutorial mostra-lhe como configurar a sincronização de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 01/14/2019
ms.openlocfilehash: 4cd7c61903bad5c0f1d6c41165e9bd7ff61a6d09
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213437"
---
# <a name="tutorial-set-up-sql-data-sync-between-azure-sql-database-and-sql-server-on-premises"></a>Tutorial: Configurar a sincronização de dados do SQL entre a base de dados do Azure SQL e SQL Server no local

Neste tutorial, saiba como configurar a sincronização de dados SQL do Azure através da criação de um grupo de sincronização que contém instâncias de base de dados do Azure SQL e SQL Server. O grupo de sincronização é personalizado configurado e sincroniza-se num agendamento definido por.

O tutorial parte do princípio de que tem, pelo menos, de alguma experiência anterior com a base de dados SQL e o SQL Server.

Para uma descrição geral da sincronização de dados SQL, consulte [sincronizar dados entre bases de dados na cloud e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md).

Para PowerShell exemplos sobre como configurar a sincronização de dados SQL, veja [como entre bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md) ou [uma base de dados do SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Sincronização de dados SQL do Azure faz **não** suporta a instância gerida da base de dados SQL do Azure neste momento.

## <a name="create-sync-group"></a>Criar grupo de sincronização

1. No browser, navegue para o portal do Azure. Localize a base de dados SQL a partir do dashboard ou, selecione o **bases de dados SQL** ícone na barra de ferramentas e, no **bases de dados SQL** , selecione a base de dados que pretende utilizar como a base de dados de hub para sincronização de dados.

    > [!NOTE]
    > O hub de base de dados ponto final de central uma topologia de sincronização, no qual um grupo de sincronização tem vários pontos de extremidade do banco de dados. Todos os outros membros bancos de dados com pontos finais no grupo de sincronização, sincronizar com a base de dados de hub.

1. Sobre o **base de dados SQL** página para a base de dados selecionada, selecione **sincronização para outros bancos de dados**.

    ![Sincronizar a outra opção de bases de dados](media/sql-database-get-started-sql-data-sync/datasync-overview.png)

1. Sobre o **sincronização para outros bancos de dados** página, selecione **novo grupo de sincronização**. O **novo grupo de sincronização** é aberta a página com **criar grupo de sincronização (passo 1)** realçado.

  ![Definições do passo 1](media/sql-database-get-started-sql-data-sync/stepone.png)

  Sobre o **criar grupo de sincronização de dados** página, altere as seguintes definições:

  | Definição                        | Descrição |
  | ------------------------------ | ------------------------------------------------- |
  | **Nome do grupo de sincronização** | Introduza um nome para o novo grupo de sincronização. Este nome é diferente do nome da base de dados em si. |
  | **Base de dados de metadados de sincronização** | Optar por criar uma base de dados (recomendado) ou utilizar uma base de dados existente.<br/><br/>Se escolher **nova base de dados**, selecione **criar nova base de dados.** Em seguida, sobre o **base de dados SQL** página, dê um nome e configurar a nova base de dados e selecione **OK**.<br/><br/>Se escolher **utilizar base dados existente**, selecione a base de dados a partir da lista. |
  | **Sincronização automática** | Selecione **nos** ou **desativar**.<br/><br/>Se escolher **nos**, introduza um número e selecione **segundos**, **minutos**, **horas**, ou **dias** no **Frequência de sincronização** secção. |
  | **Resolução de conflitos** | Selecione **Hub ganha** ou **membro ganha**.<br/><br/>**Hub ganha** significa que quando ocorrem conflitos, dados na base de dados de hub substitui dados em conflito na base de dados do membro.<br/><br/>**Membro ganha** significa que quando ocorrem conflitos, dados na base de dados membro substitui dados em conflito na base de dados de hub. |

  > [!NOTE]
  > Para criar um novo banco de dados vazio para utilização como a Microsoft recomenda a **base de dados de metadados de sincronização**. Sincronização de dados cria as tabelas nesta base de dados e executa uma carga de trabalho frequente. Esta base de dados é partilhado como o **base de dados de metadados de sincronização** para todos os grupos de sincronização numa região selecionada e não é possível alterar a base de dados ou o respetivo nome sem remover todos os grupos de sincronização e agentes de sincronização na região.

  Selecione **OK** e espere até que o grupo de sincronização ser criado e implementado.

## <a name="add-sync-members"></a>Adicionar membros de sincronização

Depois do novo grupo de sincronização é criado e implementado, **adicionar membros de sincronização (etapa 2)** é realçada no **novo grupo de sincronização** página.

Na **base de dados de Hub** , digite as credenciais existentes para o servidor de base de dados SQL, onde está localizada a base de dados de hub. Não introduza *novo* credenciais nesta secção.

![Definições do passo 2](media/sql-database-get-started-sql-data-sync/steptwo.png)

### <a name="to-add-an-azure-sql-database"></a>Para adicionar uma base de dados do SQL do Azure

Na **base de dados do membro** secção, opcionalmente, adicione uma base de dados do SQL do Azure para o grupo de sincronização ao selecionar **adicionar uma base de dados do SQL Azure**. O **configurar a base de dados de SQL do Azure** é aberta a página.

  ![Passo 2 – configurar a base de dados](media/sql-database-get-started-sql-data-sync/steptwo-configure.png)

  Sobre o **configurar a base de dados de SQL do Azure** página, altere as seguintes definições:

  | Definição                       | Descrição |
  | ----------------------------- | ------------------------------------------------- |
  | **Nome de membro de sincronização** | Forneça um nome para o novo membro de sincronização. Este nome é diferente do nome da base de dados em si. |
  | **Subscrição** | Selecione a subscrição do Azure associada para efeitos de faturação. |
  | **Azure SQL Server** | Selecione o servidor de base de dados SQL existente. |
  | **Base de Dados SQL do Azure** | Selecione a base de dados SQL existente. |
  | **Direções de sincronização** | Selecione **sincronização bidirecional**, **para o Hub**, ou **do Hub de**. |
  | **Nome de utilizador** e **palavra-passe** | Introduza as credenciais existentes para o servidor de base de dados SQL, onde está localizada a base de dados do membro. Não introduza *novo* credenciais nesta secção. |

  Selecione **OK** e espere até que o novo membro de sincronização ser criado e implementado.

<a name="add-on-prem"></a>
### <a name="to-add-an-on-premises-sql-server-database"></a>Para adicionar uma base de dados do SQL Server no local

Na **base de dados do membro** secção, opcionalmente, adicione um servidor de SQL no local para o grupo de sincronização ao selecionar **adicionar uma base de dados no local**. O **configurar no local** página abre-se de onde pode fazer as seguintes ações:

1. Selecione **escolha o Gateway do agente de sincronização**. O **selecionar agente de sincronização** é aberta a página.

  ![Criação de um agente de sincronização](media/sql-database-get-started-sql-data-sync/steptwo-agent.png)

1. Sobre o **escolha o agente de sincronização** página, escolha se pretende utilizar um agente existente ou criar um agente.

  Se escolher **agentes existentes**, selecione o agente existente na lista.

  Se escolher **criar um novo agente**, efetue os seguintes procedimentos:

    1. Transferir o agente de sincronização de dados da ligação fornecida e instalá-lo no computador onde está localizado o SQL Server. Também pode transferir o agente diretamente a partir [agente de sincronização de dados do SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Terá de abrir a porta de saída TCP 1433 na firewall para permitir que o agente do cliente comunicar com o servidor.

    1. Introduza um nome para o agente.

    1. Selecione **criar e gerar chave** e copie a chave de agente na área de transferência.

    1. Selecione **OK** para fechar a **selecionar agente de sincronização** página.

1. No computador do SQL Server, localize e execute a aplicação de agente de sincronização do cliente.

  ![Aplicação cliente do agente de sincronização de dados](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    1. Na aplicação do agente de sincronização, selecione **chave de agente submeter**. O **configuração de base de dados de metadados de sincronização** é aberta a caixa de diálogo.

    1. Na **configuração de base de dados de metadados de sincronização** caixa de diálogo, cole a chave de agente que copiou do portal do Azure. Também fornece as credenciais existentes para o servidor da SQL Database do Azure onde está localizada a base de dados de metadados. (Se tiver criado uma base de dados de metadados, esta base de dados está no mesmo servidor da base de dados de hub.) Selecione **OK** e espere até que a configuração concluir.

        ![Introduza as credenciais de chave e o servidor de agente](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se obtiver um erro de firewall, crie uma regra de firewall no Azure para permitir tráfego de entrada do computador do SQL Server. Pode criar manualmente a regra no portal ou no SQL Server Management Studio (SSMS). No SSMS, ligar à base de dados de hub no Azure ao introduzir o nome como < hub_database_name >. database.windows.net.

    1. Selecione **registar** para registar uma base de dados do SQL Server com o agente. O **configuração do SQL Server** é aberta a caixa de diálogo.

        ![Adicionar e configurar uma base de dados do SQL Server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    1. Na **configuração do SQL Server** diálogo caixa, optar por ligar-se de que a autenticação do SQL Server ou a autenticação do Windows. Se escolher a autenticação do SQL Server, introduza as credenciais existentes. Forneça o nome do SQL Server e o nome da base de dados que pretende sincronizar e selecione **Testar ligação** para testar as suas definições. Em seguida, selecione **guardar** e a base de dados registado aparece na lista.

        ![Base de dados do SQL Server está agora registado](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    1. Feche o cliente sincronizar a aplicação de agente.

1. No portal, sobre o **configurar no local** página, selecione **selecione a base de dados**.

1. No **selecionar base de dados** página, além do **nome do membro de sincronização** campo, forneça um nome para o novo membro de sincronização. Este nome é diferente do nome da base de dados em si. Selecione a base de dados a partir da lista. Na **direções de sincronização** campo, selecione **sincronização bidirecional**, **para o Hub**, ou **de Hub**.

    ![Selecione a base de dados no local](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

1. Selecione **OK** para fechar a **selecionar base de dados** página. Em seguida, selecione **OK** para fechar a **configurar no local** página e espere até que o novo membro de sincronização ser criado e implementado. Por fim, selecione **OK** para fechar a **selecionar membros de sincronização** página.

> [!NOTE]
> Para ligar a sincronização de dados SQL e o agente local, adicione o seu nome de utilizador à função *DataSync_Executor*. Sincronização de dados cria esta função na instância do SQL Server.

## <a name="configure-sync-group"></a>Configurar grupo de sincronização

Depois dos novos membros do grupo de sincronização são criados e implementados, **configurar o grupo de sincronização (etapa 3)** está realçado na **novo grupo de sincronização** página.

![Passo 3 definições](media/sql-database-get-started-sql-data-sync/stepthree.png)

1. Sobre o **tabelas** página, selecione uma base de dados a partir da lista de membros do grupo de sincronização e selecione **esquema de atualização**.

1. Na lista, selecione as tabelas que pretende sincronizar. Por predefinição, todas as colunas estão selecionadas, então, desative a caixa de verificação para as colunas que não pretende sincronizar. Certifique-se de que deixar a coluna de chave primária selecionada.

1. Selecione **Guardar**.

1. Por predefinição, as bases de dados não estão sincronizados até que a agendada ou executadas manualmente. Para executar uma sincronização manual, navegue para a base de dados SQL no portal do Azure, selecione **sincronização para outros bancos de dados**e selecione o grupo de sincronização. O **sincronização de dados** é aberta a página. Selecione **Sincronizar**.

    ![Sincronização manual](media/sql-database-get-started-sql-data-sync/datasync-sync.png)

## <a name="faq"></a>FAQ

**A frequência com que a sincronização de dados pode sincronizar os meus dados?**

A duração mínima entre as sincronizações é de cinco minutos.

**Sincronização de dados SQL totalmente criar tabelas?**

Se tabelas do esquema de sincronização estão em falta na base de dados de destino, a sincronização de dados SQL cria-as com as colunas que selecionou. No entanto, isso não é um esquema de fidelidade total pelos seguintes motivos:

- Apenas as colunas que selecionar são criadas na tabela de destino. Colunas não selecionadas serão ignoradas.
- Apenas os índices de colunas selecionadas são criados na tabela de destino. Para colunas não selecionadas, esses índices são ignorados.
- Índices em colunas de tipo XML não são criados.
- Restrições de verificação não são criadas.
- Os acionadores em tabelas de origem não são criados.
- As vistas e procedimentos armazenados não são criados.

Devido a essas limitações, recomendamos os seguintes procedimentos:

- Para ambientes de produção, crie o esquema de fidelidade total por conta própria.
- Ao experimentar com o serviço, utilize a funcionalidade de aprovisionamento automático.

**Por que vejo as tabelas que não tenha criado?**

Sincronização de dados cria tabelas adicionais na base de dados de registo de alterações. Não elimine estes ou sincronização de dados deixa de funcionar.

**É o meus dados convergent depois de uma sincronização?**

Não necessariamente. Tirar um grupo de sincronização com um hub e três spokes (A, B e C) em que as sincronizações são Hub para um Hub para o B e Hub para C. Se for feita uma alteração à base de dados A *depois de* Hub para uma sincronização, o que a alteração não for escrita para a base de dados B ou C até a próxima tarefa de sincronização.

**Como posso obter as alterações de esquema num grupo de sincronização?**

Faça e propagar manualmente a todas as alterações de esquema.

1. Replicar as alterações de esquema manualmente para o hub e a todos os membros de sincronização.
1. Atualize o esquema de sincronização.

Para adicionar novas tabelas e colunas:

Novas tabelas e colunas não tenham impacto sobre a sincronização atual e sincronização de dados ignora-os até que estes serão adicionados ao esquema de sincronização. Ao adicionar novos objetos de base de dados, siga a sequência:

1. Adicione novas tabelas ou colunas para o hub e a todos os membros de sincronização.
1. Adicione novas tabelas ou colunas ao esquema de sincronização.
1. Começa a inserir valores nas novas tabelas e colunas.

Para alterar o tipo de dados de uma coluna:

Quando altera o tipo de dados de uma coluna existente, sincronização de dados continua a funcionar, desde que os novos valores de acordo com o tipo de dados original, definido no esquema de sincronização. Por exemplo, se alterar o tipo de base de dados de origem **int** ao **bigint**, sincronização de dados continua a funcionar até que inserir um valor demasiado grande para o **int** tipo de dados. Para concluir a alteração, replicar manualmente a alteração de esquema, para o hub e a todos os membros de sincronização, em seguida, atualizar o esquema de sincronização.

**Como exportar e importar uma base de dados com a sincronização de dados?**

Depois de exportar uma base de dados como um *. bacpac* do ficheiro e importe-o para criar uma base de dados, efetue o seguinte procedimento para utilizar a sincronização de dados na nova base de dados:

1. Limpar os objetos de sincronização de dados e tabelas adicionais na base de dados nova, utilizando [este script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). O script elimina todos os objetos de sincronização de dados necessários da base de dados.
1. Recrie o grupo de sincronização com a nova base de dados. Se já não precisar do antigo grupo de sincronização, elimine-o.

**Onde posso encontrar informações sobre o agente do cliente?**

Para perguntas mais frequentes sobre o agente do cliente, consulte [FAQ do agente](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Passos Seguintes

Parabéns! Acabou de criar um grupo de sincronização que inclui uma instância de base de dados SQL e uma base de dados do SQL Server.

Para obter mais informações sobre a Sincronização de Dados SQL, veja:

- [Agente de sincronização de dados para sincronização de dados SQL do Azure](sql-database-data-sync-agent.md)
- [Melhores práticas](sql-database-best-practices-data-sync.md) e [como resolver problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)
- [Monitorizar a sincronização de dados SQL com os registos do Azure Monitor](sql-database-sync-monitor-oms.md)
- [Atualizar o esquema de sincronização com o Transact-SQL](sql-database-update-sync-schema.md) ou [PowerShell](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre a Base de Dados SQL, veja:

- [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
- [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
