---
title: Executar scripts de Python machine learning
titleSuffix: Azure Machine Learning Studio
description: Saiba como utilizar o Python no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/05/2019
ms.openlocfilehash: f508d16330bad7044a69ccff2ddf84ece74e78a2
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729421"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts de machine learning em Python no Azure Machine Learning Studio

Python é uma ferramenta importante no qual a ferramenta de muitos cientistas de dados. Ele é usado em cada etapa dos fluxos de trabalho do aprendizado de máquina típico incluindo a exploração de dados, extração de funcionalidade, preparação de modelos e validação e implementação.

Este artigo descreve como pode utilizar o módulo de executar o Script de Python a utilizar o código de Python nos serviços da web e experiências do Azure Machine Learning Studio.

## <a name="using-the-execute-python-script-module"></a>Utilizar o módulo de executar o Script de Python

A interface principal para Python no Studio é através da [executar Script do Python] [ execute-python-script] módulo. Ele aceita até três entradas e produz até duas saídas, semelhantes a [executar Script R] [ execute-r-script] módulo. Código de Python é inserido na caixa de parâmetro através de uma função de ponto de entrada especialmente nomeado chamada `azureml_main`.

![Executar o módulo de Script de Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Código de python de exemplo na caixa de parâmetro do módulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parâmetros de entrada

Entradas para o módulo de Python são expostas como Pandas DataFrames. O `azureml_main` função aceita até dois pacotes de Pandas opcional como parâmetros.

O mapeamento entre as portas de entrada e os parâmetros da função é posicional:

- A primeira porta de entrada ligada é mapeada para o primeiro parâmetro da função.
- A segunda entrada (se ligado) é mapeada para o segundo parâmetro da função.
- A terceira entrada é utilizada para [importar módulos de Python adicionais](#import-modules).

Mais detalhadas a semântica de como as portas de entrada são mapeadas para os parâmetros do `azureml_main` função são mostrados abaixo.

![Tabela de configurações de porta de entrada e de assinatura resultante do Python](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valores de retorno de saída

O `azureml_main` função tem de devolver um DataFrame Pandas único empacotados num Python [sequência](https://docs.python.org/2/c-api/sequence.html) como uma cadeia de identificação, lista ou NumPy matriz. O primeiro elemento desta seqüência é retornado para a primeira porta de saída do módulo. A segunda porta de saída do módulo é utilizada para [visualizações](#visualizations) e não requer um valor de retorno. Este esquema é mostrado abaixo.

![Mapeamento de portas para os parâmetros de entrada e retornar o valor para a porta de saída](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Conversão de tipos de dados de entrada e saída

Conjuntos de dados do Studio não são os mesmos que o pandas DataFrames. Como resultado, conjuntos de dados de entrada no Studio são convertidos em Pandas DataFrame e pacotes de saída são convertidos novamente para conjuntos de dados do Studio. Durante este processo de conversão, também são executadas as traduções seguintes:

 **Tipo de dados de Python** | **Procedimento de tradução do Studio** |
| --- | --- |
| Cadeias de caracteres e numéricos| Traduzido como está |
| Pandas "ND" | Traduzido como "Valor em falta" |
| Vetores de índice | Não suportado * |
| Nomes das colunas de cadeia de caracteres não | Chamar `str` nos nomes das colunas |
| Nomes de colunas duplicados | Adicione sufixo numérico: (1), (2), (3), e assim por diante.
**Todos os quadros de dados de entrada na função Python sempre terá um índice numérico de 64 bits de 0 para o número de linhas menos 1*

## <a id="import-modules"></a>Importar os módulos de script de Python existentes

O back-end utilizado para executar o Python se baseia [Anaconda](https://store.continuum.io/cshop/anaconda/), um amplamente utilizadas científica distribuição de Python. Ele vem com quase 200 para os pacotes de Python mais comuns utilizados em cargas de trabalho centrado em dados. No entanto, pode achar a necessidade de incorporar bibliotecas adicionais.

Um caso de uso comum é incorporar scripts existentes do Python em experimentações do Studio. O [executar Script do Python] [ execute-python-script] módulo aceita um ficheiro zip que contém módulos de Python na porta de entrada terceiro. O ficheiro é descompactei pela estrutura de execução em tempo de execução e os conteúdos são adicionados ao caminho de biblioteca do interpretador de Python. O `azureml_main` função pode, em seguida, importar esses módulos diretamente de ponto de entrada.

Por exemplo, considere o arquivo Hello.py que contém uma função de "Olá, mundo" simples.

![Função definida pelo utilizador no ficheiro de Hello.py](./media/execute-python-scripts/figure4.png)

Em seguida, criamos um ficheiro zip que contém Hello.py:

![Ficheiro zip que contém o código de Python definido pelo utilizador](./media/execute-python-scripts/figure5.png)

Carregar o ficheiro zip como um conjunto de dados no Studio. Em seguida, criar e executar uma experiência que usa o código de Python no ficheiro zip ao ligá-la para a porta de entrada terceiro do **executar Script do Python** módulo, conforme mostrado na imagem seguinte.

![Experimentação de exemplo com o Zip como entrada para um módulo de executar o Script de Python](./media/execute-python-scripts/figure6a.png)

![Código de Python definido pelo utilizador carregado como um ficheiro zip](./media/execute-python-scripts/figure6b.png)

O resultado do módulo mostra que o ficheiro zip foi descompactado e que a função `print_hello` tiver sido executado.

![Saída do módulo que mostra a função definida pelo utilizador](./media/execute-python-scripts/figure7.png)

## <a name="operationalizing-python-scripts"></a>Operacionalização de scripts do Python

Qualquer [executar Script do Python] [ execute-python-script] módulos utilizados numa experimentação classificação são chamados quando publicado como um serviço web. Por exemplo, a imagem abaixo mostra uma experimentação de classificação que contém o código para avaliar uma única expressão de Python.

![Área de trabalho do Studio para um serviço web](./media/execute-python-scripts/figure3a.png)

![Expressão de Python Pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Um serviço da web criado a partir desta experiência demoraria as seguintes ações:

1. Tirar uma expressão de Python como entrada (como uma cadeia de caracteres)
1. Enviar a expressão de Python para o interpretador de Python
1. Devolve uma tabela que contém a expressão e o resultado avaliado.

## <a id="visualizations"></a>Trabalhar com visualizações

Gráficos criados com MatplotLib podem ser devolvidos pela [executar Script do Python][execute-python-script]. No entanto, gráficos não são redirecionados automaticamente para imagens de forma que quando utilizar o R. Portanto, o utilizador tem explicitamente de guardar quaisquer gráficos para ficheiros PNG.

Para gerar imagens a partir de MatplotLib, tem de seguir os passos seguintes:

1. Mude o back-end para "AGG" do processador baseado em Qt padrão.
1. Crie um novo objeto de figura.
1. Obtenha o eixo e gerar todos os gráficos na mesma.
1. Guarde a figura num ficheiro PNG.

Este processo é ilustrado nas imagens seguintes que criar uma matriz de gráfico de dispersão usando a função de scatter_matrix no Pandas.

![Código para guardar as figuras de MatplotLib para imagens](./media/execute-python-scripts/figure-v1-8.png)

![Clique em visualizar num módulo de executar Script do Python para ver os valores](./media/execute-python-scripts/figure-v2-9a.png)

![Visualização de gráficos para uma experimentação de exemplo usando o código de Python](./media/execute-python-scripts/figure-v2-9b.png)

É possível retornar vários valores por salvá-los em imagens diferentes. O tempo de execução do Studio seleciona todas as imagens e concatena-os para visualização.

## <a name="advanced-examples"></a>Exemplos avançados

O ambiente de Anaconda instalado no Studio contém pacotes comuns, como NumPy, SciPy e Scikits de aprender. Esses pacotes podem ser usados com eficiência para processamento de dados num pipeline de aprendizagem automática.

Por exemplo, a experimentação seguinte e o script ilustram o uso de aprendizes ensemble no Scikits de aprender para calcular as pontuações de importância de funcionalidade para um conjunto de dados. As pontuações podem ser utilizadas para efetuar a seleção de funcionalidades supervisionado antes de a ser inserida em outro modelo.

Segue-se a função de Python utilizada para calcular as pontuações de importância e os recursos com base nas pontuações de ordem:

![Função às funcionalidades de classificação por pontuações](./media/execute-python-scripts/figure8.png)

Experimentação seguinte, em seguida, calcula e devolve as pontuações de importância dos recursos no conjunto de dados "Pima Rúpia Diabetes" no Azure Machine Learning Studio:

![Experimente a funcionalidades de classificação no conjunto de dados Pima Rúpia Diabetes com Python](./media/execute-python-scripts/figure9a.png)

![Visualização da saída do módulo executar Script do Python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitações

O [executar Script do Python] [ execute-python-script] módulo atualmente tem as seguintes limitações:

### <a name="sandboxed-execution"></a>Execução restrita

O tempo de execução do Python é atualmente restrita e não permite o acesso à rede ou sistema de arquivos local de forma persistente. Todos os ficheiros guardados localmente são isolados e eliminados depois do módulo for concluída. O código de Python não é possível aceder a maioria dos diretórios na máquina que é executada, a exceção que está a ser o diretório atual e de seus subdiretórios.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Falta de desenvolvimento sofisticado e a depuração de suporte

O módulo de Python não suporta atualmente recursos do IDE, como o intellisense e depuração. Além disso, se o módulo falhar em tempo de execução, o rastreio de pilha de Python completo está disponível. Mas tem de ser visualizado no registo de saída para o módulo. Atualmente, é recomendável que desenvolve e depurar scripts do Python num ambiente, como o IPython e, em seguida, importar o código para o módulo.

### <a name="single-data-frame-output"></a>Saída de quadro de dados única

O ponto de entrada de Python apenas é permitido para retornar um quadro de dados única como saída. Não é atualmente possível devolver objetos arbitrários de Python, tais como modelos de formação diretamente ao tempo de execução do Studio. Como [executar Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos pickle objetos numa matriz de bytes e, em seguida, retornará essa dentro de um quadro de dados.

### <a name="inability-to-customize-python-installation"></a>Incapacidade de personalizar a instalação do Python

Atualmente, a única forma de adicionar módulos personalizados do Python é por meio do mecanismo de arquivo zip descrito anteriormente. Embora isso seja viável para pequenos módulos, torna-se complexo para módulos grandes (especialmente módulos com DLLs nativas) ou um grande número de módulos.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script