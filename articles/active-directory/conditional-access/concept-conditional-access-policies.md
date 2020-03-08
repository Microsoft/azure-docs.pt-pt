---
title: Construção de uma política de acesso condicional - Diretório Ativo Azure
description: Quais são todas as opções disponíveis para construir uma política de Acesso Condicional e o que significam?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d2ebcc885b4018f4d9c3ff1b525ffc19b1abdda
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671934"
---
# <a name="building-a-conditional-access-policy"></a>Construção de uma política de acesso condicional

Como explica do artigo [O que é o Acesso Condicional,](overview.md)uma política de Acesso Condicional é uma declaração se-então, de Atribuições e **controlos** de **acesso.** Uma política de acesso condicional reúne sinais, para tomar decisões e impor políticas organizacionais.

Como é que uma organização cria estas políticas? O que é necessário?

![Acesso Condicional (Sinais + Decisões + Execução = Políticas)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Atribuições

A porção de atribuição controla quem, o quê e onde da política de Acesso Condicional.

### <a name="users-and-groups"></a>Utilizadores e grupos

[Utilizadores e grupos](concept-conditional-access-users-groups.md) atribuem quem a política incluirá ou excluirá. Esta atribuição pode incluir todos os utilizadores, grupos específicos de utilizadores, funções de diretório ou utilizadores externos de hóspedes. 

### <a name="cloud-apps-or-actions"></a>Aplicações na app ou ações

[Aplicações ou ações na nuvem](concept-conditional-access-cloud-apps.md) podem incluir ou excluir aplicações na nuvem ou ações de utilizador que estarão sujeitas à política.

### <a name="conditions"></a>Condições

Uma política pode conter [múltiplas condições.](concept-conditional-access-conditions.md)

#### <a name="sign-in-risk"></a>Risco de inscrição

Para organizações com Proteção de [Identidade Azure AD,](../identity-protection/overview.md)as deteções de risco aí geradas podem influenciar as suas políticas de Acesso Condicional.

#### <a name="device-platforms"></a>Plataformas de dispositivos

Organizações com várias plataformas de sistemaoperativo de dispositivos podem querer impor políticas específicas em diferentes plataformas. 

As informações utilizadas para calcular a plataforma do dispositivo provêm de fontes não verificadas, como as cordas do agente utilizador que podem ser alteradas.

#### <a name="locations"></a>Localizações

Os dados de localização são fornecidos por dados de geolocalização IP. Os administradores podem optar por definir localizações e optar por marcar alguns como os de confiança para as localizações da sua organização.

#### <a name="client-apps"></a>Aplicativos para clientes

Por padrão, as políticas de Acesso Condicional aplicam-se a aplicações de navegador, aplicações móveis e clientes de desktop que suportam a autenticação moderna. 

Esta condição de atribuição permite que as políticas de Acesso Condicional direcionem aplicações específicas do cliente que não utilizem a autenticação moderna. Estas aplicações incluem clientes Exchange ActiveSync, aplicações antigas do Office que não utilizam autenticação moderna, e protocolos de correio como IMAP, MAPI, POP e SMTP.

#### <a name="device-state"></a>Estado do dispositivo

Este controlo é utilizado para excluir dispositivos híbridos Azure AD unidos, ou marcados como compatíveis em Intune. Esta exclusão pode ser feita para bloquear dispositivos não geridos. 

## <a name="access-controls"></a>Controlos de acesso

A parte de controlo de acesso saem da política de acesso condicional na forma como uma política é aplicada.

### <a name="grant"></a>Concessão

[Grant](concept-conditional-access-grant.md) fornece aos administradores um meio de aplicação da política onde podem bloquear ou conceder acesso.

#### <a name="block-access"></a>Acesso ao bloco

O acesso ao bloco faz isso mesmo, bloqueará o acesso ao abrigo das atribuições especificadas. O controlo do bloco é poderoso e deve ser exercido com o conhecimento adequado.

#### <a name="grant-access"></a>Conceder acesso

O controlo da subvenção pode desencadear a aplicação de um ou mais controlos. 

- Exigir autenticação multi-factor (autenticação azure multi-factor)
- Exigir que o dispositivo seja marcado como conforme (Intune)
- Exigir dispositivo ad ad hybrid Azure
- Requer aplicação aprovada do cliente
- Pedir uma política de proteção de aplicações

Os administradores podem optar por exigir um dos controlos anteriores ou todos os controlos selecionados utilizando as seguintes opções. O padrão para vários controlos é exigir todos.

- Exigir todos os controlos selecionados (controlo e controlo)
- Exigir um dos comandos selecionados (controlo ou controlo)

### <a name="session"></a>Sessão

[Os controlos de sessão](concept-conditional-access-session.md) podem limitar a experiência 

- Use restrições impostas por aplicações
   - Atualmente trabalha apenas com o Exchange Online e o SharePoint Online.
      - Passa informações do dispositivo para permitir o controlo da experiência que concede acesso completo ou limitado.
- Utilizar controlo de aplicativos de acesso condicional
   - Usa sinais do Microsoft Cloud App Security para fazer coisas como: 
      - Bloquear o download, cortar, copiar e imprimir documentos sensíveis.
      - Monitorize o comportamento da sessão de risco.
      - Exija a rotulagem de ficheiros sensíveis.
- Frequência de inscrição
   - Capacidade de alterar o sinal predefinido na frequência para a autenticação moderna.
- Sessão persistente do navegador
   - Permite que os utilizadores permaneçam inscritos após o fecho e reabertura da janela do navegador.

## <a name="simple-policies"></a>Políticas simples

Uma política de acesso condicional deve conter, no mínimo, o seguinte a aplicar:

- **Nome** da apólice.
- **Atribuições**
   - **Utilizadores e/ou grupos** para aplicar a política.
   - **Aplicativos ou ações** em nuvem para aplicar a política.
- **Controlos de acesso**
   - **Controlos de concessão** ou **bloco**

![Política de Acesso Condicional Em Branco](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

O artigo [Políticas de Acesso Condicional Comum](concept-conditional-access-policy-common.md) inclui algumas políticas que pensamos que seriam úteis para a maioria das organizações.

## <a name="next-steps"></a>Passos seguintes

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)

[Planejando uma implementação de autenticação azure multi-factor baseada na nuvem](../authentication/howto-mfa-getstarted.md)

[Gerir o cumprimento do dispositivo com o Intune](/intune/device-compliance-get-started)

[Segurança de aplicativos da Microsoft Cloud e acesso condicional](/cloud-app-security/proxy-intro-aad)
