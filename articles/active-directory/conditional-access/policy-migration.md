---
title: Migrar políticas de acesso condicional-Azure Active Directory
description: Saiba o que você precisa saber para migrar políticas clássicas no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d664f6e61dbbaaf0b8ab74c392596a206ff644
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380543"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>O que é uma migração de política no Azure Active Directory acesso condicional? 

O [acesso condicional](../active-directory-conditional-access-azure-portal.md) é um recurso do Azure AD (Azure Active Directory) que permite controlar como os usuários autorizados acessam seus aplicativos de nuvem. Embora a finalidade ainda seja a mesma, o lançamento do novo portal do Azure introduziu melhorias significativas no funcionamento do acesso condicional.

Considere migrar as políticas que você não criou no portal do Azure porque:

- Agora você pode abordar os cenários que não podia manipular antes.
- Você pode reduzir o número de políticas a serem gerenciadas, consolidando-as.   
- Você pode gerenciar todas as políticas de acesso condicional em um local central.
- O portal clássico do Azure será desativado.   

Este artigo explica o que você precisa saber para migrar suas políticas de acesso condicional existentes para a nova estrutura.
 
## <a name="classic-policies"></a>Políticas clássicas

No [portal do Azure](https://portal.azure.com), a página de [políticas de acesso condicional](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) é seu ponto de entrada para suas políticas de acesso condicional. No entanto, em seu ambiente, você também pode ter políticas de acesso condicional que você não criou usando esta página. Essas políticas são conhecidas como *políticas clássicas*. As políticas clássicas são políticas de acesso condicional, que você criou em:

- O portal clássico do Azure
- O portal clássico do Intune
- O portal de Proteção de Aplicativo do Intune

Na página **acesso condicional** , você pode acessar suas políticas clássicas clicando em [**políticas clássicas (versão prévia)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) na seção **gerenciar** . 

![Azure Active Directory](./media/policy-migration/71.png)

O modo de exibição **políticas clássicas** fornece uma opção para:

- Filtre suas políticas clássicas.
 
   ![Azure Active Directory](./media/policy-migration/72.png)

- Desabilitar políticas clássicas.

   ![Azure Active Directory](./media/policy-migration/73.png)
   
- Examine as configurações de uma política clássica (e para desabilitá-la).

   ![Azure Active Directory](./media/policy-migration/74.png)

Se você tiver desabilitado uma política clássica, não poderá mais reverter essa etapa. É por isso que você pode modificar a associação de grupo em uma política clássica usando a exibição de **detalhes** . 

![Azure Active Directory](./media/policy-migration/75.png)

Alterando os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma política clássica desabilitada para alguns usuários de teste antes de desabilitar a política para todos os usuários e grupos incluídos. 

## <a name="azure-ad-conditional-access-policies"></a>Políticas de acesso condicional do Azure AD

Com o acesso condicional no portal do Azure, você pode gerenciar todas as suas políticas em um único local central. Como a implementação de como o acesso condicional foi alterado, você deve se familiarizar com os conceitos básicos antes de migrar suas políticas clássicas.

Veja:

- [O que é o acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md) para saber mais sobre os conceitos básicos e a terminologia.
- [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md) para obter diretrizes sobre como implantar o acesso condicional em sua organização.
- [Exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md) para se familiarizar com a interface do usuário na portal do Azure.
 
## <a name="migration-considerations"></a>Considerações sobre a migração

Neste artigo, as políticas de acesso condicional do Azure AD também são conhecidas como *novas políticas*.
Suas políticas clássicas continuam a funcionar lado a lado com suas novas políticas até você desabilitá-las ou excluí-las. 

Os seguintes aspectos são importantes no contexto de uma consolidação de política:

- Embora as políticas clássicas estejam vinculadas a um aplicativo de nuvem específico, você pode selecionar quantos aplicativos de nuvem forem necessários para uma nova política.
- Os controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*e*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições, se exigido pelo seu cenário. 
   - Selecionar vários requisitos de concessão como controle de acesso e combiná-los com um *or* lógico (exigir um dos controles selecionados) ou com um *and* lógico (exigir todos os controles selecionados).

   ![Azure Active Directory](./media/policy-migration/25.png)

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Se você quiser migrar políticas clássicas para o **Office 365 Exchange Online** que inclua **Exchange Active Sync** como condição de aplicativos cliente, talvez não seja possível consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você quiser dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, você não pode selecionar outros aplicativos cliente.

![Azure Active Directory](./media/policy-migration/64.png)

Uma consolidação em uma nova política também não será possível se suas políticas clássicas contiverem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte a outras condições:   

![Azure Active Directory](./media/policy-migration/08.png)

Se você tiver uma nova política que tenha **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisará certificar-se de que todas as outras condições não estão configuradas. 

![Azure Active Directory](./media/policy-migration/16.png)
 
As políticas clássicas [baseadas em aplicativo](technical-reference.md#approved-client-app-requirement) para o Office 365 Exchange Online que incluem o **Exchange Active Sync** como condição de aplicativos cliente permitem [plataformas de dispositivo](technical-reference.md#device-platform-condition) **com** e sem **suporte** . Embora não seja possível configurar plataformas de dispositivo individuais em uma nova política relacionada, você pode limitar o suporte apenas a [plataformas de dispositivo com suporte](technical-reference.md#device-platform-condition) . 

![Azure Active Directory](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para conceder acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica baseada em aplicativo no portal de proteção de aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem os dois requisitos selecionados.

![Azure Active Directory](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com [controles baseados em aplicativo](technical-reference.md#approved-client-app-requirement) são pré-configuradas com Ios e Android como a [condição de plataforma do dispositivo](technical-reference.md#device-platform-condition). 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](technical-reference.md#device-platform-condition) às quais deseja dar suporte individualmente.

![Azure Active Directory](./media/policy-migration/41.png)

## <a name="next-steps"></a>Passos seguintes

- Se você quiser saber como configurar uma política de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte as [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md). 
