---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126730"
---
## <a name="disable-email-verification"></a>Desativar a verificação do e-mail

Por predefinição, o Azure Ative Directory B2C (Azure AD B2C) verifica o endereço de e-mail do seu cliente para contas locais (contas para utilizadores que se inscrevam com endereço de e-mail ou nome de utilizador). O Azure AD B2C garante endereços de e-mail válidos, exigindo que os clientes os verifiquem durante o processo de inscrição. Também impede que atores maliciosos utilizem processos automatizados para gerar contas fraudulentas nas suas aplicações.

Alguns desenvolvedores de aplicações preferem ignorar a verificação de e-mail durante o processo de inscrição e, em vez disso, os clientes verificarem o seu endereço de e-mail mais tarde. Para suportar isto, o Azure AD B2C pode ser configurado para desativar a verificação de e-mail. Fazê-lo cria um processo de inscrição mais suave e dá aos programadores a flexibilidade para diferenciar os clientes que verificaram o seu endereço de e-mail de clientes que não o tenham feito.

> [!WARNING]
> A desativação da verificação de e-mails no processo de inscrição pode levar ao spam. Se desativar a verificação de e-mail fornecida por Acesso AD B2C por predefinido, recomendamos que implemente um sistema de verificação de substituição.
