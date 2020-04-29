---
title: 'Tutorial: Treine e implante um modelo - Machine Learning on Azure IoT Edge'
description: Neste tutorial, você irá treinar um modelo de machine learning usando O Machine Learning Azure e, em seguida, embalar o modelo como uma imagem de recipiente que pode ser implantado como um Módulo de Borda Azure IoT.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80236028"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treine e implante um modelo de Aprendizagem automática Azure

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre a utilização de Machine Learning Azure em IoT Edge. Se chegou diretamente a este artigo, encorajamo-lo a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para obter os melhores resultados.

Neste artigo, fazemos as seguintes tarefas:

* Utilize cadernos Azure para formar um modelo de aprendizagem automática.
* Embala o modelo treinado como imagem de recipiente.
* Implante a imagem do recipiente como um módulo Azure IoT Edge.

Os Cadernos Azure aproveitam um espaço de trabalho Azure Machine Learning, um bloco fundamental usado para experimentar, treinar e implementar modelos de aprendizagem automática.

Os passos neste artigo podem ser normalmente realizados por cientistas de dados.

## <a name="set-up-azure-notebooks"></a>Configurar cadernos Azure

Utilizamos cadernos Azure para acolher os dois Cadernos Jupyter e ficheiros de apoio. Aqui criamos e configuramos um projeto De Cadernos Azure. Se não usou os cadernos Jupyter e/ou Azure, aqui estão alguns documentos introdutórios:

