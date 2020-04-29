---
title: Utilizar a API do Colecionador de Dados para criar um pipeline de dados
description: Pode utilizar a API do Colecionador de Dados Do Monitor Azure Para adicionar dados POST JSON ao espaço de trabalho do Log Analytics a partir de qualquer cliente que possa ligar para a API REST. Este artigo descreve como fazer o upload de dados armazenados em ficheiros de forma automatizada.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 96c64f6a0167b678f14bf0199069ecd6b4c8d57a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80055116"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Criar um pipeline de dados com a API de Recolha de Dados

A API do Colecionador de [Dados do Monitor Azure](data-collector-api.md) permite-lhe importar quaisquer dados de registo personalizados num espaço de trabalho de Log Analytics no Monitor Azure. Os únicos requisitos são que os dados sejam formatados jSON e divididos em 30 MB ou menos segmentos. Este é um mecanismo completamente flexível que pode ser ligado de muitas formas: desde os dados enviados diretamente da sua aplicação, até uploads adhoc únicos. Este artigo irá delinear alguns pontos de partida para um cenário comum: a necessidade de fazer o upload de dados armazenados em ficheiros numa base regular e automatizada. Embora o gasoduto aqui apresentado não seja o mais performante ou otimizado de outra forma, pretende-se servir de ponto de partida para a construção de um oleoduto de produção próprio.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Problema de exemplo
Para o resto deste artigo, examinaremos os dados de visualização de páginas em Insights de Aplicação. No nosso cenário hipotético, queremos correlacionar as informações geográficas recolhidas por padrão pelo SDK de Aplicação insights a dados personalizados que contenham a população de todos os países/regiões do mundo, com o objetivo de identificar onde devemos gastar mais dólares de marketing. 

