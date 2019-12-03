---
title: 'Tutorial: treinar e implantar um modelo-Machine Learning no Azure IoT Edge'
description: Neste tutorial, você treinará um modelo de aprendizado de máquina usando Azure Machine Learning e, em seguida, empacotará o modelo como uma imagem de contêiner que pode ser implantada como um módulo Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c0613d319c0c82fa61d75ed016d68d38dfcea8d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701831"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: treinar e implantar um modelo de Azure Machine Learning

> [!NOTE]
> Este artigo faz parte de uma série de um tutorial sobre como usar Azure Machine Learning em IoT Edge. Se você chegou a este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, usamos Azure Notebooks primeiro para treinar um modelo de aprendizado de máquina usando Azure Machine Learning e, em seguida, empacotar esse modelo como uma imagem de contêiner que pode ser implantada como um módulo Azure IoT Edge. O Azure Notebooks aproveitar um espaço de trabalho Azure Machine Learning, que é um bloco básico usado para experimentar, treinar e implantar modelos de aprendizado de máquina.

As atividades nesta parte do tutorial são divididas em dois blocos de anotações.

* **01-turbofan\_regressão. ipynb:** Este bloco de anotações percorre as etapas para treinar e publicar um modelo usando Azure Machine Learning. Em grande medida, as etapas envolvidas são:

  1. Baixe, prepare e explore os dados de treinamento
  2. Usar o espaço de trabalho de serviço para criar e executar uma experiência de aprendizado de máquina
  3. Avaliar os resultados do modelo a partir do experimento
  4. Publicar o melhor modelo no espaço de trabalho de serviço

* **02-turbofan\_implantar\_Model. ipynb:** Este bloco de anotações usa o modelo criado no bloco de anotações anterior e o usa para criar uma imagem de contêiner pronta para ser implantada em um dispositivo Azure IoT Edge.

  1. Criar um script de Pontuação para o modelo
  2. Criar e publicar a imagem
  3. Implantar a imagem como um serviço Web na instância de contêiner do Azure
  4. Usar o serviço Web para validar o modelo e a imagem funcionam conforme o esperado

As etapas neste artigo podem ser normalmente executadas por cientistas de dados.

## <a name="set-up-azure-notebooks"></a>Configurar Azure Notebooks

Usamos Azure Notebooks para hospedar os dois notebooks Jupyter e arquivos de suporte. Aqui, criamos e configuramos um projeto Azure Notebooks. Se você não usou Jupyter e/ou Azure Notebooks, aqui estão alguns documentos introdutórios:

