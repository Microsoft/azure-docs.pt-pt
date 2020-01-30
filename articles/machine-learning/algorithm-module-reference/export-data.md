---
title: 'Exportar dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo exportar dados no Azure Machine Learning para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: c83bfc4a3ec8d1163669f28dfde64c63e7c6ff89
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775077"
---
# <a name="export-data-module"></a>Exportar módulo de dados

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning. 

Este módulo dá suporte à exportação de dados para os seguintes serviços de dados de nuvem:

- Contêiner de blob do Azure
- Partilha de Ficheiros do Azure
- Azure Data Lake
- Azure Data Lake Gen2

Antes de exportar seus dados, primeiro você precisa registrar um armazenamento de um datastore em seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte os dados do Access nos serviços de [armazenamento do Azure.](../how-to-access-data.md)

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo **exportar dados** ao seu pipeline no designer. Você pode encontrar esse módulo na categoria **entrada e saída** .

1. Conecte **dados de exportação** ao módulo que contém os dados que você deseja exportar.

1. Selecione **exportar dados** para abrir o painel **Propriedades** .

1. Para o **repositório de armazenamento**, selecione um repositório de armazenamento existente na lista suspensa. Você também pode criar um novo repositório de armazenamento. Verifique como visitando dados do Access nos serviços de [armazenamento do Azure.](../how-to-access-data.md)

1. Defina o caminho no repositório de dados no qual gravar o dado. 


1. Para **formato de arquivo**, selecione o formato no qual os dados devem ser armazenados.
 
1. Executar o pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
