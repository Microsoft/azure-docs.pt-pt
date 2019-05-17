---
title: Criar um pipeline de dados com a API de Recoletor de dados do Azure Monitor | Documentos da Microsoft
description: Pode utilizar a API de Recoletor de dados do Azure Monitor HTTP para adicionar dados POST JSON para a área de trabalho do Log Analytics a partir de qualquer cliente que pode chamar a API REST. Este artigo descreve como carregar dados armazenados em arquivos de maneira automatizada.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.openlocfilehash: 53457a044f5c69af7bf68561f24732e8f02219d8
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603248"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Criar um pipeline de dados com a API de Recoletor de dados

O [API do Recoletor de dados do Azure Monitor](data-collector-api.md) permite que importe quaisquer dados de registo personalizado para uma área de trabalho do Log Analytics no Azure Monitor. Os únicos requisitos são que os dados estejam formatados por JSON e dividir em 30 MB ou menos segmentos. Este é um mecanismo completamente flexível que pode ser conectado de várias maneiras: de dados que está a ser enviados diretamente a partir da sua aplicação, para ad hoc pontual carrega. Este artigo descreverá alguns pontos de partida para um cenário comum: a necessidade de carregar os dados armazenados em arquivos de forma regular, automatizado. Enquanto o pipeline apresentadas aqui não vai ser o melhor desempenho ou caso contrário otimizado, destina-se para ser usado como um ponto de partida para criar um pipeline de produção de sua própria.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Problema de exemplo
Para o restante deste artigo, examinaremos os dados de exibição de página no Application Insights. Em nosso cenário hipotético, o que queremos correlacionar informações geográficas recolhidas por predefinição pelo Application Insights SDK para dados personalizados que contém a população de cada país/região do mundo, com o objetivo de identificar em que podemos deve estar dedicando algum os marketing mais dólares. 

