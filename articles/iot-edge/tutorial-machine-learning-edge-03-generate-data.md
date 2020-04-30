---
title: 'Tutorial: Gerar dados simulados do dispositivo - Machine Learning on Azure IoT Edge'
description: Crie dispositivos virtuais que gerem telemetria simulada que mais tarde possam ser usados para treinar um modelo de aprendizagem automática.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76722389"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Gerar dados simulados do dispositivo

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Machine Learning Azure em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, utilizamos dados de formação de machine learning para simular um dispositivo que envia telemetria para o Hub Azure IoT. Como indicado na introdução, este tutorial utiliza o conjunto de dados de simulação de simulação do [motor Turbofan](https://c3.nasa.gov/dashlink/resources/139/) para simular dados de um conjunto de motores de avião para treino e teste.

No nosso cenário experimental, sabemos que:

* Os dados consistem em várias séries de tempo multivariadas.
* Cada conjunto de dados é dividido em subconjuntos de treino e teste.
* Cada série de cada vez é de um motor diferente.
* Cada motor começa com diferentes graus de desgaste inicial e variação de fabrico.

Para este tutorial, utilizamos o subconjunto de dados de formação de um único conjunto de dados (FD003).

Na realidade, cada motor seria um dispositivo IoT independente. Assumindo que não tem uma coleção de motores turbofan ligados à Internet disponíveis, vamos construir um stand-in de software para estes dispositivos.

O simulador é um programa C# que utiliza as APIs do IoT Hub para registar programaticamente dispositivos virtuais com o IoT Hub. Em seguida, lemos os dados de cada dispositivo a partir do subconjunto de dados fornecido pela NASA e enviamo-lo para o seu hub IoT usando um dispositivo IoT simulado. Todo o código para esta parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto DeviceHarness é um projeto .NET core escrito em C# composto por quatro classes:

* **Programa:** O ponto de entrada para a execução responsável pelo tratamento da entrada do utilizador e da coordenação geral.
* **TrainingFileManager:** Responsável pela leitura e análise do ficheiro de dados selecionado.
* **Data do Ciclo:** Representa uma única linha de dados num ficheiro convertido em formato de mensagem.
* **TurbofanDevice:** Responsável pela criação de um Dispositivo IoT, que corresponde a um único dispositivo (série de tempo), nos dados e transmissão dos dados para o IoT Hub.

As tarefas descritas neste artigo devem demorar cerca de 20 minutos a ser concluídas.

O equivalente ao trabalho neste passo seria provavelmente realizado por desenvolvedores de dispositivos e desenvolvedores de nuvem.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configure Código de Estúdio Visual e construa projeto DeviceHarness

1. Abra uma sessão remota de desktop para o seu VM de desenvolvimento.

1. No código do Estúdio `C:\source\IoTEdgeAndMlSample\DeviceHarness` Visual, abra a pasta.

1. Uma vez que está a utilizar extensões nesta máquina pela primeira vez, algumas extensões irão atualizar e instalar as suas dependências. Pode ser solicitado a atualizar a extensão. Em caso afirmativo, selecione **Recarregar a Janela**.

   Se surgirem erros OmniSharp na janela de saída, terá de desinstalar a extensão C#.

1. Será solicitado a adicionar os ativos necessários para o DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação pode demorar alguns segundos a aparecer.
   * Se perdeu esta notificação, verifique o ícone do sino no canto inferior direito.

   ![Popup de extensão vs código](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecione **Restaurar** para restaurar as dependências do pacote.

   ![Pedido de restauro do código VS](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Se não receber estas notificações, feche o Visual Studio Code, `C:\source\IoTEdgeAndMlSample\DeviceHarness`elimine os diretórios de bin e obj em , abra o Código do Estúdio Visual e abra a pasta DeviceHarness.

1. Valide que o seu ambiente está corretamente configurado desencadeando uma construção,**mudança** + de **turno Ctrl** + **B**, ou tarefa de**construção**de **ensaios de terminais** > .

1. É-lhe pedido que selecione a tarefa de construção a executar. Selecione **Construir**.

1. A construção corre e produz uma mensagem de sucesso.

   ![Construir mensagem de saída bem sucedida](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Pode fazer com que esta construção seja a tarefa de construção predefinida selecionando a Tarefa de**Construção padrão** de Configuração do **Terminal...** > e escolhendo **a Build** a partir do pedido.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Ligue-se ao Hub IoT e executar dispositivosHarness

Agora que temos o edifício do projeto, ligue-se ao seu hub IoT para aceder à cadeia de ligação e monitorizar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Inscreva-se no Azure no Código do Estúdio Visual

1. Inscreva-se na sua assinatura Azure no Código `Ctrl + Shift + P` do Estúdio Visual abrindo a paleta de comando, ou **visualizar** > **a Paleta**de Comando .

1. Pesquisa do **Azure: Assine no** comando.

   Uma janela do navegador abre-se e pede-lhe as suas credenciais. Quando for redirecionado para uma página de sucesso, pode fechar o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Ligue-se ao seu hub IoT e recupere a cadeia de ligação do hub

1. Na secção inferior do explorador de Código de Estúdio Visual, selecione a moldura **Do Hub Azure IoT** para expandi-lo.

1. No quadro expandido, clique em **Select IoT Hub**.

1. Quando solicitado, selecione a sua subscrição Azure e, em seguida, o seu hub IoT.

1. Clique no **...** à direita do **Azure IoT Hub** para mais ações. Selecione a cadeia de **ligação Copy IoT Hub**.

   ![Fio de ligação do Hub De cópia IoT](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Executar o projeto DeviceHarness

1. Selecione **'Ver** > **Terminal'** para abrir o terminal de Código do Estúdio Visual.

   Se não vir um aviso, prima Enter.

1. Introduza `dotnet run` no terminal.

1. Quando solicitado para a cadeia de ligação do Hub IoT, colhe a corda de ligação copiada na secção anterior.

1. Na moldura dos **dispositivos Azure IoT Hub,** clique no botão de atualização.

   ![Refresh IoT Hub lista de dispositivos](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Note que os dispositivos são adicionados ao IoT Hub e que os dispositivos aparecem a verde para indicar que os dados estão a ser enviados através desse dispositivo. Depois de os dispositivos enviarem mensagens para o centro IoT, desligam-se e parecem azuis.

1. Pode visualizar mensagens enviadas para o hub clicando à direita em qualquer dispositivo e selecionando **Start Monitoring Built-in Event Endpoint**. As mensagens aparecerão no painel de saída no Código do Estúdio Visual.

1. Pare de monitorizar clicando no painel de saída do **Hub Azure IoT** e escolha parar de monitorizar o ponto final de **evento incorporado**.

1. Deixe a candidatura esgotar-se, o que leva alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verifique o IoT Hub para obter atividade

Os dados enviados pelo DeviceHarness foram para o seu hub IoT, onde pode verificar no portal Azure.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue até ao hub IoT criado para este tutorial.

1. A partir do menu do painel esquerdo, sob **monitorização,** selecione **Métricas**.

1. Na página de definição de gráfico, clique na queda **métrica,** percorra a lista e selecione **Routing: dados entregues ao armazenamento**. O gráfico deve mostrar o pico de quando os dados foram encaminhados para o armazenamento.

   ![Gráfico mostra pico quando os dados entregues ao armazenamento](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no Armazenamento Azure

Os dados que acabamos de enviar para o seu hub IoT foram encaminhados para o contentor de armazenamento que criamos no artigo anterior. Vamos ver os dados da nossa conta de armazenamento.

1. No portal do Azure, navegue para a sua conta de armazenamento.

1. A partir do navegador da conta de armazenamento, selecione **Storage Explorer (pré-visualização)**.

1. No explorador de armazenamento, selecione **Blob Containers** então `devicedata`.

1. No painel de conteúdos, clique na pasta para o nome do hub IoT, seguido de ano, mês, dia e hora. Verá várias pastas que representam as atas quando os dados foram escritos.

   ![Ver pastas no armazenamento de bolhas](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique numa dessas pastas para encontrar ficheiros de dados marcados **00** e **01** correspondentes à partição.

1. Os ficheiros estão escritos em formato [Avro.](https://avro.apache.org/) Clique duas vezes num destes ficheiros para abrir outro separador de navegador e renderizar parcialmente os dados. Se for solicitado a abrir o ficheiro num programa, pode escolher o Código VS e será corretamente correto.

1. Não há necessidade de tentar ler ou interpretar os dados neste momento; vamos fazê-lo no próximo artigo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos um projeto .NET Core para criar um conjunto de dispositivos IoT virtuais e enviar dados através deles para o nosso hub IoT e para um recipiente de Armazenamento Azure. Este projeto simula um cenário real onde dispositivos ioT físicos enviam dados para um Hub IoT e para um armazenamento curado. Estes dados incluem leituras de sensores, configurações operacionais, sinais de falha e modos, e assim por diante. Uma vez recolhidos dados suficientes, utilizamo-lo para treinar modelos que prevejam a vida útil restante (RUL) para o dispositivo. Vamos demonstrar esta aprendizagem automática no próximo artigo.

Continuar para o próximo artigo para treinar um modelo de aprendizagem automática com os dados.

> [!div class="nextstepaction"]
> [Preparar e implementar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
