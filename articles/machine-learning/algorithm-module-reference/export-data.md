---
title: 'Dados de Exportação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de Dados de Exportação em Azure Machine Learning para guardar resultados, dados intermédios e dados de trabalho dos seus oleodutos em destinos de armazenamento em nuvem fora do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/28/2020
ms.openlocfilehash: 904b3ce1c2d05d713ee1ae99662148217f2a358e
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337828"
---
# <a name="export-data-module"></a>Módulo de Dados de Exportação

Este artigo descreve um módulo no designer de Aprendizagem automática Azure (pré-visualização).

Utilize este módulo para guardar resultados, dados intermédios e dados de trabalho dos seus oleodutos em destinos de armazenamento em nuvem. 

Este módulo suporta a exportação dos seus dados para os seguintes serviços de dados em nuvem:

- Recipiente Azure Blob
- Partilha de Ficheiros do Azure
- Azure Data Lake
- Azure Data Lake Gen2
- Base de dados SQL do Azure

Antes de exportar os seus dados, tem de registar primeiro uma loja de dados no seu espaço de trabalho Azure Machine Learning. Para mais informações, consulte [os dados do Access nos serviços de armazenamento Azure.](../how-to-access-data.md)

## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo **de Dados de Exportação** ao seu pipeline no designer. Pode encontrar este módulo na categoria **Entrada e Saída.**

1. Ligue **os Dados de Exportação** ao módulo que contém os dados que pretende exportar.

1. Selecione **Dados de Exportação** para abrir o painel **de propriedades.**

1. Para **datastore,** selecione uma datastore existente da lista de dropdown. Também pode criar uma nova loja de dados. Verifique como visita os [dados do Access nos serviços de armazenamento Azure.](../how-to-access-data.md)

    > [!NOTE]
    > Não são suportados dados de exportação de um determinado tipo de dados para uma coluna de base de dados SQL especificada como outro tipo de dados.

1. A caixa de verificação, **Regenerar a produção,** decide se executa o módulo para regenerar a saída em tempo de execução. 

    É por defeito não selecionado, o que significa que se o módulo tiver sido executado com os mesmos parâmetros anteriormente, o sistema reutilizará a saída da última execução para reduzir o tempo de funcionamento. 

    Se for selecionado, o sistema executará novamente o módulo para regenerar a saída.

1. Defina o caminho na datastore onde os dados estão. O caminho é um caminho relativo. Os caminhos vazios ou os caminhos de URL não são permitidos.


1. Para **o formato Ficheiro**, selecione o formato em que os dados devem ser armazenados.
 
1. Envie o oleoduto.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
