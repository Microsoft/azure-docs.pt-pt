---
title: 'Converter em CSV: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo converter para CSV no serviço de Azure Machine Learning para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 2b8f235014efe440e518f1f9c4d0d454f6918955
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128905"
---
# <a name="convert-to-csv-module"></a>Converter em módulo CSV

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados por vírgulas", é um formato de arquivo usado por muitas ferramentas de aprendizado de máquina externas. CSV é um formato de intercâmbio comum ao trabalhar com linguagens de software livre, como R ou Python.

Mesmo que você faça a maior parte de seu trabalho no Azure Machine Learning, há ocasiões em que você pode achar útil converter seu conjunto de seus conjuntos de seus para CSV para uso em ferramentas externas. Por exemplo:

+ Baixe o arquivo CSV para abri-lo com o Excel ou importe-o para um banco de dados relacional.  
+ Salve o arquivo CSV no armazenamento em nuvem e conecte-o de Power BI para criar visualizações.  
+ Use o formato CSV para preparar dados para uso em R e Python. Basta clicar com o botão direito do mouse na saída do módulo para gerar o código necessário para acessar os dados diretamente do Python ou de um notebook Jupyter. 

Quando você converte um conjunto de um em CSV, o arquivo é salvo em seu espaço de trabalho do Azure ML. Você pode usar um utilitário de armazenamento do Azure para abrir e usar o arquivo diretamente, ou pode clicar com o botão direito do mouse na saída do módulo e baixar o arquivo CSV em seu computador ou usá-lo em código R ou Python.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar converter em CSV

1.  Adicione o módulo [converter em CSV](./convert-to-csv.md) ao seu experimento. Você pode encontrar esse módulo no grupo de conversões de **formato de dados** na interface. 

2. Conecte-o a qualquer módulo que produza um conjunto de um DataSet.   
  
3.  Execute a experimentação.

### <a name="results"></a>Resultados
  

Clique duas vezes na saída de [converter em CSV](./convert-to-csv.md)e selecione uma destas opções.  

 + **Conjunto de resultados-> download**: Abre imediatamente uma cópia dos dados no formato CSV que você pode salvar em uma pasta local. Se você não especificar uma pasta, um nome de arquivo padrão será aplicado e o arquivo CSV será salvo na biblioteca de **downloads** local.


 + **Conjunto de > de resultados – salvar como DataSet**: Salva o arquivo CSV de volta no espaço de trabalho do Azure ML como um conjunto de uma separado.

 + **Gerar código de acesso a dados**: O Azure ML gera dois conjuntos de código para você acessar os dados, usando Python ou o R. Para acessar os dados, copie o trecho de código para seu aplicativo. (*Gerar código de acesso a dados será colocado em breve.* )

## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 