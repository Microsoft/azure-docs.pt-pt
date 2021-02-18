---
title: Utilize a API do Colecionador de Dados para criar um pipeline de dados
description: Pode utilizar a Azure Monitor HTTP Data Collector API para adicionar dados POST JSON ao espaço de trabalho Log Analytics de qualquer cliente que possa ligar para a API REST. Este artigo descreve como fazer upload de dados armazenados em ficheiros de forma automatizada.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ac2b79046718fe45ad0dad0396b6f7653efbb779
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618341"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Criar um pipeline de dados com a API do Colecionador de Dados

A [Azure Monitor Data Collector API](data-collector-api.md) permite-lhe importar quaisquer dados de registo personalizados num espaço de trabalho do Log Analytics no Azure Monitor. Os únicos requisitos são que os dados sejam formatados por JSON e divididos em segmentos de 30 MB ou menos. Trata-se de um mecanismo completamente flexível que pode ser ligado de muitas formas: desde o envio direto de dados da sua aplicação até carregamentos de adhoc pontuais. Este artigo irá delinear alguns pontos de partida para um cenário comum: a necessidade de carregar dados armazenados em ficheiros numa base regular e automatizada. Embora o gasoduto aqui apresentado não seja o mais performante ou otimizado de outra forma, pretende-se servir de ponto de partida para a construção de um oleoduto de produção próprio.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Problema de exemplo
Para o resto deste artigo, examinaremos os dados de visualização da página em Insights de Aplicação. No nosso cenário hipotético, queremos correlacionar as informações geográficas recolhidas por padrão pela Application Insights SDK a dados personalizados que contenham a população de todos os países/regiões do mundo, com o objetivo de identificar onde devemos gastar mais dólares de marketing. 

