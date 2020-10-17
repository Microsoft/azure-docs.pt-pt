---
title: Consulta dados avro usando Azure Data Lake Analytics Microsoft Docs
description: Utilize propriedades do corpo de mensagens para encaminhar a telemetria do dispositivo para o armazenamento blob e consultar os dados do formato Avro que estão escritos para o armazenamento blob.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 1eed12593168975b1dfda6e51b07b256cbd0c246
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145801"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Consulta dados avro usando Azure Data Lake Analytics

Este artigo discute como consultar os dados da Avro para encaminhar eficientemente mensagens do Azure IoT Hub para os serviços Azure. [O Encaminhamento de Mensagens](iot-hub-devguide-messages-d2c.md) permite filtrar dados usando consultas ricas com base em propriedades de mensagens, corpo de mensagens, etiquetas gémeas do dispositivo e propriedades gémeas do dispositivo. Para saber mais sobre as capacidades de consulta no Roteamento de Mensagens, consulte o artigo sobre [a sintaxe de encaminhamento de mensagens](iot-hub-devguide-routing-query-syntax.md).

O desafio tem sido que quando o Azure IoT Hub encaminha mensagens para o armazenamento Azure Blob, por padrão O IoT Hub escreve o conteúdo em formato Avro, que tem uma propriedade corporal de mensagem e uma propriedade de mensagem. O formato Avro não é utilizado para outros pontos finais. Embora o formato Avro seja ótimo para a preservação de dados e mensagens, é um desafio usá-lo para consultar dados. Em comparação, o formato JSON ou CSV é muito mais fácil para consulta de dados. O IoT Hub suporta agora a escrita de dados para o armazenamento blob em JSON, bem como avro.

Para obter mais informações, consulte [a utilização do Azure Storage como ponto final de encaminhamento](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

Para responder às necessidades e formatos não relacionais de big data e superar este desafio, pode utilizar muitos dos padrões de big data para a transformação e dimensionamento de dados. Um dos padrões, "pay per consulta", é o Azure Data Lake Analytics, que é o foco deste artigo. Embora possa executar facilmente a consulta em Hadoop ou outras soluções, o Data Lake Analytics é muitas vezes mais adequado para esta abordagem "pay per questionry".

Há um "extrator" para a Avro em U-SQL. Para mais informações, consulte [o exemplo U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Consulta e exportação de dados da Avro para um ficheiro CSV

Nesta secção, consulta os dados da Avro e exporta-os para um ficheiro CSV no armazenamento Azure Blob, embora possa facilmente colocar os dados em outros repositórios ou lojas de dados.

1. Crie o Azure IoT Hub para encaminhar os dados para um ponto final de armazenamento Azure Blob utilizando uma propriedade no corpo da mensagem para selecionar mensagens.

   ![A secção "Pontos finais personalizados"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![As Regras de Encaminhamento](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Para obter mais informações sobre as configurações de rotas e pontos finais personalizados, consulte [o Encaminhamento de Mensagens para um hub IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Certifique-se de que o seu dispositivo possui a codificação, o tipo de conteúdo e os dados necessários nas propriedades ou no corpo da mensagem, conforme indicado na documentação do produto. Quando vê estes atributos no Device Explorer, como mostrado aqui, pode verificar se estão corretamente definidos.

   ![O painel de dados do centro de eventos](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Crie uma instância da Azure Data Lake Store e uma instância data lake analytics. O Azure IoT Hub não se encaminha para uma instância da Data Lake Store, mas uma instância de Data Lake Analytics requer uma.

   ![Data Lake Store e data lake analytics instâncias](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. No Data Lake Analytics, configurar o armazenamento Azure Blob como uma loja adicional, o mesmo armazenamento Blob que a Azure IoT Hub encaminha dados para.

   ![O painel "Fontes de Dados"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Como discutido no [exemplo U-SQL Avro,](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)precisa de quatro ficheiros DLL. Faça o upload destes ficheiros para uma localização na sua aplicação data lake.

   ![Quatro ficheiros DLL carregados](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. No Visual Studio, crie um projeto U-SQL.

   ! Criar um projeto U-SQL[./media/iot-hub-consulta-avro-data/query-avro-data-6.png)

7. Cole o conteúdo do seguinte script no ficheiro recém-criado. Modifique as três secções destacadas: a sua conta Data Lake Analytics, os caminhos de ficheiros DLL associados e o caminho correto para a sua conta de armazenamento.

   ![As três secções a modificar](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   O script U-SQL real para saída simples para um ficheiro CSV:

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

    O Data Lake Analytics demorou cinco minutos a executar o seguinte script, que estava limitado a 10 unidades analíticas e processou 177 ficheiros. O resultado é mostrado na saída do ficheiro CSV que é exibida na seguinte imagem:

    ![Resultados da saída para ficheiro CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Saída convertida em ficheiro CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Para analisar o JSON, continue a pisar o passo 8.

8. A maioria das mensagens IoT estão no formato de ficheiro JSON. Ao adicionar as seguintes linhas, pode analisar a mensagem num ficheiro JSON, que permite adicionar as cláusulas WHERE e apenas os dados necessários.

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

    ![Saída mostrando uma coluna para cada item](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a consultar os dados da Avro para encaminhar eficientemente mensagens do Azure IoT Hub para os serviços Azure.

Por exemplo, de soluções completas de ponta a ponta que utilizam o Hub IoT, consulte a Documentação de [Aceleradores de Solução IoT Azure](../iot-accelerators/index.yml).

Para saber mais sobre o desenvolvimento de soluções com o IoT Hub, consulte o guia de desenvolvimento do [IoT Hub.](iot-hub-devguide.md)

Para saber mais sobre o encaminhamento de mensagens no IoT Hub, consulte [Enviar e receber mensagens com o IoT Hub](iot-hub-devguide-messaging.md).