---
title: 'Dados de Exportação: Referência do Módulo'
titleSuffix: Azure Machine Learning
description: Saiba como utilizar o módulo de Dados de Exportação em Aprendizagem Automática Azure para guardar resultados, dados intermédios e dados de trabalho dos seus oleodutos para destinos de armazenamento em nuvem fora do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 11f5bd7f01e142273509ae59ddc19a2557464bde
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152317"
---
# <a name="export-data-module"></a>Módulo de Dados de Exportação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para economizar resultados, dados intermédios e dados de trabalho dos seus oleodutos em destinos de armazenamento em nuvem fora do Azure Machine Learning. 

Este módulo suporta a exportação dos seus dados para os seguintes serviços de dados na nuvem:

- Recipiente de blob azure
- Partilha de Ficheiros do Azure
- Azure Data Lake
- Lago de Dados Azure Gen2

Antes de exportar os seus dados, primeiro tem de registar uma loja de dados no seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte os dados do Access nos serviços de [armazenamento do Azure.](../how-to-access-data.md)

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo De dados de **exportação** ao seu pipeline no designer. Pode encontrar este módulo na categoria **De entrada e saída.**

1. Ligue **os Dados de Exportação** ao módulo que contém os dados que pretende exportar.

1. Selecione **Dados de Exportação** para abrir o painel **Properties.**

1. Para **datastore,** selecione uma loja de dados existente na lista de dropdown. Também pode criar uma nova loja de dados. Verifique como visitando dados do Access nos serviços de [armazenamento do Azure.](../how-to-access-data.md)

1. Defina o caminho na loja de dados para escrever os dados. 


1. Para **o formato Ficheiro,** selecione o formato em que os dados devem ser armazenados.
 
1. Executar o pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
