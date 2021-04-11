---
title: Introdução à garantia de contas de serviço do Azure Ative Directory
description: Explicação dos tipos de contas de serviço disponíveis no Diretório Ativo Azure.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d657f1df14b083631227cb7c19f64b65be8801d0
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107010470"
---
# <a name="introduction-to-securing-azure-service-accounts"></a>Introdução à garantia de contas de serviço da Azure

Existem três tipos de contas de serviço nativas do Azure Ative Directory: Identidades geridas, princípios de serviço e contas de serviço baseadas no utilizador. As contas de serviço são um tipo especial de conta que se destina a representar uma entidade não humana, como uma aplicação, API ou outro serviço. Estas entidades operam no contexto de segurança prestado pela conta de serviço. 

## <a name="types-of-azure-active-directory-service-accounts"></a>Tipos de contas de serviço do Azure Ative Directory

Para serviços hospedados em Azure, recomendamos a utilização de uma identidade gerida, se possível, e um diretor de serviço, se não. Identidades geridas não podem ser usadas para serviços alojados fora de Azure. Nesse caso, recomendamos um diretor de serviço. Se puder utilizar uma identidade gerida ou um principal de serviço, faça-o. Recomendamos que não utilize uma conta de utilizador do Azure Ative Directory como conta de serviço. Consulte a seguinte tabela para obter um resumo.
 

| Hospedagem de serviço| Identidade gerida| Service principal (Principal de serviço)| Conta de utilizador Azure |
| - | - | - | - |
|O serviço está hospedado em Azure.| Sim. <br>Recomendado se o serviço <br>apoia uma Identidade Gerida.| Sim.| Não recomendada. |
| O serviço não está hospedado em Azure.| Não| Yes. Recomendado.| Não recomendada. |
| O serviço é multi-inquilino| Não| Yes. Recomendado.| N.º |


## <a name="managed-identities"></a>Identidades geridas

As identidades geridas são identidades seguras do Azure Ative Directory (Azure AD) criadas para fornecer identidades para os recursos do Azure. Existem [dois tipos de identidades geridas:](../managed-identities-azure-resources/overview.md#managed-identity-types) 
 
* As identidades geridas atribuídas pelo sistema podem ser atribuídas diretamente a uma instância de um serviço. 

* As identidades geridas atribuídas pelo utilizador podem ser criadas como um recurso autónomo. 

Para obter mais informações, consulte [a Garantia de identidades geridas.](service-accounts-managed-identities.md) Para obter informações gerais sobre identidades geridas, veja [quais são as identidades geridas para os recursos do Azure?](../managed-identities-azure-resources/overview.md)

## <a name="service-principals"></a>Principais de serviço

Se não puder utilizar uma identidade gerida para representar a sua aplicação, utilize um responsável de serviço. Os principais serviços podem ser usados com pedidos individuais de inquilinos e multi-inquilinos. 

Um diretor de serviço é a representação local de um objeto de aplicação em um único inquilino AZure AD. Funciona como a identidade da instância de aplicação, define quem pode aceder à aplicação, e quais os recursos a que a aplicação pode aceder. Um diretor de serviço é criado em (local para) cada inquilino onde a aplicação é usada e faz referência ao objeto de aplicação globalmente único. O inquilino assegura a inscrição e o acesso aos recursos do diretor de serviço.

Existem dois mecanismos de autenticação utilizando os principais serviços — certificados de cliente e segredos de cliente. Os certificados são mais seguros: use certificados de cliente, se possível. Ao contrário dos segredos dos clientes, os certificados de cliente não podem ser incorporados acidentalmente em código.

Para obter informações sobre a segurança dos principais serviços, consulte [os principais de serviços de garantia.](service-accounts-principal.md)

 
## <a name="next-steps"></a>Passos seguintes


Para obter mais informações sobre a segurança das contas de serviço da Azure, consulte:

[Assegurar identidades geridas](service-accounts-managed-identities.md)

[Assegurar os principais de serviços](service-accounts-principal.md)

[Contas de serviço Azure governando](service-accounts-governing-azure.md)