Utilizamos uma fonte pública de dados, como as [Perspetivas da População Mundial das Nações Unidas](https://esa.un.org/unpd/wpp/) para este fim. Os dados terão o seguinte esquema simples:

![Exemplo de esquema simples](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

No nosso exemplo, assumimos que vamos enviar um novo ficheiro com os dados do ano passado assim que este estiver disponível.

## <a name="general-design"></a>Design geral
Estamos a usar uma lógica clássica do tipo ETL para desenhar o nosso oleoduto. A arquitetura será a seguinte:

![Arquitetura de gasoduto de recolha de dados](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Este artigo não abrange como criar dados ou [carregá-lo para uma conta de Armazenamento Azure Blob](../../storage/blobs/storage-upload-process-images.md). Em vez disso, apanhamos o fluxo assim que um novo ficheiro é enviado para a bolha. A partir daqui:

1. Um processo irá detetar que novos dados foram carregados.  O nosso exemplo utiliza uma [App Azure Logic](../../logic-apps/logic-apps-overview.md), que disponibilizou um gatilho para detetar novos dados a serem enviados para uma bolha.

2. Um processador lê estes novos dados e converte-os em JSON, o formato exigido pelo Azure Monitor Neste exemplo, utilizamos uma [Função Azure](../../azure-functions/functions-overview.md) como uma forma leve e económica de executar o nosso código de processamento. A função é iniciada pela mesma App Lógica que usamos para detetar os novos dados.

3. Finalmente, uma vez que o objeto JSON está disponível, é enviado para o Azure Monitor. A mesma Aplicação Lógica envia os dados para o Azure Monitor utilizando a atividade incorporada no Log Analytics Data Collector.

Embora a configuração detalhada do armazenamento do blob, Da Aplicação Lógica ou da Função Azure não esteja delineada neste artigo, estão disponíveis instruções detalhadas nas páginas específicas dos produtos.

Para monitorizar este pipeline, utilizamos o Application Insights para monitorizar [os nossos detalhes](../../azure-functions/functions-monitoring.md)da Função Azure aqui , e o Azure Monitor para monitorizar os detalhes da nossa App Lógica [aqui.](../../logic-apps/monitor-logic-apps-log-analytics.md) 

## <a name="setting-up-the-pipeline"></a>Criação do oleoduto
Para definir o gasoduto, certifique-se primeiro de que tem o seu recipiente de bolhas criado e configurado. Da mesma forma, certifique-se de que o espaço de trabalho Log Analytics para onde pretende enviar os dados é criado.

## <a name="ingesting-json-data"></a>Ingerir dados JSON
Ingerir dados JSON é trivial com as Apps Lógicas, e como nenhuma transformação precisa de ocorrer, podemos envolver todo o pipeline numa única App Lógica. Uma vez configurado tanto o recipiente blob como o espaço de trabalho Log Analytics, crie uma nova App Lógica e configure-o da seguinte forma:

![Exemplo de fluxo de trabalho de apps lógicas](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Guarde a sua App Lógica e proceda a testá-la.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingerir XML, CSV ou outros formatos de dados
A Logic Apps hoje em dia não tem capacidades incorporadas para transformar facilmente XML, CSV ou outros tipos em formato JSON. Portanto, precisamos de usar outro meio para completar esta transformação. Para este artigo, utilizamos as capacidades de computação sem servidor das Funções Azure como uma forma muito leve e amiga dos custos de o fazer. 

Neste exemplo, analisamos um ficheiro CSV, mas qualquer outro tipo de ficheiro pode ser processado de forma semelhante. Basta modificar a parte deserializante da Função Azure para refletir a lógica correta para o seu tipo de dados específico.

1.  Crie uma nova Função Azure, utilizando o tempo de execução v1 da Função e baseado no consumo quando solicitado.  Selecione o modelo **de gatilho HTTP** direcionado para C# como ponto de partida que configura as suas ligações conforme exigimos. 
2.  A partir do separador **'Ver Ficheiros'** no painel direito, crie um novo ficheiro chamado **project.js** e cole o seguinte código a partir de pacotes NuGet que estamos a usar:

    ![Projeto exemplo Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Mude para **run.csx** do painel direito e substitua o código predefinido pelo seguinte. 

    >[!NOTE]
    >Para o seu projeto, tem de substituir o modelo de gravação (a classe "PopulationRecord") pelo seu próprio esquema de dados.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Salve a sua função.
5. Teste a função para se certificar de que o código está a funcionar corretamente. Mude para o **separador Teste** no painel direito, configurando o teste da seguinte forma. Coloque um link para uma bolha com dados de amostra na caixa de texto **do corpo request.** Depois de clicar em **Run,** deverá ver a saída JSON na caixa **de saída:**

    ![Código de teste de Apps de Função](./media/create-pipeline-datacollector-api/functions-test-01.png)

Agora precisamos voltar e modificar a App Lógica que começamos a construir mais cedo para incluir os dados ingeridos e convertidos para o formato JSON.  Utilizando o View Designer, configuure-se da seguinte forma e, em seguida, guarde a sua App Lógica:

![Fluxo de trabalho de Apps lógicas exemplo completo](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testar o oleoduto
Agora pode carregar um novo ficheiro para a bolha configurada mais cedo e tê-lo monitorizado pela sua App Lógica. Em breve, deverá ver uma nova instância da Aplicação Lógica a arrancar, chamar para a sua Função Azure e, em seguida, enviar os dados com sucesso para o Azure Monitor. 

>[!NOTE]
>Pode levar até 30 minutos para que os dados apareçam no Azure Monitor na primeira vez que enviar um novo tipo de dados.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correlacionar com outros dados em Log Analytics e Application Insights
Para completar o nosso objetivo de correlacionar os dados da página de Insights de Aplicação com os dados da população que ingerimos a partir da nossa fonte de dados personalizada, execute a seguinte consulta a partir da janela de trabalho do Application Insights Analytics ou do Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A saída deve mostrar as duas fontes de dados agora unidas.  

![Correlacionar dados desunidão num exemplo de resultado de pesquisa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Melhorias sugeridas para um gasoduto de produção
Este artigo apresentou um protótipo de trabalho, a lógica por trás da qual pode ser aplicado a uma verdadeira solução de qualidade de produção. Para uma solução de qualidade de produção, recomenda-se as seguintes melhorias:

* Adicione o tratamento de erros e relemisse a lógica na sua Aplicação lógica e função.
* Adicione lógica para garantir que o limite de chamada API de 30MB/single Log Analytics Ingestion não seja ultrapassado. Divida os dados em segmentos menores, se necessário.
* Estabeleça uma política de limpeza no seu armazenamento de bolhas. Uma vez enviado com sucesso para o espaço de trabalho Log Analytics, a menos que queira manter os dados brutos disponíveis para fins de arquivo, não há razão para continuar a guardá-lo. 
* Verifique se a monitorização está ativada em todo o gasoduto, adicionando pontos de rastreio e alertas conforme apropriado.
* Alavancar o controlo de fonte para gerir o código para a sua função e a Aplicação Lógica.
* Certifique-se de que é seguida uma política de gestão de mudanças adequadas, de modo a que, se o esquema mudar, a função e as Aplicações Lógicas sejam modificadas em conformidade.
* Se estiver a carregar vários tipos de dados diferentes, segrega-os em pastas individuais dentro do seu recipiente blob e cria lógica para abasar a lógica com base no tipo de dados. 


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a  [API do Colecionador de Dados](data-collector-api.md) para escrever dados para registar o espaço de trabalho do Log Analytics a partir de qualquer cliente da API REST.
