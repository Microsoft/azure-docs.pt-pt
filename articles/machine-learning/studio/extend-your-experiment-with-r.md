---
title: Ampliar a sua experimentação com R
titleSuffix: Azure Machine Learning Studio (classic)
description: Como estender a funcionalidade de Azure Machine Learning Studio (clássico) por meio da linguagem R usando o módulo executar script R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: a1a3eca380240d624da3e2f086749756aabccbe2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492948"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (clássico): Estenda seu experimento com o R 
Você pode estender a funcionalidade do Azure Machine Learning Studio (clássico) por meio da linguagem R usando o módulo [Executar script r][execute-r-script] .

Esse módulo aceita vários conjuntos de dados de entrada e produz um único conjunto como saída. Você pode digitar um script R no parâmetro de **script r** do módulo [Executar script r][execute-r-script] .

Você acessa cada porta de entrada do módulo usando um código semelhante ao seguinte:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Listando todos os pacotes instalados no momento
A lista de pacotes instalados pode ser alterada. Uma lista de pacotes instalados no momento pode ser encontrada em [pacotes de R com suporte pelo Azure Machine Learning Studio (clássico)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Você também pode obter a lista completa e atual dos pacotes instalados inserindo o seguinte código no módulo [Executar script R][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Isso envia a lista de pacotes para a porta de saída do módulo [Executar script R][execute-r-script] .
Para exibir a lista de pacotes, conecte um módulo de conversão, como [converter em CSV][convert-to-csv] , para a saída esquerda do módulo [Executar script R][execute-r-script] , executar o experimento e, em seguida, clique na saída do módulo de conversão e selecione **baixar**. 

![Baixar a saída do módulo "converter em CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importando pacotes
Você pode importar pacotes que ainda não estão instalados usando os seguintes comandos no módulo [Executar script R][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

onde o arquivo de `my_favorite_package.zip` contém o pacote.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
