---
title: 'Tutorial: Conecte uma solução de ponta a ponta'
titleSuffix: Azure Digital Twins
description: Tutorial para construir uma solução de gémeos digitais Azure que é impulsionada por dados do dispositivo.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 30b30697750a0b9068cfcde19ea4bf9c474f9ad9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424585"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Tutorial: Construir uma solução de ponta a ponta

Para configurar uma solução completa de ponta a ponta impulsionada por dados vivos do seu ambiente, pode ligar a sua instância Azure Digital Twins a outros serviços Azure para a gestão de dispositivos e dados.

Neste tutorial, vai...
> [!div class="checklist"]
> * Configurar um exemplo de Gémeos Digitais Azure
> * Conheça o cenário de construção da amostra e instantiu os componentes pré-escritos
> * Utilize uma app [Azure Functions](../azure-functions/functions-overview.md) para encaminhar telemetria simulada de um dispositivo [IoT Hub](../iot-hub/about-iot-hub.md) em propriedades gémeas digitais
> * Propagar alterações através do **gráfico duplo,** através do processamento de notificações digitais gémeas com Funções Azure, pontos finais e rotas

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Configurar a sessão Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Começa com o cenário de construção

O projeto de amostra utilizado neste tutorial representa um cenário de **construção** no mundo real, contendo um piso, uma sala e um dispositivo termóstato. Estes componentes serão representados digitalmente num caso Azure Digital Twins, que será então ligado ao [IoT Hub,](../iot-hub/about-iot-hub.md) [Event Grid](../event-grid/overview.md)e [duas funções Azure](../azure-functions/functions-overview.md) para facilitar o movimento de dados.

Abaixo está um diagrama que representa todo o cenário. 

Primeiro criará a instância Azure Digital Twins **(secção A** no diagrama), depois configurará o fluxo de dados de telemetria para os gémeos digitais **(seta B),** e depois configurará a propagação de dados através do gráfico gémeo **(seta C).**

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Gráfico do cenário de construção completo. Retrata dados que fluem de um dispositivo para o IoT Hub, através de uma função Azure (seta B) para uma instância Azure Digital Twins (secção A), em seguida, através da Grade de Evento para outra função Azure para o processamento (seta C)":::

Para trabalhar através do cenário, irá interagir com componentes da aplicação de amostra pré-escrita que descarregou anteriormente.

