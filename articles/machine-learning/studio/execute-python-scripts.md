---
title: Executar scripts do Python Machine Learning
titleSuffix: Azure Machine Learning Studio
description: Saiba como usar o Python no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 64030cac73b6fbd750b2ed681d85642cc6ad1146
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308868"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Executar scripts de machine learning em Python no Azure Machine Learning Studio

O Python é uma ferramenta valiosa na conjunto de ferramentas de muitos cientistas de dados. Ele é usado em todos os estágios de fluxos de trabalho de aprendizado de máquina típicos, incluindo exploração de dados, extração de recursos, treinamento e validação de modelo e implantação.

Este artigo descreve como você pode usar o módulo executar script Python para usar o código Python em seu Azure Machine Learning Studio experimentos e serviços Web.

## <a name="using-the-execute-python-script-module"></a>Usando o módulo executar script Python

A interface principal para Python no estúdio é por meio do módulo [Executar script Python][execute-python-script] . Ele aceita até três entradas e produz até duas saídas, semelhante ao módulo [Executar script R][execute-r-script] . O código Python é inserido na caixa de parâmetros por meio de uma função de ponto de `azureml_main`entrada especialmente nomeada chamada.

![Executar módulo de script Python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Código Python de exemplo na caixa de parâmetro de módulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parâmetros de entrada

As entradas para o módulo python são expostas como os dataframes do pandas. A `azureml_main` função aceita até dois quadros de as do pandas opcionais como parâmetros.

O mapeamento entre as portas de entrada e os parâmetros de função é posicional:

- A primeira porta de entrada conectada é mapeada para o primeiro parâmetro da função.
- A segunda entrada (se conectada) é mapeada para o segundo parâmetro da função.
- A terceira entrada é usada para [Importar módulos Python adicionais](#import-modules).

Uma semântica mais detalhada de como as portas de entrada são mapeadas para parâmetros `azureml_main` da função são mostradas abaixo.

![Tabela de configurações de porta de entrada e assinatura do Python resultante](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valores de retorno de saída

A `azureml_main` função deve retornar um único quadro de tabela pandas em uma [sequência](https://docs.python.org/2/c-api/sequence.html) do Python, como uma tupla, uma lista ou uma matriz numpy. O primeiro elemento dessa sequência é retornado para a primeira porta de saída do módulo. A segunda porta de saída do módulo é usada para [visualizações](#visualizations) e não requer um valor de retorno. Esse esquema é mostrado abaixo.

![Mapeando portas de entrada para parâmetros e retornando o valor para a porta de saída](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tradução de tipos de dados de entrada e saída

Os conjuntos de valores do estúdio não são os mesmos que o Panda dataframes. Como resultado, os conjuntos de dados de entrada no estúdio são convertidos em um dataframe do pandas e os dataframes de saída são convertidos de volta em conjuntos de dados do estúdio. Durante esse processo de conversão, as seguintes traduções também são executadas:

 **Tipo de dados do Python** | **Procedimento de tradução do estúdio** |
| --- | --- |
| Cadeias de caracteres e numéricos| Traduzido como está |
| Pandas ' NA ' | Traduzido como ' valor ausente ' |
| Vetores de índice | Sem suporte |
| Nomes de coluna que não são de cadeia de caracteres | Chamar `str` em nomes de coluna |
| Nomes de coluna duplicados | Adicionar sufixo numérico: (1), (2), (3) e assim por diante.

**Todos os quadros de dados de entrada na função Python sempre têm um índice numérico de 64 bits de 0 até o número de linhas menos 1*

## <a id="import-modules"></a>Importando módulos de script Python existentes

O back-end usado para executar o Python é baseado em [Anaconda](https://www.anaconda.com/distribution/), uma distribuição de Python científica amplamente usada. Ele vem com quase 200 dos pacotes python mais comuns usados em cargas de trabalho centradas em dados. Atualmente, o estúdio não dá suporte ao uso de sistemas de gerenciamento de pacotes como PIP ou Conda para instalar e gerenciar bibliotecas externas.  Se você achar a necessidade de incorporar bibliotecas adicionais, use o cenário a seguir como guia.

Um caso de uso comum é incorporar scripts Python existentes em experimentos de estúdio. O módulo [Executar script Python][execute-python-script] aceita um arquivo ZIP contendo módulos Python na terceira porta de entrada. O arquivo é descompactado pela estrutura de execução no tempo de execução e o conteúdo é adicionado ao caminho da biblioteca do intérprete do Python. A `azureml_main` função de ponto de entrada pode então importar esses módulos diretamente. 

Como exemplo, considere o arquivo Hello.py que contém uma função "Hello, World" simples.

![Função definida pelo usuário no arquivo Hello.py](./media/execute-python-scripts/figure4.png)

Em seguida, criamos um arquivo Hello. zip que contém Hello.py:

![Arquivo zip que contém o código Python definido pelo usuário](./media/execute-python-scripts/figure5.png)

Carregue o arquivo zip como um conjunto de um DataSet no estúdio. Em seguida, crie e execute um experimento que use o código Python no arquivo Hello. zip anexando-o à terceira porta de entrada do módulo **Executar script Python** , conforme mostrado na imagem a seguir.

![Teste de exemplo com Hello. zip como uma entrada para um módulo executar script Python](./media/execute-python-scripts/figure6a.png)

![Código Python definido pelo usuário carregado como um arquivo zip](./media/execute-python-scripts/figure6b.png)

A saída do módulo mostra que o arquivo zip foi desempacotado e que a função `print_hello` foi executada.

![Saída do módulo mostrando a função definida pelo usuário](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Acessando blobs de armazenamento do Azure

Você pode acessar os dados armazenados em uma conta de armazenamento de BLOBs do Azure usando estas etapas:

1. Baixe o [pacote de armazenamento de BLOBs do Azure para Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localmente.
1. Carregue o arquivo zip no seu espaço de trabalho do estúdio como um conjunto de uma.
1. Crie seu objeto BlobService com`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Desabilitar **transferência segura necessária** na guia de **configuração de armazenamento**

![Desabilite a transferência segura necessária no portal do Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacionalização de scripts Python

Qualquer módulo [Executar script Python][execute-python-script] usado em um experimento de pontuação é chamado quando publicado como um serviço Web. Por exemplo, a imagem abaixo mostra um experimento de pontuação que contém o código para avaliar uma única expressão Python.

![Espaço de trabalho do estúdio para um serviço Web](./media/execute-python-scripts/figure3a.png)

![Expressão do Python pandas](./media/execute-python-scripts/python-script-with-python-pandas.png)

Um serviço Web criado com base nesse experimento executaria as seguintes ações:

1. Usar uma expressão Python como entrada (como uma cadeia de caracteres)
1. Enviar a expressão Python para o intérprete do Python
1. Retorna uma tabela que contém a expressão e o resultado avaliado.

## <a id="visualizations"></a>Trabalhando com visualizações

As plotagens criadas usando MatplotLib podem ser retornadas pelo [script de execução do Python][execute-python-script]. No entanto, as plotagens não são redirecionadas automaticamente para imagens como são ao usar o R. Portanto, o usuário deve salvar explicitamente quaisquer plotagens em arquivos PNG.

Para gerar imagens do MatplotLib, você deve executar as seguintes etapas:

1. Alterne o back-end para "AGG" do processador padrão baseado em Qt.
1. Crie um novo objeto de figura.
1. Obter o eixo e gerar todas as plotagens nele.
1. Salve a figura em um arquivo PNG.

Esse processo é ilustrado nas imagens a seguir que criam uma matriz de gráfico de dispersão usando a função scatter_matrix em pandas.

![Código para salvar figuras MatplotLib em imagens](./media/execute-python-scripts/figure-v1-8.png)

![Clique em Visualizar em um módulo executar script Python para exibir as figuras](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizando plotagens para um experimento de exemplo usando código Python](./media/execute-python-scripts/figure-v2-9b.png)

É possível retornar várias figuras salvando-as em imagens diferentes. O tempo de execução do estúdio pega todas as imagens e as concatena para visualização.

## <a name="advanced-examples"></a>Exemplos avançados

O ambiente Anaconda instalado no estúdio contém pacotes comuns, como NumPy, SciPy e scikits-learn. Esses pacotes podem ser usados efetivamente para processamento de dados em um pipeline de Machine Learning.

Por exemplo, o experimento e o script a seguir ilustram o uso de aprendizes do Ensemble no scikits-Learn para calcular pontuações de importância de recursos para um conjunto de informações. As pontuações podem ser usadas para executar a seleção de recursos supervisionados antes de serem alimentadas em outro modelo.

Aqui está a função Python usada para calcular as pontuações de importância e ordenar os recursos com base nas pontuações:

![Função para classificar recursos por pontuações](./media/execute-python-scripts/figure8.png)

Em seguida, o experimento a seguir computa e retorna as pontuações de importância dos recursos no conjunto de Pima "diabetes Índico" no Azure Machine Learning Studio:

![Teste para classificar recursos no conjunto de Pima Índico diabetes usando Python](./media/execute-python-scripts/figure9a.png)

![Visualização da saída do módulo executar script Python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitações

O módulo [Executar script Python][execute-python-script] atualmente tem as seguintes limitações:

### <a name="sandboxed-execution"></a>Execução em área restrita

O tempo de execução do Python está em área restrita no momento e não permite o acesso à rede ou ao sistema de arquivos local de maneira persistente. Todos os arquivos salvos localmente são isolados e excluídos após a conclusão do módulo. O código Python não pode acessar a maioria dos diretórios no computador em que ele é executado, a exceção é o diretório atual e seus subdiretórios.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Falta de suporte sofisticado de desenvolvimento e depuração

O módulo python atualmente não dá suporte a recursos de IDE, como IntelliSense e depuração. Além disso, se o módulo falhar em tempo de execução, o rastreamento de pilha do Python completo estará disponível. Mas ele deve ser exibido no log de saída do módulo. No momento, recomendamos que você desenvolva e depure scripts Python em um ambiente como IPython e, em seguida, importe o código para o módulo.

### <a name="single-data-frame-output"></a>Saída de quadro de dados único

O ponto de entrada do Python só é permitido para retornar um único quadro de dados como saída. Atualmente, não é possível retornar objetos Python arbitrários, como modelos treinados diretamente para o tempo de execução do estúdio. Como [executar o script R][execute-r-script], que tem a mesma limitação, é possível, em muitos casos, pickle objetos em uma matriz de bytes e, em seguida, retorná-los dentro de um quadro de dados.

### <a name="inability-to-customize-python-installation"></a>Incapacidade de personalizar a instalação do Python

Atualmente, a única maneira de adicionar módulos do Python personalizados é por meio do mecanismo de arquivo zip descrito anteriormente. Embora isso seja viável para módulos pequenos, ele é trabalhoso para módulos grandes (especialmente módulos com DLLs nativas) ou um grande número de módulos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script