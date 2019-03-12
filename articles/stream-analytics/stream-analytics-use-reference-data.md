---
title: Utilizar dados de referência para pesquisas no Azure Stream Analytics
description: Este artigo descreve como utilizar os dados de referência para pesquisar ou correlacionar dados em design de consulta de uma tarefa Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 4ddbec6b163a939c1663630e39e89140ac6f7efe
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546488"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Utilização de dados de referência para pesquisas no Stream Analytics
Dados de referência (também conhecido como uma tabela de pesquisa) são um conjunto finito de dados que é estático ou de variação lenta por natureza, utilizado para efetuar uma pesquisa ou para correlacionar com seu fluxo de dados. Por exemplo, um cenário de IoT, poderia armazenar metadados sobre sensores (que não são alterados frequentemente) em dados de referência e associe-o com fluxos de dados de IoT em tempo real. O Azure Stream Analytics carrega dados de referência na memória para alcançar o processamento de fluxo de baixa latência. Para tornar usar dados de referência na sua tarefa do Azure Stream Analytics, geralmente utilizará um [associação de dados de referência](https://msdn.microsoft.com/library/azure/dn949258.aspx) na sua consulta. 

Stream Analytics suporta o armazenamento de Blobs do Azure e base de dados do Azure SQL como a camada de armazenamento para dados de referência. Pode também transformar e/ou copiar dados de referência para o armazenamento de Blobs do Azure Data Factory para usar [qualquer número de baseado na nuvem e arquivos de dados no local](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Dados de referência são modelados como uma sequência de blobs (definidos na configuração de entrada) em ordem ascendente da data/hora especificada no nome do blob. Ele **apenas** suporta a adição ao final da sequência usando uma data/hora **maior** que o especificado pelo blob de último na sequência.

### <a name="configure-blob-reference-data"></a>Configurar dados de referência de blob

Para configurar os seus dados de referência, tem primeiro de criar uma entrada que é do tipo **dados de referência**. A tabela abaixo explica cada propriedade que terá de fornecer ao criar os dados de referência de entrada com a respetiva descrição:

|**Nome da propriedade**  |**Descrição**  |
|---------|---------|
|Alias de Entrada   | Um nome amigável que será utilizado na consulta de trabalho para fazer referência a esta entrada.   |
|Conta de Armazenamento   | O nome da conta de armazenamento onde estão localizados os blobs. Se estiver na mesma subscrição que a tarefa do Stream Analytics, pode selecioná-lo a partir da lista pendente.   |
|Chave da Conta de Armazenamento   | A chave secreta associada à conta de armazenamento. Isso é preenchido automaticamente se a conta de armazenamento está na mesma subscrição que a tarefa de Stream Analytics.   |
|Contentor de Armazenamento   | Os contentores oferecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Ao carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob.   |
|Padrão do Caminho   | O caminho utilizado para localizar os blobs no contentor especificado. No caminho, pode optar por especificar uma ou mais instâncias das 2 variáveis seguintes:<BR>{date}, {time}<BR>Exemplo 1: products/{date}/{time}/product-list.csv<BR>Exemplo 2: products/{date}/product-list.csv<BR>Exemplo 3: produto-list.csv<BR><br> Se o blob não existir no caminho especificado, a tarefa do Stream Analytics aguardará indefinidamente para o blob para se tornar disponível.   |
|Formato de data [opcional]   | Se tiver utilizado a {date} dentro do padrão de caminho que especificou, em seguida, pode selecionar o formato de data em que os blobs são organizados na lista suspensa dos formatos suportados.<BR>Exemplo: AAAA/MM/DD, aaaa/MM/AAAA, etc.   |
|Formato de hora [opcional]   | Se tiver utilizado a {time} dentro do padrão de caminho que especificou, em seguida, pode selecionar o formato de hora em que os blobs são organizados na lista suspensa dos formatos suportados.<BR>Exemplo: HH, HH/mm ou HH-mm.  |
|Formato de serialização de eventos   | Para se certificar de que as suas consultas funcionam do modo esperado, o Stream Analytics precisa de saber que formato de serialização está a utilizar para os fluxos de dados recebidos. Para os dados de referência, os formatos suportados são CSV e JSON.  |
|Encoding   | UTF-8 é o único formato de codificação suportado neste momento.  |

### <a name="static-reference-data"></a>Dados de referência estáticos

Se seus dados de referência não são esperados para alterar, em seguida, o suporte para referência estática dados estiver ativados, especificando um caminho estático na configuração de entrada. O Azure Stream Analytics seleciona o blob a partir do caminho especificado. {date} e tokens de substituição de {time} não são necessárias. Dados de referência são imutáveis no Stream Analytics. Por conseguinte, a substituição de um blob de dados de referência estáticos não é recomendado.

### <a name="generate-reference-data-on-a-schedule"></a>Gerar dados de referência com base numa agenda

Se seus dados de referência são um conjunto de dados de mudança lenta, em seguida, o suporte para atualizar a referência de dados são ativados ao especificar um padrão de caminho na configuração de entrada com a {date} e {time} tokens de substituição. Stream Analytics seleciona as definições de dados de referência atualizadas com base neste padrão de caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data de **"Aaaa-MM-DD"** e um formato de hora de **"HH-mm"** instrui o Stream Analytics para recolher o blob atualizado `sample/2015-04-16/17-30/products.csv` às 17:00: 30 em 16 de Abril , Fuso horário UTC de 2015.

O Azure Stream Analytics analisa automaticamente para blobs de dados de referência atualizado num intervalo de um minuto.

> [!NOTE]
> Atualmente as tarefas do Stream Analytics procure a atualização de blob apenas quando o tempo de máquina avança para a hora codificada no nome do blob. Por exemplo, a tarefa irá procurar `sample/2015-04-16/17-30/products.csv` assim que possível, mas não anteriormente que 17:30, 16 de Abril de 2015 UTC fuso horário. Ele será *nunca* procure um blob com um período de tempo anterior ao último aquele que é detetado codificado.
> 
> Por exemplo, Assim que a tarefa de encontra o blob `sample/2015-04-16/17-30/products.csv` ele irá ignorar os ficheiros com uma data com codificação anteriores ao 17:30, 16 de Abril de 2015 por isso, se um atraso que chegam `sample/2015-04-16/17-25/products.csv` blob é criado no mesmo contentor a tarefa não as usará.
> 
> Da mesma forma se `sample/2015-04-16/17-30/products.csv` é produzido apenas em 10 às 18:03 16 de Abril de 2015, mas não blob com uma data anterior está presente no contentor, a tarefa irá utilizar este ficheiro a partir de 10 às 18:03 16 de Abril de 2015 e utilizar os dados de referência anterior até lá.
> 
> Uma exceção a isto é iniciada de quando a tarefa tem de voltar a processar dados para trás no tempo ou quando a tarefa é a primeira. Na hora de início, a tarefa está procurando o blob mais recente produzido antes da tarefa iniciar o período de tempo especificado. Isso é feito para garantir que existe uma **nula** referenciar o conjunto de dados quando a tarefa é iniciada. Se não for encontrado, a tarefa apresenta o diagnóstico seguinte: `Initializing input without a valid reference data blob for UTC time <start time>`.

[O Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) podem ser utilizados para orquestrar a tarefa de criar os blobs atualizados necessários pelo Stream Analytics para atualizar as definições de dados de referência. O Data Factory é um serviço de integração de dados com base na cloud que organiza e automatiza o movimento e a transformação dos dados. O Data Factory suporta [ligar a um grande número de cloud com base e no local arquivos de dados](../data-factory/copy-activity-overview.md) e mover dados facilmente a intervalos regulares que especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline do Data Factory para gerar dados de referência para o Stream Analytics, que é atualizada através de um agendamento predefinido, veja esta [exemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Obter sugestões sobre como atualizar os dados de referência de blob

1. Não substitua blobs de dados de referência, pois são imutáveis.
2. A forma recomendada para atualizar os dados de referência é:
    * Utilizar a {date} / {time} no padrão de caminho
    * Adicionar um novo blob usando o mesmo padrão de contentor e o caminho definido na tarefa de entrada
    * Utilizar uma data/hora **maior** que o especificado pelo blob de último na sequência.
3. Os blobs de dados de referência são **não** ordenados por tempo de "Última modificação" do blob, mas apenas pelo tempo e a data especificada no blob dê um nome com a {date} e {time} substituições.
3. Para evitar a necessidade de número grande de lista de blobs, considere eliminar blobs muito antigos para o qual processamento já não será feito. Tenha em atenção que o ASA pode ir a ter Reprocessar uma pequena quantidade em alguns cenários, como um reinício.

## <a name="azure-sql-database-preview"></a>Base de dados SQL do Azure (pré-visualização)

Dados de referência de base de dados SQL do Azure são obtidos pela sua tarefa do Stream Analytics e são armazenados como um instantâneo na memória para processamento. O instantâneo dos seus dados de referência também é armazenado num contentor numa conta de armazenamento que especificou nas definições de configuração. O contentor é criada automaticamente quando a tarefa é iniciada. Se a tarefa está parada ou entra num Estado com falhas, os contentores criada automaticamente são eliminados quando a tarefa for reiniciada.  

Se os dados de referência são um conjunto de dados de mudança lenta, terá de atualizar periodicamente o instantâneo que é utilizado na sua tarefa. Stream Analytics permite-lhe definir uma frequência de atualização, quando configurar a ligação de entrada de base de dados do Azure SQL. O tempo de execução do Stream Analytics irá consultar a base de dados do SQL do Azure no intervalo especificado pela taxa de atualização. A taxa mais rápida de atualização suportada é uma vez por minuto. Para cada atualização, o Stream Analytics armazena um novo instantâneo na conta de armazenamento fornecida.

Stream Analytics fornece duas opções para consultar a base de dados do SQL do Azure. Uma consulta do instantâneo é obrigatória e tem de ser incluída em cada tarefa. Stream Analytics executa a consulta do instantâneo periodicamente com base no seu intervalo de atualização e utiliza o resultado da consulta (instantâneo) como o conjunto de dados de referência. A consulta do instantâneo deve se ajustar a maioria dos cenários, mas caso se depare com problemas de desempenho com grandes conjuntos de dados e taxas de atualização rápida, pode utilizar a opção de consulta de delta. Consultas demorarem mais de 60 segundos para retornar o conjunto de dados de referência irão resultar num tempo limite.

Com a opção de consulta delta, o Stream Analytics executa a consulta do instantâneo inicialmente para obter um conjunto de dados de referência de linha de base. Depois, o Stream Analytics executa a consulta de delta periodicamente com base no seu intervalo de atualização para obter as alterações incrementais. Estas alterações incrementais continuamente são aplicadas ao conjunto de dados de referência para mantê-lo atualizado. Utilização da consulta delta pode ajudar a reduzir os custos de armazenamento e as operações de e/s de rede.

### <a name="configure-sql-database-reference"></a>Configurar a referência de base de dados SQL

Para configurar os seus dados de referência de base de dados SQL, tem primeiro de criar **dados de referência** entrada. A tabela abaixo explica cada propriedade que terá de fornecer ao criar os dados de referência de entrada com sua descrição. Para obter mais informações, consulte [dados de referência de utilização de um banco de dados SQL para uma tarefa do Azure Stream Analytics](sql-reference-data.md).

|**Nome da propriedade**|**Descrição**  |
|---------|---------|
|Alias de entrada|Um nome amigável que será utilizado na consulta de trabalho para fazer referência a esta entrada.|
|Subscrição|Escolher a sua subscrição|
|Base de Dados|O Azure SQL Database que contém os dados de referência.|
|Nome de utilizador|O nome de utilizador associada à sua base de dados do SQL do Azure.|
|Palavra-passe|A palavra-passe associada à sua base de dados do SQL do Azure.|
|Atualizar periodicamente|Esta opção permite-lhe escolher uma taxa de atualização. Escolher "Ativado" permitirá que especifique a taxa de atualização no DD:HH:MM.|
|Consulta de instantâneos|Esta é a opção de consulta padrão que obtém os dados de referência da base de dados SQL.|
|Consulta delta|Para cenários avançados com grandes conjuntos de dados e uma pequena taxa de atualização, optar por adicionar uma consulta de delta.|

## <a name="size-limitation"></a>Limite de tamanho

Stream Analytics suporta dados de referência com **tamanho máximo de 300 MB**. O limite de 300 MB de tamanho máximo dos dados de referência é alcançável apenas com consultas simples. À medida que aumenta a complexidade da consulta para incluir o processamento com monitoração de estado, por exemplo, agregados em janelas, associações temporais e funções de análise temporais, espera-se que o tamanho máximo suportado de diminuições de dados de referência. Se o Azure Stream Analytics não é possível carregar os dados de referência e executar operações complexas, a tarefa irá ficar sem memória e falhar. Nesses casos, a métrica % Utilization de SU atingirá 100%.    

|**Número de unidades de transmissão em fluxo**  |**Tamanho máximo do aprox. suportado (em MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 e muito mais   |300   |

Aumente o número de unidades de transmissão em fluxo de mensagens em fila de uma tarefa para além dos 6 não aumenta o tamanho máximo suportado de dados de referência.

Suporte para compressão não está disponível para os dados de referência. 

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Quickstart: Criar uma tarefa de Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
