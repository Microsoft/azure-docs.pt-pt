---
title: O que é uma migração de política de acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba o que precisa saber para migrar políticas clássicas no portal do Azure.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/24/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a52f0521caa86cfeaa4417a90ea6c7bc51fce0d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58163641"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>O que é uma migração de política de acesso condicional do Azure Active Directory? 


[Acesso condicional](../active-directory-conditional-access-azure-portal.md) é um recurso do Azure Active directory (Azure AD), que lhe permite controlar como autorizado aos utilizadores acesso as aplicações na cloud. Embora o objetivo ainda é o mesmo, a versão do novo portal do Azure introduziu melhorias significativas para condicional como funciona o acesso.

Deve considerar a migrar as políticas que não tenha criado no portal do Azure porque:

- Agora pode solucionar cenários que não poderia manipular antes.

- Pode reduzir o número de políticas que tem de gerir por consolidá-las.   

- Pode gerir todas as suas políticas de acesso condicional num local central.

- Portal clássico do Azure vai ser descontinuado.   

Este artigo explica o que precisa saber para migrar as políticas de acesso condicional existentes para a nova estrutura.
 
## <a name="classic-policies"></a>Políticas clássicas

Na [portal do Azure](https://portal.azure.com), o [acesso condicional - políticas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) página é o ponto de entrada para o acesso condicional políticas. No entanto, no seu ambiente, poderá também ter políticas de acesso condicional que não foram criadas utilizando essa página. Estas políticas são conhecidas como *políticas clássicas*. Políticas clássicas são as políticas de acesso condicional, que criou:

- Portal clássico do Azure
- Portal clássico do Intune
- O portal de proteção de aplicações do Intune


No **acesso condicional** página, pode acessar seu políticas clássicas ao clicar em [ **políticas de clássico (pré-visualização)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) no **gerir** secção. 


![Azure Active Directory](./media/policy-migration/71.png)


O **políticas clássicas** vista fornece-lhe uma opção para:

- Filtre as suas políticas clássicas.
 
    ![Azure Active Directory](./media/policy-migration/72.png)

- Desative políticas clássicas.

    ![Azure Active Directory](./media/policy-migration/73.png)
   
- Reveja as definições de um políticas clássicas (e para desabilitá-lo).

    ![Azure Active Directory](./media/policy-migration/74.png)


Se tiver desativado uma política clássica, não pode reverter mais este passo. Este é o motivo pelo qual pode modificar a associação a grupos numa política clássica com o **detalhes** vista. 

![Azure Active Directory](./media/policy-migration/75.png)

Alterando os grupos selecionados ou ao excluir grupos específicos, pode testar o efeito de uma política clássica desativada para alguns utilizadores de teste antes de desativar a política para utilizadores incluídos e grupos. 



## <a name="azure-ad-conditional-access-policies"></a>Políticas de acesso condicional do Azure AD

Com o acesso condicional no portal do Azure, pode gerir todas as suas políticas num local central. Uma vez que a implementação de como o acesso condicional mudou significativamente, deve se familiarizar com os conceitos básicos antes de migrar as suas políticas clássicas.

Veja:

- [O que é o acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md) para saber mais sobre os conceitos básicos e a terminologia.

- [Melhores práticas para acesso condicional no Azure Active Directory](best-practices.md) para obter orientações sobre como implementar o acesso condicional na sua organização.

- [Exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md) para se familiarizar com a interface de utilizador no portal do Azure.


 
## <a name="migration-considerations"></a>Considerações sobre a migração

Neste artigo, políticas de acesso condicional do Azure AD são também denominadas *novas políticas*.
As políticas clássicas continuam a trabalhar lado a lado com as novas políticas até que desabilitar ou eliminá-los. 

Os seguintes aspetos são importantes no contexto de uma consolidação de política:

- Enquanto as políticas clássicas estão associadas a uma aplicação de cloud específico, pode selecionar quantas aplicações na cloud precisa numa nova política.

- Controles de uma política clássica e uma nova política para uma aplicação na cloud necessitam de todos os controles (*AND*) para ser concluído. 


- Uma nova política, pode:
 
    - Combine várias condições se for necessário ao seu cenário. 

    - Selecione vários concedem requisitos que o acesso, controlam e combinação-los com uma lógica *OR* (exigir um dos controlos selecionados) ou com uma lógica *AND* (exigir todos os controles selecionados).

        ![Azure Active Directory](./media/policy-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Se pretender migrar políticas clássicas para **Office 365 Exchange online** que incluam **Exchange Active Sync** como condição de aplicações de cliente, poderá não conseguir consolide-os para uma nova política. 

Isso acontece, por exemplo, se pretende oferecer apoio técnico a todos os tipos de aplicação de cliente. Numa nova política que tenha **Exchange Active Sync** como condição de aplicações de cliente, não é possível selecionar outras aplicações de cliente.

![Azure Active Directory](./media/policy-migration/64.png)

Uma consolidação numa nova política também não é possível se suas políticas clássicas contêm várias condições. Uma nova política que tenha **Exchange Active Sync** como aplicações de cliente condição configurada não suporta outras condições:   

![Azure Active Directory](./media/policy-migration/08.png)

Se tiver uma nova política que tenha **Exchange Active Sync** como aplicações de cliente condição configurada, precisa para se certificar de que todas as outras condições não estão configuradas. 

![Azure Active Directory](./media/policy-migration/16.png)
 

[Com base na aplicação](technical-reference.md#approved-client-app-requirement) políticas clássicas para o Office 365 Exchange Online, que incluem **Exchange Active Sync** como condição de aplicações de cliente permitem **suportado** e **não suportada** [plataformas de dispositivos](technical-reference.md#device-platform-condition). Embora não é possível configurar plataformas de dispositivos individuais numa nova política relacionada, pode limitar o suporte para [plataformas de dispositivos suportadas](technical-reference.md#device-platform-condition) apenas. 

![Azure Active Directory](./media/policy-migration/65.png)

Pode consolidar várias políticas clássicas que incluem **Exchange Active Sync** como condição de aplicações de cliente se tiverem:

- Apenas **Exchange Active Sync** como condição 

- Vários requisitos para conceder acesso configurado

Um cenário comum é a consolidação de:

- Uma política clássica baseado no dispositivo do portal clássico do Azure 
- Uma política clássica baseado na aplicação no portal de proteção de aplicações do Intune 
 
Neste caso, pode consolidar suas políticas clássicas numa nova política, que tem ambos os requisitos selecionados.

![Azure Active Directory](./media/policy-migration/62.png)



### <a name="device-platforms"></a>Plataformas de dispositivos

Políticas clássicas com [com base na aplicação controles](technical-reference.md#approved-client-app-requirement) estão pré-configuradas com o iOS e Android como o [condição de plataforma de dispositivo](technical-reference.md#device-platform-condition). 

Numa nova política, tem de selecionar o [plataformas de dispositivos](technical-reference.md#device-platform-condition) pretende oferecer apoio técnico individualmente.

![Azure Active Directory](./media/policy-migration/41.png)



 
 


## <a name="next-steps"></a>Passos Seguintes

- Se quiser saber como configurar uma política de acesso condicional, consulte [exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Se estiver pronto para configurar políticas de acesso condicional para o seu ambiente, veja [Best practices for conditional access in Azure Active Directory](best-practices.md) (Melhores práticas do acesso condicional no Azure Active Directory). 
