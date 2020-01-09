---
title: Usar dados de referência para pesquisas no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para pesquisar ou correlacionar dados em um design de consulta de Azure Stream Analytics trabalho.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b3808524706b13761dd8eccffa301c602d08f481
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369569"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Usando dados de referência para pesquisas no Stream Analytics

Os dados de referência (também conhecidos como uma tabela de pesquisa) são um conjunto de dados finito estático ou com alteração lenta por natureza, usados para executar uma pesquisa ou para aumentar seus fluxos de dados. Por exemplo, em um cenário de IoT, você pode armazenar metadados sobre sensores (que não são alterados com frequência) em dados de referência e associá-los a fluxos de dados de IoT em tempo real. Azure Stream Analytics carrega dados de referência na memória para obter processamento de fluxo de baixa latência. Para fazer uso de dados de referência em seu trabalho de Azure Stream Analytics, você geralmente usará uma [junção de dados de referência](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) em sua consulta. 

O Stream Analytics dá suporte ao armazenamento de BLOBs do Azure e ao banco de dados SQL do Azure como a camada de armazenamento para a referência Você também pode transformar e/ou copiar dados de referência para o armazenamento de blobs de Azure Data Factory para usar [qualquer número de armazenamentos de dados locais e baseados em nuvem](../data-factory/copy-activity-overview.md).

## <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

Os dados de referência são modelados como uma sequência de BLOBs (definidos na configuração de entrada) em ordem crescente da data/hora especificada no nome do blob. Ele **só** dá suporte à adição ao final da sequência usando uma data/hora **maior** do que aquela especificada pelo último blob na sequência.

### <a name="configure-blob-reference-data"></a>Configurar dados de referência de BLOB

Para configurar seus dados de referência, primeiro você precisa criar uma entrada que seja do tipo **dados de referência**. A tabela a seguir explica cada propriedade que você precisará fornecer ao criar a entrada de dados de referência com sua descrição:

|**Nome da propriedade**  |**Descrição**  |
|---------|---------|
|Alias de Entrada   | Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.   |
|Conta de Armazenamento   | O nome da conta de armazenamento na qual os BLOBs estão localizados. Se estiver na mesma assinatura que seu trabalho de Stream Analytics, você poderá selecioná-lo na lista suspensa.   |
|Chave da Conta de Armazenamento   | A chave secreta associada à conta de armazenamento. Isso será preenchido automaticamente se a conta de armazenamento estiver na mesma assinatura que o seu trabalho de Stream Analytics.   |
|Contentor de armazenamento   | Os contentores oferecem um agrupamento lógico para blobs armazenados no serviço de Blobs do Microsoft Azure. Ao carregar um blob para o serviço de BLOBs, tem de especificar um contentor para esse blob.   |
|Padrão do Caminho   | O caminho usado para localizar seus BLOBs no contêiner especificado. No caminho, você pode optar por especificar uma ou mais instâncias das duas variáveis a seguir:<BR>{date}, {time}<BR>Exemplo 1: Products/{Date}/{Time}/Product-List. csv<BR>Exemplo 2: Products/{Date}/Product-List. csv<BR>Exemplo 3: Product-List. csv<BR><br> Se o BLOB não existir no caminho especificado, o trabalho de Stream Analytics aguardará indefinidamente que o blob se torne disponível.   |
|Formato de data [opcional]   | Se você tiver usado {Date} no padrão de caminho que você especificou, poderá selecionar o formato de data no qual os BLOBs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: AAAA/MM/DD, MM/DD/AAAA etc.   |
|Formato de hora [opcional]   | Se você tiver usado {time} no padrão de caminho que você especificou, poderá selecionar o formato de hora no qual os BLOBs são organizados na lista suspensa de formatos com suporte.<BR>Exemplo: HH, HH/mm ou HH-mm.  |
|Formato de serialização de evento   | Para garantir que suas consultas funcionem da maneira esperada, Stream Analytics precisa saber qual formato de serialização você está usando para os fluxos de dados de entrada. Para dados de referência, os formatos com suporte são CSV e JSON.  |
|Encoding   | UTF-8 é o único formato de codificação suportado neste momento.  |

### <a name="static-reference-data"></a>Dados de referência estática

Se não for esperado que os dados de referência sejam alterados, o suporte para dados de referência estática será habilitado especificando um caminho estático na configuração de entrada. Azure Stream Analytics seleciona o blob do caminho especificado. os tokens de substituição {Date} e {time} não são necessários. Como os dados de referência são imutáveis em Stream Analytics, não é recomendável substituir um blob de dados de referência estática.

### <a name="generate-reference-data-on-a-schedule"></a>Gerar dados de referência em uma agenda

