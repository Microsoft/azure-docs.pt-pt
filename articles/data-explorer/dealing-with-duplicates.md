---
title: Lidar com dados duplicados no Explorador de dados do Azure
description: Este tópico mostra várias abordagens para lidar com dados duplicados, ao utilizar o Explorador de dados do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045786"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Lidar com dados duplicados no Explorador de dados do Azure

Enviar dados para a Cloud de dispositivos de manter um cache local dos dados. Dependendo do tamanho de dados, a cache local pode estar a armazenar dados de dias ou até mesmo meses. Quer salvaguardar as suas bases de dados analíticos de dispositivos com funcionamento incorreto reenviar os dados em cache e fazer com que a duplicação de dados na base de dados analítico. Este tópico descreve as melhores práticas para a manipulação de dados duplicados para estes tipos de cenários.

A melhor solução para duplicação de dados está a impedir a duplicação. Se possível, corrigi o problema anteriormente no pipeline de dados, que guarda os custos associados de movimento de dados ao longo do pipeline de dados e evita gastos de recursos enfrentá ingeridos no sistema de dados duplicados. No entanto, em situações em que o sistema de origem não pode ser modificado, existem várias formas de lidar com este cenário.

## <a name="understand-the-impact-of-duplicate-data"></a>Compreender o impacto dos dados duplicados

Monitorize a percentagem de dados duplicados. Assim que a percentagem de dados duplicados seja detetada, pode analisar o escopo do impacto de problema e business e escolha a solução apropriada.

Consulta de exemplo para identificar a percentagem de registros duplicados:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Soluções para a manipulação de dados duplicados

### <a name="solution-1-dont-remove-duplicate-data"></a>Solução #1: Não remover dados duplicados

Compreenda os seus requisitos empresariais e a tolerância de dados duplicados. Alguns conjuntos de dados podem gerir com um determinado percentual de dados duplicados. Se os dados duplicados não tem impacto principais, pode ignorar sua presença. A vantagem de não remover os dados duplicados não é nenhum overhead adicional sobre o desempenho de consulta ou processo de ingestão.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Solução #2: Lidar com linhas duplicadas durante a consulta

Outra opção é filtrar as linhas duplicadas de dados durante a consulta. O [ `arg_max()` ](/azure/kusto/query/arg-max-aggfunction) função agregada pode ser utilizada para filtrar os registos duplicados e devolver o último registo com base no carimbo de hora (ou outra coluna). A vantagem de utilizar este método é mais rápida ingestão uma vez que a eliminação de duplicação ocorre durante o tempo de consulta. Além disso, todos os registos (incluindo duplicados) estão disponíveis para auditoria e resolução de problemas. A desvantagem de usar o `arg_max` função é o tempo de consulta adicionais e carga na CPU toda vez que os dados são consultados. Dependendo da quantidade de dados a serem consultados, esta solução pode se tornar não funcional ou estão consumindo memória e exigirá a mudança para outras opções.

No exemplo a seguir, podemos consultar o último registo ingerido para um conjunto de colunas que determinam os registros exclusivos:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Esta consulta também pode ser colocada dentro de uma função em vez de consultar diretamente a tabela:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Solução #3: Filtrar duplicados durante o processo de ingestão

Outra solução é filtrar duplicados durante o processo de ingestão. O sistema ignora os dados duplicados durante a ingestão em tabelas de Kusto. Dados são ingeridos para uma tabela de testes e copiados para outra tabela depois de remover as linhas duplicadas. A vantagem dessa solução é que o desempenho de consulta melhora significativamente em comparação com a solução anterior. As desvantagens incluem o tempo de ingestão maior e os custos de armazenamento de dados adicionais.

O exemplo seguinte ilustra este método:

1. Crie outra tabela com o mesmo esquema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Criar uma função para filtrar os registos duplicados por anti-junção novos registros por aqueles de que o ingeridos anteriormente.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Associações são operações vinculadas à CPU e adicionar uma carga adicional no sistema.

1. Definir [política de atualização](/azure/kusto/management/update-policy) no `DeviceEventsUnique` tabela. A política de atualização é ativada quando dados novos vão para o `DeviceEventsAll` tabela. O mecanismo de Kusto executará automaticamente a função como novos [extensões](/azure/kusto/management/extents-overview) são criados. O processamento é confinado aos dados recentemente criados. O seguinte comando stitches a tabela de origem (`DeviceEventsAll`), a tabela de destino (`DeviceEventsUnique`) e a função `RemoveDuplicatesDeviceEvents` em conjunto para criar a política de atualização.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Política de atualização estende a duração de ingestão, uma vez que os dados são filtrados durante a ingestão e, em seguida, ingeridos duas vezes (para o `DeviceEventsAll` tabela e, ao `DeviceEventsUnique` tabela).

1. (Opcional) Definir uma retenção de dados inferior a `DeviceEventsAll` tabela para evitar o armazenamento de cópias dos dados. Escolha o número de dias, consoante o volume de dados e o período de tempo que pretende manter os dados para resolução de problemas. Pode defini-la `0d` retenção de dias para guardar COGS e melhorar o desempenho, uma vez que os dados não são carregados para o armazenamento.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Resumo

Duplicação de dados pode ser processada de várias formas. Avalie as opções com cuidado, levando em desempenho e o preço de conta para determinar o método correto para a sua empresa.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escrever consultas do Azure Data Explorer](write-queries.md)
