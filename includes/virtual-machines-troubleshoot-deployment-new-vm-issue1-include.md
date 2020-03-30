---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184257"
---
## <a name="issue-custom-image-provisioning-errors"></a>Emissão: Imagem personalizada; erros de provisionamento
Os erros de provisionamento surgem se carregar ou capturar uma imagem VM generalizada como uma imagem vm especializada ou vice-versa. O primeiro irá causar um erro de tempo de compensação e este provocará uma falha no fornecimento. Para implementar a sua imagem personalizada sem erros, deve certificar-se de que o tipo de imagem não se altera durante o processo de captura.

A tabela seguinte lista as possíveis combinações de imagens generalizadas e especializadas, o tipo de erro que encontrará e o que precisa de fazer para corrigir os erros.

