---
title: Migrar políticas de acesso condicional-Azure Active Directory
description: Saiba o que você precisa saber para migrar políticas clássicas no portal do Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16c6ce46af8fb9a9ab4be5fcc63ccd4909374e4f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846181"
---
# <a name="conditional-access-classic-policy-migration"></a>Migração de política clássica de acesso condicional

O acesso condicional é a ferramenta usada por Azure Active Directory para reunir sinais, tomar decisões e impor políticas organizacionais. O acesso condicional é o coração do novo plano de controle controlado por identidade. Embora a finalidade ainda seja a mesma, o lançamento do novo portal do Azure introduziu melhorias significativas no funcionamento do acesso condicional.

Considere migrar as políticas que você não criou no portal do Azure porque:

- Agora você pode abordar os cenários que não podia manipular antes.
- Você pode reduzir o número de políticas a serem gerenciadas, consolidando-as.
- Você pode gerenciar todas as políticas de acesso condicional em um local central.
- O portal clássico do Azure será desativado.

Este artigo explica o que você precisa saber para migrar suas políticas de acesso condicional existentes para a nova estrutura.

## <a name="classic-policies"></a>Políticas clássicas

No [portal do Azure](https://portal.azure.com), as políticas de acesso condicional podem ser encontradas em Azure Active Directory **segurança** >  > **acesso condicional**. Sua organização também pode ter políticas de acesso condicional mais antigas não criadas usando esta página. Essas políticas são conhecidas como *políticas clássicas*. As políticas clássicas são políticas de acesso condicional, que você criou em:

- O portal clássico do Azure
- O portal clássico do Intune
- O portal de Proteção de Aplicativo do Intune

Na página **acesso condicional** , você pode acessar suas políticas clássicas clicando em [**políticas clássicas**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) na seção **gerenciar** . 

![Acesso condicional no Azure AD mostrando a exibição de políticas clássicas](./media/policy-migration/71.png)

O modo de exibição **políticas clássicas** fornece uma opção para:

- Filtre suas políticas clássicas.
- Desabilitar políticas clássicas.
- Examine as configurações de uma política clássica e desabilite-a.

   ![Detalhes da política clássica, incluindo configuração de política existente](./media/policy-migration/74.png)

> [!WARNING]
> Depois de desabilitada, uma política clássica não pode ser reabilitada.

A exibição de detalhes de uma política clássica permite que você documente as configurações, modifique os grupos incluídos ou excluídos e desabilite a política.

![Detalhes da política-grupos a serem incluídos ou excluídos](./media/policy-migration/75.png)

Ao alterar os grupos selecionados ou excluindo grupos específicos, você pode testar o efeito de uma política clássica desabilitada para alguns usuários de teste antes de desabilitar a política para todos os usuários e grupos incluídos.
 
## <a name="migration-considerations"></a>Considerações sobre a migração

Neste artigo, as políticas de acesso condicional do Azure AD também são conhecidas como *novas políticas*.
Suas políticas clássicas continuam a funcionar lado a lado com suas novas políticas até você desabilitá-las ou excluí-las. 

Os seguintes aspectos são importantes no contexto de uma consolidação de política:

- Embora as políticas clássicas estejam vinculadas a um aplicativo de nuvem específico, você pode selecionar quantos aplicativos de nuvem forem necessários para uma nova política.
- Os controles de uma política clássica e uma nova política para um aplicativo de nuvem exigem que todos os controles (*e*) sejam atendidos. 
- Em uma nova política, você pode:
   - Combine várias condições, se exigido pelo seu cenário. 
   - Selecionar vários requisitos de concessão como controle de acesso e combiná-los com um *or* lógico (exigir um dos controles selecionados) ou com um *and* lógico (exigir todos os controles selecionados).

### <a name="office-365-exchange-online"></a>Office 365 Exchange Online

Se você quiser migrar políticas clássicas para o **Office 365 Exchange Online** que inclua **Exchange Active Sync** como condição de aplicativos cliente, talvez não seja possível consolidá-las em uma nova política. 

Isso é, por exemplo, o caso se você quiser dar suporte a todos os tipos de aplicativo cliente. Em uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente, você não pode selecionar outros aplicativos cliente.

![Acesso condicional selecionando aplicativos cliente](./media/policy-migration/64.png)

Uma consolidação em uma nova política também não será possível se suas políticas clássicas contiverem várias condições. Uma nova política que tem **Exchange Active Sync** como condição de aplicativos cliente configurada não oferece suporte a outras condições:   

![O Exchange ActiveSync não oferece suporte às condições selecionadas](./media/policy-migration/08.png)

Se você tiver uma nova política que tenha **Exchange Active Sync** como condição de aplicativos cliente configurada, você precisará certificar-se de que todas as outras condições não estão configuradas. 

![Condições de acesso condicional](./media/policy-migration/16.png)
 
As políticas clássicas [baseadas em aplicativo](technical-reference.md#approved-client-app-requirement) para o Office 365 Exchange Online que incluem o **Exchange Active Sync** como condição de aplicativos cliente permitem [plataformas de dispositivo](technical-reference.md#device-platform-condition) **com** e sem **suporte** . Embora não seja possível configurar plataformas de dispositivo individuais em uma nova política relacionada, você pode limitar o suporte apenas a [plataformas de dispositivo com suporte](technical-reference.md#device-platform-condition) . 

![Acesso condicional selecione Exchange ActiveSync](./media/policy-migration/65.png)

Você pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicativos cliente se eles tiverem:

- Somente **Exchange Active Sync** como condição 
- Vários requisitos para conceder acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseada em dispositivo do portal clássico do Azure 
- Uma política clássica baseada em aplicativo no portal de proteção de aplicativo do Intune 
 
Nesse caso, você pode consolidar suas políticas clássicas em uma nova política que tem os dois requisitos selecionados.

![Controles de concessão de acesso condicional](./media/policy-migration/62.png)

### <a name="device-platforms"></a>Plataformas de dispositivo

As políticas clássicas com [controles baseados em aplicativo](technical-reference.md#approved-client-app-requirement) são pré-configuradas com Ios e Android como a [condição de plataforma do dispositivo](technical-reference.md#device-platform-condition). 

Em uma nova política, você precisa selecionar as [plataformas de dispositivo](technical-reference.md#device-platform-condition) às quais deseja dar suporte individualmente.

![Seleção de plataformas de dispositivo de acesso condicional](./media/policy-migration/41.png)

## <a name="next-steps"></a>Passos seguintes

- [Use o modo somente de relatório para acesso condicional para determinar o impacto das novas decisões de política.](concept-conditional-access-report-only.md)
- Se você quiser saber como configurar uma política de acesso condicional, consulte [políticas comuns de acesso condicional](concept-conditional-access-policy-common.md).
- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte o artigo [como planejar sua implantação de acesso condicional em Azure Active Directory](plan-conditional-access.md). 
