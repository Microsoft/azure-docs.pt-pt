---
title: Resolver problemas da Sincronização de Dados SQL
description: Aprenda a identificar, resolver problemas e resolver problemas comuns com o SQL Data Sync em Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: 02eaec4c86c934e8d2638de1b60aa9267babf7a8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790173"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Resolução de problemas de Sincronização de Dados SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve como resolver problemas conhecidos com SQL Data Sync em Azure. Se há uma resolução para um problema, é fornecida aqui.

Para obter uma visão geral do SQL Data Sync, consulte [os dados do Sync em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync **não** suporta a Azure SQL Managed Instance neste momento.

## <a name="sync-issues"></a>Problemas de sincronização

- [O Sync falha no portal UI para bases de dados no local que estão associadas ao agente cliente](#sync-fails)

- [O meu grupo de sincronização está preso no estado de processamento.](#sync-stuck)

- [Vejo dados errados nas minhas tabelas](#sync-baddata)

- [Vejo dados primários inconsistentes após uma sincronização bem sucedida](#sync-pkdata)

- [Vejo uma degradação significativa no desempenho](#sync-perf)

- [Vejo esta mensagem: "Não é possível inserir o valor NU NA coluna \<column> . Coluna não permite nulos. O que significa isto, e como posso consertá-lo?](#sync-nulls)

- [Como é que o Data Sync lida com referências circulares? Isto é, quando os mesmos dados são sincronizados em vários grupos de sincronização, e continua a mudar como resultado?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> O Sync falha no portal UI para bases de dados no local que estão associadas ao agente cliente

O Sync falha no portal SQL Data Sync UI para bases de dados no local que estão associadas ao agente cliente. No computador local que está a executar o agente, vê-se erros de system.iO.IOException no Registo de Eventos. Os erros dizem que o disco tem espaço insuficiente.

- **Porque.** A unidade tem espaço insuficiente.

- **Resolução** . Crie mais espaço na unidade em que se encontra o diretório %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> O meu grupo de sincronização está preso no estado de processamento.

Um grupo de sincronização no SQL Data Sync está no estado de processamento há muito tempo. Não responde ao comando **de paragem,** e os registos não mostram novas entradas.

Qualquer uma das seguintes condições pode resultar em que um grupo de sincronização fique preso no estado de processamento:

- **Porque.** O agente do cliente está offline

- **Resolução** . Confirme que o agente do cliente está online e tente novamente.

- **Porque.** O agente do cliente está desinstalado ou em falta.

- **Resolução** . Se o agente do cliente estiver desinstalado ou em falta:

    1. Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.
    1. Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.

- **Porque.** O serviço Sincronização de Dados SQL está parado.

- **Resolução** . Reinicie o serviço SQL Data Sync.

    1. No menu **Iniciar,** procure **serviços.**
    1. Nos resultados da pesquisa, selecione **Serviços.**
    1. Encontre o serviço **SQL Data Sync.**
    1. Se o estado de serviço estiver **parado,** clique com o nome do serviço à direita e, em seguida, selecione **Iniciar** .

> [!NOTE]
> Se as informações anteriores não tirarem o seu grupo de sincronização do estado de processamento, o Microsoft Support pode redefinir o estado do seu grupo de sincronização. Para que o seu estado de grupo de sincronização seja reiniciado, na [página de perguntas do Microsoft Q&A página de perguntas para Azure SQL Database,](/answers/topics/azure-sql-database.html)crie um post. No post, inclua o seu ID de subscrição e o ID do grupo de sincronização para o grupo que precisa de ser reiniciado. Um engenheiro de Suporte microsoft responderá ao seu post e informá-lo-á quando o estado tiver sido reiniciado.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> Vejo dados errados nas minhas tabelas

Se as tabelas com o mesmo nome mas que são de diferentes esquemas de base de dados estiverem incluídas numa sincronização, vê dados errados nas tabelas após a sincronização.

- **Porque.** O processo de provisionamento de sincronização de dados SQL utiliza as mesmas tabelas de rastreio para tabelas com o mesmo nome mas que estão em esquemas diferentes. Por isso, as mudanças de ambas as tabelas refletem-se na mesma tabela de rastreio. Isto causa alterações de dados erróneas durante a sincronização.

- **Resolução** . Certifique-se de que os nomes das tabelas envolvidas numa sincronização são diferentes, mesmo que as tabelas pertençam a esquemas diferentes numa base de dados.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> Vejo dados primários inconsistentes após uma sincronização bem sucedida

Uma sincronização é reportada como bem sucedida, e o registo não mostra linhas falhadas ou ignoradas, mas observa que os dados de chave primárias são inconsistentes entre as bases de dados do grupo de sincronização.

- **Porque.** Este resultado é por design. As alterações em qualquer coluna-chave primária resultam em dados inconsistentes nas linhas onde a chave primária foi alterada.

- **Resolução** . Para evitar este problema, certifique-se de que nenhum dado numa coluna-chave primária é alterado. Para corrigir este problema depois de ocorrer, elimine a linha que tem dados inconsistentes de todos os pontos finais do grupo de sincronização. Então, reinserir a fileira.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> Vejo uma degradação significativa no desempenho

O seu desempenho degrada-se significativamente, possivelmente ao ponto de nem sequer conseguir abrir a UI do Data Sync.

- **Porque.** A causa mais provável é um ciclo de sincronização. Um ciclo de sincronização ocorre quando uma sincronização do grupo A desencadeia uma sincronização pelo grupo B sincronizado, que depois aciona uma sincronização pelo grupo A sincronizado. A situação real pode ser mais complexa, e pode envolver mais de dois grupos de sincronização. A questão é que há um desencadeamento circular de sincronização que é causado por grupos sincronizados que se sobrepõem uns aos outros.

- **Resolução** . A melhor solução é a prevenção. Certifique-se de que não tem referências circulares nos seus grupos de sincronização. Qualquer linha sincronizada por um grupo de sincronização não pode ser sincronizada por outro grupo de sincronização.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> Vejo esta mensagem: "Não é possível inserir o valor NU NA coluna \<column> . Coluna não permite nulos. O que significa isto, e como posso consertá-lo? 
Esta mensagem de erro indica que ocorreu um dos dois seguintes problemas:
-  Uma mesa não tem a chave primária. Para corrigir este problema, adicione uma chave primária a todas as tabelas que está a sincronizar.
-  Há uma cláusula WHERE na sua declaração DE ÍNDICE CREATE. Data Sync não lida com esta condição. Para corrigir este problema, remova a cláusula WHERE ou faça manualmente as alterações em todas as bases de dados. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> Como é que o Data Sync lida com referências circulares? Isto é, quando os mesmos dados são sincronizados em vários grupos de sincronização, e continua a mudar como resultado?
O Data Sync não lida com referências circulares. Certifique-se de evitá-los. 

## <a name="client-agent-issues"></a>Problemas com o agente de clientes

Para resolver problemas com o agente cliente, consulte [problemas de Agente de Sincronização de Dados de Resolução de Problemas](sql-data-sync-agent-overview.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problemas de instalação e manutenção

- [Recebo uma mensagem de "disco fora do espaço".](#setup-space)

- [Não posso apagar o meu grupo de sincronização.](#setup-delete)

- [Não posso desregistralmente uma base de dados do SQL Server](#setup-unreg)

- [Não tenho privilégios suficientes para iniciar serviços de sistema.](#setup-perms)

- [Uma base de dados tem um estado "Desatualizado"](#setup-date)

- [Um grupo de sincronização tem um estado "Desatualizado"](#setup-date2)

- [Um grupo de sincronização não pode ser eliminado dentro de três minutos após desinstalar ou parar o agente](#setup-delete2)

- [O que acontece quando restaurar uma base de dados perdida ou corrompida?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> Recebo uma mensagem de "disco fora do espaço".

- **Porque.** A mensagem "disco fora do espaço" pode aparecer se os ficheiros restantes precisarem de ser apagados. Isto pode ser causado por software antivírus ou os ficheiros são abertos quando as operações de eliminação são tentadas.

- **Resolução** . Elimine manualmente os ficheiros de sincronização que se encontram na pasta %temp% `del \*sync\* /s` (). Em seguida, elimine as subdiretas na pasta %temperatura.

> [!IMPORTANT]
> Não elimine quaisquer ficheiros enquanto a sincronização estiver em curso.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> Não posso apagar o meu grupo de sincronização.

A sua tentativa de eliminar um grupo de sincronização falha. Qualquer um dos seguintes cenários pode resultar na não eliminação de um grupo de sincronização:

- **Porque.** O agente do cliente está offline.

- **Resolução** . Certifique-se de que o agente cliente está on-line e, em seguida, tente novamente.

- **Porque.** O agente do cliente está desinstalado ou em falta.

- **Resolução** . Se o agente do cliente estiver desinstalado ou em falta:  
    a. Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.  
    b. Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.

- **Porque.** Uma base de dados está desligada.

- **Resolução** . Certifique-se de que as suas bases de dados estão todas online.

- **Porque.** O grupo de sincronização está a provisão ou sincronização.

- **Resolução** . Aguarde até que o processo de provisão ou sincronização termine e, em seguida, recandiduça o grupo de sincronização.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a> Não posso desregistralmente uma base de dados do SQL Server

- **Porque.** Provavelmente, está a tentar desagregá-lo numa base de dados que já foi eliminada.

- **Resolução** . Para desregistralr uma base de dados do SQL Server, selecione a base de dados e, em seguida, selecione **Force Delete** .

  Se esta operação não remover a base de dados do grupo de sincronização:

  1. Pare e, em seguida, reinicie o serviço de anfitrião do agente cliente:  
    a. Selecione o menu **Iniciar.**  
    b. Na caixa de pesquisa, **insira o serviço.msc** .  
    c. Na secção Programas do painel de **resultados** de pesquisa, clique duplo **Em Serviços** .  
    d. Clique com o botão direito no serviço **SQL Data Sync.**  
    e. Se o serviço estiver a funcionar, para.  
    f. Clique com o botão direito no serviço e, em seguida, **selecione Start** .  
    exemplo, Verifique se a base de dados ainda está registada. Se já não estiver registado, está feito. Caso contrário, prossiga com o próximo passo.
  1. Abra a aplicação de agente cliente (SqlAzureDataSyncAgent).
  1. Selecione **'Credenciais de Edição'** e, em seguida, introduza as credenciais para a base de dados.
  1. Proceda com a não inscrição.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> Não tenho privilégios suficientes para iniciar serviços de sistema.

- **Porque.** Este erro ocorre em duas situações:
  -   O nome de utilizador e/ou a palavra-passe estão incorretos.
  -   A conta de utilizador especificada não tem privilégios suficientes para iniciar sessão como um serviço.

- **Resolução** . Conceder credenciais de login na conta do utilizador:

  1. Vá para **iniciar o** painel  >  **de controlo**  >  **Ferramentas Administrativas**  >  **Ferramentas Locais de Segurança** Local Política Local  >  **Política**  >  **De Direitos do Utilizador** .
  1. Selecione **Iniciar sessão como um serviço** .
  1. Na caixa de diálogo **Properties,** adicione a conta de utilizador.
  1. Selecione **Apply** (Aplicar) e **OK** .
  1. Feche todas as janelas.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> Uma base de dados tem um estado "Desatualizado"

- **Porque.** O SQL Data Sync remove bases de dados que estiveram offline do serviço durante 45 dias ou mais (como contado a partir do momento em que a base de dados ficou offline). Se uma base de dados estiver offline durante 45 dias ou mais e voltar a estar online, o seu estado está **desatualizado** .

- **Resolução** . Pode evitar um **estado desatualizado,** garantindo que nenhuma das suas bases de dados fique offline durante 45 dias ou mais.

  Se o estado de uma base de dados estiver **desatualizado:**

  1. Remova a base de dados que tem um estado **desatualizado** do grupo de sincronização.
  1. Adicione a base de dados de volta ao grupo de sincronização.

  > [!WARNING]
  > Perde-se todas as alterações feitas nesta base de dados enquanto estava offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> Um grupo de sincronização tem um estado "Desatualizado"

- **Porque.** Se uma ou mais alterações não se aplicarem durante todo o período de retenção de 45 dias, um grupo de sincronização pode ficar desatualizado.

- **Resolução** . Para evitar um **estado desatualizado** para um grupo de sincronização, examine regularmente os resultados dos seus trabalhos de sincronização no visualizador de história. Investigue e resolva quaisquer alterações que não se apliquem.

  Se o estado de um grupo de sincronização estiver **desatualizado,** elimine o grupo de sincronização e, em seguida, re-crie-o.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> Um grupo de sincronização não pode ser eliminado dentro de três minutos após desinstalar ou parar o agente

Não é possível eliminar um grupo de sincronização no prazo de três minutos após a desinstalação ou paragem do agente cliente SQL Data Sync associado.

- **Resolução** .

  1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem on-line (recomendado).
  1. Se o agente estiver offline mas estiver instalado, leve-o on-line no computador no local. Aguarde que o estado do agente apareça como **Online** no portal SQL Data Sync. Em seguida, remova o grupo de sincronização.
  1. Se o agente estiver desligado porque foi desinstalado:  
    a.  Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.  
    b.  Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.  
    c. Tente eliminar o grupo de sincronização.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> O que acontece quando restaurar uma base de dados perdida ou corrompida?

Se restaurar uma base de dados perdida ou corrompida a partir de uma cópia de segurança, pode haver uma não convergência de dados nos grupos de sincronização a que a base de dados pertence.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o SQL Data Sync, consulte:

-   Visão geral - [Sync dados em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar o Data Sync
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados SQL do Azure e o SqL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados num caso de Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for SQL Data Sync in Azure](sql-data-sync-agent-overview.md)
-   Melhores práticas - [Melhores práticas para SQL Data Sync em Azure](sql-data-sync-best-practices.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](./monitor-tune-overview.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação de alterações de esquema no SQL Data Sync em Azure](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [Use o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)

Para obter mais informações sobre a Base de Dados SQL, consulte:

-   [Descrição Geral da Base de Dados SQL](sql-database-paas-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))