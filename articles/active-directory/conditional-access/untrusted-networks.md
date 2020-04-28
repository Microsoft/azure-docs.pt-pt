---
title: Exigir MFA de redes não confiáveis - Diretório Ativo Azure
description: Saiba como configurar uma política de acesso condicional no Azure Ative Directory (Azure AD) para tentativas de acesso a redes não confiáveis.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74379997"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Como: Exigir MFA para acesso a partir de redes não confiáveis com Acesso Condicional   

O Azure Ative Directory (Azure AD) permite um único início de sessão em dispositivos, aplicações e serviços de qualquer lugar. Os seus utilizadores podem aceder às suas aplicações na nuvem não só a partir da rede da sua organização, mas também de qualquer localização não confiável da Internet. Uma boa prática comum para o acesso a partir de redes não confiáveis é exigir a autenticação de vários fatores (MFA).

Este artigo dá-lhe a informação necessária para configurar uma política de Acesso Condicional que requer mFA para acesso a partir de redes não confiáveis. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com: 

- Os [conceitos básicos](overview.md) de Acesso Condicional Azure AD 
- As [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal Azure

## <a name="scenario-description"></a>Descrição do cenário

Para dominar o equilíbrio entre segurança e produtividade, pode ser suficiente para que apenas necessite de uma senha para os insines da rede da sua organização. No entanto, para acesso a partir de uma localização de rede não confiável, existe um risco acrescido de os sign-ins não serem realizados por utilizadores legítimos. Para resolver esta preocupação, pode bloquear o acesso a redes não confiáveis. Em alternativa, também pode exigir a autenticação de vários fatores (MFA) para recuperar a garantia adicional de que foi feita uma tentativa pelo legítimo proprietário da conta. 

Com o Acesso Condicional Azure AD, pode abordar este requisito com uma única política que concede acesso: 

- Para aplicativos de nuvem selecionados
- Para utilizadores e grupos selecionados  
- Requerendo a autenticação de vários fatores 
- Quando o acesso é originado a partir de: 
   - Um local que não é confiável

## <a name="implementation"></a>Implementação

O desafio deste cenário é traduzir o acesso de uma localização de *rede não confiável* para uma condição de Acesso Condicional. Numa política de Acesso Condicional, pode configurar a condição de [localização](location-condition.md) para abordar cenários relacionados com as localizações da rede. A condição de localização permite selecionar locais nomeados, que são agrupamentos lógicos de gamas de endereços IP, países e regiões.  

Normalmente, a sua organização possui uma ou mais gamas de endereços, por exemplo, 199.30.16.0 - 199.30.16.15.
Pode configurar uma localização nomeada por:

- Especificando esta gama (199.30.16.0/28) 
- Atribuir um nome descritivo, como **Rede Corporativa** 

Em vez de tentar definir quais são todos os locais que não são confiáveis, pode:

- Incluir qualquer localização 

   ![Acesso Condicional](./media/untrusted-networks/02.png)

- Excluir todos os locais confiáveis 

   ![Acesso Condicional](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Implementação da Política

Com a abordagem descrita neste artigo, pode agora configurar uma política de Acesso Condicional para locais não confiáveis. Para garantir que a sua política funcione como esperado, a melhor prática recomendada é testá-la antes de a lançar em produção. Idealmente, use um inquilino de teste para verificar se a sua nova política funciona como pretendido. Para mais informações, consulte [como implementar uma nova política.](best-practices.md#how-should-you-deploy-a-new-policy) 

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o Acesso Condicional, veja [o que é o Acesso Condicional no Diretório Ativo Azure?](../active-directory-conditional-access-azure-portal.md)
