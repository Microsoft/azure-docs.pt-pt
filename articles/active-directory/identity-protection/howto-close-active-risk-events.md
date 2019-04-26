---
title: Como fechar eventos de risco ativo no Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba mais sobre as opções que tem eventos de risco ativo fechar.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e003aec8fa5aeab587fa07acdae3a13b370a535
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453506"
---
# <a name="how-to-close-active-risk-events"></a>Como: Fechar eventos de risco ativos

Com o [eventos de risco](../reports-monitoring/concept-risk-events.md), Azure Active Directory Deteta indicadores para contas de utilizador potencialmente comprometidos. Como administrador, queira obter todos os eventos de risco fechados, para que os utilizadores afetados já não estão em risco.

Este artigo fornece uma descrição geral das opções adicionais, que terá de fechar eventos de risco de Active Directory.

## <a name="options-to-close-risk-events"></a>Opções para fechar eventos de risco 

O estado de um evento de risco está **Active Directory** ou **fechado**. Todos os eventos de risco ativo contribuem para o cálculo de um valor chamado de nível de risco do utilizador. O nível de risco do utilizador é um indicador (baixa, média, alta) para indicar a probabilidade de que uma conta foi comprometida. 

Para fechar eventos de risco de Active Directory, tem as seguintes opções:

- Exigir palavra-passe de reposição com uma política de risco do utilizador

- Manual de palavra-passe
 
- Dispensar todos os eventos de risco 

- Fechar eventos de risco individuais manualmente



## <a name="require-password-reset-with-a-user-risk-policy"></a>Exigir palavra-passe de reposição com uma política de risco do utilizador

Ao configurar a [política de acesso condicional de risco do utilizador](howto-user-risk-policy.md), pode requerer uma alteração de palavra-passe se um nível de risco de utilizador especificado tiver sido detetado automaticamente. 

![Repor palavra-passe](./media/howto-close-active-risk-events/13.png)

Uma reposição de palavra-passe fecha todos os eventos de riscos do Active Directory do utilizador relacionado e recupera a identidade para um estado seguro. Através de uma política de risco do utilizador é o método preferencial para fechar eventos de risco de Active Directory, porque este método é automatizado. Não há nenhuma interação necessária entre o suporte técnico ajuda e o utilizador afetado ou um administrador.

No entanto, através de uma política de risco de utilizador nem sempre é aplicável. Isto aplica-se, por exemplo, para:

- Utilizadores que não tenham sido registrados para autenticação multifator (MFA).
- Utilizadores com eventos de risco ativo que tem sido eliminados.
- Uma investigação revela que um evento de risco comunicados foi realizado pelo usuário legítimo.


## <a name="manual-password-reset"></a>Manual de palavra-passe

Se exigir uma palavra-passe através de uma política de risco de utilizador de reposição não é uma opção, pode obter todos os eventos de risco de um utilizador fechado com uma reposição de palavra-passe manual.

![Repor palavra-passe](./media/howto-close-active-risk-events/04.png)


A caixa de diálogo relacionada fornece dois métodos diferentes para repor uma palavra-passe:

![Repor palavra-passe](./media/howto-close-active-risk-events/05.png)


**Gerar uma palavra-passe temporária** -ao gerar uma palavra-passe temporária, pode imediatamente recupera uma identidade para um estado seguro. Este método requer interação com os utilizadores afetados porque precisam saber qual é a palavra-passe temporária. Pode, por exemplo, enviar a nova palavra-passe temporária para um endereço de correio eletrónico alternativo para o utilizador ou para o Gestor do utilizador. Uma vez que a palavra-passe é temporária, é pedido ao utilizador para alterar a palavra-passe durante o próximo início de sessão.


**Exigir que o utilizador para repor a palavra-passe** -que os utilizadores tenham de redefinir senhas permite auto-recuperação sem contatar o suporte técnico ou um administrador. Como no caso de uma política de risco do utilizador, este método só se aplica aos utilizadores que estão registados para o MFA. Para os utilizadores que não tenham sido registrados para o MFA ainda, esta opção não está disponível.


## <a name="dismiss-all-risk-events"></a>Dispensar todos os eventos de risco

Se uma palavra-passe reposição não é uma opção para, também pode dispensar todos os eventos de risco. 

![Repor palavra-passe](./media/howto-close-active-risk-events/03.png)

Quando clica em **dispensar todos os eventos**, todos os eventos são fechados e o utilizador afectado já não está em risco. No entanto, como esse método não tem um impacto sobre a palavra-passe existente, ela não recupera as relacionadas com identidade para um estado seguro. A utilização preferencial para esse método é um utilizador eliminado com eventos de risco ativo. 


## <a name="close-individual-risk-events-manually"></a>Fechar eventos de risco individuais manualmente

Pode fechar eventos de risco individuais manualmente. Se fechar eventos de risco manualmente, pode reduzir o nível de risco do utilizador. Normalmente, os eventos de risco são fechados manualmente em resposta a uma investigação relacionada. Por exemplo, ao conversar com um utilizador revela que um evento de risco de Active Directory não é necessário, mais. 
 
Ao fechar eventos de risco manualmente, pode optar por colocar qualquer uma das seguintes ações para alterar o estado de um evento de risco:

![Ações](./media/howto-close-active-risk-events/06.png)

- **Resolver** - se depois de investigar um evento de risco, que realizou uma ação de remediação adequadas fora de proteção de identidade e considera que deve ser considerado o evento de risco fechado, marque o evento como resolvido. Resolver eventos irão definir o estado do evento de risco para fechado e o evento de risco já não irá contribuir para o risco de utilizador.

- **Marcar como falso-positivos** -em alguns casos, pode investigar um evento de risco e detetar que incorretamente foi sinalizado como um risco. Pode ajudar a reduzir o número de tais ocorrências, marcando o evento de risco como falso positivo. Isso ajudará os algoritmos de machine learning para melhorar a classificação de eventos semelhantes no futuro. O estado de eventos de Falso-positivos é fechado e já não irão contribuir para o risco de utilizador.

- **Ignorar** – se não tomar qualquer ação de remediação, mas o evento de risco a serem removidos da lista Active Directory, pode marcar um evento de risco ignorar e o estado do evento será fechado. Eventos ignorados não contribuem para o risco de utilizador. Esta opção deve ser usada apenas em circunstâncias invulgares.

- **Reativar** -eventos de risco que foram fechados manualmente (escolhendo resolver, falso positivo ou ignorar) podem ser reativados, definir o estado do evento para o Active Directory. Eventos de risco reativado contribuem para o cálculo de nível de risco de utilizador. Eventos de risco fechados através de remediação (como repor uma palavra-passe segura) não podem ser reativados.
  

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview.md).
