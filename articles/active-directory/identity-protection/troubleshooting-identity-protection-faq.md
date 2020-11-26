---
title: FAQs para proteção de identidade em Diretório Ativo Azure
description: Perguntas frequentes Azure AD Proteção de Identidade
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba22745923f1b157c477dce0b5704c7e278e748e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96176006"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Perguntas frequentes proteção de identidade no Diretório Ativo Azure

## <a name="dismiss-user-risk-known-issues"></a>Descartar problemas conhecidos do risco do utilizador

**Descartar o risco do utilizador** na clássica Proteção de Identidade define o ator no histórico de risco do utilizador na Proteção de Identidade para **Azure AD**.

**Descartar o risco de utilização** na Proteção de Identidade define o ator no histórico de risco do utilizador na Proteção de Identidade para **\<Admin’s name with a hyperlink pointing to user’s blade\>** .

Existe um problema atual conhecido que causa latência no fluxo de despedimento de risco do utilizador. Se tiver uma "política de risco de utilizador", esta política deixará de se aplicar aos utilizadores dispensados poucos minutos depois de clicar em "Dispensar o risco do utilizador". No entanto, são conhecidos os atrasos com o UX a refrescar o "estado de risco" dos utilizadores dispensados. Como solução, atualize a página no nível do navegador para ver o mais recente utilizador "Estado de risco".


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-is-a-user-is-at-risk"></a>Por que um utilizador está em risco?

