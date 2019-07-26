---
title: Como fechar eventos de risco ativos no Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba mais sobre as opções de fechar eventos de risco ativos.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5e24c12b72852ee7009533c8dc24d231fe636f2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333992"
---
# <a name="how-to-close-active-risk-events"></a>Como: Fechar eventos de risco ativos

Com [eventos de risco](../reports-monitoring/concept-risk-events.md), Azure Active Directory detecta indicadores para contas de usuário potencialmente comprometidas. Como administrador, você deseja obter todos os eventos de risco fechados, para que os usuários afetados não estejam mais em risco.

Este artigo fornece uma visão geral das opções adicionais que você precisa para fechar os eventos de risco ativos.

## <a name="options-to-close-risk-events"></a>Opções para fechar eventos de risco 

O status de um evento de risco é **ativo** ou **fechado**. Todos os eventos de risco ativos contribuem para o cálculo de um valor chamado nível de risco do usuário. O nível de risco do usuário é um indicador (baixo, médio, alto) para a probabilidade de que uma conta tenha sido comprometida. 

Para fechar os eventos de risco ativos, você tem as seguintes opções:

- Exigir redefinição de senha com uma política de risco de usuário
- Redefinição de senha manual
- Ignorar todos os eventos de risco 
- Fechar eventos de risco individuais manualmente

## <a name="require-password-reset-with-a-user-risk-policy"></a>Exigir redefinição de senha com uma política de risco de usuário

Ao configurar a [política de acesso condicional de risco do usuário](howto-user-risk-policy.md), você poderá exigir uma alteração de senha se um nível de risco do usuário especificado tiver sido detectado automaticamente. 

![Repor palavra-passe](./media/howto-close-active-risk-events/13.png)

Uma redefinição de senha fecha todos os eventos de riscos ativos do usuário relacionado e coloca a identidade novamente em um estado seguro. O uso de uma política de risco do usuário é o método preferencial para fechar os eventos de risco ativos, pois esse método é automatizado. Não há nenhuma interação necessária entre o usuário afetado e o suporte técnico ou um administrador.

No entanto, o uso de uma política de risco do usuário nem sempre é aplicável. Isso se aplica, por exemplo, a:

- Usuários que não foram registrados para MFA (autenticação multifator).
- Usuários com eventos de risco ativos que foram excluídos.
- Uma investigação que revela que um evento de risco relatado foi executado pelo usuário legítimo.

## <a name="manual-password-reset"></a>Redefinição de senha manual

Se exigir uma redefinição de senha usando uma política de risco do usuário não for uma opção, você poderá obter todos os eventos de risco de um usuário fechado com uma redefinição de senha manual.

![Repor palavra-passe](./media/howto-close-active-risk-events/04.png)

A caixa de diálogo relacionada fornece dois métodos diferentes para redefinir uma senha:

![Repor palavra-passe](./media/howto-close-active-risk-events/05.png)

**Gerar uma senha temporária** , gerando uma senha temporária, você pode imediatamente colocar uma identidade de volta em um estado seguro. Esse método requer interação com os usuários afetados porque eles precisam saber qual é a senha temporária. Você pode, por exemplo, enviar a nova senha temporária para um endereço de email alternativo para o usuário ou para o gerente do usuário. Como a senha é temporária, o usuário é solicitado a alterar a senha durante a próxima entrada.

**Exigir que o usuário redefina a senha** – exigir que os usuários redefinam senhas permite a AutoRecuperação sem entrar em contato com o suporte técnico ou um administrador. Como no caso de uma política de risco de usuário, esse método só se aplica a usuários registrados para MFA. Para usuários que ainda não foram registrados para MFA, essa opção não está disponível.

## <a name="dismiss-all-risk-events"></a>Ignorar todos os eventos de risco

Se uma redefinição de senha não for uma opção para você, você também poderá ignorar todos os eventos de risco. 

![Repor palavra-passe](./media/howto-close-active-risk-events/03.png)

Quando você clica em **ignorar todos os eventos**, todos os eventos são fechados e o usuário afetado não está mais em risco. No entanto, como esse método não tem um impacto na senha existente, ele não coloca a identidade relacionada novamente em um estado seguro. O caso de uso preferencial para esse método é um usuário excluído com eventos de risco ativo. 

## <a name="close-individual-risk-events-manually"></a>Fechar eventos de risco individuais manualmente

Você pode fechar os eventos de risco individuais manualmente. Ao fechar os eventos de risco manualmente, você pode diminuir o nível de risco do usuário. Normalmente, os eventos de risco são fechados manualmente em resposta a uma investigação relacionada. Por exemplo, quando conversar com um usuário revela que um evento de risco ativo não é mais necessário. 
 
Ao fechar eventos de risco manualmente, você pode optar por executar qualquer uma das seguintes ações para alterar o status de um evento de risco:

![Ações](./media/howto-close-active-risk-events/06.png)

- **Resolver** – se, depois de investigar um evento de risco, você tomou uma ação de correção apropriada fora do Identity Protection e acreditar que o evento de risco deve ser considerado fechado, marcar o evento como resolvido. Os eventos resolvidos definirão o status do evento de risco como fechado e o evento de risco não contribuirá mais para o risco do usuário.
- **Marcar como falso-positivo** -em alguns casos, você pode investigar um evento de risco e descobrir que ele foi sinalizado incorretamente como um risco. Você pode ajudar a reduzir o número de ocorrências desse tipo marcando o evento de risco como falso-positivo. Isso ajudará os algoritmos de aprendizado de máquina a melhorar a classificação de eventos semelhantes no futuro. O status de eventos falsos positivos é fechado e não contribuirá mais para o risco do usuário.
- **Ignorar** – se você não executou nenhuma ação de correção, mas deseja que o evento de risco seja removido da lista ativa, você pode marcar um evento de risco como ignorar e o status do evento será fechado. Os eventos ignorados não contribuem para o risco do usuário. Essa opção só deve ser usada em circunstâncias incomuns.
- **Reativar** -os eventos de risco que foram fechados manualmente (escolhendo resolver, falso positivo ou ignorar) podem ser reativados, definindo o status do evento de volta como ativo. Os eventos de risco reativados contribuem para o cálculo no nível de risco do usuário. Os eventos de risco fechados por meio da correção (como uma redefinição de senha segura) não podem ser reativados.

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [visão geral Azure ad Identity Protection](overview.md).
