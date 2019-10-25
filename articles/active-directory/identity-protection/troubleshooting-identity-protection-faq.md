---
title: Perguntas frequentes sobre a proteção de identidade no Azure Active Directory
description: Perguntas frequentes Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d22973867782ddb64ced2ac95e84c0b27a3addd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887601"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Perguntas frequentes sobre a proteção de identidade em Azure Active Directory

## <a name="dismiss-user-risk-known-issues"></a>Ignorar problemas conhecidos de risco do usuário

**Ignorar o risco do usuário** na proteção de identidade clássica define o ator no histórico de riscos do usuário em identidade Protection para o **Azure ad**.

**Ignorar o risco do usuário** no Identity Protection define o ator no histórico de riscos do usuário em identidade protection para **\<nome do administrador com um hiperlink apontando para a folha do usuário\>** .

Há um problema atual conhecido causando latência no fluxo de perda de risco do usuário. Se você tiver uma "política de risco do usuário", essa política deixará de ser aplicada a usuários ignorados em minutos após o clique em "ignorar risco do usuário". No entanto, há atrasos conhecidos com o UX atualizando o "estado de risco" de usuários ignorados. Como alternativa, atualize a página no nível do navegador para ver o último usuário "estado de risco".

## <a name="risky-users-report-known-issues"></a>Os usuários arriscados relatam problemas conhecidos

As consultas no campo nome de **usuário** diferenciam maiúsculas de minúsculas, enquanto as consultas no campo **nome** não diferenciam maiúsculas de minúsculas.

A alternância de **Mostrar datas como** oculta a coluna **risco da última atualização** . Para ler a coluna, clique em **colunas** na parte superior da folha usuários arriscados.

**Ignorar todos os eventos** em clássico identidade Protection define o status das detecções de risco para **fechado (resolvido)** .

## <a name="risky-sign-ins-report-known-issues"></a>Problemas conhecidos do relatório de entradas arriscadas

**Resolver** em uma detecção de risco define o status para **os usuários passaram pela MFA controlada pela política baseada em risco**.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Por que não posso definir meus próprios níveis de risco para cada detecção de riscos?

Os níveis de risco na proteção de identidade baseiam-se na precisão da detecção e da plataforma de aprendizado de máquina supervisionado. Para personalizar a experiência que os usuários são apresentados, o administrador pode incluir/excluir determinados usuários/grupos das políticas de risco do usuário e de entrada.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Por que o local de uma entrada não corresponde onde o usuário realmente se conectou?

O mapeamento de localização geográfica de IP é um desafio em todo o setor. Se você sentir que o local listado no relatório de entradas não corresponde ao local real, entre em contato com o suporte da Microsoft. 

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funciona o mecanismo de comentários no Identity Protection?

**Confirmar comprometimento** (em uma entrada) – informa Azure ad Identity Protection que a entrada não foi executada pelo proprietário da identidade e indica um comprometimento.

- Ao receber esses comentários, movemos o estado de entrada e de risco do usuário para o nível de risco e **comprometido confirmado** como **alto**.

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para aprimoramentos futuros na avaliação de riscos.

    > [!NOTE]
    > Se o usuário já foi corrigido, não clique em **confirmar comprometido** porque ele move a entrada e o estado de risco do usuário para o nível de risco e **comprometido confirmado** para **alto**.

**Confirmar segurança** (em uma entrada) – informa Azure ad Identity Protection que a entrada foi executada pelo proprietário da identidade e não indica um comprometimento.

- Ao receber esses comentários, movemos o estado de risco de entrada (não o usuário) para a **confirmação de segurança** e o nível de risco para **-** .

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para aprimoramentos futuros na avaliação de riscos.

    > [!NOTE]
    > Se você acreditar que o usuário não está comprometido, use **ignorar o risco do usuário** no nível do usuário, em vez de usar a **segurança confirmada** no nível de entrada. Um **risco de ignorar usuário** no nível do usuário fecha o risco do usuário e todos os logons e as detecções de risco anteriores.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Por que estou vendo um usuário com uma pontuação de risco baixa (ou acima), mesmo que nenhuma entrada arriscada ou detecção de risco seja mostrada na proteção de identidade?

Considerando que o risco do usuário é cumulativo por natureza e não expira, um usuário pode ter um risco de usuário baixo ou superior, mesmo que não haja entradas arriscadas recentes ou detecções de risco mostradas na proteção de identidade. Essa situação pode acontecer se a única atividade mal-intencionada em um usuário fosse feita além do período de tempo para o qual armazenamos os detalhes de entradas arriscadas e detecções de risco. Não expiramos o risco do usuário porque os atores inválidos permanecem no ambiente dos clientes em mais de 140 dias atrás de uma identidade comprometida antes de aumentar o ataque. Os clientes podem examinar a linha do tempo de risco do usuário para entender por que um usuário está em risco, acessando: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que uma entrada tem uma pontuação alta de "risco de entrada (agregada)" quando as detecções associadas a ela são de risco baixo ou médio?

A pontuação de risco de agregação alta pode ser baseada em outros recursos da entrada ou no fato de que mais de uma detecção foi acionada para essa entrada. E, por outro lado, uma entrada pode ter um risco de entrada (agregação) de médio, mesmo se as detecções associadas à entrada forem de alto risco. 
