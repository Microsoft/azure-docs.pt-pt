---
title: Tutorial - Armazenar dados com módulo SQL utilizando o Edge Azure IoT
description: Este tutorial mostra como armazenar dados localmente no seu dispositivo IoT Edge com um módulo SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d1b5ea9a9f78bc8a83159a34026d58d7a8cc89b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944277"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Tutorial: Armazenar dados na periferia com bases de dados do SQL Server

Implemente um módulo SQL Server para armazenar dados num dispositivo Linux que executa o Azure IoT Edge.

Utilize o Azure IoT Edge e o SQL Server para armazenar e consultar dados na periferia. O Azure IoT Edge tem capacidades básicas de armazenamento para cache mensagens se um dispositivo ficar offline e, em seguida, reencaminhar-os quando a ligação for restabelecida. No entanto, pode querer capacidades de armazenamento mais avançadas, como a capacidade de consultar dados localmente. Os seus dispositivos IoT Edge podem utilizar bases de dados locais para realizar computação mais complexa sem ter de manter uma ligação ao IoT Hub.

Este artigo fornece instruções para implementar uma base de dados do SQL Server num dispositivo IoT Edge. As Funções do Azure, em execução no dispositivo IoT Edge, estruturam os dados recebidos e, em seguida, envia-os para a base de dados. Os passos neste artigo também podem ser aplicados a outras bases de dados que funcionam em contentores, como o MySQL ou o PostgreSQL.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Utilize o Visual Studio Code para criar uma função do Azure
> * Implementar uma base de dados do SQL Server no seu dispositivo IoT Edge
> * Utilize o Visual Studio Code para criar módulos e implementá-los no seu dispositivo IoT Edge
> * Ver os dados gerados

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deveria ter passado pelo tutorial anterior para configurar o seu ambiente de desenvolvimento para o desenvolvimento de contentores Linux: [Desenvolver módulos IoT Edge para dispositivos Linux](tutorial-develop-for-linux.md). Ao completar esse tutorial, deve ter os seguintes pré-requisitos no lugar:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) no escalão gratuito ou standard no Azure.
* Um dispositivo Linux AMD64 [que executa o Edge Azure IoT](quickstart-linux.md).
  * Dispositivos ARM, como Raspberry Pis, não podem executar o Servidor SQL. Se pretender utilizar o SQL num dispositivo ARM, pode inscrever-se para experimentar o [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) na pré-visualização.
