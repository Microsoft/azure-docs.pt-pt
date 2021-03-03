---
title: Começar com O Azure Percept desenvolvimento avançado na nuvem
description: Começar com desenvolvimento avançado na nuvem através de Cadernos Jupyter e Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665054"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Começando com desenvolvimento avançado na nuvem através de Cadernos Jupyter e Azure Machine Learning

Este artigo acompanha-o através do processo de criação de um espaço de trabalho Azure Machine Learning, enviando uma amostra pré-configurada jupyter Notebook para o espaço de trabalho, criando uma instância de computação, e executando as células do caderno dentro do espaço de trabalho.

O [portátil](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) realiza a aprendizagem de transferência utilizando um modelo tensorFlow pré-treinado (MobileNetSSDV2Lite) em AzureML em Python com um conjunto de dados personalizado para detetar tigelas.

O portátil mostra como começar a partir do [COCO,](https://cocodataset.org/#home)filtre-o apenas para a classe de interesse (tigelas) e, em seguida, convertê-lo no formato apropriado. Em alternativa, pode utilizar a ferramenta de rotulagem [VoTT 2](https://github.com/microsoft/VoTT) de código aberto para criar e rotular caixas de delimitação no formato PASCAL VOC.

Depois de retreinar o modelo no conjunto de dados personalizado, o modelo pode então ser implantado no seu Azure Percept DK utilizando o método de atualização twin do módulo.

Em seguida, pode verificar a inferencing do modelo visualizando o fluxo RTSP ao vivo do Azure Percept Vision SoM. Tanto a reconversão do modelo como a implementação são realizadas dentro do caderno na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

- [Assinatura de aprendizagem automática Azure](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK com Azure Percept Vision SoM conectado](./overview-azure-percept-dk.md)
- [Experiência de embarque Azure Percept DK](./quickstart-percept-dk-set-up.md) concluída

## <a name="download-azure-percept-github-repository"></a>Baixar o repositório Azure Percept GitHub

1. Vá ao [repositório Azure Percept GitHub.](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)

1. Clone o repositório ou descarregue o ficheiro ZIP. Perto da parte superior do ecrã, clique em **Código**  ->  **Descarregamento ZIP**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="Ecrã de descarregamento do GitHub.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Configurar portal de aprendizagem automática Azure e caderno

1. Vá ao [Portal de Aprendizagem automática Azure.](https://ml.azure.com)

1. Selecione o seu diretório, subscrição Azure e espaço de trabalho machine learning a partir dos menus drop down e clique **em Começar**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Azure ML começa o ecrã.":::

    Se ainda não tiver um espaço de trabalho para aprendizagem automática Azure, clique em **Criar um novo espaço de trabalho.** No novo separador do navegador, faça o seguinte:

    1. Selecione a sua subscrição do Azure.
    1. Selecione o seu grupo de recursos.
    1. Insira um nome para o seu espaço de trabalho.
    1. Selecione a sua região.
    1. Selecione a sua edição do espaço de trabalho.
    1. Clique em **Rever e criar**.
    1. Na página seguinte, reveja as suas seleções e clique em **Criar**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Tela de criação de espaço de trabalho em Azure ML.":::

    Por favor, permita alguns minutos para a criação do espaço de trabalho. Após a criação do espaço de trabalho estar concluída, verá marcas de verificação verdes ao lado dos seus recursos e a **sua implantação está completa** no topo da página de visão geral dos Serviços de Aprendizagem automática.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Confirmação da criação do espaço de trabalho." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Assim que a sua criação de espaço de trabalho estiver concluída, volte ao separador portal de machine learning e clique em **Iniciar**.

1. Na página inicial do espaço de trabalho de aprendizagem automática, clique em **Cadernos** no painel esquerdo.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Página inicial do Azure ML.":::

1. No **separador 'Os meus ficheiros',** clique na seta vertical para carregar o ficheiro .ipynb.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Página inicial realçando o ícone do ficheiro de upload.":::

1. Navegue para e selecione o [ficheiro Transferlearningusing_SSDLiteV2 Model.ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) a partir da sua cópia local do repositório Azure Percept GitHub. Clique em **Abrir**. Na janela **de ficheiros upload,** verifique a caixa ao lado **do I trust conteúdos deste ficheiro** e clique em **Upload**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Ecrã de seleção de ficheiros.":::

1. Na barra de menu superior direita, verifique o seu estado **de Computação.** Se não forem encontrados cálculos, clique no **+** ícone para criar um novo cálculo.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Estado do cálculo com + ícone destacado." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. Na janela de instância de **computação Nova,** introduza um **nome Compute,** escolha um **tipo de máquina Virtual** e selecione um tamanho de máquina **Virtual**. Clique em **Criar**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Novo ecrã de criação de instância compute.":::

    Uma vez clicada Em **Criar,** o seu estado **de Computação** apresentará um círculo azul e **\<your compute name> - Criar**

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Estado do cálculo quando a criação de cálculo ainda está em andamento.":::

    O seu estado **de Computação** apresentará um círculo verde e **\<your compute name> - Correr** após a criação do cálculo está completo.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="O estado do cálculo que mostra a criação de computação está completo.":::

1. Uma vez que o cálculo esteja em funcionamento, selecione o núcleo **Python 3.6 - AzureML** do menu suspenso ao lado do **+** ícone.

## <a name="working-with-the-notebook"></a>Trabalhando com o caderno

Está agora pronto para executar o caderno para treinar o seu detetor de taças personalizado e implantá-lo no seu devkit. Certifique-se de que executa cada célula do caderno individualmente, uma vez que algumas das células requerem parâmetros de entrada antes de executar o script. As células que necessitam de parâmetros de entrada podem ser editadas diretamente no caderno. Para executar uma célula, clique no ícone de reprodução no lado esquerdo da célula:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Portátil realçando ícone de célula." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Passos seguintes

Para mais cadernos de exemplo de serviço de aprendizagem automática Azure, visite este [repo](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)
