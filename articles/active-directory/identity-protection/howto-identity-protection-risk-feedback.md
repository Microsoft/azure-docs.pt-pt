---
title: Fornecer feedback de risco na Proteção de Identidade do Diretório Ativo do Azure
description: Como e por que fornecer feedback sobre deteções de risco de proteção de identidade.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382082"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Como: Dar feedback de risco na Proteção de Identidade Azure AD

A Azure AD Identity Protection permite-lhe dar feedback sobre a sua avaliação de risco. O documento que se segue enumera os cenários em que pretende dar feedback sobre a avaliação de risco da Azure AD Identity Protection e como a incorporamos.

## <a name="what-is-a-detection"></a>O que é uma deteção?

Uma deteção de Proteção de Identidade é um indicador de atividade suspeita do ponto de vista do risco de identidade. Estas atividades suspeitas são chamadas de deteções de risco. Estas deteções baseadas na identidade podem basear-se em heurísticas, machine learning ou podem vir de produtos parceiros. Estas deteções são utilizadas para determinar o risco de inscrição e o risco do utilizador,

* O risco do utilizador representa a probabilidade de uma identidade estar comprometida.
* O risco de entrada representa a probabilidade de um inserido estar comprometido (por exemplo, o inserição não é autorizado pelo proprietário da identidade).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Por que devo dar feedback de risco às avaliações de risco da Azure AD? 

Existem várias razões pelas quais deve dar feedback de risco da AD Azure:

- **Encontrou o utilizador da Azure AD ou a avaliação de risco de entrada incorreta**. Por exemplo, um registo apresentado no relatório "Sign-ins de risco" era benigno e todas as deteções desse registo eram falsos positivos.
- **Validou que o utilizador da Azure AD ou a avaliação de risco de entrada estava correta.** Por exemplo, um sessão de inscrição mostrado no relatório "Sign-ins arriscado" foi de facto malicioso e quer que a AD Azure saiba que todas as deteções nesse sign-in eram verdadeiramente positivas.
- **Remedia o risco para esse utilizador fora da Proteção de Identidade Azure AD** e pretende que o nível de risco do utilizador seja atualizado.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Como é que o Azure AD usa o meu feedback de risco?

A Azure AD utiliza o seu feedback para atualizar o risco do utilizador subjacente e/ou o início de sessão e a precisão destes eventos. Este feedback ajuda a proteger o utilizador final. Por exemplo, uma vez que confirma que um inserido está comprometido, o Azure AD aumenta imediatamente o risco do utilizador e o risco agregado de entrada (não risco em tempo real) para High. Se este utilizador estiver incluído na sua política de risco de utilizador para forçar os utilizadores de alto risco a reporem as suas palavras-passe de forma segura, o utilizador remediar-se-á automaticamente da próxima vez que iniciar sessão.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Como devo dar feedback de risco e o que acontece debaixo do capot?

Aqui estão os cenários e mecanismos para dar feedback de risco à AD Azure.

