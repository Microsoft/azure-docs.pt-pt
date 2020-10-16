---
title: Exigir MFA de redes não fidedquicas - Azure Ative Directory
description: Saiba como configurar uma política de acesso condicional no Azure Ative Directory (Azure AD) para obter tentativas de acesso a redes não fidedignais.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b2f9a6e13fdc39ab18a2056fab15982aa9fb0e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948169"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Como: Exigir MFA para acesso a partir de redes não fidedquipantes com Acesso Condicional   

O Azure Ative Directory (Azure AD) permite um único sinal de inscrição para dispositivos, apps e serviços de qualquer lugar. Os seus utilizadores podem aceder às suas aplicações na nuvem não só a partir da rede da sua organização, mas também a partir de qualquer localização não fidedquiríssquia da Internet. Uma melhor prática comum para o acesso a partir de redes não fidedquisas é exigir a autenticação de vários fatores (MFA).

Este artigo dá-lhe a informação necessária para configurar uma política de Acesso Condicional que requer MFA para acesso a redes não fidedignáveis. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com: 

- Os [conceitos básicos](overview.md) de Acesso Condicional AD Azure 
- As [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal Azure

## <a name="scenario-description"></a>Descrição do cenário

Para dominar o equilíbrio entre segurança e produtividade, pode ser suficiente para você apenas exigir uma senha para iniciar s-ins da rede da sua organização. No entanto, para o acesso a partir de uma localização de rede não fidedquirada, existe um risco acrescido de os ins-ins não serem realizados por utilizadores legítimos. Para resolver esta preocupação, pode bloquear o acesso a redes não fidedquis. Em alternativa, também pode exigir a autenticação de vários fatores (MFA) para recuperar a garantia adicional de que foi feita uma tentativa pelo legítimo proprietário da conta. 

Com o Acesso Condicional Azure AD, pode endereçar este requisito com uma única política que concede acesso: 

- Para aplicativos de nuvem selecionados
- Para utilizadores e grupos selecionados  
- Requerendo autenticação de vários fatores 
- Quando o acesso é originado a partir de: 
   - Um local que não é de confiança

## <a name="implementation"></a>Implementação

O desafio deste cenário é traduzir *o acesso de uma localização de rede não fidedqui* a uma condição de Acesso Condicional. Numa política de Acesso Condicional, pode configurar a [condição de locais](location-condition.md) para abordar cenários relacionados com as localizações da rede. A condição de locais permite-lhe selecionar locais nomeados, que são agrupamentos lógicos de intervalos de endereços IP, países e regiões.  

Normalmente, a sua organização possui uma ou mais gamas de endereços, por exemplo, 199.30.16.0 - 199.30.16.15.
Pode configurar um local nomeado:

- Especificando esta gama (199.30.16.0/28) 
- Atribuição de um nome descritivo, como **Rede Corporativa** 

Em vez de tentar definir quais são todos os locais que não são de confiança, você pode:

- Incluir qualquer localização 

   ![Acesso Condicional](./media/untrusted-networks/02.png)

- Excluir todos os locais fidedignos 

   ![Acesso Condicional](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>Implementação da Política

Com a abordagem descrita neste artigo, pode agora configurar uma política de acesso condicional para locais não fidedignais. Para garantir que a sua política funciona como esperado, a melhor prática recomendada é testá-la antes de a lançar para a produção. Idealmente, use um inquilino de teste para verificar se a sua nova apólice funciona como pretendido. Para mais informações, consulte [Como implementar uma nova política.](best-practices.md#how-should-you-deploy-a-new-policy) 

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o Acesso Condicional, veja [o que é o Acesso Condicional no Diretório Ativo Azure?](./overview.md)