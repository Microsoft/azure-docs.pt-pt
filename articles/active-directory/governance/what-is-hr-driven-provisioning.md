---
title: O que é o fornecimento de RH com o Azure Ative Directory? | Microsoft Docs
description: Descreve a visão geral do fornecimento conduzido por RH.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaf033f7da7a5bd4882d0c78a2321640aa64d8ac
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646564"
---
# <a name="what-is-hr-driven-provisioning"></a>O que é o fornecimento conduzido pela HR?

![Fornecimento de RH](./media/what-is-hr-driven-provisioning/cloud2a.png)

O fornecimento impulsionado por RH é o processo de criação de identidades digitais baseadas num sistema de recursos humanos.  Os sistemas de RH, tornam-se o início de autoridade para estas identidades digitais recém-criadas e são muitas vezes o ponto de partida para inúmeros processos de provisionamento.  Por exemplo, se um novo funcionário se juntar à sua empresa, eles são criados no sistema de recursos humanos.  A criação, desencadeia o fornecimento de uma conta de utilizador no Ative Directory e, em seguida, a Azure AD Connect fornece esta conta para Azure AD, etc.

O fornecimento orientado por RH pode ser baseado no local ou baseado em nuvem.

## <a name="on-premises-based-hr-provisioning"></a>Fornecimento de RH no local
O fornecimento de RH no local é realizado utilizando um sistema de RH local e um meio de fornecimento de novas identidades digitais.

Os sistemas de RH vêm em uma variedade de pacotes, pacotes de software e podem usar servidores SQL, diretórios LDAP, etc.

Atualmente, as soluções de provisionamento hr da Microsoft no local utilizam o Microsoft Identity Manager para desencadear o provisionamento quando uma nova identidade é criada nestes sistemas de RH.

Utilizando o MIM, pode forissar os utilizadores dos seus sistemas HR no local para o Ative Directory ou Azure AD.

Para obter informações sobre o Microsoft Identity Manager e os sistemas que suporta consulte a documentação do [Gestor de Identidade da Microsoft.](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016)

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Próximos passos 
- [O que é a gestão do ciclo de vida da identidade](what-is-identity-lifecycle-management.md)
- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento de aplicações?](what-is-app-provisioning.md)
- [O que é o aprovisionamento entre diretórios?](what-is-inter-directory-provisioning.md)
