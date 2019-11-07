---
title: Consultar dados de Avro usando Azure Data Lake Analytics | Microsoft Docs
description: Use as propriedades do corpo da mensagem para rotear a telemetria do dispositivo para o armazenamento de BLOBs e consultar os dados de formato Avro gravados no armazenamento de BLOBs.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605621"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Consultar dados de Avro usando Azure Data Lake Analytics

Este artigo discute como consultar dados de Avro para rotear com eficiência as mensagens do Hub IoT do Azure para os serviços do Azure. O [Roteamento de mensagens](iot-hub-devguide-messages-d2c.md) permite que você filtre dados usando consultas avançadas com base nas propriedades da mensagem, no corpo da mensagem, nas marcas de dispositivo e nas propriedades do dispositivo. Para saber mais sobre os recursos de consulta no roteamento de mensagens, consulte o artigo sobre a [sintaxe de consulta de roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O desafio foi que, quando o Hub IoT do Azure roteia mensagens para o armazenamento de BLOBs do Azure, por padrão, o Hub IoT grava o conteúdo no formato Avro, que tem uma propriedade de corpo de mensagem e uma propriedade de mensagem. O formato Avro não é usado para nenhum outro ponto de extremidade. Embora o formato Avro seja ótimo para a preservação de dados e de mensagens, é um desafio usá-lo para consultar dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consultar dados. O Hub IoT agora dá suporte à gravação de dados no armazenamento de BLOBs em JSON, bem como AVRO.

Para obter mais informações, consulte [usando o armazenamento do Azure como um ponto de extremidade de roteamento](iot-hub-devguide-messages-d2c.md#azure-storage).

Para lidar com necessidades e formatos de Big data não relacionais e superar esse desafio, você pode usar muitos dos padrões de Big data para transformar e dimensionar dados. Um dos padrões, "pague por consulta", é Azure Data Lake Analytics, que é o foco deste artigo. Embora você possa executar facilmente a consulta no Hadoop ou em outras soluções, a Data Lake Analytics geralmente é mais adequada para essa abordagem de "pagamento por consulta".

Há um "extrator" para Avro no U-SQL. Para obter mais informações, consulte [exemplo de Avro U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consultar e exportar dados de Avro para um arquivo CSV

Nesta seção, você consulta dados de Avro e os exporta para um arquivo CSV no armazenamento de BLOBs do Azure, embora você possa posicionar facilmente os dados em outros repositórios ou armazenamentos de dados.

1. Configure o Hub IoT do Azure para rotear dados para um ponto de extremidade do armazenamento de BLOBs do Azure usando uma propriedade no corpo da mensagem para selecionar mensagens.

   ![A seção "pontos de extremidade personalizados"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![As regras de roteamento](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Para obter mais informações sobre configurações de rotas e pontos de extremidade personalizados, consulte [Roteamento de mensagens para um hub IOT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Verifique se o dispositivo tem a codificação, o tipo de conteúdo e os dados necessários nas propriedades ou no corpo da mensagem, conforme referenciado na documentação do produto. Ao exibir esses atributos em Device Explorer, como mostrado aqui, você pode verificar se eles estão definidos corretamente.

   ![O painel de dados do hub de eventos](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Configure uma instância de Azure Data Lake Store e uma instância de Data Lake Analytics. O Hub IoT do Azure não encaminha para uma instância de Data Lake Store, mas uma instância de Data Lake Analytics requer uma.

   ![Instâncias de Data Lake Store e Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. No Data Lake Analytics, configure o armazenamento de BLOBs do Azure como um armazenamento adicional, o mesmo armazenamento de BLOBs ao qual o Hub IoT do Azure roteia dados.

   ![O painel "fontes de dados"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Conforme discutido no [exemplo de Avro do U-SQL](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), você precisa de quatro arquivos dll. Carregue esses arquivos em um local na sua instância de Data Lake Store.

   ![Quatro arquivos DLL carregados](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. No Visual Studio, crie um projeto do U-SQL.

   ! Criar um projeto U-SQL] (./Media/IOT-Hub-Query-Avro-data/Query-Avro-data-6.png)

7. Cole o conteúdo do script a seguir no arquivo recém-criado. Modifique as três seções realçadas: sua conta de Data Lake Analytics, os caminhos de arquivo DLL associados e o caminho correto para sua conta de armazenamento.

   ![As três seções a serem modificadas](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   O script U-SQL real para saída simples para um arquivo CSV:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Demorou Data Lake Analytics cinco minutos para executar o script a seguir, que era limitado a 10 unidades analíticas e processava 177 arquivos. O resultado é mostrado na saída do arquivo CSV que é exibida na imagem a seguir:

    ![Resultados da saída para o arquivo CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Saída convertida em arquivo CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Para analisar o JSON, continue na etapa 8.

8. A maioria das mensagens de IoT está no formato de arquivo JSON. Ao adicionar as linhas a seguir, você pode analisar a mensagem em um arquivo JSON, que permite adicionar as cláusulas WHERE e gerar somente os dados necessários.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    A saída exibe uma coluna para cada item no comando `SELECT`.

    ![Saída mostrando uma coluna para cada item](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a consultar dados de Avro para rotear mensagens do Hub IoT do Azure para os serviços do Azure de forma eficiente.

Para obter exemplos de soluções completas de ponta a ponta que usam o Hub IoT, consulte a [documentação dos aceleradores de soluções do Azure IOT](/azure/iot-accelerators).

Para saber mais sobre como desenvolver soluções com o Hub IoT, confira o [Guia do desenvolvedor do Hub IOT](iot-hub-devguide.md).

Para saber mais sobre o roteamento de mensagens no Hub IoT, consulte [Enviar e receber mensagens com o Hub IOT](iot-hub-devguide-messaging.md).
