---
title: Utilizar o serviço de migração de base de dados do Azure para monitorizar a atividade de migração | Documentos da Microsoft
description: Saiba como utilizar o serviço de migração de base de dados do Azure para monitorizar a atividade de migração.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 44fbbcf42635288be055feddde1d9df2a1c67541
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790785"
---
# <a name="monitor-migration-activity"></a>Monitorizar a atividade de migração
Neste artigo, saiba como monitorizar o progresso de uma migração num nível de base de dados e um nível de tabela.

## <a name="monitor-at-the-database-level"></a>Monitorizar a nível da base de dados
Para monitorizar a atividade ao nível da base de dados, ver o painel de nível de base de dados:

![Painel de nível de base de dados](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Selecionar o hiperlink de base de dados irá mostrar a lista de tabelas e o progresso da migração.

A tabela seguinte lista os campos no painel de nível de base de dados e descreve os vários valores de estado associados a cada.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Nome do campo</strong></th>
      <th><strong>Subestado do campo</strong></th>
      <th><strong>Descrição</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Estado da atividade</strong></td>
      <td>A executar</td>
      <td>Atividade de migração está em execução.</td>
    </tr>
    <tr>
      <td>Bem-sucedido</td>
      <td>Atividade de migração foi concluída com êxito sem problemas.</td>
    </tr>
    <tr>
      <td>Falhou</td>
      <td>Falha na migração. Selecione a ligação "Ver detalhes do erro" em detalhes de migração para a mensagem de erro completa.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Estado</strong></td>
      <td>A iniciar</td>
      <td>O DMS é configurar o pipeline de migração.</td>
    </tr>
    <tr>
      <td>A executar</td>
      <td>O DMS pipeline está em execução e efetuar a migração.</td>
    </tr>
    <tr>
      <td>Concluir</td>
      <td>Migração concluída.</td>
    </tr>
    <tr>
      <td>Com Falhas</td>
      <td>Falha na migração. Clique em detalhes de migração para ver os erros de migração.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Detalhes da migração</strong></td>
      <td>Iniciar o pipeline de migração</td>
      <td>O DMS é configurar o pipeline de migração.</td>
    </tr>
    <tr>
      <td>Carregamento de dados completo em curso</td>
      <td>O DMS está a efetuar o carregamento inicial.</td>
    </tr>
    <tr>
      <td>Pronto para Cutover</td>
      <td>Depois de concluído o carregamento inicial, DMS serão marcadas como base de dados como pronto para transferência. Utilizador deve verificar se dados detetou na sincronização contínua.</td>
    </tr>
    <tr>
      <td>Todas as alterações foram aplicadas</td>
      <td>Carga inicial e a sincronização contínua estão completos. Este estado também ocorre depois da base de dados é transferência com êxito.</td>
    </tr>
    <tr>
      <td>Ver detalhes do erro</td>
      <td>Clique no link para mostrar detalhes do erro.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Duração</strong></td>
      <td>N/A</td>
      <td>Total de tempo de atividade de migração que está a ser inicializada para a migração foi concluída ou falha de migração.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Monitorizar no nível de tabela – resumo rápido
Para monitorizar a atividade no nível da tabela, ver o painel de nível de tabela. A parte superior do painel mostra o que número de linhas detalhado migrados no carregamento completo e atualizações incrementais. 

A parte inferior do painel lista as tabelas e mostra um resumo rápido de progresso da migração.

![Painel de nível de tabela – resumo rápido](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

A tabela seguinte descreve os campos exibidos nos detalhes do nível de tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Carregamento completo concluído**      | Número de tabelas de concluir o carregamento de dados completo. |
| **Carga completa em fila**      | Número de tabelas em fila para carregamento completo.      |
| **Carregamento de carga completo** | Falha ao número de tabelas.      |
| **Atualizações incrementais**      | Número de dados de alteração captura atualizações (CDC) em linhas aplicadas ao destino. |
| **Inserções de incrementais**      | Número de CDC insere em linhas aplicadas ao destino.      |
| **Eliminações incrementais** | Número de CDC elimina em linhas aplicadas ao destino.      |
| **Alterações pendentes**      | Número de CDC nas linhas que ainda estão a aguardar para ser aplicadas ao destino. |
| **Alterações aplicadas**      | Total de CDC atualizações, inserções e elimina em linhas aplicadas ao destino.      |
| **Tabelas no Estado com erros** | Número de tabelas que estão no estado "error" durante a migração. Alguns exemplos que tabelas podem entrar em estado de erro são quando existirem duplicatas identificadas no destino ou dados não não compatíveis de carregar a tabela de destino.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Monitorizar no nível de tabela – Resumo detalhadas
Há duas guias que mostram o progresso de migração no carregamento completo e sincronização de dados incrementais.
    
![Separador de carregamento completo](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Separador de sincronização de dados incrementais](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

A tabela seguinte descreve os campos exibidos em curso de nível de migração de tabela.

| Nome do campo        | Descrição       |
| ------------- | ------------- |
| **Estado - a sincronizar**      | Sincronização contínua está em execução. |
| **Inserir**      | Número de CDC insere em linhas aplicadas ao destino.      |
| **Atualização** | Número de atualizações de CDC em linhas aplicadas ao destino.      |
| **Eliminar**      | Número de CDC elimina em linhas aplicadas ao destino. |
| **Total aplicado**      | Total de CDC atualizações, inserções e elimina em linhas aplicadas ao destino. |
| **Erros de dados** | Número de erros de dados ocorreu nesta tabela. Alguns exemplos dos erros são *511: Não é possível criar uma linha do tamanho %d que é maior que o tamanho de linha máximo permitido de %d, 8114: Tipo de dados de conversão de erro %ls para %ls.*  Cliente deve consultar da tabela de dms_apply_exceptions no destino do Azure para ver os detalhes do erro.    |

> [!NOTE]
> Valores de CDC de Insert, Update e Delete e aplicadas Total podem diminuir quando a base de dados é transferência ou migração é reiniciada.

## <a name="next-steps"></a>Passos Seguintes
- Reveja as orientações de migração no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).