---
title: Gerar dados de dispositivo simulado - Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Crie dispositivos virtuais que geram telemetria simulada que pode ser utilizada mais tarde para preparar um modelo de machine learning.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a93b1128fe1ea0e03efc9060f2c3c4a93145f838
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432856"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Gerar dados de dispositivo simulado

> [!NOTE]
> Este artigo faz parte de uma série de para um tutorial sobre como utilizar o Azure Machine Learning do IoT Edge. Se o ter chegado neste artigo diretamente, é recomendável que começa com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter melhores resultados.

Neste artigo, vamos utilizar o machine learning para simular um dispositivo enviar telemetria para o IoT Hub, os dados de formação. Conforme mencionado na introdução, este tutorial ponto-a-ponto utiliza a [conjunto de dados de simulação de degradação de motor de Turbofan](https://c3.nasa.gov/dashlink/resources/139/) para simular dados a partir de um conjunto de motores de avião para preparação e teste.

Do Readme. txt que acompanha este artigo que Sabemos que:

* Os dados consiste em várias séries de tempo multivariate
* Cada conjunto de dados está dividido em subconjuntos de preparação e teste
* Cada série de tempo é de um mecanismo de diferente
* Cada motor começa com diferentes níveis de desgaste pode eventualmente inicial e a variação de fabrico

Neste tutorial, vamos utilizar o subconjunto de dados de treinamento de um único conjunto de dados (FD003).

Na realidade, cada mecanismo seria um dispositivo de IoT independente. Partindo do princípio de que não tem uma coleção de mecanismos de ligação à internet turbofan disponíveis, que iremos criar um substituto de software para estes dispositivos.

O simulator é um C# programa que usa as APIs do Hub IoT através de programação registar dispositivos virtuais com o IoT Hub. Em seguida, vamos ler os dados para cada dispositivo do subconjunto de dados fornecidos pelo NASA e enviá-lo para o seu hub IoT com um dispositivo de IoT simulados. Todo o código para essa parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto de DeviceHarness é um projeto de núcleo de .NET escrito em C# consiste em quatro classes:

* **Programa:** O ponto de entrada para execução responsável por processar a entrada do usuário e a coordenação geral.
* **TrainingFileManager:** responsável por ler e analisar o ficheiro de dados selecionada.
* **CycleData:** representa uma única linha de dados num arquivo convertida em formato de mensagem.
* **TurbofanDevice:** responsável pela criação de um dispositivo de IoT que corresponde a um único dispositivo (série de tempo) dos dados e transmitir os dados para o IoT Hub através do dispositivo IoT.

As tarefas descritas neste artigo devem demorar cerca de 20 minutos a concluir.

O equivalente do mundo real para o trabalho neste passo provavelmente seria realizado pelos desenvolvedores de dispositivo e na cloud.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configurar o Visual Studio Code e criar o projeto DeviceHarness

1. Abra uma sessão de ambiente de trabalho remoto para a máquina virtual, como demonstrado no artigo anterior.

1. Abra o Visual Studio Code.

1. No Visual Studio Code, selecione **arquivo** > **Abrir pasta...** .

1. Na **pasta** caixa de texto, introduza `C:\source\IoTEdgeAndMlSample\DeviceHarness` e clique no **selecionar pasta** botão.

   Se surgirem erros de OmniSharp na janela de saída, terá de desinstalar o C# extensão, feche e volte a abrir VS Code, instalar o C# extensão e, em seguida, recarregue a janela.

1. Uma vez que estiver a utilizar extensões nesta máquina pela primeira vez, algumas extensões irão atualizar e instalar as respetivas dependências. Poderá ser-lhe pedido para atualizar a extensão. Nesse caso, selecione **janela de recarregamento**.

1. Será solicitado para adicionar recursos necessários para DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação pode demorar alguns segundos a aparecer.
   * Se perdeu esta notificação, verifique o ícone de "sino", no canto inferior direito.

   ![Janela de pop-up da extensão do código de VS](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecione **restaurar** para restaurar as dependências de pacote.

   ![Pedido de restauro de código VS](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Validar que o ambiente está devidamente configurado ao acionar uma compilação `Ctrl + Shift + B` ou **Terminal** > **executar tarefa de compilação**.

1. É solicitado a selecionar a tarefa de compilação para executar. Selecione **criar**.

1. A compilação é executada e devolve uma mensagem de êxito.

   ![Mensagem de saída da compilação foi concluída com êxito](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Pode fazer isso criar a tarefa de compilação predefinida selecionando **Terminal** > **configurar tarefas de compilação padrão...**  e escolhendo **criar** da linha de comandos.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Ligar ao IoT Hub e execute DeviceHarness

Agora que temos o projeto a criar, ligar-se ao seu hub IoT para acessar a cadeia de ligação e monitorizar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Inicie sessão no Azure no Visual Studio Code

1. Inicie sessão na sua subscrição do Azure no Visual Studio Code, abra a paleta de comandos `Ctrl + Shift + P` ou **vista** > **paleta de comandos...** .

1. Na pesquisa de linha de comandos para e selecione **Azure: Inicie sessão no**.

1. Uma janela do browser abre-se e pede-lhe as suas credenciais. Quando será redirecionado para uma página de êxito, pode fechar o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Ligar ao seu hub IoT e obter cadeia de ligação do hub

1. Na secção de na parte inferior do Explorador do Visual Studio Code, selecione o **dispositivos do IoT Hub do Azure** quadro para expandi-la.

1. Na estrutura de expandido, clique em **selecione o IoT Hub**.

1. Quando lhe for pedido, selecione a sua subscrição do Azure e, em seguida, o hub IoT.

1. Clique para o **dispositivos do IoT Hub do Azure** fotograma e clique em **...**  por mais ações. Selecione **cadeia de ligação do Hub de IoT de cópia**.

   ![Copie a cadeia de ligação do IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Execute o projeto de DeviceHarness

1. Selecione **View** > **Terminal** para abrir o terminal do Visual Studio Code.

   Se não vir uma linha de comandos, selecione Enter.

1. Introduza `dotnet run` no terminal.

1. Quando lhe for pedido para a cadeia de ligação do Hub IoT, cole a cadeia de ligação que copiou na secção anterior.

1. Na **dispositivos do IoT Hub do Azure** de fotograma, clique no botão de atualização.

   ![Atualizar a lista de dispositivos do IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Tenha em atenção que os utilizadores são adicionados ao IoT Hub e que os dispositivos apresentados a verde para indicar que os dados estão a ser enviados por meio desse dispositivo.

1. Pode ver as mensagens a ser enviadas para o hub, clicando com o botão direito em qualquer dispositivo e selecionando **iniciar mensagens D2C monitorização**. Mostram as mensagens no painel de saída no Visual Studio Code.

1. Parar a monitorização, clicando na **Kit de ferramentas do Azure IoT Hub** painel de saída e escolha **parar mensagens D2C monitorização**.

1. Permitir que o aplicativo seja executado para conclusão, o que demora alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verifique o IoT Hub para a atividade

Os dados enviados pelo DeviceHarness correu ao seu hub IoT. É fácil verificar que dados atingiu o seu hub no portal do Azure.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até ao seu hub IoT.

1. Na página Descrição geral do deverá ver que foram enviados dados para o hub:  

   ![Dispositivo de vista para mensagens de cloud do IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no armazenamento do Azure

Os dados que acabámos de enviar ao seu hub IoT foi encaminhados para o contentor de armazenamento que criámos no artigo anterior. Vamos examinar os dados em nossa conta de armazenamento.

1. No portal do Azure, navegue para a sua conta de armazenamento.

1. A partir do navegador de conta de armazenamento, selecione **Explorador de armazenamento (pré-visualização)** .

1. No Explorador de armazenamento, selecione **contentores de BLOBs** , em seguida, **devicedata**.

1. No painel de conteúdo, clique na pasta para o nome do IoT hub, em seguida, o ano, mês, dia e hora. Verá várias pastas que representa os minutos quando os dados foi escritos.

   ![Visualizar pastas no armazenamento de BLOBs](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique dessas pastas para encontrar os ficheiros de dados etiquetados **00** e **01** correspondente para a partição.

1. Os ficheiros são escritos em [Avro](https://avro.apache.org/) formato, mas duas vezes em um desses arquivos abrirá noutro separador do browser e parcialmente compor os dados. Se lhe for pedido em vez disso, para abrir o arquivo num programa, pode escolher o VS Code e ele será composta corretamente.

1. Não é necessário para tentar ler ou interpretar os dados neste momento; podemos fazê-lo no próximo artigo.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, nós usamos um projeto .NET Core para criar um conjunto de dispositivos virtuais e enviar dados por meio desses dispositivos através do nosso IoT Hub e para um contentor de armazenamento do Azure. Este projeto simula um cenário do mundo real em que dispositivos físicos enviam dados, incluindo as leituras dos sensores, as definições operacionais, sinais de falha e modos de, e assim por diante, para um IoT Hub e ou superior num armazenamento organizado. Assim que foram recolhidos dados suficientes, podemos utilizá-lo a criar modelos que prever a vida útil remanescente (RUL) para o dispositivo, o que vamos demonstrar no próximo artigo.

Avance para o artigo seguinte para preparar um modelo de machine learning com os dados.

> [!div class="nextstepaction"]
> [Preparar e implementar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
