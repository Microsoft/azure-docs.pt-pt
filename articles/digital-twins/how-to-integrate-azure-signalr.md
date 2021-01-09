---
title: Integrar com o Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Veja como transmitir telemetria Azure Digital Twins a clientes que usam Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d84acc5501b3d40f6db85d0ee6ee369aec5a6aa4
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051110"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integre as Gémeas Digitais Azure com o Serviço Azure SignalR

Neste artigo, você vai aprender a integrar Azure Digital Twins com [O Serviço Azure SignalR](../azure-signalr/signalr-overview.md).

A solução descrita neste artigo permitirá que você pressione dados de telemetria digital twin para clientes conectados, como uma única página web ou uma aplicação móvel. Como resultado, os clientes são atualizados com métricas e estado em tempo real a partir de dispositivos IoT, sem necessidade de pesquisar o servidor ou submeter novos pedidos HTTP para atualizações.

## <a name="prerequisites"></a>Pré-requisitos

Aqui estão os pré-requisitos que deve completar antes de prosseguir:

* Antes de integrar a sua solução com o serviço Azure SignalR neste artigo, deverá completar o Tutorial de Gémeos Digitais Azure: [_**Conecte uma solução de ponta a ponta,**_](tutorial-end-to-end.md)porque esta forma de construir em cima dela. O tutorial acompanha-o através da criação de um exemplo de Azure Digital Twins que funciona com um dispositivo IoT virtual para desencadear atualizações digitais de gémeos. Este como ligar essas atualizações a uma aplicação web de amostras utilizando o Serviço Azure SignalR.
    - Você precisará do nome do tópico da grelha de **evento** que criou no tutorial.