Se os dados de referência forem um conjunto de dados de alteração lenta, o suporte para atualizar os dados de referência será habilitado especificando um padrão de caminho na configuração de entrada usando os tokens de substituição {Date} e {time}. Stream Analytics seleciona as definições de dados de referência atualizadas com base nesse padrão de caminho. Por exemplo, um padrão de `sample/{date}/{time}/products.csv` com um formato de data **"aaaa-mm-dd"** e um formato de hora **"hh-mm"** instrui Stream Analytics a pegar o `sample/2015-04-16/17-30/products.csv` de blob atualizado às 5:30, em 16 de abril, 2015 fuso horário UTC.

Azure Stream Analytics examina automaticamente os blobs de dados de referência atualizados em um intervalo de um minuto. Se um blob com carimbo de data/hora 10:30:00 for carregado com um pequeno atraso (por exemplo, 10:30:30), você observará um pequeno atraso no trabalho Stream Analytics referenciando esse BLOB. Para evitar esses cenários, é recomendável carregar o blob antes do tempo efetivo de destino (10:30:00 neste exemplo) para permitir que o trabalho de Stream Analytics tempo suficiente para descobri-lo e carregá-lo na memória e executar operações. 

> [!NOTE]
> Atualmente Stream Analytics trabalhos procuram a atualização do blob somente quando a hora do computador avança para o horário codificado no nome do blob. Por exemplo, o trabalho procurará `sample/2015-04-16/17-30/products.csv` assim que possível, mas não antes de 5:30 PM em 16 de abril, 2015 fuso horário UTC. Ele *nunca* procurará um blob com uma hora codificada anterior à última descoberta.
> 
> Por exemplo, depois que o trabalho encontrar o blob `sample/2015-04-16/17-30/products.csv` ele ignorará todos os arquivos com uma data codificada anterior a 5:30 PM 16 de abril de 2015, portanto, se um blob de chegada de `sample/2015-04-16/17-25/products.csv` for criado no mesmo contêiner, o trabalho não o usará.
> 
> Da mesma forma, se `sample/2015-04-16/17-30/products.csv` for produzido apenas às 10:03 de abril, 2015, mas nenhum blob com uma data anterior estiver presente no contêiner, o trabalho usará esse arquivo a partir de 10:03 às de abril, 2015 e usará os dados de referência anteriores até lá.
> 
> Uma exceção a isso é quando o trabalho precisa reprocessar dados de volta no tempo ou quando o trabalho é iniciado pela primeira vez. Na hora de início, o trabalho está procurando o blob mais recente produzido antes da hora de início do trabalho especificada. Isso é feito para garantir que haja um conjunto de dados de referência **não vazio** quando o trabalho for iniciado. Se não for possível encontrar um, o trabalho exibirá o seguinte diagnóstico: `Initializing input without a valid reference data blob for UTC time <start time>`.

