---
title: 'Executar script Python: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo executar script Python no Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 1d82261d4b5c1a66498c33610670d7a38acbb197
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152334"
---
# <a name="execute-python-script-module"></a>Execute módulo de script python

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design da Python, consulte [o seguinte artigo.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Com o Python, você pode executar tarefas que atualmente não são suportadas por módulos existentes, como:

+ Visualizar dados usando `matplotlib`
+ Usando bibliotecas do Python para enumerar conjuntos de valores e modelos em seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes não suportadas pelo módulo [de dados](./import-data.md) de importação
+ Execute seu próprio código de aprendizado profundo 


Azure Machine Learning usa a distribuição Anaconda do Python, que inclui muitos utilitários comuns para processamento de dados. Atualizaremos a versão do Anaconda automaticamente. A versão atual é:
 -  Anaconda 4.5 + distribuição para Python 3,6 

Os pacotes pré-instalados são:
-  asn1crypto = = 0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- Certificates = = 2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distribuição = = 1.4.0
- IDNA = = 2,8
- jsonschema==3.0.1
- lightgbm = = 2.2.3
- more-itertools==6.0.0
- numpy = = 1.16.2
- pandas = = 0.24.2
- Pillow = = 6.0.0
- pip==19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex = = 3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz = = 2018.9
- solicitações = = 2.21.0
- scikit-Learn = = 0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3 = = 1.24.1
- Wheel = = 0.33.1 

 Para instalar outros pacotes que não estão na lista pré-instalada, por *exemplo, scikit-misc,* adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Como utilizar

O módulo **Execute Python Script** contém um código Python de amostra que pode utilizar como ponto de partida. Para configurar o módulo **Execute Python Script,** fornece um conjunto de inputs e código Python para executar na caixa de **texto** python.

1. Adicione o módulo **Execute Python Script** ao seu pipeline.

2. Adicione e ligue no **Dataset1** quaisquer conjuntos de dados do designer que pretende utilizar para a entrada. Refira este conjunto de dados no seu script Python como **DataFrame1**.

    O uso de um conjunto de dados é opcional, se você quiser gerar dados usando o Python ou usar o código Python para importar os dados diretamente para o módulo.

    Este módulo suporta a adição de um segundo conjunto de dados no **Dataset2**. Referencie o segundo conjunto de DataSet em seu script Python como DataFrame2.

    Os conjuntos de dados armazenados no Azure Machine Learning são automaticamente convertidos em dados **pandas.frames** quando carregados com este módulo.

    ![Executar o mapa de entrada do Python](media/module/python-module.png)

4. Para incluir novos pacotes python ou código, adicione o ficheiro zipped contendo estes recursos personalizados no **pacote script**. A entrada para script **bundle** deve ser um ficheiro com fecho já enviado para o seu espaço de trabalho. 

    Qualquer arquivo contido no arquivo compactado carregado pode ser usado durante a execução do pipeline. Se o arquivo incluir uma estrutura de diretório, a estrutura é preservada, mas deve preparar um diretório chamado **SRC** para o caminho.

5. Na caixa de texto de **script Python,** escreva ou pasta script python válido.

    A caixa de texto de **script Python** é pré-povoada com algumas instruções em comentários, e código de amostra para acesso e saída de dados. Você deve editar ou substituir este código. Certifique-se de seguir as convenções do Python sobre recuos e maiúsculas e minúsculas.

    + O script deve conter uma função denominada `azureml_main` como ponto de entrada para este módulo.
    + A função do ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
    + Os ficheiros com fecho ligados à terceira porta de entrada são desapertados e armazenados no diretório, `.\Script Bundle`, que também é adicionado ao `sys.path`Python . 

    Portanto, se o seu ficheiro zip contiver `mymodule.py`, importe-o utilizando `import mymodule`.

    + Dois conjuntos de dados podem ser devolvidos ao designer, que deve ser uma sequência de tipo `pandas.DataFrame`. Você pode criar outras saídas em seu código Python e gravá-las diretamente no armazenamento do Azure.

6. Executar o pipeline, ou selecionar o módulo e clicar **Executar selecionado** para executar apenas o script Python.

    Todos os dados e o código são carregados em uma máquina virtual e executados usando o ambiente do Python especificado.

## <a name="results"></a>Resultados

Os resultados de qualquer computação executada pelo código Python inserido devem ser fornecidos como pandas. Dataframe, que é convertido automaticamente no formato de conjunto de Azure Machine Learning, para que você possa usar os resultados com outros módulos no pipeline.

O módulo retorna dois conjuntos de valores:  
  
+ **Resultados Dataset 1**, definido pelo primeiro quadro de dados de pandas devolvidos no script Python

+ Conjunto de Dados de **Resultados 2**, definido pelo segundo quadro de dados de pandas devolvidos no script Python


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 