* **Quickstart:** [Criar e partilhar um caderno](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutorial:** [Crie e execute um caderno Jupyter com Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

A utilização de cadernos Azure garante um ambiente consistente para o exercício.

> [!NOTE]
> Uma vez configurado, o serviço Depornais Azure pode ser acedido a partir de qualquer máquina. Durante a configuração, deve utilizar o VM de desenvolvimento, que tem todos os ficheiros de que necessita.

### <a name="create-an-azure-notebooks-account"></a>Criar uma conta de Cadernos Azure

Para utilizar os Cadernos Azure, é necessário criar uma conta. As contas do Azure Notebook são independentes das subscrições do Azure.

1. Navegue para [os cadernos Azure.](https://notebooks.azure.com)

1. Clique em **Iniciar sessão** no canto superior direito da página.

1. Inscreva-se na sua conta de trabalho ou escolar (Diretório Ativo Azure) ou na sua conta pessoal (Conta Microsoft).

1. Se não tiver usado os Cadernos Azure antes, será solicitado a conceder acesso à aplicação Azure Notebooks.

1. Crie uma identificação de utilizador para os Cadernos Azure.

### <a name="upload-jupyter-notebook-files"></a>Upload de ficheiros de caderno jupyter

Enviaremos ficheiros de cadernos de amostras para um novo projeto de Cadernos Azure.

1. A partir da página de utilizador da sua nova conta, selecione **My Projects** a partir da barra de menu seletiva.

1. Adicione um novo projeto **+** selecionando o botão.

1. Na caixa de diálogo **Create New Project,** forneça um Nome de **Projeto**. 

1. Deixe **o Público** e a **README** descontrolados, uma vez que não há necessidade de o projeto ser público ou ter um readme.

1. Selecione **Criar**.

1. **Selecione Upload** (o ícone da seta para cima) e escolha **Entre O Computador**.

1. Selecione **Escolher ficheiros**.

1. Navegue para **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os ficheiros da lista e clique em **Abrir**.

1. Verifique o **conteúdo destacaixa de ficheiros.**

1. Selecione **Carregar** para começar a carregar e, em seguida, selecione **Feito** assim que o processo estiver concluído.

### <a name="azure-notebook-files"></a>Ficheiros de cadernos Azure

Vamos rever os ficheiros que fez no seu projeto Decadernos Azure. As atividades nesta parte do tutorial estendem-se por dois ficheiros de cadernos, que utilizam alguns ficheiros de suporte.

* **01-turbofan\_regressão.ipynb:** Este caderno utiliza o espaço de trabalho do serviço machine learning para criar e executar uma experiência de aprendizagem automática. Em termos gerais, o caderno faz os seguintes passos:

  1. Descarrega dados da conta De armazenamento azure que foi gerado pelo arnês do dispositivo.
  1. Explora e prepara os dados, depois utiliza os dados para treinar o modelo de classificação.
  1. Avalie o modelo a partir da\_experiência utilizando um conjunto de dados de teste (Test FD003.txt).
  1. Publica o melhor modelo de classificação para o espaço de trabalho do serviço de Aprendizagem automática.

* **02-turbofan\_\_deploy model.ipynb:** Este caderno pega no modelo criado no caderno anterior e usa-o para criar uma imagem de contentor pronta a ser implantada para um dispositivo Azure IoT Edge. O caderno executa os seguintes passos:

  1. Cria um roteiro de pontuação para o modelo.
  1. Produz uma imagem de recipiente utilizando o modelo de classificação que foi guardado no espaço de trabalho do serviço de Aprendizagem Automática.
  1. Implanta a imagem como um serviço web em Azure Container Instance.
  1. Usa o serviço web para validar o modelo e o trabalho de imagem como esperado. A imagem validada será implantada para o nosso dispositivo IoT Edge na [parte de Módulos Create e implementará módulos IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Teste\_FD003.txt:** Este ficheiro contém os dados que usaremos como o nosso conjunto de testes ao validar o nosso classificador treinado. Optámos por utilizar os dados do teste, conforme previsto para o concurso original, como o nosso teste definido para a sua simplicidade.

* **RUL\_FD003.txt:** Este ficheiro contém a Vida Útil Remanescente (RUL)\_para o último ciclo de cada dispositivo no ficheiro FD003.txt test. Consulte os ficheiros readme.txt e da propagação de\\\\danos Modeling.pdf\\no\\C: fonte IoTEdgeAndMlSample dados Turbofan para obter uma explicação detalhada dos dados.

* **Utils.py:** Contém um conjunto de funções de utilidade python para trabalhar com dados. O primeiro caderno contém uma explicação detalhada das funções.

* **README.md:** Readme descrevendo o uso dos cadernos.  

## <a name="run-azure-notebooks"></a>Executar cadernos Azure

Agora que o projeto é criado, pode sondar os cadernos. 

1. Na sua página do projeto, selecione **regressão de\_01-turbof.ipynb**.

    ![Selecione o primeiro caderno para executar](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Se o caderno estiver listado como **Não Confiável,** clique no widget **Não Confiável** no direito superior do caderno. Quando o diálogo aparecer, selecione **Trust**.

1. Para obter os melhores resultados, leia a documentação para cada célula e execute-a individualmente. Selecione **Executar** na barra de ferramentas. Mais tarde, vai achar oportuno executar várias células. Pode ignorar avisos de atualização e depreciação.

    Quando uma célula está em funcionamento, apresenta um asterisco entre os suportes quadrados ([]).\* Quando o funcionamento da célula estiver concluído, o asterisco é substituído por um número e pode aparecer uma saída relevante. As células de um caderno constroem sequencialmente e só uma pode estar a funcionar de cada vez.

    Também pode usar opções de `Ctrl`  +  `Enter` execução a partir `Shift`  +  `Enter` do menu **Cell,** para executar uma célula, e para executar uma célula e avançar para a próxima célula.

    > [!TIP]
    > Para operações celulares consistentes, evite executar o mesmo caderno a partir de vários separadores no seu navegador.

1. Na célula que segue as instruções de **propriedades globais do set,** escreva nos valores para a sua subscrição Azure, configurações e recursos. Então, gerea cela.

    ![Definir propriedades globais no caderno](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Na célula anterior aos detalhes do **Workspace,** depois de ter sido executada, procure o link que lhe instrua a iniciar sessão para autenticar:

    ![Inscreva-se em solicitação para autenticação do dispositivo](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Abra o link e introduza o código especificado. Este procedimento de entrada autentica o caderno Jupyter para aceder aos recursos do Azure utilizando a Interface de Linha de Comando de Plataformas Cruzadas Microsoft Azure.  

    ![Autenticar aplicação na confirmação do dispositivo](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Na célula que precede **Explore os resultados,** copie o valor do ID de execução e cole-o para o ID de execução na célula que segue **Reconstituir uma execução**.

   ![Copiar o ID de execução entre células](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Execute as células restantes no caderno.

1. Guarde o caderno e volte à sua página do projeto.

1. Abra **o modelo.ipynb de\_implantação\_02-turbofan** e executar cada célula. Terá de iniciar sessão para autenticar na célula que segue o espaço de **trabalho configure**.

1. Guarde o caderno e volte à sua página do projeto.

### <a name="verify-success"></a>Verificar o sucesso

Para verificar se os cadernos foram concluídos com sucesso, verifique se foram criados alguns itens.

1. Na página do projeto Do seu Projeto De Cadernos Azure, selecione **itens escondidos** para que os nomes de itens que começam com um período apareçam.

1. Verifique se foram criados os seguintes ficheiros:

    | Ficheiro | Descrição |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Ficheiro de configuração utilizado para criar o espaço de trabalho de aprendizagem automática Azure. |
    | ./aml_config/model_config.json | Ficheiro de configuração que teremos de implementar o modelo no espaço de trabalho **turbofanDemo** Machine Learning em Azure. |
    | myenv.yml| Fornece informações sobre as dependências do modelo de Machine Learning implantado.|

1. Verifique se foram criados os seguintes recursos Azure. Alguns nomes de recursos são anexados com caracteres aleatórios.

    | Recurso Azure | Nome |
    | --- | --- |
    | Espaço de trabalho de aprendizagem automática | turborfanDemo |
    | Registo de Contentor | turbofandemoxxxxxxxx |
    | Insights de Aplicações | turbofaninsightxxxxxxxxx |
    | Cofre de Chaves | turbofankeyvaultbxxxxxxxx |
    | Armazenamento | turbofanstoragexxxxxxxxxxxx |

### <a name="debugging"></a>Depurar

Pode inserir declarações de Python no caderno para `print()` depuração, como o comando para mostrar valores. Se vir variáveis ou objetos que não estejam definidos, corra as células onde são declaradas ou instantâneas.

Poderá ter de eliminar ficheiros previamente criados e recursos Azure se precisar de refazer os cadernos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, utilizámos dois Cadernos Jupyter em Execução em Cadernos Azure para usar os dados dos dispositivos turbofan para treinar uma restante classe útil de vida (RUL), para salvar o classificador como modelo, para criar uma imagem de contentor, e para implantar e testar a imagem como um serviço web.

Continue para o próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configure um dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
