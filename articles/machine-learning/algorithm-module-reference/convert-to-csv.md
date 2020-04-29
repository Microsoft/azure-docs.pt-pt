---
title: 'Converter para CSV: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo Convert para CSV em Azure Machine Learning para converter um conjunto de dados num formato CSV que pode ser descarregado, exportado ou partilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477685"
---
# <a name="convert-to-csv-module"></a>Converter para módulo CSV

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para converter um conjunto de dados num formato CSV que pode ser descarregado, exportado ou partilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados de vírem", é um formato de ficheiro utilizado por muitas ferramentas externas de aprendizagem automática. O CSV é um formato de intercâmbio comum quando se trabalha com línguas de código aberto, como R ou Python.

Mesmo que faça a maior parte do seu trabalho em Azure Machine Learning, há momentos em que pode achar útil converter o seu conjunto de dados em CSV para usar em ferramentas externas. Por exemplo:

+ Descarregue o ficheiro CSV para o abrir com o Excel, ou importe-o numa base de dados relacional.  
+ Guarde o ficheiro CSV para o armazenamento em nuvem e ligue-o a partir do Power BI para criar visualizações.  
+ Utilize o formato CSV para preparar dados para utilização em R e Python. 

Quando converte um conjunto de dados para CSV, o csv é guardado no seu espaço de trabalho Azure ML. Pode utilizar um utilitário de armazenamento Azure para abrir e utilizar o ficheiro diretamente. Também pode aceder ao CSV no designer selecionando o módulo **Convert e CSV** e, em seguida, selecione o ícone histograma sob o separador **Saídas** no painel certo para visualizar a saída. Pode transferir o CSV da pasta Resultados para um diretório local.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar Converter em CSV


1.  Adicione o módulo Converte ao módulo CSV ao seu pipeline. Pode encontrar este módulo no grupo **de Transformação** de Dados no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   
  
3.  Submeta o oleoduto.

### <a name="results"></a>Resultados
  

Selecione o separador **Saídas** no painel direito de **Converter para CSV**, e selecione num destes ícones sob as **saídas**da Porta .  

+ **Registar**o conjunto de dados : Selecione o ícone e guarde o ficheiro CSV de volta ao espaço de trabalho Azure ML como um conjunto de dados separado. Pode encontrar o conjunto de dados como um módulo na árvore do módulo sob a secção **My Datasets.**

 + **Ver saída**: Selecione o ícone dos olhos e siga as instruções para navegar na pasta **Results_dataset** e descarregue o ficheiro data.csv.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 