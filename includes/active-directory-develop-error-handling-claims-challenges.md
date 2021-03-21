---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760653"
---
## <a name="conditional-access-and-claims-challenges"></a>Acesso condicional e reclama desafios

Ao receber tokens silenciosamente, a sua aplicação pode receber erros quando um [desafio de reclamações de acesso condicional,](../articles/active-directory/develop/v2-conditional-access-dev-guide.md) como a política de MFA, é exigido por uma API que está a tentar aceder.

O padrão para lidar com este erro é adquirir interativamente um símbolo usando o MSAL. Isto incita o utilizador e dá-lhes a oportunidade de satisfazer a política de acesso condicional exigida.

Em certos casos, ao ligar para uma API que requer acesso condicional, pode receber um desafio de reclamações no erro da API. Por exemplo, se a política de Acesso Condicional pretender ter um dispositivo gerido (Intune) o erro será algo como [AADSTS53000: O seu dispositivo é obrigado a ser gerido para aceder a este recurso](../articles/active-directory/develop/reference-aadsts-error-codes.md) ou algo similar. Neste caso, pode passar as reclamações na chamada de ficha de aquisição para que o utilizador seja solicitado para satisfazer a política adequada.
