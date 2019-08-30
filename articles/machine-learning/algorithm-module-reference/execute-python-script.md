---
title: 'Executar script Python: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo executar script Python no serviço de Azure Machine Learning para executar o código Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 4bd3433db92767f2d0d733ab71e4298fc5e618f8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128813"
---
# <a name="execute-python-script-module"></a>Executar módulo de script Python

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para executar o código Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [o artigo a seguir.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Com o Python, você pode executar tarefas que atualmente não são suportadas por módulos existentes, como:

+ Visualizando dados usando`matplotlib`
+ Usando bibliotecas do Python para enumerar conjuntos de valores e modelos em seu espaço de trabalho
+ Leitura, carregamento e manipulação de dados de fontes sem suporte pelo módulo [importar dados](./import-data.md)
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

 Para instalar outros pacotes que não estão na lista pré-instalada, por exemplo, *scikit-misc*, adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Como utilizar

O módulo **Executar script Python** contém o código Python de exemplo que você pode usar como ponto de partida. Para configurar o módulo **Executar script Python** , você fornece um conjunto de entradas e código Python para executar na caixa de texto **script Python** .

1. Adicione o módulo **Executar script Python** ao seu experimento.

2. Adicione e conecte-se em **dataSet1** quaisquer conjuntos de dados da interface que você deseja usar para entrada. Referencie este conjunto de DataSet em seu script Python como **DataFrame1**.

    O uso de um conjunto de dados é opcional, se você quiser gerar dados usando o Python ou usar o código Python para importar os dados diretamente para o módulo.

    Este módulo dá suporte à adição de um segundo conjunto de uma em **Dataset2**. Referencie o segundo conjunto de DataSet em seu script Python como DataFrame2.

    Os conjuntos de dados armazenados em Azure Machine Learning são convertidos automaticamente em **pandas** Data. frames quando carregados com esse módulo.

    ![Executar o mapa de entrada do Python](media/module/python-module.png)

4. Para incluir novos pacotes ou códigos do Python, adicione o arquivo compactado que contém esses recursos personalizados no **pacote de script**. A entrada para o **grupo de scripts** deve ser um arquivo compactado já carregado em seu espaço de trabalho. 

    Qualquer arquivo contido no arquivo compactado carregado pode ser usado durante a execução do experimento. Se o arquivo incluir uma estrutura de diretório, a estrutura será preservada, mas você deverá preceder um diretório chamado **src** para o caminho.

5. Na caixa de texto **script do Python** , digite ou cole script Python válido.

    A caixa de texto **script Python** é preenchida previamente com algumas instruções em comentários e código de exemplo para acesso a dados e saída. **Você deve editar ou substituir este código.** Certifique-se de seguir as convenções do Python sobre recuos e maiúsculas e minúsculas.

    + O script deve conter uma função chamada `azureml_main` como ponto de entrada para este módulo.
    + A função de ponto de entrada pode conter até dois argumentos de `Param<dataframe1>` entrada: e`Param<dataframe2>`
    + Os arquivos compactados conectados à terceira porta de entrada são descompactados e armazenados no `.\Script Bundle`diretório, que também é adicionado ao Python `sys.path`. 

    Portanto, se o arquivo zip contiver `mymodule.py`, importe-o usando. `import mymodule`

    + Dois conjuntos de valores podem ser retornados para a interface, que deve ser uma sequência do `pandas.DataFrame`tipo. Você pode criar outras saídas em seu código Python e gravá-las diretamente no armazenamento do Azure.

6. Execute o experimento ou selecione o módulo e clique em **executar selecionado** para executar apenas o script Python.

    Todos os dados e o código são carregados em uma máquina virtual e executados usando o ambiente do Python especificado.

## <a name="results"></a>Resultados

Os resultados de qualquer computação executada pelo código Python inserido devem ser fornecidos como pandas. Dataframe, que é convertido automaticamente no formato de conjunto de Azure Machine Learning, para que você possa usar os resultados com outros módulos no experimento.

O módulo retorna dois conjuntos de valores:  
  
+ **Conjunto de resultados 1**, definido pela primeira vez que o banco de dataframe do Panda retornado no script Python

+ O **conjunto de resultados de resultado 2**, definido pelo segundo retornou o dataframe pandas no script Python


## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 