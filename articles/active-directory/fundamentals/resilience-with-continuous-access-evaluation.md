---
title: Construa resiliência utilizando a Avaliação contínua de Acesso no Diretório Ativo do Azure
description: Um guia para arquitetos e administradores de TI sobre a utilização do CAE
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d792c1eb0a85f2a898d1dc5c63047a4553f13b0
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106777"
---
# <a name="build-resilience-by-using-continuous-access-evaluation"></a>Construa resiliência utilizando a Avaliação contínua de Acesso

[A Avaliação contínua de Acesso](../conditional-access/concept-continuous-access-evaluation.md) (CAE) permite que as aplicações AD do Azure subscrevam eventos críticos que podem ser avaliados e aplicados. Isto inclui a avaliação dos seguintes eventos:

* A conta de utilizador a ser eliminada ou desativada

* A palavra-passe para um utilizador é alterada

* O MFA está ativado para o utilizador.

* O administrador revoga explicitamente um símbolo.

* É detetado um risco elevado de utilização.

Como resultado, as aplicações podem rejeitar fichas não úmidas com base nos eventos sinalizados pela Azure AD, como descrito no diagrama seguinte.

![conceptualização do CAE](./media/resilience-with-cae/admin-resilience-continuous-access-evaluation.png)

## <a name="how-does-cae-help"></a>Como é que a CAE ajuda?

Este mecanismo permite que a Azure AD emita fichas de vida mais longa, ao mesmo tempo que permite às aplicações uma forma de revogar o acesso e forçar a reaudição apenas quando necessário. O resultado líquido deste padrão é menos chamadas para adquirir fichas, o que significa que o fluxo de ponta a ponta é mais resistente. 

Para utilizar o CAE, tanto o serviço como o cliente devem ser capazes de CAE. Serviços Microsoft 365 como Exchange Online, Teams e SharePoint Online suportam CAE. Do lado do cliente, as experiências baseadas no navegador que utilizam estes serviços office 365 (por exemplo, Outlook Web App) e versões específicas do Office 365 clientes nativos são capazes de CAE. Mais serviços na nuvem da Microsoft tornar-se-ão capazes de CAE.

A Microsoft está a trabalhar com a indústria para criar [padrões](https://openid.net/wg/sse/) que permitam que aplicações de terceiros utilizem esta capacidade. Também pode desenvolver aplicações que sejam capazes de CAE. Veja como construir resiliência na sua aplicação para obter mais informações.

## <a name="how-do-i-implement-cae"></a>Como posso implementar o CAE?

* [Atualize o seu código para utilizar APIs ativados por CAE](../develop/app-resilience-continuous-access-evaluation.md).

* [Ativar o CAE](../conditional-access/concept-continuous-access-evaluation.md) na Configuração de Segurança Ad Azure.

* Certifique-se de que a sua organização está a utilizar [versões compatíveis](../conditional-access/concept-continuous-access-evaluation.md) de aplicações nativas do Microsoft Office.

* [Otimize as suas instruções de reautorca](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

 
## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com gestão credencial](resilience-in-credentials.md)

* [Construir resiliência com estados de dispositivo](resilience-with-device-states.md)

* [Construir resiliência na autenticação externa do utilizador](resilience-b2b-authentication.md)

* [Construa resiliência na sua autenticação híbrida](resilience-in-hybrid.md)

* [Criar resiliência no acesso à aplicação com Aplicação Proxy](resilience-on-premises-access.md)

Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)
