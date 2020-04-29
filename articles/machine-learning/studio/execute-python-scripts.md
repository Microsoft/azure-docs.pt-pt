---
title: Executar scripts Python
titleSuffix: ML Studio (classic) - Azure
description: Aprenda a usar o módulo Execute Python Script para utilizar o código Python em machine learning studio (clássico) experiências e serviços web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218085"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Execute scripts de aprendizagem automática Python no Estúdio de Aprendizagem automática Azure (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python é uma ferramenta valiosa no peito de ferramentas de muitos cientistas de dados. É usado em todas as fases dos fluxos típicos de trabalho de aprendizagem automática, incluindo exploração de dados, extração de recursos, treino e validação de modelos, e implantação.

Este artigo descreve como pode utilizar o módulo Execute Python Script para utilizar o código Python nas suas experiências e serviços web do Azure Machine Learning Studio (clássico).

## <a name="using-the-execute-python-script-module"></a>Utilizando o módulo de script de execução python

A interface primária para Python em Estúdio (clássico) é através do módulo [Execute Python Script.][execute-python-script] Aceita até três inputs e produz até duas saídas, semelhantes ao módulo [Execute R Script.][execute-r-script] O código Python é introduzido na caixa de parâmetros `azureml_main`através de uma função de ponto de entrada especialmente denominada .

