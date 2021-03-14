---
title: 'Tutorial: Treine e implemente um modelo - Machine Learning on Azure IoT Edge'
description: Neste tutorial, você vai treinar um modelo de machine learning usando Azure Machine Learning e, em seguida, embalar o modelo como uma imagem de recipiente que pode ser implantado como um módulo Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463107"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treine e implemente um modelo de aprendizagem automática Azure

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, fazemos as seguintes tarefas:

* Use o Azure Machine Learning Studio para treinar um modelo de aprendizagem automática.
* Embale o modelo treinado como imagem de recipiente.
* Desloque a imagem do recipiente como um módulo Azure IoT Edge.

O Machine Learning Studio é um bloco fundamental usado para experimentar, treinar e implementar modelos de aprendizagem automática.

Os passos neste artigo podem ser normalmente realizados por cientistas de dados.

Nesta secção do tutorial, aprenderá a:

> [!div class="checklist"]
> * Crie cadernos Jupyter num espaço de trabalho de Aprendizagem automática Azure para treinar um modelo de aprendizagem automática.
> * Contentorize o modelo de aprendizagem de máquinas treinado.
> * Crie um módulo IoT Edge a partir do modelo de aprendizagem automática contentorizado.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre o uso de Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se chegou a este artigo diretamente, veja o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="set-up-azure-machine-learning"></a>Configurar a aprendizagem automática Azure

Usamos o Machine Learning Studio para hospedar os dois cadernos Jupyter e ficheiros de suporte. Aqui criamos e configuramos um projeto de Machine Learning. Se não usou o Jupyter ou o Machine Learning Studio, aqui estão dois documentos introdutórios:

