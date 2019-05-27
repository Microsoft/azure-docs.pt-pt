---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160331"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problema: Imagem personalizada; erros de aprovisionamento
Erros de aprovisionamento surgirem se carrega ou captura uma imagem VM generalizada como uma imagem VM especializada ou vice versa. O primeiro fará com que um erro de tempo limite de aprovisionamento e a última opção fará com que uma falha de aprovisionamento. Para implementar a sua imagem personalizada sem erros, tem de se certificar de que o tipo da imagem não é alterada durante o processo de captura.

A tabela seguinte lista as combinações possíveis de imagens generalizadas e especializadas, o tipo de erro que encontrará e o que precisa fazer para corrigir os erros.

