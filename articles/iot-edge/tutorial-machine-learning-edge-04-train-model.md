---
title: Preparar e implementar um modelo - Machine Learning no Azure IoT Edge | Documentos da Microsoft
description: Preparar um modelo de aprendizagem automática com o Azure Machine Learning e, em seguida, a empacotar o modelo como uma imagem de contentor que pode ser implementado como um módulo do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: af91dc09f8ed68d7bd4f2378c13d99eb67c52e9e
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155637"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Preparar e implementar um modelo do Azure Machine Learning

> [!NOTE]
> Este artigo faz parte de uma série de para um tutorial sobre como utilizar o Azure Machine Learning do IoT Edge. Se o ter chegado neste artigo diretamente, é recomendável que começa com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter melhores resultados.

Neste artigo, utilizamos blocos de notas do Azure pela primeira vez para formar um modelo de machine learning com o Azure Machine Learning e, em seguida, a empacotar esse modelo como uma imagem de contentor que pode ser implementado como um módulo do Azure IoT Edge. Os blocos de notas do Azure tire partido de um trabalho de serviço do Azure Machine Learning, que é um bloco fundamental usado para testar, preparar e implementar modelos de aprendizagem automática.

As atividades nessa parte do tutorial são divididas em dois blocos de notas.

* **01-turbofan\_regression.ipynb:** Este bloco de notas explica os passos para formar e publicar um modelo com o Azure Machine Learning. De forma ampla, os passos envolvidos são:

  1. Transferir, preparar e explorar os dados de treinamento
  2. Utilize a área de trabalho do serviço para criar e executar uma experimentação do machine learning
  3. Avaliar os resultados de modelo de experimentação
  4. Publicar o melhor modelo para a área de trabalho do serviço

* **02-turbofan\_deploy\_model.ipynb:** Este bloco de notas usa o modelo criado no bloco de notas anterior e utiliza-o para criar uma imagem de contentor pronta para ser implementada para um dispositivo Azure IoT Edge.

  1. Criar um script de classificação para o modelo
  2. Criar e publicar a imagem
  3. Implementar a imagem como um serviço web na instância de contentor do Azure
  4. Utilizar o serviço da web para validar o modelo e o trabalho de imagem, conforme o esperado

Os passos neste artigo podem ser, normalmente realizados por cientistas de dados.

## <a name="set-up-azure-notebooks"></a>Configurar blocos de notas do Azure

Podemos usar blocos de notas do Azure para alojar os dois blocos de notas do Jupyter e ficheiros de suporte. Aqui criamos e configurar um projeto de blocos de notas do Azure. Se não tiver utilizado o Jupyter e/ou blocos de notas do Azure, aqui estão alguns documentos de introdução:

