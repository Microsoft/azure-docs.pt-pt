---
title: Perguntas frequentes para proteção de identidade em Diretório Ativo Azure
description: Perguntas frequentes da Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443575"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Perguntas frequentes sobre proteção de identidade no Diretório Ativo Azure

## <a name="dismiss-user-risk-known-issues"></a>Descartar problemas conhecidos do risco do utilizador

**Descartar o risco do utilizador** na proteção de identidade clássica define o ator no histórico de risco do utilizador em Proteção de Identidade para **AD Azure**.

**Descartar** o risco de utilizador na Proteção de Identidade define o ator no histórico de risco do utilizador em Proteção de Identidade para ** \<o nome de Admin com uma hiperligação apontando para a lâmina\>do utilizador**.

Existe um problema atual conhecido que causa latência no fluxo de despedimento do risco do utilizador. Se tiver uma "política de risco do utilizador", esta política deixará de se aplicar aos utilizadores dispensados minutos depois de clicar em "Descartar o risco do utilizador". No entanto, existem atrasos conhecidos com o UX a refrescar o "estado de risco" dos utilizadores dispensados. Como uma suver, refresque a página a nível do navegador para ver o mais recente utilizador "Risco".

## <a name="risky-users-report-known-issues"></a>Utilizadores de risco relatam problemas conhecidos

As consultas no campo do nome de **utilizador** são sensíveis a casos, enquanto as consultas no campo **Nome** são agnósticas.

Toggling **Mostrar datas como** esconde a coluna RISK LAST **UPDATED.** Para ler a coluna clique em **Colunas** na parte superior da lâmina Utilizadores Arriscados.

**Descartar todos os eventos** na Proteção de Identidade clássica define o estado das deteções de risco para **Fechado (resolvido)**.

## <a name="risky-sign-ins-report-known-issues"></a>Relatórios de inscrição arriscados reportam questões conhecidas

**A resolução** sobre uma deteção de risco define o estado para **utilizadores aprovados por MFA impulsionados por uma política baseada no risco**.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-is-a-user-is-at-risk"></a>Porque é que um utilizador está em risco?

