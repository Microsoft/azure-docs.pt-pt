---
title: Como exigir a MFA (autenticação multifator) para acesso de redes não confiáveis com o acesso condicional do Azure Active Directory (Azure AD) | Microsoft Docs
description: Saiba como configurar uma política de acesso condicional no Azure Active Directory (Azure AD) para para tentativas de acesso de redes não confiáveis.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec09c1ecb94a5ae189317d89cce4bc8f279b48
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175696"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Como: exigir MFA para acesso de redes não confiáveis com acesso condicional   

O Azure Active Directory (AD do Azure) habilita o logon único para dispositivos, aplicativos e serviços de qualquer lugar. Os usuários podem acessar seus aplicativos de nuvem não apenas da rede da sua organização, mas também de qualquer local de Internet não confiável. Uma prática recomendada comum para o acesso de redes não confiáveis é exigir a MFA (autenticação multifator).

Este artigo fornece as informações necessárias para configurar uma política de acesso condicional que requer MFA para acesso de redes não confiáveis. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) do acesso condicional do Azure AD 
- As [práticas recomendadas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure

## <a name="scenario-description"></a>Descrição do cenário

Para dominar o equilíbrio entre segurança e produtividade, pode ser suficiente para que você precise apenas de uma senha para entradas da rede da sua organização. No entanto, para acesso de um local de rede não confiável, há um risco maior de que as entradas não são executadas por usuários legítimos. Para resolver essa preocupação, você pode bloquear o acesso de redes não confiáveis. Como alternativa, você também pode exigir a MFA (autenticação multifator) para obter uma garantia adicional de que uma tentativa foi feita pelo proprietário legítimo da conta. 

Com o acesso condicional do Azure AD, você pode atender a esse requisito com uma única política que concede acesso: 

- Para aplicativos de nuvem selecionados
- Para usuários e grupos selecionados  
- Exigindo a autenticação multifator 
- Quando o acesso é originado em: 
   - Um local que não é confiável

## <a name="implementation"></a>Implementação

O desafio desse cenário é converter o *acesso de um local de rede não confiável* em uma condição de acesso condicional. Em uma política de acesso condicional, você pode configurar a [condição de locais](location-condition.md) para tratar de cenários relacionados a locais de rede. A condição locais permite que você selecione locais nomeados, que são agrupamentos lógicos de intervalos de endereços IP, países e regiões.  

Normalmente, sua organização possui um ou mais intervalos de endereços, por exemplo, 199.30.16.0-199.30.16.15.
Você pode configurar um local nomeado por:

- Especificando esse intervalo (199.30.16.0/28) 
- Atribuindo um nome descritivo, como **rede corporativa** 

Em vez de tentar definir quais locais não são confiáveis, você pode:

- Incluir qualquer local 

   ![Acesso Condicional](./media/untrusted-networks/02.png)

- Excluir todos os locais confiáveis 

   ![Acesso Condicional](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Implantação de política

Com a abordagem descrita neste artigo, agora você pode configurar uma política de acesso condicional para locais não confiáveis. Para garantir que sua política funcione conforme o esperado, a melhor prática recomendada é testá-la antes de distribuí-la para produção. O ideal é usar um locatário de teste para verificar se a nova política funciona como esperado. Para obter mais informações, consulte [como implantar uma nova política](best-practices.md#how-should-you-deploy-a-new-policy). 

## <a name="next-steps"></a>Passos seguintes

Se você quiser saber mais sobre o acesso condicional, consulte [o que é o acesso condicional no Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
