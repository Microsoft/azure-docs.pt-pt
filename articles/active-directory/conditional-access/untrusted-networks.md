---
title: Como requerer a autenticação multifator (MFA) para acesso a partir de redes não fidedignas com acesso condicional do Azure Active Directory (Azure AD) | Documentos da Microsoft
description: Saiba como configurar uma política de acesso condicional no Azure Active Directory (Azure AD) para tentativas de acesso a partir de redes não confiáveis.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2018
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebf8cb1c3b49975356389c7c9444aff319326f74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301128"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Como: Exigir a MFA para acesso a partir de redes não fidedignas com acesso condicional   

O Azure Active Directory (Azure AD) permite início de sessão único para dispositivos, aplicações e serviços de qualquer lugar. Os utilizadores podem aceder a suas aplicações na cloud não apenas da rede da sua organização, mas também de qualquer local de Internet não fidedigno. É melhor prática comum para acesso a partir de redes não fidedignas exigir autenticação multifator (MFA).

Este artigo dá-lhe as informações necessárias configurar uma política de acesso condicional que exige o MFA para acesso a partir de redes não fidedignas. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com: 

- O [conceitos básicos](overview.md) de acesso condicional do Azure AD 
- O [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure



## <a name="scenario-description"></a>Descrição do cenário

Dominar o equilíbrio entre segurança e produtividade, poderá ser suficiente para que possa apenas exigir uma palavra-passe para inícios de sessão de rede da sua organização. No entanto, para obter acesso a partir de uma localização de rede não fidedigna, há um maior risco de inícios de sessão não são realizadas por utilizadores legítimos. Para abordar esta questão, pode bloquear o acesso a partir de redes não fidedignas. Em alternativa, pode também exigir a autenticação multifator (MFA) para obter a garantia de adicional que foi efetuada uma tentativa pelo legítimo proprietário da conta. 

Com acesso condicional do Azure AD, pode solucionar esse requisito com uma única política que concede acesso: 

- Para aplicações na cloud selecionada

- Para utilizadores e grupos selecionados  

- Exigir autenticação multifator 

- Quando o acesso é originado de: 

    - Uma localização que não é fidedigna


## <a name="implementation"></a>Implementação

O desafio desse cenário é traduzir *acesso a partir de uma localização de rede não fidedigna* numa condição de acesso condicional. Na política de acesso condicional, pode configurar o [condição de localizações](location-condition.md) cenários relacionados com a localizações de rede. A condição de localizações permite-lhe selecionar localizações com nome, o que são agrupamentos lógicos de intervalos de endereços IP, países e regiões.  

Normalmente, a sua organização é proprietária um ou mais intervalos de endereços, por exemplo, 199.30.16.0 - 199.30.16.24.
Pode configurar um local nomeado por:

- Especificar este intervalo (199.30.16.0/24) 

- Atribuir um nome descritivo como **rede empresarial** 


Em vez de tentar definir o que todos os locais são que não são confiáveis, pode:

- Incluir qualquer localização 

    ![Acesso condicional](./media/untrusted-networks/02.png)

- Excluir todas as localizações fidedignas 

    ![Acesso condicional](./media/untrusted-networks/01.png)



## <a name="policy-deployment"></a>Implementação da política

Com a abordagem descrita neste artigo, pode configurar uma política de acesso condicional para localizações não fidedignas. Para certificar-se de que a política funciona conforme esperado, a prática recomendada é testá-lo antes de a disponibilizar para produção. O ideal é que utilize um inquilino de teste para verificar se a sua nova política funciona como pretendido. Para obter mais informações, consulte [como implementar uma nova política](best-practices.md#how-should-you-deploy-a-new-policy). 



## <a name="next-steps"></a>Passos Seguintes

Se gostaria de saber mais sobre o acesso condicional, consulte o artigo [o que é o acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
