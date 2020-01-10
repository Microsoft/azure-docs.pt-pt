---
title: Tutorial – armazenar dados com o módulo SQL usando Azure IoT Edge
description: Este tutorial mostra como armazenar dados localmente em seu dispositivo IoT Edge com um módulo SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c16fca06950ea06b80f2e27d6fb845f5d0d282c0
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665118"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Armazenar dados na periferia com bases de dados do SQL Server

Implante um módulo SQL Server para armazenar dados em um dispositivo Linux que executa o Azure IoT Edge.

Utilize o Azure IoT Edge e o SQL Server para armazenar e consultar dados na periferia. O Azure IoT Edge tem capacidades de armazenamento básico em cache as mensagens se um dispositivo ficar offline e, em seguida, reencaminhe-os quando a ligação for restabelecida. No entanto, pode querer capacidades de armazenamento mais avançadas, como a capacidade de consultar dados localmente. Seus dispositivos IoT Edge podem usar bancos de dados locais para executar uma computação mais complexa sem precisar manter uma conexão com o Hub IoT. 

Este artigo fornece instruções para implementar uma base de dados do SQL Server num dispositivo IoT Edge. As Funções do Azure, em execução no dispositivo IoT Edge, estruturam os dados recebidos e, em seguida, envia-os para a base de dados. Os passos neste artigo também podem ser aplicados a outras bases de dados que funcionam em contentores, como o MySQL ou o PostgreSQL.

Neste tutorial, ficará a saber como: 

