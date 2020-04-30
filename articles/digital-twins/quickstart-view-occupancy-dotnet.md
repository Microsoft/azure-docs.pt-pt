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
ms.openlocfilehash: de8611e53339d38a03836bc9272ad42ff88a59b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79371431"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Quickstart: Encontre quartos disponíveis utilizando gémeos digitais Azure

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

O serviço Azure Digital Twins permite-lhe recriar uma imagem digital do seu ambiente físico. Depois, pode ser notificado por eventos no ambiente e personalizar as respostas para esses eventos.

Este quickstart usa [um par de amostras .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) para digitalizar um edifício de escritórios imaginário. Mostra como encontrar quartos disponíveis naquele edifício. Com as Gémeas Digitais, pode associar muitos sensores ao seu ambiente. Você também pode descobrir se a qualidade do ar do seu quarto disponível é ideal com a ajuda de um sensor simulado para dióxido de carbono. Uma das aplicações da amostra gera dados de sensores aleatórios para ajudá-lo a visualizar este cenário.

O seguinte vídeo resume a configuração quickstart:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma conta do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. As duas aplicações de consola que executa neste quickstart são escritas usando C#. Instale a [versão .NET Core SDK 2.1.403 ou superior](https://www.microsoft.com/net/download) na sua máquina de desenvolvimento. Se tiver instalado o .NET Core SDK, verifique a versão atual de C# na sua máquina de desenvolvimento. Corra `dotnet --version` num pedido de comando.