* **Jupyter Notebook**: [Trabalhando com cadernos Jupyter no Código do Estúdio Visual](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning**: [Começa com a Azure Machine Learning em cadernos Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Após a instalação do serviço, o Machine Learning pode ser acedido a partir de qualquer máquina. Durante a configuração, deve utilizar o VM de desenvolvimento, que tem todos os ficheiros de que necessita.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Instalar extensão de código de estúdio visual de aprendizagem de máquina de azure

O Código do Estúdio Visual sobre o Desenvolvimento VM deve ter esta extensão instalada. Se estiver a executar um caso diferente, reinstale a extensão tal como descrito na [definição da extensão Do Código do Estúdio Visual](../machine-learning/tutorial-setup-vscode-extension.md).

### <a name="create-an-azure-machine-learning-account"></a>Criar uma conta de Aprendizagem automática Azure

Para obter recursos e executar cargas de trabalho no Azure, inscreva-se com as suas credenciais de conta Azure.

1. No Código do Estúdio Visual, abra a paleta de comando selecionando **a** Paleta de Comando ver  >   a partir da barra de menu.

1. Introduza o comando `Azure: Sign In` na paleta de comando para iniciar o processo de inscrição. Siga as instruções para concluir o s.a.

1. Crie uma instância computacional de Machine Learning para executar a sua carga de trabalho. Na paleta de comando, introduza o comando `Azure ML: Create Compute` .
1. Selecione a sua subscrição do Azure.
1. Selecione **+ Crie o novo espaço de trabalho Azure ML**, e insira o nome **turbofandemo**.
1. Selecione o grupo de recursos que tem usado para esta demonstração.
1. Você deve ver o progresso da criação do espaço de trabalho no canto inferior direito da sua janela Visual Studio Code: **Creating Workspace: turobofandemo**. Este passo pode levar um minuto ou dois.
1. Aguarde que o espaço de trabalho seja criado com sucesso. Deve dizer que **a Azure ML workspace turbofandemo criou.**

### <a name="upload-jupyter-notebook-files"></a>Carregar ficheiros de cadernos Jupyter

Vamos enviar ficheiros de cadernos para um novo espaço de trabalho para machine learning.

1. Vá para ml.azure.com e inscreva-se.
1. Selecione o seu diretório microsoft, subscrição Azure e o espaço de trabalho de Machine Learning recém-criado.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Screenshot que mostra a seleção do seu espaço de trabalho de aprendizagem de máquinas Azure." :::

1. Depois de ter assinado no seu espaço de trabalho machine learning, vá à secção **De Cadernos** utilizando o menu do lado esquerdo.
1. Selecione o **separador 'Os meus ficheiros'.**

1. Selecione **Upload** (o ícone de seta para cima).

1. Ir para **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os ficheiros da lista e selecione **Abrir**.

1. Selecione o **I trust the content of these files** check box.

1. Selecione **Upload** para começar a carregar. Em seguida, selecione **Feito** depois de concluído o processo.

### <a name="jupyter-notebook-files"></a>Arquivos de cadernos Jupyter

Vamos rever os ficheiros que enviaste para o teu espaço de trabalho machine learning. As atividades nesta parte do tutorial estendem-se por dois ficheiros de cadernos, que utilizam alguns ficheiros de suporte.

* **01-turbofan \_ regression.ipynb**: Este caderno utiliza o espaço de trabalho machine learning para criar e executar uma experiência de aprendizagem automática. Em termos gerais, o caderno faz os seguintes passos:

  1. Descarrega dados da conta Azure Storage que foi gerada pelo arnês do dispositivo.
  1. Explora e prepara os dados e, em seguida, utiliza os dados para treinar o modelo de classificador.
  1. Avalia o modelo a partir da experiência utilizando um conjunto de dados de teste (Test \_FD003.txt).
  1. Publica o melhor modelo classificador para o espaço de trabalho Machine Learning.

* **02 turbofan \_ deploy \_ model.ipynb**: Este caderno pega no modelo criado no caderno anterior e utiliza-o para criar uma imagem de recipiente pronta a ser implantada num dispositivo IoT Edge. O caderno executa os seguintes passos:

  1. Cria um roteiro de pontuação para o modelo.
  1. Produz uma imagem de recipiente utilizando o modelo de classificador que foi guardado no espaço de trabalho machine learning.
  1. Implementa a imagem como um serviço web em Instâncias de Contentores Azure.
  1. Utiliza o serviço web para validar o modelo e o trabalho de imagem como esperado. A imagem validada será implantada no nosso dispositivo IoT Edge no Create e implementará porção personalizada de [módulos IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Teste \_FD003.txt**: Este ficheiro contém os dados que usaremos como nosso conjunto de testes quando validarmos o nosso classificador treinado. Optámos por utilizar os dados do teste, conforme previsto no concurso original, como o nosso conjunto de testes para a sua simplicidade.
* **RUL \_FD003.txt**: Este ficheiro contém a vida útil restante (RUL) para o último ciclo de cada dispositivo no ficheiro Test \_FD003.txt. Consulte os ficheiros Modeling.pdf de propagação de readme.txt e danos em C: \\ \\ dados de IoTEdgeAndMlSample \\ \\ Turbofan para obter uma explicação detalhada dos dados.
* **Utils.py**: Este ficheiro contém um conjunto de funções de utilidade Python para trabalhar com dados. O primeiro caderno contém uma explicação detalhada das funções.
* **README.md**: Este ficheiro de readme descreve a utilização dos cadernos.

## <a name="run-the-jupyter-notebooks"></a>Executar os cadernos jupyter

Agora que o espaço de trabalho foi criado, pode executar os cadernos.

1. Na sua página **de ficheiros,** selecione **01-turbofan \_ regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Screenshot que mostra a seleção do primeiro caderno a ser executado.":::

1. Se o caderno estiver listado como **Não Confiável,** selecione o widget **"Não Confiável"** no canto superior direito do caderno. Quando a caixa de diálogo aparecer, selecione **Trust**.

1. Para obter os melhores resultados, leia a documentação para cada célula e execute-a individualmente. Selecione **Executar** na barra de ferramentas. Mais tarde, vai achar oportuno executar várias células. Pode ignorar os avisos de atualização e depreciação.

    Quando uma célula está em funcionamento, exibe um asterisco entre os suportes quadrados \* ([]). Quando o funcionamento da célula estiver concluído, o asterisco é substituído por um número e pode aparecer uma saída relevante. As células de um caderno constroem-se sequencialmente, e só uma célula pode funcionar de cada vez.

    Também pode utilizar opções de execução a partir do menu **Cell.** Selecione **Ctrl+Enter** para executar uma célula e selecione **Shift+Enter** para executar uma célula e avançar para a célula seguinte.

    > [!TIP]
    > Para operações celulares consistentes, evite executar o mesmo portátil a partir de vários separadores no seu navegador.

1. Na célula que segue as instruções **de propriedades globais definidas,** insira os valores para a sua subscrição, configurações e recursos Azure. Então, executar a cela.

    ![Screenshot que mostra a definição de propriedades globais no caderno.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Na célula anterior aos detalhes do **Workspace,** depois de ter sido executado, procure o link que o instrui a iniciar sação para autenticar.

    ![Screenshot que mostra o pedido de inscrição para a autenticação do dispositivo.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Abra o link e introduza o código especificado. Este procedimento de início de sposição autentica o portátil Jupyter para aceder aos recursos do Azure utilizando a Interface de Linha de Comando cross-Platform do Microsoft Azure.

    ![Screenshot que mostra aplicação autenticada na confirmação do dispositivo.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Na célula que precede **Explore os resultados,** copie o valor do ID de execução e cole-o para o ID de execução na célula que segue **Reconstituir uma execução**.

   ![Screenshot que mostra a cópia do ID de execução entre as células.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Executar as células restantes no caderno.

1. Guarde o caderno e volte à página do seu projeto.

1. Abra **o modelo de 02 turbofanse o \_ \_ modelo.ipynb** e execute cada célula. Terá de iniciar seduca para autenticar na célula que segue o **espaço de trabalho Configure.**

1. Guarde o caderno e volte à página do seu projeto.

### <a name="verify-success"></a>Verificar o sucesso

Para verificar se os cadernos foram concluídos com sucesso, verifique se foram criados alguns itens.

1. Nos seus cadernos machine learning **Os meus ficheiros,** selecione **refresh**.

1. Verifique se foram criados os seguintes ficheiros.

    | Ficheiro | Description |
    | --- | --- |
    | ./aml_config/.azureml/config.jsem | Ficheiro de configuração utilizado para criar o espaço de trabalho machine learning. |
    | ./aml_config/model_config.jsem | Ficheiro de configuração que teremos de implantar o modelo no espaço de trabalho de aprendizagem automática **turbofandemo** em Azure. |
    | myenv.yml| Fornece informações sobre as dependências do modelo de Machine Learning implantado.|

1. Verifique se foram criados os seguintes recursos Azure. Alguns nomes de recursos são anexados a caracteres aleatórios.

    | Recurso do Azure | Name |
    | --- | --- |
    | Área de trabalho do Azure Machine Learning | turborfanDemo |
    | Registo de Contentores do Azure | turbofandemoxxxx |
    | Application Insights | turbofaninsightxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxx |
    | Storage do Azure | turbofanstoragexxxxxxxx |

### <a name="debugging"></a>Depurar

Pode inserir declarações python no caderno para depurar, como o `print()` comando para mostrar valores. Se vir variáveis ou objetos que não estão definidos, execute as células onde são declaradas ou instantâneas pela primeira vez.

Poderá ter de eliminar ficheiros previamente criados e recursos Azure se precisar de refazer os cadernos.

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial faz parte de um conjunto onde cada artigo baseia-se no trabalho feito nos anteriores. Espere para limpar todos os recursos até completar o tutorial final.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos dois cadernos Jupyter em execução no Machine Learning Studio para usar os dados dos dispositivos turbofan para:

- Treine um classificador RUL.
- Guarde o classificado como modelo.
- Criar uma imagem de recipiente.
- Implementar e testar a imagem como um serviço web.

Continue até ao próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo Azure IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)