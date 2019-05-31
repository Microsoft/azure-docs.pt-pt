---
title: Consultar dados Avro com o Azure Data Lake Analytics | Documentos da Microsoft
description: Utilize as propriedades do corpo de mensagem para encaminhar a telemetria do dispositivo para o armazenamento de BLOBs e consultar os dados de formato Avro que são escritos no armazenamento de Blobs.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 84e1dd77c6e873dc2facb5126bbddf795192b60d
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257754"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Consultar dados Avro com o Azure Data Lake Analytics

Este artigo discute como consultar dados Avro para encaminhar eficientemente mensagens do IoT Hub do Azure aos serviços do Azure. [Encaminhamento de mensagens](iot-hub-devguide-messages-d2c.md) permite-lhe filtrar dados através de consultas com base nas propriedades da mensagem, corpo da mensagem, etiquetas de twin do dispositivo e propriedades de twin do dispositivo. Para saber mais sobre as capacidades de consultas de mensagem de encaminhamento, consulte o artigo [sintaxe de consulta de encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O desafio foi que quando o IoT Hub do Azure redireciona as mensagens para o armazenamento de Blobs do Azure, por predefinição o IoT Hub grave o conteúdo no formato Avro, que tem uma propriedade de corpo de mensagem e uma propriedade da mensagem. O formato Avro não é utilizado para outros pontos de extremidade. Embora o formato Avro é ótimo para preservação de dados e a mensagem, é um desafio para utilizá-lo para consultar dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consultar dados. IoT Hub suporta agora a escrita de dados no armazenamento de BLOBs em JSON, bem como AVRO.

Para obter mais informações, consulte [usando o armazenamento de Blobs do Azure como um ponto de final de encaminhamento](iot-hub-devguide-messages-d2c.md#azure-blob-storage).

Para resolver necessidades de grandes volumes de dados não relacionais e formatos e superar esse desafio, pode utilizar muitos dos padrões de grandes volumes de dados para transformar e dados de dimensionamento. Um dos padrões, "pagar por consulta", é o Azure Data Lake Analytics, que é o foco deste artigo. Embora seja possível executar a consulta facilmente no Hadoop ou outras soluções, o Data Lake Analytics é costuma ser mais bem adequado para essa abordagem de "pagar por consulta".

Há um "extrator" para Avro em U-SQL. Para obter mais informações, consulte [exemplo de U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consultar e exportar dados Avro para um ficheiro CSV

Nesta secção, consultar dados Avro e exportá-lo para um ficheiro CSV no armazenamento de Blobs do Azure, embora pode colocar facilmente os dados em outros armazenamentos de dados ou de repositórios.

1. Configure o IoT Hub do Azure, para encaminhar os dados para um ponto de final de armazenamento de Blobs do Azure, usando uma propriedade no corpo da mensagem para selecionar as mensagens.

   ![A secção "Pontos finais personalizados"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![As regras de encaminhamento](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Para obter mais informações sobre as definições de segurança de rotas e os pontos finais personalizados, consulte [roteamento de mensagens para um hub IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Certifique-se de que o dispositivo tem a codificação, tipo de conteúdo e os dados necessários no corpo da mensagem, conforme referenciado na documentação do produto ou as propriedades. Quando exibir esses atributos no Device Explorer, conforme mostrado aqui, pode verificar se estão definidos corretamente.

   ![O painel de dados do Hub de eventos](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Configure uma instância do Azure Data Lake Store e uma instância do Data Lake Analytics. O IoT Hub do Azure não é encaminhado para uma instância do Data Lake Store, mas uma instância do Data Lake Analytics requer uma.

   ![Instâncias do Data Lake Store e do Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. No Data Lake Analytics, configure o armazenamento de Blobs do Azure como um armazenamento adicional, o mesmo armazenamento de BLOBs que encaminha dados para o IoT Hub do Azure.

   ![O painel "Origens de dados"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Como explicado no [Avro de U-SQL de exemplo](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), terá quatro arquivos DLL. Carregar estes ficheiros para uma localização na sua instância do Data Lake Store.

   ![Quatro arquivos DLL carregados](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. No Visual Studio, crie um projeto de U-SQL.

   ! Criar um project](./media/iot-hub-query-avro-data/query-avro-data-6.png) U-SQL

7. Cole o conteúdo do seguinte script no ficheiro recentemente criado. Modificar as três secções realçadas: conta do Data Lake Analytics, os caminhos de ficheiro DLL associados e o caminho correto para a sua conta de armazenamento.

   ![As três seções para ser modificado](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   O script de U-SQL real para saída simple para um ficheiro CSV:

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

    Demorou do Data Lake Analytics para executar o script seguinte, o que estava limitado a 10 unidades de análise e processados 177 ficheiros de cinco minutos. O resultado é mostrado no resultado do ficheiro CSV que é apresentado na imagem seguinte:

    ![Resultados da saída para o ficheiro CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Convertido em arquivo CSV de saída](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Para analisar o JSON, avance para o passo 8.

8. A maioria das mensagens de IoT estão no formato de ficheiro JSON. Ao adicionar as seguintes linhas, pode analisar a mensagem para um ficheiro JSON, que permite adicionar cláusulas WHERE e apenas os dados necessários de saída.

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

    A saída apresenta uma coluna para cada item no `SELECT` comando.

    ![Mostrar uma coluna para cada item de saída](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a consultar dados Avro para encaminhar eficientemente mensagens do IoT Hub do Azure aos serviços do Azure.

Para obter exemplos de soluções ponto a ponto completas que utilizam o IoT Hub, consulte a [documentação de Aceleradores de soluções de IoT do Azure](/azure/iot-accelerators).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, veja a [Guia do programador do IoT Hub](iot-hub-devguide.md).

Para saber mais sobre o roteamento de mensagens do IoT Hub, veja [enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).
