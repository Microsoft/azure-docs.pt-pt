---
title: 'Converter em CSV: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Converte-se em CSV no designer de Aprendizagem automática Azure para converter um conjunto de dados num ficheiro CSV que pode ser reutilizado mais tarde.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421928"
---
# <a name="convert-to-csv-module"></a>Converter para módulo CSV

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para converter um conjunto de dados num formato CSV que possa ser descarregado, exportado ou partilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados por vírgula", é um formato de ficheiro utilizado por muitas ferramentas externas de aprendizagem automática. CSV é um formato de intercâmbio comum quando se trabalha com línguas de código aberto, como R ou Python.

Mesmo que faça a maior parte do seu trabalho em Azure Machine Learning, há momentos em que poderá achar útil converter o seu conjunto de dados em CSV para usar em ferramentas externas. Por exemplo:

+ Faça o download do ficheiro CSV para o abrir com o Excel, ou importe-o numa base de dados relacional.  
+ Guarde o ficheiro CSV para o armazenamento em nuvem e conecte-o a partir do Power BI para criar visualizações.  
+ Utilize o formato CSV para preparar dados para utilização em R e Python. 

Quando converte um conjunto de dados para CSV, o csv é guardado no seu espaço de trabalho Azure ML. Pode utilizar um utilitário de armazenamento Azure para abrir e utilizar o ficheiro diretamente. Também pode aceder ao CSV no designer selecionando o módulo **Convertendo para CSV** e, em seguida, selecione o ícone de histograma no separador **Saídas** no painel direito para visualizar a saída. Você pode baixar o CSV da pasta Resultados para um diretório local.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar Converter em CSV


1.  Adicione o módulo Convertendo-o em CSV ao seu oleoduto. Pode encontrar este módulo no grupo **de Transformação de Dados** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   
  
3.  Envie o oleoduto.

### <a name="results"></a>Resultados
  

Selecione o separador **Saídas** no painel direito de **Converter para CSV** e selecione num destes ícones sob as **saídas** da Porta .  

+ **Registar conjunto de dados**: Selecione o ícone e guarde o ficheiro CSV de volta para o espaço de trabalho Azure ML como um conjunto de dados separado. Pode encontrar o conjunto de dados como um módulo na árvore do módulo sob a secção **My Datasets.**

 + **Ver saída**: Selecione o ícone do olho e siga as instruções para navegar na pasta **Results_dataset** e descarregue o ficheiro data.csv.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 