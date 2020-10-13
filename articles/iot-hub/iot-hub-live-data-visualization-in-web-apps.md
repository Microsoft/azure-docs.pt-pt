---
title: Visualização de dados em tempo real dos seus dados do hub IoT numa aplicação web
description: Utilize uma aplicação web para visualizar dados de temperatura e humidade que são recolhidos a partir de um sensor e enviados para o seu hub de iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 6a8f39ae5d73bade2c86a7e15efe75956c2aed24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327570"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualize os dados do sensor em tempo real do seu hub Azure IoT numa aplicação web

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste tutorial, aprende-se a visualizar dados de sensores em tempo real que o seu hub IoT recebe com uma aplicação web node.js a decorrer no seu computador local. Depois de executar a aplicação web localmente, você pode opcionalmente seguir passos para hospedar a aplicação web no Azure App Service. Se quiser tentar visualizar os dados no seu hub IoT utilizando o Power BI, consulte [o Power BI para visualizar os dados do sensor em tempo real a partir do Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que faz

* Adicione um grupo de consumidores ao seu hub IoT que a aplicação web usará para ler dados de sensores
* Descarregue o código de aplicações web do GitHub
* Examine o código de aplicação web
* Configure variáveis ambientais para manter os artefactos IoT Hub necessários pela sua aplicação web
* Execute a aplicação web na sua máquina de desenvolvimento
* Abra uma página web para ver os dados de temperatura e humidade em tempo real do seu hub IoT
* (Opcional) Utilize o Azure CLI para hospedar a sua aplicação web no Azure App Service

## <a name="what-you-need"></a>O que precisa

* Complete o tutorial [de simulador on-line Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:

  * Uma subscrição ativa do Azure
  * Um hub Iot sob a sua assinatura
  * Uma aplicação de cliente que envia mensagens para o seu centro de iot

* [Baixar Git](https://www.git-scm.com/downloads)

* Os passos deste artigo assumem uma máquina de desenvolvimento Windows; no entanto, pode facilmente executar estes passos num sistema Linux na sua concha preferida.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Executar o seguinte comando para adicionar a extensão IoT do Microsoft Azure para Azure CLI à sua instância Cloud Shell. A extensão IOT adiciona comandos específicos do IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) ao Azure CLI.

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicione um grupo de consumidores ao seu hub IoT

[Os grupos de consumidores fornecem vistas independentes](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) sobre o fluxo de eventos que permitem que as aplicações e os serviços Azure consumam dados independentes a partir do mesmo ponto final do Event Hub. Nesta secção, adicione um grupo de consumidores ao ponto final integrado do seu hub IoT que a aplicação web utilizará para ler dados.

Executar o seguinte comando para adicionar um grupo de consumidores ao ponto final incorporado do seu hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Reparem no nome que escolherem, precisarão dele mais tarde neste tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obtenha uma cadeia de ligação de serviço para o seu hub IoT

Os hubs IoT são criados com várias políticas de acesso predefinidos. Uma dessas políticas é a política de **serviços,** que fornece permissões suficientes para um serviço ler e escrever os pontos finais do hub IoT. Executar o seguinte comando para obter uma cadeia de ligação para o seu hub IoT que adere à política de serviço:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A cadeia de ligação deve ser semelhante à seguinte:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Note a cadeia de ligação de serviço, você vai precisar mais tarde neste tutorial.

## <a name="download-the-web-app-from-github"></a>Descarregue o aplicativo web do GitHub

Abra uma janela de comando e insira os seguintes comandos para descarregar a amostra do GitHub e mudar para o diretório da amostra:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examine o código de aplicação web

A partir do diretório de visualização de dados web-apps-node-iot-hub-visualização de dados, abra a aplicação web no seu editor favorito. O seguinte mostra a estrutura de ficheiros visualizada no Código VS:

![Estrutura de ficheiros de aplicativos web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Tire um momento para examinar os seguintes ficheiros:

* **Server.js** é um script do lado do serviço que inicializa a tomada web e a classe de invólucro Event Hub. Fornece uma chamada de volta para a classe de invólucro Event Hub que a classe usa para transmitir mensagens de entrada para a tomada web.

* **Event-hub-reader.js** é um script do lado do serviço que se conecta ao ponto final incorporado do hub IoT usando a cadeia de ligação especificada e o grupo de consumidores. Extrai o DeviceId e o EnqueuedTimeUtc a partir de metadados em mensagens recebidas e, em seguida, transmite a mensagem utilizando o método de chamada registado por server.js.

* **Chart-device-data.js** é um script do lado do cliente que ouve na tomada web, mantém o registo de cada DeviceId e armazena os últimos 50 pontos de dados de entrada para cada dispositivo. Em seguida, liga os dados do dispositivo selecionados ao objeto da tabela.

* **Index.html** trata do layout da UI para a página web e faz referência aos scripts necessários para a lógica do lado do cliente.

## <a name="configure-environment-variables-for-the-web-app"></a>Configure variáveis de ambiente para a aplicação web

Para ler dados do seu hub IoT, a aplicação web precisa da cadeia de ligação do seu hub IoT e do nome do grupo de consumidores que deve ler. Obtém estas cordas do ambiente de processo nas seguintes linhas em server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Desaprote as variáveis ambientais na janela de comando com os seguintes comandos. Substitua os valores do espaço reservado pelo fio de ligação de serviço para o seu hub IoT e o nome do grupo de consumidores que criou anteriormente. Não cite as cordas.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. Certifique-se de que o seu dispositivo está a executar e a enviar dados.

2. Na janela de comando, executar as seguintes linhas para descarregar e instalar pacotes referenciados e iniciar o website:

   ```cmd
   npm install
   npm start
   ```

3. Deve ver a saída na consola que indica que a aplicação web está conectada com sucesso ao seu hub IoT e está a ouvir na porta 3000:

   ![Web app começou na consola](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Abra uma página web para ver os dados do seu hub IoT

Abra um browser para `http://localhost:3000` .

Na lista **de dispositivos Selecione uma** lista de dispositivos, selecione o seu dispositivo para ver um enredo de execução dos últimos 50 pontos de dados de temperatura e humidade enviados pelo dispositivo para o seu hub IoT.

![Página de aplicativo web mostrando temperatura e humidade em tempo real](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Também deve ver saída na consola que mostra as mensagens que a sua aplicação web está a transmitir para o cliente do navegador:  

![Transmissão de aplicativo web na consola](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hospedar a aplicação web no Serviço de Aplicações

A [funcionalidade de Aplicações Web do Azure App Service](https://docs.microsoft.com/azure/app-service/overview) fornece uma plataforma como um serviço (PAAS) para hospedar aplicações web. As aplicações web hospedadas no Azure App Service podem beneficiar de funcionalidades poderosas do Azure, como segurança adicional, equilíbrio de carga e escalabilidade, bem como soluções Azure e partner DevOps como implementação contínua, gestão de pacotes, e assim por diante. O Azure App Service suporta aplicações web desenvolvidas em muitos idiomas populares e implantadas em infraestruturas Windows ou Linux.

Nesta secção, você fornece uma aplicação web no Serviço de Aplicações e implementa o seu código para ele usando comandos Azure CLI. Pode encontrar detalhes dos comandos utilizados na documentação do [webapp az.](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) Antes de começar, certifique-se de ter completado os passos para adicionar um grupo de [recursos ao seu hub IoT,](#add-a-consumer-group-to-your-iot-hub)obter uma cadeia de [conexão de serviço para o seu hub IoT](#get-a-service-connection-string-for-your-iot-hub), e [descarregar a aplicação web do GitHub](#download-the-web-app-from-github).

1. Um plano de Serviço de [Aplicações](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) define um conjunto de recursos computacionares para uma aplicação hospedada no Serviço de Aplicações a executar. Neste tutorial, utilizamos o nível Developer/Free para hospedar a aplicação web. Com o nível Gratuito, a sua aplicação web funciona em recursos partilhados do Windows com outras aplicações do App Service, incluindo aplicações de outros clientes. O Azure também oferece planos de Serviço de Aplicações para implementar aplicações web em recursos de computação Linux. Pode saltar este passo se já tiver um plano de Serviço de Aplicações que pretende utilizar.

   Para criar um plano de Serviço de Aplicações utilizando o nível livre do Windows, execute o seguinte comando. Use o mesmo grupo de recursos em que o seu hub IoT está. O nome do seu plano de serviço pode conter letras maiúsculas e minúsculas, números e hífens.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Agora, disposi uma aplicação web no seu plano de Serviço de Aplicações. O `--deployment-local-git` parâmetro permite que o código da aplicação web seja carregado e implantado a partir de um repositório git na sua máquina local. O nome da sua aplicação web deve ser globalmente único e pode conter letras maiúsculas e minúsculas, números e hífenes. Certifique-se de especificar a versão 10.6 do Nó ou posterior para o `--runtime` parâmetro, dependendo da versão do tempo de execução Node.js que estiver a utilizar. Pode usar o `az webapp list-runtimes` comando para obter uma lista de tempos de execução suportados.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Adicione agora configurações de aplicação para as variáveis ambientais que especificam a cadeia de ligação do hub IoT e o grupo de consumidores do centro de eventos. As configurações individuais são delimitadas no `-settings` parâmetro. Utilize o fio de ligação de serviço para o seu hub IoT e o grupo de consumidores que criou anteriormente neste tutorial. Não cite os valores.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Ativar o protocolo de Tomadas Web para a aplicação web e definir a aplicação web apenas para receber pedidos HTTPS (os pedidos HTTPS são redirecionados para HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Para implementar o código para o Serviço de Aplicações, utilizará as [suas credenciais de implementação ao nível do utilizador.](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials) As suas credenciais de implementação ao nível do utilizador são diferentes das suas credenciais Azure e são usadas para implementações locais e FTP de Git para uma aplicação web. Uma vez definidas, são válidas em todas as suas aplicações do Serviço de Aplicações em todas as subscrições da sua conta Azure. Se tiver definido previamente credenciais de implementação ao nível do utilizador, pode usá-las.

   Se ainda não definiu as credenciais de implementação ao nível do utilizador ou se não se lembrar da sua palavra-passe, execute o seguinte comando. O seu nome de utilizador de implementação deve ser único dentro do Azure, e não deve conter o símbolo '@' para os pushes git locais. Quando for solicitado, insira e confirme a sua nova senha. A palavra-passe deve ter pelo menos oito caracteres, com dois dos seguintes três elementos: letras, números e símbolos.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Obtenha que o URL Git use para empurrar o seu código para o Serviço de Aplicações.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adicione um controlo remoto ao seu clone que faz referência ao repositório git para a aplicação web no Serviço de Aplicações. Para \<Git clone URL\> , utilize o URL devolvido no passo anterior. Executar o seguinte comando na sua janela de comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Para implementar o código para o Serviço de Aplicações, insira o seguinte comando na sua janela de comando. Se for solicitado para obter credenciais, insira as credenciais de implementação ao nível do utilizador que criou no passo 5. Certifique-se de que empurra para o ramo principal do serviço de aplicações remoto.

    ```cmd
    git push webapp master:master
    ```

9. O progresso da implantação será atualizado na sua janela de comando. Uma implementação bem sucedida terminará com linhas semelhantes à seguinte saída:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Executa o seguinte comando para consultar o estado da sua aplicação web e certifique-se de que está em execução:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Navegue para `https://<your web app name>.azurewebsites.net` num browser. Uma página web semelhante à que viu quando executou a aplicação web localmente. Assumindo que o seu dispositivo está a executar e a enviar dados, deverá ver um lote de funcionamento das 50 leituras de temperatura e humidade mais recentes enviadas pelo dispositivo.

## <a name="troubleshooting"></a>Resolução de problemas

Se encontrar algum problema com esta amostra, experimente os passos nas seguintes secções. Se ainda tiver problemas, envie-nos feedback na parte inferior deste tópico.

### <a name="client-issues"></a>Problemas com o cliente

* Se um dispositivo não aparecer na lista, ou não estiver a ser desenhado nenhum gráfico, certifique-se de que o código do dispositivo está em funcionamento no seu dispositivo.

* No navegador, abra as ferramentas de desenvolvimento (em muitos navegadores a tecla F12 irá abri-la) e encontre a consola. Procure por quaisquer avisos ou erros impressos lá.

* Pode depurar o script do lado do cliente em /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemas no site local

* Observe a saída na janela onde lançou o nó para a saída da consola.

* Depurar o código do servidor, especificamente server.js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemas do Serviço de Aplicações Azure

* No portal Azure, vá à sua aplicação web. Em **Monitorização** no painel esquerdo, selecione **registos de Serviço de Aplicações**. Rode o **registo de aplicação (Sistema de ficheiros)** para ligar, definir **nível** para erro e, em seguida, selecionar **Guardar**. Em seguida, abra **o fluxo de registo** (em **monitorização).**

* A partir da sua aplicação web no portal Azure, em **Ferramentas de Desenvolvimento**  selecione  **Consola** e valide as versões nó e npm com `node -v` e `npm -v` .

* Se vir um erro ao não encontrar um pacote, pode ter executado os passos fora de ordem. Quando o site é implantado `git push` (com) o serviço de aplicações é executado , que funciona com base na versão atual do nó que `npm install` configura. Se isso for alterado na configuração mais tarde, terá de fazer uma alteração sem sentido no código e voltar a empurrar.

## <a name="next-steps"></a>Passos seguintes

Utilizou com sucesso a sua aplicação web para visualizar dados de sensores em tempo real a partir do seu hub IoT.

Para obter outra forma de visualizar os dados do Azure IoT Hub, consulte [Use Power BI para visualizar dados de sensores em tempo real a partir do seu hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
