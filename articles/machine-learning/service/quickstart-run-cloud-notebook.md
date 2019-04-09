---
title: Início rápido executar um bloco de notas na cloud
titleSuffix: Azure Machine Learning service
description: Introdução ao serviço Azure Machine Learning. Utilize um servidor gerido na cloud para experimentar a sua área de trabalho.  A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9cd643185fb4647b19082980edfd333c507aab8a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266261"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Início rápido: Utilizar um servidor de bloco de notas com base na cloud para começar a utilizar com o Azure Machine Learning

Criar um servidor de bloco de notas com base na cloud, em seguida, utilizá-lo para executar o código que registra em log valores no serviço do Azure Machine Learning [área de trabalho](concept-azure-machine-learning-architecture.md). A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática com o Machine Learning. 

Este início rápido mostra como criar um recurso da nuvem na sua área de trabalho do Azure Machine Learning, configurado com o ambiente do Python necessário para executar o Azure Machine Learning. Para utilizar em vez disso, o seu próprio ambiente, consulte [início rápido: Utilizar o seu próprio servidor de bloco de notas para começar a utilizar com o Azure Machine Learning](quickstart-run-local-notebook.md).  
 
Neste início rápido, efetuar as seguintes ações:

* Criar uma estação de trabalho
* Iniciar um servidor de bloco de notas do Jupyter na estação de trabalho
* Abra um bloco de notas que contém o código para calcular o instalador de plataforma e registos de erros em cada iteração.
* Execute o bloco de notas.
* Ver os valores de erro com sessão iniciada na sua área de trabalho.  Este exemplo mostra como a área de trabalho pode ajudá-lo a manter o controlo das informações geradas num script. 

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar uma área de trabalho do Azure Machine Learning](setup-create-workspace.md#portal) se não tiver uma.

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  Se não tem a certeza de como localizar a sua área de trabalho no portal, consulte como [encontrar a sua área de trabalho](how-to-manage-workspace.md#view).

## <a name="create-a-workstation"></a>Criar uma estação de trabalho 

Uma estação de trabalho do bloco de notas oferece uma plataforma com base na cloud para blocos de notas do Jupyter que é pré-configurado com tudo o que precisa para executar o serviço Azure Machine Learning. Na área de trabalho, pode criar esta plataforma para começar a utilizar blocos de notas do Jupyter.

1. Na sua página de área de trabalho no portal do Azure, selecione **estação de trabalho do bloco de notas** à esquerda.

1. Selecione **criar blocos de notas na estação de trabalho do Azure Machine Learning (pré-visualização)**

   ![Explore a área de trabalho](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. O **estações de trabalho do bloco de notas** seção mostra uma lista de todos os servidores de bloco de notas com base na cloud disponíveis, em sua área de trabalho.  A partir daqui também pode gerir estes recursos e eliminá-las quando já não são necessários. 

1. Selecione **estação de trabalho adicionar** para criar uma estação de trabalho do bloco de notas.

     ![Selecione Adicionar estação de trabalho](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Na secção de adicionar a estação de trabalho de bloco de notas, dar a sua estação de trabalho uma **nome de computação** e selecione um **tipo de computação**. Em seguida, selecione **Criar**.

    ![Criar nova estação de trabalho](media/quickstart-run-cloud-notebook/create-new-workstation.png)

    > [!NOTE]
    > A estação de trabalho leva aproximadamente dois minutos para criar. Quando terminar, as atualizações de estado "Em execução" e links para Jupyter e JupyterLab aparecem.

## <a name="launch-jupyter-web-interface"></a>Iniciar a interface de web do Jupyter

Depois de criar a sua estação de trabalho, utilize a secção de estações de trabalho do bloco de notas para iniciar a interface de web do Jupyter.

* Selecione **Jupyter** ou **Jupyter laboratório** no **iniciar** coluna para a estação de trabalho.

    ![Iniciar o servidor de bloco de notas do Jupyter](./media/quickstart-run-cloud-notebook/start-notebook-server.png)

    Isso inicia o servidor de bloco de notas e abre a página inicial do servidor num novo separador do browser.  O servidor mostra a blocos de notas de exemplo que pode utilizar para começar a utilizar o serviço Azure Machine Learning.

### <a name="run-the-notebook"></a>Executar o bloco de notas

Execute um bloco de notas que as estimativas de instalador de plataforma e registra o erro em sua área de trabalho.

1. Selecione **01.run experiment.ipynb** para abrir o bloco de notas.

1. A área de status informa ao aguardar até que o kernel foi iniciado.  A mensagem desaparecerá depois do kernel está pronto.

    ![Aguarde de kernel começar](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Depois de iniciado o kernel, execute as células um a um momento, utilizando **Shift + Enter**. Ou selecione **células** > **executar todos** para executar o bloco de notas completo. Quando vir um asterisco (__*__) ao lado de uma célula, a célula ainda está em execução. Após o código para essa célula terminar, é apresentado um número.  

Depois de terminar a executar todas as células no bloco de notas, pode ver os valores com sessão iniciada na sua área de trabalho.

## <a name="view-logged-values"></a>Ver valores registados

1. A saída do `run` célula contém uma ligação para o portal do Azure para ver os resultados da experimentação na sua área de trabalho. 

    ![Ver experimentações](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Clique nas **ligação para o portal do Azure** para ver informações sobre a execução na sua área de trabalho.  Esta ligação abre-se a sua área de trabalho no portal do Azure.

1. Os gráficos de valores com sessão iniciada, que verá automaticamente foram criados na área de trabalho. Sempre que registar vários valores com o mesmo parâmetro de nome, é automaticamente gerado um desenho.

   ![Ver histórico](./media/quickstart-run-cloud-notebook/web-results.png)

Uma vez que o código para pi aproximado utiliza valores aleatórios, seus gráficos irão mostrar valores diferentes.  

## <a name="clean-up-resources"></a>Limpar recursos 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Também pode manter o grupo de recursos mas eliminar uma área de trabalho única. Apresentar as propriedades de área de trabalho e selecione **eliminar**.


## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, concluiu o seguinte:

* Criar uma estação de trabalho
* Iniciar um servidor de bloco de notas do Jupyter na estação de trabalho
* Abra um bloco de notas que contém o código para calcular o instalador de plataforma e registos de erros em cada iteração.
* Execute o bloco de notas.
* Ver os valores de erro com sessão iniciada na sua área de trabalho.  Este exemplo mostra como a área de trabalho pode ajudá-lo a manter o controlo das informações geradas num script. 

Para obter uma experiência de fluxo de trabalho detalhados, siga os tutoriais de Machine Learning para preparar e implementar um modelo:  

> [!div class="nextstepaction"]
> [Tutorial: Preparar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)