[Azure data Factory](https://azure.microsoft.com/documentation/services/data-factory/) pode ser usado para orquestrar a tarefa de criar os BLOBs atualizados exigidos pelo Stream Analytics para atualizar as definições de dados de referência. O Data Factory é um serviço de integração de dados com base na cloud que organiza e automatiza o movimento e a transformação dos dados. O Data Factory dá suporte [à conexão com um grande número de armazenamentos de dados locais e baseados em nuvem](../data-factory/copy-activity-overview.md) e na movimentação fácil de dados em um agendamento regular que você especificar. Para obter mais informações e orientações passo a passo sobre como configurar um pipeline de Data Factory para gerar dados de referência para Stream Analytics que se atualiza em uma agenda predefinida, confira este [exemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs).

### <a name="tips-on-refreshing-blob-reference-data"></a>Dicas sobre como atualizar dados de referência de BLOB

1. Não substitua blobs de dados de referência, pois eles são imutáveis.
2. A maneira recomendada para atualizar os dados de referência é:
    * Use {date}/{time} no padrão de caminho
    * Adicionar um novo BLOB usando o mesmo padrão de contêiner e caminho definido na entrada do trabalho
    * Use uma data/hora **maior** do que aquela especificada pelo último blob na sequência.
3. Os blobs de dados de referência **não** são ordenados pela hora da "última modificação" do blob, mas apenas pela hora e data especificadas no nome do blob usando as substituições {Date} e {time}.
3. Para evitar a necessidade de listar um grande número de BLOBs, considere excluir BLOBs muito antigos para os quais o processamento não será mais feito. Observe que o ASA pode precisar reprocessar uma pequena quantidade em alguns cenários, como uma reinicialização.

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

O banco de dados SQL do Azure é recuperado por seu trabalho de Stream Analytics e é armazenado como um instantâneo na memória para processamento. O instantâneo dos dados de referência também é armazenado em um contêiner em uma conta de armazenamento que você especifica nas definições de configuração. O contêiner é criado automaticamente quando o trabalho é iniciado. Se o trabalho for interrompido ou entrar em um estado de falha, os contêineres criados automaticamente serão excluídos quando o trabalho for reiniciado.  

Se os dados de referência forem um conjunto de dados de alteração lenta, você precisará atualizar periodicamente o instantâneo que é usado em seu trabalho. Stream Analytics permite que você defina uma taxa de atualização ao configurar a conexão de entrada do banco de dados SQL do Azure. O tempo de execução do Stream Analytics consultará o banco de dados SQL do Azure no intervalo especificado pela taxa de atualização. A taxa de atualização mais rápida com suporte é uma vez por minuto. Para cada atualização, Stream Analytics armazena um novo instantâneo na conta de armazenamento fornecida.

Stream Analytics fornece duas opções para consultar o banco de dados SQL do Azure. Uma consulta de instantâneo é obrigatória e deve ser incluída em cada trabalho. Stream Analytics executa a consulta de instantâneo periodicamente com base no seu intervalo de atualização e usa o resultado da consulta (o instantâneo) como o conjunto de dados de referência. A consulta de instantâneo deve se ajustar à maioria dos cenários, mas se você encontrar problemas de desempenho com grandes conjuntos de dados e taxas de atualização rápida, poderá usar a opção de consulta Delta. As consultas que levam mais de 60 segundos para retornar o conjunto de dados de referência resultarão em um tempo limite.

Com a opção de consulta Delta, Stream Analytics executa a consulta de instantâneo inicialmente para obter um conjunto de dados de referência de linha de base. Depois, Stream Analytics executa periodicamente a consulta Delta com base em seu intervalo de atualização para recuperar alterações incrementais. Essas alterações incrementais são aplicadas continuamente ao conjunto de dados de referência para mantê-lo atualizado. O uso da consulta Delta pode ajudar a reduzir O custo de armazenamento e as operações de e/s de rede.

### <a name="configure-sql-database-reference"></a>Configurar referência do banco de dados SQL

Para configurar os dados de referência do banco de dado SQL, primeiro você precisa criar entrada de **dados de referência** . A tabela a seguir explica cada propriedade que você precisará fornecer ao criar a entrada de dados de referência com sua descrição. Para obter mais informações, consulte [usar dados de referência de um banco de dado SQL para um trabalho Azure Stream Analytics](sql-reference-data.md).

Você pode usar [instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) como uma entrada de dados de referência. Você precisa [Configurar o ponto de extremidade público em instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) e, em seguida, definir manualmente as configurações a seguir em Azure Stream Analytics. A máquina virtual do Azure que executa o SQL Server com um banco de dados anexado também é suportada Configurando manualmente as configurações abaixo.

|**Nome da propriedade**|**Descrição**  |
|---------|---------|
|Alias de entrada|Um nome amigável que será usado na consulta de trabalho para fazer referência a essa entrada.|
|Subscrição|Escolher a sua subscrição|
|Base de Dados|O banco de dados SQL do Azure que contém os sua referência. Por Instância Gerenciada do Banco de Dados SQL do Azure, é necessário especificar a porta 3342. Por exemplo, *sampleserver. Public. Database. Windows. net, 3342*|
|Nome de utilizador|O nome de usuário associado ao seu banco de dados SQL do Azure.|
|Palavra-passe|A senha associada ao banco de dados SQL do Azure.|
|Atualizar periodicamente|Essa opção permite que você escolha uma taxa de atualização. Escolher "ativado" permitirá que você especifique a taxa de atualização em DD: HH: MM.|
|Consulta de instantâneo|Essa é a opção de consulta padrão que recupera os dados de referência do banco de dado SQL.|
|Consulta Delta|Para cenários avançados com grandes conjuntos de dados e uma taxa de atualização curta, escolha Adicionar uma consulta Delta.|

## <a name="size-limitation"></a>Limitação de tamanho

Stream Analytics dá suporte a dados de referência com o **tamanho máximo de 300 MB**. O limite de 300 MB de tamanho máximo dos dados de referência é atingível somente com consultas simples. À medida que a complexidade da consulta aumenta para incluir o processamento com estado, como agregações em janela, junções temporais e funções analíticas temporais, espera-se que o tamanho máximo com suporte dos dados de referência seja reduzido. Se Azure Stream Analytics não puder carregar os dados de referência e executar operações complexas, o trabalho ficará sem memória e falhará. Nesses casos, a métrica de utilização de% de SU atingirá 100%.    

|**Número de unidades de streaming**  |**Tamanho máximo aprox. com suporte (em MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 e além   |300   |

O aumento do número de unidades de streaming de um trabalho além de 6 não aumenta o tamanho máximo com suporte dos dados de referência.

Suporte para compressão não está disponível para os dados de referência. 

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início rápido: Criar uma tarefa do Stream Analytics com o portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