> [!div class="checklist"]
> * Utilize o Visual Studio Code para criar uma função do Azure
> * Implementar uma base de dados do SQL Server no seu dispositivo IoT Edge
> * Utilize o Visual Studio Code para criar módulos e implementá-los no seu dispositivo IoT Edge
> * Ver os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter passado pelo tutorial anterior para configurar seu ambiente de desenvolvimento para desenvolvimento de contêiner do Linux: [desenvolver módulos IOT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao concluir esse tutorial, você deve ter os seguintes pré-requisitos em vigor: 

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um [dispositivo AMD64 Linux em execução Azure IOT Edge](quickstart-linux.md).
  * Dispositivos ARM, como Raspberry PIS, não podem executar SQL Server. Se você quiser usar o SQL em um dispositivo ARM, poderá se inscrever para experimentar a [borda do banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database-edge/) na versão prévia. 
* Um registro de contêiner, como o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configurado com as [ferramentas de IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contêineres do Linux.

Este tutorial usa um módulo Azure Functions para enviar dados para o SQL Server. Para desenvolver um módulo IoT Edge com Azure Functions, instale os seguintes pré-requisitos adicionais em seu computador de desenvolvimento: 

* [C#para a extensão do Visual Studio Code (com tecnologia da omnisharp) para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Criar um projeto de função

Para enviar dados numa base de dados, precisa de um módulo que possa estruturar corretamente os dados e, em seguida, armazená-los numa tabela. 

### <a name="create-a-new-project"></a>Criar um novo projeto

As etapas a seguir mostram como criar uma função de IoT Edge usando Visual Studio Code e as ferramentas de IoT do Azure.

1. Abra o Visual Studio Code.

2. Abra a paleta de comandos do VS Code ao selecionar **Ver** > **Paleta de Comandos**.

3. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, indique as seguintes informações para criar a sua solução: 

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução, como **SqlSolution**, ou aceite a predefinição. |
   | Selecionar modelo de módulo | Escolher **as funções do Azure - C#** . |
   | Indicar um nome para o módulo | Atribua o nome **sqlFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>A cadeia de caracteres final é semelhante a \<nome do registro\>. azurecr.io/sqlfunction. |

   A janela do VS Code carrega a área de trabalho da solução do IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione sua arquitetura de destino

Atualmente, Visual Studio Code pode desenvolver módulos C para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura está sendo direcionada a cada solução, porque o contêiner é compilado e executado de forma diferente para cada tipo de arquitetura. O padrão é o Linux AMD64. 

1. Abra a paleta de comandos e pesquise **Azure IOT Edge: definir a plataforma de destino padrão para a solução de borda**ou selecione o ícone de atalho na barra lateral na parte inferior da janela. 

2. Na paleta de comandos, selecione a arquitetura de destino na lista de opções. Para este tutorial, estamos usando uma máquina virtual Ubuntu como o dispositivo IoT Edge, portanto, manterá o **AMD64**padrão. 

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No Explorador do VS Code, abra **módulos** > **sqlFunction** > **sqlFunction.cs**.

2. Substitua todo o conteúdo do arquivo pelo código a seguir:

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

3. Na linha 35, substitua a cadeia de caracteres **\<cadeia de ligação sql\>** com a seguinte cadeia de caracteres. A propriedade da **fonte de dados** faz referência ao contêiner SQL Server, que ainda não existe, mas você o criará com o nome **SQL** na próxima seção. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Guardar a **sqlFunction.cs** ficheiro. 

5. Abra o **sqlFunction.csproj** ficheiro.

6. Localize o grupo de referências de pacote e adicione um novo para incluir o SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Guardar a **sqlFunction.csproj** ficheiro.

## <a name="add-the-sql-server-container"></a>Adicionar o contêiner de SQL Server

Um [Manifesto de implementação](module-composition.md) declara os módulos que o runtime do IoT Edge irá instalar no seu dispositivo IoT Edge. Você forneceu o código para criar um módulo de função personalizado na seção anterior, mas o módulo SQL Server já está compilado e disponível no Azure Marketplace. Apenas tem de indicar ao runtime do IoT Edge para o incluir e configurar no seu dispositivo. 

1. Em Visual Studio Code, abra a paleta de comandos selecionando **exibir** > **paleta de comandos**.

2. Na paleta de comandos, digite e execute o comando **Azure IOT Edge: adicionar IOT Edge módulo**. Na paleta de comandos, forneça as seguintes informações para adicionar um novo módulo: 

   | Campo | Valor | 
   | ----- | ----- |
   | Selecione o ficheiro de modelo da implementação | A paleta de comandos realça o arquivo Deployment. Template. JSON em sua pasta de solução atual. Selecione esse arquivo.  |
   | Selecionar modelo de módulo | Selecione o **módulo do Azure Marketplace**. |

3. No módulo Azure IoT Edge Marketplace, procure e selecione **SQL Server módulo**. 

4. Altere o nome do módulo para **SQL**, tudo em minúsculas. Esse nome corresponde ao nome do contêiner declarado na cadeia de conexão no arquivo sqlFunction.cs. 

5. Selecione **importar** para adicionar o módulo à sua solução. 

6. Na pasta da solução, abra o arquivo **Deployment. Template. JSON** . 

7. Encontrar o **módulos** secção. Você deve ver três módulos. O módulo *SimulatedTemperatureSensor* é incluído por padrão em novas soluções e fornece dados de teste para uso com seus outros módulos. O módulo *SqlFunction* é o módulo criado inicialmente e atualizado com o novo código. Por fim, o módulo *SQL* foi importado do Azure Marketplace. 

   >[!Tip]
   >O módulo SQL Server vem com uma senha padrão definida nas variáveis de ambiente do manifesto de implantação. Sempre que criar um contentor do SQL Server num ambiente de produção, deve [alterar a palavra-passe de administrador do sistema predefinida](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Feche o arquivo **Deployment. Template. JSON** .

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Nas secções anteriores, criou uma solução com um módulo e, em seguida, adicionou outra ao modelo de manifesto de implementação. O módulo SQL Server é hospedado publicamente pela Microsoft, mas você precisa colocar o código em contêiner no módulo funções. Nesta seção, você criará a solução, criará imagens de contêiner para o módulo SqlFunction e enviará por push a imagem para o registro de contêiner. 

1. No Visual Studio Code, selecione **View** (Ver)  > **Terminal** para abrir o terminal integrado do VS Code.  

1. Inicie sessão no seu registo de contentor do Visual Studio Code para que possa enviar as imagens para o seu registo. Use as mesmas credenciais de ACR (registro de contêiner do Azure) que você adicionou ao arquivo. env. Introduza o seguinte comando no terminal integrado:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Você poderá ver um aviso de segurança recomendando o uso do parâmetro--password-stdin. Enquanto a sua utilização está fora do âmbito deste artigo, recomendamos que siga esta melhor prática. Para obter mais informações, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) . 

2. No explorador do VS Code, clique com o botão direito do rato no ficheiro **deployment.template.json** e selecione **Criar e Emitir solução do IoT Edge**. 

Quando você informa Visual Studio Code para criar sua solução, ela primeiro pega as informações no modelo de implantação e gera um arquivo Deployment. JSON em uma nova pasta chamada **config**. Em seguida, ele executa dois comandos no terminal integrado: `docker build` e `docker push`. Estes dois comandos Compile seu código, o módulo de colocar num contentor e, em seguida, enviar o código para o registo de contentor que especificou quando a solução é inicializado. 

Você pode verificar se o módulo SqlFunction foi enviado com êxito para o registro de contêiner. Na portal do Azure, navegue até o registro de contêiner. Selecione **repositórios** e pesquise **SqlFunction**. Os outros dois módulos, SimulatedTemperatureSensor e SQL, não serão enviados para o registro de contêiner porque você já está apontando para seus repositórios nos registros da Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Implementar a solução num dispositivo

Pode definir módulos num dispositivo através do Hub IoT, mas também pode aceder o seu Hub IoT e dispositivos através do Visual Studio Code. Nesta secção, pode configurar o acesso ao Hub IoT e, em seguida, utilizar o VS Code para implementar a solução no seu dispositivo IoT Edge. 

1. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**. 

2. Clique com o botão direito do rato no dispositivo destinado à implementação e selecione **Criar Implementação para o único dispositivo**. 

3. No Explorador de ficheiros, navegue para o **config** pasta dentro de sua solução e escolha **deployment.amd64**. Clique em **Selecionar manifesto de implementação do Edge**. 

   Não use o arquivo Deployment. Template. JSON como um manifesto de implantação.

Se a implementação for bem-sucedida, é apresentada uma mensagem de confirmação no resultado do VS Code. 

Atualize o estado do seu dispositivo na secção de dispositivos de Hub IoT do Azure do VS Code. Os novos módulos são listados e começarão a ser relatados como sendo executados nos próximos minutos, já que os contêineres são instalados e iniciados. Também pode verificar se todos os módulos estão em execução no seu dispositivo. No dispositivo IoT Edge, execute o seguinte comando para ver o estado dos módulos. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Criar uma base de dados SQL

Ao aplicar o manifesto de implementação ao seu dispositivo, obtém três módulos em execução. O módulo SimulatedTemperatureSensor gera dados de ambiente simulados. O módulo sqlFunction utiliza os dados e formata-os para uma base de dados. Esta secção orienta-o na configuração da base de dados do SQL Server para armazenar os dados de temperatura. 

Execute os seguintes comandos no seu dispositivo IoT Edge. Esses comandos se conectam ao módulo do **SQL** em execução no seu dispositivo e criam um banco de dados e uma tabela para manter a data de temperatura que está sendo enviada a ele. 

1. Uma ferramenta da linha de comandos no seu dispositivo IoT Edge, ligar à base de dados. 
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

   ![Ver conteúdos da base de dados local](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Limpar recursos

Se planeia avançar para o próximo artigo recomendado, pode manter os recursos e as configurações que criou e reutilizá-los. Também pode continuar a utilizar o mesmo dispositivo IoT Edge como um dispositivo de teste. 

Caso contrário, pode eliminar as configurações locais e os recursos do Azure que criou neste artigo para evitar custos. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um módulo das Funções do Azure que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para criar os seus próprios módulos, pode saber mais sobre como [Desenvolver Funções do Azure com o Azure IoT Edge para Visual Studio Code](how-to-develop-csharp-function.md). 

Se você quiser tentar outro método de armazenamento na borda, leia sobre como usar o armazenamento de BLOBs do Azure em IoT Edge. 

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com o Armazenamento de Blobs do Azure no IoT Edge](how-to-store-data-blob.md)
