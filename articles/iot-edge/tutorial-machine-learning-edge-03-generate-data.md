---
title: 'Tutorial: gerar Machine Learning de dados do dispositivo simulado em Azure IoT Edge'
description: Crie dispositivos virtuais que gerem telemetria simulada que mais tarde possam ser usados para treinar um modelo de aprendizagem automática.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722389"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: gerar dados de dispositivo simulados

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, utilizamos dados de formação de machine learning para simular um dispositivo que envia telemetria para o Hub Azure IoT. Como indicado na introdução, este tutorial utiliza o conjunto de dados de simulação de simulação do [motor Turbofan](https://c3.nasa.gov/dashlink/resources/139/) para simular dados de um conjunto de motores de avião para treino e teste.

No nosso cenário experimental, sabemos que:

* Os dados consistem em várias séries de tempo multivariadas.
* Cada conjunto de dados é dividido em subconjuntos de treino e teste.
* Cada série de cada vez é de um motor diferente.
* Cada motor começa com diferentes graus de desgaste inicial e variação de fabrico.

Para este tutorial, usamos o subconjunto de dados de treinamento de um conjunto de dados único (FD003).

Na realidade, cada mecanismo seria um dispositivo IoT independente. Supondo que você não tenha uma coleção de mecanismos de turbofan conectados à Internet disponíveis, criaremos um suporte de software para esses dispositivos.

O simulador é C# um programa que usa as APIs do Hub IOT para registrar programaticamente dispositivos virtuais com o Hub IOT. Em seguida, podemos ler os dados de cada dispositivo do subconjunto de dados fornecido pela NASA e enviá-los para o Hub IoT usando um dispositivo IoT simulado. Todo o código para esta parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto DeviceHarness é um projeto do .NET Core escrito C# em consistindo em quatro classes:

* **Programa:** O ponto de entrada para a execução responsável pelo tratamento da entrada do utilizador e da coordenação geral.
* **TrainingFileManager:** Responsável pela leitura e análise do ficheiro de dados selecionado.
* **Data do Ciclo:** Representa uma única linha de dados num ficheiro convertido em formato de mensagem.
* **TurbofanDevice:** Responsável pela criação de um Dispositivo IoT, que corresponde a um único dispositivo (série de tempo), nos dados e transmissão dos dados para o IoT Hub.

As tarefas descritas neste artigo devem levar cerca de 20 minutos para serem concluídas.

O equivalente do mundo real ao trabalho nesta etapa provavelmente seria executado por desenvolvedores de dispositivos e desenvolvedores de nuvem.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configurar Visual Studio Code e compilar o projeto DeviceHarness

1. Abra uma sessão remota de desktop para o seu VM de desenvolvimento.

1. No código Visual Studio, abra a pasta `C:\source\IoTEdgeAndMlSample\DeviceHarness`.

1. Como você está usando extensões neste computador pela primeira vez, algumas extensões serão atualizadas e instalarão suas dependências. Você pode ser solicitado a atualizar a extensão. Em caso afirmativo, selecione **Recarregar a Janela**.

   Se surgirem erros OmniSharp na janela de saída, C# terá de desinstalar a extensão.

1. Você será solicitado a adicionar os ativos necessários para DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação pode levar alguns segundos para ser exibida.
   * Se perdeu esta notificação, verifique o ícone do sino no canto inferior direito.

   ![Pop-up de extensão de VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecione **Restaurar** para restaurar as dependências do pacote.

   ![Prompt de restauração de VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Se não receber estas notificações, feche o Visual Studio Code, elimine os diretórios de bin e obj em `C:\source\IoTEdgeAndMlSample\DeviceHarness`, abra o Código do Estúdio Visual e abra a pasta DeviceHarness.

1. Valide que o seu ambiente está corretamente configurado desencadeando uma construção, a Mudança **de** + **CTRL** + **B,** ou **a Tarefa** de **Construção**de > Terminal .

1. É-lhe pedido que selecione a tarefa de construção a executar. Selecione **Construir**.

1. A compilação é executada e gera uma mensagem de êxito.

   ![Mensagem de saída de compilação bem-sucedida](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Pode fazer com que esta construção seja a tarefa de construção predefinida selecionando **o Terminal** > Configurar a Tarefa de **Construção padrão...** e escolhendo **construir** a partir do pedido.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Conectar-se ao Hub IoT e executar o DeviceHarness

Agora que temos a compilação do projeto, conecte-se ao Hub IoT para acessar a cadeia de conexão e monitorar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Entrar no Azure no Visual Studio Code

1. Inscreva-se na sua assinatura Azure no Visual Studio Code abrindo a paleta de comando, `Ctrl + Shift + P` ou **ver** > Paleta de **Comando**.

1. Pesquisa do **Azure: Assine no** comando.

   Uma janela do navegador é aberta e solicita suas credenciais. Quando for redirecionado para uma página de sucesso, pode fechar o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Conectar-se ao Hub IoT e recuperar a cadeia de conexão do Hub

1. Na secção inferior do explorador de Código de Estúdio Visual, selecione a moldura **Do Hub Azure IoT** para expandi-lo.

1. No quadro expandido, clique em **Select IoT Hub**.

1. Quando solicitado, selecione sua assinatura do Azure e, em seguida, o Hub IoT.

1. Clique no **...** à direita do **Azure IoT Hub** para mais ações. Selecione a cadeia de **ligação Copy IoT Hub**.

   ![Copiar cadeia de conexão do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Executar o projeto DeviceHarness

1. Selecione **Ver** > **Terminal** para abrir o terminal de Código de Estúdio Visual.

   Se não vir um aviso, prima Enter.

1. Introduza `dotnet run` no terminal.

1. Quando a cadeia de conexão do Hub IoT for solicitada, Cole a cadeia de conexão copiada na seção anterior.

1. Na moldura dos **dispositivos Azure IoT Hub,** clique no botão de atualização.

   ![Atualizar a lista de dispositivos do Hub IoT](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observe que os dispositivos são adicionados ao Hub IoT e que os dispositivos aparecem em verde para indicar que os dados estão sendo enviados por meio desse dispositivo. Depois de os dispositivos enviarem mensagens para o centro IoT, desligam-se e parecem azuis.

1. Pode visualizar mensagens enviadas para o hub clicando à direita em qualquer dispositivo e selecionando **Start Monitoring Built-in Event Endpoint**. As mensagens serão mostradas no painel de saída em Visual Studio Code.

1. Pare de monitorizar clicando no painel de saída do **Hub Azure IoT** e escolha parar de monitorizar o ponto final de **evento incorporado**.

1. Deixe o aplicativo ser executado até a conclusão, o que levará alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verificar a atividade do Hub IoT

Os dados enviados pelo DeviceHarness foram para o seu hub IoT, onde pode verificar no portal Azure.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue até ao hub IoT criado para este tutorial.

1. A partir do menu do painel esquerdo, sob **monitorização,** selecione **Métricas**.

1. Na página de definição de gráfico, clique na queda **métrica,** percorra a lista e selecione **Routing: dados entregues ao armazenamento**. O gráfico deve mostrar o pico de quando os dados foram encaminhados para o armazenamento.

   ![Gráfico mostra pico quando os dados entregues ao armazenamento](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no armazenamento do Azure

Os dados que acabamos de enviar para o Hub IoT foram roteados para o contêiner de armazenamento que criamos no artigo anterior. Vamos examinar os dados em nossa conta de armazenamento.

1. No portal do Azure, navegue para a sua conta de armazenamento.

1. A partir do navegador da conta de armazenamento, selecione **Storage Explorer (pré-visualização)** .

1. No explorador de armazenamento, selecione **Blob Containers** e, em seguida, `devicedata`.

1. No painel de conteúdos, clique na pasta para o nome do hub IoT, seguido de ano, mês, dia e hora. Você verá várias pastas que representam os minutos em que os dados foram gravados.

   ![Exibir pastas no armazenamento de BLOBs](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique numa dessas pastas para encontrar ficheiros de dados marcados **00** e **01** correspondentes à partição.

1. Os ficheiros estão escritos em formato [Avro.](https://avro.apache.org/) Clique duas vezes num destes ficheiros para abrir outro separador de navegador e renderizar parcialmente os dados. Se for solicitado a abrir o ficheiro num programa, pode escolher o Código VS e será corretamente correto.

1. Não é necessário tentar ler ou interpretar os dados no momento; Faremos isso no próximo artigo.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, usamos um projeto .NET Core para criar um conjunto de dispositivos IoT virtuais e enviar dados através deles para o nosso hub IoT e para um recipiente de Armazenamento Azure. Este projeto simula um cenário real onde dispositivos ioT físicos enviam dados para um Hub IoT e para um armazenamento curado. Estes dados incluem leituras de sensores, configurações operacionais, sinais de falha e modos, e assim por diante. Uma vez recolhidos dados suficientes, utilizamo-lo para treinar modelos que prevejam a vida útil restante (RUL) para o dispositivo. Vamos demonstrar esta aprendizagem automática no próximo artigo.

Continue no próximo artigo para treinar um modelo de aprendizado de máquina com os dados.

> [!div class="nextstepaction"]
> [Treine e implemente um modelo de Aprendizagem automática Azure](tutorial-machine-learning-edge-04-train-model.md)
