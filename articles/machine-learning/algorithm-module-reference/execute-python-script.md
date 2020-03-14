---
title: 'Execute o roteiro python: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Execute Python Script em Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: 3370c7ebb8e0253543e6b9cb6ce7614811fb5bd0
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79140795"
---
# <a name="execute-python-script-module"></a>Execute módulo de script python

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design da Python, consulte [o seguinte artigo.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Com a Python, pode executar tarefas que não são suportadas atualmente por módulos existentes, tais como:

+ Visualizar dados usando `matplotlib`
+ Utilização de bibliotecas Python para enumerar conjuntos de dados e modelos no seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes não suportadas pelo módulo [de dados](./import-data.md) de importação
+ Executa o seu próprio código de aprendizagem profunda 


O Azure Machine Learning utiliza a distribuição da Anaconda da Python, que inclui muitos utilitários comuns para o processamento de dados. Atualizaremos automaticamente a versão Anaconda. A versão atual é:
 -  Distribuição anaconda 4.5+ para Python 3.6 

As embalagens pré-instaladas são:
-  asn1crypto==0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro==1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0,24.2
- Almofada==6.0.0
- pip==19.0.3
- pyarrow==0.12.1
- pycparser==2.19
- pycryptodomex==3.7.3
- pirsistente==0.14.11
- python-dateutil==2.8.0
- pytz==2018.9
- pedidos==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3==1.24.1
- roda==0,33.1 

 Para instalar outros pacotes que não estão na lista pré-instalada, por *exemplo, scikit-misc,* adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="upload-files"></a>Carregar ficheiros
O **Script Execute Python** suporta o upload de ficheiros utilizando o [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#upload-file-name--path-or-stream-).

O exemplo que se segue mostra como carregar um ficheiro de imagem no módulo **execute Python Script:**

```Python

# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

Depois de o gasoduto ser submetido com sucesso, pode pré-visualizar a imagem no painel direito do módulo

[!div class="mx-imgBorder"]
![](media/module/upload-image-in-python-script.png) de imagem carregada

## <a name="how-to-configure-execute-python-script"></a>Como configurar o Script Python execute

O módulo **Execute Python Script** contém um código Python de amostra que pode utilizar como ponto de partida. Para configurar o módulo **Execute Python Script,** fornece um conjunto de inputs e código Python para executar na caixa de **texto** python.

1. Adicione o módulo **Execute Python Script** ao seu pipeline.

2. Adicione e ligue no **Dataset1** quaisquer conjuntos de dados do designer que pretende utilizar para a entrada. Refira este conjunto de dados no seu script Python como **DataFrame1**.

    A utilização de um conjunto de dados é opcional, se pretender gerar dados utilizando python, ou utilizar o código Python para importar os dados diretamente para o módulo.

    Este módulo suporta a adição de um segundo conjunto de dados no **Dataset2**. Consulte o segundo conjunto de dados no seu script Python como DataFrame2.

    Os conjuntos de dados armazenados no Azure Machine Learning são automaticamente convertidos em dados **pandas.frames** quando carregados com este módulo.

    ![Execute o mapa de entrada python](media/module/python-module.png)

4. Para incluir novos pacotes python ou código, adicione o ficheiro zipped contendo estes recursos personalizados no **pacote script**. A entrada para o **pacote Script** deve ser um ficheiro com fecho de mão enviado para o seu espaço de trabalho como um Conjunto de Dados do tipo Ficheiro. Pode fazer o upload do conjunto de dados na página de ativos **datasets** e pode arrastar e largar o módulo dataset da lista **my datasets** na árvore do módulo esquerdo na página de autor de designers. 

    Qualquer ficheiro contido no arquivo com fecho carregado pode ser utilizado durante a execução do gasoduto. Se o arquivo incluir uma estrutura de diretório, a estrutura é preservada, mas deve preparar um diretório chamado **SRC** para o caminho.

5. Na caixa de texto de **script Python,** escreva ou pasta script python válido.

    A caixa de texto de **script Python** é pré-povoada com algumas instruções em comentários, e código de amostra para acesso e saída de dados. Tem de editar ou substituir este código. Certifique-se de seguir as convenções python sobre o entalhe e o invólucro.

    + O script deve conter uma função denominada `azureml_main` como ponto de entrada para este módulo.
    + A função do ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
    + Os ficheiros com fecho ligados à terceira porta de entrada são desapertados e armazenados no diretório, `.\Script Bundle`, que também é adicionado ao `sys.path`Python . 

    Portanto, se o seu ficheiro zip contiver `mymodule.py`, importe-o utilizando `import mymodule`.

    + Dois conjuntos de dados podem ser devolvidos ao designer, que deve ser uma sequência de tipo `pandas.DataFrame`. Pode criar outras saídas no seu código Python e escrevê-las diretamente no armazenamento do Azure.

6. Executar o pipeline, ou selecionar o módulo e clicar **Executar selecionado** para executar apenas o script Python.

    Todos os dados e códigosão carregados numa máquina virtual e funcionam utilizando o ambiente python especificado.

## <a name="results"></a>Resultados

Os resultados de quaisquer cálculos efetuados pelo código Python incorporado devem ser fornecidos como pandas. DataFrame, que é automaticamente convertido para o formato de conjunto de dados Azure Machine Learning, para que possa utilizar os resultados com outros módulos no pipeline.

O módulo devolve dois conjuntos de dados:  
  
+ **Resultados Dataset 1**, definido pelo primeiro quadro de dados de pandas devolvidos no script Python

+ Conjunto de Dados de **Resultados 2**, definido pelo segundo quadro de dados de pandas devolvidos no script Python


## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 