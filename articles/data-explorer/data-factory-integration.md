---
title: Integração do Azure Data Explorer com o Azure Data Factory
description: Neste tópico, integre o Azure Data Explorer com Azure Data Factory para usar as atividades de cópia, pesquisa e comando
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: dd2b3bd584bb39810e0a5c9acde1a961330c273d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093764"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrar Data Explorer do Azure com Azure Data Factory

[Azure data Factory](/azure/data-factory/) (ADF) é um serviço de integração de dados baseado em nuvem que permite a integração de diferentes armazenamentos de dados e a realização de atividades nos dados. O ADF permite que você crie fluxos de trabalho controlados por dados para orquestrar e automatizar a movimentação de dados e a transformação de dados. O Azure Data Explorer é um dos [armazenamentos de dados com suporte](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) no Azure data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory atividades do Azure Data Explorer

Várias integrações com Azure Data Factory estão disponíveis para usuários do Azure Data Explorer:

### <a name="copy-activity"></a>Atividade Copiar
 
Azure Data Factory atividade de cópia é usada para transferir dados entre armazenamentos de dados. O Azure Data Explorer tem suporte como uma origem, onde os dados são copiados do Azure Data Explorer para qualquer armazenamento de dados com suporte e um coletor, no qual os dados são copiados de qualquer armazenamento de dados com suporte para o Azure Data Explorer. Para obter mais informações, consulte [copiar dados de ou para o Azure data Explorer usando Azure data Factory](/azure/data-factory/connector-azure-data-explorer). e para obter instruções detalhadas, consulte [carregar dados de Azure data Factory para o Azure data Explorer](data-factory-load-data.md).
O Data Explorer do Azure é compatível com Azure IR (Integration Runtime), usado quando os dados são copiados no Azure e o IR hospedado internamente, usado ao copiar dados de/para armazenamentos de dados localizados localmente ou em uma rede com controle de acesso, como uma rede virtual do Azure. Para obter mais informações, consulte [qual ir usar](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Ao usar a atividade de cópia e criar um **serviço vinculado** ou um **conjunto**de dados, selecione a data Explorer do Azure de Data Store **(Kusto)** e não o armazenamento de dados antigo **Kusto**.  

### <a name="lookup-activity"></a>Atividade de pesquisa
 
A atividade de pesquisa é usada para executar consultas no Azure Data Explorer. O resultado da consulta será retornado como a saída da atividade de pesquisa e poderá ser usado na próxima atividade no pipeline, conforme descrito na [documentação de pesquisa do ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Além do limite de tamanho de resposta de 5.000 linhas e 2 MB, a atividade também tem um limite de tempo limite de consulta de 1 hora.

### <a name="command-activity"></a>Atividade de comando

A atividade Command permite a execução de comandos do [controle](/azure/kusto/concepts/#control-commands)de data Explorer do Azure. Ao contrário das consultas, os comandos de controle podem potencialmente modificar dados ou metadados. Alguns dos comandos de controle são destinados a ingerir dados no Azure Data Explorer, usando comandos como `.ingest`ou `.set-or-append`) ou copiar dados do Azure Data Explorer para armazenamentos de dados externos usando comandos como `.export`.
Para obter um passo a passo detalhado da atividade de comando, consulte [usar Azure data Factory atividade de comando para executar comandos de controle de data Explorer do Azure](data-factory-command-activity.md).  O uso de um comando de controle para copiar dados pode, às vezes, ser uma opção mais rápida e barata do que a atividade de cópia. Para determinar quando usar a atividade de comando versus a atividade de cópia, confira [selecionar entre atividades de cópia e comando ao copiar dados](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Copiar em massa de um modelo de banco de dados

A [cópia em massa de um banco de dados para o Azure data Explorer usando o modelo de Azure data Factory](data-factory-template.md) é um pipeline de Azure data Factory predefinido. O modelo é usado para criar vários pipelines por banco de dados ou por tabela para uma cópia mais rápida. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Selecione entre as atividades de comando copiar e Data Explorer do Azure ao copiar dados 

Esta seção ajudará você a selecionar a atividade correta para suas necessidades de cópia de dados.

Ao copiar dados de ou para o Azure Data Explorer, há duas opções disponíveis no Azure Data Factory:
* Atividade de cópia.
* A atividade de comando Data Explorer do Azure, que executa um dos comandos de controle que transferem dados no Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Copiar dados do Azure Data Explorer
  
Você pode copiar dados do Azure Data Explorer usando a atividade de cópia ou o comando [`.export`](/azure/kusto/management/data-export/) . O comando `.export` executa uma consulta e, em seguida, exporta os resultados da consulta. 

Consulte a tabela a seguir para obter uma comparação da atividade de cópia e `.export` comando para copiar dados do Data Explorer do Azure.

| | Atividade Copiar | comando. Export |
|---|---|---|
| **Descrição do fluxo** | O ADF executa uma consulta em Kusto, processa o resultado e o envia para o armazenamento de dados de destino. <br>(**ADX > ADF > armazenamento de dados do coletor**) | O ADF envia um comando de controle de `.export` para Data Explorer do Azure, que executa o comando e envia os dados diretamente para o armazenamento de dados de destino. <br>(**ADX > armazenamento de dados do coletor**) |
| **Armazenamentos de dados de destino com suporte** | Uma ampla variedade de [armazenamentos de dados com suporte](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, BLOB do Azure, banco de dados SQL |
| **Performance** (Desempenho) | Centralizado | <ul><li>Distribuído (padrão), exportando dados de vários nós simultaneamente</li><li>Mais rápido e COGS eficientes.</li></ul> |
| **Limites do servidor** | [Os limites de consulta](/azure/kusto/concepts/querylimits) podem ser estendidos/desabilitados. Por padrão, as consultas do ADF contêm: <ul><li>Limite de tamanho de 500.000 registros ou 64 MB.</li><li>Limite de tempo de 10 minutos.</li><li>`noTruncation` definido como false.</li></ul> | Por padrão, o estende ou desabilita os limites de consulta: <ul><li>Os limites de tamanho estão desabilitados.</li><li>O tempo limite do servidor é estendido para uma hora.</li><li>`MaxMemoryConsumptionPerIterator` e `MaxMemoryConsumptionPerQueryPerNode` são estendidos para Max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Se o destino da cópia for um dos armazenamentos de dados com suporte pelo comando `.export` e se nenhum dos recursos da atividade de cópia for crucial para suas necessidades, selecione o comando `.export`.

### <a name="copying-data-to-azure-data-explorer"></a>Copiando dados para o Azure Data Explorer

Você pode copiar dados para o Azure Data Explorer usando a atividade de cópia ou comandos de ingestão, como [ingestão de consulta](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`e [ingestão de armazenamento](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Consulte a tabela a seguir para obter uma comparação da atividade de cópia e os comandos de ingestão para copiar dados para o Azure Data Explorer.

| | Atividade Copiar | Ingerir da consulta<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Ingestão do armazenamento <br> `.ingest` |
|---|---|---|---|
| **Descrição do fluxo** | O ADF Obtém os dados do armazenamento de dados de origem, converte-os em um formato tabular e faz as alterações necessárias no mapeamento de esquema. Em seguida, o ADF carrega os dados nos BLOBs do Azure, divide-os em partes e, em seguida, baixa os BLOBs para ingerir na tabela ADX. <br> (**Armazenamento de dados de origem > ADF > BLOBs do Azure > ADX**) | Esses comandos podem executar uma consulta ou um comando `.show` e ingerir os resultados da consulta em uma tabela (**ADX > ADX**). | Esse comando ingere dados em uma tabela por meio da "extração" dos dados de um ou mais artefatos de armazenamento em nuvem. |
| **Armazenamentos de dados de origem com suporte** |  [variedade de opções](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, BLOB do Azure, SQL (usando o plug-in sql_request), Cosmos (usando o plug-in cosmosdb_sql_request) e qualquer outro armazenamento de dados que forneça APIs HTTP ou Python. | FileSystem, armazenamento de BLOBs do Azure, ADLS Gen 1, ADLS Gen 2 |
| **Performance** (Desempenho) | As ingestãos são enfileiradas e gerenciadas, o que garante ingestãos de tamanho pequeno e assegura alta disponibilidade fornecendo balanceamento de carga, tentativas e tratamento de erros. | <ul><li>Esses comandos não foram projetados para a importação de dados de alto volume.</li><li>Funciona como esperado e mais barato. Mas para cenários de produção e quando as taxas de tráfego e os tamanhos de dados são grandes, use a atividade de cópia.</li></ul> |
| **Limites do servidor** | <ul><li>Sem limite de tamanho.</li><li>Limite de tempo limite máximo: 1 hora por blob ingerido. |<ul><li>Há apenas um limite de tamanho na parte de consulta, que pode ser ignorado com a especificação de `noTruncation=true`.</li><li>Limite de tempo limite máximo: 1 hora.</li></ul> | <ul><li>Sem limite de tamanho.</li><li>Limite de tempo limite máximo: 1 hora.</li></ul>|

> [!TIP]
> * Ao copiar dados do ADF para o Azure Data Explorer use os comandos `ingest from query`.  
> * Para conjuntos de dados grandes (> 1GB), use a atividade de cópia.  

## <a name="required-permissions"></a>Permissões obrigatórias

A tabela a seguir lista as permissões necessárias para várias etapas na integração com o Azure Data Factory.

| Passo | Operação | Nível mínimo de permissões | Notas |
|---|---|---|---|
| **Criar um serviço vinculado** | Navegação de banco de dados | *Visualizador de banco de dados* <br>O usuário conectado usando o ADF deve ser autorizado a ler os metadados do banco de dados. | O usuário pode fornecer o nome do banco de dados manualmente. |
| | Testar conexão | *Monitor de banco de dados* ou *ingestão de tabela* <br>A entidade de serviço deve ser autorizada a executar comandos de `.show` no nível do banco de dados ou ingestão de nível de tabela. | <ul><li>TestConnection verifica a conexão com o cluster e não com o banco de dados. Ele pode ter sucesso mesmo que o banco de dados não exista.</li><li>As permissões de administrador de tabela não são suficientes.</li></ul>|
| **Criando um conjunto de uma** | Navegação de tabela | *monitor de banco de dados* <br>O usuário conectado usando o ADF deve estar autorizado a executar comandos de `.show` no nível do banco de dados. | O usuário pode fornecer o nome da tabela manualmente.|
| **Criando um conjunto de um DataSet ou uma atividade de** **cópia** | Visualizar dados | *Visualizador de banco de dados* <br>A entidade de serviço deve ser autorizada a ler metadados do banco de dados. | | 
|   | Importar esquema | *Visualizador de banco de dados* <br>A entidade de serviço deve ser autorizada a ler metadados do banco de dados. | Quando ADX é a origem de uma cópia de tabela a tabular, o ADF importará o esquema automaticamente, mesmo que o usuário não tenha importado explicitamente o esquema. |
| **ADX como coletor** | Criar um mapeamento de coluna por nome | *monitor de banco de dados* <br>A entidade de serviço deve ser autorizada a executar comandos de `.show` no nível do banco de dados. | <ul><li>Todas as operações obrigatórias funcionarão com o *ingeridor de tabela*.</li><li> Algumas operações opcionais podem falhar.</li></ul> |
|   | <ul><li>Criar um mapeamento de CSV na tabela</li><li>Remover o mapeamento</li></ul>| *ingerirr de tabela* ou *administrador de banco de dados* <br>A entidade de serviço deve ser autorizada a fazer alterações em uma tabela. | |
|   | Ingerir dados | *ingerirr de tabela* ou *administrador de banco de dados* <br>A entidade de serviço deve ser autorizada a fazer alterações em uma tabela. | | 
| **ADX como fonte** | Executar consulta | *Visualizador de banco de dados* <br>A entidade de serviço deve ser autorizada a ler metadados do banco de dados. | |
| **Comando Kusto** | | De acordo com o nível de permissões de cada comando. |

## <a name="performance"></a>Desempenho 

Se o Azure Data Explorer for a origem e você usar a atividade de pesquisa, cópia ou comando que contém uma consulta em que, consulte [práticas recomendadas de consulta](/azure/kusto/query/best-practices) para obter informações de desempenho e [documentação do ADF para a atividade de cópia](/azure/data-factory/copy-activity-performance).
  
Esta seção aborda o uso da atividade de cópia em que o Azure Data Explorer é o coletor. A taxa de transferência estimada para o coletor de Data Explorer do Azure é de 11-13 MBps. A tabela a seguir detalha os parâmetros que influenciam o desempenho do coletor de Data Explorer do Azure.

| Parâmetro | Notas |
|---|---|
| **Proximidade geográfica dos componentes** | Coloque todos os componentes na mesma região:<ul><li>armazenamentos de dados de origem e de coletor.</li><li>Tempo de execução de integração do ADF.</li><li>Seu cluster ADX.</li></ul>Verifique se pelo menos o Integration Runtime está na mesma região que o cluster ADX. |
| **Número de DIUs** | 1 VM para cada 4 DIUs usada pelo ADF. <br>Aumentar o DIUs ajudará apenas se sua fonte for um armazenamento baseado em arquivo com vários arquivos. Cada VM, em seguida, processará um arquivo diferente em paralelo. Portanto, copiar um único arquivo grande terá uma latência maior do que copiar vários arquivos menores.|
|**Quantidade e SKU do seu cluster ADX** | O número alto de nós ADX aumentará o tempo de processamento de ingestão.|
| **Paralelismo** | Para copiar uma quantidade muito grande de dados de um banco de dados do, Particione-os e use um loop ForEach que copia cada partição em paralelo ou use o [modelo cópia em massa do banco de dados para o Azure data Explorer](data-factory-template.md). Observação: **as configurações** > **grau de paralelismo** na atividade de cópia não são relevantes para ADX. |
| **Complexidade do processamento de dados** | A latência varia de acordo com o formato do arquivo de origem, o mapeamento de coluna e a compactação.|
| **A VM que executa o Integration Runtime** | <ul><li>Para a cópia do Azure, as VMs do ADF e as SKUs do computador não podem ser alteradas.</li><li> Para a cópia local para o Azure, determine se a VM que hospeda o IR auto-hospedado é forte o suficiente.</li></ul>|

## <a name="monitor-activity-progress"></a>Monitorar o andamento da atividade

* Ao monitorar o andamento da atividade, a propriedade de *dados gravados* pode ser muito maior do que a propriedade de *leitura* de dados porque a *leitura de dados* é calculada de acordo com o tamanho do arquivo binário, enquanto os *dados gravados* são calculados de acordo com o tamanho da memória, depois que os dados são desserializados e descompactados.

* Ao monitorar o andamento da atividade, você pode ver que os dados são gravados no coletor de Data Explorer do Azure. Ao consultar a tabela de Data Explorer do Azure, você verá que os dados não chegaram. Isso ocorre porque há dois estágios ao copiar para o Azure Data Explorer. 
    * O primeiro estágio lê os dados de origem, divide-os em blocos de 900 MB e carrega cada bloco em um blob do Azure. O primeiro estágio é visto pelo modo de exibição progresso da atividade do ADF. 
    * O segundo estágio começa quando todos os dados são carregados nos BLOBs do Azure. Os nós do mecanismo de Data Explorer do Azure baixam os BLOBs e ingerim os dados na tabela de coletor. Os dados são então vistos em sua tabela de Data Explorer do Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [copiar dados para o Azure data Explorer usando Azure data Factory](data-factory-load-data.md).
* Saiba como usar o [modelo Azure data Factory para cópia em massa do banco de dados para o Azure data Explorer](data-factory-template.md).
* Saiba mais sobre como usar [Azure data Factory atividade de comando para executar comandos do controle de data Explorer do Azure](data-factory-command-activity.md).
* Saiba mais sobre as [consultas do Azure data Explorer](/azure/data-explorer/web-query-data) para consulta de dados.



