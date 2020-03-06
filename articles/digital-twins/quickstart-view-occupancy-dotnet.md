---
title: 'Quickstart: Encontre quartos disponíveis - Azure Digital Twins [ Azure Digital Twins ] Microsoft Docs'
description: Neste início rápido, vai executar dois exemplos de aplicações .NET Core para enviar telemetria simulada de movimento e dióxido de carbono para um espaço no Azure Digital Twins. O objetivo é encontrar salas disponíveis com ar fresco a partir das APIs de Gestão após o processamento calculado na cloud.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: 6c9c5df27f4a361e534bac2fe21b2c470f8d0186
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382216"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Início rápido: Salas disponíveis ao utilizar o gémeos Digital do Azure

O serviço de duplos Digital do Azure permite-lhe voltar a criar uma imagem digital do seu ambiente físico. Depois, pode ser notificado por eventos no ambiente e personalizar as respostas para esses eventos.

Este quickstart usa [um par de amostras .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) para digitalizar um edifício de escritórios imaginário. Ele mostra como de salas disponíveis desse edifício. Digital duplos, pode associar vários sensores com o seu ambiente. Também pode descobrir se a qualidade de ar do seu espaço disponível é o ideal com a ajuda de um sensor simulado para emissões de dióxido de carbono. Uma das aplicações de exemplo gera dados de sensor aleatório para o ajudar a visualizar este cenário.

O seguinte vídeo resume a configuração quickstart:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma conta do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. As aplicações de dois consola executadas neste início rápido são escritas utilizando C#. Instale a [versão .NET Core SDK 2.1.403 ou superior](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento. Se tiver o .NET Core SDK instalado, certifique-se a versão atual do C# no computador de desenvolvimento. Corra `dotnet --version` num pedido de comando.

