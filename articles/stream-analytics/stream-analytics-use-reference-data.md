---
title: Utilize dados de referência para procuras no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para procurar ou correlacionar dados num design de consulta de trabalho do Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/11/2020
ms.openlocfilehash: 524fc747e8e3dc70bdcc594a38b2a083b8381daa
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124079"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Utilização de dados de referência para procuras no Stream Analytics

Os dados de referência (também conhecidos como mesa de lookup) são um conjunto de dados finito que está estático ou muda lentamente na natureza, usado para realizar uma procura ou para aumentar os seus fluxos de dados. Por exemplo, num cenário ioT, você poderia armazenar metadados sobre sensores (que não mudam frequentemente) em dados de referência e juntá-lo com fluxos de dados IoT em tempo real. O Azure Stream Analytics carrega dados de referência na memória para obter um processamento de fluxo de latência baixa. Para utilizar os dados de referência no seu trabalho de Análise de Fluxo de Fluxo, geralmente utilizará um Encontro de Dados de [Referência](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) na sua consulta. 

## <a name="example"></a>Exemplo  
Você pode ter um fluxo real de eventos gerados quando os carros passam por uma portagem. A portagem pode capturar a placa em tempo real e juntar-se a um conjunto de dados estático que tem detalhes de registo para identificar placas que tenham expirado.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics suporta o armazenamento azure Blob e a Base de Dados Azure SQL como camada de armazenamento para dados de referência. Também pode transformar e/ou copiar dados de referência para o armazenamento blob da Azure Data Factory para utilizar [qualquer número de lojas de dados baseadas em nuvem e no local.](../data-factory/copy-activity-overview.md)

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Os dados de referência são modelados como uma sequência de bolhas (definidas na configuração de entrada) na ordem ascendente da data/hora especificada no nome blob. **Apenas** suporta a adição ao fim da sequência utilizando uma data/hora **maior** do que a especificada pela última bolha na sequência.

### <a name="configure-blob-reference-data"></a>Configure dados de referência blob

Para configurar os seus dados de referência, primeiro é necessário criar uma entrada que seja de dados de **referência**do tipo . O quadro abaixo explica cada imóvel que terá de fornecer ao mesmo tempo que cria a entrada de dados de referência com a sua descrição:

|**Nome da Propriedade**  |**Descrição**  |
|---------|---------|
|Alias de Entrada   | Um nome amigável que será usado na consulta de trabalho para fazer referência a esta entrada.   |
|Conta de Armazenamento   | O nome da conta de armazenamento onde estão as suas bolhas. Se estiver na mesma subscrição que o seu Stream Analytics Job, pode selecioná-lo a partir da queda.   |
|Chave da conta de armazenamento   | A chave secreta associada à conta de armazenamento. Isto é automaticamente povoado se a conta de armazenamento estiver na mesma subscrição que o seu trabalho stream analytics.   |
|Recipiente de armazenamento   | Os contentores fornecem um agrupamento lógico para bolhas armazenadas no serviço Microsoft Azure Blob. Quando enviar uma bolha para o serviço Blob, deve especificar um recipiente para essa bolha.   |
|Padrão do Caminho   | Esta é uma propriedade necessária que é usada para localizar as suas bolhas dentro do recipiente especificado. Dentro do caminho, pode optar por especificar uma ou mais instâncias das seguintes 2 variáveis:<BR>{date}, {time}<BR>Exemplo 1: produtos/{date}/{time}/product-list.csv<BR>Exemplo 2: produtos/{date}/product-list.csv<BR>Exemplo 3: lista de produtos.csv<BR><br> Se a bolha não existir no caminho especificado, o trabalho de Stream Analytics aguarda indefinidamente que a bolha fique disponível.   |
|Formato de data [opcional]   | Se tiver usado {date} dentro do Padrão de Percurso que especificou, então pode selecionar o formato de data no qual as suas bolhas são organizadas a partir da queda dos formatos suportados.<BR>Exemplo: YYYY/MM/DD, MM/DD/YYYY, etc.   |
|Formato de Tempo [opcional]   | Se tiver usado {time} dentro do Padrão do Caminho que especificou, então pode selecionar o formato de tempo em que as suas bolhas são organizadas a partir da queda dos formatos suportados.<BR>Exemplo: HH, HH/mm ou HH-mm.  |
|Formato de Serialização de Eventos   | Para garantir que as suas consultas funcionam da forma que espera, o Stream Analytics precisa de saber qual o formato de serialização que está a usar para os fluxos de dados que chegam. Para dados de referência, os formatos suportados são CSV e JSON.  |
|Codificação   | O UTF-8 é o único formato de codificação suportado neste momento.  |

### <a name="static-reference-data"></a>Dados de referência estáticos

Se não se esperar que os seus dados de referência se alterem, o suporte para dados de referência estáticos está ativado especificando um caminho estático na configuração de entrada. O Azure Stream Analytics recolhe a bolha do caminho especificado. Não são necessárias fichas de substituição {date} e {time} Como os dados de referência são imutáveis no Stream Analytics, não é recomendada a sobreposição de uma bolha de dados de referência estática.

