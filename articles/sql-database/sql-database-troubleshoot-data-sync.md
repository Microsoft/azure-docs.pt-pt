---
title: Resolver problemas da Sincronização de Dados SQL
description: Saiba como resolver problemas comuns com o Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 8e223d0c6243bfddc1e5a56867c4c69de5e2a62e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822461"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Resolução de problemas de Sincronização de Dados SQL

Este artigo descreve como resolver problemas conhecidos com o Azure SQL Data Sync. Se houver uma resolução para um problema, é fornecida aqui.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure SQL Data Sync **não** suporta a Instância Gerida pela Base de Dados Azure SQL neste momento.

## <a name="sync-issues"></a>Problemas de sincronização

- [Sync falha no portal UI para bases de dados no local que estão associadas ao agente cliente](#sync-fails)

- [O meu grupo de sincronização está preso no estado de processamento.](#sync-stuck)

- [Vejo dados errados nas minhas tabelas](#sync-baddata)

- [Vejo dados primários inconsistentes após uma sincronização bem sucedida](#sync-pkdata)

- [Vejo uma degradação significativa no desempenho](#sync-perf)

- [Vejo esta mensagem: "Não posso inserir \<o valor NULO na coluna>. Coluna não permite nulos. O que isto significa, e como posso consertá-lo?](#sync-nulls)

- [Como é que o Data Sync lida com referências circulares? Isto é, quando os mesmos dados são sincronizados em múltiplos grupos de sincronização, e continua a mudar como resultado?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a>Sync falha no portal UI para bases de dados no local que estão associadas ao agente cliente

O Sync falha no portal SQL Data Sync UI para bases de dados no local que estão associadas ao agente cliente. No computador local que está a comandar o agente, vês erros de system.iO.iOException no Registo de Eventos. Os erros dizem que o disco tem espaço insuficiente.

- **Porque.** A unidade tem espaço insuficiente.

- **Resolução**. Crie mais espaço na unidade em que se situa o diretório %TEMP%.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a>O meu grupo de sincronização está preso no estado de processamento.

Um grupo de sincronização no SQL Data Sync está no estado de processamento há muito tempo. Não responde ao comando de **paragem,** e os registos não mostram novas entradas.

Qualquer uma das seguintes condições pode resultar em um grupo de sincronização ficar preso no estado de processamento:

- **Porque.** O agente do cliente está offline

- **Resolução**. Confirme que o agente do cliente está online e tente novamente.

- **Porque.** O agente do cliente está desinstalado ou em falta.

- **Resolução**. Se o agente do cliente estiver desinstalado ou em falta:

    1. Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.
    1. Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.

- **Porque.** O serviço Sincronização de Dados SQL está parado.

- **Resolução**. Reiniciar o serviço SQL Data Sync.

    1. No menu **Iniciar,** procure **Serviços.**
    1. Nos resultados da pesquisa, selecione **Serviços**.
    1. Encontre o serviço **SQL Data Sync.**
    1. Se o estado de serviço estiver **parado,** clique no nome do serviço e, em seguida, selecione **Iniciar**.

> [!NOTE]
> Se as informações anteriores não moverem o seu grupo de sincronização para fora do estado de processamento, o Microsoft Support pode redefinir o estado do seu grupo de sincronização. Para que o seu estado de grupo de sincronização seja reposto, no [fórum azure SQL Database, crie](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)um post. No post, inclua o seu ID de subscrição e o ID do grupo de sincronização para o grupo que precisa de ser reiniciado. Um engenheiro do Microsoft Support responderá ao seu post e informá-lo-á quando o estado foi reposto.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a>Vejo dados errados nas minhas tabelas

Se as tabelas que têm o mesmo nome mas que são de diferentes bases de dados estiverem incluídas numa sincronização, vê-se dados errados nas tabelas após a sincronização.

- **Porque.** O processo de fornecimento de Sincronização de Dados SQL utiliza as mesmas tabelas de rastreio para tabelas com o mesmo nome, mas que estão em esquemas diferentes. Por isso, as alterações de ambas as tabelas refletem-se na mesma tabela de rastreio. Isto provoca alterações erróneas de dados durante a sincronização.

- **Resolução**. Certifique-se de que os nomes das tabelas envolvidas numa sincronização são diferentes, mesmo que as tabelas pertençam a diferentes esquemas numa base de dados.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a>Vejo dados primários inconsistentes após uma sincronização bem sucedida

Uma sincronização é reportada como bem sucedida, e o registo não mostra nenhuma linha falhada ou ignorada, mas observa que os dados principais principais são inconsistentes entre as bases de dados do grupo de sincronização.

- **Porque.** Este resultado é por desígnio. As alterações em qualquer coluna principal de chaves resultam em dados inconsistentes nas linhas onde a chave principal foi alterada.

- **Resolução**. Para evitar este problema, certifique-se de que não são alterados dados numa coluna-chave primária. Para corrigir este problema depois de ter ocorrido, elimine a linha que tem dados inconsistentes de todos os pontos finais do grupo de sincronização. Em seguida, volte a inserir a linha.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a>Vejo uma degradação significativa no desempenho

O seu desempenho degrada-se significativamente, possivelmente ao ponto de nem sequer conseguir abrir o Data Sync UI.

- **Porque.** A causa mais provável é um loop de sincronização. Um loop de sincronização ocorre quando um sync pelo sync group A desencadeia uma sincronização pelo grupo B, que então desencadeia uma sincronização pelo grupo De sync. A situação real pode ser mais complexa, e pode envolver mais de dois grupos de sincronização no ciclo. A questão é que há um desencadeamento circular de sincronização que é causado por grupos de sincronização que se sobrepõem uns aos outros.

- **Resolução**. A melhor solução é a prevenção. Certifique-se de que não tem referências circulares nos seus grupos de sincronização. Qualquer linha sincronizada por um grupo de sincronização não pode ser sincronizada por outro grupo de sincronização.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a>Vejo esta mensagem: "Não posso inserir \<o valor NULO na coluna>. Coluna não permite nulos. O que isto significa, e como posso consertá-lo? 
Esta mensagem de erro indica que ocorreu um dos dois seguintes problemas:
-  Uma mesa não tem uma chave primária. Para corrigir este problema, adicione uma chave primária a todas as tabelas que está a sincronizar.
-  Há uma cláusula WHERE na sua declaração de CREATE INDEX. Data Sync não lida com esta condição. Para corrigir este problema, remova a cláusula WHERE ou faça manualmente as alterações em todas as bases de dados. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a>Como é que o Data Sync lida com referências circulares? Isto é, quando os mesmos dados são sincronizados em múltiplos grupos de sincronização, e continua a mudar como resultado?
Data Sync não lida com referências circulares. Certifique-se de evitá-los. 

## <a name="client-agent-issues"></a>Problemas com o agente cliente

Para resolver problemas com o agente cliente, consulte problemas de [Agente de Sincronização](sql-database-data-sync-agent.md#agent-tshoot)de Dados da Troubleshoot .

## <a name="setup-and-maintenance-issues"></a>Problemas de configuração e manutenção

- [Recebo uma mensagem de "disco fora do espaço".](#setup-space)

- [Não posso apagar o meu grupo de sincronização.](#setup-delete)

- [Não posso desregistar uma base de dados do SQL Server no local.](#setup-unreg)

- [Não tenho privilégios suficientes para começar os serviços do sistema.](#setup-perms)

- [Uma base de dados tem um estado "desatualizado"](#setup-date)

- [Um grupo de sincronização tem um estado "desatualizado"](#setup-date2)

- [Um grupo de sincronização não pode ser eliminado dentro de três minutos após desinstalar ou parar o agente](#setup-delete2)

- [O que acontece quando restauro uma base de dados perdida ou corrompida?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a>Recebo uma mensagem de "disco fora do espaço".

- **Porque.** A mensagem "disco fora do espaço" pode aparecer se os ficheiros restantes precisarem de ser eliminados. Isto pode ser causado por software antivírus, ou os ficheiros estão abertos quando se tenta a eliminação de operações.

- **Resolução**. Elimine manualmente os ficheiros de sincronização`del \*sync\* /s`que se encontram na pasta %temp% (). Em seguida, elimine os subdiretórios na pasta %temporário%.

> [!IMPORTANT]
> Não apague ficheiros enquanto a sincronização estiver em andamento.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a>Não posso apagar o meu grupo de sincronização.

A sua tentativa de apagar um grupo de sincronização falha. Qualquer um dos seguintes cenários pode resultar na não eliminação de um grupo de sincronização:

- **Porque.** O agente do cliente está offline.

- **Resolução**. Certifique-se de que o agente cliente está online e tente novamente.

- **Porque.** O agente do cliente está desinstalado ou em falta.

- **Resolução**. Se o agente do cliente estiver desinstalado ou em falta:  
    a. Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.  
    b. Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.

- **Porque.** Uma base de dados está offline.

- **Resolução**. Certifique-se de que as bases de dados SQL e as bases de dados do SQL Server estão todas online.

- **Porque.** O grupo de sincronização está a fornecer ou a sincronizar.

- **Resolução**. Aguarde até que o processo de fornecimento ou sincronização termine e, em seguida, retente apagar o grupo de sincronização.

### <a name="i-cant-unregister-an-on-premises-sql-server-database"></a><a name="setup-unreg"></a>Não posso desregistar uma base de dados do SQL Server no local.

- **Porque.** Muito provavelmente, está a tentar desregistar uma base de dados que já foi eliminada.

- **Resolução**. Para desregistar uma base de dados do Servidor SQL no local, selecione a base de dados e, em seguida, selecione **Force Delete**.

  Se esta operação não conseguir remover a base de dados do grupo de sincronização:

  1. Pare e, em seguida, reinicie o serviço de hospedar do agente cliente:  
    a. Selecione o menu **Iniciar.**  
    b. Na caixa de pesquisa, insira **serviços.msc**.  
    c. Na secção Programas do painel de **resultados** da pesquisa, clique duas vezes **serviços**.  
    d. Clique à direita no serviço **SQL Data Sync.**  
    e. Se o serviço está a funcionar, para.  
    f. Clique no serviço e, em seguida, selecione **Iniciar**.  
    g. Verifique se a base de dados ainda está registada. Se já não estiver registado, está feito. Caso contrário, proceda ao próximo passo.
  1. Abra a aplicação de agente cliente (SqlAzureDataSyncAgent).
  1. Selecione **Credenciais de Edição**e, em seguida, introduza as credenciais para a base de dados.
  1. Proceda à não inscrição.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a>Não tenho privilégios suficientes para começar os serviços do sistema.

- **Porque.** Este erro ocorre em duas situações:
  -   O nome do utilizador e/ou a palavra-passe estão incorretos.
  -   A conta de utilizador especificada não tem privilégios suficientes para iniciar sessão como serviço.

- **Resolução**. Conceda credenciais de log-on-as-a-service na conta de utilizador:

  1. Vá para **iniciar** > **ferramentas administrativas** > **do Painel** > de Controlo Política**de Segurança** > **Local** > **Gestão de Direitos dos Utilizadores.**
  1. Selecione **Iniciar sessão como serviço**.
  1. Na caixa de diálogo **Properties,** adicione a conta de utilizador.
  1. Selecione **Apply** (Aplicar) e **OK**.
  1. Feche todas as janelas.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a>Uma base de dados tem um estado "desatualizado"

- **Porque.** O SQL Data Sync remove bases de dados que estão offline do serviço há 45 dias ou mais (conforme contado a partir do momento em que a base de dados ficou offline). Se uma base de dados estiver offline durante 45 dias ou mais e depois voltar a funcionar, o seu estado é **desatualizado**.

- **Resolução**. Pode evitar um estado **dedesactualizado,** garantindo que nenhuma das suas bases de dados fique offline durante 45 dias ou mais.

  Se o estado de uma base de dados estiver **desatualizado:**

  1. Remova a base de dados que tem um estado **dedesactualizado** do grupo de sincronização.
  1. Adicione a base de dados de volta ao grupo de sincronização.

  > [!WARNING]
  > Perde-se todas as alterações feitas nesta base de dados enquanto estava offline.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a>Um grupo de sincronização tem um estado "desatualizado"

- **Porque.** Se uma ou mais alterações não se aplicarem durante todo o período de retenção de 45 dias, um grupo de sincronização pode ficar desatualizado.

- **Resolução**. Para evitar um estado **desatualizado** para um grupo de sincronização, examine regularmente os resultados dos seus trabalhos de sincronização no espectador de história. Investigue e resolva quaisquer alterações que não se apliquem.

  Se o estado de um grupo de sincronização estiver **desatualizado,** elimine o grupo de sincronização e recrie-o.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a>Um grupo de sincronização não pode ser eliminado dentro de três minutos após desinstalar ou parar o agente

Não é possível eliminar um grupo de sincronização no prazo de três minutos após a desinstalação ou paragem do agente cliente SQL Data Sync associado.

- **Resolução**.

  1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem on-line (recomendado).
  1. Se o agente estiver offline mas estiver instalado, leve-o on-line para o computador no local. Aguarde que o estado do agente apareça como **Online** no portal SQL Data Sync. Em seguida, retire o grupo de sincronização.
  1. Se o agente estiver offline porque foi desinstalado:  
    a.  Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.  
    b.  Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.  
    c. Tente apagar o grupo de sincronização.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a>O que acontece quando restauro uma base de dados perdida ou corrompida?

Se restaurar uma base de dados perdida ou corrompida a partir de uma cópia de segurança, pode haver uma não convergência de dados nos grupos de sincronização a que a base de dados pertence.

## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre o SQL Data Sync, consulte:

-   Visão geral - Sync dados em várias bases de dados de nuvem e no local com O Sincronizado de [Dados Azure SQL](sql-database-sync-data.md)
-   Configurar o Sincronizado de Dados
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o Servidor SQL no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Data Sync Agent for Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Boas práticas - [Boas práticas para O Sincronização de Dados Azure SQL](sql-database-best-practices-data-sync.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](sql-database-sync-monitor-oms.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação de alterações de esquemas no Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Com powerShell - [Use powerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para mais informações sobre a Base de Dados SQL, consulte:

-   [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