| Cenário | Como dar feedback? | O que acontece debaixo do capuz? | Notas |
| --- | --- | --- | --- |
| **Inscreveu-se não comprometido (Falso positivo)** <br> O relatório "Risk sign-ins" mostra um sinal de risco [Estado de risco = em risco] mas esse inscreveu-se não foi comprometido. | Selecione o sessão e clique em 'Confirmar o cofre de sessão'. | A Azure AD irá mover o risco agregado do sign-in para nenhum [Estado de risco = Confirmado seguro; Nível de risco (Agregado) = -] e inverterá o seu impacto no risco do utilizador. | Atualmente, a opção "Confirmar o "confirmar o "cofre de inscrição" só está disponível no relatório "Sign-ins de risco". |
| **Inado comprometido (Verdadeiro positivo)** <br> O relatório 'Risk sign-ins' mostra um sinal de risco [Estado de risco = Em risco] com baixo risco [Nível de risco (agregado) = Baixo] e que o inscreveu foi de facto comprometido. | Selecione o sessão e clique em 'Confirmar o sessão comprometido'. | A Azure AD moverá o risco agregado do sign-in e o risco do utilizador para o Alto [Estado de Risco = Confirmado comprometido; Nível de risco = Alto]. | Atualmente, a opção "Confirmar o compromisso de inscrição" só está disponível no relatório "Sign-ins de risco". |
| **Utilizador comprometido (Verdadeiro positivo)** <br> O relatório 'Utilizadores de risco' mostra um utilizador em risco [Estado de risco = Em risco] com baixo risco [nível de risco = baixo] e esse utilizador foi de facto comprometido. | Selecione o utilizador e clique em 'Confirmar o utilizador comprometido'. | A Azure AD moverá o risco do utilizador para alto [estado de risco = Confirmado comprometido; Nível de risco = Alto] e adicionará uma nova deteção 'Admin confirmed user comprometido'. | Atualmente, a opção "Confirmar o utilizador comprometido" só está disponível no relatório "Utilizadores de Risco". <br> A deteção "Admin confirmed user comprometido" é indicada no separador "Deteções de risco não ligadas a um insessão" no relatório "Utilizadores de risco". |
| **Utilizador remediado fora da Proteção de Identidade Azure AD (True positive + Remediado)** <br> O relatório «Utilizadores de risco» mostra um utilizador em risco e remediar o utilizador fora da Proteção de Identidade da Azure AD. | 1. Selecione o utilizador e clique em "Confirmar o utilizador comprometido". (Este processo confirma à Azure AD que o utilizador estava de facto comprometido.) <br> 2. Aguarde que o "nível de risco" do utilizador vá para o Alto. (Desta vez dá ao Azure AD o tempo necessário para levar o feedback acima para o motor de risco.) <br> 3. Selecione o utilizador e clique em "Descartar o risco do utilizador". (Este processo confirma à Azure AD que o utilizador já não está comprometido.) |  A Azure AD move o risco do utilizador para nenhum [Estado de risco = Dispensado; Nível de risco = -] e fecha o risco em todos os sign-ins existentes com risco ativo. | Clicar em "Descartar o risco do utilizador" irá fechar todos os riscos no utilizador e nos anteriores sign-ins. Esta ação não pode ser desfeita. |
| **Utilizador não comprometido (Falso positivo)** <br> O relatório «Utilizadores de risco» mostra o utilizador em risco, mas o utilizador não está comprometido. | Selecione o utilizador e clique em "Descartar o risco do utilizador". (Este processo confirma à Azure AD que o utilizador não está comprometido.) | A Azure AD move o risco do utilizador para nenhum [Estado de risco = Dispensado; Nível de risco = -]. | Clicar em "Descartar o risco do utilizador" irá fechar todos os riscos no utilizador e nos anteriores sign-ins. Esta ação não pode ser desfeita. |
| Quero colmatar o risco do utilizador, mas não sei se o utilizador está comprometido/seguro. | Selecione o utilizador e clique em "Descartar o risco do utilizador". (Este processo confirma à Azure AD que o utilizador já não está comprometido.) | A Azure AD move o risco do utilizador para nenhum [Estado de risco = Dispensado; Nível de risco = -]. | Clicar em "Descartar o risco do utilizador" irá fechar todos os riscos no utilizador e nos anteriores sign-ins. Esta ação não pode ser desfeita. Recomendamos que reamediar o utilizador clicando em 'Reset password' ou solicite ao utilizador que repor/alterar de forma segura as suas credenciais. |

O feedback sobre as deteções de risco do utilizador na Proteção de Identidade é processado offline e pode demorar algum tempo a ser atualizado. A coluna estatal de processamento de risco fornecerá o estado atual de processamento de feedback.

![Estado de processamento de risco para relatório de utilizador arriscado](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

- [Referência de deteção de risco de deteção de risco de proteção de identidade de diretório ativo Azure](risk-events-reference.md)
