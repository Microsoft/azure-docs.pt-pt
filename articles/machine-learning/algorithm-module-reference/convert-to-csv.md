---
title: 'Converter em CSV: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo converter para CSV no Azure Machine Learning para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 30d1ba89f6a18ed3400868e6d9a5d17ceef5de04
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546814"
---
# <a name="convert-to-csv-module"></a>Converter em módulo CSV

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados por vírgulas", é um formato de arquivo usado por muitas ferramentas de aprendizado de máquina externas. CSV é um formato de intercâmbio comum ao trabalhar com linguagens de software livre, como R ou Python.

Mesmo que você faça a maior parte de seu trabalho no Azure Machine Learning, há ocasiões em que você pode achar útil converter seu conjunto de seus conjuntos de seus para CSV para uso em ferramentas externas. Por exemplo:

+ Baixe o arquivo CSV para abri-lo com o Excel ou importe-o para um banco de dados relacional.  
+ Salve o arquivo CSV no armazenamento em nuvem e conecte-o de Power BI para criar visualizações.  
+ Use o formato CSV para preparar dados para uso em R e Python. 

Quando você converte um conjunto de um em CSV, o CSV é salvo em seu espaço de trabalho do Azure ML. Você pode usar um utilitário de armazenamento do Azure para abrir e usar o arquivo diretamente. Você também pode acessar o CSV no designer selecionando o módulo **converter em CSV** e, em seguida, selecionando o ícone de histograma na guia **saídas** no painel direito para exibir a saída. Você pode baixar o CSV da pasta de resultados para um diretório local.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar converter em CSV


1.  Adicione o módulo converter em CSV ao seu pipeline. Você pode encontrar esse módulo no grupo de **transformação de dados** no designer. 

2. Conecte-o a qualquer módulo que produza um conjunto de um DataSet.   
  
3.  Executar o pipeline.

### <a name="results"></a>Resultados
  

Selecione a guia **saídas** no painel direito de **converter em CSV**e selecione em um desses ícones sob as saídas de **porta**.  

+ **Registrar conjunto de registros**: selecione o ícone e salve o arquivo CSV de volta no espaço de trabalho do Azure ml como um conjunto de uma separado. Você pode encontrar o conjunto de módulos como um módulo na árvore de módulo na seção **meus conjuntos de valores** .

 + **Exibir saída**: selecione o ícone de olho e siga a instrução para procurar a pasta **Results_dataset** e baixe o arquivo data. csv.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 