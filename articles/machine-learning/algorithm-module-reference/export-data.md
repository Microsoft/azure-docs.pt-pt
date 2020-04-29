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
ms.date: 02/22/2020
ms.openlocfilehash: 807771fd4018c9666f059c965370ebc36d0105df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79456306"
---
# <a name="export-data-module"></a>Módulo de Dados de Exportação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para economizar resultados, dados intermédios e dados de trabalho dos seus oleodutos em destinos de armazenamento em nuvem. 

Este módulo suporta a exportação dos seus dados para os seguintes serviços de dados na nuvem:

- Recipiente de blob azure
- Partilha de Ficheiros do Azure
- Azure Data Lake
- Lago de Dados Azure Gen2

Antes de exportar os seus dados, precisa primeiro de registar uma loja de dados no seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte os dados do Access nos serviços de [armazenamento do Azure.](../how-to-access-data.md)

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo De dados de **exportação** ao seu pipeline no designer. Pode encontrar este módulo na categoria **De entrada e saída.**

1. Ligue **os Dados de Exportação** ao módulo que contém os dados que pretende exportar.

1. Selecione **Dados de Exportação** para abrir o painel **Properties.**

1. Para **datastore,** selecione uma loja de dados existente na lista de dropdown. Também pode criar uma nova loja de dados. Verifique como visitando dados do Access nos serviços de [armazenamento do Azure.](../how-to-access-data.md)

1. A caixa de verificação, regerar a **saída,** decide se executa o módulo para regenerar a saída no tempo de funcionamento. 

    É por padrão não selecionado, o que significa que se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de funcionar. 

    Se for selecionado, o sistema executará novamente o módulo para regenerar a saída.

1. Defina o caminho na loja de dados onde estão os dados. O caminho é um caminho relativo. Os caminhos vazios ou os caminhos de URL não são permitidos.


1. Para **o formato Ficheiro,** selecione o formato em que os dados devem ser armazenados.
 
1. Submeta o oleoduto.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
