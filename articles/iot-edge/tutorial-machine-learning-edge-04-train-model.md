---
title: 'Tutorial: Treine e implemente um modelo - Machine Learning on Azure IoT Edge'
description: Neste tutorial, você vai treinar um modelo de machine learning usando Azure Machine Learning e, em seguida, embalar o modelo como uma imagem de recipiente que pode ser implantado como um Módulo de Borda IoT Azure.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121153"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treine e implemente um modelo de aprendizagem automática Azure

Neste artigo, fazemos as seguintes tarefas:

* Use o Azure Machine Learning Studio para treinar um modelo de aprendizagem automática.
* Embale o modelo treinado como imagem de recipiente.
* Desloque a imagem do recipiente como um módulo Azure IoT Edge.

O Azure Machine Learning Studio é um bloco fundamental usado para experimentar, treinar e implementar modelos de aprendizagem automática.

Os passos neste artigo podem ser normalmente realizados por cientistas de dados.

Nesta secção do tutorial, aprende-se a:

> [!div class="checklist"]
> * Crie cadernos Jupyter em Azure Machine Learning Workspace para treinar um modelo de aprendizagem automática.
> * Contentorize o modelo de aprendizagem de máquinas treinado.
> * Crie um módulo Azure IoT Edge a partir do modelo de aprendizagem automática contentorizado.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se já chegou a este artigo diretamente, visite o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="set-up-azure-machine-learning"></a>Configurar a aprendizagem automática Azure 

Usamos o Azure Machine Learning Studio para acolher os dois Cadernos Jupyter e ficheiros de suporte. Aqui criamos e configuramos um projeto Azure Machine Learning. Se não usou o Jupyter e/ou Azure Machine Learning Studio, aqui estão alguns documentos introdutórios:

