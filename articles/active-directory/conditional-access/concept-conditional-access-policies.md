---
title: Construção de uma política de acesso condicional - Diretório Ativo Azure
description: Quais são todas as opções disponíveis para construir uma política de acesso condicional e o que significam?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab632010ec2bbbc19ac71cbeccab2ff6b3565f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948390"
---
# <a name="building-a-conditional-access-policy"></a>Construção de uma política de acesso condicional

Como explicado no artigo [O que é Acesso Condicional](overview.md), uma política de acesso condicional é uma declaração se-então, de atribuições e **controlos** de **acesso**. Uma política de acesso condicional reúne sinais, para tomar decisões e impor políticas organizacionais.

Como é que uma organização cria estas políticas? O que é necessário?

![Acesso Condicional (Sinais + Decisões + Execução = Políticas)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Atribuições

A parte das atribuições controla quem, o quê e onde da política de acesso condicional.

### <a name="users-and-groups"></a>Utilizadores e grupos

[Os utilizadores e grupos](concept-conditional-access-users-groups.md) atribuem quem a apólice incluirá ou excluirá. Esta atribuição pode incluir todos os utilizadores, grupos específicos de utilizadores, funções de diretório ou utilizadores de convidados externos. 

### <a name="cloud-apps-or-actions"></a>Aplicações na app ou ações

[Aplicações ou ações na nuvem](concept-conditional-access-cloud-apps.md) podem incluir ou excluir aplicações em nuvem ou ações do utilizador que estarão sujeitas à política.

### <a name="conditions"></a>Condições

Uma política pode conter múltiplas [condições](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Risco de início de sessão

Para organizações com [Proteção de Identidade AZure AD,](../identity-protection/overview-identity-protection.md)as deteções de risco aí geradas podem influenciar as suas políticas de Acesso Condicional.

#### <a name="device-platforms"></a>Plataformas de dispositivos

As organizações com múltiplas plataformas de sistema operativo de dispositivos podem querer impor políticas específicas em diferentes plataformas. 

As informações utilizadas para calcular a plataforma do dispositivo provêm de fontes não verificadas, como as cadeias de agente do utilizador que podem ser alteradas.

#### <a name="locations"></a>Localizações

Os dados de localização são fornecidos por dados de geolocalização IP. Os administradores podem optar por definir localizações e optar por marcar alguns como confiáveis como os das localizações de rede da sua organização.

#### <a name="client-apps"></a>Aplicações do cliente

Por predefinição, as políticas de Acesso Condicional aplicam-se a aplicações de navegador, aplicações móveis e clientes de desktop que suportam a autenticação moderna. 

Esta condição de atribuição permite que as políticas de Acesso Condicional direcionem aplicações específicas do cliente que não utilizem a autenticação moderna. Estas aplicações incluem clientes Exchange ActiveSync, aplicações antigas do Office que não utilizam a autenticação moderna, e protocolos de correio como IMAP, MAPI, POP e SMTP.

#### <a name="device-state"></a>Estado do dispositivo

Este controlo é utilizado para excluir dispositivos que sejam híbridos Azure AD unidos, ou marcados como conformes no Intune. Esta exclusão pode ser feita para bloquear dispositivos não geridos. 

## <a name="access-controls"></a>Controlos de acesso

A parte dos controlos de acesso da política de acesso condicional controla a forma como uma política é aplicada.

### <a name="grant"></a>Concessão

[Grant](concept-conditional-access-grant.md) fornece aos administradores um meio de aplicação da política onde podem bloquear ou conceder acesso.

#### <a name="block-access"></a>Bloquear o acesso

O acesso ao bloco faz isso mesmo, bloqueará o acesso sob as atribuições especificadas. O controlo de blocos é poderoso e deve ser exercido com o conhecimento adequado.

#### <a name="grant-access"></a>Conceder acesso

O controlo da concessão pode desencadear a aplicação de um ou mais controlos. 

- Requerem autenticação multi-factor (Autenticação multi-factor Azure)
- Exigir que o dispositivo seja marcado como conforme (Intune)
- Requera o dispositivo de ad AD híbrido Azure
- Requera uma aplicação de cliente aprovada
- Pedir uma política de proteção de aplicações

Os administradores podem optar por exigir um dos controlos anteriores ou todos os controlos selecionados utilizando as seguintes opções. O padrão para múltiplos controlos é exigir tudo.

- Exigir todos os controlos selecionados (controlo e controlo)
- Exigir um dos controlos selecionados (controlo ou controlo)

### <a name="session"></a>Sessão

[Os controlos de](concept-conditional-access-session.md) sessão podem limitar a experiência 

- Use restrições impostas por aplicativos
   - Atualmente funciona apenas com Exchange Online e SharePoint Online.
      - Passa informações do dispositivo para permitir o controlo da experiência que concede acesso total ou limitado.
- Utilizar controlo de aplicativos de acesso condicional
   - Utiliza sinais da Microsoft Cloud App Security para fazer coisas como: 
      - Bloqueie o download, corte, cópia e impressão de documentos sensíveis.
      - Monitorize o comportamento da sessão de risco.
      - Requerem a rotulagem de ficheiros sensíveis.
- Frequência de inscrição
   - Capacidade de alterar o sinal predefinido na frequência para a autenticação moderna.
- Sessão de navegador persistente
   - Permite que os utilizadores permaneçam assinados após o fecho e reabertura da janela do navegador.

## <a name="simple-policies"></a>Políticas simples

Uma política de acesso condicional deve conter, no mínimo, o seguinte a aplicar:

- **O nome** da apólice.
- **Atribuições**
   - **Utilizadores e/ou grupos** a aplicar a política.
   - **Aplicativos ou ações** em nuvem para aplicar a política.
- **Controlos de acesso**
   - **Controlos** de subvenções ou **blocos**

![Política de Acesso Condicional em Branco](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

O artigo [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md) inclui algumas políticas que pensamos que seriam úteis para a maioria das organizações.

## <a name="next-steps"></a>Passos seguintes

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)

[Planear a implementação da Multi-Factor Authentication do Azure baseada na cloud](../authentication/howto-mfa-getstarted.md)

[Gestão da conformidade do dispositivo com a Intune](/intune/device-compliance-get-started)

[Segurança e Acesso Condicional da Aplicação Microsoft Cloud](/cloud-app-security/proxy-intro-aad)