Se for um cliente Azure AD Identity Protection, vá ao ponto de vista [dos utilizadores arriscados](howto-identity-protection-investigate-risk.md#risky-users) e clique num utilizador em risco. Na gaveta de baixo, o separador 'Histórico de risco' mostrará todos os eventos que levaram a uma mudança de risco do utilizador. Para ver todos os insus máximos de segurança para o utilizador, clique em 'Ins de risco do utilizador'. Para ver todas as deteções de risco para este utilizador, clique em "Deteções de risco do utilizador".

## <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>Porque é que a minha inscrição estava bloqueada, mas a Proteção de Identidade não gerou uma deteção de risco?
As inscrições podem ser bloqueadas por várias razões. É importante notar que a Proteção de Identidade só gera deteções de risco quando são utilizadas credenciais corretas no pedido de autenticação. Se um utilizador utilizar credenciais incorretas, não será sinalizado pela Proteção de Identidade, uma vez que não existe risco de compromisso credencial, a menos que um mau ator utilize as credenciais corretas. Algumas razões pelas quais um utilizador pode ser impedido de assinar que não gerará uma deteção de Proteção de Identidade incluem:
* O **IP pode ser bloqueado** devido a atividades maliciosas a partir do endereço IP. A mensagem bloqueada ip não diferencia se as credenciais estavam corretas ou não. Se o IP estiver bloqueado e não forem utilizadas credenciais corretas, não gerará uma deteção de Proteção de Identidade
* **[Smart Lockout](../authentication/howto-password-smart-lockout.md)** pode bloquear a inscrição da conta após várias tentativas falhadas
* Uma **política de acesso condicional** pode ser aplicada que usa condições que não o nível de risco para bloquear um pedido de autenticação

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Como posso obter um relatório de deteções de um tipo específico?

Aceda à visualização e filtro de deteção de risco por "Tipo de Deteção". Em seguida, pode fazer o download deste relatório em . CSV ou . . Formato JSON utilizando o botão **Descarregar** na parte superior. Para mais informações, consulte o artigo [Como: Investigar o risco.](howto-identity-protection-investigate-risk.md#risk-detections)

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Por que não posso definir os meus próprios níveis de risco para cada deteção de risco?

Os níveis de risco na Proteção de Identidade baseiam-se na precisão da deteção e alimentados pela nossa aprendizagem automática supervisionada. Para personalizar a experiência que os utilizadores são apresentados, o administrador pode incluir/excluir certos utilizadores/grupos das Políticas de Risco do Utilizador e Sign-In De Risco.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Por que a localização de um sinal não corresponde de onde o utilizador realmente se inscreveu?

O mapeamento de geolocalização IP é um desafio a nível da indústria. Se sentir que a localização listada no relatório de insusição não corresponde à localização real, contacte o suporte da Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Como posso encerrar deteções específicas de risco como fiz na antiga UI?

Pode dar feedback sobre deteções de risco confirmando o insusitado ligado como comprometido ou seguro. O feedback dado no sinal de inscrição resume-se a todas as deteções feitas nesse s-in. Se pretender encerrar deteções que não estejam ligadas a uma sindagem, pode fornecer esse feedback ao nível do utilizador. Para obter mais informações, consulte o artigo [Como: Dar feedback de risco na Proteção de Identidade AZure AD](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Até onde posso voltar no tempo para perceber o que se passa com o meu utilizador?

- A visualização [arriscada dos utilizadores](howto-identity-protection-investigate-risk.md#risky-users) mostra o risco de um utilizador estar em pé com base em todos os insus máximos de sposições anteriores. 
- A visão de entradas de risco mostra sinais de risco nos [últimos](howto-identity-protection-investigate-risk.md#risky-sign-ins) 30 dias. 
- A visão [de deteção de risco](howto-identity-protection-investigate-risk.md#risk-detections) mostra deteções de risco feitas nos últimos 90 dias.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Como posso saber mais sobre uma deteção específica?

Todas as deteções de risco estão documentadas no artigo [O que é o risco](concept-identity-protection-risks.md#risk-types-and-detection). Pode pairar sobre o símbolo (i) ao lado da deteção no portal Azure para saber mais sobre uma deteção.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funcionam os mecanismos de feedback na Proteção de Identidade?

**Confirmar comprometido** (em uma entrada) – Informa a Azure AD Identity Protection que a inscrição não foi realizada pelo titular da identidade e indica um compromisso.

- Ao recebermos este feedback, movemos o estado de início de saúde e o estado de risco do utilizador para **confirmado comprometido** e nível de risco para **Alto.**

- Além disso, fornecemos a informação aos nossos sistemas de aprendizagem automática para futuras melhorias na avaliação de riscos.

    > [!NOTE]
    > Se o utilizador já estiver remediado, não clique em **Confirmar comprometido** porque move o estado de entrada e risco do utilizador para **confirmado comprometido** e nível de risco para **Alto**.

**Confirmar segurança** (em sinal de entrada) – Informa a Azure AD Identity Protection que a inscrição foi realizada pelo titular da identidade e não indica um compromisso.

- Ao recebermos este feedback, movemos o estado de risco de inscrição (não do utilizador) para **confirmado seguro** e o nível de risco para **-** .

- Além disso, fornecemos a informação aos nossos sistemas de aprendizagem automática para futuras melhorias na avaliação de riscos.

    > [!NOTE]
    > Se acreditar que o utilizador não está comprometido, utilize **o risco do utilizador desprezado** ao nível do utilizador em vez de utilizar o cofre **Confirmado** no nível de inscrição. Um **risco de utilizador dedessaltado** ao nível do utilizador fecha o risco do utilizador e todas as insuposições de risco passadas e deteções de risco.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Porque é que estou a ver um utilizador com uma pontuação de risco baixa (ou acima), mesmo que não sejam apresentados insusitados de risco ou deteções de risco na Proteção de Identidade?

Dado que o risco do utilizador é cumulativo de natureza e não expira, um utilizador pode ter um risco de utilização baixo ou superior, mesmo que não existam recentes insuposições de risco ou deteções de risco mostradas na Proteção de Identidade. Esta situação poderia acontecer se a única atividade maliciosa num utilizador ocorresse para além do prazo para o qual armazenamos os detalhes de insusitadas de risco e deteções de riscos. Não expiramos o risco do utilizador porque os maus atores são conhecidos por permanecerem no ambiente dos clientes mais de 140 dias atrás de uma identidade comprometida antes de aumentarem o seu ataque. Os clientes podem rever a linha temporal de risco do utilizador para entender por que um utilizador está em risco ao ir: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que razão um sinal tem uma pontuação de "risco de entrada (agregado)" de High quando as deteções associadas a ele são de baixo ou médio risco?

A pontuação de risco agregada elevada pode basear-se em outras características do sinal, ou no facto de mais de uma deteção ter disparado para esse sinal. E inversamente, um sinal pode ter um risco de inscrição (agregado) de Medium, mesmo que as deteções associadas à inscrição sejam de alto risco.