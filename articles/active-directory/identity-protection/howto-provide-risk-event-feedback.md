---
title: Forneça comentários sobre eventos de risco no Azure AD Identity Protection - Azure Active Directory
description: Como e por que deve fornecer comentários sobre eventos de risco do Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827909"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Como: Enviar comentários de risco no Azure AD Identity Protection

O Azure AD Identity Protection permite-lhe dar feedback sobre a sua avaliação de riscos. O documento seguinte lista os cenários em que gostaria de dar feedback sobre a avaliação de riscos do Azure AD Identity Protection e como podemos incorporá-la.

## <a name="what-is-a-detection"></a>O que é uma deteção?

Uma deteção de Identity Protection é um indicador de atividade suspeita de uma perspectiva de risco de identidade. Estas atividades suspeitas são chamadas de eventos de risco. Estas deteções com base na identidade podem ser baseadas em heurística, machine learning ou podem vir de produtos de parceiros. Estas deteções são utilizadas para determinar o risco de início de sessão e risco de utilizador

* Risco de utilizador representa a probabilidade de que uma identidade é comprometida.
* Início de sessão de risco representa a probabilidade de um início de sessão for comprometido (por exemplo, o início de sessão não está autorizado pelo proprietário de identidade).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Por que motivo deve conceder comentários de risco para avaliações de riscos do Azure AD? 

Há vários motivos por que deve fornecer comentários de risco do Azure AD:

1. **Encontrado avaliação de risco de utilizador ou início de sessão do Azure AD incorreto**. Por exemplo, um início de sessão mostrado no relatório de 'Risco inícios de sessão' era benigno e todas as detecções nesse início de sessão foram falsos positivos.
1. **Validar o utilizador do Azure AD ou avaliação de risco de início de sessão foi correta**. Por exemplo, um início de sessão mostrado no relatório de 'Risco inícios de sessão' era realmente mal-intencionado e pretende que o Azure AD para saber de que todas as detecções nesse início de sessão eram positivos verdadeiros.
1. **Remediar o risco com esse utilizador fora do Azure AD Identity Protection** e pretende que o nível de risco do utilizador para ser atualizado.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Como é que o Azure AD utiliza meus comentários de risco?

Azure AD utiliza os seus comentários para atualizar o risco do subjacente utilizador e/ou início de sessão. Estes comentários ajuda a proteger o usuário final. Por exemplo, depois de confirmar que um início de sessão for comprometido, do Azure AD imediatamente aumenta o risco do utilizador e o agregado risco do início de sessão (risco não em tempo real) para o maior. Se este utilizador está incluído na sua política de risco do utilizador para forçar os usuários de alto risco em segurança repor as palavras-passe, o utilizador irá remediar automaticamente em si na próxima vez que iniciar sessão.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Como deve oferecer risco comentários e o que acontece nos bastidores?

Aqui estão os cenários e mecanismos para comentários de risco para o Azure AD.

