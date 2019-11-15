---
title: 'Tutorial: gerar Machine Learning de dados do dispositivo simulado em Azure IoT Edge'
description: 'Tutorial: criar dispositivos virtuais que geram telemetria simulada que posteriormente pode ser usada para treinar um modelo de aprendizado de máquina.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 51d93e5b83d203f3fa99b69cc5f2877bbfdb6fb1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112865"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: gerar dados de dispositivo simulados

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, usamos dados de treinamento do Machine Learning para simular um dispositivo enviando telemetria para o Hub IoT. Conforme mencionado na introdução, este tutorial de ponta a ponta usa o conjunto de [dados de simulação de degradação do mecanismo turbofan](https://c3.nasa.gov/dashlink/resources/139/) para simular dados de um conjunto de motores de avião para treinamento e teste.

No arquivo readme. txt que o acompanha, sabemos que:

* Os dados consistem em várias séries temporais multivariadas
* Cada conjunto de dados é dividido em subconjuntos de treinamento e teste
* Cada série temporal é de um mecanismo diferente
* Cada mecanismo começa com diferentes graus de desgaste inicial e variação de fabricação

Para este tutorial, usamos o subconjunto de dados de treinamento de um conjunto de dados único (FD003).

Na realidade, cada mecanismo seria um dispositivo IoT independente. Supondo que você não tenha uma coleção de mecanismos de turbofan conectados à Internet disponíveis, criaremos um suporte de software para esses dispositivos.

O simulador é C# um programa que usa as APIs do Hub IOT para registrar programaticamente dispositivos virtuais com o Hub IOT. Em seguida, podemos ler os dados de cada dispositivo do subconjunto de dados fornecido pela NASA e enviá-los para o Hub IoT usando um dispositivo IoT simulado. Todo o código para esta parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto DeviceHarness é um projeto do .NET Core escrito C# em consistindo em quatro classes:

* **Programa:** O ponto de entrada para a execução responsável por lidar com a entrada do usuário e a coordenação geral.
* **TrainingFileManager:** responsável por ler e analisar o arquivo de dados selecionado.
* **CycleData:** representa uma única linha de dados em um arquivo convertido em formato de mensagem.
* **TurbofanDevice:** responsável por criar um dispositivo IOT que corresponde a um único dispositivo (série temporal) nos dados e transmitindo os dados para o Hub IOT por meio do dispositivo IOT.

As tarefas descritas neste artigo devem levar cerca de 20 minutos para serem concluídas.

O equivalente do mundo real ao trabalho nesta etapa provavelmente seria executado por desenvolvedores de dispositivos e desenvolvedores de nuvem.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configurar Visual Studio Code e compilar o projeto DeviceHarness

1. Abra uma sessão de área de trabalho remota para sua máquina virtual, conforme demonstrado no artigo anterior.

1. Abra o Visual Studio Code.

1. Em Visual Studio Code, selecione **arquivo** > **abrir pasta.** ...

1. Na caixa de texto **pasta** , insira `C:\source\IoTEdgeAndMlSample\DeviceHarness` e clique no botão **Selecionar pasta** .

   Se os erros de OmniSharp aparecerem na janela de saída, você precisará C# desinstalar a extensão, fechar e reabrir vs Code, C# instalar a extensão e recarregar a janela.

1. Como você está usando extensões neste computador pela primeira vez, algumas extensões serão atualizadas e instalarão suas dependências. Você pode ser solicitado a atualizar a extensão. Nesse caso, selecione **recarregar janela**.

1. Você será solicitado a adicionar os ativos necessários para DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação pode levar alguns segundos para ser exibida.
   * Se você perdeu essa notificação, verifique o ícone "Bell" no canto inferior direito.

   ![Pop-up de extensão de VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecione **restaurar** para restaurar as dependências do pacote.

   ![Prompt de restauração de VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Valide se seu ambiente está configurado corretamente disparando um Build, `Ctrl + Shift + B` ou **Terminal** > **executar a tarefa de compilação**.

1. Você será solicitado a selecionar a tarefa de compilação a ser executada. Selecione **Compilar**.

1. A compilação é executada e gera uma mensagem de êxito.

   ![Mensagem de saída de compilação bem-sucedida](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Você pode fazer com que essa compilação seja a tarefa de compilação padrão selecionando **Terminal** > **Configurar tarefa de compilação padrão...** e escolhendo **Compilar** no prompt.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Conectar-se ao Hub IoT e executar o DeviceHarness

Agora que temos a compilação do projeto, conecte-se ao Hub IoT para acessar a cadeia de conexão e monitorar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Entrar no Azure no Visual Studio Code

1. Entre na sua assinatura do Azure em Visual Studio Code abrindo a paleta de comandos, `Ctrl + Shift + P` ou **exibir** > **paleta de comandos...** .

1. No prompt, pesquise e selecione **Azure: entrar**.

1. Uma janela do navegador é aberta e solicita suas credenciais. Quando você for Redirecionado para uma página de êxito, poderá fechar o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Conectar-se ao Hub IoT e recuperar a cadeia de conexão do Hub

1. Na seção inferior do Visual Studio Code Explorer, selecione o quadro **dispositivos do Hub IOT do Azure** para expandi-lo.

1. No quadro expandido, clique em **selecionar Hub IOT**.

1. Quando solicitado, selecione sua assinatura do Azure e, em seguida, o Hub IoT.

1. Clique no quadro **dispositivos do Hub IOT do Azure** e clique em **...** para obter mais ações. Selecione **copiar cadeia de conexão do Hub IOT**.

   ![Copiar cadeia de conexão do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Executar o projeto DeviceHarness

1. Selecione **exibir** > **terminal** para abrir o terminal Visual Studio Code.

   Se você não vir um prompt, selecione Enter.

1. Insira `dotnet run` no terminal.

1. Quando a cadeia de conexão do Hub IoT for solicitada, Cole a cadeia de conexão copiada na seção anterior.

1. No quadro **dispositivos do Hub IOT do Azure** , clique no botão atualizar.

   ![Atualizar a lista de dispositivos do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observe que os dispositivos são adicionados ao Hub IoT e que os dispositivos aparecem em verde para indicar que os dados estão sendo enviados por meio desse dispositivo.

1. Você pode exibir as mensagens que estão sendo enviadas ao Hub clicando com o botão direito do mouse em qualquer dispositivo e selecionando **Iniciar Monitoramento de ponto de extremidade de evento interno**. As mensagens serão mostradas no painel de saída em Visual Studio Code.

1. Pare o monitoramento clicando no painel de saída do **Kit de ferramentas do Hub IOT do Azure** e escolha **parar monitoramento do ponto de extremidade do evento interno**.

1. Deixe o aplicativo ser executado até a conclusão, o que levará alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verificar a atividade do Hub IoT

Os dados enviados pelo DeviceHarness entraram em seu hub IoT. É fácil verificar se os dados atingiram o Hub usando o portal do Azure.

1. Abra o [portal do Azure](https://portal.azure.com/) e navegue até o Hub IOT.

1. Na página Visão geral, você verá que os dados foram enviados para o Hub:  

   ![Exibir dispositivo para mensagens na nuvem no Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no armazenamento do Azure

Os dados que acabamos de enviar para o Hub IoT foram roteados para o contêiner de armazenamento que criamos no artigo anterior. Vamos examinar os dados em nossa conta de armazenamento.

1. No portal do Azure, navegue para a sua conta de armazenamento.

1. No navegador da conta de armazenamento, selecione **Gerenciador de armazenamento (versão prévia)** .

1. No Gerenciador de armazenamento, selecione **contêineres de blob** e, em seguida, **devicedata**.

1. No painel de conteúdo, clique na pasta para o nome do Hub IoT, depois o ano, o mês, o dia e a hora. Você verá várias pastas que representam os minutos em que os dados foram gravados.

   ![Exibir pastas no armazenamento de BLOBs](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique em uma dessas pastas para localizar os arquivos de dados rotulados como **00** e **01** correspondentes à partição.

1. Os arquivos são gravados no formato [Avro](https://avro.apache.org/) , mas clicar duas vezes em um desses arquivos abrirá outra guia do navegador e renderizará parcialmente os dados. Se, em vez disso, você for solicitado a abrir o arquivo em um programa, poderá escolher VS Code e ele será renderizado corretamente.

1. Não é necessário tentar ler ou interpretar os dados no momento; Faremos isso no próximo artigo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos um projeto .NET Core para criar um conjunto de dispositivos virtuais e enviar dados por meio desses dispositivos por meio de nosso Hub IoT e para um contêiner de armazenamento do Azure. Este projeto simula um cenário do mundo real em que os dispositivos físicos enviam dados, incluindo leituras de sensor, configurações operacionais, sinais de falha e modos e assim por diante, a um hub IoT e em um armazenamento organizado. Uma vez coletados dados suficientes, usamos-os para treinar modelos que preveem a vida útil restante (RUL) para o dispositivo, que demonstraremos no próximo artigo.

Continue no próximo artigo para treinar um modelo de aprendizado de máquina com os dados.

> [!div class="nextstepaction"]
> [Treinar e implantar um modelo de Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
