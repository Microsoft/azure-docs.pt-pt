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
ms.openlocfilehash: 761ecefa768914e0c27f46d37050743e4f1f60ff
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362440"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Início rápido: Utilizar um servidor de bloco de notas com base na cloud para começar a utilizar com o Azure Machine Learning

Neste artigo, vai utilizar blocos de notas do Azure para executar o código que é registado no serviço do Azure Machine Learning [área de trabalho](concept-azure-machine-learning-architecture.md). A área de trabalho é o bloco fundamental na cloud que utilizar para testar, preparar e implementar modelos de aprendizagem automática com o Machine Learning. 

Este início rápido utiliza recursos na cloud e não requer qualquer instalação. Para utilizar em vez disso, o seu próprio ambiente, consulte [início rápido: Utilizar o seu próprio servidor de bloco de notas para começar a utilizar com o Azure Machine Learning](quickstart-run-local-notebook.md).  
 
Neste início rápido, efetuar as seguintes ações:

* Ligar à sua área de trabalho com o Python num bloco de notas do Jupyter. O bloco de anotações contém código para calcular o instalador de plataforma e registos de erros em cada iteração. 
* Ver os valores de erro com sessão iniciada na sua área de trabalho.

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="prerequisite"></a>Pré-requisito

1. [Criar uma área de trabalho do Azure Machine Learning](setup-create-workspace.md#portal) se não tiver uma.

1. Abra a área de trabalho do [portal do Azure](https://portal.azure.com/).  Veja como [encontrar a sua área de trabalho](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Utilizar a sua área de trabalho

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Saiba como uma área de trabalho ajuda-o a gerir os seus scripts do machine learning. Nesta secção, siga os passos seguintes:

* Abrir um bloco de notas no Azure Notebooks.
* Executar código que cria alguns valores registados.
* Ver os valores registados na área de trabalho.

Este exemplo mostra como a área de trabalho pode ajudá-lo a manter o controlo das informações geradas num script. 

### <a name="open-a-notebook"></a>Abrir um bloco de notas 

[Blocos de notas do Azure](https://notebooks.azure.com) fornece uma plataforma de cloud gratuitos para blocos de notas do Jupyter que é pré-configurado com tudo o que precisa para executar o Machine Learning. Na área de trabalho, pode iniciar esta plataforma para começar a utilizar a sua área de trabalho do serviço do Azure Machine Learning.

1. Na página de descrição geral de área de trabalho, selecione **introdução ao Azure blocos de notas** para experimentar a sua primeira experiência em blocos de notas do Azure.  Blocos de notas do Azure é um serviço separado, que permite que execute blocos de notas Jupyter gratuitamente na cloud.  Quando utiliza esta ligação para o serviço, informações sobre como ligar à sua área de trabalho serão adicionadas à biblioteca de que criar em blocos de notas do Azure.

   ![Explore a área de trabalho](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Inicie sessão em blocos de notas do Azure.  Certifique-se de que iniciar sessão com a mesma conta utilizada para iniciar sessão no portal do Azure. A sua organização poderá precisar de [consentimento de administrador](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent) antes de poder iniciar sessão.

1. Depois de iniciar sessão, é aberto um novo separador e um aviso `Clone Library` é apresentado. Clonar esta biblioteca carregará um conjunto de blocos de notas e outros ficheiros na sua conta de blocos de notas do Azure.  Estes ficheiros ajudam a explorar os recursos do Azure Machine Learning.

1. Desmarque **público** para que não a partilhar as informações da sua área de trabalho com outras pessoas.

1. Selecione **Clone**.

   ![Clonar uma biblioteca](./media/quickstart-run-cloud-notebook/clone.png)

1. Se vir que o status do projeto é parado, clique em **executado no computador livre** para utilizar o servidor de bloco de notas gratuito.

    ![Executar um projeto no cálculo gratuita](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>Executar o bloco de notas

Na lista de ficheiros para este projeto, verá um `config.json` ficheiro. Este ficheiro de configuração contém informações sobre a área de trabalho que criou no portal do Azure.  Este ficheiro permite que seu código ligar a e adicione informações na área de trabalho.

1. Selecione **01.run experiment.ipynb** para abrir o bloco de notas.

1. A área de status informa ao aguardar até que o kernel foi iniciado.  A mensagem desaparecerá depois do kernel está pronto.

    ![Aguarde de kernel começar](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Depois de iniciado o kernel, execute as células um a um momento, utilizando **Shift + Enter**. Ou selecione **células** > **executar todos** para executar o bloco de notas completo. Quando vir um asterisco __*__, junto a uma célula, a célula ainda está em execução. Após o código para essa célula terminar, é apresentado um número. 

1. Siga as instruções no bloco de notas para autenticar a sua subscrição do Azure.

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

Criou os recursos necessários para experimentar e implementar modelos. Também executou algum código num bloco de notas. E explorou o histórico de execuções desse código na sua área de trabalho na cloud.

Para obter uma experiência de fluxo de trabalho detalhados, siga os tutoriais de Machine Learning para preparar e implementar um modelo:  

> [!div class="nextstepaction"]
> [Tutorial: Preparar um modelo de classificação de imagem](tutorial-train-models-with-aml.md)
