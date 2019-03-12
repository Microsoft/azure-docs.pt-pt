---
title: Salas disponíveis - duplos Digital do Azure | Documentos da Microsoft
description: Neste início rápido, vai executar dois exemplos de aplicações .NET Core para enviar telemetria simulada de movimento e dióxido de carbono para um espaço no Azure Digital Twins. O objetivo é encontrar salas disponíveis com ar fresco a partir das APIs de Gestão após o processamento calculado na cloud.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 12/17/2018
ms.author: alinast
ms.openlocfilehash: 9426fb1df38797ffbb9cc457ba4fe9eb62cfba40
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544278"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Início rápido: Salas disponíveis ao utilizar o gémeos Digital do Azure

O serviço de duplos Digital do Azure permite-lhe voltar a criar uma imagem digital do seu ambiente físico. Depois, pode ser notificado por eventos no ambiente e personalizar as respostas para esses eventos.

Este início rápido utiliza [um par de amostras do .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) a digitalização de um edifício de escritórios imaginário. Ele mostra como de salas disponíveis desse edifício. Digital duplos, pode associar vários sensores com o seu ambiente. Também pode descobrir se a qualidade de ar do seu espaço disponível é o ideal com a ajuda de um sensor simulado para emissões de dióxido de carbono. Uma das aplicações de exemplo gera dados de sensor aleatório para o ajudar a visualizar este cenário.

O vídeo seguinte apresenta um resumo da configuração do início rápido:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Pré-requisitos

1. Se não tiver uma conta do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. As aplicações de dois consola executadas neste início rápido são escritas utilizando C#. Instalar o [.NET Core SDK versão 2.1.403 ou acima](https://www.microsoft.com/net/download) no computador de desenvolvimento. Se tiver o .NET Core SDK instalado, certifique-se a versão atual do C# no computador de desenvolvimento. Executar `dotnet --version` numa linha de comandos.

