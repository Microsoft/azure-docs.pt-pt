---
title: Monitorar a atividade de migração-serviço de migração de banco de dados do Azure
description: Saiba como usar o serviço de migração de banco de dados do Azure para monitorar a atividade de migração.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: b3ba634ddb084b5637d0a0c97c0ac4ff72193c1d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437938"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorar a atividade de migração usando o serviço de migração de banco de dados do Azure
Neste artigo, você aprenderá a monitorar o progresso de uma migração no nível de banco de dados e em um nível de tabela.

## <a name="monitor-at-the-database-level"></a>Monitorar no nível de banco de dados
Para monitorar a atividade no nível de banco de dados, exiba a folha de nível de banco de dados:

![Folha de nível de banco de dados](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Selecionar o hiperlink do banco de dados mostrará a lista de tabelas e o andamento da migração.

A tabela a seguir lista os campos na folha de nível de banco de dados e descreve os vários valores de status associados a cada um.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nome do campo</strong></th>
      <th><strong>Substatus do campo</strong></th>
      <th><strong>Descrição</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Status da atividade</strong></td>
      <td>A executar</td>
      <td>A atividade de migração está em execução.</td>
    </tr>
    <tr>
      <td>Bem-sucedido</td>
      <td>Atividade de migração bem-sucedida sem problemas.</td>
    </tr>
    <tr>
      <td>Com falha</td>
      <td>Falha na migração. Selecione o link ' Ver detalhes do erro ' em detalhes da migração para a mensagem de erro completa.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Estado</strong></td>
      <td>A inicializar</td>
      <td>DMS está configurando o pipeline de migração.</td>
    </tr>
    <tr>
      <td>A executar</td>
      <td>O pipeline DMS está em execução e executando a migração.</td>
    </tr>
    <tr>
      <td>Completo</td>
      <td>Migração concluída.</td>
    </tr>
    <tr>
      <td>Com Falhas</td>
      <td>Falha na migração. Clique em detalhes de migração para ver os erros de migração.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Detalhes da migração</strong></td>
      <td>Iniciando o pipeline de migração</td>
      <td>DMS está configurando o pipeline de migração.</td>
    </tr>
    <tr>
      <td>Carregamento de dados completo em andamento</td>
      <td>O DMS está executando a carga inicial.</td>
    </tr>
    <tr>
      <td>Pronto para a transferência</td>
      <td>Após a conclusão do carregamento inicial, o DMS marcará o banco de dados como pronto para a transferência. O usuário deve verificar se os dados foram capturados na sincronização contínua.</td>
    </tr>
    <tr>
      <td>Todas as alterações aplicadas</td>
      <td>A carga inicial e a sincronização contínua estão concluídas. Esse status também ocorre depois que o banco de dados é transtransferência com êxito.</td>
    </tr>
    <tr>
      <td>Consulte os detalhes do erro</td>
      <td>Clique no link para mostrar os detalhes do erro.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Permanência</strong></td>
      <td>N/A</td>
      <td>Tempo total da atividade de migração que está sendo inicializada para a migração concluída ou falha na migração.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitorar no nível de tabela – resumo rápido
Para monitorar a atividade no nível de tabela, exiba a folha de nível de tabela. A parte superior da folha mostra o número detalhado de linhas migradas em carga completa e atualizações incrementais. 

A parte inferior da folha lista as tabelas e mostra um resumo rápido do progresso da migração.

![Folha de nível de tabela-resumo rápido](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

A tabela a seguir descreve os campos mostrados nos detalhes de nível de tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Carregamento completo concluído**      | O número de tabelas concluiu o carregamento de dados completo. |
| **Carga total na fila**      | Número de tabelas sendo enfileiradas para carregamento completo.      |
| **Carregamento de carga completo** | Número de tabelas com falha.      |
| **Atualizações incrementais**      | Número de atualizações de captura de dados de alteração (CDC) em linhas aplicadas ao destino. |
| **Inserções incrementais**      | Número de inserções de CDC em linhas aplicadas ao destino.      |
| **Exclusões incrementais** | Número de exclusões de CDC em linhas aplicadas ao destino.      |
| **Alterações pendentes**      | Número de CDC em linhas que ainda estão aguardando para serem aplicadas ao destino. |
| **Alterações aplicadas**      | Total de atualizações, inserções e exclusões de CDC em linhas aplicadas ao destino.      |
| **Tabelas no estado de erro** | Número de tabelas que estão no estado ' erro ' durante a migração. Alguns exemplos que as tabelas podem entrar em estado de erro são quando há duplicatas identificadas no destino ou os dados não são carregados no carregamento da tabela de destino.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitorar no nível de tabela – resumo detalhado
Há duas guias que mostram o progresso da migração na carga completa e na sincronização de dados incremental.
    
![Guia de carregamento completo](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Guia sincronização de dados incremental](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

A tabela a seguir descreve os campos mostrados no progresso da migração em nível de tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Sincronização de status**      | A sincronização contínua está em execução. |
| **Inserido**      | Número de inserções de CDC em linhas aplicadas ao destino.      |
| **Atualização** | Número de atualizações de CDC em linhas aplicadas ao destino.      |
| **Eliminar**      | Número de exclusões de CDC em linhas aplicadas ao destino. |
| **Total aplicado**      | Total de atualizações, inserções e exclusões de CDC em linhas aplicadas ao destino. |
| **Erros de dados** | Número de erros de dados ocorridos nesta tabela. Alguns exemplos dos erros são *511: não é possível criar uma linha de tamanho% d que seja maior que o tamanho máximo permitido de% d, 8114: erro ao converter o tipo de dados% ls em% ls.*  O cliente deve consultar dms_apply_exceptions tabela no destino do Azure para ver os detalhes do erro.    |

> [!NOTE]
> Os valores de CDC de Insert, Update e DELETE e total aplicado podem diminuir quando o banco de dados é de transferência ou a migração é reiniciada.

## <a name="next-steps"></a>Passos seguintes
- Examine as diretrizes de migração no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.