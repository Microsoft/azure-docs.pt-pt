---
title: Visualização de dados em tempo real dos seus dados do hub IoT numa aplicação web
description: Utilize uma aplicação web para visualizar dados de temperatura e humidade que são recolhidos a partir de um sensor e enviados para o seu hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 138e077f7b47fa9f38a4710db95eb7208cef78e3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675320"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizar dados de sensores em tempo real do seu hub Azure IoT numa aplicação web

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste tutorial, aprende a visualizar dados de sensores em tempo real que o seu hub IoT recebe com uma aplicação web node.js a funcionar no seu computador local. Depois de executar a aplicação web localmente, você pode opcionalmente seguir passos para hospedar a aplicação web no Azure App Service. Se quiser visualizar os dados no seu hub IoT utilizando o Power BI, consulte [o Use Power BI para visualizar dados de sensores em tempo real a partir do Hub Azure IoT](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que faz

* Adicione um grupo de consumidores ao seu hub IoT que a aplicação web usará para ler dados de sensores
* Descarregue o código da aplicação web a partir do GitHub
* Examinar o código da aplicação web
* Configure variáveis ambientais para conter os artefactos IoT Hub necessários pela sua aplicação web
* Execute a aplicação web na sua máquina de desenvolvimento
* Abra uma página web para ver dados de temperatura e humidade em tempo real do seu hub IoT
* (Opcional) Use o Azure CLI para hospedar a sua aplicação web no Serviço de Aplicações Azure

## <a name="what-you-need"></a>Do que precisa

* Complete o tutorial de [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com nó.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes cobrem os seguintes requisitos:

  * Uma subscrição ativa do Azure
  * Um hub de iot sob a sua assinatura
  * Uma aplicação de cliente que envia mensagens para o seu hub de iot

* [Baixar Git](https://www.git-scm.com/downloads)

* Os passos neste artigo assumem uma máquina de desenvolvimento do Windows; no entanto, pode facilmente executar estes passos num sistema Linux na sua concha preferida.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Execute o seguinte comando para adicionar a extensão Microsoft Azure IoT para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicione um grupo de consumidores ao seu hub IoT

[Os grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecem pontos de vista independentes para o fluxo de eventos que permitem que apps e serviços Azure consumam dados independentemente do mesmo ponto final do Event Hub. Nesta secção, você adiciona um grupo de consumidores ao ponto final incorporado do seu ioT hub que a aplicação web usará para ler dados a partir de.

Execute o seguinte comando para adicionar um grupo de consumidores ao ponto final incorporado do seu hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Anote o nome que escolher, vai precisar dele mais tarde neste tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obtenha uma cadeia de conexão de serviço para o seu hub IoT

Os hubs IoT são criados com várias políticas de acesso padrão. Uma dessas políticas é a política de **serviços,** que fornece permissões suficientes para um serviço para ler e escrever os pontos finais do centro ioT. Execute o seguinte comando para obter uma corda de ligação para o seu hub IoT que adere à política de serviço:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A cadeia de ligação deve ser semelhante à seguinte:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Note a cadeia de ligação de serviço, você precisará dele mais tarde neste tutorial.

## <a name="download-the-web-app-from-github"></a>Descarregue a aplicação web do GitHub

Abra uma janela de comando e introduza os seguintes comandos para descarregar a amostra do GitHub e mudar para o diretório da amostra:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examinar o código da aplicação web

A partir do diretório web-apps-node-iot-hub-visualização de dados, abra a aplicação web no seu editor favorito. O seguinte mostra a estrutura de ficheiros visualizada no Código VS:

![Estrutura de ficheiros de aplicativoweb](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Tire um momento para examinar os seguintes ficheiros:

* **Server.js** é um script do lado do serviço que inicializa a tomada web e a classe de invólucro do Event Hub. Fornece uma chamada de volta para a classe de invólucro do Event Hub que a classe usa para transmitir mensagens de entrada para a tomada web.

* **Event-hub-reader.js** é um script do lado do serviço que se conecta ao ponto final incorporado do hub IoT usando a cadeia de ligação especificada e grupo de consumidores. Extrai o DeviceId e o EnqueuedTimeUtc de metadados em mensagens de entrada e, em seguida, transmite a mensagem utilizando o método de chamada registado pelo servidor.js.

* **Chart-device-data.js** é um script do lado do cliente que ouve na tomada web, acompanha cada DispositivoId e armazena os últimos 50 pontos de dados de entrada para cada dispositivo. Em seguida, liga os dados do dispositivo selecionados ao objeto gráfico.

* **O Index.html** trata do layout ui para a página web e refere os scripts necessários para a lógica do lado do cliente.

## <a name="configure-environment-variables-for-the-web-app"></a>Configure variáveis ambientais para a aplicação web

Para ler dados do seu hub IoT, a aplicação web precisa da cadeia de conexão do seu hub IoT e do nome do grupo de consumidores que deve ler. Obtém estas cordas do ambiente de processo nas seguintes linhas no servidor.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Coloque as variáveis ambientais na janela de comando com os seguintes comandos. Substitua os valores do espaço reservado pela cadeia de ligação de serviço para o seu hub IoT e o nome do grupo de consumidores que criou anteriormente. Não cite as cordas.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. Certifique-se de que o seu dispositivo está a funcionar e a enviar dados.

2. Na janela de comando, faça as seguintes linhas para descarregar e instalar pacotes referenciados e iniciar o website:

   ```cmd
   npm install
   npm start
   ```

3. Deve ver a saída na consola que indica que a aplicação web tem ligação com sucesso ao seu hub IoT e está a ouvir na porta 3000:

   ![Aplicação web começou na consola](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Abra uma página web para ver dados do seu hub IoT

Abra um navegador para `http://localhost:3000`.

Na lista **Select a device,** selecione o seu dispositivo para ver um plano de execução dos últimos 50 pontos de dados de temperatura e humidade enviados pelo dispositivo para o seu hub IoT.

![Página da aplicação web mostrando temperatura e humidade em tempo real](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Também deve ver a saída na consola que mostra as mensagens que a sua aplicação web está a transmitir para o cliente do navegador:  

![Saída de transmissão de aplicativo web na consola](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hospedar a aplicação web no Serviço de Aplicações

A [funcionalidade de Aplicações Web do Azure App Service](https://docs.microsoft.com/azure/app-service/overview) fornece uma plataforma como serviço (PAAS) para hospedar aplicações web. As aplicações web alojadas no Azure App Service podem beneficiar de funcionalidades poderosas do Azure, como segurança adicional, equilíbrio de carga e escalabilidade, bem como soluções Azure e partner DevOps, como implementação contínua, gestão de pacotes, e assim por diante. O Azure App Service suporta aplicações web desenvolvidas em muitas línguas populares e implementadas na infraestrutura Windows ou Linux.

Nesta secção, você disponibiliza uma aplicação web no Serviço de Aplicações e implementa o seu código através da utilização de comandos Azure CLI. Pode encontrar detalhes dos comandos utilizados na documentação da [AZ WebApp.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Antes de começar, certifique-se de que completou os passos para adicionar um grupo de [recursos ao seu hub IoT,](#add-a-consumer-group-to-your-iot-hub)obtenha uma cadeia de ligação de serviço para o seu hub [IoT](#get-a-service-connection-string-for-your-iot-hub), e [descarregue a aplicação web do GitHub](#download-the-web-app-from-github).

1. Um plano de Serviço de [Aplicações](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) define um conjunto de recursos computacionais para uma aplicação hospedada no App Service para executar. Neste tutorial, utilizamos o nível Developer/Free para hospedar a aplicação web. Com o free tier, a sua aplicação web funciona em recursos do Windows partilhados com outras aplicações do App Service, incluindo apps de outros clientes. O Azure também oferece planos do App Service para implementar aplicações web em recursos computacionais Linux. Pode saltar este passo se já tiver um plano de Serviço de Aplicações que deseja utilizar.

   Para criar um plano de Serviço de Aplicações utilizando o nível livre do Windows, execute o seguinte comando. Use o mesmo grupo de recursos em que o seu hub IoT está. O nome do seu plano de serviço pode conter letras maiúsculas e minúsculas, números e hífenes.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Agora, disponibilize uma aplicação web no seu plano de Serviço de Aplicações. O parâmetro `--deployment-local-git` permite que o código da aplicação web seja carregado e implantado a partir de um repositório Git na sua máquina local. O nome da sua aplicação web deve ser globalmente único e pode conter letras maiúsculas e minúsculas, números e hífenes. Certifique-se de especificar a versão nó 10.6 ou posterior para o parâmetro `--runtime`, dependendo da versão do tempo de execução noNó.js que estiver a utilizar. Pode usar o comando `az webapp list-runtimes` para obter uma lista de tempos de execução suportados.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Adicione agora as Definições de Aplicação para as variáveis ambientais que especificam a cadeia de ligação do hub IoT e o grupo de consumidores do hub do Evento. As configurações individuais são delimitadas no espaço no parâmetro `-settings`. Utilize a cadeia de ligação de serviço para o seu hub IoT e o grupo de consumidores que criou anteriormente neste tutorial. Não cite os valores.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Ative o protocolo Web Sockets para a aplicação web e configura ruma a aplicação web para receber apenas pedidos HTTPS (os pedidos HTTP são redirecionados para HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Para implementar o código para o Serviço de Aplicações, utilizará [as suas credenciais de implementação ao nível do utilizador](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). As suas credenciais de implementação ao nível do utilizador são diferentes das suas credenciais Azure e são utilizadas para implementações locais e FTP git para uma aplicação web. Uma vez definidos, são válidos em todas as suas aplicações do App Service em todas as subscrições da sua conta Azure. Se tiver definido previamente credenciais de implementação ao nível do utilizador, pode utilizá-las.

   Se não tiver definido previamente as credenciais de implementação ao nível do utilizador ou se não se lembrar da sua palavra-passe, execute o seguinte comando. O seu nome de utilizador de implementação deve ser único dentro do Azure, e não deve conter o símbolo '@' para os pushs git locais. Quando for solicitado, insira e confirme a sua nova senha. A palavra-passe deve ter pelo menos oito caracteres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Faça com que o URL Git utilize para empurrar o seu código para o Serviço de Aplicações.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adicione um controlo remoto ao seu clone que faz referência ao repositório Git para a aplicação web no App Service. Para \<url de clone Git\>, utilize o URL devolvido no passo anterior. Execute o seguinte comando na janela de comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Para implementar o código para o Serviço de Aplicações, introduza o seguinte comando na janela de comando. Se for solicitado para obter credenciais, introduza as credenciais de implementação ao nível do utilizador que criou no passo 5. Certifique-se de que empurra para o ramo principal do comando do Serviço de Aplicações.

    ```cmd
    git push webapp master:master
    ```

9. O progresso da implantação irá atualizar-se na sua janela de comando. Uma implantação bem sucedida terminará com linhas semelhantes à seguinte saída:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Execute o seguinte comando para consultar o estado da sua aplicação web e certifique-se de que está em execução:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Navegue para `https://<your web app name>.azurewebsites.net` num browser. Uma página web semelhante à que viu quando executou as exibições locais da aplicação web. Assumindo que o seu dispositivo está a funcionar e a enviar dados, deve ver um enredo de execução das 50 leituras mais recentes de temperatura e humidade enviadas pelo dispositivo.

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar algum problema com esta amostra, experimente os passos nas seguintes secções. Se ainda tiver problemas, envie-nos feedback na parte inferior deste tópico.

### <a name="client-issues"></a>Problemas com o cliente

* Se um dispositivo não aparecer na lista, ou se não estiver a ser desenhado nenhum gráfico, certifique-se de que o código do dispositivo está a funcionar no seu dispositivo.

* No navegador, abra as ferramentas de desenvolvimento (em muitos navegadores a chave F12 irá abri-la), e encontrar a consola. Procure por quaisquer avisos ou erros impressos lá.

* Pode depurar o script do lado do cliente em /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemas no site local

* Observe a saída na janela onde lançou o nó para a saída da consola.

* Desinver o código do servidor, especificamente servidor.js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemas do Serviço de Aplicações Azure

* No portal Azure, vá à sua aplicação web. Sob **monitorização** no painel esquerdo, selecione registos do **Serviço de Aplicações**. Rode o **registo de aplicações (sistema de ficheiros)** ligado, coloque o **Nível** para o Erro e, em seguida, selecione **Save**. Em seguida, abra **o fluxo de registo** (sob **monitorização**).

* A partir da sua aplicação web no portal Azure, em **Ferramentas** de Desenvolvimento selecione **Console** e valide versões de nó e npm com `node -v` e `npm -v`.

* Se vir um erro em não encontrar um pacote, pode ter corrido os passos fora de ordem. Quando o site é implantado (com `git push`), o serviço de aplicações corre `npm install`, que funciona com base na versão atual do nó que configura. Se isso for alterado na configuração mais tarde, terá de fazer uma alteração sem sentido no código e empurrar novamente.

## <a name="next-steps"></a>Passos seguintes

Usou com sucesso a sua aplicação web para visualizar dados de sensores em tempo real a partir do seu hub IoT.

Para mais uma forma de visualizar os dados do Hub Azure IoT, consulte [o Use Power BI para visualizar dados de sensores em tempo real a partir do seu hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
