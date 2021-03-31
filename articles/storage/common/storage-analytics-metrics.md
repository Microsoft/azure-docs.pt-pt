---
title: Métricas Azure Storage Analytics (clássico)
description: Aprenda a usar as métricas de Storage Analytics no Azure Storage. Conheça as métricas de transação e capacidade, como as métricas são armazenadas, permitindo métricas, e muito mais.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d900ffa4481ba2b6deb21a8325f3f8def8084f84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714736"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Métricas Azure Storage Analytics (clássico)

No **dia 31 de agosto de 2023** serão retiradas as métricas de Storage Analytics, também referidas como *métricas clássicas.* Para obter mais informações, veja o [anúncio oficial](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Se utilizar as métricas clássicas, certifique-se de que faz a transição para as métricas do Azure Monitor antes dessa data. Este artigo ajuda na transição. 

O Azure Storage utiliza a solução Storage Analytics para armazenar métricas que incluem estatísticas de transações agregadas e dados de capacidade sobre pedidos a um serviço de armazenamento. As transações são reportadas ao nível de operação da API e ao nível do serviço de armazenamento. A capacidade é reportada ao nível do serviço de armazenamento. Os dados métricos podem ser utilizados para:
- Analise o uso do serviço de armazenamento.
- Diagnosticar problemas com pedidos feitos contra o serviço de armazenamento.
- Melhorar o desempenho das aplicações que utilizam um serviço.

 Armazenamento As métricas de Analytics são ativadas por padrão para novas contas de armazenamento. Pode configurar métricas no [portal Azure,](https://portal.azure.com/)utilizando o PowerShell, ou utilizando o Azure CLI. Para obter orientação passo a passo, consulte [Ativar e gerir métricas analíticas de armazenamento Azure (clássica)](./manage-storage-analytics-logs.md). Também pode ativar o Storage Analytics programáticamente através da API REST ou da biblioteca do cliente. Utilize as operações De Conjunto De Propriedades de Serviço para ativar o Storage Analytics para cada serviço.  

> [!NOTE]
> As métricas de Storage Analytics estão disponíveis para armazenamento Azure Blob, armazenamento da fila Azure, armazenamento de mesa Azure e Ficheiros Azure.
> Armazenamento As métricas de Analytics são agora métricas clássicas. Recomendamos que utilize [métricas de armazenamento no Azure Monitor](../blobs/monitor-blob-storage.md) em vez de métricas de Storage Analytics.

## <a name="transaction-metrics"></a>Métricas de transação  
 Um conjunto robusto de dados é registado em intervalos de hora ou minuto para cada serviço de armazenamento e operação API solicitada, que inclui entradas e saídas, disponibilidade, erros e percentagens de pedido categorizadas. Para obter uma lista completa dos detalhes da transação, consulte o [esquema da tabela de métricas storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Os dados de transação são registados ao nível do serviço e do nível de operação da API. Ao nível do serviço, as estatísticas que resumem todas as operações solicitadas da API são escritas a uma entidade de mesa a cada hora, mesmo que não tenha sido feito nenhum pedido ao serviço. Ao nível da operação da API, as estatísticas só são escritas a uma entidade se a operação tiver sido solicitada dentro dessa hora.  

 Por exemplo, se efetuar uma operação **GetBlob** no seu serviço blob, a Storage Analytics Metrics regista o pedido e inclui-o nos dados agregados para o serviço blob e a operação **GetBlob.** Se não for solicitada nenhuma operação **GetBlob** durante a hora, uma entidade não é escrita para *$MetricsTransactionsBlob* para essa operação.  

 As métricas de transação são registadas para pedidos de utilizador e pedidos feitos pela própria Storage Analytics. Por exemplo, são registados pedidos por Storage Analytics para escrever registos e entidades de tabelas.

## <a name="capacity-metrics"></a>Métricas de capacidade  

> [!NOTE]
>  Atualmente, as métricas de capacidade estão disponíveis apenas para o serviço blob.

 Os dados de capacidade são registados diariamente para o serviço de blob de uma conta de armazenamento, e duas entidades de tabela são escritas. Uma entidade fornece estatísticas para os dados dos utilizadores, e a outra fornece estatísticas sobre o `$logs` recipiente blob utilizado pela Storage Analytics. A *tabela $MetricsCapacityBlob* inclui as seguintes estatísticas:  

- **Capacidade**: A quantidade de armazenamento utilizada pelo serviço blob da conta de armazenamento, em bytes.  
- **Contentor:** O número de recipientes de bolhas no serviço de bolhas da conta de armazenamento.  
- **ObjectCount**: O número de blocos ou bolhas de página comprometidos e não comprometidos no serviço blob da conta de armazenamento.  

  Para obter mais informações sobre as métricas de capacidade, consulte [o esquema da tabela de métricas storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Como as métricas são armazenadas  

 Todos os dados métricos de cada um dos serviços de armazenamento são armazenados em três tabelas reservadas para esse serviço. Uma tabela é para informações de transação, uma tabela é para informações de transação minúsculas, e outra tabela é para informações de capacidade. A informação de transação e de transação minúscula consiste em dados de pedido e resposta. A informação sobre a capacidade consiste em dados de utilização de armazenamento. As métricas de hora, as métricas minúsculas e a capacidade do serviço blob de uma conta de armazenamento são acedidas em tabelas que são indicadas como descritas na tabela seguinte.  

|Nível de métricas|Nomes de tabelas|Suportado para versões|  
|-------------------|-----------------|----------------------------|  
|Métricas horárias, localização primária|- $MetricsTransactionsBlob<br />- $MetricsTransactionsTable<br />- $MetricsTransactionsQueue|Versões anteriores a 15 de agosto de 2013, apenas. Embora estes nomes ainda estejam suportados, recomendamos que mude a utilizar as tabelas que se seguem.|  
|Métricas horárias, localização primária|- $MetricsHourPrimaryTransactionsBlob<br />- $MetricsHourPrimaryTransactionsTable<br />- $MetricsHourPrimaryTransactionsQueue<br />- $MetricsHourPrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de ficheiros só está disponível na versão 5 de abril de 2015 e mais tarde.|  
|Métricas minúsculas, localização primária|- $MetricsMinutePrimaryTransactionsBlob<br />- $MetricsMinutePrimaryTransactionsTable<br />- $MetricsMinutePrimaryTransactionsQueue<br />- $MetricsMinutePrimaryTransactionsFile|Todas as versões. O suporte para métricas de serviço de ficheiros só está disponível na versão 5 de abril de 2015 e mais tarde.|  
|Métricas horárias, localização secundária|- $MetricsHourSecondaryTransactionsBlob<br />- $MetricsHourSecondaryTransactionsTable<br />- $MetricsHourSecondaryTransactionsQueue|Todas as versões. A replicação geo-redundante de acesso à leitura deve ser ativada.|  
|Métricas minúsculas, localização secundária|- $MetricsMinuteSecondaryTransactionsBlob<br />- $MetricsMinuteSecondaryTransactionsTable<br />- $MetricsMinuteSecondaryTransactionsQueue|Todas as versões. A replicação geo-redundante de acesso à leitura deve ser ativada.|  
|Capacidade (apenas serviço blob)|$MetricsCapacityBlob|Todas as versões.|  

 Estas tabelas são criadas automaticamente quando o Storage Analytics está ativado para um ponto final de serviço de armazenamento. São acedidos através do espaço de nome da conta de armazenamento, por `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` exemplo, . As tabelas de métricas não aparecem numa operação de listagem e devem ser acedidas diretamente através do nome da tabela.

## <a name="metrics-alerts"></a>Alertas de métricas
Considere configurar alertas no [portal Azure para](https://portal.azure.com) que seja automaticamente notificado de mudanças importantes no comportamento dos seus serviços de armazenamento. Para obter orientação passo a passo, consulte [criar alertas de métricas](./manage-storage-analytics-logs.md).

Se utilizar uma ferramenta Do Explorador de Armazenamento para descarregar estes dados métricos num formato delimitado, pode utilizar o Microsoft Excel para analisar os dados. Para obter uma lista das ferramentas disponíveis do Storage Explorer, consulte as [ferramentas do cliente do Azure Storage](./storage-explorers.md).

> [!IMPORTANT]
> Pode haver um atraso entre um evento de armazenamento e quando os dados de métricas de hora ou minuto correspondentes são registados. No caso de métricas minúsculas, vários minutos de dados podem ser escritos de uma só vez. Esta emissão pode levar a que as transações de minutos anteriores sejam agregadas na transação para o minuto em curso. Quando este problema acontece, o serviço de alerta pode não ter todos os dados métricos disponíveis para o intervalo de alerta configurado, o que pode levar a alertas disparando inesperadamente.
>

## <a name="billing-on-storage-metrics"></a>Faturação em métricas de armazenamento
Os pedidos de escrita para a criação de entidades de tabela para métricas são cobrados às taxas padrão aplicáveis a todas as operações de Armazenamento Azure.  

Ler e apagar pedidos de dados de métricas por um cliente também são faturais a taxas padrão. Se configurar uma política de retenção de dados, não é cobrado quando o Azure Storage elimina dados de métricas antigas. Se eliminar dados de análise, a sua conta é cobrada para as operações de eliminação.  

A capacidade utilizada pelas tabelas de métricas também é faturada. Utilizar as seguintes informações para estimar a quantidade de capacidade utilizada para armazenar dados de métricas:  

-   Se cada hora um serviço utilizar cada API em cada serviço, aproximadamente 148 KB de dados é armazenado a cada hora nas tabelas de transações de métricas se você ativar um resumo de nível de serviço e nível API.  
-   Se dentro de cada hora um serviço utilizar cada API no serviço, aproximadamente 12 KB de dados é armazenado a cada hora nas tabelas de transações de métricas se ativar apenas um resumo ao nível do serviço.  
-   A tabela de capacidade para bolhas tem duas linhas adicionadas por dia, desde que tenha optado por registos. Este cenário implica que todos os dias o tamanho desta tabela aumenta em cerca de 300 bytes.

## <a name="next-steps"></a>Passos seguintes
* [Monitorizar uma conta de armazenamento](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Armazenamento Analytics métricas esquema de tabela](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Armazenamento Analytics registou operações e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Registo de analítica de armazenamento](storage-analytics-logging.md)
