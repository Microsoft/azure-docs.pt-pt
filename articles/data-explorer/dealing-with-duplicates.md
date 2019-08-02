---
title: Manipular dados duplicados no Azure Data Explorer
description: Este tópico mostrará várias abordagens para lidar com dados duplicados ao usar o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608224"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Manipular dados duplicados no Azure Data Explorer

Os dispositivos que enviam dados para a nuvem mantêm um cache local dos dados. Dependendo do tamanho dos dados, o cache local pode estar armazenando dados por dias ou até mesmo meses. Você deseja proteger seus bancos de dados analíticos de dispositivos com funcionamento incorreto que reenviam os dado armazenados em cache e causam a duplicação de dados no banco de dado analítico. Este tópico descreve as práticas recomendadas para lidar com dados duplicados para esses tipos de cenários.

A melhor solução para a duplicação de dados está impedindo a duplicação. Se possível, corrija o problema anteriormente no pipeline de dados, o que poupa os custos associados à movimentação de dados ao longo do pipeline de dados e evita gastar recursos em coping com dados duplicados ingeridos no sistema. No entanto, em situações em que o sistema de origem não pode ser modificado, há várias maneiras de lidar com esse cenário.

## <a name="understand-the-impact-of-duplicate-data"></a>Entender o impacto de dados duplicados

Monitore a porcentagem de dados duplicados. Depois que a porcentagem de dados duplicados for descoberta, você poderá analisar o escopo do problema e o impacto nos negócios e escolher a solução apropriada.

Exemplo de consulta para identificar a porcentagem de registros duplicados:

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

## <a name="solutions-for-handling-duplicate-data"></a>Soluções para tratamento de dados duplicados

### <a name="solution-1-dont-remove-duplicate-data"></a>#1 da solução: Não remover dados duplicados

Entenda seus requisitos de negócios e a tolerância de dados duplicados. Alguns conjuntos de dados podem ser gerenciados com uma determinada porcentagem de duplicação. Se os dados duplicados não tiverem um grande impacto, você poderá ignorar sua presença. A vantagem de não remover os dados duplicados não é uma sobrecarga adicional no processo de ingestão ou no desempenho da consulta.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>#2 da solução: Manipular linhas duplicadas durante a consulta

Outra opção é filtrar as linhas duplicadas nos dados durante a consulta. A [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) função agregada pode ser usada para filtrar os registros duplicados e retornar o último registro com base no carimbo de data/hora (ou em outra coluna). A vantagem de usar esse método é a ingestão mais rápida, uma vez que a eliminação de duplicação ocorre durante o tempo de consulta. Além disso, todos os registros (incluindo duplicatas) estão disponíveis para auditoria e solução de problemas. A desvantagem de usar a `arg_max` função é o tempo de consulta adicional e a carga na CPU toda vez que os dados são consultados. Dependendo da quantidade de dados que estão sendo consultados, essa solução poderá se tornar não funcional ou que consome memória e exigirá a troca para outras opções.

No exemplo a seguir, consultamos o último registro ingerido para um conjunto de colunas que determinam os registros exclusivos:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Essa consulta também pode ser colocada dentro de uma função em vez de consultar diretamente a tabela:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>#3 da solução: Filtrar duplicatas durante o processo de ingestão

Outra solução é filtrar duplicatas durante o processo de ingestão. O sistema ignora os dados duplicados durante a ingestão nas tabelas Kusto. Os dados são ingeridos em uma tabela de preparo e copiados para outra tabela após a remoção de linhas duplicadas. A vantagem dessa solução é que o desempenho da consulta aumenta drasticamente em comparação com a solução anterior. As desvantagens incluem maior tempo de ingestão e custos de armazenamento de dados adicionais. Adicionalmente, essa solução funcionará somente se as duplicações não estiverem ingeridas simultaneamente. Se houver várias ingestãos simultâneas contendo registros duplicados, todas poderão ser ingeridas, já que o processo de eliminação de duplicação não encontrará nenhum registro correspondente existente na tabela.    

O exemplo a seguir descreve esse método:

1. Crie outra tabela com o mesmo esquema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Crie uma função para filtrar os registros duplicados, unindo os novos registros com os ingeridos anteriormente.

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
    > As junções são operações associadas à CPU e adicionam uma carga adicional no sistema.

1. Defina a política de `DeviceEventsUnique` [atualização](/azure/kusto/management/update-policy) na tabela. A política de atualização é ativada quando novos dados entram `DeviceEventsAll` na tabela. O mecanismo Kusto executará automaticamente a função à medida que novas [extensões](/azure/kusto/management/extents-overview) forem criadas. O processamento tem como escopo os dados recém-criados. O comando a seguir costura a tabela de origem`DeviceEventsAll`(), a tabela`DeviceEventsUnique`de destino () e `RemoveDuplicatesDeviceEvents` a função em conjunto para criar a política de atualização.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > A política de atualização estende a duração da ingestão desde que os dados são filtrados durante a ingestão e, em `DeviceEventsAll` seguida, são ingeridos duas vezes (à tabela e `DeviceEventsUnique` à tabela).

1. Adicional Defina uma retenção de dados inferior na `DeviceEventsAll` tabela para evitar o armazenamento de cópias dos dados. Escolha o número de dias dependendo do volume de dados e o período de tempo que você deseja manter os dados para solução de problemas. Você pode defini-la `0d` para a retenção de dias para economizar o Cogs e melhorar o desempenho, já que os dados não são carregados no armazenamento.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Resumo

A duplicação de dados pode ser tratada de várias maneiras. Avalie as opções com cuidado, levando em conta o preço e o desempenho, para determinar o método correto para sua empresa.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Escrever consultas do Azure Data Explorer](write-queries.md)
