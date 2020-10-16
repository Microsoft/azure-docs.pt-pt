---
title: Tutorial - Armazenar dados com módulo SQL utilizando Azure IoT Edge
description: Este tutorial mostra como armazenar dados localmente no seu dispositivo IoT Edge com um módulo SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: a8d09f762002c89d225ccc00eac83da336850a3c
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047952"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Armazenar dados na periferia com bases de dados do SQL Server

Implemente um módulo SQL Server para armazenar dados num dispositivo Linux que executa a Azure IoT Edge.

Utilize o Azure IoT Edge e o SQL Server para armazenar e consultar dados na periferia. O Azure IoT Edge tem capacidades básicas de armazenamento para cache mensagens se um dispositivo ficar offline e, em seguida, encaminhá-las quando a ligação for restabelecida. No entanto, pode querer capacidades de armazenamento mais avançadas, como a capacidade de consultar dados localmente. Os seus dispositivos IoT Edge podem utilizar bases de dados locais para realizar computação mais complexa sem ter de manter uma ligação ao IoT Hub.

Este artigo fornece instruções para implementar uma base de dados do SQL Server num dispositivo IoT Edge. As Funções do Azure, em execução no dispositivo IoT Edge, estruturam os dados recebidos e, em seguida, envia-os para a base de dados. Os passos neste artigo também podem ser aplicados a outras bases de dados que funcionam em contentores, como o MySQL ou o PostgreSQL.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Utilize o Visual Studio Code para criar uma função do Azure
> * Implementar uma base de dados do SQL Server no seu dispositivo IoT Edge
> * Utilize o Visual Studio Code para criar módulos e implementá-los no seu dispositivo IoT Edge
> * Ver os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento para o desenvolvimento de recipientes Linux: [Desenvolver módulos IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao completar este tutorial, deverá ter os seguintes pré-requisitos no lugar:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um dispositivo AMD64 [Linux em execução Azure IoT Edge](quickstart-linux.md).
  * Dispositivos ARM, como Raspberry Pis, não podem executar o SQL Server. Se quiser utilizar o SQL num dispositivo ARM, pode inscrever-se para experimentar [o Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/) na pré-visualização.
* Um registo de contentores, como [o Registo de Contentores de Azure.](../container-registry/index.yml)
* [Código de Estúdio Visual](https://code.visualstudio.com/) configurado com as [Ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores Linux.

Este tutorial utiliza um módulo Azure Functions para enviar dados para o SqL Server. Para desenvolver um módulo IoT Edge com Funções Azure, instale os seguintes pré-requisitos adicionais na sua máquina de desenvolvimento:

* [C# para extensão visual Studio Code (alimentado pela OmniSharp) para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

Para enviar dados numa base de dados, precisa de um módulo que possa estruturar corretamente os dados e, em seguida, armazená-los numa tabela.

### <a name="create-a-new-project"></a>Criar um novo projeto

Os passos a seguir mostram-lhe como criar uma função IoT Edge utilizando o Código de Estúdio Visual e as Ferramentas IoT Azure.

1. Abra o Visual Studio Code.

2. Abra a paleta de comandos VS Code selecionando a paleta de comando **do**  >  **Ver**.

3. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, indique as seguintes informações para criar a sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução, como **SqlSolution,** ou aceite o padrão. |
   | Selecionar modelo de módulo | Escolha **funções Azure - C#**. . |
   | Indicar um nome para o módulo | Atribua o nome **sqlFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **o local:5000** pelo valor do **servidor login** do registo do seu contentor Azure. Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure. <br><br>A corda final parece \<registry name\> .azurecr.io/sqlfunction. |

   A janela do VS Code carrega a área de trabalho da solução do IoT Edge.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

A extensão IoT Edge tenta retirar as credenciais de registo do seu contentor do Azure e povoá-las no ficheiro ambiente. Verifique se as suas credenciais já estão incluídas. Caso contrário, adicione-os agora:

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura-alvo

Você precisa selecionar que arquitetura você está dirigindo com cada solução, porque o recipiente é construído e executado de forma diferente para cada tipo de arquitetura. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure **por Azure IoT Edge: Definir Plataforma-alvo padrão para solução de borda**, ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comando, selecione a arquitetura-alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manteremos o **amd64**padrão.

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador de código VS, **abram módulos**  >  **sqlFunction**  >  **sqlFunction.csproj**.

2. Encontre o grupo de referências de pacotes e adicione um novo para incluir sqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Guarde o ficheiro **sqlFunction.csproj.**

4. Abra o ficheiro **sqlFunction.cs.**

5. Substitua todo o conteúdo do ficheiro pelo seguinte código:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

6. Na linha 35, substitua a corda **\<sql connection string\>** pela seguinte corda. A propriedade **Data Source** refere-se ao contentor SQL Server, que ainda não existe. Irá criá-lo com o nome **SQL** na secção seguinte.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Guarde o ficheiro **sqlFunction.cs.**

## <a name="add-the-sql-server-container"></a>Adicione o recipiente SQL Server

Um [Manifesto de implementação](module-composition.md) declara os módulos que o runtime do IoT Edge irá instalar no seu dispositivo IoT Edge. Forneceu o código para fazer um módulo de função personalizado na secção anterior, mas o módulo SQL Server já está construído e disponível no Azure Marketplace. Apenas tem de indicar ao runtime do IoT Edge para o incluir e configurar no seu dispositivo.

1. No Código do Estúdio Visual, abra a paleta de comando selecionando a paleta **de**Comando ver  >  **Command palette**.

2. Na paleta de comando, escreva e execute o comando **Azure IoT Edge: Adicione o módulo IoT Edge**. Na paleta de comando, forneça as seguintes informações para adicionar um novo módulo:

   | Campo | Valor |
   | ----- | ----- |
   | Selecione o ficheiro de modelo da implementação | A paleta de comando realça a **deployment.template.jsficheiro na** sua pasta de solução atual. Selecione o ficheiro.  |
   | Selecionar modelo de módulo | Selecione **Módulo do Azure Marketplace**. |

3. No mercado do módulo Azure IoT Edge, procure e selecione **o Módulo de Servidor SQL**.

4. Mude o nome do módulo para **sql,** todos os minúsculos. Este nome corresponde ao nome do recipiente declarado no fio de ligação no ficheiro sqlFunction.cs.

5. **Selecione Importar** para adicionar o módulo à sua solução.

6. Na pasta da solução, abra a **deployment.template.jsno** ficheiro.

7. Encontre a secção **de módulos.** Devia ver três módulos. O módulo *SimulatedTemperatureSensor* está incluído por padrão em novas soluções e fornece dados de teste para utilizar com os seus outros módulos. O módulo *sqlFunction* é o módulo que inicialmente criou e atualizou com novo código. Finalmente, o módulo *sql* foi importado do Mercado Azure.

   >[!Tip]
   >O módulo SQL Server vem com uma palavra-passe padrão definida nas variáveis ambientais do manifesto de implementação. Sempre que criar um contentor do SQL Server num ambiente de produção, deve [alterar a palavra-passe de administrador do sistema predefinida](/sql/linux/quickstart-install-connect-docker).

8. Feche a **deployment.template.jsarquivada.**

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Nas secções anteriores, criou uma solução com um módulo e, em seguida, adicionou outra ao modelo de manifesto de implementação. O módulo SQL Server é hospedado publicamente pela Microsoft, mas é necessário contentorizar o código no módulo Funções. Nesta secção, constrói a solução, cria imagens de contentores para o módulo sqlFunction e empurra a imagem para o registo do seu contentor.

1. No Código do Estúdio Visual, abra o terminal integrado selecionando **o Terminal de Visualização**  >  **Terminal**.  

1. Inicie sessão no seu registo de contentor do Visual Studio Code para que possa enviar as imagens para o seu registo. Utilize as mesmas credenciais de Registo de Contentores Azure (ACR) que adicionou ao ficheiro .env. Introduza o seguinte comando no terminal integrado:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Pode ver um aviso de segurança recomendando a utilização do parâmetro --password-stdin. Enquanto a sua utilização está fora do âmbito deste artigo, recomendamos que siga esta melhor prática. Para obter mais informações, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. No explorador de código VS, clique com o botão direito ** no ficheirodeployment.template.js** e selecione a **solução Build and Push IoT Edge**.

   O comando de construção e pressão inicia três operações. Em primeiro lugar, cria uma nova pasta na solução chamada **config** que detém o manifesto de implantação completo, que é construído a partir de informações no modelo de implementação e outros ficheiros de solução. Em segundo lugar, funciona `docker build` para construir a imagem do contentor com base no arquivo apropriado para a arquitetura do seu alvo. Em seguida, corre `docker push` para empurrar o repositório de imagem para o seu registo de contentores.

   Este processo pode demorar vários minutos na primeira vez, mas é mais rápido da próxima vez que executar os comandos.

   Pode verificar se o módulo sqlFunction foi empurrado com sucesso para o registo do seu contentor. No portal Azure, navegue para o seu registo de contentores. **Selecione repositórios** e procure **sqlFunction**. Os outros dois módulos, SimulatedTemperatureSensor e sql, não serão empurrados para o seu registo de contentores porque os seus repositórios já estão nos registos da Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Implementar a solução num dispositivo

Pode definir módulos num dispositivo através do Hub IoT, mas também pode aceder o seu Hub IoT e dispositivos através do Visual Studio Code. Nesta secção, pode configurar o acesso ao Hub IoT e, em seguida, utilizar o VS Code para implementar a solução no seu dispositivo IoT Edge.

1. No explorador visual Studio Code, na secção **Azure IoT Hub,** expanda **os Dispositivos** para ver a sua lista de dispositivos IoT.

2. Clique com o botão direito no dispositivo que pretende direcionar com a sua implementação e selecione **Criar Implementação para dispositivo único**.

3. Selecione a **deployment.amd64.jsno** ficheiro na pasta **config** e, em seguida, clique em **Select Edge Deployment Manifest**. Não utilize o ficheiro deployment.template.json.

4. Sob o seu dispositivo, expanda **os Módulos** para ver uma lista de módulos implantados e em funcionamento. Clique no botão Atualizar. Deverá ver os novos módulos **sql** e **sqlFunction** a funcionar juntamente com o módulo **SimuladoTemperatureSensor** e o **$edgeAgent** e **$edgeHub**.

    Também pode verificar se todos os módulos estão em execução no seu dispositivo. No dispositivo IoT Edge, execute o seguinte comando para ver o estado dos módulos.

   ```cmd/sh
   iotedge list
   ```

    Pode levar alguns minutos para os módulos começarem. O tempo de execução IoT Edge precisa de receber o seu novo manifesto de implantação, retirar as imagens do módulo do tempo de funcionamento do contentor e, em seguida, iniciar cada novo módulo.

## <a name="create-the-sql-database"></a>Criar uma base de dados SQL

Ao aplicar o manifesto de implementação ao seu dispositivo, obtém três módulos em execução. O módulo SimuladoSteperatureSensor gera dados de ambiente simulados. O módulo sqlFunction utiliza os dados e formata-os para uma base de dados. Esta secção orienta-o na configuração da base de dados do SQL Server para armazenar os dados de temperatura.

Executar os seguintes comandos no seu dispositivo IoT Edge. Estes comandos ligam-se ao módulo **sql** em funcionamento no seu dispositivo e criam uma base de dados e uma tabela para conter os dados de temperatura que lhe são enviados.

1. Numa ferramenta de linha de comando no seu dispositivo IoT Edge, ligue-se à sua base de dados.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Abra a ferramenta de comandos SQL.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Criar a base de dados:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Defina a tabela.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Pode personalizar o ficheiro de docker do SQL Server para configurar automaticamente o SQL Server para ser implementado em vários dispositivos IoT Edge. Para obter mais informações, veja o [Projeto de demonstração de contentor do Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="view-the-local-data"></a>Ver os dados locais

Assim que a tabela for criada, o módulo sqlFunction começa a armazenar os dados numa base de dados local do SQL Server 2017 no seu dispositivo IoT Edge.

A partir da ferramenta de comandos SQL, execute o seguinte comando para ver os dados de tabela formatados:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Ver conteúdo da base de dados local](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

Neste tutorial, criou um módulo das Funções do Azure que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para construir os seus próprios módulos, poderá aprender mais sobre como [desenvolver funções de Azure com Azure IoT Edge para Código de Estúdio Visual.](./how-to-vs-code-develop-module.md)

## <a name="next-steps"></a>Passos seguintes

Se quiser experimentar outro método de armazenamento na borda, leia como utilizar o Azure Blob Storage on IoT Edge.

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com o Armazenamento de Blobs do Azure no IoT Edge](how-to-store-data-blob.md)