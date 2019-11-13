---
title: Visualização de dados em tempo real dos dados do Hub IoT em um aplicativo Web
description: Use um aplicativo Web para visualizar dados de temperatura e umidade que são coletados de um sensor e enviados para o Hub IOT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.openlocfilehash: 073a766662b2ead4b816276fa7fda6dc5e6caca7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954654"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Visualizar dados de sensor em tempo real do Hub IoT do Azure em um aplicativo Web

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que irá aprender

Neste tutorial, você aprenderá a Visualizar dados de sensor em tempo real que seu hub IoT recebe com um aplicativo Web node. js em execução no computador local. Depois de executar o aplicativo Web localmente, você pode, opcionalmente, seguir as etapas para hospedar o aplicativo Web no serviço Azure App. Se você quiser tentar visualizar os dados em seu hub IoT usando Power BI, consulte [usar Power bi para visualizar dados de sensor em tempo real do Hub IOT do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que você faz

* Adicionar um grupo de consumidores ao Hub IoT que o aplicativo Web usará para ler os dados do sensor
* Baixar o código do aplicativo Web do GitHub
* Examinar o código do aplicativo Web
* Configurar variáveis de ambiente para manter os artefatos do Hub IoT necessários para seu aplicativo Web
* Executar o aplicativo Web em seu computador de desenvolvimento
* Abra uma página da Web para ver dados de temperatura e umidade em tempo real do Hub IoT
* Adicional Usar CLI do Azure para hospedar seu aplicativo Web no serviço Azure App

## <a name="what-you-need"></a>Do que precisa

* Conclua o tutorial do [simulador online do Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) ou um dos tutoriais do dispositivo; por exemplo, [Raspberry Pi com node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Eles abrangem os seguintes requisitos:

  * Uma assinatura ativa do Azure
  * Um hub IOT em sua assinatura
  * Um aplicativo cliente que envia mensagens para o Hub IOT

* [Baixar o Git](https://www.git-scm.com/downloads)

* As etapas neste artigo pressupõem um computador de desenvolvimento do Windows; no entanto, você pode facilmente executar essas etapas em um sistema Linux em seu shell preferencial.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Execute o comando a seguir para adicionar a extensão de IoT Microsoft Azure para CLI do Azure à sua instância de Cloud Shell. A extensão de IOT adiciona comandos específicos do serviço de provisionamento de dispositivos IOT, IoT Edge e do Hub IoT a CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao Hub IoT

Os [grupos de consumidores](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) fornecem exibições independentes no fluxo de eventos que permitem que aplicativos e serviços do Azure consumam dados de forma independente do mesmo ponto de extremidade do hub de eventos. Nesta seção, você adiciona um grupo de consumidores ao ponto de extremidade interno do Hub IoT que o aplicativo Web usará para ler dados.

Execute o seguinte comando para adicionar um grupo de consumidores ao ponto de extremidade interno do Hub IoT:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Anote o nome que você escolher, você precisará dele mais tarde neste tutorial.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Obter uma cadeia de conexão de serviço para o Hub IoT

Os hubs IoT são criados com várias políticas de acesso padrão. Uma dessas políticas é a política de **serviço** , que fornece permissões suficientes para que um serviço leia e grave os pontos de extremidade do Hub IOT. Execute o comando a seguir para obter uma cadeia de conexão para o Hub IoT que segue a política de serviço:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A cadeia de conexão deve ser semelhante ao seguinte:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Anote a cadeia de conexão de serviço, você precisará dela mais tarde neste tutorial.

## <a name="download-the-web-app-from-github"></a>Baixar o aplicativo Web do GitHub

Abra uma janela de comando e insira os seguintes comandos para baixar o exemplo do GitHub e altere para o diretório de exemplo:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>Examinar o código do aplicativo Web

No diretório Web-Apps-node-IOT-Hub-data-visualization, abra o aplicativo Web em seu editor favorito. O seguinte mostra a estrutura de arquivo exibida no VS Code:

![Estrutura de arquivos do aplicativo Web](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Reserve um tempo para examinar os seguintes arquivos:

* **Server. js** é um script do lado do serviço que inicializa o soquete da Web e a classe wrapper do hub de eventos. Ele fornece um retorno de chamada para a classe wrapper do hub de eventos que a classe usa para difundir mensagens de entrada para o soquete da Web.

* **Event-Hub-Reader. js** é um script do lado do serviço que se conecta ao ponto de extremidade interno do Hub IOT usando a cadeia de conexão especificada e o grupo de consumidores. Ele extrai o DeviceID e o EnqueuedTimeUtc de metadados em mensagens de entrada e, em seguida, retransmite a mensagem usando o método de retorno de chamada registrado por Server. js.

* **Chart-Device-Data. js** é um script do lado do cliente que escuta no soquete da Web, controla cada DeviceID e armazena os últimos 50 pontos de dados de entrada para cada dispositivo. Em seguida, ele associa os dados do dispositivo selecionado ao objeto de gráfico.

* **Index. html** manipula o layout da interface do usuário para a página da Web e faz referência aos scripts necessários para a lógica do lado do cliente.

## <a name="configure-environment-variables-for-the-web-app"></a>Configurar variáveis de ambiente para o aplicativo Web

Para ler dados de seu hub IoT, o aplicativo Web precisa da cadeia de conexão do seu hub IoT e o nome do grupo de consumidores que ele deve ler. Ele obtém essas cadeias de caracteres do ambiente de processo nas seguintes linhas no Server. js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Defina as variáveis de ambiente na janela de comando com os comandos a seguir. Substitua os valores de espaço reservado pela cadeia de conexão de serviço para o Hub IoT e o nome do grupo de consumidores criado anteriormente. Não citar as cadeias de caracteres.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. Verifique se o dispositivo está executando e enviando dados.

2. Na janela de comando, execute as seguintes linhas para baixar e instalar pacotes referenciados e iniciar o site:

   ```cmd
   npm install
   npm start
   ```

3. Você deve ver a saída no console do que indica que o aplicativo Web foi conectado com êxito ao Hub IoT e está escutando na porta 3000:

   ![Aplicativo Web iniciado no console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Abrir uma página da Web para ver os dados do Hub IoT

Abra um navegador para `http://localhost:3000`.

Na lista **selecionar um dispositivo** , selecione o dispositivo para ver um gráfico em execução dos últimos 50 de dados de temperatura e umidade enviados pelo dispositivo para o Hub IOT.

![Página do aplicativo Web mostrando a temperatura em tempo real e a umidade](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Você também deve ver a saída no console do que mostra as mensagens que seu aplicativo Web está transmitindo para o cliente de navegador:  

![Saída de difusão do aplicativo Web no console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>Hospedar o aplicativo Web no serviço de aplicativo

O [recurso aplicativos Web do serviço de Azure app](https://docs.microsoft.com/azure/app-service/overview) fornece uma PaaS (plataforma como serviço) para hospedar aplicativos Web. Os aplicativos Web hospedados no serviço de Azure App podem se beneficiar de recursos avançados do Azure, como segurança adicional, balanceamento de carga e escalabilidade, bem como soluções de DevOps do Azure e de parceiros, como implantação contínua, gerenciamento de pacotes e assim por diante. Azure App serviço oferece suporte a aplicativos Web desenvolvidos em muitas linguagens populares e implantados na infraestrutura do Windows ou do Linux.

Nesta seção, você provisiona um aplicativo Web no serviço de aplicativo e implanta seu código usando CLI do Azure comandos. Você pode encontrar detalhes dos comandos usados na documentação do [AZ webapp](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest) . Antes de começar, verifique se você concluiu as etapas para [Adicionar um grupo de recursos ao Hub IOT](#add-a-consumer-group-to-your-iot-hub), [obtenha uma cadeia de conexão de serviço para o Hub IOT](#get-a-service-connection-string-for-your-iot-hub)e [Baixe o aplicativo Web do GitHub](#download-the-web-app-from-github).

1. Um [plano do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) define um conjunto de recursos de computação para um aplicativo hospedado no serviço de aplicativo para execução. Neste tutorial, usamos a camada de desenvolvedor/gratuita para hospedar o aplicativo Web. Com a camada gratuita, seu aplicativo Web é executado em recursos compartilhados do Windows com outros aplicativos do serviço de aplicativo, incluindo aplicativos de outros clientes. O Azure também oferece planos do serviço de aplicativo para implantar aplicativos Web em recursos de computação do Linux. Você pode ignorar esta etapa se já tiver um plano do serviço de aplicativo que deseja usar.

   Para criar um plano do serviço de aplicativo usando a camada gratuita do Windows, execute o comando a seguir. Use o mesmo grupo de recursos no qual o Hub IoT está. O nome do plano de serviço pode conter letras maiúsculas e minúsculas, números e hifens.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Agora, provisione um aplicativo Web em seu plano do serviço de aplicativo. O parâmetro `--deployment-local-git` permite que o código do aplicativo Web seja carregado e implantado de um repositório git em seu computador local. O nome do aplicativo Web deve ser globalmente exclusivo e pode conter letras maiúsculas e minúsculas, números e hifens.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --deployment-local-git
   ```

3. Agora, adicione configurações de aplicativo para as variáveis de ambiente que especificam a cadeia de conexão do Hub IoT e o grupo de consumidores do hub de eventos. As configurações individuais são delimitadas por espaço no parâmetro `-settings`. Use a cadeia de conexão de serviço para o Hub IoT e o grupo de consumidores que você criou anteriormente neste tutorial. Não citar os valores.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString=<your IoT hub connection string>
   ```

4. Habilite o protocolo Web Sockets para o aplicativo Web e defina o aplicativo Web para receber somente solicitações HTTPS (as solicitações HTTP são redirecionadas para HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Para implantar o código no serviço de aplicativo, você usará suas [credenciais de implantação no nível do usuário](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials). Suas credenciais de implantação no nível do usuário são diferentes das suas credenciais do Azure e são usadas para implantações locais e FTP do git em um aplicativo Web. Uma vez definido, eles são válidos em todos os seus aplicativos do serviço de aplicativo em todas as assinaturas em sua conta do Azure. Se você tiver definido anteriormente as credenciais de implantação no nível do usuário, poderá usá-las.

   Se você não definiu anteriormente as credenciais de implantação no nível do usuário ou não se lembra da sua senha, execute o comando a seguir. Seu nome de usuário de implantação deve ser exclusivo no Azure e não deve conter o símbolo ' @ ' para Pushes git locais. Quando for solicitado, insira e confirme sua nova senha. A senha deve ter pelo menos oito caracteres de comprimento, com dois dos três elementos a seguir: letras, números e símbolos.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Obtenha a URL do git a ser usada para enviar por push seu código para o serviço de aplicativo.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adicione um remoto ao clone que referencie o repositório Git para o aplicativo Web no serviço de aplicativo. Para \<URL de clone de git\>, use a URL retornada na etapa anterior. Execute o comando a seguir na janela de comando.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Para implantar o código no serviço de aplicativo, insira o comando a seguir na janela de comando. Se você for solicitado a fornecer credenciais, insira as credenciais de implantação no nível do usuário que você criou na etapa 5. Certifique-se de enviar por push para a ramificação mestre do serviço de aplicativo remoto.

    ```cmd
    git push webapp master:master
    ```

9. O progresso da implantação será atualizado na janela de comando. Uma implantação bem-sucedida terminará com linhas semelhantes à seguinte saída:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  master -> master
    ```

10. Execute o comando a seguir para consultar o estado do seu aplicativo Web e verificar se ele está em execução:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Navegue para `https://<your web app name>.azurewebsites.net` num browser. Uma página da Web semelhante à que você viu quando executou o aplicativo Web é exibida localmente. Supondo que o dispositivo esteja executando e enviando dados, você deverá ver um gráfico em execução das leituras mais recentes de temperatura e umidade do 50 enviadas pelo dispositivo.

## <a name="troubleshooting"></a>Resolução de problemas

Se você tiver problemas com este exemplo, tente as etapas nas seções a seguir. Se você ainda tiver problemas, envie-nos comentários na parte inferior deste tópico.

### <a name="client-issues"></a>Problemas do cliente

* Se um dispositivo não aparecer na lista ou se nenhum grafo estiver sendo desenhado, verifique se o código do dispositivo está em execução no dispositivo.

* No navegador, abra as ferramentas de desenvolvedor (em muitos navegadores a tecla F12 irá abri-la) e localize o console. Procure avisos ou erros impressos.

* Você pode depurar o script do lado do cliente no/JS/chat-Device-Data.js.

### <a name="local-website-issues"></a>Problemas do site local

* Assista à saída na janela em que você iniciou o nó para a saída do console.

* Depurar o código do servidor, especificamente Server. js e/scripts/Event-Hub-Reader.js.

### <a name="azure-app-service-issues"></a>Problemas de serviço Azure App

* Em portal do Azure, vá para seu aplicativo Web. Em **monitoramento** no painel esquerdo, selecione **logs do serviço de aplicativo**. Ative o **registro em log do aplicativo (sistema de arquivos)** para ativado, defina **nível** como erro e, em seguida, selecione **salvar**. Em seguida, abra o **fluxo de log** (em **monitoramento**).

* Em seu aplicativo Web no portal do Azure, em **ferramentas de desenvolvimento** , selecione **console** e valide versões de nó e npm com `node -v` e `npm -v`.

* Se você vir um erro sobre a não localização de um pacote, você pode ter executado as etapas fora de ordem. Quando o site é implantado (com `git push`), o serviço de aplicativo é executado `npm install`, que é executado com base na versão atual do nó que ele configurou. Se isso for alterado na configuração mais tarde, você precisará fazer uma alteração sem sentido no código e enviá-lo novamente.

## <a name="next-steps"></a>Passos seguintes

Você usou com êxito seu aplicativo Web para visualizar dados de sensor em tempo real de seu hub IoT.

Para outra maneira de Visualizar dados do Hub IoT do Azure, consulte [usar Power bi para visualizar dados de sensor em tempo real de seu hub IOT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
