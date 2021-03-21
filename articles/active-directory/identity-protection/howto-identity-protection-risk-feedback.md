---
title: Fornecer feedback de risco na Azure Ative Directory Identity Protection
description: Como e por que deve fornecer feedback sobre deteções de riscos de Proteção de Identidade.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88950362"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Como: Dar feedback de risco na Proteção de Identidade AZure AD

A Azure AD Identity Protection permite-lhe dar feedback sobre a sua avaliação de risco. O documento que se segue enumera os cenários em que gostaria de dar feedback sobre a avaliação de risco da Azure AD Identity Protection e como a incorporamos.

## <a name="what-is-a-detection"></a>O que é uma deteção?

Uma deteção de Proteção de Identidade é um indicador de atividade suspeita do ponto de vista do risco de identidade. Estas atividades suspeitas são chamadas deteções de risco. Estas deteções baseadas em identidade podem basear-se em heurística, machine learning ou podem vir de produtos parceiros. Estas deteções são utilizadas para determinar o risco de inscrição e o risco do utilizador,

* O risco do utilizador representa a probabilidade de uma identidade estar comprometida.
* O risco de entrada representa a probabilidade de um sinal de entrada estar comprometido (por exemplo, o insindutor não é autorizado pelo titular da identidade).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Por que devo dar feedback de risco às avaliações de risco da Azure? 

Existem várias razões pelas quais deve dar feedback sobre o risco da Azure AD:

- **Encontrou o utilizador da Azure AD ou a avaliação do risco de entrada incorreta .** Por exemplo, uma inscrição apresentada no relatório "Risky sign-ins" era benigna e todas as deteções nesse sinal eram falsos positivos.
- **Validou que o utilizador da Azure AD ou a avaliação do risco de inscrição estava correta**. Por exemplo, uma sposição apresentada no relatório "Risky sign-ins" foi, de facto, maliciosa e quer que a Azure AD saiba que todas as deteções dessa sposição foram verdadeiramente positivas.
- **Remediado o risco para aquele utilizador fora da Proteção de Identidade Azure AD** e deseja que o nível de risco do utilizador seja atualizado.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Como é que a Azure AD usa o meu feedback de risco?

O Azure AD utiliza o seu feedback para atualizar o risco do utilizador subjacente e/ou o teste de sação e a precisão destes eventos. Este feedback ajuda a proteger o utilizador final. Por exemplo, uma vez que você confirme que um sinal está comprometido, Azure AD aumenta imediatamente o risco do utilizador e o risco agregado de entrada (não risco em tempo real) para Alto. Se este utilizador estiver incluído na sua política de risco de utilizador para forçar os utilizadores de alto risco a redefinirem as suas palavras-passe de forma segura, o utilizador remediar-se-á automaticamente na próxima vez que iniciar a sua sposição.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Como devo dar feedback de risco e o que acontece debaixo do capot?

Aqui estão os cenários e mecanismos para dar feedback de risco ao Azure AD.

