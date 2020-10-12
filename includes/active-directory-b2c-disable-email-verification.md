---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79126730"
---
## <a name="disable-email-verification"></a>Desativar a verificação do e-mail

Por padrão, o Azure Ative Directory B2C (Azure AD B2C) verifica o endereço de e-mail do seu cliente para contas locais (contas para utilizadores que se inscrevam com endereço de e-mail ou nome de utilizador). O Azure AD B2C garante endereços de e-mail válidos, exigindo que os clientes os verifiquem durante o processo de inscrição. Também impede que atores maliciosos utilizem processos automatizados para gerar contas fraudulentas nas suas aplicações.

Alguns desenvolvedores de aplicações preferem ignorar a verificação de e-mail durante o processo de inscrição e, em vez disso, fazer com que os clientes verifiquem o seu endereço de e-mail mais tarde. Para o suportar, o Azure AD B2C pode ser configurado para desativar a verificação de e-mail. Ao fazê-lo, cria-se um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade para diferenciar os clientes que verificaram o seu endereço de e-mail de clientes que não o fizeram.

> [!WARNING]
> A desativação da verificação de emails no processo de inscrição pode levar a spam. Se desativar a verificação de e-mail fornecida por Azure AD B2C, recomendamos que implemente um sistema de verificação de substituição.
