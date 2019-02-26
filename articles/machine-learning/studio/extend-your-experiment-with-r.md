---
title: Ampliar a sua experimentação com R
titleSuffix: Azure Machine Learning Studio
description: Como estender a funcionalidade do Azure Machine Learning Studio pela linguagem R com o módulo de executar o Script de R.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: ab3d42c1a1131489bcd27033c4c99d17e62d6ac6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820608"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Azure Machine Learning Studio: Ampliar a sua experimentação com R 
Pode expandir a funcionalidade do Azure Machine Learning Studio pela linguagem R ao utilizar o [executar Script R] [ execute-r-script] módulo.

Este módulo aceita vários conjuntos de dados de entrada e produz um único conjunto de dados como saída. Pode escrever um script R para o **R Script** parâmetro do [executar Script do R] [ execute-r-script] módulo.

Aceder a cada porta de entrada do módulo usando código semelhante ao seguinte:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>A listagem de todos os pacotes atualmente instalado
Pode alterar a lista de pacotes instalados. Uma lista de pacotes atualmente instalados pode ser encontrada na [pacotes de R suportados pelo Azure Machine Learning Studio](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Também pode obter a lista completa, atual de pacotes instalados ao introduzir o código seguinte para o [executar Script R] [ execute-r-script] módulo:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Esta ação envia a lista de pacotes para a porta de saída a [executar Script R] [ execute-r-script] módulo.
Para ver a lista de pacotes, ligar um módulo de conversão, tal como [converter para CSV] [ convert-to-csv] à saída do esquerda o [executar Script do R] [ execute-r-script] módulo, Execute a experimentação, em seguida, clique com o resultado do módulo de conversão e selecione **transferir**. 

![Transferir o resultado do módulo de "Converter para CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importação dos pacotes
Pode importar pacotes que ainda não estiver instalados utilizando os seguintes comandos do [executar Script R] [ execute-r-script] módulo:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

onde o `my_favorite_package.zip` arquivo contém o pacote.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
