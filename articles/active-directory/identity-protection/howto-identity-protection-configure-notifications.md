---
title: Notificações de Proteção de Identidade Azure Ative
description: Saiba como as notificações suportam as suas atividades de investigação.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c786aaecd3ab2f18f242cea2f5c45838f9ecf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839352"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações de Proteção de Identidade Azure Ative

A Azure AD Identity Protection envia dois tipos de e-mails de notificação automatizados para ajudá-lo a gerir o risco do utilizador e as deteções de riscos:

- Utilizadores em risco detetado e-mail
- E-mail semanal de digestão

Este artigo fornece-lhe uma visão geral de ambos os e-mails de notificação.

## <a name="users-at-risk-detected-email"></a>Utilizadores em risco detetado e-mail

Em resposta a uma conta detetada em risco, a Azure AD Identity Protection gera um alerta de e-mail com **os Utilizadores em risco detetados** como sujeitos. O e-mail inclui um link para os **[Utilizadores sinalizados para](./overview-identity-protection.md)** relatório de risco. Como uma boa prática, deve investigar imediatamente os utilizadores em risco.

A configuração deste alerta permite especificar a que nível de risco o utilizador pretende que o alerta seja gerado. O e-mail será gerado quando o nível de risco do utilizador atingir o que especificou. Por exemplo, se definir a política para alertar sobre o risco médio do utilizador e a pontuação de risco do utilizador do utilizador do utilizador Desloca-se para risco médio devido a um risco de entrada em tempo real, receberá os utilizadores em risco detetado e-mail. Se o utilizador tiver subsequentes deteções de risco que façam com que o cálculo do nível de risco do utilizador seja o nível de risco especificado (ou superior), receberá e-mails adicionais detetados pelo utilizador quando a pontuação de risco do utilizador for recalculada. Por exemplo, se um utilizador se deslocar para risco médio no dia 1 de janeiro, receberá uma notificação por e-mail se as suas definições estiverem definidas para alertar para o risco médio. Se esse mesmo utilizador tiver então outra deteção de risco no dia 5 de janeiro, que também é de risco médio, e a pontuação de risco do utilizador for recalculada e ainda for média, receberá outra notificação de e-mail. 

No entanto, uma notificação adicional de e-mail só será enviada se o tempo de deteção de risco ocorrer (que causou a alteração do nível de risco do utilizador) for mais recente do que quando o último e-mail foi enviado. Por exemplo, um utilizador entra em sação no dia 1 de janeiro às 5 da manhã e não existe qualquer risco em tempo real (o que significa que nenhum e-mail seria gerado devido a essa inscrição). Dez minutos depois, às 5:10 da manhã, o mesmo utilizador volta a entrar e tem um elevado risco em tempo real, fazendo com que o nível de risco do utilizador se mova para alto e um e-mail seja enviado. Em seguida, às 5:15 da manhã, a pontuação de risco offline para o início de sôr entrada às 5 da manhã altera-se para alto risco devido ao processamento de risco offline. Um utilizador adicional sinalizado para o e-mail de risco não seria enviado, uma vez que a hora da primeira inscrição foi antes da segunda sindes que já desencadeou uma notificação por e-mail.

Para evitar uma sobrecarga de e-mails, receberá apenas um utilizadores em risco detetados num período de 5 segundos. Isto significa que se vários utilizadores se moverem para o nível de risco especificado durante o mesmo período de 5 segundos, agregaremos e enviaremos um e-mail para representar a mudança no nível de risco para todos eles.

![Utilizadores em risco detetado e-mail](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure utilizadores em alertas de risco detetados

Como administrador, pode definir:

- **O nível de risco do utilizador que desencadeia a geração deste e-mail** - Por padrão, o nível de risco é definido para risco "elevado".
- **Os destinatários deste e-mail** - Os utilizadores nas funções de administrador global, administrador de segurança ou leitor de Segurança são automaticamente adicionados a esta lista. Tentamos enviar e-mails aos primeiros 20 membros de cada função. Se um utilizador estiver inscrito na PIM para elevar para uma destas funções a pedido, só **receberá e-mails se estiver elevado no momento em que o e-mail for enviado**.
   - Opcionalmente pode **Adicionar e-mail personalizado aqui** os utilizadores definidos devem ter as permissões apropriadas para ver os relatórios ligados no portal Azure.

Configure os utilizadores em risco no **portal Azure** ao abrigo do **Azure Ative Directory**  >  **Security**  >  **Protection**  >  **Users em alertas detetados**.

## <a name="weekly-digest-email"></a>E-mail semanal de digestão

O e-mail semanal de digestão contém um resumo de novas deteções de risco.  
Inclui:

- Detetados novos utilizadores de risco
- Detetados novos sinais de risco (em tempo real)
- Ligações aos relatórios relacionados na Proteção de Identidade

![E-mail semanal de digestão](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Os utilizadores das funções de administrador global, administrador de segurança ou leitor de segurança são automaticamente adicionados a esta lista. Tentamos enviar e-mails aos primeiros 20 membros de cada função. Se um utilizador estiver inscrito na PIM para elevar a uma destas funções a pedido, só **receberá e-mails se for elevado no momento em que o e-mail for enviado.**

### <a name="configure-weekly-digest-email"></a>Configure e-mail semanal de digestão

Como administrador, pode mudar o envio de um e-mail semanal de digestão ligado ou desligado e escolher os utilizadores designados para receber o e-mail.

Configure o e-mail semanal de digestão no **portal Azure** no âmbito da **Azure Ative**  >  **Directory Security**Identity  >  **Protection**Weekly  >  **digest**.

## <a name="see-also"></a>Consulte também

- [Proteção de Identidade do Diretório Ativo Azure](./overview-identity-protection.md)
