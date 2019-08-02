---
title: Solucionar problemas do Azure Sincronização de Dados SQL | Microsoft Docs
description: Saiba como solucionar problemas comuns com o Azure Sincronização de Dados SQL.
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
ms.openlocfilehash: f1345c7de3ef56473b8ebd16cea20cfe76f0380e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566282"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>Solucionar problemas com Sincronização de Dados SQL

Este artigo descreve como solucionar problemas conhecidos com o Azure Sincronização de Dados SQL. Se houver uma resolução para um problema, ele será fornecido aqui.

Para obter uma descrição geral da Sincronização de Dados SQL, veja [Sincronizar dados em várias bases de dados na cloud e no local com a Sincronização de Dados SQL do Azure](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL não **oferece suporte a** instância gerenciada do banco de dados SQL do Azure no momento.

## <a name="sync-issues"></a>Problemas de sincronização

- [A sincronização falha na interface do usuário do portal para bancos de dados locais associados ao agente cliente](#sync-fails)

- [Meu grupo de sincronização está preso no estado de processamento](#sync-stuck)

- [Vejo dados incorretos em minhas tabelas](#sync-baddata)

- [Vejo dados de chave primária inconsistentes após uma sincronização bem-sucedida](#sync-pkdata)

- [Vejo uma degradação significativa no desempenho](#sync-perf)

- [Vejo esta mensagem: "Não é possível inserir o valor NULL na \<coluna de coluna >. A coluna não permite valores nulos. " O que isso significa e como posso corrigi-lo?](#sync-nulls)

- [Como a sincronização de dados lida com referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização e continuam sendo alterados como resultado?](#sync-circ)

### <a name="sync-fails"></a>A sincronização falha na interface do usuário do portal para bancos de dados locais associados ao agente cliente

A sincronização falha na interface do usuário do portal de Sincronização de Dados SQL para bancos de dados locais associados ao agente cliente. No computador local que está executando o agente, você verá erros de System. IO. IOException no log de eventos. Os erros dizem que o disco não tem espaço suficiente.

- **Causa**. A unidade não tem espaço suficiente.

- **Resolução**. Crie mais espaço na unidade em que o diretório% TEMP% está localizado.

### <a name="sync-stuck"></a>Meu grupo de sincronização está preso no estado de processamento

Um grupo de sincronização no Sincronização de Dados SQL esteve no estado de processamento por um longo tempo. Ele não responde ao comando **parar** e os logs não mostram novas entradas.

Qualquer uma das condições a seguir pode resultar em um grupo de sincronização paralisado no estado de processamento:

- **Causa**. O agente do cliente está offline

- **Resolução**. Confirme que o agente do cliente está online e tente novamente.

- **Causa**. O agente do cliente está desinstalado ou em falta.

- **Resolução**. Se o agente do cliente estiver desinstalado ou em falta:

    1. Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.
    1. Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.

- **Causa**. O serviço Sincronização de Dados SQL está parado.

- **Resolução**. Reinicie o serviço Sincronização de Dados SQL.

    1. No menu **Iniciar** , procure **Serviços**.
    1. Nos resultados da pesquisa, selecione **Serviços**.
    1. Localize o serviço **sincronização de dados SQL** .
    1. Se o status do serviço for **parado**, clique com o botão direito do mouse no nome do serviço e selecione **Iniciar**.

> [!NOTE]
> Se as informações anteriores não moverem o grupo de sincronização para fora do estado de processamento, Suporte da Microsoft poderá redefinir o status do seu grupo de sincronização. Para que o status do grupo de sincronização seja redefinido, no [Fórum do banco de dados SQL do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted), crie uma postagem. Na postagem, inclua sua ID de assinatura e a ID do grupo de sincronização para o grupo que precisa ser redefinido. Um engenheiro de Suporte da Microsoft responderá à sua postagem e permitirá que você saiba quando o status foi redefinido.

### <a name="sync-baddata"></a>Vejo dados incorretos em minhas tabelas

Se as tabelas que têm o mesmo nome, mas que são de esquemas de banco de dados diferentes forem incluídas em uma sincronização, você verá dados incorretos nas tabelas após a sincronização.

- **Causa**. O processo de provisionamento de Sincronização de Dados SQL usa as mesmas tabelas de acompanhamento para tabelas que têm o mesmo nome, mas que estão em esquemas diferentes. Por isso, as alterações de ambas as tabelas são refletidas na mesma tabela de acompanhamento. Isso causa alterações de dados incorretas durante a sincronização.

- **Resolução**. Certifique-se de que os nomes das tabelas envolvidas em uma sincronização sejam diferentes, mesmo que as tabelas pertençam a esquemas diferentes em um banco de dados.

### <a name="sync-pkdata"></a>Vejo dados de chave primária inconsistentes após uma sincronização bem-sucedida

Uma sincronização é relatada como bem-sucedida e o log não mostra nenhuma linha com falha ou ignorada, mas você observa que os dados de chave primária estão inconsistentes entre os bancos de dado no grupo de sincronização.

- **Causa**. Esse resultado é por design. As alterações em qualquer coluna de chave primária resultam em dados inconsistentes nas linhas em que a chave primária foi alterada.

- **Resolução**. Para evitar esse problema, certifique-se de que nenhum dado em uma coluna de chave primária seja alterado. Para corrigir esse problema depois de ter ocorrido, exclua a linha que tem dados inconsistentes de todos os pontos de extremidade no grupo de sincronização. Em seguida, reinsira a linha.

### <a name="sync-perf"></a>Vejo uma degradação significativa no desempenho

O desempenho é prejudicado significativamente, possivelmente para o ponto em que você não pode até abrir a interface de usuário da sincronização de dados.

- **Causa**. A causa mais provável é um loop de sincronização. Um loop de sincronização ocorre quando uma sincronização por grupo de sincronização A dispara uma sincronização pelo grupo de sincronização B, que dispara uma sincronização pelo grupo de sincronização A. A situação real pode ser mais complexa e pode envolver mais de dois grupos de sincronização no loop. O problema é que há um disparo circular de sincronização causado por grupos de sincronização sobrepostos uns aos outros.

- **Resolução**. A melhor correção é a prevenção. Verifique se você não tem referências circulares em seus grupos de sincronização. Qualquer linha sincronizada por um grupo de sincronização não pode ser sincronizada por outro grupo de sincronização.

### <a name="sync-nulls"></a>Vejo esta mensagem: "Não é possível inserir o valor NULL na \<coluna de coluna >. A coluna não permite valores nulos. " O que isso significa e como posso corrigi-lo? 
Essa mensagem de erro indica que um dos dois problemas a seguir ocorreu:
-  Uma tabela não tem uma chave primária. Para corrigir esse problema, adicione uma chave primária a todas as tabelas que você está sincronizando.
-  Há uma cláusula WHERE em sua instrução CREATE INDEX. A sincronização de dados não lida com essa condição. Para corrigir esse problema, remova a cláusula WHERE ou faça as alterações manualmente em todos os bancos de dados. 
 
### <a name="sync-circ"></a>Como a sincronização de dados lida com referências circulares? Ou seja, quando os mesmos dados são sincronizados em vários grupos de sincronização e continuam sendo alterados como resultado?
A sincronização de dados não lida com referências circulares. Não se esqueça de evitá-los. 

## <a name="client-agent-issues"></a>Problemas do agente cliente

Para solucionar problemas com o agente cliente, consulte [solucionar problemas do agente de sincronização de dados](sql-database-data-sync-agent.md#agent-tshoot).

## <a name="setup-and-maintenance-issues"></a>Problemas de instalação e manutenção

- [Recebo uma mensagem de "disco sem espaço"](#setup-space)

- [Não consigo excluir meu grupo de sincronização](#setup-delete)

- [Não consigo cancelar o registro de um banco de dados SQL Server local](#setup-unreg)

- [Não tenho privilégios suficientes para iniciar os serviços do sistema](#setup-perms)

- [Um banco de dados tem um status "desatualizado"](#setup-date)

- [Um grupo de sincronização tem um status "desatualizado"](#setup-date2)

- [Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente](#setup-delete2)

- [O que acontece quando eu restauro um banco de dados perdido ou corrompido?](#setup-restore)

### <a name="setup-space"></a>Recebo uma mensagem de "disco sem espaço"

- **Causa**. A mensagem "disco sem espaço" pode aparecer se arquivos restantes precisarem ser excluídos. Isso pode ser causado por software antivírus, ou arquivos abertos quando são tentadas operações de exclusão.

- **Resolução**. Exclua manualmente os arquivos de sincronização que estão na pasta% temp%`del \*sync\* /s`(). Em seguida, exclua os subdiretórios na pasta% Temp%.

> [!IMPORTANT]
> Não exclua nenhum arquivo enquanto a sincronização estiver em andamento.

### <a name="setup-delete"></a>Não consigo excluir meu grupo de sincronização

Falha ao tentar excluir um grupo de sincronização. Qualquer um dos cenários a seguir pode resultar em falha ao excluir um grupo de sincronização:

- **Causa**. O agente do cliente está offline.

- **Resolução**. Verifique se o agente cliente está online e tente novamente.

- **Causa**. O agente do cliente está desinstalado ou em falta.

- **Resolução**. Se o agente do cliente estiver desinstalado ou em falta:  
    a. Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.  
    b. Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.

- **Causa**. Um banco de dados está offline.

- **Resolução**. Verifique se os bancos de dados SQL e os bancos de dados SQL Server estão todos online.

- **Causa**. O grupo de sincronização está Provisionando ou sincronizando.

- **Resolução**. Aguarde até que o processo de provisionamento ou de sincronização seja concluído e, em seguida, repita a exclusão do grupo de sincronização.

### <a name="setup-unreg"></a>Não consigo cancelar o registro de um banco de dados SQL Server local

- **Causa**. Provavelmente, você está tentando cancelar o registro de um banco de dados que já foi excluído.

- **Resolução**. Para cancelar o registro de um banco de dados SQL Server local, selecione o banco de dados e, em seguida, selecione **forçar exclusão**.

  Se essa operação falhar ao remover o banco de dados do grupo de sincronização:

  1. Pare e reinicie o serviço host do agente do cliente:  
    a. Selecione o menu **Iniciar** .  
    b. Na caixa de pesquisa, digite **Services. msc**.  
    c. Na seção **programas** do painel de resultados da pesquisa, clique duas vezes em **Serviços**.  
    d. Clique com o botão direito do mouse no serviço **sincronização de dados SQL** .  
    e. Se o serviço estiver em execução, interrompa-o.  
    f. Clique com o botão direito do mouse no serviço e selecione **Iniciar**.  
    g. Verifique se o banco de dados ainda está registrado. Se ele não estiver mais registrado, você terminará. Caso contrário, continue com a próxima etapa.
  1. Abra o aplicativo agente cliente (SqlAzureDataSyncAgent).
  1. Selecione **Editar credenciais**e, em seguida, insira as credenciais para o banco de dados.
  1. Continue com o cancelamento de registro.

### <a name="setup-perms"></a>Não tenho privilégios suficientes para iniciar os serviços do sistema

- **Causa**. Esse erro ocorre em duas situações:
  -   O nome de usuário e/ou a senha estão incorretos.
  -   A conta de usuário especificada não tem privilégios suficientes para fazer logon como um serviço.

- **Resolução**. Conceda credenciais de logon como serviço à conta de usuário:

  1. Vá para **Iniciar** > **painel** > decontrole > ferramentas administrativaspolítica > de**segurança local**usuário**política local**Rights Management. > 
  1. Selecione **fazer logon como um serviço**.
  1. Na caixa de diálogo **Propriedades** , adicione a conta de usuário.
  1. Selecione **Apply** (Aplicar) e **OK**.
  1. Feche todas as janelas.

### <a name="setup-date"></a>Um banco de dados tem um status "desatualizado"

- **Causa**. Sincronização de Dados SQL remove os bancos de dados que ficaram offline do serviço por 45 dias ou mais (contados a partir do momento em que o banco de dados ficou offline). Se um banco de dados estiver offline por 45 dias ou mais e voltar a ficar online, seu **status estará**desatualizado.

- **Resolução**. Você pode **evitar um status** desatualizado garantindo que nenhum dos bancos de dados fique offline por 45 dias ou mais.

  Se o status de um banco de **dados estiver**desatualizado:

  1. Remova o banco de dados que **tem um status** desatualizado do grupo de sincronização.
  1. Adicione o banco de dados de volta ao grupo de sincronização.

  > [!WARNING]
  > Você perde todas as alterações feitas nesse banco de dados enquanto ele estava offline.

### <a name="setup-date2"></a>Um grupo de sincronização tem um status "desatualizado"

- **Causa**. Se uma ou mais alterações não puderem ser aplicadas durante todo o período de retenção de 45 dias, um grupo de sincronização poderá ficar desatualizado.

- **Resolução**. Para evitar um **status** desatualizado para um grupo de sincronização, examine regularmente os resultados de seus trabalhos de sincronização no Visualizador de histórico. Investigue e resolva as alterações que não forem aplicadas.

  Se o status de um grupo de **sincronização estiver**desatualizado, exclua o grupo de sincronização e recrie-o.

### <a name="setup-delete2"></a>Um grupo de sincronização não pode ser excluído em até três minutos após a desinstalação ou interrupção do agente

Você não pode excluir um grupo de sincronização em até três minutos após a desinstalação ou interrupção do agente cliente do Sincronização de Dados SQL associado.

- **Resolução**.

  1. Remova um grupo de sincronização enquanto os agentes de sincronização associados estiverem online (recomendado).
  1. Se o agente estiver offline, mas estiver instalado, coloque-o online no computador local. Aguarde até que o status do agente apareça como **online** no portal de sincronização de dados SQL. Em seguida, remova o grupo de sincronização.
  1. Se o agente estiver offline porque ele foi desinstalado:  
    a.  Remova o ficheiro XML do agente da pasta de instalação da Sincronização de Dados SQL, se o ficheiro existir.  
    b.  Instale o agente num computador no local (pode ser o mesmo computador ou um diferente). Em seguida, submeta a chave do agente que é gerada no portal relativa ao agente que aparece offline.  
    c. Tente excluir o grupo de sincronização.

### <a name="setup-restore"></a>O que acontece quando eu restauro um banco de dados perdido ou corrompido?

Se você restaurar um banco de dados perdido ou corrompido a partir de um backup, poderá haver uma não convergência entre os grupos de sincronização aos quais o banco de dado pertence.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre Sincronização de Dados SQL, consulte:

-   Visão geral – [sincronizar dados entre vários bancos de dados locais e de nuvem com o Azure sincronização de dados SQL](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal- [tutorial: Configurar o Sincronização de Dados SQL para sincronizar dados entre o Azure SQL Database e o SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de - de sincronização de dados [agente de sincronização de dados SQL do Azure de sincronização de dados](sql-database-data-sync-agent.md)
-   Práticas recomendadas- [práticas recomendadas para o Azure sincronização de dados SQL](sql-database-best-practices-data-sync.md)
-   Monitorar [sincronização de dados SQL monitorar com Azure monitor logs](sql-database-sync-monitor-oms.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL – [Automatize a replicação de alterações de esquema no Azure sincronização de dados SQL](sql-database-update-sync-schema.md)
    -   Com o PowerShell- [use o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)

Para obter mais informações sobre o banco de dados SQL, consulte:

-   [Descrição Geral da Base de Dados SQL](sql-database-technical-overview.md)
-   [Gestão do Ciclo de Vida da Base de Dados](https://msdn.microsoft.com/library/jj907294.aspx)
