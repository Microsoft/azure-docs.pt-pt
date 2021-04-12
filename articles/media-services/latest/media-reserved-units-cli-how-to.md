---
title: Unidades reservadas de mídia de escala (MRUs) CLI
description: Este tópico mostra como usar o CLI para escalar o processamento de mídia com a Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: c5fa3aa8397ea6e13500717f035c414af8de8e3d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121681"
---
# <a name="how-to-scale-media-reserved-units"></a>Como escalar unidades reservadas para os meios de comunicação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como escalar Unidades Reservadas de Mídia (MRSs) para uma codificação mais rápida.

> [!WARNING]
> Este comando deixará de funcionar para as contas dos Media Services que são criadas com a versão 2020-05-01 da API ou posterior. Para estas contas, as unidades reservadas aos meios de comunicação já não são necessárias, uma vez que o sistema irá automaticamente aumentar e descer automaticamente com base na carga. Se não vir a opção de gerir as MRUs no portal Azure, está a utilizar uma conta que foi criada com a API 2020-05-01 ou mais tarde.

## <a name="prerequisites"></a>Pré-requisitos

[Criar uma conta de Serviços de Comunicação](./account-create-how-to.md)Social.

Compreender [unidades reservadas de mídia.](concept-media-reserved-units.md)

## <a name="scale-media-reserved-units-with-cli"></a>Unidades reservadas para mídia de escala com CLI

Execute o comando `mru`.

O comando [mru da conta AZ AMS](/cli/azure/ams/account/mru) define As Unidades Reservadas de Mídia na conta "amsaccount" utilizando os parâmetros **de contagem** e **tipo.**

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Faturação

É cobrado com base no número de minutos que as Unidades Reservadas de Mídia estão a ser adsteadas na sua conta. Isto ocorre independentemente de existir algum Jobs a decorrer na sua conta. Para obter uma explicação detalhada, consulte a secção de PERGUNTAS Frequentes da página [de preços dos Serviços de Comunicação Social.](https://azure.microsoft.com/pricing/details/media-services/)   

## <a name="next-step"></a>Passo seguinte

[Analisar vídeos](analyze-videos-tutorial.md)

## <a name="see-also"></a>Ver também

* [Quotas e limites](limits-quotas-constraints-reference.md)