* **Cadernos Jupyter:** [Trabalhando com cadernos Jupyter em Código de Estúdio Visual](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Começar com Azure Machine Learning em Cadernos Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> Uma vez configurado, o serviço de Aprendizagem automática Azure pode ser acedido a partir de qualquer máquina. Durante a configuração, deverá utilizar o VM de desenvolvimento, que tem todos os ficheiros de que necessita.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Instalar extensão de código de estúdio visual de aprendizagem de máquina de azure
VS Código sobre o desenvolvimento VM deve ter esta extensão instalada. Se estiver a funcionar numa instância diferente, por favor, volte a instalar a extensão tal como descrito [aqui.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Criar uma conta de Aprendizagem automática Azure  
Para obter recursos e executar cargas de trabalho no Azure, tem de assinar com as suas credenciais de conta Azure.

1. No Código do Estúdio Visual, abra a paleta de comando selecionando **a** Paleta de Comando ver  >   a partir da barra de menu. 

1. Introduza o comando `Azure: Sign In` na paleta de comando para iniciar o sinal em processo. Siga as instruções para concluir o s.a. 

1. Crie uma instância Azure ML Compute para executar a sua carga de trabalho. A utilização da palete de comando entra no comando `Azure ML: Create Compute` . 
1. Selecione a sua subscrição do Azure
1. Selecione **+ Crie um novo espaço de trabalho Azure ML** e insira o nome `turbofandemo` .
1. Selecione o grupo de recursos que tem usado para esta demonstração.
1. Você deve ser capaz de ver o progresso da criação do espaço de trabalho no canto inferior direito da sua janela VS Code: **Criando espaço de trabalho: turobofandemo** (isto pode demorar um minuto ou dois). 
1. Por favor, aguarde que o espaço de trabalho seja criado com sucesso. Deve dizer que **a Azure ML workspace turbofandemo criou.**


### <a name="upload-jupyter-notebook-files"></a>Carregar ficheiros de cadernos Jupyter

Vamos enviar ficheiros de cadernos de amostras para um novo espaço de trabalho Azure ML.

1. Navegue para ml.azure.com e inscreva-se.
1. Selecione o seu Microsoft Directory, Azure Subscription e o recém-criado espaço de trabalho Azure ML.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Selecione o seu espaço de trabalho Azure ML." :::

1. Uma vez iniciado o seu espaço de trabalho Azure ML, navegue para a secção **DeEstas usando** o menu do lado esquerdo.
1. Selecione o **separador 'Os meus ficheiros'.**

1. Selecione **Upload** (o ícone de seta para cima) 


1. Navegue para **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os ficheiros da lista e clique em **Abrir**.

1. Verifique o conteúdo desta caixa **de ficheiros.**

1. Selecione **upload** para começar a carregar e, em seguida, selecione **Feito** uma vez que o processo esteja concluído.

### <a name="jupyter-notebook-files"></a>Arquivos de cadernos Jupyter

Vamos rever os ficheiros que enviaste para o teu espaço de trabalho Azure ML. As atividades nesta parte do tutorial estendem-se por dois ficheiros de cadernos, que utilizam alguns ficheiros de suporte.

* **01-turbofan \_ regression.ipynb:** Este caderno utiliza o espaço de trabalho de serviço machine learning para criar e executar uma experiência de aprendizagem automática. Em termos gerais, o caderno faz os seguintes passos:

  1. Descarrega dados da conta Azure Storage que foi gerada pelo arnês do dispositivo.
  1. Explora e prepara os dados, em seguida, usa os dados para treinar o modelo de classificador.
  1. Avalie o modelo a partir da experiência utilizando um conjunto de dados de teste \_ (testFD003.txt).
  1. Publica o melhor modelo classificador para o espaço de trabalho de serviço machine learning.

* **02 turbofan \_ deploy \_ model.ipynb:** Este caderno pega no modelo criado no caderno anterior e usa-o para criar uma imagem de recipiente pronta a ser implantada num dispositivo Azure IoT Edge. O caderno executa os seguintes passos:

  1. Cria um roteiro de pontuação para o modelo.
  1. Produz uma imagem de recipiente utilizando o modelo de classificador que foi guardado no espaço de trabalho de serviço machine learning.
  1. Implementa a imagem como um serviço web na Instância do Recipiente Azure.
  1. Utiliza o serviço web para validar o modelo e o trabalho de imagem como esperado. A imagem validada será implantada no nosso dispositivo IoT Edge no Create e implementará porção personalizada de [módulos IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Teste \_FD003.txt:** Este ficheiro contém os dados que usaremos como nosso conjunto de testes ao validar o nosso classificador treinado. Optámos por utilizar os dados do teste, conforme previsto no concurso original, como o nosso conjunto de testes para a sua simplicidade.

* **RUL \_FD003.txt:** Este ficheiro contém a Vida Útil Restante (RUL) para o último ciclo de cada dispositivo no ficheiro Test \_FD003.txt. Consulte os ficheiros readme.txt e propagação de danos Modeling.pdf nos ficheiros C: \\ fonte \\ IoTEdgeAndMlSample \\ dados \\ Turbofan para obter uma explicação detalhada dos dados.

* **Utils.py:** Contém um conjunto de funções de utilidade Python para trabalhar com dados. O primeiro caderno contém uma explicação detalhada das funções.

* **README.md:** Leia-me descrevendo o uso dos cadernos.  

## <a name="run-jupyter-notebooks"></a>Executar Blocos de Notas do Jupyter

Agora que o espaço de trabalho foi criado, pode executar os cadernos. 

1. Na sua página **de ficheiros,** selecione **01-turbofan \_ regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Selecione o primeiro caderno para executar. ":::

1. Se o caderno estiver listado como **Não Confiável,** clique no widget **"Não Confiável"** no direito superior do caderno. Quando o diálogo aparecer, selecione **Trust**.

1. Para obter os melhores resultados, leia a documentação para cada célula e execute-a individualmente. Selecione **Executar** na barra de ferramentas. Mais tarde, vai achar oportuno executar várias células. Pode ignorar os avisos de atualização e depreciação.

    Quando uma célula está em funcionamento, exibe um asterisco entre os suportes quadrados \* ([]). Quando o funcionamento da célula estiver concluído, o asterisco é substituído por um número e pode aparecer uma saída relevante. As células de um caderno constroem-se sequencialmente e só uma pode estar a funcionar de cada vez.

    Também pode usar opções de execução a partir do menu **Cell,** `Ctrl`  +  `Enter` para executar uma célula, e `Shift`  +  `Enter` para executar uma célula e avançar para a célula seguinte.

    > [!TIP]
    > Para operações celulares consistentes, evite executar o mesmo portátil a partir de vários separadores no seu navegador.

1. Na célula que segue as instruções **de propriedades globais definidas,** escreva os valores para a sua subscrição, configurações e recursos Azure. Então, executar a cela.

    ![Definir propriedades globais no caderno](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Na célula anterior aos detalhes do **Workspace,** depois de ter sido executado, procure o link que lhe instrui para iniciar sação para autenticar:

    ![Inscreva-se na solicitação para a autenticação do dispositivo](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Abra o link e introduza o código especificado. Este procedimento de início de sposição autentica o portátil Jupyter para aceder aos recursos do Azure utilizando a Interface de Linha de Comando cross-Platform do Microsoft Azure.  

    ![Autenticar aplicação na confirmação do dispositivo](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Na célula que precede **Explore os resultados,** copie o valor do ID de execução e cole-o para o ID de execução na célula que segue **Reconstituir uma execução**.

   ![Copiar o ID de execução entre células](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Executar as células restantes no caderno.

1. Guarde o caderno e volte à página do seu projeto.

1. Abra **o modelo de 02 turbofans \_ \_ e** corram cada célula. Terá de se inscrever para autenticar na célula que segue o **espaço de trabalho Configure**.

1. Guarde o caderno e volte à página do seu projeto.

### <a name="verify-success"></a>Verificar o sucesso

Para verificar se os cadernos foram concluídos com sucesso, verifique se foram criados alguns itens.

1. No seu Azure ML Notebooks **O separador de ficheiros,** selecione **refresh**.

1. Verifique se foram criados os seguintes ficheiros:

    | Ficheiro | Descrição |
    | --- | --- |
    | ./aml_config/.azureml/config.jsem | Ficheiro de configuração utilizado para criar o espaço de trabalho de aprendizagem da máquina Azure. |
    | ./aml_config/model_config.jsem | Ficheiro de configuração que teremos de implantar o modelo no espaço de trabalho de aprendizagem automática **turbofandemo** em Azure. |
    | myenv.yml| Fornece informações sobre as dependências do modelo de Machine Learning implantado.|

1. Verifique se foram criados os seguintes recursos Azure. Alguns nomes de recursos são anexados a caracteres aleatórios.

    | Recurso do Azure | Name |
    | --- | --- |
    | Espaço de trabalho de aprendizagem automática | turborfanDemo |
    | Container Registry | turbofandemoxx |
    | Insights de Aplicações | turbofaninsightx |
    | Key Vault | turbofankeyvaultbx |
    | Armazenamento | turbofanstoragexx |

### <a name="debugging"></a>Depurar

Pode inserir declarações python no caderno para depurar, como o `print()` comando para mostrar valores. Se vir variáveis ou objetos que não estejam definidos, execute as células onde são primeiramente declaradas ou instantâneas.

Poderá ter de eliminar ficheiros previamente criados e recursos Azure se precisar de refazer os cadernos.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto onde cada artigo baseia-se no trabalho feito nos anteriores. Por favor, espere para limpar todos os recursos até completar o tutorial final.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos dois Jupyter Notebooks em execução no Azure ML Studio para usar os dados dos dispositivos turbofan para treinar uma vida útil restante (RUL) classificador, para salvar o classificador como modelo, para criar uma imagem de recipiente, e para implementar e testar a imagem como um serviço web.

Continue até ao próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo Azure IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)