1. Descarregue o [projeto C# da amostra.](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) Extrair o arquivo digital-twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Criar uma instância do Digital Twins

Crie uma nova instância de Gémeos Digitais no [portal](https://portal.azure.com) seguindo os passos nesta secção.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Definir permissões para a aplicação

Esta secção regista a sua aplicação de amostra seletiva para o Azure Ative Directory (Azure AD) para que possa aceder à instância das Gémeas Digitais. Se já tem uma inscrição na aplicação Azure AD, reutilize-a para a sua amostra. Certifique-se de que está configurado como descrito nesta secção.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Compilar a aplicação

Construa a aplicação de ocupação seguindo estes passos.

1. Abra uma linha de comandos. Vá para a `digital-twins-samples-csharp-master.zip` pasta onde os seus ficheiros foram extraídos.
1. Execute `cd occupancy-quickstart/src`.
1. Execute `dotnet restore`.
1. Edite [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) para atualizar as variáveis seguintes:
    - **ClientId**: Introduza o ID de aplicação do registo da sua aplicação Azure AD, anotado na secção anterior.
    - **Inquilino**: Insira a identificação do Diretório do seu inquilino Azure AD, também anotado na secção anterior.
    - **BaseUrl**: O URL aPI de gestão `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`da sua instância De Gémeos Digitais está no formato . Substitua os espaços reservados neste URL por valores, por exemplo, da secção anterior.

    Guarde o ficheiro atualizado.

## <a name="provision-graph"></a>Aprovisionar o gráfico

Este passo prevê o seu gráfico espacial Digital Twins com:

- Vários espaços.
- Um dispositivo.
- Dois sensores.
- Uma função personalizada.
- Uma missão de papel.

O gráfico espacial é provisionado utilizando o ficheiro [provisionSample.yaml.](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)

1. Execute `dotnet run ProvisionSample`.

    >[!NOTE]
    >A ferramenta Login Azure CLI do dispositivo é utilizada para autenticar o utilizador à AD Azure. O utilizador deve introduzir um determinado código para autenticar utilizando a página de [login da Microsoft.](https://microsoft.com/devicelogin) Depois de introduzido o código, siga os passos para autenticar. O utilizador deve autenticar quando a ferramenta estiver em funcionamento.

    >[!TIP]
    > Quando executar este passo, certifique-se de que as suas variáveis foram devidamente copiadas se aparecer a seguinte mensagem de erro:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. O passo de provisionamento pode demorar alguns minutos. Também prevê um Hub IoT dentro da sua instância de Gémeos Digitais. Passa até que o Hub IoT`Running`mostre status= .

    [![Provision a amostra - Status=Running](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. No final da execução, `ConnectionString` copie o dispositivo para utilização na amostra de simulador do dispositivo. Copie apenas a corda descrita nesta imagem.

    [![Copiar a cadeia de ligação](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > Pode ver e modificar o seu gráfico espacial utilizando o [Telespectador digital Twins Graph Viewer](https://github.com/Azure/azure-digital-twins-graph-viewer).

Mantenha a janela da consola aberta para ser utilizada novamente mais tarde.

## <a name="send-sensor-data"></a>Enviar dados do sensor

Construa e execute a aplicação do dispositivo simulador de sensores seguindo estes passos.

1. Abra um novo pedido de comando. Vá ao projeto que descarregou na `digital-twins-samples-csharp-master` pasta.
1. Execute `cd device-connectivity`.
1. Execute `dotnet restore`.
1. Editar [as definições de aplicações.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) para atualizar **deviceConnectionString** com o anterior `ConnectionString`. Guarde o ficheiro atualizado.
1. Corra `dotnet run` para começar a enviar dados do sensor. Será enviado para a Azure Digital Twins, como mostra a imagem seguinte.

     [![Conectividade do dispositivo](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Deixe este simulador funcionar para que possa ver os resultados lado a lado com a próxima ação do passo. Esta janela mostra-lhe os dados do sensor simulado enviados para Gémeos Digitais. O próximo passo consulta em tempo real para encontrar quartos disponíveis com ar fresco.

    >[!TIP]
    > Quando executar este passo, `DeviceConnectionString` certifique-se de que foi copiado corretamente se aparecer a seguinte mensagem de erro:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Encontrar espaços disponíveis com ar fresco

A amostra do sensor simula valores de dados aleatórios para dois sensores. São movimento e dióxido de carbono. Os espaços disponíveis com ar fresco são definidos na amostra sem presença na sala. Também são definidos por um nível de dióxido de carbono inferior a 1.000 ppm. Se a condição não for cumprida, o espaço não está disponível ou a qualidade do ar é fraca.

1. Abra o pedido de comando que usou para executar o passo de provisionamento mais cedo.
1. Execute `dotnet run GetAvailableAndFreshSpaces`.
1. Olhe para este pedido de comando e o comando de dados do sensor é rápido lado a lado.

    O pedido de comando de dados do sensor envia dados simulados de movimento e dióxido de carbono para gémeos digitais a cada cinco segundos. O outro pedido de comando lê o gráfico em tempo real para descobrir quartos disponíveis com ar fresco com base em dados simulados aleatórios. Apresenta uma destas condições em tempo quase real com base nos dados do sensor que foram enviados por último:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Obter espaços disponíveis com ar fresco](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Para entender o que aconteceu neste arranque rápido e o que as APIs `digital-twins-samples-csharp`foram chamadas, abra o Código do Estúdio [Visual](https://code.visualstudio.com/Download) com o projeto de espaço de trabalho de código encontrado em . Utilize o seguinte comando:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Os tutoriais entram profundamente no código. Ensinam-nos a modificar os dados de configuração e o que as APIs são chamadas. Para mais informações sobre APIs de Gestão, vá à sua página De Gémeos Digitais Swagger:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | O nome da sua instância de Gémeos Digitais |
| YOUR_LOCATION | A região do servidor onde a sua instância é hospedada |

Ou por conveniência, navegue até [às Gémeas Digitais Swagger.](https://docs.westcentralus.azuresmartspaces.net/management/swagger)

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais entram em detalhes sobre como:

- Construa uma aplicação para os gestores de instalações aumentarem a produtividade dos ocupantes.
- Opere o edifício de forma mais eficiente.

Para continuar com os tutoriais, não limpe os recursos criados neste arranque rápido. Se não pretende continuar, elimine todos os recursos criados por este arranque rápido.

1. Elimine a pasta que foi criada quando descarregou o repositório da amostra.
1. A partir do menu à esquerda no [portal Azure,](https://portal.azure.com)selecione **Todos os recursos.** Em seguida, selecione o seu recurso Digital Twins. No topo do painel **de todos os recursos,** selecione **Delete**.

    > [!TIP]
    > Se já teve problemas em apagar a sua instância De Gémeos Digitais, foi lançada uma atualização de serviço com a correção. Por favor, tente apagar o seu caso.

## <a name="next-steps"></a>Passos seguintes

Este quickstart usou um cenário simples e aplicações de amostra para mostrar como as Gémeas Digitais podem ser usadas para encontrar quartos com boas condições de trabalho. Para uma análise aprofundada deste cenário, leia este tutorial:

>[!div class="nextstepaction"]
>[Tutorial: Deploy Azure Digital Twins and configure a spatial graph](tutorial-facilities-setup.md) (Tutorial: Implementar o Azure Digital Twins e configurar um gráfico espacial)