| Cenário | Como enviar comentários? | O que acontece nos bastidores? | Notas |
| --- | --- | --- | --- |
| **Início de sessão não comprometido (falso positivo)** <br> Relatório de "Risco inícios de sessão" apresenta um risco início de sessão [o risco de estado = em risco], mas que o início de sessão não foi comprometida. | Selecione o início de sessão e clique em "Confirmar início de sessão seguro". | O Azure AD irá mover risco agregado do início de sessão como none [o risco de estado = Confirmed seguro; (Agregar) de nível de risco =-] e reverterá o seu impacto no risco do utilizador. | Atualmente, a opção 'Confirmar início de sessão seguro' só está disponível no relatório de "Risco inícios de sessão". |
| **Início de sessão comprometida (positivo verdadeiro)** <br> Relatório de "Risco inícios de sessão" apresenta um risco início de sessão [o risco de estado = em risco] com baixo risco [(agregar) de nível de risco = baixa] e esse início de sessão, de fato, foi comprometida. | Selecione o início de sessão e clique em "Confirmar início de sessão comprometido". | O Azure AD irá mover agregado risco do início de sessão e o risco de utilizador para alta [o risco de estado = Confirmed comprometido; Nível de risco elevado de =]. | Atualmente, a opção 'Confirmar início de sessão comprometido' só está disponível no relatório de "Risco inícios de sessão". |
| **Utilizador comprometida (positivo verdadeiro)** <br> Relatório de utilizadores de risco mostra um utilizador de risco [o risco de estado = em risco] com baixo risco [nível de risco = baixa] e que o usuário realmente foi comprometido. | Selecione o utilizador e clique em "O utilizador confirmar comprometido". | O Azure AD irá mover o risco de utilizador para alta [o risco de estado = Confirmed comprometido; Nível de risco elevado de =] e adicionar uma nova deteção "Administrador confirmada comprometido do utilizador". | Atualmente, a opção "O utilizador confirmar comprometido" só está disponível no relatório de utilizadores de risco. <br> A deteção "Administrador confirmada comprometido do utilizador" é apresentada no separador "Eventos de risco não associados a um início de sessão" no relatório "Utilizadores de risco". |
| **Utilizadores remediados fora do Azure AD Identity Protection (positivo verdadeiro + Remediated)** <br> Relatório de utilizadores de risco mostra um utilizador de risco e, em seguida, posso ter remediado o utilizador fora do Azure AD Identity Protection. | 1. Selecione o utilizador e clique em "O utilizador confirmar comprometido". (Este processo confirma para o Azure AD que o usuário realmente foi comprometido.) <br> 2. Aguarde que "nível de risco" o utilizador para ir para alta. (Este lhe do Azure AD o tempo necessário para levar os comentários acima para o mecanismo de risco.) <br> 3. Selecione o utilizador e clique em "Dispensar o risco de utilizador". (Este processo confirma para o Azure AD que o utilizador já não é comprometido.) |  O Azure AD move o risco de utilizador para none [o risco de estado = dispensados; Nível de risco =-] e fecha o risco em todas as existentes inícios de sessão com risco de Active Directory. | Clicar em 'Dispensar o risco de utilizador' irá fechar todos os riscos no usuário e passado de inícios de sessão. Esta ação não pode ser anulada. |
| **Utilizador não comprometido (falso positivo)** <br> Relatório de utilizadores de risco mostra ao utilizador de risco, mas o utilizador não é comprometido. | Selecione o utilizador e clique em "Dispensar o risco de utilizador". (Este processo confirma para o Azure AD que o utilizador não é comprometido.) | O Azure AD move o risco de utilizador para none [o risco de estado = dispensados; Nível de risco =-]. | Clicar em 'Dispensar o risco de utilizador' irá fechar todos os riscos no usuário e passado de inícios de sessão. Esta ação não pode ser anulada. |
| Quero fechar o risco de utilizador, mas não sei se o usuário é comprometido / seguro. | Selecione o utilizador e clique em "Dispensar o risco de utilizador". (Este processo confirma para o Azure AD que o utilizador já não é comprometido.) | O Azure AD move o risco de utilizador para none [o risco de estado = dispensados; Nível de risco =-]. | Clicar em 'Dispensar o risco de utilizador' irá fechar todos os riscos no usuário e passado de inícios de sessão. Esta ação não pode ser anulada. Recomendamos remediar o utilizador ao clicar em 'Repor palavra-passe' ou pedir ao utilizador em segurança reposição/alterar as respetivas credenciais. |

Comentários sobre eventos de risco de utilizador no Identity Protection é processado offline e podem demorar algum tempo para atualizar. O risco de processamento da coluna de estado irá fornecer o estado atual do processamento de comentários.

![O risco de estado de processamento para o relatório de utilizador de risco](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Passos Seguintes

[Referência de eventos de risco do Azure Active Directory Identity Protection](risk-events-reference.md)