---
title: Criar um pipeline de dados com a API do coletor de dados Azure Monitor | Microsoft Docs
description: Você pode usar a API do coletor de dados HTTP Azure Monitor para adicionar dados JSON POST ao espaço de trabalho Log Analytics de qualquer cliente que possa chamar a API REST. Este artigo descreve como carregar dados armazenados em arquivos de maneira automatizada.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 08/09/2018
ms.openlocfilehash: 8b739d86ec557ca8c7de7e0999c905b51d1d97a7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932618"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Criar um pipeline de dados com a API do coletor de dados

A [API do coletor de dados Azure monitor](data-collector-api.md) permite importar dados de log personalizados para um espaço de trabalho Log Analytics no Azure monitor. Os únicos requisitos são que os dados sejam formatados em JSON e divididos em 30 MB ou menos segmentos. Esse é um mecanismo completamente flexível que pode ser conectado de várias maneiras: a partir de dados enviados diretamente do seu aplicativo, a uploads adhoc únicos. Este artigo descreverá alguns pontos de partida para um cenário comum: a necessidade de carregar dados armazenados em arquivos de forma regular e automatizada. Embora o pipeline apresentado aqui não seja o mais eficaz ou otimizado de outra forma, ele se destina a servir como um ponto de partida para a criação de um pipeline de produção por conta própria.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Problema de exemplo
Para o restante deste artigo, examinaremos os dados de exibição de página em Application Insights. Em nosso cenário hipotético, queremos correlacionar informações geográficas coletadas por padrão pelo SDK Application Insights para dados personalizados que contenham a população de cada país/região do mundo, com o objetivo de identificar onde devemos gastar a maior parte dos investimentos em marketing. 

