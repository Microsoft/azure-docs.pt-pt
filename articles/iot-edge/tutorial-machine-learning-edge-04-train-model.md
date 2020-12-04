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
ms.openlocfilehash: 8efc0301296f64abfe9b827e943882e930929086
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575334"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treine e implemente um modelo de aprendizagem automática Azure

Neste artigo, fazemos as seguintes tarefas:

* Use cadernos Azure para treinar um modelo de aprendizagem automática.
* Embale o modelo treinado como imagem de recipiente.
* Desloque a imagem do recipiente como um módulo Azure IoT Edge.

Os Cadernos Azure aproveitam um espaço de trabalho Azure Machine Learning, um bloco fundamental usado para experimentar, treinar e implementar modelos de aprendizagem automática.

Os passos neste artigo podem ser normalmente realizados por cientistas de dados.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre a utilização de Azure Machine Learning em IoT Edge. Cada artigo da série baseia-se no trabalho no artigo anterior. Se já chegou a este artigo diretamente, visite o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="set-up-azure-notebooks"></a>Configurar cadernos Azure

Usamos cadernos Azure para hospedar os dois Cadernos Jupyter e ficheiros de suporte. Aqui criamos e configuramos um projeto Azure Notebooks. Se não usou Os Cadernos Jupyter e/ou Azure, aqui estão alguns documentos introdutórios:

* **Quickstart:** [Criar e partilhar um caderno](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutorial:** [Criar e executar um caderno Jupyter com Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

A utilização de Cadernos Azure garante um ambiente consistente para o exercício.

> [!NOTE]
> Uma vez configurado, o serviço Azure Notebooks pode ser acedido a partir de qualquer máquina. Durante a configuração, deverá utilizar o VM de desenvolvimento, que tem todos os ficheiros de que necessita.

### <a name="create-an-azure-notebooks-account"></a>Criar uma conta de Cadernos Azure

Para utilizar os Cadernos Azure, é necessário criar uma conta. As contas do Azure Notebook são independentes das subscrições da Azure.

1. Navegue para [Azure Notebooks](https://notebooks.azure.com).

1. Clique **em Iniciar s nota** no canto superior direito da página.

1. Faça sedida com o seu trabalho ou conta escolar (Azure Ative Directory) ou com a sua conta pessoal (Conta Microsoft).

1. Se ainda não ter usado Os Cadernos Azure, será solicitado que tenha acesso à aplicação Azure Notebooks.

1. Crie um ID de utilizador para Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Carregar ficheiros de cadernos Jupyter

Vamos enviar ficheiros de cadernos de amostras para um novo projeto Azure Notebooks.

1. Na página de utilizador da sua nova conta, selecione **My Projects** a partir da barra de menu superior.

1. Adicione um novo projeto selecionando o **+** botão.

1. Na caixa de diálogo **Create New Project,** forneça um **Nome de Projeto.** 

1. Deixe **o Público** e a **README** descontrolados, uma vez que não há necessidade de o projeto ser público ou de ter uma leitura.

1. Selecione **Criar**.

1. Selecione **Upload** (o ícone de seta para cima) e escolha **De Computador**.

1. **Selecione Escolha ficheiros**.

1. Navegue para **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os ficheiros da lista e clique em **Abrir**.

1. Verifique o conteúdo desta caixa **de ficheiros.**

1. Selecione **upload** para começar a carregar e, em seguida, selecione **Feito** uma vez que o processo esteja concluído.

### <a name="azure-notebook-files"></a>Ficheiros Azure Notebook

Vamos rever os ficheiros que enviaste para o teu projeto Azure Notebooks. As atividades nesta parte do tutorial estendem-se por dois ficheiros de cadernos, que utilizam alguns ficheiros de suporte.

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

## <a name="run-azure-notebooks"></a>Executar cadernos Azure

Agora que o projeto foi criado, pode executar os cadernos. 

1. Na sua página do projeto, selecione **01-turbofan \_ regression.ipynb**.

    ![Selecione o primeiro caderno para executar](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

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

1. Na sua página de projeto Azure Notebooks, **selecione Mostrar itens ocultos** para que os nomes dos itens que comecem com um período apareçam.

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
    | Registo de Contentor | turbofandemoxxxxxxxxxxxxx |
    | Insights de Aplicações | turbofaninsightxxxxxxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxxxxxxxxx |
    | Armazenamento | turbofanstoragexxxxxxxxxxxxxxx |

### <a name="debugging"></a>Depurar

Pode inserir declarações python no caderno para depurar, como o `print()` comando para mostrar valores. Se vir variáveis ou objetos que não estejam definidos, execute as células onde são primeiramente declaradas ou instantâneas.

Poderá ter de eliminar ficheiros previamente criados e recursos Azure se precisar de refazer os cadernos.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto onde cada artigo baseia-se no trabalho feito nos anteriores. Por favor, espere para limpar todos os recursos até completar o tutorial final.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, usamos dois Cadernos Jupyter em execução em Cadernos Azure para usar os dados dos dispositivos turbofanos para treinar uma vida útil restante (RUL), para salvar o classificador como modelo, para criar uma imagem de recipiente, e para implantar e testar a imagem como um serviço web.

Continue até ao próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo Azure IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