* Um registo de contentores, como o Registo de [Contentores Azure.](https://docs.microsoft.com/azure/container-registry/)
* [Código de estúdio visual](https://code.visualstudio.com/) configurado com as [ferramentas Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configurado para executar contentores Linux.

Este tutorial utiliza um módulo de Funções Azure para enviar dados para o Servidor SQL. Para desenvolver um módulo IoT Edge com Funções Azure, instale os seguintes pré-requisitos adicionais na sua máquina de desenvolvimento:

* [C# para Código de Estúdio Visual (alimentado por OmniSharp) extensão para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [SDK de .NET Core 2.1](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Criar um projeto de função

Para enviar dados numa base de dados, precisa de um módulo que possa estruturar corretamente os dados e, em seguida, armazená-los numa tabela.

### <a name="create-a-new-project"></a>Criar um novo projeto

Os seguintes passos mostram-lhe como criar uma função IoT Edge utilizando o Código de Estúdio Visual e as Ferramentas Azure IoT.

1. Abra o Visual Studio Code.

2. Abra a paleta de comando vs Código selecionando a paleta de**comando** **de visualização** > .

3. Na paleta de comandos, escreva e execute o comando **Azure IoT Edge: Nova solução do IoT Edge**. Na paleta de comandos, indique as seguintes informações para criar a sua solução:

   | Campo | Valor |
   | ----- | ----- |
   | Selecionar pasta | Escolha a localização no computador de desenvolvimento na qual o VS Code vai criar os ficheiros da solução. |
   | Indicar um nome para a solução | Introduza um nome descritivo para a sua solução, como **sqlSolution,** ou aceite o padrão. |
   | Selecionar modelo de módulo | Escolha **funções Azure - C#**. |
   | Indicar um nome para o módulo | Atribua o nome **sqlFunction** ao módulo. |
   | Indicar o repositório de imagens do Docker para o módulo | Os repositórios de imagens incluem o nome do seu registo de contentor e o nome da sua imagem de contentor. A imagem de contentor é pré-preenchida no passo anterior. Substitua **localhost:5000** pelo valor do servidor de início de sessão do registo de contentor do Azure Container Registry. Pode obter o servidor de início de sessão na página Overview (Descrição Geral) do registo de contentor no portal do Azure. <br><br>A corda final \<parece\>o nome do registo .azurecr.io/sqlfunction. |

   A janela do VS Code carrega a área de trabalho da solução do IoT Edge.

### <a name="add-your-registry-credentials"></a>Adicionar as credenciais do registo

O ficheiro de ambiente armazena as credenciais do seu registo de contentor e partilha-as com o runtime do IoT Edge. O runtime precisa destas credenciais para solicitar as imagens privadas para o dispositivo IoT Edge.

1. No explorador do VS Code, abra o ficheiro .env.
2. Atualize os campos com os valores **nome de utilizador** e **palavra-passe** que copiou do seu Azure Container Registry.
3. Guarde este ficheiro.

### <a name="select-your-target-architecture"></a>Selecione a sua arquitetura alvo

Atualmente, o Visual Studio Code pode desenvolver módulos C para dispositivos Linux AMD64 e Linux ARM32v7. Você precisa selecionar qual arquitetura você está alvo com cada solução, porque o recipiente é construído e executado de forma diferente para cada tipo de arquitetura. O padrão é Linux AMD64.

1. Abra a paleta de comando e procure o **Azure IoT Edge: Definir**a Plataforma de Destino Padrão para Solução de Borda , ou selecione o ícone de atalho na barra lateral na parte inferior da janela.

2. Na paleta de comando, selecione a arquitetura alvo da lista de opções. Para este tutorial, estamos a usar uma máquina virtual Ubuntu como dispositivo IoT Edge, por isso manterá o **amd64**padrão .

### <a name="update-the-module-with-custom-code"></a>Atualizar o módulo com o código personalizado

1. No explorador do Código VS, os **módulos abertos** > **sqlFunction** > **sqlFunction.cs**.

2. Substitua todo o conteúdo do ficheiro pelo seguinte código:

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

3. Na linha 35, substitua a ** \<corda\> de ligação sql** de corda com a seguinte corda. A propriedade **Data Source** refere o recipiente SQL Server, que ainda não existe. Irá criá-lo com o nome **SQL** na secção seguinte.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Guarde o ficheiro **sqlFunction.cs.**

5. Abra o ficheiro **sqlFunction.csproj.**

6. Encontre o grupo de referências do pacote e adicione um novo para incluir o SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Guarde o ficheiro **sqlFunction.csproj.**

## <a name="add-the-sql-server-container"></a>Adicione o recipiente SQL Server

Um [Manifesto de implementação](module-composition.md) declara os módulos que o runtime do IoT Edge irá instalar no seu dispositivo IoT Edge. Forneceu o código para fazer um módulo de Função personalizado na secção anterior, mas o módulo SQL Server já está construído e disponível no Mercado Azure. Apenas tem de indicar ao runtime do IoT Edge para o incluir e configurar no seu dispositivo.

1. No Código do Estúdio Visual, abra a paleta de comando selecionando a paleta de**comando**de **visualização** > .

2. Na paleta de comando, digite e corra o comando **Azure IoT Edge: Adicione o módulo IoT Edge**. Na paleta de comando, forneça as seguintes informações para adicionar um novo módulo:

   | Campo | Valor |
   | ----- | ----- |
   | Selecione o ficheiro de modelo da implementação | A paleta de comando destaca o ficheiro deployment.template.json na sua pasta de solução atual. Selecione o ficheiro.  |
   | Selecionar modelo de módulo | Selecione **Módulo do Mercado Azure**. |

3. No mercado do módulo Azure IoT Edge, procure e selecione **Módulo servidor SQL**.

4. Mude o nome do módulo para **sql,** toda a minúscula. Este nome corresponde ao nome do recipiente declarado na cadeia de ligação no ficheiro sqlFunction.cs.

5. Selecione **Importar** para adicionar o módulo à sua solução.

6. Na sua pasta de solução, abra o ficheiro **deployment.template.json.**

7. Encontre a secção de **módulos.** Devia ver três módulos. O módulo *SimuladoTemperatureSensor* está incluído por padrão em novas soluções e fornece dados de teste para usar com os outros módulos. O módulo *sqlFunction* é o módulo que inicialmente criou e atualizou com o novo código. Finalmente, o módulo *sql* foi importado do Mercado Azure.

   >[!Tip]
   >O módulo SQL Server vem com uma palavra-passe padrão definida nas variáveis ambientais do manifesto de implementação. Sempre que criar um contentor do SQL Server num ambiente de produção, deve [alterar a palavra-passe de administrador do sistema predefinida](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Feche o ficheiro **deployment.template.json.**

## <a name="build-your-iot-edge-solution"></a>Criar a sua solução do IoT Edge

Nas secções anteriores, criou uma solução com um módulo e, em seguida, adicionou outra ao modelo de manifesto de implementação. O módulo SQL Server é hospedado publicamente pela Microsoft, mas é necessário containerizar o código no módulo Funções. Nesta secção, constrói-se a solução, cria imagens de contentores para o módulo sqlFunction e empurra a imagem para o registo do recipiente.

1. No Visual Studio Code, abra o terminal integrado selecionando o**Terminal** **de Visualização** > .  

1. Inicie sessão no seu registo de contentor do Visual Studio Code para que possa enviar as imagens para o seu registo. Utilize as mesmas credenciais de Registo de Contentores Azure (ACR) que adicionou ao ficheiro .env. Introduza o seguinte comando no terminal integrado:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Pode ver um aviso de segurança recomendando a utilização do parâmetro-palavra-passe-stdin. Enquanto a sua utilização está fora do âmbito deste artigo, recomendamos que siga esta melhor prática. Para mais informações, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. No explorador de código VS, clique no ficheiro **deployment.template.json** e selecione **Build and Push IoT Edge solution**.

Quando diz ao Visual Studio Code para construir a sua solução, primeiro pega na informação no modelo de implementação e gera um ficheiro implementação.json numa nova pasta chamada **config**. Em seguida, executa dois comandos `docker build` no `docker push`terminal integrado: e . O comando de construção constrói o seu código e contentoriza o módulo. Em seguida, o comando push empurra o código para o registo do recipiente que especificou quando inicializou a solução.

Pode verificar se o módulo sqlFunction foi empurrado com sucesso para o registo do seu contentor. No portal Azure, navegue para o seu registo de contentores. Selecione **repositórios** e procure por **sqlFunction**. Os outros dois módulos, SimuladTemperatureSensor e sql, não serão empurrados para o registo do seu contentor porque os seus repositórios já estão nos registos da Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Implementar a solução num dispositivo

Pode definir módulos num dispositivo através do Hub IoT, mas também pode aceder o seu Hub IoT e dispositivos através do Visual Studio Code. Nesta secção, pode configurar o acesso ao Hub IoT e, em seguida, utilizar o VS Code para implementar a solução no seu dispositivo IoT Edge.

1. No explorador do VS Code, expanda a secção **Dispositivos do Hub IoT do Azure**.

2. Clique com o botão direito do rato no dispositivo destinado à implementação e selecione **Criar Implementação para o único dispositivo**.

3. No explorador de ficheiros, navegue para a pasta **de config** dentro da sua solução e escolha **implementação.amd64**. Clique em **Selecionar manifesto de implementação do Edge**.

   Não utilize o ficheiro deployment.template.json como um manifesto de implantação.

Se a implementação for bem-sucedida, é apresentada uma mensagem de confirmação no resultado do VS Code.

Refresque o estado do seu dispositivo na secção dispositivos Hub Azure IoT do Código VS. Os novos módulos estão listados e começarão a reportar como correndo ao longo dos próximos minutos à medida que os recipientes são instalados e iniciados. Também pode verificar se todos os módulos estão em execução no seu dispositivo. No dispositivo IoT Edge, execute o seguinte comando para ver o estado dos módulos.

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Criar uma base de dados SQL

Ao aplicar o manifesto de implementação ao seu dispositivo, obtém três módulos em execução. O módulo SimuladoTemperatureSensor gera dados de ambiente simulados. O módulo sqlFunction utiliza os dados e formata-os para uma base de dados. Esta secção orienta-o na configuração da base de dados do SQL Server para armazenar os dados de temperatura.

Execute os seguintes comandos no seu dispositivo IoT Edge. Estes comandos ligam-se ao módulo **sql** que funciona no seu dispositivo e criam uma base de dados e uma tabela para manter os dados de temperatura que lhe são enviados.

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

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um módulo das Funções do Azure que contém código para filtrar dados não processados gerados pelo seu dispositivo IoT Edge. Quando estiver pronto para construir os seus próprios módulos, poderá aprender mais sobre como [desenvolver funções azure com O Edge Azure IoT para Código](how-to-develop-csharp-function.md)de Estúdio Visual .

Se quiser experimentar outro método de armazenamento na borda, leia sobre como utilizar o Armazenamento De Blob Azure no IoT Edge.

> [!div class="nextstepaction"]
> [Armazenar dados na periferia com o Armazenamento de Blobs do Azure no IoT Edge](how-to-store-data-blob.md)
