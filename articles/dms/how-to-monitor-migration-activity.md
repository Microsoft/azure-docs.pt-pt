---
title: Monitorizar a atividade de migração - Serviço de Migração de Bases de Dados Azure
description: Aprenda a utilizar o Serviço de Migração de Bases de Dados Azure para monitorizar a atividade migratória.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 31b49cdd9e0e5569981b2a0b0c6efcab7239e019
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77648517"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorizar a atividade de migração utilizando o Serviço de Migração da Base de Dados Azure
Neste artigo, aprende-se a acompanhar o progresso de uma migração tanto a nível de base de dados como a nível de tabela.

## <a name="monitor-at-the-database-level"></a>Monitor ao nível da base de dados
Para monitorizar a atividade ao nível da base de dados, consulte a lâmina de nível de base de dados:

![Lâmina de nível de base de dados](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> A seleção da hiperligação da base de dados mostrar-lhe-á a lista de tabelas e o seu progresso migratório.

A tabela seguinte lista os campos na lâmina de nível de base de dados e descreve os vários valores de estado associados a cada um.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nome do campo</strong></th>
      <th><strong>Subestatuto de campo</strong></th>
      <th><strong>Descrição</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Estado da atividade</strong></td>
      <td>A executar</td>
      <td>A atividade migratória está a decorrer.</td>
    </tr>
    <tr>
      <td>Bem-sucedido</td>
      <td>A atividade migratória foi bem sucedida sem problemas.</td>
    </tr>
    <tr>
      <td>Defeituoso</td>
      <td>A migração falhou. Selecione o link 'Ver detalhes de erro' sob os detalhes da migração para obter a mensagem de erro completa.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Estado</strong></td>
      <td>A inicializar</td>
      <td>O DMS está a montar o oleoduto de migração.</td>
    </tr>
    <tr>
      <td>A executar</td>
      <td>O oleoduto DMS está a funcionar e a realizar migração.</td>
    </tr>
    <tr>
      <td>Concluída</td>
      <td>Migração concluída.</td>
    </tr>
    <tr>
      <td>Falhou</td>
      <td>A migração falhou. Clique em detalhes da migração para ver erros de migração.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Detalhes de migração</strong></td>
      <td>Início do gasoduto de migração</td>
      <td>O DMS está a montar o oleoduto de migração.</td>
    </tr>
    <tr>
      <td>Carga completa de dados em curso</td>
      <td>O DMS está a executar a carga inicial.</td>
    </tr>
    <tr>
      <td>Pronto para Cutover</td>
      <td>Após a carga inicial ser concluída, o DMS marcará a base de dados como pronto para o corte. O utilizador deve verificar se os dados foram apanhados em sincronização contínua.</td>
    </tr>
    <tr>
      <td>Todas as alterações aplicadas</td>
      <td>A carga inicial e a sincronização contínua estão completas. Este estado também ocorre após a redução da base de dados com sucesso.</td>
    </tr>
    <tr>
      <td>Ver detalhes de erro</td>
      <td>Clique no link para mostrar detalhes de erro.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duração</strong></td>
      <td>N/D</td>
      <td>O tempo total da atividade migratória foi inicializado para a migração concluída ou a migração falhou.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitor ao nível da mesa - Resumo Rápido
Para monitorizar a atividade ao nível da mesa, veja a lâmina ao nível da mesa. A parte superior da lâmina mostra o número detalhado de linhas migradas em carga total e atualizações incrementais. 

A parte inferior da lâmina lista as tabelas e mostra um resumo rápido do progresso migratório.

![Lâmina de nível de mesa - resumo rápido](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

A tabela seguinte descreve os campos mostrados nos detalhes do nível da tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Carga completa concluída**      | Número de tabelas completada carga completa de dados. |
| **Carga completa em fila**      | Número de mesas em fila para carga completa.      |
| **Carregamento de carga completa** | O número de mesas falhou.      |
| **Atualizações incrementais**      | Número de atualizações de captura de dados de alteração (CDC) em linhas aplicadas ao alvo. |
| **Inserções incrementais**      | Número de inserções de CDC em linhas aplicadas ao alvo.      |
| **Eliminações incrementais** | Número de CCD apaga em linhas aplicadas ao alvo.      |
| **Alterações pendentes**      | Número de CDC em filas que ainda estão à espera de ser aplicados ao alvo. |
| **Alterações aplicadas**      | Total de atualizações, inserções e exclusões do CDC em linhas aplicadas ao alvo.      |
| **Tabelas em estado de erro** | Número de tabelas que estão em estado de "erro" durante a migração. Alguns exemplos de que as tabelas podem entrar em estado de erro são quando há duplicados identificados no alvo ou os dados não são compatíveis com o carregamento na tabela-alvo.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitor ao nível da tabela - Resumo Detalhado
Existem dois separadores que mostram o progresso da migração na carga completa e na sincronização de dados incrementais.
    
![Separador de carga completa](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Separador de sincronização de dados incrementais](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

A tabela seguinte descreve os campos mostrados no progresso da migração ao nível da tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Estado - Sincronização**      | A sincronização contínua está a funcionar. |
| **Inserir**      | Número de inserções de CDC em linhas aplicadas ao alvo.      |
| **Atualizar** | Número de atualizações do CDC em linhas aplicadas ao alvo.      |
| **Eliminar**      | Número de CCD apaga em linhas aplicadas ao alvo. |
| **Total Aplicado**      | Total de atualizações, inserções e exclusões do CDC em linhas aplicadas ao alvo. |
| **Erros de dados** | O número de erros de dados aconteceu nesta tabela. Alguns exemplos dos erros são *511: Não pode criar uma linha de tamanho %d que seja superior ao tamanho máximo admissível da linha de %d, 8114: Erro de conversão do tipo de dados %ls para %ls.*  O cliente deve consultar a partir de dms_apply_exceptions tabela no alvo Azure para ver os detalhes do erro.    |

> [!NOTE]
> Os valores cdc de Insert, Update and Delete e Total Applied podem diminuir quando a base de dados é cortada ou a migração é reiniciada.

## <a name="next-steps"></a>Passos seguintes
- Reveja a orientação de migração no Guia de [Migração](https://datamigration.microsoft.com/)da Microsoft Database .