1. Transfira o [exemplo C# projeto](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extraia o arquivo de digital-twins-exemplos-csharp-Master.

## <a name="create-a-digital-twins-instance"></a>Criar uma instância do Digital Twins

Criar uma nova instância de duplos Digital no [portal](https://portal.azure.com) ao seguir os passos nesta secção.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Definir permissões para a aplicação

Esta secção registra seu aplicativo de exemplo para o Azure Active Directory (Azure AD) para que ele pode aceder à sua instância de duplos Digital. Se já tiver um registo de aplicação do Azure AD, reutilizá-lo para o seu exemplo. Certifique-se de que está configurado conforme descrito nesta secção.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Compilar a aplicação

Crie a aplicação de ocupação seguindo estes passos.

1. Abra uma linha de comandos. Aceda à pasta onde sua `digital-twins-samples-csharp-master.zip` arquivos foram extraídos.
1. Execute `cd occupancy-quickstart/src`.
1. Execute `dotnet restore`.
1. Edite [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) para atualizar as variáveis seguintes:
    - **ClientId**: Introduza o ID da aplicação do seu registo de aplicação do Azure AD, indicado na secção anterior.
    - **Inquilino**: Introduza o ID de diretório do seu inquilino do Azure AD, também é indicado na secção anterior.
    - **BaseUrl**: O URL da API de gestão da sua instância de duplos digitais está no formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Substitua os marcadores de posição este URL com os valores para a sua instância da secção anterior.

## <a name="provision-graph"></a>Aprovisionar o gráfico

Este passo Aprovisiona o gráfico de geográfico duplos Digital com:

- Vários espaços.
- Um dispositivo.
- Dois sensores.
- Uma função personalizada.
- Uma atribuição de função.

O gráfico espacial é aprovisionado através da utilização a [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) ficheiro.

1. Execute `dotnet run ProvisionSample`.
    >[!NOTE]
    >A ferramenta de CLI do dispositivo início de sessão do Azure é utilizada para autenticar o utilizador para o Azure AD. O utilizador tem de introduzir um código específico para autenticar com [o início de sessão do Microsoft](https://microsoft.com/devicelogin) página. Depois do código é inserido, siga os passos para se autenticar. O utilizador tem de ser autenticado quando é executada a ferramenta.

    >[!TIP]
    > Quando executar este passo, certifique-se de que suas variáveis foram copiadas corretamente se é apresentada a seguinte mensagem de erro: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. O passo de aprovisionamento pode demorar alguns minutos. Ele também fornece um IoT Hub na sua instância de duplos Digital. Percorre até que o IoT Hub mostra o estado =`Running`.

    ![Exemplo de aprovisionamento][4]

1. No final da execução, copie o `ConnectionString` do dispositivo para utilização no exemplo de simulador de dispositivos. Copie apenas a cadeia de caracteres descrita nesta imagem.

    ![Exemplo de aprovisionamento][1]

    >[!TIP]
    > Pode ver e modificar seu gráfico espacial com o [Visualizador de gráfico de duplos do Azure Digital](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="send-sensor-data"></a>Enviar dados do sensor

Crie e execute o aplicativo de simulador do sensor, seguindo estes passos.

1. Abra uma nova linha de comandos. Vá para o projeto que transferiu na pasta digital-twins-exemplos-csharp-master.
1. Execute `cd device-connectivity`.
1. Execute `dotnet restore`.
1. Editar [appSettings](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) para atualizar **DeviceConnectionString** com o anterior `ConnectionString`.
1. Executar `dotnet run` para começar a enviar dados de sensor. Verá enviados para o Digital duplos, conforme mostrado na imagem seguinte.

     ![Conectividade do dispositivo][2]

1. Permitir que este simulador em execução para que possa visualizar os resultados lado a lado com a ação de passo seguinte. Esta janela mostra os dados de sensor simulado enviados para o Digital duplos. As consultas de passo seguintes em tempo real de salas com ar fresco disponíveis.

    >[!TIP]
    > Quando executar este passo, certifique-se de que `DeviceConnectionString` foram copiados corretamente se é apresentada a seguinte mensagem de erro: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Encontrar espaços disponíveis com ar fresco

O exemplo de sensor simula os valores de dados aleatórios para dois sensores. Eles são motion e emissões de dióxido de carbono. Disponíveis espaços com ar fresco são definidos no exemplo sem presença na sala. Também estão definidos por um nível de dióxido de carbono em 1000 ppm. Se a condição não é atendida, o espaço não está disponível ou a qualidade de ar é fraca.

1. Abra o prompt de comando utilizado para executar o passo de aprovisionamento anterior.
1. Execute `dotnet run GetAvailableAndFreshSpaces`.
1. Examinar esse prompt de comando e de linha de comandos de dados do sensor lado a lado.

    Um prompt de comando envia dados de movimento e emissões de dióxido de carbono simulados para Digital Twins cada cinco segundos. O outro comando lê o gráfico em tempo real para descobrir os ambientes disponíveis com ar fresco com base nos dados simulados aleatórios. Apresenta uma dessas condições em tempo real com base nos dados de sensor que foi enviados pela última vez:
    - Salas disponíveis com ar fresco.
    - Sala ocupada ou com má qualidade de ar.

     ![Obter espaços disponíveis com ar fresco][3]

Para compreender o que aconteceu neste guia de introdução e que foram chamadas de APIs, abra [Visual Studio Code](https://code.visualstudio.com/Download) com o projeto de área de trabalho de código foi encontrado na digital-twins-exemplos-csharp. Utilize o seguinte comando:

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Os tutoriais fornecem profundamente o código. Que ensinam como modificar os dados de configuração e o que são chamadas de APIs. Para obter mais informações sobre APIs de gestão, aceda à sua página de Swagger de duplos Digital:

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | O nome da sua instância de duplos Digital |
| YOUR_LOCATION | Qual sua instância estiver alojada num servidor a região |

Ou para sua comodidade, navegue até [Swagger de duplos Digital](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais entrar em detalhes sobre como:

- Crie uma aplicação para os gestores de recurso aumentar a produtividade de occupant.
- Operar a criação com mais eficiência.

Para continuar para os tutoriais, não limpe os recursos criados neste início rápido. Se não quiser continuar, elimine todos os recursos criados por este início rápido.

1. Elimine a pasta que foi criada quando transferiu o repositório de exemplo.
1. No menu à esquerda na [portal do Azure](https://portal.azure.com), selecione **todos os recursos**. Em seguida, selecione o recurso de duplos Digital. Na parte superior a **todos os recursos** painel, selecione **eliminar**.

    > [!TIP]
    > Se teve problemas ao eliminar a instância de duplos Digital, uma atualização de serviço capacidade foi implementada com a correção. Volte a tentar eliminar a instância.

## <a name="next-steps"></a>Passos Seguintes

Este guia de introdução utilizado um cenário simples para mostrar como salas com boa condições de trabalho. Para uma análise aprofundada deste cenário, consulte este tutorial:

>[!div class="nextstepaction"]
>[Tutorial: Implementar duplos Digital do Azure e configurar um gráfico espacial](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