Usamos uma fonte de dados pública, como os [clientes potenciais de população](https://esa.un.org/unpd/wpp/) sem o mundo para essa finalidade. Os dados terão o seguinte esquema simples:

![Esquema simples de exemplo](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

Em nosso exemplo, supomos que carregaremos um novo arquivo com os dados do ano mais recente assim que ele se tornar disponível.

## <a name="general-design"></a>Design geral
Estamos usando uma lógica clássica do tipo ETL para criar nosso pipeline. A arquitetura terá a seguinte aparência:

![Arquitetura de pipeline de coleta de dados](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Este artigo não abordará como criar dados ou [carregá-los em uma conta de armazenamento de BLOBs do Azure](../../storage/blobs/storage-upload-process-images.md). Em vez disso, escolhemos o fluxo assim que um novo arquivo é carregado no BLOB. Daqui:

1. Um processo detectará que novos dados foram carregados.  Nosso exemplo usa um [aplicativo lógico do Azure](../../logic-apps/logic-apps-overview.md), que tem um gatilho disponível para detectar novos dados que estão sendo carregados em um blob.

2. Um processador lê esses novos dados e os converte em JSON, o formato exigido por Azure Monitor neste exemplo, usamos uma [função do Azure](../../azure-functions/functions-overview.md) como uma maneira leve e econômica de executar nosso código de processamento. A função é inicializada pelo mesmo aplicativo lógico usado para detectar os novos dados.

3. Por fim, depois que o objeto JSON estiver disponível, ele será enviado para Azure Monitor. O mesmo aplicativo lógico envia os dados para Azure Monitor usando a atividade interna do coletor de dados Log Analytics.

Embora a configuração detalhada do armazenamento de BLOBs, do aplicativo lógico ou do Azure Function não esteja descrita neste artigo, as instruções detalhadas estão disponíveis nas páginas de produtos específicos.

Para monitorar esse pipeline, usamos Application Insights para monitorar os detalhes da função do Azure [aqui](../../azure-functions/functions-monitoring.md)e Azure monitor monitorar os detalhes do aplicativo lógico [aqui](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Configurando o pipeline
Para definir o pipeline, primeiro verifique se o seu contêiner de BLOBs foi criado e configurado. Da mesma forma, certifique-se de que o espaço de trabalho Log Analytics para o qual você deseja enviar os dados seja criado.

## <a name="ingesting-json-data"></a>Ingerir dados JSON
A ingestão de dados JSON é trivial com aplicativos lógicos e, como nenhuma transformação precisa ocorrer, podemos encerrarão o pipeline inteiro em um único aplicativo lógico. Depois que o contêiner de BLOB e o espaço de trabalho do Log Analytics tiverem sido configurados, crie um novo aplicativo lógico e configure-o da seguinte maneira:

![Exemplo de fluxo de trabalho de aplicativos lógicos](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Salve seu aplicativo lógico e continue para testá-lo.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingestão de XML, CSV ou outros formatos de dados
Atualmente, os aplicativos lógicos não têm recursos internos para transformar facilmente XML, CSV ou outros tipos em formato JSON. Portanto, precisamos usar outro meio para concluir essa transformação. Para este artigo, usamos os recursos de computação sem servidor do Azure Functions como uma maneira muito leve e amigável de fazer isso. 

Neste exemplo, analisamos um arquivo CSV, mas qualquer outro tipo de arquivo pode ser processado de forma semelhante. Basta modificar a parte de desserialização da função do Azure para refletir a lógica correta para seu tipo de dados específico.

1.  Crie uma nova função do Azure usando o tempo de execução de função v1 e com base no consumo quando solicitado.  Selecione o modelo de **gatilho http** direcionado para C# como um ponto de partida que configura suas associações conforme necessário. 
2.  Na guia **Exibir arquivos** no painel direito, crie um novo arquivo chamado **Project. JSON** e cole o seguinte código dos pacotes NuGet que estamos usando:

    ![Azure Functions projeto de exemplo](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. Alterne para **Run. CSX** no painel direito e substitua o código padrão pelo seguinte. 

    >[!NOTE]
    >Para seu projeto, você precisa substituir o modelo de registro (a classe "PopulationRecord") por seu próprio esquema de dados.
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

4. Salve sua função.
5. Teste a função para certificar-se de que o código está funcionando corretamente. Alterne para a guia **teste** no painel direito, configurando o teste da seguinte maneira. Coloque um link para um blob com dados de exemplo na caixa de texto **corpo da solicitação** . Depois de clicar em **executar**, você deverá ver a saída JSON na caixa **saída** :

    ![Código de teste de aplicativos de funções](./media/create-pipeline-datacollector-api/functions-test-01.png)

Agora precisamos voltar e modificar o aplicativo lógico que começamos a criar anteriormente para incluir os dados ingeridos e convertidos no formato JSON.  Usando o designer de exibição, configure da seguinte maneira e, em seguida, salve seu aplicativo lógico:

![Exemplo de fluxo de trabalho de aplicativos lógicos concluído](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testando o pipeline
Agora você pode carregar um novo arquivo no blob configurado anteriormente e tê-lo monitorado por seu aplicativo lógico. Em breve, você deve ver uma nova instância do aplicativo lógico disparar, chamar sua função do Azure e enviar os dados com êxito para Azure Monitor. 

>[!NOTE]
>Pode levar até 30 minutos para que os dados apareçam em Azure Monitor na primeira vez que você enviar um novo tipo de dados.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correlacionar com outros dados em Log Analytics e Application Insights
Para concluir nosso objetivo de correlacionar Application Insights dados de exibição de página com os dados populacionais que ingerimos de nossa fonte de dados personalizada, execute a seguinte consulta na janela Application Insights Analytics ou no espaço de trabalho Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A saída deve mostrar as duas fontes de dados agora Unidas.  

![Correlacionando dados separados em um exemplo de resultado da pesquisa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Aprimoramentos sugeridos para um pipeline de produção
Este artigo apresentou um protótipo de trabalho, a lógica por trás, que pode ser aplicada em direção a uma verdadeira solução de qualidade de produção. Para uma solução de qualidade de produção, são recomendadas as seguintes melhorias:

* Adicione o tratamento de erros e a lógica de repetição em seu aplicativo lógico e função.
* Adicione lógica para garantir que o limite de chamadas da API de ingestão de Log Analytics 30MB/único não seja excedido. Divida os dados em segmentos menores, se necessário.
* Configure uma política de limpeza em seu armazenamento de BLOBs. Uma vez enviado com êxito para o espaço de trabalho do Log Analytics, a menos que você queira manter os dados brutos disponíveis para fins de arquivamento, não há motivo para continuar a armazená-los. 
* Verifique se o monitoramento está habilitado em todo o pipeline, adicionando pontos de rastreamento e alertas conforme apropriado.
* Aproveite o controle de código-fonte para gerenciar o código da sua função e do aplicativo lógico.
* Verifique se uma política apropriada de gerenciamento de alterações é seguida, de modo que, se o esquema for alterado, os aplicativos lógicos e de função serão modificados de acordo.
* Se você estiver carregando vários tipos de dados diferentes, separe-os em pastas individuais dentro do seu contêiner de BLOBs e crie uma lógica para o ventilador da lógica com base no tipo de dados. 


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [API do coletor de dados](data-collector-api.md) para gravar dados no espaço de trabalho log Analytics de qualquer cliente da API REST.
