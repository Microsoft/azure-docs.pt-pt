---
title: Dados da Consulta Avro utilizando o Azure Data Lake Analytics [ Microsoft Docs
description: Utilize propriedades do corpo de mensagens para encaminhar a telemetria do dispositivo para o armazenamento blob e consultar os dados do formato Avro que está escrito para o armazenamento blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605621"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Dados da Consulta Avro utilizando o Azure Data Lake Analytics

Este artigo discute como consultar os dados da Avro para direcionar eficientemente as mensagens do Azure IoT Hub para os serviços Azure. [O Message Routing](iot-hub-devguide-messages-d2c.md) permite-lhe filtrar dados usando consultas ricas com base em propriedades de mensagens, corpo de mensagem, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo. Para saber mais sobre as capacidades de consulta em Message Routing, consulte o artigo sobre a [sintaxe](iot-hub-devguide-routing-query-syntax.md)de consulta de envio de mensagens .

O desafio tem sido que quando o Azure IoT Hub envia mensagens para o armazenamento do Azure Blob, por padrão IoT Hub escreve o conteúdo em formato Avro, que tem tanto uma propriedade do corpo de mensagens como uma propriedade de mensagem. O formato Avro não é utilizado para outros pontos finais. Embora o formato Avro seja ótimo para a preservação de dados e mensagens, é um desafio usá-lo para consultar dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consulta de dados. O IoT Hub agora suporta a escrita de dados para o armazenamento blob em JSON, bem como AVRO.

Para mais informações, consulte [o Uso do Armazenamento Azure como ponto final de encaminhamento](iot-hub-devguide-messages-d2c.md#azure-storage).

Para responder às necessidades e formatos não relacionais de big data e superar este desafio, pode utilizar muitos dos padrões de big data tanto para transformar como escalar dados. Um dos padrões, "pay per consulta", é o Azure Data Lake Analytics, que é o foco deste artigo. Embora possa executar facilmente a consulta em Hadoop ou outras soluções, data Lake Analytics é muitas vezes mais adequado para esta abordagem "pay per consulta".

Há um "extrator" para a Avro na U-SQL. Para mais informações, consulte [o exemplo u-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consulta e exportação de dados da Avro para um ficheiro CSV

Nesta secção, consulta os dados da Avro e exporta-os para um ficheiro CSV no armazenamento da Blob Azure, embora possa facilmente colocar os dados em outros repositórios ou lojas de dados.

1. Configurar o Azure IoT Hub para encaminhar dados para um ponto final de armazenamento Do Blob Azure, utilizando uma propriedade no corpo de mensagens para selecionar mensagens.

   ![A secção "Pontos finais personalizados"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![As Regras de Encaminhamento](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Para obter mais informações sobre as configurações para as rotas e pontos finais personalizados, consulte [o Message Routing para um hub IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Certifique-se de que o seu dispositivo tem a codificação, o tipo de conteúdo e os dados necessários nas propriedades ou no corpo de mensagens, conforme referenciado na documentação do produto. Quando visualiza estes atributos no Device Explorer, como mostrado aqui, pode verificar se estão corretamente definidos.

   ![O painel de dados do Hub de Eventos](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Crie uma instância azure Data Lake Store e uma instância de Data Lake Analytics. O Azure IoT Hub não se encaminha para uma instância da Data Lake Store, mas uma instância de Data Lake Analytics requer uma.

   ![Data Lake Store e data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. No Data Lake Analytics, configure o armazenamento da Blob como uma loja adicional, o mesmo armazenamento Blob para o que o Azure IoT Hub encaminha dados.

   ![O painel "Fontes de Dados"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Como discutido no [exemplo U-SQL Avro,](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)precisa de quatro ficheiros DLL. Faça upload destes ficheiros para um local na sua instância data lake store.

   ![Quatro ficheiros DLL carregados](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. No Estúdio Visual, crie um projeto U-SQL.

   ! Criar um projeto U-SQL(./media/iot-hub-question-avro-data/query-avro-data-6.png)

7. Colhe o conteúdo do seguinte script no ficheiro recém-criado. Modifique as três secções destacadas: a sua conta Data Lake Analytics, os caminhos de ficheiros DLL associados e o caminho correto para a sua conta de armazenamento.

   ![As três secções a modificar](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   O script U-SQL real para uma saída simples para um ficheiro CSV:

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

    O Data Lake Analytics demorou cinco minutos a executar o seguinte guião, que estava limitado a 10 unidades analíticas e processou 177 ficheiros. O resultado é mostrado na saída do ficheiro CSV que é exibida na seguinte imagem:

    ![Resultados da saída para o ficheiro CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Saída convertida em ficheiro CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Para analisar o JSON, continue a pisar 8.

8. A maioria das mensagens IoT estão no formato de ficheiro JSON. Ao adicionar as seguintes linhas, pode analisar a mensagem num ficheiro JSON, que permite adicionar as cláusulas WHERE e a saída apenas os dados necessários.

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

    A saída apresenta uma coluna para `SELECT` cada item no comando.

    ![Saída mostrando uma coluna para cada item](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a consultar dados da Avro para direcionar eficientemente as mensagens do Azure IoT Hub para os serviços Azure.

Por exemplo, de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte a Documentação dos Aceleradores de [Soluções Azure IoT](/azure/iot-accelerators).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de [desenvolvimento do IoT Hub.](iot-hub-devguide.md)

Para saber mais sobre o encaminhamento de mensagens no IoT Hub, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).
