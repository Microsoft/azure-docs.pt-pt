---
title: Transformar dados
titleSuffix: Azure Machine Learning
description: Aprenda a transformar dados no designer de Machine Learning Azure para criar os seus próprios conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.custom: designer
ms.openlocfilehash: 718023424834ffca7a026dc5b3d35b8fb11fe633
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644288"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>Transforme dados em designer de Machine Learning Azure (pré-visualização)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Neste artigo, aprende-se a transformar e guardar conjuntos de dados no designer de Machine Learning Azure para que possa preparar os seus próprios dados para machine learning.

Utilizará a amostra Adult [Census Income Binary Classification](sample-designer-datasets.md) para preparar dois conjuntos de dados: um conjunto de dados que inclui informações de recenseamento adulto apenas dos Estados Unidos e outro conjunto de dados que inclui informações de recenseamento de adultos não americanos.

Neste artigo, vai aprender a:

1. Transforme um conjunto de dados para prepará-lo para o treino.
1. Exportar os conjuntos de dados resultantes para uma loja de dados.
1. Ver resultados.

Este como-fazer é um pré-requisito para a forma de retreinar o artigo de modelos de [designers.](how-to-retrain-designer.md) Nesse artigo, aprenderá a utilizar os conjuntos de dados transformados para treinar vários modelos com parâmetros de pipeline.

## <a name="transform-a-dataset"></a>Transformar um conjunto de dados

Nesta secção, aprende-se a importar o conjunto de dados da amostra e a dividir os dados em conjuntos de dados dos EUA e não dos EUA. Para obter mais informações sobre como importar os seus próprios dados para o designer, consulte [como importar dados](how-to-designer-import-data.md).

### <a name="import-data"></a>Importar dados

Utilize os seguintes passos para importar o conjunto de dados da amostra.

1. Inscreva-se na <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com que pretende trabalhar.

1. Vai ter com o designer. Selecione **módulos de pré-construção Easy-to-use** para criar um novo pipeline.

1. Selecione um alvo de computação padrão para executar o gasoduto.

1. À esquerda da tela do oleoduto está uma paleta de conjuntos de dados e módulos. Selecione **Conjuntos**de Dados . Em seguida, veja a secção **amostras.**

1. Arraste e deixe cair o conjunto de dados bináriode rendimento do **recenseamento adulto** na tela.

1. Selecione o módulo de conjunto de dados **do Rendimento do Censo Adulto.**

1. No painel de detalhes que aparece à direita da tela, selecione **Saídas**.

1. Selecione o ícone visualizar ![visualizar ícone](media/how-to-designer-transform-data/visualize-icon.png).

1. Utilize a janela de pré-visualização de dados para explorar o conjunto de dados. Tome nota especial dos valores da coluna "país nativo".

### <a name="split-the-data"></a>Dividir os dados

Nesta secção, utiliza-se o [módulo Split Data](algorithm-module-reference/split-data.md) para identificar e dividir linhas que contenham "Estados Unidos" na coluna "país nativo". 

1. Na paleta de módulos à esquerda da tela, expanda a secção **de Transformação** de Dados e encontra o módulo **De dados divididos.**

1. Arraste o módulo **De dados divididos** para a tela e deixe cair o módulo abaixo do módulo dataset.

1. Ligue o módulo dataset ao módulo **De dados split.**

1. Selecione o módulo **De dados split.**

1. No módulo os detalhes painelam à direita da tela, desloque **o modo de divisão** para a expressão **regular**.

1. Introduza a **Expressão Regular:** `\"native-country" United-States` .

    O modo **de expressão regular** testa uma única coluna por um valor. Para obter mais informações sobre o módulo De dados divididos, consulte a página de referência do [módulo de algoritmo](algorithm-module-reference/split-data.md)relacionado .

O seu oleoduto deve ser assim:

![Screenshot mostrando como configurar o pipeline e o módulo De dados split](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>Guardar os conjuntos de dados

Agora que o seu pipeline está configurado para dividir os dados, precisa especificar onde persistir os conjuntos de dados. Para este exemplo, utilize o módulo **De dados de exportação** para guardar o seu conjunto de dados para uma loja de dados. Para mais informações sobre as lojas de dados, consulte Os serviços de [armazenamento Connect to Azure](how-to-access-data.md)

1. Na paleta de módulos à esquerda da tela, expanda a secção **de Entrada e Saída** de Dados e encontra o módulo de Dados de **Exportação.**

1. Arraste e deixe cair dois módulos de **Dados de Exportação** abaixo do módulo **De dados divididos.**

1. Ligue cada porta de saída do módulo **De dados divididos** a um módulo de **Dados de Exportação** diferente.

    O seu oleoduto deve ser parecido com isto:

    ![Screenshot mostrando como ligar os módulos de Dados de Exportação](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Selecione o módulo De dados de **exportação** que está ligado à *porta mais à esquerda*do módulo De dados **split.**

    A ordem das portas de saída é importante para o módulo **De dados split.** A primeira porta de saída contém as linhas onde a expressão regular é verdadeira. Neste caso, o primeiro porto contém linhas para os rendimentos baseados nos EUA, e a segunda porta contém linhas para rendimentos não baseados nos EUA.

1. No módulo os detalhes painelem à direita da tela, detete as seguintes opções:
    
    **Tipo de loja**de dados : Armazenamento Azure Blob

    **Datastore**: Selecione uma loja de dados existente ou selecione "Nova datastore" para criar uma agora.

    **Caminho:**`/data/us-income`

    **Formato de ficheiro**: csv

    > [!NOTE]
    > Este artigo assume que tem acesso a uma loja de dados registada no atual espaço de trabalho azure machine learning. Para obter instruções sobre como configurar uma loja de dados, consulte os serviços de [armazenamento Connect to Azure](how-to-access-data.md#azure-machine-learning-studio).

    Se não tiver uma loja de dados, pode criar uma agora. Por exemplo, este artigo irá guardar os conjuntos de dados para a conta de armazenamento blob padrão associada ao espaço de trabalho. Guardará os conjuntos de dados no `azureml` recipiente numa nova pasta chamada `data` .

1.  Selecione o módulo de Dados de **Exportação** ligado à *porta mais correta*do módulo De dados **divididos.**

1. No módulo os detalhes painelem à direita da tela, detete as seguintes opções:
    
    **Tipo de loja**de dados : Armazenamento Azure Blob

    **Datastore**: Selecione a mesma loja de dados que acima

    **Caminho:**`/data/non-us-income`

    **Formato de ficheiro**: csv

1. Confirme que o módulo de Dados de **Exportação** ligado à porta esquerda dos **Dados Divididos** tem o **Caminho** `/data/us-income` .

1. Confirme que o módulo **de Dados de Exportação** ligado à porta direita tem o **Caminho** `/data/non-us-income` .

    O seu oleoduto e as suas definições devem ser assim:
    
    ![Screenshot mostrando como configurar os módulos de Dados de Exportação](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Submeter a corrida

Agora que o seu oleoduto está configurado para dividir e exportar os dados, submeta uma execução de gasoduto.

1. Na parte superior da tela, selecione **Submeter**.

1. No diálogo de execução do **gasoduto Configurar,** selecione **Criar novo** para criar uma experiência.

    Experiências logicamente agrupam-se em conjunto com gasodutos relacionados. Se executar este oleoduto no futuro, deve usar a mesma experiência para fins de exploração e rastreio.

1. Forneça um nome de experiência descritivo como "dados de recenseamento dividido".

1. Selecione **Submeter**.

## <a name="view-results"></a>Ver resultados

Depois de o gasoduto terminar de funcionar, pode ver os seus resultados navegando até ao seu armazenamento de blob no portal Azure. Também pode visualizar os resultados intermediários do módulo **Split Data** para confirmar que os seus dados foram divididos corretamente.

1. Selecione o módulo **De dados split.**

1. No módulo os detalhes painelam à direita da tela, selecione **Saídas + troncos**. 

1. Selecione o ícone visualizar o ![ ícone visualizar o ícone ao lado do ](media/how-to-designer-transform-data/visualize-icon.png) **dataset1**dos resultados . 

1. Verifique se a coluna "país nativo" contém apenas o valor "Estados Unidos".

1. Selecione o ícone visualizar o ícone visualizar o ícone ao lado do conjunto de ![ dados ](media/how-to-designer-transform-data/visualize-icon.png) **resultados2**. 

1. Verifique se a coluna "país nativo" não contém o valor "Estados Unidos".

## <a name="clean-up-resources"></a>Limpar recursos

Ignore esta secção se quiser continuar com a parte 2 disto [como, retreinar modelos com o designer de Machine Learning Azure.](how-to-retrain-designer.md)

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a transformar um conjunto de dados e a guardá-lo para uma loja de dados registada.

Continue para a próxima parte desta série de how-to com [os modelos Retrain com o designer de Machine Learning Azure](how-to-retrain-designer.md) para usar os seus conjuntos de dados e parâmetros de pipeline transformados para treinar modelos de machine learning.