### <a name="generate-reference-data-on-a-schedule"></a>Gerar dados de referência num horário

Se os seus dados de referência forem um conjunto de dados em mudança lenta, o suporte para dados de referência refrescantes está ativado especificando um padrão de caminho na configuração de entrada utilizando as fichas de substituição {date} e {time} . O Stream Analytics capta as definições de dados de referência atualizadas com base neste padrão de percurso. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data de **"YYYY-MM-DD"** e um formato de tempo de **"HH-mm"** instrui o Stream Analytics a pegar na bolha atualizada `sample/2015-04-16/17-30/products.csv` às 17:30 do dia 16 de abril de 2015.

O Azure Stream Analytics procura automaticamente bolhas de dados de referência renovadas num intervalo de um minuto. Se uma bolha com carimbo de tempo 10:30:00 é carregada com um pequeno atraso (por exemplo, 10:30:30), você vai notar um pequeno atraso no trabalho de Stream Analytics referindo esta bolha. Para evitar tais cenários, recomenda-se o upload da bolha mais cedo do que o tempo de eficácia do alvo (10:30:00 neste exemplo) para permitir que o trabalho do Stream Analytics seja suficiente para descobrir e carregar na memória e realizar operações. 

> [!NOTE]
> Atualmente, os trabalhos da Stream Analytics procuram a atualização da bolha apenas quando o tempo da máquina avança para o tempo codificado no nome blob. Por exemplo, o trabalho procurará o `sample/2015-04-16/17-30/products.csv` mais rapidamente possível, mas não antes das 17:30 do dia 16 de abril de 2015, fuso horário UTC. *Nunca* procurará uma bolha com um tempo codificado mais cedo do que a última que foi descoberta.
> 
> Por exemplo, uma vez que o trabalho encontre a `sample/2015-04-16/17-30/products.csv` bolha, ignorará quaisquer ficheiros com uma data codificada antes das 17:30 de 16 de abril de 2015, pelo que se for criada uma bolha tardia `sample/2015-04-16/17-25/products.csv` no mesmo recipiente, o trabalho não a utilizará.
> 
> Da mesma forma, se `sample/2015-04-16/17-30/products.csv` for produzido apenas às 22:03 de 16 de abril de 2015, mas não houver uma bolha com data anterior no contentor, o trabalho utilizará este ficheiro a partir das 22:03 de 16 de abril de 2015 e utilizará os dados de referência anteriores até lá.
> 
> Uma exceção a isto é quando o trabalho precisa de reprocessar os dados no tempo ou quando o trabalho é iniciado. Na hora de início, o trabalho procura a mais recente bolha produzida antes do início do trabalho especificado. Isto é feito para garantir que existe um conjunto de dados de referência **não vazio** quando o trabalho começa. Se não for possível encontrar um, o trabalho apresenta o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>` .

A [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) pode ser usada para orquestrar a tarefa de criar as bolhas atualizadas exigidas pelo Stream Analytics para atualizar definições de dados de referência. Data Factory é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Data Factory suporta [a ligação a um grande número de lojas de dados baseadas em nuvem e no local](../data-factory/copy-activity-overview.md) e movendo dados facilmente num horário regular que especifica. Para mais informações e orientações passo a passo sobre como configurar um pipeline data factory para gerar dados de referência para o Stream Analytics que atualiza sobre um horário pré-definido, confira esta [amostra GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Dicas sobre dados refrescantes de referência blob

1. Não substitua as bolhas de dados de referência por serem imutáveis.
2. A forma recomendada de atualizar os dados de referência é:
    * Utilize {date}/{time} no padrão do caminho
    * Adicione uma nova bolha utilizando o mesmo recipiente e padrão de caminho definido na entrada de trabalho
    * Utilize uma data/hora **superior** à especificada pela última bolha da sequência.
3. As bolhas de dados de referência **não** são encomendadas pelo tempo "Última Modificação" da bolha, mas apenas pela hora e data especificadas no nome blob utilizando as substituições {date} e {time} .
3. Para evitar ter de enumerar um grande número de bolhas, considere apagar bolhas muito antigas para as quais o processamento deixará de ser feito. Por favor, note que a ASA pode ir ter que reprocessar uma pequena quantidade em alguns cenários como um recomeço.

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Os dados de referência da Base de Dados Azure SQL são recuperados pelo seu trabalho de Stream Analytics e são armazenados como um instantâneo na memória para processamento. O instantâneo dos seus dados de referência também é armazenado num recipiente numa conta de armazenamento que especifica nas definições de configuração. O recipiente é criado automaticamente quando o trabalho começa. Se o trabalho for interrompido ou entrar em estado de falha, os recipientes criados automaticamente são eliminados quando o trabalho é reiniciado.  

Se os seus dados de referência forem um conjunto de dados em mudança lenta, é necessário refrescar periodicamente o instantâneo que é utilizado no seu trabalho. O Stream Analytics permite-lhe definir uma taxa de atualização quando configurar a sua ligação de entrada de base de dados Azure SQL. O tempo de execução do Stream Analytics irá consultar a sua Base de Dados Azure SQL no intervalo especificado pela taxa de atualização. A taxa de atualização mais rápida suportada é uma vez por minuto. Para cada atualização, o Stream Analytics armazena um novo instantâneo na conta de armazenamento fornecida.

O Stream Analytics fornece duas opções para consulta da sua Base de Dados Azure SQL. Uma consulta instantânea é obrigatória e deve ser incluída em cada trabalho. O Stream Analytics executa periodicamente a consulta instantânea com base no intervalo de atualização e utiliza o resultado da consulta (o instantâneo) como conjunto de dados de referência. A consulta instantânea deve encaixar na maioria dos cenários, mas se você encontrar problemas de desempenho com grandes conjuntos de dados e taxas de atualização rápida, você pode usar a opção de consulta delta. Consultas que demoram mais de 60 segundos a devolver o conjunto de dados de referência resultarão num tempo de paragem.

Com a opção de consulta delta, o Stream Analytics executa a consulta instantânea inicialmente para obter um conjunto de dados de referência de referência. Depois, o Stream Analytics executa periodicamente a consulta delta com base no intervalo de atualização para recuperar alterações incrementais. Estas alterações incrementais são continuamente aplicadas ao conjunto de dados de referência para mantê-lo atualizado. A utilização de consulta delta pode ajudar a reduzir o custo de armazenamento e as operações de I/S da rede.

### <a name="configure-sql-database-reference"></a>Configurar referência de base de dados SQL

Para configurar os dados de referência da Base de Dados SQL, primeiro é necessário criar a entrada de Dados de **Referência.** A tabela abaixo explica cada imóvel que terá de fornecer ao mesmo tempo que cria a entrada de dados de referência com a sua descrição. Para mais informações, consulte Utilize dados de referência de uma base de [dados SQL para um trabalho de Análise de Fluxo sinuoso](sql-reference-data.md).

Pode utilizar o Caso gerido pela Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como entrada de dados de referência. Tem de configurar o ponto final público na Instância gerida pela Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, configurar manualmente as seguintes definições no Azure Stream Analytics. A máquina virtual Azure que executa o SQL Server com uma base de dados anexada também é suportada configurando manualmente as definições abaixo.

|**Nome da Propriedade**|**Descrição**  |
|---------|---------|
|Alias de entrada|Um nome amigável que será usado na consulta de trabalho para fazer referência a esta entrada.|
|Subscrição|Escolher a sua subscrição|
|Base de Dados|A Base de Dados Azure SQL que contém os seus dados de referência. Para a Instância Gerida pela Base de Dados Azure SQL, é necessário especificar a porta 3342. Por exemplo, *sampleserver.public.database.windows.net,3342*|
|Nome de utilizador|O nome de utilizador associado à sua Base de Dados Azure SQL.|
|Palavra-passe|A palavra-passe associada à sua Base de Dados Azure SQL.|
|Atualizar periodicamente|Esta opção permite-lhe escolher uma taxa de atualização. A escolha "On" permitir-lhe-á especificar a taxa de atualização em DD:HH:MM.|
|Consulta instantânea|Esta é a opção de consulta padrão que recupera os dados de referência da sua Base de Dados SQL.|
|Consulta delta|Para cenários avançados com grandes conjuntos de dados e uma taxa de atualização curta, opte por adicionar uma consulta delta.|

## <a name="size-limitation"></a>Limitação de tamanho

Recomenda-se a utilização de conjuntos de dados de referência inferiores a 300 MB para um melhor desempenho. A utilização de dados de referência superiores a 300 MB é suportada em empregos com 6 USOu ou mais. Esta funcionalidade encontra-se em pré-visualização e não deve ser utilizada na produção. A utilização de dados de referência muito grandes pode ter impacto no desempenho do seu trabalho. À medida que a complexidade da consulta aumenta para incluir o processamento audato, tais como agregados janelas, juntas temporais e funções analíticas temporais, espera-se que o tamanho máximo suportado dos dados de referência diminua. Se o Azure Stream Analytics não conseguir carregar os dados de referência e realizar operações complexas, o trabalho ficará sem memória e falhará. Nesses casos, a métrica de utilização da SU % chegará a 100%.    

|**Número de Unidades de Streaming**  |**Tamanho Recomendado**  |
|---------|---------|
|1   |50 MB ou inferior   |
|3   |150 MB ou inferior   |
|6 e além   |300 MB ou menos. A utilização de dados de referência superiores a 300 MB é suportada na pré-visualização e pode ter impacto no desempenho do seu trabalho.    |

O suporte à compressão não está disponível para dados de referência.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Juntando vários conjuntos de dados de referência num trabalho
Pode juntar apenas uma entrada de fluxo com uma entrada de dados de referência num único passo da sua consulta. No entanto, pode juntar vários conjuntos de dados de referência, desfazendo a sua consulta em vários passos. Apresentamos um exemplo abaixo.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
