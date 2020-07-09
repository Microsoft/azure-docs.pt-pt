---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184257"
---
## <a name="issue-custom-image-provisioning-errors"></a>Emissão: Imagem personalizada; erros de provisionamento
Erros de provisionamento surgem se carregar ou capturar uma imagem de VM generalizada como uma imagem VM especializada ou vice-versa. O primeiro provocará um erro de tempo limite de provisionamento e este último provocará uma falha no provisionamento. Para implementar a sua imagem personalizada sem erros, deve certificar-se de que o tipo de imagem não muda durante o processo de captura.

A tabela que se segue lista as possíveis combinações de imagens generalizadas e especializadas, o tipo de erro que vai encontrar e o que precisa de fazer para corrigir os erros.

