---
title: Exportar usando Stream Analytics da Azure Application Insights | Microsoft Docs
description: O Stream Analytics pode transformar, filtrar e encaminhar continuamente os dados que exporta do Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: c8486d7e5656a7770aec4a50739d3a9160e123e3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584327"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Utilize o Stream Analytics para processar dados exportados a partir de Insights de Aplicação
[O Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) é a ferramenta ideal para o processamento de dados [exportados do Application Insights](export-telemetry.md). Stream Analytics pode extrair dados de uma variedade de fontes. Pode transformar e filtrar os dados, e depois encaminhá-lo para uma variedade de pias.

Neste exemplo, vamos criar um adaptador que retira dados da Application Insights, renomeia e processa alguns dos campos, e canaliza-os para o Power BI.

> [!WARNING]
> Existem formas muito melhores e mais fáceis [de exibir dados de Insights de Aplicação no Power BI](./export-power-bi.md). O caminho aqui ilustrado é apenas um exemplo para ilustrar como processar dados exportados.
> 
> 

![Diagrama de bloco para exportação através de SA para PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Criar armazenamento em Azure
A exportação contínua produz sempre dados para uma conta de Armazenamento Azure, por isso é necessário criar o armazenamento primeiro.

1. Crie uma conta de armazenamento "clássica" na sua subscrição no [portal Azure.](https://portal.azure.com)
   
   ![No portal Azure, escolha Novos, Dados, Armazenamento](./media/export-stream-analytics/030.png)
2. Criar um contentor
   
    ![No novo armazenamento, selecione Recipientes, clique no azulejo de Recipientes e, em seguida, Adicione](./media/export-stream-analytics/040.png)
3. Copie a chave de acesso ao armazenamento
   
    Em breve, vai precisar dele para configurar a entrada para o serviço de análise de fluxo.
   
    ![No armazenamento, abra as definições, chaves e pegue uma cópia da chave de acesso primário](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicie a exportação contínua para o armazenamento da Azure
[A exportação contínua](export-telemetry.md) transfere dados da Application Insights para o armazenamento do Azure.

1. No portal Azure, consulte o recurso Application Insights que criou para a sua aplicação.
   
    ![Escolha Procurar, Insights de Aplicação, a sua aplicação](./media/export-stream-analytics/050.png)
2. Criar uma exportação contínua.
   
    ![Escolha Configurações, Exportação Contínua, Adicionar](./media/export-stream-analytics/060.png)

    Selecione a conta de armazenamento que criou anteriormente:

    ![Definir o destino de exportação](./media/export-stream-analytics/070.png)

    Desação os tipos de eventos que pretende ver:

    ![Escolha tipos de eventos](./media/export-stream-analytics/080.png)

1. Deixe alguns dados acumularem-se. Sente-se e deixe as pessoas usarem a sua aplicação por um tempo. A telemetria chegará e verá gráficos estatísticos em [exploradores métricos](../essentials/metrics-charts.md) e eventos individuais em [pesquisa de diagnóstico.](./diagnostic-search.md) 
   
    Além disso, os dados serão exportadas para o seu armazenamento. 
2. Inspecione os dados exportados. No Estúdio Visual, escolha **View / Cloud Explorer,** e abra O Azure / Storage. (Se não tiver esta opção de menu, tem de instalar o Azure SDK: Abra o diálogo do Novo Projeto e abra o Visual C# / Cloud / Obtenha o Microsoft Azure SDK para .NET.)
   
    ![Screenshot mostrando como definir os tipos de eventos que você deseja ver.](./media/export-stream-analytics/04-data.png)
   
    Tome nota da parte comum do nome do caminho, que é derivado do nome da aplicação e da chave de instrumentação. 

Os eventos são escritos para ficheiros blob no formato JSON. Cada ficheiro pode conter um ou mais eventos. Então gostaríamos de ler os dados do evento e filtrar os campos que queremos. Há todo o tipo de coisas que poderíamos fazer com os dados, mas o nosso plano hoje é usar o Stream Analytics para canalizar os dados para o Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Criar um exemplo de Azure Stream Analytics
A partir do [portal Azure,](https://portal.azure.com/)selecione o serviço Azure Stream Analytics e crie um novo trabalho stream Analytics:

![Screenshot que mostra a página principal para criar o trabalho stream Analytics no portal Azure.](./media/export-stream-analytics/SA001.png)

![Screenshot que mostra os detalhes necessários ao criar um novo trabalho stream Analytics.](./media/export-stream-analytics/SA002.png)

Quando o novo trabalho for criado, selecione **Ir para o recurso**.

![Screenshot que mostra a mensagem recebida quando a nova implementação de trabalho stream Analytics é bem sucedida.](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Adicione uma nova entrada

![Screenshot que mostra como adicionar entradas ao trabalho stream Analytics.](./media/export-stream-analytics/SA004.png)

Desacalça-o para obter a entrada da sua bolha de exportação contínua:

![Screenshot que mostra configurar o trabalho stream Analytics para obter a entrada de uma bolha de exportação contínua.](./media/export-stream-analytics/SA0005.png)

Agora vai precisar da chave de acesso primário da sua conta de armazenamento, que observou anteriormente. Desa esta medida como a chave de conta de armazenamento.

### <a name="set-path-prefix-pattern"></a>Definir padrão de prefixo de caminho

**Certifique-se de que define o formato de data para YYYY-MM-DD (com traços).**

O Padrão do Prefixo do Caminho especifica onde o Stream Analytics encontra os ficheiros de entrada no armazenamento. É necessário defini-lo para corresponder à forma como a Exportação Contínua armazena os dados. Definir assim:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

Neste exemplo:

* `webapplication27`é o nome do recurso Application Insights **todos os casos inferiores.**
* `1234...` é a chave de instrumentação do recurso Application Insights, **omitindo traços**. 
* `PageViews` é o tipo de dados que pretende analisar. Os tipos disponíveis dependem do filtro que definiu na Exportação Contínua. Examinar os dados exportados para ver os outros tipos disponíveis e ver o modelo de dados de [exportação.](export-data-model.md)
* `/{date}/{time}` é um padrão escrito literalmente.

> [!NOTE]
> Inspecione o armazenamento para se certificar de que faz o caminho certo.
> 

## <a name="add-new-output"></a>Adicionar nova saída
Agora selecione o seu trabalho > **Outputs**  >  **Add**.

![Screenshot que mostra selecionar o seu trabalho Stream Analytics para adicionar uma nova saída.](./media/export-stream-analytics/SA006.png)


![Selecione o novo canal, clique em Saídas, Adicionar, Power BI](./media/export-stream-analytics/SA010.png)

Forneça o seu **trabalho ou conta escolar** para autorizar o Stream Analytics a aceder ao seu recurso Power BI. Em seguida, inventa um nome para a saída, e para o conjunto de dados e tabela do Power BI alvo.

## <a name="set-the-query"></a>Definir a consulta
A consulta rege a tradução da entrada para a saída.

Utilize a função Teste para verificar se obtém a saída certa. Dê-lhe os dados da amostra que recolheu da página de entradas. 

### <a name="query-to-display-counts-of-events"></a>Consulta para exibir contagem de eventos
Cole esta consulta:

```SQL
SELECT
  flat.ArrayValue.name,
  count(*)
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.[event]) as flat
GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* exportação-entrada é o pseudónimo que demos à entrada de fluxo
* pbi-output é o pseudónimo de saída que definimos
* Utilizamos [OUTER APPLY GetElements](/stream-analytics-query/apply-azure-stream-analytics) porque o nome do evento está numa matriz JSON aninhada. Em seguida, o Select escolhe o nome do evento, juntamente com uma contagem do número de casos com esse nome no período de tempo. A cláusula [Grupo Por](/stream-analytics-query/group-by-azure-stream-analytics) Cláusula agruta os elementos em períodos de tempo de um minuto.

### <a name="query-to-display-metric-values"></a>Consulta para mostrar valores métricos

```SQL
SELECT
  A.context.data.eventtime,
  avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.context.custom.metrics) as flat
GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime
```

* Esta consulta perfura a telemetria métrica para obter o tempo do evento e o valor métrico. Os valores métricos estão dentro de uma matriz, por isso usamos o padrão OUTER APPLY GetElements para extrair as linhas. "myMetric" é o nome da métrica neste caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Consulta para incluir valores de propriedades de dimensão

```SQL
WITH flat AS (
SELECT
  MySource.context.data.eventTime as eventTime,
  InstanceId = MyDimension.ArrayValue.InstanceId.value,
  BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
FROM MySource
OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
)
SELECT
  eventTime,
  InstanceId,
  BusinessUnitId
INTO AIOutput
FROM flat
```

* Esta consulta inclui valores das propriedades de dimensão sem depender de uma determinada dimensão estar num índice fixo na matriz de dimensão.

## <a name="run-the-job"></a>Executar a tarefa
Pode selecionar uma data no passado para iniciar o trabalho. 

![Selecione o trabalho e clique em Consulta. Cole a amostra abaixo.](./media/export-stream-analytics/SA008.png)

Espera até o trabalho estar a correr.

## <a name="see-results-in-power-bi"></a>Ver resultados em Power BI
> [!WARNING]
> Existem formas muito melhores e mais fáceis [de exibir dados de Insights de Aplicação no Power BI](./export-power-bi.md). O caminho aqui ilustrado é apenas um exemplo para ilustrar como processar dados exportados.
> 
> 

Open Power BI com o seu trabalho ou conta escolar, e selecione o conjunto de dados e tabela que definiu como a saída do trabalho stream Analytics.

![No Power BI, selecione o seu conjunto de dados e campos.](./media/export-stream-analytics/200.png)

Agora pode utilizar este conjunto de dados em relatórios e dashboards no [Power BI](https://powerbi.microsoft.com).

![A screenshot mostra um exemplo de relatório feito a partir de um conjunto de dados no Power BI.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Não existem dados?
* Verifique se [define corretamente o formato da data](#set-path-prefix-pattern) para YYYY-MM-DD (com traços).

## <a name="video"></a>Vídeo
Noam Ben Zeev mostra como processar dados exportados usando o Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Exportação contínua](export-telemetry.md)
* [Referência detalhada do modelo de dados para os tipos e valores da propriedade.](export-data-model.md)
* [Application Insights](./app-insights-overview.md)