Utilizamos uma origem de dados públicos, como o [propostas de população de mundo anular](https://esa.un.org/unpd/wpp/) para esta finalidade. Os dados terão o esquema simple seguinte:

![Esquema de exemplo simples](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

No nosso exemplo, partimos do princípio de irá carregar um novo ficheiro com dados o ano mais recente assim que ele se torna disponível.

## <a name="general-design"></a>Design geral
Estamos a utilizar uma lógica de tipo de ETL clássica para criar nosso pipeline. A arquitetura será semelhante ao seguinte:

![Arquitetura de pipeline de recolha de dados](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Este artigo não abrange como criar dados ou [carregá-lo para uma conta de armazenamento de Blobs do Azure](../../storage/blobs/storage-upload-process-images.md). Em vez disso, vamos pegar o fluxo quando um novo ficheiro é carregado para o blob. A partir daqui:

1. Um processo irá detetar que os novos dados tem sido carregados.  Nosso exemplo utiliza um [aplicação lógica do Azure](../../logic-apps/logic-apps-overview.md), que tem disponível um acionador para detetar novos dados a ser carregados para um blob.

2. Um processador lê estes dados novos e converte-o JSON, o formato exigido pelo Azure Monitor In neste exemplo, utilizamos um [função do Azure](../../azure-functions/functions-overview.md) como uma forma simples, econômica e eficiente de executar nosso código de processamento. A função é iniciada pela mesma aplicação lógica que usamos para detetar um os novos dados.

3. Por fim, quando o objeto JSON estiver disponível, será enviado para o Azure Monitor. A mesma aplicação lógica envia os dados para o Azure Monitor usando incorporado na atividade do Log Analytics Data Collector.

Enquanto a configuração detalhada do armazenamento de BLOBs, a aplicação lógica ou função do Azure não é descrita neste artigo, instruções detalhadas estão disponíveis nas páginas dos produtos específicos.

Para monitorizar este pipeline, podemos utilizar o Application Insights para monitorizar a nossa função do Azure [detalhes aqui](../../azure-functions/functions-monitoring.md)e o Azure Monitor para monitorizar a nossa aplicação de lógica [detalhes aqui](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md). 

## <a name="setting-up-the-pipeline"></a>Como configurar o pipeline
Para definir o pipeline, certifique-se de que tem o seu contentor de blob criado e configurado. Da mesma forma, certifique-se de que a área de trabalho do Log Analytics em que gostaria de enviar os dados para é criada.

## <a name="ingesting-json-data"></a>Ingestão de dados JSON
Ingestão de dados JSON é trivial com o Logic Apps e, uma vez que não precisa ser feita nenhuma transformação, pode envolver todo o pipeline numa única aplicação lógica. Depois do contentor de BLOBs e a área de trabalho do Log Analytics tiverem sido configuradas, crie uma nova aplicação lógica e configurá-lo da seguinte forma:

![Exemplo de fluxo de trabalho de aplicações lógicas](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Guardar a aplicação lógica e continue para testá-lo.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Ingestão de XML, CSV ou outros formatos de dados
O Logic Apps hoje não possui capacidades incorporadas para transformar facilmente XML, CSV ou outros tipos em formato JSON. Portanto, precisamos de utilizar outro meio para concluir essa transformação. Neste artigo, vamos utilizar as capacidades de computação sem servidor das funções do Azure como uma forma muito simples e amigável de custo de se fazer isso. 

Neste exemplo, vamos analisar um ficheiro CSV, mas qualquer outro tipo de ficheiro pode ser processado da mesma forma. Basta modificar a anular a serialização dos parte da função do Azure para refletir a lógica correta para o seu tipo de dados específicos.

1.  Criar uma nova função do Azure, utilizando o v1 de tempo de execução de função e baseado no consumo quando lhe for pedido.  Selecione o **acionador HTTP** modelo orientado para c# como ponto de partida, que configura as suas ligações, é necessário. 
2.  Partir do **ver ficheiros** separador no painel direito, crie um novo arquivo chamado **Project** e cole o seguinte código de pacotes NuGet que estamos a utilizar:

    ![Projeto de exemplo das funções do Azure](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
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

3. Mude para o **csx** no painel direito e substitua o código de padrão com o seguinte. 

    >[!NOTE]
    >Para o seu projeto, terá de substituir o modelo de registo (a classe "PopulationRecord") com o seu próprio esquema de dados.
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

4. Guarde a sua função.
5. Teste a função para se certificar de que o código está a funcionar corretamente. Mude para o **testar** separador no painel da direita, configuração do teste da seguinte forma. Colocar uma ligação para um blob com os dados de exemplo para o **corpo do pedido** caixa de texto. Depois de clicar em **execute**, deverá ver o JSON de saída no **saída** caixa:

    ![Código de teste de aplicações de função](./media/create-pipeline-datacollector-api/functions-test-01.png)

Agora, precisamos voltar atrás e modificar a aplicação lógica, começamos criando anteriormente para incluir os dados ingeridos e convertidos no formato JSON.  A utilizar o estruturador de vistas, configure da seguinte forma e, em seguida, guarde a aplicação lógica:

![Exemplo completo do fluxo de trabalho de aplicações lógicas](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Teste o pipeline
Agora pode carregar um ficheiro novo para o blob configurado anteriormente e fazer com que ele monitorizado pela sua aplicação lógica. Em breve, deve ver uma nova instância de dar a aplicação lógica, chamar sua função do Azure e, em seguida, enviar os dados com êxito para o Azure Monitor. 

>[!NOTE]
>Pode demorar até 30 minutos para que os dados sejam apresentados no Azure Monitor na primeira vez, enviar um novo tipo de dados.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correlacionar com outros dados no Log Analytics e Application Insights
Para concluir o nosso objetivo de correlacionar dados de exibição de página do Application Insights com os dados de população que ingeridos a partir de nossa origem de dados personalizada, execute a seguinte consulta a partir da sua janela do Application Insights Analytics ou de uma área de trabalho do Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

A saída deve mostrar os dados de duas origens agora associadas.  

![Correlacionando desagregado dados num exemplo de resultado de pesquisa](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Melhorias sugeridas para um pipeline de produção
Este artigo apresentou um protótipo funcional, a lógica por trás do que pode ser aplicada numa verdadeira solução de qualidade de produção. Para essa solução qualidade de produção, são recomendadas as seguintes melhorias:

* Adicionar tratamento de erros e lógica na sua aplicação lógica e a função de repetição.
* Adicione lógica para se certificar de que não seja excedido o limite de chamada de API de ingestão do Log Analytics de 30MB/única. Divida os dados em segmentos mais pequenos, se necessário.
* Configure uma política de limpeza no seu armazenamento de Blobs. Uma vez enviado com êxito para a área de trabalho do Log Analytics, a menos que gostaria de manter os dados não processados disponíveis para fins de arquivamento, não há motivo para continuar a armazená-los. 
* Certifique-se a monitorização está ativada no pipeline completo, adicionar pontos de rastreamento e alerta conforme adequada.
* Tirar partido do controlo de origem para gerir o código para a sua função e a aplicação lógica.
* Certifique-se de que é seguida de uma política de gestão de alterações adequado, que se o esquema for alterado, a função e o Logic Apps são modificados em conformidade.
* Se estiver a carregar vários tipos de dados diferentes, segregá-los em pastas individuais dentro do seu contentor de BLOBs e criar a lógica para a ventoinha a lógica de saída com base no tipo de dados. 


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [API do Recoletor de dados](data-collector-api.md) para escrever dados para a área de trabalho do Log Analytics a partir de qualquer cliente de REST API.