| Scenario | Como dar feedback? | O que acontece debaixo do capuz? | Notas |
| --- | --- | --- | --- |
| **S-in não comprometido (Falso positivo)** <br> O relatório "Ins de risco" mostra um sinal de risco [Estado de risco = em risco] mas essa inscrição não foi comprometida. | Selecione o sôm-in e clique em 'Confirme o cofre de entrada'. | A Azure AD moverá o risco agregado do sign-in para nenhum [Estado de risco = Seguro confirmado; Nível de risco (Agregado) = -] e reverterá o seu impacto no risco de utilizador. | Atualmente, a opção "Confirmar o aviso seguro" só está disponível no relatório "Entradas arriscadas". |
| **Inscrição comprometida (Verdadeiro positivo)** <br> O relatório «Ins de risco» mostra um sinal de risco [Estado de risco = em risco] com baixo risco [Nível de risco (agregado) = Baixo] e essa inscrição foi efetivamente comprometida. | Selecione o sômin e clique em 'Confirme o sinal comprometido'. | O Azure AD moverá o risco agregado do sign-in e o risco do utilizador para o estado de alto risco = confirmado comprometido; Nível de risco = Alto]. | Atualmente, a opção "Confirmar a inscrição comprometida" só está disponível no relatório "Entradas arriscadas". |
| **Utilizador comprometido (Verdadeiro positivo)** <br> O relatório "utilizadores arriscados" mostra um utilizador em risco [Estado de risco = em risco] com baixo risco [Nível de risco = baixo] e esse utilizador foi efetivamente comprometido. | Selecione o utilizador e clique em 'Confirme o utilizador comprometido'. | A Azure AD irá mover o risco do utilizador para o estado de alto risco = confirmado comprometido; Nível de risco = Alto] e adicionará uma nova deteção 'Admin utilizador confirmado comprometido'. | Atualmente, a opção "Confirmar o utilizador comprometido" só está disponível no relatório "Utilizadores arriscados". <br> A deteção "Admin confirmou o compromisso do utilizador" no separador "Deteções de risco não ligadas a uma insusição" no relatório "Utilizadores arriscados". |
| **Utilizador remediado fora da Proteção de Identidade AZure AD (Verdadeiro positivo + Remediado)** <br> O relatório "utilizadores arriscados" mostra um utilizador em risco e eu remediado posteriormente o utilizador fora da Azure AD Identity Protection. | 1. Selecione o utilizador e clique em "Confirmar o utilizador comprometido". (Este processo confirma à Azure AD que o utilizador foi de facto comprometido.) <br> 2. Aguarde que o "nível de risco" do utilizador vá para o Alto. (Desta vez dá ao Azure AD o tempo necessário para levar o feedback acima para o motor de risco.) <br> 3. Selecione o utilizador e clique em 'Descartar o risco do utilizador'. (Este processo confirma à Azure AD que o utilizador já não está comprometido.) |  A Azure AD move o risco do utilizador para nenhum [Estado de risco = Dispensado; Nível de risco = -] e fecha o risco de todos os insu máximos existentes com risco ativo. | Clicar em "Descartar o risco do utilizador" irá fechar todos os riscos no utilizador e nos insus de saúde anteriores. Esta ação não pode ser desfeita. |
| **Utilizador não comprometido (Falso positivo)** <br> O relatório "utilizadores arriscados" mostra no utilizador em risco, mas o utilizador não está comprometido. | Selecione o utilizador e clique em 'Descartar o risco do utilizador'. (Este processo confirma à Azure AD que o utilizador não está comprometido.) | A Azure AD move o risco do utilizador para nenhum [Estado de risco = Dispensado; Nível de risco = -]. | Clicar em "Descartar o risco do utilizador" irá fechar todos os riscos no utilizador e nos insus de saúde anteriores. Esta ação não pode ser desfeita. |
| Quero fechar o risco do utilizador mas não sei se o utilizador está comprometido/seguro. | Selecione o utilizador e clique em 'Descartar o risco do utilizador'. (Este processo confirma à Azure AD que o utilizador já não está comprometido.) | A Azure AD move o risco do utilizador para nenhum [Estado de risco = Dispensado; Nível de risco = -]. | Clicar em "Descartar o risco do utilizador" irá fechar todos os riscos no utilizador e nos insus de saúde anteriores. Esta ação não pode ser desfeita. Recomendamos que remediar o utilizador clicando em 'Redefinir a palavra-passe' ou solicitar ao utilizador que reinicie/altere as suas credenciais de forma segura. |

O feedback sobre as deteções de risco do utilizador na Proteção de Identidade é processado offline e pode demorar algum tempo a ser atualizado. A coluna do estado de processamento de risco fornecerá o estado atual do processamento de feedback.

![Estado de processamento de risco para relatório de utilizador arriscado](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Passos seguintes

- [Referência de deteção de risco de proteção de identidade ativa Azure Ative](./concept-identity-protection-risks.md)