Se for um cliente de Proteção de Identidade Azure AD, vá à visualização de [utilizadores de risco](howto-identity-protection-investigate-risk.md#risky-users) e clique num utilizador em risco. Na gaveta de baixo, o separador 'História de risco' mostrará todos os eventos que levaram a uma mudança de risco do utilizador. Para ver todos os insines de risco para o utilizador, clique em 'User's risky sign-ins'. Para ver todas as deteções de risco para este utilizador, clique em 'Deteções de risco do utilizador'.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Como posso obter um relatório de deteções de um tipo específico?

Vá à vista de deteções de risco e filtre por 'Tipo de detecção'. Em seguida, pode fazer o download deste relatório em . CSV ou . Formato JSON utilizando o botão **Descarregamento** na parte superior. Para mais informações, consulte o artigo [Como: Investigar](howto-identity-protection-investigate-risk.md#risk-detections)o risco.

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Por que não posso definir os meus próprios níveis de risco para cada deteção de risco?

Os níveis de risco na Proteção de Identidade baseiam-se na precisão da deteção e alimentados pela nossa aprendizagem automática supervisionada. Para personalizar a experiência que os utilizadores são apresentados, o administrador pode incluir/excluir certos utilizadores/grupos das Políticas de Risco do Utilizador e de Risco de Iniciar Sessão.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Porque é que a localização de um sinal de sessão não corresponde ao local onde o utilizador realmente se inscreveu?

O mapeamento de geolocalização IP é um desafio a nível da indústria. Se sentir que a localização indicada no relatório de inscrição não corresponde à localização real, contacte o suporte da Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Como posso fechar deteções específicas de risco, como fiz na antiga UI?

Pode dar feedback sobre deteções de risco confirmando o sign-in ligado como comprometido ou seguro. O feedback dado sobre o sinal de inscrição diminui para todas as deteções feitas nesse sign-in. Se pretender encerrar deteções que não estejam ligadas a um sessão, pode fornecer esse feedback ao nível do utilizador. Para mais informações, consulte o artigo Como: Dar feedback de [risco na Proteção de Identidade Da Azure AD](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Até onde posso voltar no tempo para perceber o que se passa com o meu utilizador?

- A visão arriscada dos [utilizadores](howto-identity-protection-investigate-risk.md#risky-users) mostra o risco de um utilizador estar em pé com base em todos os anteriores sign-ins. 
- A vista [de inscrições arriscadamostra sinais](howto-identity-protection-investigate-risk.md#risky-sign-ins) de risco nos últimos 30 dias. 
- A visão de [deteção](howto-identity-protection-investigate-risk.md#risk-detections) de riscos mostra deteções de risco feitas nos últimos 90 dias.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Como posso saber mais sobre uma deteção específica?

Todas as deteções de risco estão documentadas no artigo [O que é risco.](concept-identity-protection-risks.md#risk-types-and-detection) Pode pairar sobre o símbolo (i) ao lado da deteção no portal Azure para saber mais sobre uma deteção.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funcionam os mecanismos de feedback na Proteção de Identidade?

**Confirme comprometido** (em um sign-in) – Informa a Azure AD Identity Protection de que o sign-in não foi realizado pelo proprietário da identidade e indica um compromisso.

- Ao receber este feedback, movemos o estado de risco de inscrição e utilizador para confirmado com **prometido** e risco para **Alto**.

- Além disso, fornecemos a informação aos nossos sistemas de aprendizagem automática para futuras melhorias na avaliação de riscos.

    > [!NOTE]
    > Se o utilizador já estiver remediado, não clique em **Confirmar-se comprometido** porque move o estado de risco de inscrição e utilizador para confirmado com o nível de risco **comprometido** e de risco para **Alto**.

**Confirme seguro** (em um sign-in) – Informa a Azure AD Identity Protection que o sign-in foi realizado pelo proprietário da identidade e não indica um compromisso.

- Ao receber este feedback, movemos o estado de risco de inscrição (não **-** o utilizador) para confirmar **seguro** e o nível de risco para .

- Além disso, fornecemos a informação aos nossos sistemas de aprendizagem automática para futuras melhorias na avaliação de riscos.

    > [!NOTE]
    > Se acreditar que o utilizador não está comprometido, utilize **o risco de utilizador desativado** ao nível do utilizador em vez de utilizar o cofre **Confirmado** no nível de sessão. Um **Risco de utilizador de serrote** ao nível do utilizador fecha o risco do utilizador e todos os insines e deteções de risco de risco anteriores.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Porque é que estou a ver um utilizador com uma pontuação de risco baixa (ou superior), mesmo que não sejam mostrados inscrições arriscadas ou deteções de risco na Proteção de Identidade?

Dado que o risco do utilizador é cumulativo na natureza e não expira, um utilizador pode ter um risco de utilizador baixo ou superior, mesmo que não existam recentes inscrições de risco ou deteções de risco mostradas na Proteção de Identidade. Esta situação poderia acontecer se a única atividade maliciosa num utilizador ocorresse para além do prazo para o qual armazenamos os detalhes de inscrições arriscadas e deteções de risco. Não expiramos o risco do utilizador porque os maus atores são conhecidos por permanecerem no ambiente dos clientes mais de 140 dias atrás de uma identidade comprometida antes de intensificarem o seu ataque. Os clientes podem rever a linha de tempo de risco do utilizador para entender por que um utilizador está em risco indo para:`Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que razão um inato tem uma pontuação de "risco de entrada (agregado)" de Alta quando as deteções associadas a ele são de baixo ou médio risco?

A pontuação de risco agregado elevada pode basear-se em outras características do sign-in, ou no facto de mais de uma deteção ter disparado para esse sign-in. E, inversamente, um sign-in pode ter um risco de inscrição (agregado) de Medium, mesmo que as deteções associadas ao sign-in sejam de alto risco. 
