---
title: 'Execute o Script de Python: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de executar o Script de Python no serviço Azure Machine Learning para executar código de Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6e61ed5a18e69b107b78bf2042de21d14cd6beb5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029134"
---
# <a name="execute-python-script-module"></a>Executar o módulo de Script de Python

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para executar o código de Python. Para obter mais informações sobre os princípios de arquitetura e design do Python, consulte [o seguinte artigo.](https://docs.microsoft.com/azure/machine-learning/machine-learning-execute-python-scripts)

Com o Python, pode realizar tarefas que não são atualmente suportadas pelo módulos existentes, tais como:

+ Visualizar dados com `matplotlib`
+ Utilizar as bibliotecas Python para enumerar conjuntos de dados e modelos em sua área de trabalho
+ Ler, para carregar e manipular dados de fontes não suportadas o [importar dados](./import-data.md) módulo
+ Executar o seu próprio código de aprendizagem profunda 


O Azure Machine Learning utiliza a distribuição Anaconda Python, que inclui muitos utilitários comuns para processamento de dados. Iremos atualizar versão Anaconda automaticamente. Versão atual é:
 -  Distribuição de anaconda 4.5 + para o Python 3.6 

Os pacotes pré-instalados são:
-  asn1crypto = = 0.24.0
- attrs==19.1.0
- azure-common==1.1.18
- azure-storage-blob==1.5.0
- azure-storage-common==1.4.0
- certifi==2019.3.9
- cffi==1.12.2
- chardet==3.0.4
- cryptography==2.6.1
- distro = = 1.4.0
- idna==2.8
- jsonschema==3.0.1
- lightgbm==2.2.3
- more-itertools==6.0.0
- numpy==1.16.2
- pandas==0.24.2
- Pillow = = 6.0.0
- pip==19.0.3
- pyarrow = = 0.12.1
- pycparser = = 2.19
- pycryptodomex==3.7.3
- pyrsistent = = 0.14.11
- python-dateutil==2.8.0
- pytz = = 2018.9
- requests==2.21.0
- scikit-learn==0.20.3
- scipy==1.2.1
- setuptools==40.8.0
- six==1.12.0
- torch==1.0.1.post2
- torchvision==0.2.2.post3
- urllib3 = = 1.24.1
- roda = = 0.33.1 

 Para instalar outros pacotes não estão na lista previamente instalada, por exemplo *scikit-DIV*, adicione o seguinte código ao seu script: 

 ```python
import os
os.system(f"pip install scikit-misc")
```

## <a name="how-to-use"></a>Como utilizar

O **executar Script do Python** módulo contém o código de Python de exemplo que pode utilizar como ponto de partida. Para configurar o **executar Script do Python** módulo, fornecer um conjunto de entradas e código de Python para executar o **script de Python** caixa de texto.

1. Adicionar a **executar Script do Python** módulo à sua experimentação.

2. Adicionar e ligar-se no **Dataset1** quaisquer conjuntos de dados a partir da interface que pretende utilizar para a entrada. Este conjunto de dados no seu script de Python como de referência **DataFrame1**.

    Utilização de um conjunto de dados é opcional, se pretende gerar dados com o Python ou utilize o código de Python para importar os dados diretamente para o módulo.

    Este módulo suporta a adição de um segundo conjunto de dados no **Dataset2**. O segundo conjunto de dados no seu script de Python como DataFrame2 de referência.

    Conjuntos de dados armazenados no Azure Machine Learning são automaticamente convertidos para **pandas** data.frames quando carregada com este módulo.

    ![Executar o mapa de entrada de Python](media/module/python-module.png)

4. Para incluir novos pacotes de Python ou código, adicione o arquivo zipado, que contém estes recursos personalizados no **pacote de Script**. A entrada para **pacote de Script** tem de ser um arquivo zipado já carregado para a área de trabalho. 

    Qualquer arquivo contido no arquivo zipado carregado pode ser utilizado durante a execução da experimentação. Se o arquivo inclui uma estrutura de diretório, a estrutura é mantida, mas tem de preceder um diretório chamado **src** ao caminho.

5. Na **script de Python** caixa de texto, escreva ou cole o script de Python válido.

    O **script de Python** caixa de texto é povoada previamente com algumas instruções em comentários e código de exemplo para acesso a dados e de saída. **Tem de editar ou substituir esse código.** Não se esqueça de seguir as convenções de Python sobre avanço e tem maiúsculas e minúsculas.

    + O script tem de conter uma função chamada `azureml_main` como ponto de entrada para este módulo.
    + A função de ponto de entrada pode conter até dois argumentos de entrada: `Param<dataframe1>` e `Param<dataframe2>`
    + Arquivos compactados ligados à porta de entrada terceiro são descompactei e armazenados no diretório, `.\Script Bundle`, que também é adicionado para o Python `sys.path`. 

    Por conseguinte, se o ficheiro zip contém `mymodule.py`, importe-o utilizando `import mymodule`.

    + Dois conjuntos de dados podem ser retornados para a interface, que tem de ser uma seqüência de tipo `pandas.DataFrame`. Pode criar outras saídas em seu código de Python e escrevê-los diretamente ao armazenamento do Azure.

6. Execute a experimentação, ou selecione o módulo e clique em **executar seleção** para executar apenas o script de Python.

    Todos os dados e o código é carregado numa máquina virtual e executados utilizando o ambiente de Python especificado.

## <a name="results"></a>Resultados

Os resultados de qualquer computações realizadas por código de Python incorporado devem ser fornecidos como um pandas. Pacote de dados, que é convertido automaticamente para o formato de conjunto de dados do Azure Machine Learning, para que possa utilizar os resultados com outros módulos na experimentação.

O módulo retorna dois conjuntos de dados:  
  
+ **Resultados 1 conjunto de dados**, definido pelo retornado pandas dataframe primeiro no script de Python

+ **Resultar de conjunto de dados 2**, definido pelo retornado pandas dataframe segundo no script de Python


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 