1. Descarregue o [projeto da amostra. C# ](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) Extraia o arquivo de digital-twins-exemplos-csharp-Master.

## <a name="create-a-digital-twins-instance"></a>Criar uma instância do Digital Twins

Crie uma nova instância de Gémeos Digitais no [portal](https://portal.azure.com) seguindo os passos nesta secção.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Definir permissões para a aplicação

Esta secção registra seu aplicativo de exemplo para o Azure Active Directory (Azure AD) para que ele pode aceder à sua instância de duplos Digital. Se já tiver um registo de aplicação do Azure AD, reutilizá-lo para o seu exemplo. Certifique-se de que está configurado conforme descrito nesta secção.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Compilar a aplicação

Crie a aplicação de ocupação seguindo estes passos.

1. Abra uma linha de comandos. Vá para a pasta onde os seus ficheiros `digital-twins-samples-csharp-master.zip` foram extraídos.
1. Execute `cd occupancy-quickstart/src`.
1. Execute `dotnet restore`.
1. Edite [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) para atualizar as variáveis seguintes:
    - **ClientId**: Introduza o ID de aplicação do registo da sua aplicação Azure AD, anotado na secção anterior.
    - **Inquilino**: Insira a identificação do Diretório do seu inquilino Azure AD, também anotado na secção anterior.
    - **BaseUrl**: O URL aPI de gestão da sua instância De Gémeos Digitais está no formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Substitua os marcadores de posição este URL com os valores para a sua instância da secção anterior.

    Guarde o ficheiro atualizado.

## <a name="provision-graph"></a>Aprovisionar o gráfico

Este passo Aprovisiona o gráfico de geográfico duplos Digital com:

- Vários espaços.
- Um dispositivo.
- Dois sensores.
- Uma função personalizada.
- Uma atribuição de função.

O gráfico espacial é provisionado utilizando o ficheiro [provisionSample.yaml.](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)

1. Execute `dotnet run ProvisionSample`.

    >[!NOTE]
    >A ferramenta de CLI do dispositivo início de sessão do Azure é utilizada para autenticar o utilizador para o Azure AD. O utilizador deve introduzir um determinado código para autenticar utilizando a página de [login da Microsoft.](https://microsoft.com/devicelogin) Depois de introduzido o código, siga os passos para autenticar. O utilizador tem de ser autenticado quando é executada a ferramenta.

    >[!TIP]
    > Ao executar este passo, certifique-se de que as suas variáveis foram devidamente copiadas se aparecer a seguinte mensagem de erro: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. O passo de aprovisionamento pode demorar alguns minutos. Ele também fornece um IoT Hub na sua instância de duplos Digital. Passa até que o Hub IoT mostre status=`Running`.

    [![Provisão da amostra - Status=Running](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. No final da execução, copie a `ConnectionString` do dispositivo para utilização na amostra de simulador do dispositivo. Copie apenas a cadeia de caracteres descrita nesta imagem.

    [![Copiar a cadeia de ligação](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > Pode ver e modificar o seu gráfico espacial utilizando o [Telespectador digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

Mantenha a janela da consola aberta para ser utilizada novamente mais tarde.

## <a name="send-sensor-data"></a>Enviar dados do sensor

Construa e execute a aplicação do dispositivo simulador de sensores seguindo estes passos.

1. Abra uma nova linha de comandos. Vá ao projeto que descarregou na pasta `digital-twins-samples-csharp-master`.
1. Execute `cd device-connectivity`.
1. Execute `dotnet restore`.
1. Editar [as definições de aplicações.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) para atualizar **o DeviceConnectionString** com a `ConnectionString`anterior . Guarde o ficheiro atualizado.
1. Executar `dotnet run` para começar a enviar dados do sensor. Será enviado para a Azure Digital Twins, como mostra a imagem seguinte.

     [Conectividade do dispositivo ![](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Permitir que este simulador em execução para que possa visualizar os resultados lado a lado com a ação de passo seguinte. Esta janela mostra os dados de sensor simulado enviados para o Digital duplos. As consultas de passo seguintes em tempo real de salas com ar fresco disponíveis.

    >[!TIP]
    > Quando executar este passo, certifique-se de que `DeviceConnectionString` foi copiado corretamente se aparecer a seguinte mensagem de erro: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Encontrar espaços disponíveis com ar fresco

O exemplo de sensor simula os valores de dados aleatórios para dois sensores. Eles são motion e emissões de dióxido de carbono. Disponíveis espaços com ar fresco são definidos no exemplo sem presença na sala. Também estão definidos por um nível de dióxido de carbono em 1000 ppm. Se a condição não é atendida, o espaço não está disponível ou a qualidade de ar é fraca.

1. Abra o pedido de comando que usou para executar o passo de provisionamento mais cedo.
1. Execute `dotnet run GetAvailableAndFreshSpaces`.
1. Examinar esse prompt de comando e de linha de comandos de dados do sensor lado a lado.

    O pedido de comando de dados do sensor envia dados simulados de movimento e dióxido de carbono para gémeos digitais a cada cinco segundos. O outro pedido de comando lê o gráfico em tempo real para descobrir quartos disponíveis com ar fresco com base em dados simulados aleatórios. Apresenta uma dessas condições em tempo real com base nos dados de sensor que foi enviados pela última vez:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Obtenha espaços disponíveis com ar fresco](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Para entender o que aconteceu neste arranque rápido e o que as APIs foram chamadas, abra o [Código do Estúdio Visual](https://code.visualstudio.com/Download) com o projeto de espaço de trabalho de código encontrado em `digital-twins-samples-csharp`. Utilize o seguinte comando:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Os tutoriais fornecem profundamente o código. Que ensinam como modificar os dados de configuração e o que são chamadas de APIs. Para obter mais informações sobre APIs de gestão, aceda à sua página de Swagger de duplos Digital:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | O nome da sua instância de duplos Digital |
| YOUR_LOCATION | A região do servidor onde a sua instância é hospedada |

Ou por conveniência, navegue até [às Gémeas Digitais Swagger.](https://docs.westcentralus.azuresmartspaces.net/management/swagger)

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais entrar em detalhes sobre como:

- Crie uma aplicação para os gestores de recurso aumentar a produtividade de occupant.
- Operar a criação com mais eficiência.

Para continuar para os tutoriais, não limpe os recursos criados neste início rápido. Se não quiser continuar, elimine todos os recursos criados por este início rápido.

1. Elimine a pasta que foi criada quando transferiu o repositório de exemplo.
1. A partir do menu à esquerda no [portal Azure,](https://portal.azure.com)selecione **Todos os recursos.** Em seguida, selecione o recurso de duplos Digital. No topo do painel **de todos os recursos,** selecione **Delete**.

    > [!TIP]
    > Se já teve problemas em apagar a sua instância De Gémeos Digitais, foi lançada uma atualização de serviço com a correção. Volte a tentar eliminar a instância.

## <a name="next-steps"></a>Passos seguintes

Este quickstart usou um cenário simples e aplicações de amostra para mostrar como as Gémeas Digitais podem ser usadas para encontrar quartos com boas condições de trabalho. Para uma análise aprofundada deste cenário, leia este tutorial:

>[!div class="nextstepaction"]
>[Tutorial: Deploy Azure Digital Twins and configure a spatial graph](tutorial-facilities-setup.md) (Tutorial: Implementar o Azure Digital Twins e configurar um gráfico espacial)
