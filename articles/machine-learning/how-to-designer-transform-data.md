---
title: Transformar dados no designer
titleSuffix: Azure Machine Learning
description: Aprenda a importar e transformar dados no designer Azure Machine Learning para criar os seus próprios conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 10904275d382c7f9dbf223ded78cab0adf14992a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661057"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transforme dados em Azure Machine Learning designer


Neste artigo, aprende-se a transformar e guardar conjuntos de dados no designer Azure Machine Learning para que possa preparar os seus próprios dados para a aprendizagem automática.

Você usará a amostra de conjunto de dados [de classificação binária de rendimento de censos](./samples-designer.md) adulto para preparar dois conjuntos de dados: um conjunto de dados que inclui informações de recenseamento de adultos apenas dos Estados Unidos e outro conjunto de dados que inclui informações de recenseamento de adultos não americanos.

Neste artigo, vai aprender a:

1. Transforme um conjunto de dados para prepará-lo para o treino.
1. Exporte os conjuntos de dados resultantes para uma data-loja.
1. Ver resultados.

Este como-fazer é um pré-requisito para como reconverter o artigo [de modelos de designers.](how-to-retrain-designer.md) Nesse artigo, você aprenderá a usar os conjuntos de dados transformados para treinar vários modelos com parâmetros de pipeline.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformar um conjunto de dados

Nesta secção, aprende-se a importar o conjunto de dados da amostra e divide os dados em conjuntos de dados dos EUA e não dos EUA. Para obter mais informações sobre como importar os seus próprios dados para o designer, consulte [como importar dados.](how-to-designer-import-data.md)

### <a name="import-data"></a>Importar dados

Utilize os seguintes passos para importar o conjunto de dados da amostra.

1. Inscreva-se para <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com o quais pretende trabalhar.

1. Vai ter com o designer. Selecione **módulos pré-depurações fáceis de usar** para criar um novo oleoduto.

1. Selecione um alvo de computação padrão para executar o pipeline.

1. À esquerda da tela do gasoduto está uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos de dados**. Em seguida, veja a secção **amostras.**

1. Arraste e deixe cair o conjunto de dados de **classificação binária de rendimento do rendimento** do rendimento adulto na tela.

1. Clique com o botão direito no módulo de conjunto de dados **do Rendimento do Censo de Adultos** e selecione a saída do Conjunto de Dados **visualize**  >  

1. Utilize a janela de pré-visualização de dados para explorar o conjunto de dados. Tome nota especial dos valores da coluna "país nativo".

### <a name="split-the-data"></a>Dividir os dados

Nesta secção, utiliza-se o [módulo de Dados Divididos](algorithm-module-reference/split-data.md) para identificar e dividir linhas que contêm "Estados Unidos" na coluna "país nativo". 

1. Na paleta de módulos à esquerda da tela, expanda a secção **de Transformação de Dados** e encontre o módulo **de Dados Divididos.**

1. Arraste o módulo **de Dados Divididos** para a tela e deixe cair o módulo abaixo do módulo de conjunto de dados.

1. Ligue o módulo de conjunto de dados ao módulo **Dados Divididos.**

1. Selecione o módulo **de dados divididos.**

1. No painel de detalhes do módulo à direita da tela, de ajuste o **modo de divisão** para a Expressão **Regular**.

1. Introduza a **Expressão Regular:** `\"native-country" United-States` .

    O modo **de expressão regular** testa uma única coluna por um valor. Para obter mais informações sobre o módulo Dados Divididos, consulte a página de referência do [módulo de algoritmo](algorithm-module-reference/split-data.md)relacionado .

O seu oleoduto deve ser assim:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="Screenshot mostrando como configurar o pipeline e o módulo de dados divididos":::


## <a name="save-the-datasets"></a>Guardar os conjuntos de dados

Agora que o seu pipeline está configurado para dividir os dados, precisa de especificar onde persistir os conjuntos de dados. Para este exemplo, utilize o módulo **de Dados de Exportação** para guardar o seu conjunto de dados para uma datastore. Para obter mais informações sobre as datastores, consulte [os serviços de armazenamento Connect to Azure](how-to-access-data.md)

1. Na paleta do módulo à esquerda da tela, expanda a secção entrada e saída de **dados** e encontre o módulo **de Dados de Exportação.**

1. Arraste e deixe cair dois módulos **de Dados** de Exportação abaixo do módulo **de dados divididos.**