Utilizamos uma fonte de dados público, como as [Perspetivas Mundiais da População das Nações Unidas](https://esa.un.org/unpd/wpp/) para este fim. Os dados terão o seguinte simples esquema:

![Exemplo de esquema simples](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

No nosso exemplo, assumimos que enviaremos um novo ficheiro com os dados do último ano assim que estiver disponível.

## <a name="general-design"></a>Design geral
Estamos a usar uma lógica clássica do tipo ETL para desenhar o nosso oleoduto. A arquitetura será a seguinte:

![Arquitetura do gasoduto de recolha de dados](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Este artigo não cobre como criar dados ou [enviá-lo para uma conta de Armazenamento Azure Blob](../../storage/blobs/storage-upload-process-images.md). Em vez disso, captamos o fluxo assim que um novo ficheiro é enviado para a bolha. A partir daqui:

1. Um processo irá detetar que novos dados foram carregados.  O nosso exemplo utiliza uma [App Lógica Azure,](../../logic-apps/logic-apps-overview.md)que tem um gatilho disponível para detetar novos dados a serem enviados para uma bolha.

2. Um processador lê estes novos dados e converte-os para a JSON, o formato exigido pelo Azure Monitor Neste exemplo, utilizamos uma [Função Azure](../../azure-functions/functions-overview.md) como uma forma leve e rentável de executar o nosso código de processamento. A função é iniciada pela mesma App Lógica que usamos para detetar os novos dados.

3. Finalmente, uma vez disponível o objeto JSON, é enviado para o Monitor Azure. A mesma Aplicação Lógica envia os dados para o Monitor Azure utilizando a atividade incorporada no Log Analytics Data Collector.

Embora a configuração detalhada do armazenamento de blob, Logic App ou Azure Function não esteja delineada neste artigo, instruções detalhadas estão disponíveis nas páginas dos produtos específicos.

Para monitorizar este pipeline, utilizamos o Application Insights para monitorizar [os nossos detalhes](../../azure-functions/functions-monitoring.md)da Função Azure aqui , e o Monitor Azure para monitorizar os detalhes da nossa Aplicação Lógica [aqui](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Configuração do oleoduto
Para definir o gasoduto, certifique-se primeiro de que tem o seu recipiente de bolhas criado e configurado. Da mesma forma, certifique-se de que o espaço de trabalho do Log Analytics para onde gostaria de enviar os dados é criado.

## <a name="ingesting-json-data"></a>Ingerir dados jSON
Ingestão de dados JSON é trivial com apps lógicas, e como nenhuma transformação precisa de ocorrer, podemos envolver todo o pipeline numa única Aplicação Lógica. Uma vez configurado tanto o recipiente blob como o espaço de trabalho log Analytics, crie uma nova App Lógica e configure-a da seguinte forma:

![Exemplo de fluxo de aplicativos lógicos](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Guarde a sua App Lógica e proceda à sua prova.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingestão de XML, CSV ou outros formatos de dados
As Aplicações Lógicas de hoje não têm capacidades incorporadas para transformar facilmente XML, CSV ou outros tipos em formato JSON. Por conseguinte, temos de utilizar outro meio para completar esta transformação. Para este artigo, utilizamos as capacidades computacionais sem servidores das Funções Azure como uma forma muito leve e amigável de fazê-lo. 

Neste exemplo, analisamos um ficheiro CSV, mas qualquer outro tipo de ficheiro pode ser processado da mesma forma. Basta modificar a parte desserialização da Função Azure para refletir a lógica correta para o seu tipo de dados específico.

1.  Crie uma nova Função Azure, utilizando o tempo de funcionamento da função v1 e baseado no consumo quando solicitado.  Selecione o modelo de **gatilho HTTP** direcionado para C# como um ponto de partida que configura as suas encadernações conforme exigimos. 
2.  A partir do separador **'Ver Ficheiros'** no painel certo, crie um novo ficheiro chamado **project.json** e colhe o seguinte código dos pacotes NuGet que estamos a utilizar:

    ![Projeto de exemplo de funções azure](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. Mude para **run.csx** a partir do painel direito e substitua o código predefinido pelo seguinte. 

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
5. Teste a função para se certificar de que o código está a funcionar corretamente. Mude para o separador **Test** no painel direito, configurando o teste da seguinte forma. Coloque um link para uma bolha com dados da amostra na caixa de texto **do corpo de Solicitar.** Depois de clicar em **Executar,** deve ver a saída da JSON na caixa **de saída:**

    ![Código de teste de aplicativos de função](./media/create-pipeline-datacollector-api/functions-test-01.png)

Agora precisamos voltar e modificar a App Lógica que começamos a construir mais cedo para incluir os dados ingeridos e convertidos para o formato JSON.  Utilizando o View Designer, configure o seguinte e, em seguida, guarde a sua Aplicação Lógica:

![Lógica Apps fluxo de trabalho completo exemplo](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testar o gasoduto
Agora pode enviar um novo ficheiro para a bolha configurada anteriormente e tê-lo monitorizado pela sua App Lógica. Em breve, deverá ver um novo exemplo da Aplicação Lógica arrancar, ligar para a sua Função Azure e, em seguida, enviar os dados com sucesso para o Monitor Azure. 

>[!NOTE]
>Pode levar até 30 minutos para os dados aparecerem no Monitor Azure na primeira vez que envia um novo tipo de dados.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correlacionado com outros dados em Log Analytics e Application Insights
Para completar o nosso objetivo de correlacionar os dados da página De insights de aplicação com os dados da população que ingerimos a partir da nossa fonte de dados personalizada, executar a seguinte consulta a partir da janela de Análise de Aplicações insights ou log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A saída deve mostrar que as duas fontes de dados agora aderiram.  

![Correlacionar dados desacompanhados num exemplo de resultado de pesquisa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Melhorias sugeridas para um gasoduto de produção
Este artigo apresentou um protótipo funcional, a lógica por trás da qual pode ser aplicada para uma verdadeira solução de qualidade de produção. Para uma solução de qualidade de produção, recomendam-se as seguintes melhorias:

* Adicione a lógica de manipulação de erros e de novo na sua Aplicação lógica e função.
* Adicione lógica para garantir que o limite de chamada de API de ingestão de analítica de log analytics de 30MB/single não seja ultrapassado. Divida os dados em segmentos mais pequenos, se necessário.
* Estabeleça uma política de limpeza no seu armazenamento de bolhas. Uma vez enviado com sucesso para o espaço de trabalho log Analytics, a menos que queira manter os dados brutos disponíveis para fins de arquivo, não há razão para continuar a armazená-los. 
* Verifique se a monitorização está ativada em todo o oleoduto, adicionando pontos de rastreio e alertas conforme apropriado.
* Aproveite o controlo de fontes para gerir o código para a sua função e Aplicação Lógica.
* Certifique-se de que uma política de gestão de mudanças adequada é seguida, de modo a que se o esquema mudar, a função e as Aplicações Lógicas sejam modificadas em conformidade.
* Se estiver a carregar vários tipos de dados diferentes, segrega-os em pastas individuais dentro do seu recipiente blob e crie lógica para abanar a lógica com base no tipo de dados. 


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [API](data-collector-api.md) do Colecionador de Dados para escrever dados para log Analytics espaço de trabalho de qualquer cliente da API REST.
