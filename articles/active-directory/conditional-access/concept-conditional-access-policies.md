---
title: Criando uma política de acesso condicional-Azure Active Directory
description: Quais são todas as opções disponíveis para criar uma política de acesso condicional e o que elas significam?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f3e815f541ad4cfabc22d917ca9cecba47b50f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077610"
---
# <a name="building-a-conditional-access-policy"></a>Criando uma política de acesso condicional

Conforme explicado no artigo [o que é acesso condicional](overview.md), uma política de acesso condicional é uma instrução if-then, de **atribuições** e **controles de acesso**. Uma política de acesso condicional reúne sinais, para tomar decisões e impor políticas organizacionais.

Como uma organização cria essas políticas? O que é necessário?

![Acesso condicional (sinais + decisões + imposição = políticas)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Atribuições

A parte de atribuições controla quem, e e onde a política de acesso condicional.

### <a name="users-and-groups"></a>Utilizadores e grupos

Usuários e grupos atribuem quem a política incluirá ou excluirá. Essa atribuição pode incluir todos os usuários, grupos específicos de usuários, funções de diretório ou usuários convidados externos. 

### <a name="cloud-apps-or-actions"></a>Aplicações na cloud ou ações

Os aplicativos de nuvem ou as ações podem incluir ou excluir aplicativos de nuvem ou ações do usuário que estarão sujeitas à política.

### <a name="conditions"></a>Condições

Uma política pode conter várias condições.

#### <a name="sign-in-risk"></a>Risco de entrada

Para organizações com [Azure ad Identity Protection](../identity-protection/overview.md), as detecções de risco geradas podem influenciar suas políticas de acesso condicional.

#### <a name="device-platforms"></a>Plataformas de dispositivos

As organizações com várias plataformas de sistema operacional de dispositivos podem querer impor políticas específicas em diferentes plataformas. 

As informações usadas para calcular a plataforma de dispositivo vêm de fontes não verificadas, como cadeias de caracteres de agente do usuário que podem ser alteradas.

#### <a name="locations"></a>Localizações

Os dados de local são fornecidos por dados de localização geográfica de IP. Os administradores podem optar por definir locais e optar por marcá-los como confiáveis para os locais de rede de sua organização.

#### <a name="client-apps"></a>Aplicações do cliente

Por padrão, as políticas de acesso condicional se aplicam a aplicativos de navegador, aplicativos móveis e clientes de área de trabalho que dão suporte à autenticação moderna. 

Essa condição de atribuição permite que as políticas de acesso condicional destinam-se a aplicativos cliente específicos que não usam autenticação moderna. Esses aplicativos incluem clientes do Exchange ActiveSync, aplicativos mais antigos do Office que não usam autenticação moderna e protocolos de email como IMAP, MAPI, POP e SMTP.

#### <a name="device-state"></a>Estado do dispositivo

Esse controle é usado para excluir dispositivos que são ingressados no Azure AD híbrido ou marcados como em conformidade no Intune. Essa exclusão pode ser feita para bloquear dispositivos não gerenciados. 

## <a name="access-controls"></a>Controlos de acesso

A parte controles de acesso da política de acesso condicional controla como uma política é imposta.

### <a name="grant"></a>Conceder

#### <a name="block-access"></a>Bloquear acesso

Bloquear acesso faz exatamente isso, ele bloqueará o acesso nas atribuições especificadas. O controle de bloco é poderoso e deve ser atraente com o conhecimento apropriado.

#### <a name="grant-access"></a>Conceder acesso

O controle Grant pode disparar a imposição de um ou mais controles. 

- Exigir autenticação multifator (autenticação multifator do Azure)
- Exigir que o dispositivo seja marcado como compatível (Intune)
- Exigir dispositivo ingressado no Azure AD híbrido
- Requer aplicação aprovada do cliente
- Requer uma política de proteção da aplicação

Os administradores podem optar por exigir um dos controles anteriores ou todos os controles selecionados usando as opções a seguir. O padrão para vários controles é exigir todos.

- Exigir todos os controles selecionados (controle e controle)
- Exigir um dos controles selecionados (controle ou controle)

### <a name="session"></a>Sessão

Controles de sessão podem limitar a experiência 

- Utilizar as restrições da aplicação imposta
   - Atualmente, funciona somente com o Exchange Online e o SharePoint Online.
      - Passa informações do dispositivo para permitir o controle da experiência que concede acesso completo ou limitado.
- Utilizar Controlo de Aplicações de Acesso Condicional
   - Usa sinais de Microsoft Cloud App Security para fazer coisas como: 
      - Bloquear download, recortar, copiar e imprimir documentos confidenciais.
      - Monitore o comportamento de sessão arriscada.
      - Exigir rotulagem de arquivos confidenciais.
- Frequência de entrada
   - Capacidade de alterar a frequência de entrada padrão para autenticação moderna.
- Sessão de browser persistente
   - Permite que os usuários permaneçam conectados após fechar e reabrir a janela do navegador.

## <a name="simple-policies"></a>Políticas simples

Uma política de acesso condicional deve conter, no mínimo, o seguinte para ser aplicado:

- **Nome** da política.
- **Atribuições**
   - **Usuários e/ou grupos** aos quais aplicar a política.
   - **Aplicativos de nuvem ou ações** às quais aplicar a política.
- **Controles de acesso**
   - Controles **Grant** ou **Block**

![Política de acesso condicional em branco](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

## <a name="next-steps"></a>Passos Seguintes

[Simular comportamento de entrada usando a ferramenta de What If de acesso condicional](troubleshoot-conditional-access-what-if.md)

[Políticas de acesso condicional comum](concept-conditional-access-policy-common.md)

[Planejando uma implantação da autenticação multifator do Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md)

[Gerenciando a conformidade do dispositivo com o Intune](https://docs.microsoft.com/intune/device-compliance-get-started)

[Microsoft Cloud App Security e acesso condicional](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
