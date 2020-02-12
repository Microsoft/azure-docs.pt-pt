---
title: 'Converter em CSV: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo converter para CSV no Azure Machine Learning para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 8206a88695c89d04eabe89e79a5aff8469cc6862
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152436"
---
# <a name="convert-to-csv-module"></a>Converter em módulo CSV

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados por vírgulas", é um formato de arquivo usado por muitas ferramentas de aprendizado de máquina externas. CSV é um formato de intercâmbio comum ao trabalhar com linguagens de software livre, como R ou Python.

Mesmo que você faça a maior parte de seu trabalho no Azure Machine Learning, há ocasiões em que você pode achar útil converter seu conjunto de seus conjuntos de seus para CSV para uso em ferramentas externas. Por exemplo:

+ Baixe o arquivo CSV para abri-lo com o Excel ou importe-o para um banco de dados relacional.  
+ Salve o arquivo CSV no armazenamento em nuvem e conecte-o de Power BI para criar visualizações.  
+ Use o formato CSV para preparar dados para uso em R e Python. 

Quando você converte um conjunto de um em CSV, o CSV é salvo em seu espaço de trabalho do Azure ML. Você pode usar um utilitário de armazenamento do Azure para abrir e usar o arquivo diretamente. Também pode aceder ao CSV no designer selecionando o módulo **Convert e CSV** e, em seguida, selecione o ícone histograma sob o separador **Saídas** no painel certo para visualizar a saída. Você pode baixar o CSV da pasta de resultados para um diretório local.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar converter em CSV


1.  Adicione o módulo converter em CSV ao seu pipeline. Pode encontrar este módulo no grupo **de Transformação** de Dados no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de dados.   
  
3.  Executar o pipeline.

### <a name="results"></a>Resultados
  

Selecione o separador **Saídas** no painel direito de **Converter para CSV**, e selecione num destes ícones sob as **saídas**da Porta .  

+ **Registar**o conjunto de dados : Selecione o ícone e guarde o ficheiro CSV de volta ao espaço de trabalho Azure ML como um conjunto de dados separado. Pode encontrar o conjunto de dados como um módulo na árvore do módulo sob a secção **My Datasets.**

 + **Ver saída**: Selecione o ícone dos olhos e siga as instruções para navegar na pasta **Results_dataset** e descarregue o ficheiro data.csv.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 