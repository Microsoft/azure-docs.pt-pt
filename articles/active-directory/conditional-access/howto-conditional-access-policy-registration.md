---
title: Acesso Condicional - Informações combinadas de segurança - Diretório Ativo Azure
description: Crie uma política de acesso condicional personalizado para o registo de informações de segurança
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/29/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38ddd825321a3cb7947eb76c4f5f6cdbc6cb3414
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055710"
---
# <a name="conditional-access-securing-security-info-registration"></a>Acesso Condicional: Proteger o registo de informações de segurança

Garantir quando e como os utilizadores se registam para autenticação multi-factor AD Azure e redefinição de senha de autosserviço é possível com as ações do utilizador numa política de Acesso Condicional. Esta funcionalidade está disponível para organizações que tenham ativado o [registo combinado.](../authentication/concept-registration-mfa-sspr-combined.md) Esta funcionalidade permite que as organizações tratem o processo de registo como qualquer aplicação numa política de Acesso Condicional e utilizem todo o poder do Acesso Condicional para garantir a experiência. 

Algumas organizações no passado podem ter usado a localização da rede de confiança ou a conformidade do dispositivo como forma de garantir a experiência de registo. Com a adição do Passe de [Acesso Temporário](../authentication/howto-authentication-temporary-access-pass.md) em Azure AD, os administradores podem providenciar credenciais limitadas no tempo aos seus utilizadores que lhes permitam registar-se a partir de qualquer dispositivo ou local. As credenciais de Passe de Acesso Temporário satisfazem os requisitos de acesso condicional para a autenticação de vários fatores.

## <a name="create-a-policy-to-secure-registration"></a>Criar uma política para garantir o registo

A seguinte política aplica-se aos utilizadores selecionados, que tentam registar-se utilizando a experiência de registo combinado. A política exige que os utilizadores realizem a autenticação de vários fatores ou utilizem credenciais de Passe de Acesso Temporário.

1. No **portal Azure,** navegue pelo **Azure Ative Directory**  >    >  **Security Conditional Access**.
1. Selecione **Nova política**.
1. Em Nome, Insira um Nome para esta política. Por exemplo, **Registo de Informações de Segurança Combinadas com a TAP.**
1. Em **Atribuições**, selecione **Utilizadores e grupos**, e selecione os utilizadores e grupos a que pretende que esta política se aplique.
   1. Em **Incluir**, selecione **Todos os utilizadores**.

      > [!WARNING]
      > Os utilizadores devem estar habilitados para o [registo combinado.](../authentication/howto-registration-mfa-sspr-combined.md)

   1. Em **"Excluir".**
      1. Selecione **Todos os utilizadores convidados e externos**.
      
         > [!NOTE]
         > O Passe de Acesso Temporário não funciona para os utilizadores convidados.

      1. Selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
1. No **âmbito de aplicações ou ações cloud**, selecione as **ações do Utilizador,** consulte **as informações de segurança do Registo**.
1. Sob **controlos de acesso**  >  **Grant**.
   1. Selecione **Conceder acesso**.
   1. **Selecione Requera a autenticação de vários fatores.**
   1. Clique em **Selecionar**.
1. Defina **Ativar política** como **Ativado**.
1. Em seguida, selecione **Criar**.

Os administradores terão agora de emitir credenciais de Passe de Acesso Temporário a novos utilizadores para que possam satisfazer os requisitos para que a autenticação multi-factor se registe. Passos para realizar esta tarefa, encontram-se na secção [Criar um Passe de Acesso Temporário no Portal AD AZure](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass).

As organizações podem optar por exigir outros controlos de subvenção para além ou em vez de **exigirem a autenticação de vários fatores** no passo 6b. Ao selecionar vários controlos, certifique-se de que seleciona o botão de rádio apropriado para exigir **todos** ou **um dos** controlos selecionados ao escoar esta alteração.

### <a name="guest-user-registration"></a>Registo do utilizador convidado

Para [os utilizadores convidados](../external-identities/what-is-b2b.md) que necessitem de se registar para autenticação multi-fator no seu diretório, pode optar por bloquear o registo de fora de locais de [rede fidedignos](concept-conditional-access-conditions.md#locations) utilizando o seguinte guia.

1. No **portal Azure,** navegue pelo **Azure Ative Directory**  >    >  **Security Conditional Access**.
1. Selecione **Nova política**.
1. Em Nome, Insira um Nome para esta política. Por exemplo, **Registo combinado de informações de segurança em redes fidedignas**.
1. Em **Atribuições**, selecione **Utilizadores e grupos**, e selecione os utilizadores e grupos a que pretende que esta política se aplique.
   1. Em **Incluir**, selecione **Todos os utilizadores convidados e externos.**
1. No **âmbito de aplicações ou ações cloud**, selecione as **ações do Utilizador,** consulte **as informações de segurança do Registo**.
1. Em   >  **Locais de Condições**.
   1. Configurar **Sim.**
   1. Incluir **qualquer localização.**
   1. Excluir **todos os locais fidedignos.**
   1. Selecione **Feito** na lâmina de locais.
   1. Selecione **Feito** na lâmina Condições.
1. Sob **controlos de acesso**  >  **Grant**.
   1. Selecione **o acesso ao Bloco**.
   1. Em seguida, clique em **Selecionar**.
1. Defina **Ativar política** como **Ativado**.
1. Em seguida, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
