---
title: Ampliar a sua experimentação com R
titleSuffix: ML Studio (classic) - Azure
description: Como alargar a funcionalidade do Azure Machine Learning Studio (clássico) através da linguagem R utilizando o módulo Execute R Script.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218043"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (clássico): Prolongue a sua experiência com R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Pode estender a funcionalidade do Azure Machine Learning Studio (clássico) através da linguagem R utilizando o módulo [Execute R Script.][execute-r-script]

Este módulo aceita múltiplos conjuntos de dados de entrada e produz um único conjunto de dados como saída. Pode digitar um script R no parâmetro **R Script** do módulo Execute [R Script.][execute-r-script]

Acede a cada porta de entrada do módulo utilizando código semelhante ao seguinte:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Listando todos os pacotes atualmente instalados
A lista de pacotes instalados pode ser alterada. Uma lista de pacotes atualmente instalados pode ser encontrada em [Pacotes R Suportados pelo Azure Machine Learning Studio (clássico)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Também pode obter a lista completa e atual de pacotes instalados, inserindo o seguinte código no módulo [Execute R Script:][execute-r-script]

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Isto envia a lista de pacotes para a porta de saída do módulo [Execute R Script.][execute-r-script]
Para visualizar a lista de pacotes, ligue um módulo de conversão como [Converter para CSV][convert-to-csv] à saída esquerda do módulo [Execute R Script,][execute-r-script] execute a experiência, clique na saída do módulo de conversão e selecione **Download**. 

![Descarregue a saída do módulo "Converta para CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Pacotes de importação
Pode importar pacotes que ainda não estejam instalados utilizando os seguintes comandos no módulo [Execute R Script:][execute-r-script]

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

onde `my_favorite_package.zip` o ficheiro contém o seu pacote.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
