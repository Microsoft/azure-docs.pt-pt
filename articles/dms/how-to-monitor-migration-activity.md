---
title: Monitorizar a atividade de migração - Azure Database Migration Service
description: Aprenda a utilizar o Serviço de Migração da Base de Dados Azure para monitorizar a atividade de migração.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91297475"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Monitorizar a atividade de migração utilizando o Serviço de Migração da Base de Dados de Azure
Neste artigo, aprende-se a monitorizar o progresso de uma migração tanto a nível de base de dados como a nível de tabela.

## <a name="monitor-at-the-database-level"></a>Monitorar ao nível da base de dados
Para monitorizar a atividade ao nível da base de dados, consulte a lâmina de nível de base de dados:

![Lâmina de nível de base de dados](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> A seleção da hiperligação da base de dados mostrar-lhe-á a lista de tabelas e o seu progresso migratório.

A tabela que se segue lista os campos na lâmina de nível de base de dados e descreve os vários valores de estado associados a cada um.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nome do campo</strong></th>
      <th><strong>Subestato de campo</strong></th>
      <th><strong>Descrição</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Estado da atividade</strong></td>
      <td>Em Execução</td>
      <td>A atividade de migração está a decorrer.</td>
    </tr>
    <tr>
      <td>Com êxito</td>
      <td>A atividade migratória foi bem sucedida sem problemas.</td>
    </tr>
    <tr>
      <td>Defeituoso</td>
      <td>A migração falhou. Selecione o link 'Ver detalhes de erro' nos detalhes da migração para obter a mensagem de erro completa.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Estado</strong></td>
      <td>A inicializar</td>
      <td>O DMS está a montar o oleoduto de migração.</td>
    </tr>
    <tr>
      <td>Em Execução</td>
      <td>O oleoduto DMS está a funcionar e a realizar migração.</td>
    </tr>
    <tr>
      <td>Concluído</td>
      <td>Migração concluída.</td>
    </tr>
    <tr>
      <td>Com falhas</td>
      <td>A migração falhou. Clique em detalhes de migração para ver erros de migração.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Detalhes da migração</strong></td>
      <td>Início do oleoduto de migração</td>
      <td>O DMS está a montar o oleoduto de migração.</td>
    </tr>
    <tr>
      <td>Carga completa de dados em curso</td>
      <td>O DMS está a fazer a carga inicial.</td>
    </tr>
    <tr>
      <td>Pronto para Cutover</td>
      <td>Após a conclusão da carga inicial, o DMS marcará a base de dados como pronta para a redução. O utilizador deve verificar se os dados se acumularam em sincronização contínua.</td>
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
      <td rowspan="1" class="duration"><strong>Duration</strong></td>
      <td>N/D</td>
      <td>O tempo total da atividade migratória que está a ser inicializada para a migração concluída ou a migração falhou.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitor ao nível da mesa - Resumo Rápido
Para monitorizar a atividade ao nível da mesa, consulte a lâmina ao nível da mesa. A parte superior da lâmina mostra o número detalhado de linhas migradas em completa carga e atualizações incrementais. 

A parte inferior da lâmina lista as tabelas e mostra um resumo rápido do progresso da migração.

![Lâmina de nível de mesa - resumo rápido](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

A tabela seguinte descreve os campos indicados nos detalhes do nível de tabela.

| Nome do campo        | Description       |
| ------------- | ------------- |
| **Carga completa concluída**      | Número de tabelas preenchidas com carga completa de dados. |
| **Carga completa em fila**      | Número de mesas em fila para a carga completa.      |
| **Carregamento completo** | Número de mesas falhou.      |
| **Atualizações incrementais**      | Número de atualizações de captura de dados de alteração (CDC) em linhas aplicadas ao alvo. |
| **Inserções incrementais**      | Número de inserções de CDC em linhas aplicadas ao alvo.      |
| **Eliminações incrementais** | Número de CDC elimina em linhas aplicadas ao alvo.      |
| **Alterações pendentes**      | Número de CDC em filas que ainda estão à espera de ser aplicado ao alvo. |
| **Alterações aplicadas**      | Total de atualizações, inserções e eliminações de CDC em linhas aplicadas ao alvo.      |
| **Tabelas em estado de erro** | Número de tabelas que estão em estado de "erro" durante a migração. Alguns exemplos de que as tabelas podem entrar em estado de erro são quando há duplicados identificados no alvo ou os dados não são compatíveis com o carregamento na tabela alvo.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitor ao nível da tabela - Resumo Detalhado
Existem dois separadores que mostram o progresso da migração na sincronização de dados full load e incremental.
    
![Separador de carga completa](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Separador de sincronização de dados incrementais](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

O quadro que se segue descreve os campos indicados no progresso migratório ao nível da tabela.

| Nome do campo        | Description       |
| ------------- | ------------- |
| **Estado - Sincronização**      | A sincronização contínua está a funcionar. |
| **Inserir**      | Número de inserções de CDC em linhas aplicadas ao alvo.      |
| **Atualizar** | Número de atualizações do CDC em linhas aplicadas ao alvo.      |
| **Eliminar**      | Número de CDC elimina em linhas aplicadas ao alvo. |
| **Total aplicado**      | Total de atualizações, inserções e eliminações de CDC em linhas aplicadas ao alvo. |
| **Erros de Dados** | Número de erros de dados ocorreu nesta tabela. Alguns exemplos dos erros são *511: Não é possível criar uma linha de tamanho %d que seja maior do que o tamanho máximo de linha permitido de %d, 8114: Erro convertendo o tipo de dados %ls para %ls.*  O cliente deve consultar a partir de dms_apply_exceptions tabela no alvo Azure para ver os detalhes de erro.    |

> [!NOTE]
> Os valores do CDC de Inserir, Atualizar e Eliminar e Total Aplicados podem diminuir quando a base de dados é cortada ou a migração é reiniciada.

## <a name="next-steps"></a>Passos seguintes
- Reveja a orientação de migração no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft.