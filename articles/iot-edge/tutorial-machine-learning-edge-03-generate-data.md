---
title: 'Tutorial: Gerar dados de dispositivos simulados - Machine Learning on Azure IoT Edge'
description: Tutorial - Criar dispositivos virtuais que gerem telemetria simulada que mais tarde podem ser usados para treinar um modelo de aprendizagem automática.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5fdd762834d351119116c5e4854dd4233671c29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463134"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Gerar dados simulados do dispositivo

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, usamos dados de treinamento de machine learning para simular um dispositivo que envia telemetria para Azure IoT Hub. Tal como indicado na introdução, este tutorial utiliza o [conjunto de dados](https://c3.nasa.gov/dashlink/resources/139/) de simulação de degradação do motor Turbofan para simular dados de um conjunto de motores de avião para treino e teste.

No nosso cenário experimental, sabemos que:

* Os dados consistem em múltiplas séries de tempo multivariadas.
* Cada conjunto de dados é dividido em subconjuntos de treino e teste.
* Cada série de tempo é de um motor diferente.
* Cada motor começa com diferentes graus de desgaste inicial e variação de fabrico.

Para este tutorial, utilizamos o subconjunto de dados de formação de um único conjunto de dados (FD003).

Na realidade, cada motor seria um dispositivo IoT independente. Assumindo que não tem uma coleção de motores turbofan ligados à Internet disponíveis, vamos construir um suporte de software para estes dispositivos.

O simulador é um programa C# que utiliza as APIs IoT Hub para registar programáticamente dispositivos virtuais com o IoT Hub. Em seguida, lemos os dados de cada dispositivo a partir do subconjunto de dados fornecido pela NASA e enviamo-lo para o seu hub IoT usando um dispositivo IoT simulado. Todo o código para esta parte do tutorial pode ser encontrado no diretório DeviceHarness do repositório.

O projeto DeviceHarness é um projeto núcleo .NET escrito em C# composto por quatro classes:

* **Programa:** O ponto de entrada para a execução responsável pelo manuseamento da entrada do utilizador e coordenação geral.
* **FormaçãoFileManager:** Responsável pela leitura e análise do ficheiro de dados selecionado.
* **CicloData:** Representa uma única linha de dados num ficheiro convertido em formato de mensagem.
* **TurbofanDevice:** Responsável pela criação de um Dispositivo IoT, que corresponde a um único dispositivo (série de tempo), nos dados e na transmissão dos dados para o IoT Hub.

As tarefas descritas neste artigo devem demorar cerca de 20 minutos a ser concluídas.

O equivalente real ao trabalho neste passo seria provavelmente realizado por desenvolvedores de dispositivos e desenvolvedores de nuvem.

Nesta secção do tutorial, aprende-se a:

> [!div class="checklist"]
>
> * Incorpore um projeto externo no seu ambiente de desenvolvimento.
> * Utilize o projeto DeviceHarness da amostra para gerar dados simulados do dispositivo IoT.
> * Ver dados gerados no seu Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se já chegou a este artigo diretamente, visite o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configure código de estúdio visual e construa o projeto DeviceHarness

1. Abra uma sessão de desktop remota para o seu VM de desenvolvimento.

1. No código Visual Studio, abra a `C:\source\IoTEdgeAndMlSample\DeviceHarness` pasta.

1. Uma vez que está a utilizar extensões nesta máquina pela primeira vez, algumas extensões irão atualizar e instalar as suas dependências. Pode ser solicitado para atualizar a extensão. Em caso afirmativo, selecione **Reload Window**.

   Se surgirem erros da OmniSharp na janela de saída, terá de desinstalar a extensão C#.

1. Ser-lhe-á solicitado que adicione os ativos necessários para o DeviceHarness. Selecione **Sim** para adicioná-los.

   * A notificação pode demorar alguns segundos a aparecer.
   * Se perdeu esta notificação, verifique o ícone da campainha no canto inferior direito.

   ![Popup de extensão do código VS](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. **Selecione Restaurar** para restaurar as dependências do pacote.

   ![Vs Código restaurar o pedido](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Se não receber estas notificações, feche o Código do Estúdio Visual, elimine o caixote do lixo e os diretórios do OBJ `C:\source\IoTEdgeAndMlSample\DeviceHarness` em , abra o Código do Estúdio Visual e reabra a pasta DeviceHarness.

1. Valide que o seu ambiente está devidamente configurado desencadeando uma construção, **ctrl**  +  **shift**  +  **B,** ou **Terminal** Run  >  **Build Task**.

1. É-lhe pedido que selecione a tarefa de construção a executar. Selecione **Build**.

1. A construção corre e produz uma mensagem de sucesso.

   ![Construir mensagem de saída bem sucedida](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Pode fazer com que esta construa a tarefa de construção predefinida selecionando **a**  >  **Tarefa de Construção Padrão de Configuração** do Terminal... e escolhendo **Construir** a partir do pedido.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Ligue-se ao IoT Hub e executar DeviceHarness

Agora que temos o edifício do projeto, ligue-se ao seu hub IoT para aceder à cadeia de ligação e monitorizar o progresso da geração de dados.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Iniciar sessão no Azure no Visual Studio Code

1. Inscreva-se na sua subscrição Azure no Código do Estúdio Visual abrindo a paleta de comando `Ctrl + Shift + P` ou **ver** paleta  >  **de comando**.

1. Procure o **Azure: Inscreva-se no** comando.

   Uma janela do navegador abre-se e pede-lhe as suas credenciais. Quando é redirecionado para uma página de sucesso, pode fechar o navegador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Ligue-se ao seu hub IoT e recupere a cadeia de ligação do hub

1. Na secção inferior do explorador visual Studio Code, selecione a moldura **Azure IoT Hub** para expandi-la.

1. No quadro expandido, clique em **Select IoT Hub**.

1. Quando solicitado, selecione a sua subscrição Azure e, em seguida, o seu hub IoT.

1. Clique no **...** à direita do **Azure IoT Hub** para mais ações. Selecione a cadeia de **ligação Copy IoT Hub**.

   ![Cadeia de conexão IoT Hub de cópia](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Executar o projeto DeviceHarness

1. Selecione **Ver**  >  **Terminal** para abrir o terminal Visual Studio Code.

   Se não vir uma solicitação, prima Enter.

1. Introduza `dotnet run` no terminal.

1. Quando solicitado para a cadeia de ligação do hub IoT, cole a cadeia de ligação copiada na secção anterior.

1. Na moldura dos **dispositivos Azure IoT Hub,** clique no botão de atualização.

   ![Lista de dispositivos IoT Hub atualizado](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Note que os dispositivos são adicionados ao Hub IoT e que os dispositivos aparecem em verde para indicar que os dados estão a ser enviados através desse dispositivo. Depois de os dispositivos enviarem mensagens para o hub IoT, desligam-se e aparecem azuis.

1. Pode visualizar as mensagens enviadas para o centro clicando à direita em qualquer dispositivo e selecionando **Start Monitoring Built-in Event Endpoint**. As mensagens aparecerão no painel de saída no Código do Estúdio Visual.

1. Pare de monitorizar clicando no painel de saída do **Azure IoT Hub** e escolha **stop monitoring Built-in Event Endpoint**.

1. Deixe a aplicação correr até ao fim, o que leva alguns minutos.

## <a name="check-iot-hub-for-activity"></a>Verifique o IoT Hub para obter atividade

Os dados enviados pelo DeviceHarness foram para o seu hub IoT, onde pode verificar no portal Azure.

1. Abra o [portal Azure](https://portal.azure.com/) e navegue até ao hub IoT criado para este tutorial.

1. A partir do menu do painel esquerdo, em **Monitorização,** selecione **Métricas**.

1. Na página de definição de gráfico, clique na **gota métrica** para baixo, desloque a lista e selecione **Encaminhamento: dados entregues no armazenamento**. O gráfico deve mostrar o pico de quando os dados foram encaminhados para o armazenamento.

   ![Gráfico mostra pico quando os dados entregues ao armazenamento](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validar dados no Azure Storage

Os dados que acabámos de enviar para o seu hub IoT foram encaminhados para o contentor de armazenamento que criámos no artigo anterior. Vamos ver os dados na nossa conta de armazenamento.

1. No portal do Azure, navegue para a sua conta de armazenamento.

1. A partir do navegador da conta de armazenamento, selecione **Storage Explorer (pré-visualização)**.

1. No explorador de armazenamento, selecione **Containers Blob** então `devicedata` .

1. No painel de conteúdo, clique na pasta para ver o nome do hub IoT, seguido de ano, mês, dia e hora. Verá várias pastas que representam as atas quando os dados foram escritos.

   ![Ver pastas no armazenamento de bolhas](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Clique numa dessas pastas para encontrar ficheiros de dados rotulados **00** e **01** correspondentes à partição.

1. Os ficheiros estão escritos no formato [Avro.](https://avro.apache.org/) Clique duas vezes num destes ficheiros para abrir outro separador de navegador e render parcialmente os dados. Se for solicitado a abrir o ficheiro num programa, pode escolher o Código VS e renderizar-se-á corretamente.

1. Não há necessidade de tentar ler ou interpretar os dados neste momento; vamos fazê-lo no próximo artigo.

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial faz parte de um conjunto onde cada artigo baseia-se no trabalho feito nos anteriores. Por favor, espere para limpar todos os recursos até completar o tutorial final.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos um projeto .NET Core para criar um conjunto de dispositivos IoT virtuais e enviar dados através deles para o nosso hub IoT e para um recipiente de Armazenamento Azure. Este projeto simula um cenário real onde dispositivos físicos de IoT enviam dados para um Hub IoT e seguem para um armazenamento curado. Estes dados incluem leituras de sensores, configurações operacionais, sinais de falha e modos, e assim por diante. Uma vez recolhidos dados suficientes, utilizamo-lo para formar modelos que prevejam a vida útil restante (RUL) para o dispositivo. Vamos demonstrar esta aprendizagem automática no próximo artigo.

Continue até ao próximo artigo para treinar um modelo de machine learning com os dados.

> [!div class="nextstepaction"]
> [Preparar e implementar um modelo do Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