1. Ligue cada porta de saída do módulo **de dados divididos** a um módulo de **dados de exportação** diferente.

    O seu oleoduto deve ser parecido com isto:

    ![Screenshot mostrando como ligar os módulos de Dados de Exportação](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Selecione o módulo **dados de exportação** que está ligado à porta *esquerda* do módulo **Dados Divididos.**

    A ordem das portas de saída é importante para o módulo **De Dados Divididos.** A primeira porta de saída contém as linhas onde a expressão regular é verdadeira. Neste caso, a primeira porta contém filas para os rendimentos baseados nos EUA, e a segunda porta contém filas para rendimentos não americanos.

1. No painel de detalhes do módulo à direita da tela, desa estale as seguintes opções:
    
    **Tipo de loja de dados**: Azure Blob Storage

    **Datastore**: Selecione uma datastore existente ou selecione "New datastore" para criar um agora.

    **Caminho:**`/data/us-income`

    **Formato de ficheiro**: csv

    > [!NOTE]
    > Este artigo pressupõe que você tem acesso a uma datastore registrada no atual espaço de trabalho Azure Machine Learning. Para obter instruções sobre como configurar uma loja de dados, consulte [os serviços de armazenamento Connect to Azure](how-to-connect-data-ui.md#create-datastores).

    Se não tiver uma loja de dados, pode criar uma agora. Por exemplo, este artigo irá guardar os conjuntos de dados para a conta de armazenamento de bolhas predefinida associada ao espaço de trabalho. Guardará os conjuntos de dados no `azureml` recipiente numa nova pasta chamada `data` .

1.  Selecione o módulo **dados de exportação** ligado à porta *direita* do módulo **Dados Divididos.**

1. No painel de detalhes do módulo à direita da tela, desa estale as seguintes opções:
    
    **Tipo de loja de dados**: Azure Blob Storage

    **Datastore**: Selecione a mesma loja de dados acima

    **Caminho:**`/data/non-us-income`

    **Formato de ficheiro**: csv

1. Confirme que o módulo **de Dados de Exportação** ligado à porta esquerda dos **Dados Divididos** tem o **Caminho** `/data/us-income` .

1. Confirme que o módulo **de Dados de Exportação** ligado à porta direita tem o **Caminho** `/data/non-us-income` .

    O seu oleoduto e as suas configurações devem ser assim:
    
    ![Screenshot mostrando como configurar os módulos de dados de exportação](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Submeter a corrida

Agora que o seu oleoduto está configurado para dividir e exportar os dados, envie uma corrida de gasoduto.

1. Na parte superior da tela, **selecione Enviar por favor.**

1. No diálogo de execução do **gasoduto set up,** selecione **Criar novos** para criar uma experiência.

    As experiências agrupam logicamente as corridas de gasodutos relacionadas. Se executar este oleoduto no futuro, deverá utilizar a mesma experiência para fazer login e rastrear.

1. Fornecer um nome de experiência descritiva como "split-census-data".

1. Selecione **Submeter**.

## <a name="view-results"></a>Ver resultados

Depois de o gasoduto terminar a funcionar, pode ver os seus resultados navegando para o seu armazenamento de bolhas no portal Azure. Também pode ver os resultados intermediários do módulo **Dados Divididos** para confirmar que os seus dados foram corretamente divididos.

1. Selecione o módulo **de dados divididos.**

1. No painel de detalhes do módulo à direita da tela, selecione **Outputs + logs**. 

1. Selecione o ícone ![ visualizado visualizar ícone ](media/how-to-designer-transform-data/visualize-icon.png) ao lado do conjunto de **dados de resultados1**. 

1. Verifique se a coluna "país nativo" contém apenas o valor "Estados Unidos".

1. Selecione o ícone ![ visualizado visualizar ícone ](media/how-to-designer-transform-data/visualize-icon.png) ao lado do conjunto de **dados de resultados2**. 

1. Verifique se a coluna "país nativo" não contém o valor "Estados Unidos".

## <a name="clean-up-resources"></a>Limpar os recursos

Salte esta secção se quiser continuar com a parte 2 deste como, [retrain modelos com o designer de Machine Learning Azure](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a transformar um conjunto de dados e guardá-lo para uma datastore registada.

Continue até à próxima parte desta série de como fazer com [os modelos Retrain com o designer Azure Machine Learning](how-to-retrain-designer.md) para usar os seus conjuntos de dados transformados e parâmetros de pipeline para treinar modelos de aprendizagem automática.