Aqui estão os componentes implementados pela aplicação de amostra do cenário de construção *AdtSampleApp:*
* Autenticação do dispositivo 
* [.NET (C#) Exemplos](/dotnet/api/overview/azure/digitaltwins/client) de utilização da SDK (encontrados em *CommandLoop.cs*)
* Interface de consola para chamar a AZure Digital Twins API
* *SampleClientApp* - Uma solução de amostra Azure Digital Twins
* *SampleFunctionsApp* - Uma app Azure Functions que atualiza o seu gráfico Azure Digital Twins como resultado da telemetria dos eventos IoT Hub e Azure Digital Twins

### <a name="instantiate-the-pre-created-twin-graph"></a>Instantiate o gráfico gémeo pré-criado

Em primeiro lugar, utilizará a solução *AdtSampleApp* do projeto de amostra para construir a peça Azure Digital Twins do cenário de ponta a ponta **(secção A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Um excerto do cenário de construção completo da secção A, a instância Azure Digital Twins":::

Na janela do Seu Estúdio Visual onde o projeto _**AdtE2ESample**_ está aberto, executar o projeto com este botão na barra de ferramentas:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="O botão de arranque do Estúdio Visual (projeto SampleClientApp)":::

Uma janela da consola abrir-se-á, procederá à autenticação e esperará por um comando. Nesta consola, executar o próximo comando para instantaneaizar a solução Azure Digital Twins.

> [!IMPORTANT]
> Se já tem gémeos digitais e relações na sua instância Azure Digital Twins, executar este comando irá apagá-los e substituí-los pelos gémeos e relacionamentos para o cenário da amostra.

```cmd/sh
SetupBuildingScenario
```

A saída deste comando é uma série de mensagens de confirmação, uma vez que três [**gémeos digitais**](concepts-twins-graph.md) são criados e conectados no seu exemplo Azure Digital Twins: um piso chamado *piso 1*, uma sala chamada *sala21*, e um sensor de temperatura chamado *termóstato67*. Estes gémeos digitais representam as entidades que existiriam num ambiente real.

Estão ligados através de relações ao gráfico [**gémeo**](concepts-twins-graph.md)seguinte. O gráfico gémeo representa o ambiente como um todo, incluindo a forma como as entidades interagem e se relacionam entre si.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Um gráfico que mostra que o piso1 contém a sala21, e o quarto21 contém termóstato67" border="false":::

Pode verificar os gémeos que foram criados executando o seguinte comando, que consulta a instância de Gémeos Digitais Azure ligados para todos os gémeos digitais que contém:

```cmd/sh
Query
```

>[!TIP]
> Este método simplificado é fornecido no âmbito do projeto _**AdtE2ESample.**_ Fora do contexto deste código de amostra, pode consultar todos os gémeos no seu caso a qualquer momento, utilizando as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos CLI](how-to-use-cli.md).
>
> Aqui está o corpo de consulta completa para obter todos os gémeos digitais no seu caso:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Depois disto, pode parar de dirigir o projeto. Mantenha a solução aberta no Visual Studio, pois continuará a usá-la durante todo o tutorial.

## <a name="set-up-the-sample-function-app"></a>Configurar a aplicação de função da amostra

O próximo passo é criar uma [app Azure Functions](../azure-functions/functions-overview.md) que será usada ao longo deste tutorial para processar dados. A aplicação de função, *SampleFunctionsApp,* contém duas funções:
* *ProcessHubToDTEvents*: processa a entrada de dados do IoT Hub e atualiza as Gémeas Digitais Azure em conformidade
* *ProcessDTRoutedData:* processa dados de gémeos digitais e atualiza as gémeas-mãe em Azure Digital Twins em conformidade

Nesta secção, publicará a aplicação de função pré-escrita e garantirá que a aplicação de função pode aceder a Azure Digital Twins atribuindo-lhe uma identidade de Azure Ative Directory (Azure AD). O preenchimento destes passos permitirá que o resto do tutorial utilize as funções dentro da aplicação de função. 

De volta à janela do Estúdio Visual, onde o projeto _**AdtE2ESample**_ está aberto, a aplicação de função está localizada no ficheiro de projeto _**SampleFunctionsApp.**_ Pode vê-lo no painel *Solution Explorer.*

### <a name="update-dependencies"></a>Atualizar dependências

Antes de publicar a app, é uma boa ideia certificar-se de que as suas dependências estão atualizadas, certificando-se de que tem a versão mais recente de todos os pacotes incluídos.

No painel *Solution Explorer,* expanda _**as dependências do SampleFunctionsApp** >._ *Selecione pacotes e* escolha Gerir *Pacotes NuGet...*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Estúdio Visual: Gerir pacotes NuGet para o projeto SampleFunctionsApp" border="false":::

Isto abrirá o Gestor de Pacotes NuGet. Selecione o separador *Atualizações* e se houver pacotes a serem atualizados, consulte a caixa para *Selecionar todas as embalagens*. Em seguida, *acerte o Update*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Estúdio Visual: Selecionando para atualizar todos os pacotes no Gestor de Pacotes NuGet":::

### <a name="publish-the-app"></a>Publicar a aplicação

De volta à janela do Estúdio Visual onde está aberto o projeto _**AdtE2ESample,**_ localize o projeto _**SampleFunctionsApp**_ no painel *Solution Explorer.*

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="assign-permissions-to-the-function-app"></a>Atribuir permissões à aplicação de função

Para permitir que a aplicação de função aceda a Azure Digital Twins, o próximo passo é configurar uma configuração de uma aplicação, atribuir à app uma identidade AD AD gerida pelo sistema, e dar a esta identidade o papel *de Proprietário de Dados Azure Digital Twins* na instância Azure Digital Twins. Esta função é necessária para qualquer utilizador ou função que pretenda realizar muitas atividades de data plane no caso. Pode ler mais sobre segurança e atribuições de papéis em [*Soluções Concepts: Security for Azure Digital Twins.*](concepts-security.md)

No Azure Cloud Shell, utilize o seguinte comando para definir uma definição de aplicação que a sua aplicação de função utilizará para fazer referência à sua instância Azure Digital Twins. Preencha os espaços reservados com os detalhes dos seus recursos (lembre-se que o url de exemplo de Azure Digital Twins é o seu nome de anfitrião precedido por *https://).*

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

A saída é a lista de definições para a Função Azure, que deve agora conter uma entrada chamada **ADT_SERVICE_URL**.

Utilize o seguinte comando para criar a identidade gerida pelo sistema. Procure o campo **principal na** saída.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Utilize o valor **principal** da saída no seguinte comando, para atribuir a identidade da aplicação de função à função *Azure Digital Twins Data Owner* para a sua instância Azure Digital Twins.

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```

O resultado deste comando é informação sobre a função que criou. A aplicação de função tem agora permissões para aceder à sua instância Azure Digital Twins.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Processo de telemetria simulada a partir de um dispositivo IoT Hub

Um gráfico Azure Digital Twins deve ser conduzido por telemetria a partir de dispositivos reais. 

Neste passo, irá ligar um dispositivo de termóstato simulado registado no [IoT Hub](../iot-hub/about-iot-hub.md) ao gémeo digital que o representa em Azure Digital Twins. À medida que o dispositivo simulado emite telemetria, os dados serão direcionados através da função *ProcessHubToDTEvents* Azure que desencadeia uma atualização correspondente no twin digital. Desta forma, o gémeo digital mantém-se atualizado com os dados do dispositivo real. Em Azure Digital Twins, o processo de direcionar dados de eventos de um lugar para outro é chamado [**de eventos de encaminhamento**](concepts-route-events.md).

Isto acontece nesta parte do cenário de ponta a ponta **(seta B):**

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Um excerto do cenário de construção completo que destaca a seta B, os elementos antes de Azure Digital Twins: o dispositivo, IoT Hub, e primeira função Azure":::

Aqui estão as ações que irá completar para configurar esta ligação do dispositivo:
1. Crie um hub IoT que irá gerir o dispositivo simulado
2. Ligue o hub IoT à função Azure apropriada, criando uma subscrição de evento
3. Registar o dispositivo simulado no hub IoT
4. Executar o dispositivo simulado e gerar telemetria
5. Consulta Azure Digital Twins para ver os resultados ao vivo

### <a name="create-an-iot-hub-instance"></a>Criar uma instância do Hub IoT

A Azure Digital Twins foi concebida para trabalhar ao lado [do IoT Hub,](../iot-hub/about-iot-hub.md)um serviço Azure para gestão de dispositivos e seus dados. Neste passo, você vai criar um hub IoT que irá gerir o dispositivo de amostra neste tutorial.

Em Azure Cloud Shell, use este comando para criar um novo hub IoT:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

A saída deste comando é informação sobre o hub IoT que foi criado.

Guarde o **nome** que deu ao seu centro de IoT. Irá utilizá-lo mais tarde.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Ligue o hub IoT à função Azure

Em seguida, ligue o seu hub IoT à função *ProcessHubToDTEvents* Azure na aplicação de função que publicou anteriormente, para que os dados possam fluir do dispositivo no IoT Hub através da função, que atualiza a Azure Digital Twins.

Para isso, irá criar uma Subscrição de **Eventos** no seu IoT Hub, com a função Azure como ponto final. Isto "subscreve" a função de eventos que acontecem no IoT Hub.

No [portal Azure,](https://portal.azure.com/)navegue até ao seu recém-criado hub IoT, procurando o seu nome na barra de pesquisa de topo. Selecione *Eventos* a partir do menu do hub e selecione *+ Subscrição de eventos.*

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Portal Azure: Subscrição de eventos IoT Hub":::

Isto irá trazer para cima a página *de Subscrição de Eventos* Criar.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Portal Azure: criar subscrição de eventos":::

Preencha os campos da seguinte forma (não são mencionados os campos preenchidos por defeito):
* *DETALHES DA SUBSCRIÇÃO DO*  >  EVENTO **Nome**: Dê um nome à subscrição do seu evento.
* *DETALHES TÓPICOS*  >  **Nome do tópico do sistema**: Dê um nome para usar para o tópico do sistema. 
* *TIPOS DE EVENTOS*  >  **Filtrar para tipos de eventos**: Selecione *a telemetria* do dispositivo a partir das opções do menu.
* *DETALHES DO PONTO FINAL*  >  **Tipo ponto final**: Selecione *Azure Function* a partir das opções do menu.
* *DETALHES DO PONTO FINAL*  >  **Ponto final**: Acerte na *ligação De ponto final* Select. Isto abrirá uma janela *Select Azure Function:* :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Subscrição do evento do portal Azure: selecione a função Azure" border="false":::
    - Preencha a sua **Subscrição**, **Grupo de Recursos**, **Função app** e **Função** (*ProcessHubToDTEvents*). Alguns destes podem preencher automaticamente após a seleção da subscrição.
    - Hit **Confirm Selection**.

De volta à página *de Subscrição de Eventos Create,* hit **Create**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Registar o dispositivo simulado com ioT Hub 

Esta secção cria uma representação do dispositivo no IoT Hub com o *termóstato* de ID67 . O dispositivo simulado irá ligar-se a isto, e é assim que os eventos de telemetria vão do dispositivo para o IoT Hub, onde a função Azure subscrita do passo anterior está a ouvir, pronta para recolher os eventos e continuar o processamento.

Em Azure Cloud Shell, crie um dispositivo no IoT Hub com o seguinte comando:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

A saída é informação sobre o dispositivo que foi criado.

### <a name="configure-and-run-the-simulation"></a>Configurar e executar a simulação

Em seguida, configure o simulador de dispositivos para enviar dados para a instância do Hub IoT.

Comece por obter a *cadeia de ligação do hub IoT* com este comando:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Em seguida, obtenha a *cadeia de ligação de dispositivo* com este comando:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Irá ligar estes valores ao código do simulador do dispositivo no seu projeto local para ligar o simulador a este hub IoT e ao dispositivo do hub IoT.

Numa nova janela do Estúdio Visual, abra (da pasta de solução descarregada) o Simulador de _Dispositivos > **DeviceSimulator.sln**_.

>[!NOTE]
> Deverá agora ter duas janelas do Estúdio Visual, uma com _**DeviceSimulator.sln**_ e outra de anterior com _**a AdtE2ESample.sln**_.

A partir do painel *Solution Explorer* nesta nova janela do Estúdio Visual, selecione _DeviceSimulator/AzureIoTHub.cs_ para abri-lo na janela de edição. Altere os seguintes valores de cadeia de ligação para os valores acima recolhidos:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Guarde o ficheiro.

Agora, para ver os resultados da simulação de dados que configuraste, executar o projeto **DeviceSimulator** com este botão na barra de ferramentas:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="O botão de arranque do Estúdio Visual (projeto DeviceSimulator)":::

Uma janela da consola abrir-se-á e apresentará mensagens de telemetria de temperatura simulada. Estes estão a ser enviados para o IoT Hub, onde são depois recolhidos e processados pela função Azure.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Saída da consola do simulador do dispositivo mostrando telemetria de temperatura a ser enviada":::

Não precisas de fazer mais nada nesta consola, mas deixa-a a funcionar enquanto completas os próximos passos.

### <a name="see-the-results-in-azure-digital-twins"></a>Veja os resultados em Azure Digital Twins

A função *ProcessHubToDTEvents* que publicou anteriormente ouve os dados do IoT Hub e chama uma API de Gémeos Digitais Azure para atualizar a propriedade *Temperatura* no twin *termóstato67.*

Para ver os dados do lado Azure Digital Twins, vá à janela do Seu Estúdio Visual onde o projeto _**AdtE2ESample**_ está aberto e executar o projeto.

Na janela da consola do projeto que abre, executar o seguinte comando para obter as temperaturas relatadas pelo *termóstato digital twin67:*

```cmd
ObserveProperties thermostat67 Temperature
```

Deve ver as temperaturas atualizadas ao vivo *da sua instância Azure Digital Twins* sendo registadas na consola a cada dois segundos.

>[!NOTE]
> Pode levar alguns segundos para que os dados do dispositivo se propaguem até ao gémeo. As primeiras leituras de temperatura podem mostrar como 0 antes de os dados começarem a chegar.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Saída da consola mostrando registo de mensagens de temperatura do termóstato digital twin67":::

Assim que verificares que isto está a funcionar com sucesso, podes parar de executar ambos os projetos. Mantenha as janelas do Estúdio Visual abertas, pois continuará a usá-las no resto do tutorial.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Propagate Azure Digital Twins eventos através do gráfico

Até agora, neste tutorial, já viu como a Azure Digital Twins pode ser atualizada a partir de dados de dispositivos externos. Em seguida, você verá como as mudanças para um gémeo digital podem propagar-se através do gráfico Azure Digital Twins - ou seja, como atualizar gémeos a partir de dados internos de serviço.

Para isso, utilizará a função *ProcessDTRoutedData* Azure para atualizar um *Twin Room* quando o twin *termóstato* ligado for atualizado. Isto acontece nesta parte do cenário de ponta a ponta **(seta C):**

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Um excerto do cenário de construção completo gráfico destacando seta C, os elementos após Azure Digital Twins: a Grade de Evento e segunda função Azure":::

Aqui estão as ações que irá completar para configurar este fluxo de dados:
1. Criar um ponto final de Grade de Eventos em Azure Digital Twins que liga o caso à Grade de Eventos
2. Estabeleça uma rota dentro da Azure Digital Twins para enviar eventos de mudança de propriedade twin para o ponto final
3. Implementar uma aplicação Azure Functions que ouve (através [da Grade de Eventos)](../event-grid/overview.md)no ponto final, e atualiza outros gémeos em conformidade
4. Executar o dispositivo simulado e consultar a Azure Digital Twins para ver os resultados ao vivo

### <a name="set-up-endpoint"></a>Configurar ponto final

[Event Grid](../event-grid/overview.md) é um serviço Azure que o ajuda a encaminhar e entregar eventos vindos da Azure Services para outros lugares dentro do Azure. Você pode criar um [tópico de grelha de eventos](../event-grid/concepts.md) para recolher certos eventos de uma fonte, e então os subscritores podem ouvir sobre o tópico para receber os eventos à medida que eles passam.

Nesta secção, cria-se um tópico de grelha de eventos e, em seguida, cria-se um ponto final dentro da Azure Digital Twins que aponta (envia eventos) para esse tema. 

Em Azure Cloud Shell, executar o seguinte comando para criar um tópico de grelha de evento:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Para obter uma lista de nomes da região de Azure que podem ser passados em comandos no CLI Azure, executar este comando:
> ```azurecli-interactive
> az account list-locations -o table
> ```

A saída deste comando é informação sobre o tópico da grelha de eventos que criou.

Em seguida, crie um ponto final de Grade de Eventos em Azure Digital Twins, que irá ligar o seu caso ao tópico da grelha de eventos. Utilize o comando abaixo, preenchendo os campos reservados, se necessário:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

A saída deste comando é informação sobre o ponto final que criaste.

Também pode verificar se a criação de ponto final foi bem sucedida executando o seguinte comando para consultar a sua instância Azure Digital Twins para este ponto final:

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Procure o `provisioningState` campo na saída, e verifique se o valor é "Bem sucedido". Pode também dizer "Provisioning", o que significa que o ponto final ainda está a ser criado. Neste caso, aguarde alguns segundos e volte a executar o comando para verificar se foi concluído com sucesso.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Resultado da consulta de ponto final, mostrando o ponto final com um Estado de provisionamento de Sucesso":::

Guarde os nomes que deu ao **tema da grelha de eventos** e ao ponto **final** da Grelha de Eventos em Azure Digital Twins. Vais usá-los mais tarde.

### <a name="set-up-route"></a>Configurar rota

Em seguida, crie uma rota Azure Digital Twins que envia eventos para o ponto final da Grade de Eventos que acabaste de criar.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

A saída deste comando é alguma informação sobre a rota que criou.

>[!NOTE]
>Os pontos finais (a partir do passo anterior) devem ser concluídos antes de poder configurar uma rota de eventos que os utilize. Se a criação da rota falhar porque os pontos finais não estão prontos, aguarde alguns minutos e tente novamente.

#### <a name="connect-the-function-to-event-grid"></a>Ligue a função à Grelha de Eventos

Em seguida, subscreva a função *ProcessDTRoutedData* Azure para o tópico da grelha de eventos que criou anteriormente, para que os dados de telemetria possam fluir do *termóstato67* twin através do tópico da grelha de evento para a função, que remonta ao Azure Digital Twins e atualiza a *sala21* twin em conformidade.

Para isso, irá criar uma **subscrição de 'Grade de Eventos'** que envia dados do tópico da grelha de **eventos** que criou anteriormente para a sua função *ProcessDTRoutedData* Azure.

No [portal Azure,](https://portal.azure.com/)navegue para o tópico da grelha de eventos procurando o seu nome na barra de pesquisa de topo. Selecione *+ Subscrição de Eventos*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Portal Azure: Subscrição de eventos da Grade de Eventos":::

Os passos para criar esta subscrição do evento são semelhantes ao de quando subscreveu a primeira função Azure ao IoT Hub no início deste tutorial. Desta vez, não precisa de especificar a *Telemetria do Dispositivo* como o tipo de evento para ouvir, e irá ligar-se a uma função Azure diferente.

Na página *'Criar Subscrição de* Eventos', preencha os campos da seguinte forma (os campos preenchidos por predefinição não são mencionados):
* *DETALHES DA SUBSCRIÇÃO DO*  >  EVENTO **Nome**: Dê um nome à subscrição do seu evento.
* *DETALHES DO PONTO FINAL*  >  **Tipo ponto final**: Selecione *Azure Function* a partir das opções do menu.
* *DETALHES DO PONTO FINAL*  >  **Ponto final**: Acerte na *ligação De ponto final* Select. Isto abrirá uma janela *de função Select Azure:*
    - Preencha a sua **Subscrição**, **Grupo de Recursos**, **Função app** e **Função** *(ProcessDTRoutedData).* Alguns destes podem preencher automaticamente após a seleção da subscrição.
    - Hit **Confirm Selection**.

De volta à página *de Subscrição de Eventos Create,* hit **Create**.

### <a name="run-the-simulation-and-see-the-results"></a>Executar a simulação e ver os resultados

Agora pode executar o simulador do dispositivo para iniciar o novo fluxo de eventos que montou. Vá à janela do Estúdio Visual onde o projeto _**DeviceSimulator**_ está aberto e execute o projeto.

Como quando executou o simulador do dispositivo mais cedo, uma janela da consola abrir-se-á e apresentará mensagens de telemetria de temperatura simulada. Estes eventos estão a passar pelo fluxo que configuraste mais cedo para atualizar o twin *termóstato67,* e depois passar pelo fluxo que definiu recentemente para atualizar o *twin da sala21* para combinar.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Saída da consola do simulador do dispositivo mostrando telemetria de temperatura a ser enviada":::

Não precisas de fazer mais nada nesta consola, mas deixa-a a funcionar enquanto completas os próximos passos.

Para ver os dados do lado Azure Digital Twins, vá à janela do Seu Estúdio Visual onde o projeto _**AdtE2ESample**_ está aberto e execute o projeto.

Na janela da consola do projeto que abre, corram o seguinte comando para que as temperaturas sejam reportadas **tanto** pelo *termóstato* digital twin 67 como pela sala digital twin *twin21*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Deve ver as temperaturas atualizadas ao vivo *da sua instância Azure Digital Twins* sendo registadas na consola a cada dois segundos. Note que a temperatura do *quarto21* está a ser atualizada para corresponder às atualizações *ao termostato67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Saída da consola mostrando registo de mensagens de temperatura, a partir de um termóstato e de uma sala":::

Assim que verificares que isto está a funcionar com sucesso, podes parar de executar ambos os projetos. Também pode fechar as janelas do Estúdio Visual, uma vez que o tutorial está agora completo.

## <a name="review"></a>Revisão

Aqui está uma revisão do cenário que você construiu neste tutorial.

1. Um exemplo de Azure Digital Twins representa digitalmente um piso, uma sala e um termóstato (representado pela **secção A** no diagrama abaixo)
2. A telemetria do dispositivo simulado é enviada para o IoT Hub, onde a função *ProcessHubToDTEvents* Azure está a ouvir eventos de telemetria. A função *ProcessHubToDTEvents* Azure utiliza as informações nestes eventos para definir a propriedade *Temperatura* no *termostato67* **(seta B** no diagrama).
3. Os eventos de mudança de propriedade em Azure Digital Twins são encaminhados para um tópico de grelha de eventos, onde a função *ProcessDTRoutedData* Azure está a ouvir eventos. A função *ProcessDTRoutedData* Azure utiliza as informações nestes eventos para definir a propriedade *Temperatura* na *sala21* **(seta C** no diagrama).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Gráfico do cenário de construção completo. Retrata dados que fluem de um dispositivo para o IoT Hub, através de uma função Azure (seta B) para uma instância Azure Digital Twins (secção A), em seguida, através da Grade de Evento para outra função Azure para o processamento (seta C)":::

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de completar este tutorial, pode escolher quais os recursos que gostaria de remover, dependendo do que gostaria de fazer a seguir.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Se quiser continuar a usar a instância Azure Digital Twins que configura neste artigo, mas desmarcar alguns ou todos os seus modelos, gémeos e relacionamentos,** pode utilizar os comandos [Az dt](/cli/azure/ext/azure-iot/dt) CLI numa janela [Azure Cloud Shell](https://shell.azure.com) para eliminar os elementos que gostaria de remover.

    Esta opção não removerá nenhum dos outros recursos Azure criados neste tutorial (IoT Hub, app Azure Functions, etc.). Pode eliminá-los individualmente utilizando os [comandos dt apropriados](/cli/azure/reference-index) para cada tipo de recurso.

Também pode querer eliminar a pasta do projeto da sua máquina local.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um cenário de ponta a ponta que mostra as Gémeas Digitais Azure a serem impulsionadas por dados de dispositivos ao vivo.

Em seguida, comece a olhar para a documentação do conceito para saber mais sobre os elementos com que trabalhou no tutorial:

> [!div class="nextstepaction"]
> [*Conceitos: Modelos personalizados*](concepts-models.md)