* **Início rápido:** [criar e compartilhar um bloco de anotações](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutorial:** [criar e executar um notebook Jupyter com Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Assim como a máquina virtual do desenvolvedor antes, o uso de blocos de anotações do Azure garante um ambiente consistente para o exercício.

> [!NOTE]
> Uma vez configurado, o serviço de Azure Notebooks pode ser acessado em qualquer computador. Durante a instalação, você deve usar a máquina virtual de desenvolvimento, que tem todos os arquivos que serão necessários.

### <a name="create-an-azure-notebooks-account"></a>Criar uma conta de Azure Notebooks

As contas do bloco de anotações do Azure são independentes das assinaturas do Azure. Para usar Azure Notebooks, você precisa criar uma conta.

1. Navegue até [blocos de anotações do Azure](https://notebooks.azure.com).

2. Clique em **entrar** no canto superior direito da página.

3. Entre com sua conta corporativa ou de estudante (Azure Active Directory) ou sua conta pessoal (conta da Microsoft).

4. Se você não tiver usado Azure Notebooks antes, será solicitado a conceder acesso para o aplicativo Azure Notebooks.

5. Crie uma ID de usuário para Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Carregar arquivos do Jupyter notebooks

Nesta etapa, criamos um novo projeto Azure Notebooks e carregamos arquivos nele. Especificamente, os arquivos que carregamos são:

* **01-turbofan\_regressão. ipynb**: arquivo de notebook Jupyter que percorre o processo de download dos dados gerados pelo equipamento de dispositivo da conta de armazenamento do Azure; explorando e preparando os dados para treinar o classificador; treinando o modelo; testando os dados usando o conjunto de dado de teste encontrado no teste\_arquivo FD003. txt; e, por fim, salvar o modelo do classificador no espaço de trabalho Machine Learning serviço.

* **02-turbofan\_implantar\_Model. ipynb:** Jupyter notebook, que orienta você pelo processo de usar o modelo de classificação salvo no espaço de trabalho do serviço Machine Learning para produzir uma imagem de contêiner. Depois que a imagem for criada, o notebook o guiará pelo processo de implantação da imagem como um serviço Web para que você possa validá-la se está funcionando conforme o esperado. A imagem validada será implantada em nosso dispositivo de IoT Edge na parte [criar e implantar módulos de IOT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Teste\_FD003. txt:** Esse arquivo contém os dados que usaremos como nosso conjunto de testes ao validar nosso classificador treinado. Optamos por usar os dados de teste fornecidos para o concurso original como nosso conjunto de teste para simplificar o exemplo.

* **RUL\_FD003. txt:** Esse arquivo contém o RUL para o último ciclo de cada dispositivo no teste\_arquivo FD003. txt. Consulte os arquivos **README. txt** e **. pdf de propagação de danos** na fonte C:\\\\IoTEdgeAndMlSample\\data\\turbofan para obter uma explicação detalhada dos dados.

* **Utils.py:** Contém um conjunto de funções de utilitário Python para trabalhar com dados. O primeiro bloco de anotações contém uma explicação detalhada das funções.

* **README.MD:** Leiame que descreve o uso dos notebooks.

Crie um novo projeto e carregue os arquivos para o bloco de anotações.

1. Selecione **meus projetos** na barra de menus superior.

1. Selecione **+ novo projeto**. Forneça um nome e uma ID. Não é necessário que o projeto seja público ou tenha um Leiame.

1. Selecione **carregar** e escolha **do computador**.

1. Selecione **escolher arquivos**.

1. Navegue até **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os arquivos e clique em **abrir**.

1. Selecione **carregar** para começar a carregar e, em seguida, selecione **concluído** depois que o processo for concluído.

## <a name="run-azure-notebooks"></a>Executar Azure Notebooks

Agora que o projeto foi criado, execute **01-turbofan\_regressão. ipynb** notebook.

1. Na página do projeto turbofan, selecione **01-turbofan\_regressão. ipynb**.

    ![Selecione o primeiro bloco de anotações a ser executado](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Se solicitado, escolha o kernel Python 3,6 na caixa de diálogo e selecione **definir kernel**.

3. Se o bloco de anotações estiver listado como **não confiável**, clique no widget **não confiável** no canto superior direito do bloco de anotações. Quando a caixa de diálogo aparecer, selecione **confiar**.

4. Siga as instruções no bloco de anotações.

    * `Ctrl + Enter` executa uma célula.
    * `Shift + Enter` executa uma célula e navega para a próxima célula.
    * Quando uma célula está em execução, ela tem um asterisco entre colchetes, como **[\*]** . Quando for concluído, o asterisco será substituído por um número e uma saída relevante poderá ser exibida abaixo. Como as células geralmente se baseiam no trabalho dos anteriores, apenas uma célula pode ser executada por vez.

5. Quando terminar de executar a execução do **turbofan\_regressão. ipynb** do notebook, retorne à página do projeto.

6. Abra **02-turbofan\_implantar\_Model. ipynb** e repita as etapas nesta seção para executar o segundo bloco de anotações.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos dois blocos de anotações do Jupyter em execução no Azure Notebooks para usar os dados dos dispositivos turbofan para treinar um classificador restante da vida útil (RUL), para salvar o classificador como um modelo, para criar uma imagem de contêiner e para implantar e testar a imagem como uma Web se rvice.

Continue no próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
