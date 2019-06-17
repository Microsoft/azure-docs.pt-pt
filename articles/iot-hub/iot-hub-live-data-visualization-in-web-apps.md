---
title: Visualização de dados em tempo real dos dados de sensor do seu hub IoT do Azure numa aplicação web | Documentos da Microsoft
description: Utilize uma aplicação web para visualizar dados de temperatura e humidade recolhidos a partir de um sensor e enviados ao seu hub Iot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 22b15a95e529d4f09560e9b7e59d9f78f70651bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475992"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizar dados de sensores em tempo real do seu hub IoT do Azure numa aplicação web

![Diagrama de ponto a ponto](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste tutorial, saiba como visualizar dados de sensores em tempo real que recebe o seu hub IoT com uma aplicação web de node. js em execução no seu computador local. Depois de executar a aplicação web localmente, opcionalmente, pode seguir os passos para alojar a aplicação web no App Service do Azure. Se quiser tentar visualizar os dados do seu hub IoT através do Power BI, veja [utilize o Power BI para visualizar dados de sensores em tempo real do IoT Hub do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

* Adicionar um grupo de consumidores ao seu hub IoT que o aplicativo web utilizará para ler os dados de sensor
* Transferir o código da aplicação web a partir do GitHub
* Examinar o código de aplicação web
* Configurar variáveis de ambiente para conter o IoT Hub artefactos necessários para a sua aplicação web
* Executar a aplicação web no computador de desenvolvimento
* Abrir uma página da web para ver dados de temperatura e humidade em tempo real do seu hub IoT
* (Opcional) Utilizar a CLI do Azure para alojar a sua aplicação web no App Service do Azure

## <a name="what-you-need"></a>Do que precisa

* Concluir o [simulador online de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes incluem os seguintes requisitos:

  * Uma subscrição do Azure Active Directory
  * Um hub Iot com a sua subscrição
  * Uma aplicação de cliente que envia mensagens para o seu hub Iot

* [Baixe o Git](https://www.git-scm.com/downloads)

* Os passos neste artigo partem do princípio de uma máquina de desenvolvimento do Windows; No entanto, pode facilmente executar estes passos num sistema Linux na sua shell preferido.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Execute o seguinte comando para adicionar a extensão de IoT do Microsoft Azure para a CLI do Azure à sua instância do Cloud Shell. A extensão de IOT adiciona comandos específicos de IoT Hub, o IoT Edge e o serviço aprovisionamento de dispositivos IoT (DPS) para a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao seu hub IoT

[Grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecer vistas independentes para o fluxo de eventos que permitem a aplicações e serviços do Azure de forma independente consumir dados a partir do mesmo ponto de final do Hub de eventos. Nesta secção, é possível adicionar um grupo de consumidores para o ponto final incorporado do seu hub IoT que a aplicação web utilizará para ler dados a partir de.

Execute o seguinte comando para adicionar um grupo de consumidor para o ponto final incorporado do IoT hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Nota do nome que escolher, precisará dela mais tarde neste tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obter uma cadeia de ligação de serviço para o seu hub IoT

Os hubs IoT são criados com várias políticas de acesso predefinida. Uma destas políticas é o **serviço** política, que fornece permissões suficientes para um serviço ler e escrever os pontos finais do hub IoT. Execute o seguinte comando para obter uma cadeia de ligação do hub IoT que cumpra a política do serviço:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A cadeia de ligação deve ter um aspeto semelhante ao seguinte:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Tome nota da cadeia de ligação de serviço, precisará dela mais tarde neste tutorial.

## <a name="download-the-web-app-from-github"></a>Transferir a aplicação web a partir do GitHub

Abra uma janela de comando e introduza os seguintes comandos para transferir o exemplo a partir do GitHub e altere o diretório de exemplo:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examinar o código de aplicação web

A partir do diretório web-apps-node-iot-hub-data-visualization, abra a aplicação web no seu editor favorito. O código a seguir mostra a estrutura de arquivo visualizada no VS Code:

![Estrutura de ficheiros de aplicação Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Dedique uns momentos para examinar os seguintes ficheiros:

* **Server. js** é um script do lado do serviço que inicializa o socket de web e a classe de wrapper do Hub de eventos. Ele fornece um retorno de chamada para a classe de wrapper do Hub de eventos que a classe usa para mensagens de entrada para o socket de web de difusão.

* **Event-hub-reader.js** é um script do lado do serviço que se liga para ponto final incorporado o hub de IoT a utilizar o grupo de cadeia de caracteres e o consumidor de ligação especificada. Extrai o DeviceId e EnqueuedTimeUtc a partir de metadados em mensagens de entrada e retransmitem a mensagem usando o método de retorno de chamada registrado pelo Server. js.

* **Gráfico-dispositivo-js** é um script do lado do cliente que fica à escuta no web socket, mantém um registro de cada DeviceId e armazena os últimos 50 pontos de dados de entrada para cada dispositivo. Em seguida, ele liga os dados de dispositivo selecionado para o objeto de gráfico.

* **Index** lida com o layout de interface do Usuário para a página da web e faz referência os scripts necessários para a lógica do lado do cliente.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurar variáveis de ambiente para a aplicação web

Para ler dados do seu hub IoT, a aplicação web tem de cadeia de ligação do hub IoT e o nome do grupo de consumidor que ele deve ler. Obtém essas cadeias de caracteres a partir do ambiente do processo nas seguintes linhas em Server. js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Defina as variáveis de ambiente na sua janela de comando com os seguintes comandos. Substitua os valores de marcador de posição pela cadeia de ligação de serviço para o seu hub IoT e o nome do grupo de consumidores que criou anteriormente. Não citar as cadeias de caracteres.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. Certifique-se de que o seu dispositivo está em execução e o envio de dados.

2. Na janela de comando, execute as seguintes linhas ao transferir e instalar pacotes referenciados e iniciar o Web site:

   ```cmd
   npm install
   npm start
   ```

3. Deverá ver um resultado na consola do que indica que a aplicação web foi ligado com êxito ao seu hub IoT e está à escuta na porta 3000:

   ![Aplicação Web iniciada na consola](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Abrir uma página da web para ver os dados do seu hub IoT

Abra um browser para `http://localhost:3000`.

Na **selecione um dispositivo** lista, selecione o seu dispositivo para ver um desenho em execução do último 50 pontos de dados de temperatura e humidade enviados pelo dispositivo ao seu hub IoT.

![Página da aplicação Web que mostra em tempo real temperatura e humidade](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Também deverá ver um resultado na consola que mostra as mensagens que a aplicação web estiver a difundir para o cliente de navegador:  

![Aplicação Web de difusão de saída na consola](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Alojar a aplicação web no serviço de aplicações

O [funcionalidade de aplicações Web do serviço de aplicações do Azure](https://docs.microsoft.com/azure/app-service/overview) fornece uma plataforma como serviço (PAAS) para alojar aplicações web. Aplicações Web alojadas no serviço de aplicações do Azure podem beneficiar de poderosas funcionalidades do Azure, como a segurança adicional, balanceamento de carga, e a escalabilidade de bem, como o Azure e de parceiros DevOps, soluções como a implementação contínua, gestão de pacotes e assim por diante. Serviço de aplicações do Azure suporta aplicações web desenvolvidas em vários idiomas populares e implementado na infraestrutura do Windows ou Linux.

Nesta secção, aprovisionar uma aplicação web no serviço de aplicações e implementar o código para o mesmo ao utilizar comandos da CLI do Azure. Pode encontrar detalhes dos comandos utilizados no [az webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) documentação. Antes de começar, certifique-se de que concluiu os passos para [adicionar um grupo de recursos ao seu hub IoT](#add-a-consumer-group-to-your-iot-hub), [obter uma cadeia de ligação de serviço para o seu hub IoT](#get-a-service-connection-string-for-your-iot-hub), e [transferir a aplicação web a partir do GitHub](#download-the-web-app-from-github).

1. Uma [plano do App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) define um conjunto de recursos de computação para uma aplicação alojada no serviço de aplicações para executar. Neste tutorial, utilizamos o escalão de programador/gratuito para alojar a aplicação web. Com o escalão gratuito, a sua aplicação web é executado em recursos do Windows partilhados com outras aplicações de serviço de aplicações, incluindo aplicações de outros clientes. O Azure também oferece que planos do App Service para implementar aplicações web no Linux recursos de computação. Pode ignorar este passo se já tiver um plano do serviço de aplicações que pretende utilizar.

   Para criar um plano de serviço de aplicações através do escalão gratuito do Windows, execute o seguinte comando. Utilize o mesmo grupo de recursos que é de seu hub IoT no. O nome do plano de serviço pode conter o canto superior e letras minúsculas, números e hífenes.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Agora Aprovisione uma aplicação web no seu plano do serviço de aplicações. O `--deployment-local-git` parâmetro permite que o código da aplicação web ser carregado e implementadas a partir de um repositório de Git no seu computador local. Nome da sua aplicação web tem de ser globalmente exclusivo e pode conter o canto superior e letras minúsculas, números e hífenes.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Agora, adicione as definições da aplicação para as variáveis de ambiente que especificam a cadeia de ligação do hub IoT e o grupo de consumidores do hub de eventos. As definições individuais são delimitados por no espaço do `-settings` parâmetro. Utilize a cadeia de ligação de serviço para o seu hub IoT e o grupo de consumidores que criou anteriormente neste tutorial. Não citar os valores.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Ativar o Web Sockets de protocolo da aplicação web e definir a aplicação web para receber HTTPS apenas pedidos (os pedidos de HTTP são redirecionados para HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Para implementar o código no serviço de aplicações, usará seus [credenciais de implementação de nível de usuário](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). As suas credenciais de implementação de nível de usuário são diferentes das suas credenciais do Azure e são utilizadas para implementações de FTP para uma aplicação web e de Git local. Uma vez definido, eles são válidos em todas as suas aplicações de serviço de aplicações em todas as subscrições na sua conta do Azure. Se anteriormente tiver definido as credenciais de implementação de nível de usuário, pode usá-los.

   Se ainda não tiver definido previamente as credenciais de implementação de nível de usuário ou se lembrar da palavra-passe, execute o seguinte comando. O nome de utilizador de implementação tem de ser exclusivo no Azure, e não tem de conter o ' @' Símbolo para envia por push do local Git. Quando lhe for pedido, introduza e confirme a palavra-passe nova. A palavra-passe tem de ser, pelo menos, oito carateres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Obtenha o URL do Git para utilizar para emitir o código até o serviço de aplicações.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adicione um remoto ao seu clone que referencia o repositório de Git para a aplicação web no serviço de aplicações. Para \<URL de clone de Git\>, utilize o URL devolvido no passo anterior. Execute o seguinte comando na janela de comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Para implementar o código no serviço de aplicações, introduza o seguinte comando na janela de comando. Se lhe forem pedidas as credenciais, introduza as credenciais de implementação ao nível do utilizador que criou no passo 5. Certifique-se de que enviar por push para o ramo principal do serviço de aplicações remoto.

    ```cmd
    git push webapp master:master
    ```

9. O progresso da implementação será atualizado na janela de comando. Uma implantação bem-sucedida terminará com linhas semelhantes à saída seguinte:

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

11. Navegue para `https://<your web app name>.azurewebsites.net` num browser. Uma página da web semelhante àquele que viu quando executou a aplicação web localmente apresenta. Supondo que o dispositivo está em execução e o envio de dados, deverá ver um desenho em execução das 50 mais recentes temperatura e humidade leituras enviados pelo dispositivo.

## <a name="troubleshooting"></a>Resolução de problemas

Se se deparou com quaisquer problemas com este exemplo, tente os passos nas secções seguintes. Se ainda tiver problemas, envie-nos comentários na parte inferior deste tópico.

### <a name="client-issues"></a>Problemas do cliente

* Se um dispositivo não aparece na lista, ou nenhum gráfico está sendo desenhado, certificar-se de que o código de dispositivo está em execução no seu dispositivo.

* No browser, abra as ferramentas de desenvolvimento (em vários navegadores F12 chave será aberto) e localize a consola. Procure avisos ou erros impressos lá.

* Pode depurar o script do lado do cliente no /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemas de Web site local

* Observe a saída na janela de onde foi iniciado nó para o resultado da consola.

* Depure o código de servidor, especificamente Server. js e /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Problemas de serviço de aplicações do Azure

* No portal do Azure, aceda à sua aplicação web. Sob **monitorização** no painel esquerdo, selecione **registos do serviço de aplicações**. Ative **registo de aplicação (sistema de ficheiros)** , definir **nível** erro e, em seguida, selecione **guardar**. Em seguida, abra **fluxo de registo** (sob **monitorização**).

* Da sua aplicação web no portal do Azure, em **ferramentas de desenvolvimento** selecionar **consola** e validar as versões de nó e o npm com `node -v` e `npm -v`.

* Se vir um erro sobre como não localizar um pacote, pode executar os passos fora de ordem. Quando o site for implementado (com `git push`) o serviço de aplicação executa `npm install`, qual é executado com base na versão atual do nó configurou. Se isso mudou na configuração mais tarde, terá de fazer uma alteração sem significado no código e envie novamente.

## <a name="next-steps"></a>Passos Seguintes

Utilizou com êxito a aplicação web para visualizar dados de sensores em tempo real do seu hub IoT.

Para outra forma de visualizar dados do IoT Hub do Azure, consulte [utilize o Power BI para visualizar dados de sensores em tempo real do seu hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
