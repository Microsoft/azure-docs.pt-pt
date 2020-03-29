---
title: Manuseie dados duplicados no Azure Data Explorer
description: Este tópico irá mostrar-lhe várias abordagens para lidar com dados duplicados ao utilizar o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608224"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Manuseie dados duplicados no Azure Data Explorer

Os dispositivos que enviam dados para a Cloud mantêm uma cache local dos dados. Dependendo do tamanho dos dados, a cache local pode estar a armazenar dados durante dias ou mesmo meses. Pretende salvaguardar as suas bases de dados analíticas de dispositivos avariados que reenviam os dados em cache e causam duplicação de dados na base de dados analítica. Este tópico descreve as melhores práticas para o manuseamento de dados duplicados para este tipo de cenários.

A melhor solução para a duplicação de dados é impedir a duplicação. Se possível, corrija o problema mais cedo no pipeline de dados, o que poupa custos associados ao movimento de dados ao longo do pipeline de dados e evita gastar recursos para lidar com dados duplicados ingeridos no sistema. No entanto, em situações em que o sistema de origem não pode ser modificado, existem várias formas de lidar com este cenário.

## <a name="understand-the-impact-of-duplicate-data"></a>Compreender o impacto dos dados duplicados

Monitorize a percentagem de dados duplicados. Uma vez descoberta a percentagem de dados duplicados, pode analisar o âmbito do problema e o impacto do negócio e escolher a solução adequada.

Consulta de amostra para identificar a percentagem de registos duplicados:

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

## <a name="solutions-for-handling-duplicate-data"></a>Soluções para o manuseamento de dados duplicados

### <a name="solution-1-dont-remove-duplicate-data"></a>Solução #1: Não remova dados duplicados

Compreenda os seus requisitos de negócio e a tolerância aos dados duplicados. Alguns conjuntos de dados podem gerir com uma certa percentagem de dados duplicados. Se os dados duplicados não tiverem um grande impacto, pode ignorar a sua presença. A vantagem de não remover os dados duplicados não é uma sobrecarga adicional no processo de ingestão ou no desempenho da consulta.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Solução #2: Manuseie linhas duplicadas durante consulta

Outra opção é filtrar as linhas duplicadas nos dados durante a consulta. A [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) função agregada pode ser utilizada para filtrar os registos duplicados e devolver o último registo com base na marca ção (ou outra coluna). A vantagem de usar este método é uma ingestão mais rápida, uma vez que a desduplicação ocorre durante o tempo de consulta. Além disso, todos os registos (incluindo duplicados) estão disponíveis para auditoria e resolução de problemas. A desvantagem `arg_max` de utilizar a função é o tempo adicional de consulta e a carga no CPU sempre que os dados são consultados. Dependendo da quantidade de dados que estão a ser consultados, esta solução pode tornar-se não funcional ou que consome memória e exigirá a mudança para outras opções.

No exemplo seguinte, consultamos o último registo ingerido por um conjunto de colunas que determinam os registos únicos:

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

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Solução #3: Filtrar duplica durante o processo de ingestão

Outra solução é filtrar duplicados durante o processo de ingestão. O sistema ignora os dados duplicados durante a ingestão nas tabelas kusto. Os dados são ingeridos numa mesa de preparação e copiados para outra mesa depois de remover em filas duplicadas. A vantagem desta solução é que o desempenho da consulta melhora drasticamente em comparação com a solução anterior. As desvantagens incluem o aumento do tempo de ingestão e os custos adicionais de armazenamento de dados. Além disso, esta solução só funciona se as duplicações não forem ingeridas simultaneamente. Se existirem múltiplas ingestão simultânea que contenham registos duplicados, todas podem ser ingeridas, uma vez que o processo de desduplicação não encontrará registos correspondentes existentes na tabela.    

O exemplo que se segue retrata este método:

1. Crie outra tabela com o mesmo esquema:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Crie uma função para filtrar os registos duplicados, anti-juntando os novos registos com os anteriormente ingeridos.

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
    > As juntas são operações ligadas ao CPU e adicionam uma carga adicional no sistema.

1. Definir a `DeviceEventsUnique` política de [atualização](/azure/kusto/management/update-policy) na tabela. A política de atualização é ativada quando novos dados entram na `DeviceEventsAll` tabela. O motor Kusto executará automaticamente a função à medida que forem criadas novas [extensões.](/azure/kusto/management/extents-overview) O tratamento é remeto aos dados recém-criados. O comando seguinte cose`DeviceEventsAll`a tabela`DeviceEventsUnique`de origem `RemoveDuplicatesDeviceEvents` ( ), e a função em conjunto para criar a política de atualização.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > A política de atualização alarga a duração da ingestão uma vez que `DeviceEventsAll` os dados `DeviceEventsUnique` são filtrados durante a ingestão e depois ingeridos duas vezes (para a mesa e para a mesa).

1. (Opcional) Coloque uma menor retenção de dados na `DeviceEventsAll` tabela para evitar armazenar cópias dos dados. Escolha o número de dias dependendo do volume de dados e do tempo que pretende reter dados para resolução de problemas. Pode defini-lo `0d` em dias de retenção para salvar COGS e melhorar o desempenho, uma vez que os dados não são enviados para armazenamento.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Resumo

A duplicação de dados pode ser manuseada de várias maneiras. Avalie cuidadosamente as opções, tendo em conta o preço e o desempenho, para determinar o método correto para o seu negócio.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escrever consultas do Azure Data Explorer](write-queries.md)