* **Início rápido:** [Criar e partilhar um bloco de notas](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutorial:** [Criar e executar um bloco de notas do Jupyter com Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Como com a máquina virtual de desenvolvedor antes, a utilização de blocos de notas do Azure assegura um ambiente consistente para o exercício.

> [!NOTE]
> Uma vez configurado, o serviço de blocos de notas do Azure pode ser acedido em qualquer máquina. Durante a configuração, deve utilizar a máquina virtual de desenvolvimento, que tem todos os ficheiros que irá precisar.

### <a name="create-an-azure-notebooks-account"></a>Criar uma conta de blocos de notas do Azure

Contas de bloco de notas do Azure são independentes entre subscrições do Azure. Para utilizar blocos de notas do Azure, terá de criar uma conta.

1. Navegue para [blocos de notas do Azure](http://notebooks.azure.com).

2. Clique em **sessão** no canto superior, à direita da página.

3. Inicie sessão com o seu trabalho ou a conta escolar (Azure Active Directory) ou a sua conta pessoal (Account Microsoft).

4. Se não tiver utilizado a blocos de notas do Azure antes, será solicitado a conceder acesso para a aplicação de blocos de notas do Azure.

5. Crie um ID de utilizador para blocos de notas do Azure.

### <a name="upload-jupyter-notebooks-files"></a>Carregar ficheiros de blocos de notas do Jupyter

Neste passo, vamos criar um novo projeto de blocos de notas do Azure e carregar ficheiros para o mesmo. Especificamente, os ficheiros que vamos carregar são:

* **01-turbofan\_regression.ipynb**: Ficheiro de bloco de notas do Jupyter que explica o processo de transferência de dados gerados pelo agente de dispositivo da conta de armazenamento do Azure; explorar e preparar os dados para treinar o classificador; preparar o modelo; teste os dados usando o conjunto de dados de teste encontradas no teste\_FD003.txt ficheiro; e, finalmente a guardar o classificador de modelo na área de trabalho de serviço de Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** Notas do Jupyter que o orienta ao longo do processo de usar o modelo de classificador guardado na área de trabalho de serviço de Machine Learning para produzir uma imagem de contentor. Depois da imagem é criada, as movimentações de bloco de notas pelo processo de implantação da imagem como um serviço web para que pode validar está a funcionar como esperado. A imagem validada será implementada em nosso dispositivo do Edge no [criar e implementar módulos personalizados do IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) parte deste tutorial.

* **Test\_FD003.txt:** Este ficheiro contém os dados que irá utilizar como nosso teste definido quando a validar nossas classificador treinado. Optamos por usar os dados de teste conforme fornecido para o concurso original como nosso teste definido para manter a simplicidade do exemplo.

* **RUL\_FD003.txt:** Este ficheiro contém a RUL para o último ciclo de cada dispositivo no teste\_FD003.txt ficheiro. Consulte a **Readme. txt** e o **Modeling.pdf de propagação de danos** ficheiros na unidade c:\\origem\\IoTEdgeAndMlSample\\dados\\Turbofan para um explicação detalhada dos dados.

* **Utils.py:** Contém um conjunto de funções de utilitário de Python para trabalhar com dados. O primeiro bloco de anotações contém uma explicação detalhada das funções.

* **README.md:** Leia-me que descreve a utilização dos blocos de notas.

Criar um novo projeto e carregue os ficheiros para o bloco de notas.

1. Selecione **meus projetos** da barra de menus superior.

1. Selecione **+ novo projeto**. Forneça um nome e um ID. Não é necessário para o projeto para ser público ou ter um ficheiro Leia-me.

1. Selecione **carregue** e escolha **de computador**.

1. Selecione **escolher ficheiros**.

1. Navegue para **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os ficheiros e clique em **aberto**.

1. Selecione **carregue** para começar a carregar e, em seguida, selecione **feito** uma vez concluído o processo.

## <a name="run-azure-notebooks"></a>Execute blocos de notas do Azure

Agora que o projeto estiver criado, execute o **01 turbofan\_regression.ipynb** bloco de notas.

1. Na página de projeto turbofan, selecione **01 turbofan\_regression.ipynb**.

    ![Selecione o primeiro bloco de notas para executar](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Se lhe for pedido, escolha o Kernel do Python 3.6 na caixa de diálogo e selecione **Kernel definir**.

3. Se o bloco de notas é listado como **não fidedigno**, clique nas **não fidedigno** widget no canto superior direito do bloco de notas. Quando a caixa de diálogo é exibido, selecione **confiar**.

4. Siga as instruções no bloco de notas.

    * `Ctrl + Enter` executa uma célula.
    * `Shift + Enter` executa uma célula e navega para a próxima célula.
    * Quando uma célula está em execução, ele tem um asterisco entre parênteses Retos, como **[\*]** . Quando estiver concluída, o asterisco será substituído por um número e saída relevante pode ser apresentados abaixo. Uma vez que as células dependem muitas vezes, o trabalho das, pode executar somente uma célula de cada vez.

5. Quando terminar de executar o **01 turbofan\_regression.ipynb** bloco de notas, retorno para a página do projeto.

6. Open **02-turbofan\_implementar\_model.ipynb** e repita os passos nesta secção para executar o segundo bloco de notas.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, nós usamos dois blocos de notas Jupyter em execução em blocos de notas do Azure para utilizar os dados dos dispositivos turbofan para treinar um classificador de vida útil (RUL) restantes, para guardar o classificador de como um modelo, para criar uma imagem de contentor e para implementar e testar a imagem como um se web rvice.

Avance para o artigo seguinte para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