* Instale [**Node.js**](https://nodejs.org/) na sua máquina.

Também pode entrar no portal [Azure](https://portal.azure.com/) com a sua conta Azure.

## <a name="solution-architecture"></a>Arquitetura de soluções

Irá anexar o Serviço Azure SignalR às Gémeas Digitais Azure através do caminho abaixo. As secções A, B e C do diagrama são retiradas do diagrama de arquitetura do [pré-requisito tutorial de ponta a ponta;](tutorial-end-to-end.md) neste como-fazer, você vai construir sobre isso adicionando a secção D.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="Uma visão dos serviços da Azure num cenário de ponta a ponta. Retrata dados que fluem de um dispositivo para o IoT Hub, através de uma função Azure (seta B) para uma instância Azure Digital Twins (secção A), depois através da Grade de Evento para outra função Azure para processamento (seta C). A secção D mostra dados que fluem da mesma grelha de eventos na seta C para uma Função Azure rotulada de &quot;transmissão&quot;. A 'difusão' comunica com outra função Azure rotulada de &quot;negociar&quot;, e tanto &quot;difusão&quot; como &quot;negociar&quot; comunicam com dispositivos informáticos." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Descarregue as aplicações da amostra

Primeiro, descarregue as aplicações de amostras necessárias. Vai precisar de ambos os seguintes:
* [**Amostras de ponta a ponta da Azure Digital Twins**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): Esta amostra contém um *AdtSampleApp* que contém duas funções Azure para mover dados em torno de uma instância Azure Digital Twins (pode aprender sobre este cenário com mais detalhes no [*Tutorial: Conecte uma solução de ponta a ponta).*](tutorial-end-to-end.md) Também contém uma aplicação de amostra *de DeviceSimulator* que simula um dispositivo IoT, gerando um novo valor de temperatura a cada segundo. 
    - Navegue no link de amostra e apere no botão *Download ZIP* para transferir uma cópia da amostra para a sua máquina, como _**Azure_Digital_Twins_end_to_end_samples.zip**_. Deszipe a pasta.
* Amostra de [**aplicações web de integração SignalR**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/): Esta é uma amostra React web app que irá consumir dados de telemetria Azure Digital Twins a partir de um serviço Azure SignalR.
    -  Navegue no link de amostra e apere no botão *Download ZIP* para transferir uma cópia da amostra para a sua máquina, como _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Deszipe a pasta.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Deixe a janela do navegador aberta para o portal Azure, uma vez que irá usá-la novamente na secção seguinte.

## <a name="configure-and-run-the-azure-functions-app"></a>Configure e executar a app Azure Functions

Nesta secção, irá configurar duas funções Azure:
* **negociar** - Uma função de gatilho HTTP. Utiliza a ligação de entrada *SignalRConnectionInfo* para gerar e devolver informações de ligação válidas.
* **transmissão** - Uma função de gatilho [de Grade de Evento.](../event-grid/overview.md) Recebe dados de telemetria Azure Digital Twins através da grelha de eventos, e utiliza a ligação de saída da instância *SignalR* que criou no passo anterior para transmitir a mensagem a todas as aplicações de clientes conectadas.

Primeiro, vá ao navegador onde o portal Azure é aberto e complete os seguintes passos para obter a cadeia de **ligação** para a instância SignalR que você configura. Vai precisar dele para configurar as funções.
1. Confirme que a instância do Serviço SignalR que implementou anteriormente foi criada com sucesso. Pode fazê-lo procurando o seu nome na caixa de pesquisa no topo do portal. Selecione a instância para abri-la.

1. Selecione **Chaves** do menu de instância para visualizar as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione o ícone para copiar a cadeia de ligação primária.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Screenshot do portal Azure que mostra a página Chaves para a instância SignalR. É realçado o ícone 'Copy to clipboard' ao lado da STRING DE LIGAÇÃO Primária." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Em seguida, inicie o Visual Studio (ou outro editor de código à sua escolha) e abra a solução de código na pasta *Azure_Digital_Twins_end_to_end_samples > ADTSampleApp.* Em seguida, faça os seguintes passos para criar as funções:

1. Crie uma nova classe C# afiada chamada **SignalRFunctions.cs** no projeto *SampleFunctionsApp.*

1. Substitua o conteúdo do ficheiro de classe pelo seguinte código:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. Na janela da consola do Gestor de *Pacotes* do Estúdio Visual, ou em qualquer janela de comando da sua máquina na pasta *Azure_Digital_Twins_end_to_end_samples\AdtSampleApp\SampleFunctionsApp,* execute o seguinte comando para instalar o `SignalRService` pacote NuGet no projeto:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Isto deve resolver quaisquer problemas de dependência na classe.

Em seguida, publique a sua função no Azure, utilizando os passos descritos na secção publicar a secção de [ *aplicações*](tutorial-end-to-end.md#publish-the-app) do *"Connect" um tutorial de solução de ponta a ponta.* Pode publicá-lo na mesma aplicação de serviço/função que utilizou no pré-req tutorial de ponta a ponta, ou criar uma nova , mas pode querer usar a mesma para minimizar a duplicação. Além disso, termine a publicação da aplicação com os seguintes passos:
1. Recolha o URL de **ponto final HTTP** da função *de negociação.* Para isso, aceda à página de [aplicações do](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) portal Azure e selecione a sua aplicação de função da lista. No menu de aplicações, selecione *Funções* e escolha a função *de negociação.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Vista do portal Azure da aplicação de função, com 'Funções' em destaque no menu. A lista de funções é mostrada na página, e a função 'negociar' também é destacada.":::

    Hit *Get function URL* e copiar o valor através de **_/api_ (não inclua o último _/negociar?_)**. Vais usar isto mais tarde.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Vista do portal Azure da função &quot;negociar&quot;. O botão 'Obter URL de função' é realçado, e a parte do URL desde o início até '/api'":::

1. Por fim, adicione a sua cadeia de **ligação** Azure SignalR de anteriormente às definições de aplicação da função, utilizando o seguinte comando Azure CLI. O comando pode ser executado em [Azure Cloud Shell,](https://shell.azure.com)ou localmente se tiver o Azure CLI [instalado na sua máquina:](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    A saída deste comando imprime todas as definições de aplicações configurada para a sua função Azure. Procure `AzureSignalRConnectionString` o fundo da lista para verificar se foi adicionado.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Excerto de saída numa janela de comando, mostrando um item de lista chamado 'AzureSignalRConnectionString'":::

#### <a name="connect-the-function-to-event-grid"></a>Ligue a função à Grelha de Eventos

Em seguida, subscreva a função Azure *de transmissão* ao tópico da grelha de **eventos** que criou durante o [*Tutorial: Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md) pré-rereq. Isto permitirá que os dados de telemetria possam fluir do *termóstato67* twin através do tópico da grelha de evento para a função, que pode ser transmitida para todos os clientes.

Para isso, irá criar uma **subscrição** de Grade de Eventos do tópico da grelha de eventos para a sua função de Azure *de difusão* como ponto final.

No [portal Azure,](https://portal.azure.com/)navegue para o tópico da grelha de eventos procurando o seu nome na barra de pesquisa de topo. Selecione *+ Subscrição de Eventos*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Portal Azure: Subscrição de eventos da Grade de Eventos":::

Na página *'Criar Subscrição de* Eventos', preencha os campos da seguinte forma (os campos preenchidos por predefinição não são mencionados):
* *DETALHES DA SUBSCRIÇÃO DO*  >  EVENTO **Nome**: Dê um nome à subscrição do seu evento.
* *DETALHES DO PONTO FINAL*  >  **Tipo ponto final**: Selecione *Azure Function* a partir das opções do menu.
* *DETALHES DO PONTO FINAL*  >  **Ponto final**: Acerte na *ligação De ponto final* Select. Isto abrirá uma janela *de função Select Azure:*
    - Preencha a sua **Subscrição**, **Grupo de Recursos,** **Função app** e **Função** *(transmissão).* Alguns destes podem preencher automaticamente após a seleção da subscrição.
    - Hit **Confirm Selection**.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Vista do portal Azure de criar uma subscrição de eventos. Os campos acima são preenchidos e os botões 'Confirmar Seleção' e 'Criar' são realçados.":::

De volta à página *de Subscrição de Eventos Create,* hit **Create**.

## <a name="configure-and-run-the-web-app"></a>Configure e execute a aplicação web

Nesta secção, verá o resultado em ação. Em primeiro lugar, iniciará a **aplicação de amostra de dispositivo simulado** que envia dados de telemetria através da sua instância Azure Digital Twins. Em seguida, irá configurar a **aplicação web** do cliente da amostra para se conectar ao fluxo Azure SignalR que criou. Depois disso, deverá ser possível ver os dados que atualizam a aplicação web da amostra em tempo real.

### <a name="run-the-device-simulator"></a>Executar o simulador de dispositivo

Durante o pré-requisito tutorial de ponta a ponta, [configuraste o simulador do dispositivo](tutorial-end-to-end.md#configure-and-run-the-simulation) para enviar dados através de um Hub IoT e para a tua instância Azure Digital Twins.

Agora, tudo o que tem de fazer é iniciar o projeto de simulador, localizado no *Azure_Digital_Twins_end_to_end_samples > DeviceSimulator > DeviceSimulator.sln*. Se estiver a utilizar o Visual Studio, pode abrir o projeto e executá-lo com este botão na barra de ferramentas:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="O botão de arranque do Estúdio Visual (projeto DeviceSimulator)":::

Uma janela da consola abrir-se-á e apresentará mensagens de telemetria de temperatura simulada. Estes estão a ser enviados através da sua instância Azure Digital Twins, onde são depois captadas pelas funções Azure e SignalR.

Não precisas de fazer mais nada nesta consola, mas deixa-a a funcionar enquanto completas os próximos passos.

### <a name="configure-the-sample-client-web-app"></a>Configure a aplicação web do cliente da amostra

Em seguida, crie a amostra de **aplicação web de integração signalR** com estes passos:
1. Utilizando o Visual Studio ou qualquer editor de código à sua escolha, abra a pasta _**de Azure_Digital_Twins_SignalR_integration_web_app_sample**_ desapertado que descarregou na secção [*Descodiá-la.*](#download-the-sample-applications)

1. Abra o ficheiro *src/App.js* e substitua o URL `HubConnectionBuilder` pelo URL do ponto final HTTP da função de **negociação** que guardou anteriormente:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. No pedido de *comando do Programador* visual ou em qualquer janela de comando da sua máquina, navegue para a pasta *Azure_Digital_Twins_SignalR_integration_web_app_sample\src.* Executar o seguinte comando para instalar as embalagens de nó dependentes:

    ```cmd
    npm install
    ```

Em seguida, desaprote permissões na sua aplicação de função no portal Azure:
1. Na página de [aplicações 'Fun'](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) do portal Azure, selecione a sua instância de aplicação de função.
1. Desloque-se para baixo no menu de exemplos e selecione *CORS*. Na página CORS, adicione `http://localhost:3000` como uma origem permitida inserindo-a na caixa vazia. Verifique se a caixa é *ativada para ativar o controlo de acesso-permitir-credenciais* e bata *em Save*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Regulação do CORS na Função Azure":::

### <a name="see-the-results"></a>Veja os resultados

Para ver os resultados em ação, inicie a amostra de **aplicações web de integração signalR.** Pode fazê-lo a partir de qualquer janela da consola na localização *Azure_Digital_Twins_SignalR_integration_web_app_sample\src,* executando este comando:

```cmd
npm start
```

Isto abrirá uma janela do navegador que executa a aplicação da amostra, que exibe um medidor de temperatura visual. Uma vez que a aplicação esteja em execução, deve começar a ver os valores de telemetria de temperatura do simulador do dispositivo que se propagam através do Azure Digital Twins sendo refletidos pela aplicação web em tempo real.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Excerto da aplicação web do cliente da amostra, mostrando um medidor de temperatura visual. A temperatura refletida é de 67,52":::

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não necessitar dos recursos criados neste artigo, siga estes passos para os eliminar. 

Utilizando o Azure Cloud Shell ou o Azure CLI local, pode eliminar todos os recursos Azure num grupo de recursos com o comando de eliminação do [grupo Az.](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) A remoção do grupo de recursos também removerá...
* o exemplo das Gémeas Digitais Azure (do tutorial de ponta a ponta)
* o hub IoT e o registo do dispositivo do hub (do tutorial de ponta a ponta)
* o tópico da grelha de eventos e subscrições associadas
* a app Azure Functions, incluindo as três funções e recursos associados como o armazenamento
* a instância Azure SignalR

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Por fim, elimine as pastas de amostra de projeto que descarregou para a sua máquina local *(Azure_Digital_Twins_end_to_end_samples.zip* e *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip).*

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você configura funções Azure com a SignalR para transmitir eventos de telemetria Azure Digital Twins para uma aplicação de cliente de amostra.

Em seguida, saiba mais sobre o Serviço Azure SignalR:
* [*O que é o Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Ou leia mais sobre autenticação do serviço Azure SignalR com Funções Azure:
* [*Autenticação do Serviço Azure SignalR*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)