![Execute módulo de script python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Código python de amostra na caixa de parâmetros do módulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parâmetros de entrada

As inputs do módulo Python são expostas como Pandas DataFrames. A `azureml_main` função aceita até dois Pandas DataFrames opcionais como parâmetros.

O mapeamento entre as portas de entrada e os parâmetros de função é posicional:

- A primeira porta de entrada ligada é mapeada para o primeiro parâmetro da função.
- A segunda entrada (se ligada) é mapeada para o segundo parâmetro da função.
- A terceira entrada é utilizada para [importar módulos Python adicionais.](#import-modules)

Semântica mais detalhada de como as portas de entrada `azureml_main` são mapeadas para os parâmetros da função são mostradas abaixo.

![Tabela de configurações da porta de entrada e assinatura Python resultante](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valores de retorno de saída

A `azureml_main` função deve devolver um único DataFrame pandas embalado numa [sequência](https://docs.python.org/2/c-api/sequence.html) Python, como uma matriz de tuple, lista ou numpy. O primeiro elemento desta sequência é devolvido à primeira porta de saída do módulo. A segunda porta de saída do módulo é utilizada para [visualizações](#visualizations) e não requer um valor de retorno. Este esquema é mostrado abaixo.

![Mapear portas de entrada para parâmetros e devolver valor à porta de saída](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tradução dos tipos de dados de entrada e de saída

Os conjuntos de dados do estúdio não são os mesmos que os Dados panda. Como resultado, os conjuntos de dados de entrada em Studio (clássico) são convertidos para Pandas DataFrame, e os DataFrames de saída são convertidos de volta para conjuntos de dados Studio (clássicos). Durante este processo de conversão, são também realizadas as seguintes traduções:

 **Tipo de dados python** | **Procedimento de tradução de estúdio** |
| --- | --- |
| Cordas e numéricos| Traduzido como é |
| Pandas 'NA' | Traduzido como 'Valor em falta' |
| Vetores de índice | Sem apoio* |
| Nomes de colunas sem cordas | Chamada `str` sobre nomes de colunas |
| Nomes de colunas duplicadas | Adicione sufixo numérico: (1), (2), (3) e assim por diante.

**Todos os quadros de dados de entrada na função Python têm sempre um índice numérico de 64 bits de 0 ao número de linhas menos 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importar módulos de script python existentes

O backend usado para executar Python é baseado em [Anaconda,](https://www.anaconda.com/distribution/)uma distribuição científica de Python amplamente utilizada. Vem com cerca de 200 dos pacotes python mais comuns usados em cargas de trabalho centradas em dados. O Studio (clássico) não suporta atualmente a utilização de sistemas de gestão de pacotes como o Pip ou a Conda para instalar e gerir bibliotecas externas.  Se encontrar a necessidade de incorporar bibliotecas adicionais, utilize o seguinte cenário como guia.

Um caso comum de uso é incorporar scripts Python existentes em experiências studio (clássicas). O módulo [Execute Python Script][execute-python-script] aceita um ficheiro zip contendo módulos Python na terceira porta de entrada. O ficheiro é desapertado pelo quadro de execução em tempo de execução e os conteúdos são adicionados ao caminho da biblioteca do intérprete Python. A `azureml_main` função do ponto de entrada pode então importar estes módulos diretamente. 

Como exemplo, considere o ficheiro Hello.py contendo uma função simples "Olá, Mundo".

![Função definida pelo utilizador no ficheiro Hello.py](./media/execute-python-scripts/figure4.png)

Em seguida, criamos um ficheiro Hello.zip que contém Hello.py:

![Ficheiro postal contendo código Python definido pelo utilizador](./media/execute-python-scripts/figure5.png)

Faça upload do ficheiro zip como um conjunto de dados para studio (clássico). Em seguida, crie e execute uma experiência que usa o código Python no ficheiro Hello.zip, fixando-o à terceira porta de entrada do módulo **Execute Python Script,** como mostrado na imagem seguinte.

![Experimente a amostra com Hello.zip como entrada para um módulo execute Python Script](./media/execute-python-scripts/figure6a.png)

![Código Python definido pelo utilizador carregado como um ficheiro zip](./media/execute-python-scripts/figure6b.png)

A saída do módulo mostra que o ficheiro zip `print_hello` foi desembalado e que a função foi executada.

![Saída do módulo mostrando função definida pelo utilizador](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Acesso a Blobs de Armazenamento Azure

Pode aceder aos dados armazenados numa conta de Armazenamento Azure Blob utilizando estas etapas:

1. Descarregue o [pacote de armazenamento Azure Blob para Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localmente.
1. Faça upload do ficheiro zip para o seu Studio (clássico) espaço de trabalho como conjunto de dados.
1. Crie o seu objeto BlobService com`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Desativar **a transferência segura necessária** no separador de definição de **configuração** de armazenamento

![Desativar transferência segura necessária no portal Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Roteiros piton operacionalizadores

Quaisquer módulos [execute Python Script][execute-python-script] usados numa experiência de pontuação são chamados quando publicados como um serviço web. Por exemplo, a imagem abaixo mostra uma experiência de pontuação que contém o código para avaliar uma única expressão Python.

![Espaço de trabalho estúdio para um serviço web](./media/execute-python-scripts/figure3a.png)

![Expressão de Pandas Python](./media/execute-python-scripts/python-script-with-python-pandas.png)

Um serviço web criado a partir desta experiência todaria as seguintes ações:

1. Tome uma expressão Python como entrada (como uma corda)
1. Envie a expressão Python ao intérprete Python
1. Devolve uma tabela contendo tanto a expressão como o resultado avaliado.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Trabalhar com visualizações

Os enredos criados usando o MatplotLib podem ser devolvidos pelo [Script Execute Python][execute-python-script]. No entanto, os enredos não são automaticamente redirecionados para imagens como são quando se utiliza R. Por isso, o utilizador deve guardar explicitamente quaisquer parcelas para ficheiros PNG.

Para gerar imagens do MatplotLib, deve tomar os seguintes passos:

1. Mude o backend para "AGG" do renderizador baseado em Qt predefinido.
1. Crie um novo objeto de figura.
1. Pegue o eixo e gere todos os enredos nele.
1. Guarde a figura para um ficheiro PNG.

Este processo é ilustrado nas seguintes imagens que criam uma matriz de enredo de dispersão usando a função scatter_matrix em Pandas.

![Código para salvar figuras do MatplotLib para as imagens](./media/execute-python-scripts/figure-v1-8.png)

![Clique em visualizar um módulo execute Python Script para ver os números](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizar parcelas para uma experiência de amostra usando código Python](./media/execute-python-scripts/figure-v2-9b.png)

É possível devolver várias figuras, guardando-as em diferentes imagens. O tempo de execução do estúdio (clássico) capta todas as imagens e concatena-as para visualização.

## <a name="advanced-examples"></a>Exemplos avançados

O ambiente Anaconda instalado no Studio (clássico) contém pacotes comuns como NumPy, SciPy e Scikits-Learn. Estes pacotes podem ser eficazmente utilizados para o processamento de dados num pipeline de aprendizagem automática.

Por exemplo, a experiência e o script que se aseguir ilustram o uso de alunos de conjunto em Scikits-Learn para calcular pontuações de importância para um conjunto de dados. As pontuações podem ser usadas para realizar a seleção de funcionalidades supervisionada antes de serem alimentadas noutro modelo.

Aqui está a função Python usada para calcular as pontuações de importância e encomendar as características com base nas pontuações:

![Função para classificar funcionalidades por pontuações](./media/execute-python-scripts/figure8.png)

A experiência seguinte computa e devolve a importância de dezenas de funcionalidades no conjunto de dados "Pima Indian Diabetes" no Azure Machine Learning Studio (clássico):

![Experimente classificar as características no conjunto de dados pima indian diabetes usando Python](./media/execute-python-scripts/figure9a.png)

![Visualização da saída do módulo execute Python Script](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitações

O módulo [execute Python Script][execute-python-script] tem atualmente as seguintes limitações:

### <a name="sandboxed-execution"></a>Execução de sandboxed

O tempo de funcionação python é atualmente sandboxed e não permite o acesso à rede ou ao sistema de ficheiros local de forma persistente. Todos os ficheiros guardados localmente são isolados e eliminados assim que o módulo terminar. O código Python não pode aceder à maioria dos diretórios da máquina em que funciona, sendo a exceção o atual diretório e os seus subdiretórios.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Falta de desenvolvimento sofisticado e apoio à depuração

O módulo Python atualmente não suporta funcionalidades ide tais como intellisense e depuração. Além disso, se o módulo falhar no tempo de funcionar, o conjunto de vestígios de pilha Python está disponível. Mas deve ser visto no registo de saída do módulo. Atualmente recomendamos que desenvolva e depura scripts Python num ambiente como o IPython e, em seguida, importe o código para o módulo.

### <a name="single-data-frame-output"></a>Saída de quadro único de dados

O ponto de entrada python só é permitido devolver um único quadro de dados como saída. Atualmente não é possível devolver objetos python arbitrários, como modelos treinados diretamente de volta ao Estúdio (clássico) tempo de execução. Tal como o [Execute R Script][execute-r-script], que tem a mesma limitação, é possível, em muitos casos, colocar objetos picles numa matriz byte e depois devolvê-lo dentro de um quadro de dados.

### <a name="inability-to-customize-python-installation"></a>Incapacidade de personalizar instalação Python

Atualmente, a única maneira de adicionar módulos Python personalizados é através do mecanismo de ficheiro zip descrito anteriormente. Embora isso seja viável para pequenos módulos, é complicado para módulos grandes (especialmente módulos com DLLs nativos) ou um grande número de módulos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
