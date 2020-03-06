---
title: Políticas de proteção de aplicativos com Acesso Condicional - Diretório Ativo Azure
description: Saiba como exigir a política de proteção de aplicações para o acesso de apps na nuvem com acesso condicional no Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d8cdb32e04f9ba1274291430ac230107f3150c6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298382"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Como: Exigir a política de proteção de aplicações e uma aplicação de cliente aprovada para acesso a aplicações na nuvem com acesso condicional

As pessoas usam regularmente os seus dispositivos móveis para tarefas pessoais e de trabalho. Ao mesmo tempo que asseguram que o pessoal pode ser produtivo, as organizações também querem evitar a perda de dados de aplicações potencialmente inseguras. Com o Acesso Condicional, as organizações podem restringir o acesso a aplicações de clientes aprovadas (modernas capazes de autenticação) com políticas de proteção de aplicações Intune aplicadas a elas.

Este artigo apresenta dois cenários para configurar políticas de Acesso Condicional para recursos como Office 365, Exchange Online e SharePoint Online.

- [Cenário 1: As aplicações do Office 365 exigem aplicações aprovadas com políticas de proteção de aplicações](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 2: Trocar Online e SharePoint Online exigem uma aplicação de clientes aprovada e política de proteção de aplicações](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

No Acesso Condicional, estas aplicações de clientes são conhecidas por estarem protegidas com uma política de proteção de aplicações. Mais informações sobre políticas de proteção de apps podem ser encontradas no artigo, visão geral das políticas de proteção de [aplicações](/intune/apps/app-protection-policy)

Para obter uma lista de aplicações de clientes elegíveis, consulte o requisito da política de proteção de [aplicações](concept-conditional-access-grant.md).

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 1: As aplicações do Office 365 exigem aplicações aprovadas com políticas de proteção de aplicações

Neste cenário, a Contoso decidiu que todos os recursos de acesso móvel ao Office 365 devem utilizar aplicações de clientes aprovadas, como o Outlook mobile, OneDrive e Microsoft Teams protegidos por uma política de proteção de aplicações antes de receberem acesso. Todos os seus utilizadores já assinam com credenciais Da Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar as seguintes etapas de forma a exigir a utilização de uma aplicação de cliente aprovada em dispositivos móveis.

**Passo 1: Configure uma política de acesso condicional Azure AD para o Office 365**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue no **Diretório Ativo do Azure** > **Segurança** > **Acesso Condicional.**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os utilizadores e **grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. Em **aplicações ou ações cloud** > **Incluir**, selecione **Office 365 (pré-visualização)** .
1. Em **Condições,** selecione **plataformas de dispositivos.**
   1. Definir **Configurar** para **Sim**.
   1. Inclua **Android** e **iOS.**
1. Em **Condições,** selecione **aplicações do Cliente (pré-visualização)** .
   1. Definir **Configurar** para **Sim**.
   1. Selecione **aplicativos Móveis e clientes de desktop** e clientes de **autenticação moderna.**
1. Sob **os controlos de acesso** > **Grant,** selecione as seguintes opções:
   - **Exigir aplicação de cliente aprovada**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Exigir todos os controlos selecionados**
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Configure uma política de acesso condicional Azure AD para troca online com ActiveSync (EAS)**

Para a política de Acesso Condicional neste passo, configure os seguintes componentes:

1. Navegue no **Diretório Ativo do Azure** > **Segurança** > **Acesso Condicional.**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os utilizadores e **grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. Sob **aplicações ou ações cloud** > **Incluir**, selecione **Office 365 Exchange Online**.
1. Em **condições:**
   1. **Aplicativos para clientes (pré-visualização)** :
      1. Definir **Configurar** para **Sim**.
      1. Selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**
1. Sob **controlos de acesso** > **Grant**, selecione **Grant access**, **Requerer aplicação de cliente aprovada**, e selecione **Select**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 3: Configure Política de Proteção de Aplicações Intune para aplicações de iOS e clientes Android**

Reveja o artigo Como criar e atribuir políticas de [proteção de aplicações,](/intune/apps/app-protection-policies)para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Cenário 2: Trocar Online e SharePoint Online exigem uma aplicação de clientes aprovada e política de proteção de aplicações

Neste cenário, a Contoso decidiu que os utilizadores só podem aceder a dados de email e SharePoint em dispositivos móveis, desde que utilizem uma aplicação de cliente aprovada como o Outlook mobile protegido por uma política de proteção de aplicações antes de receberem acesso. Todos os seus utilizadores já assinam com credenciais Da Azure AD e têm licenças atribuídas a eles que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar os três passos seguintes para exigir a utilização de uma aplicação de cliente aprovada em dispositivos móveis e clientes Exchange ActiveSync.

**Passo 1: Política para clientes de autenticação moderna baseados em Android e iOS que exigem o uso de uma aplicação de cliente aprovada e política de proteção de aplicações ao aceder ao Exchange Online e ao SharePoint Online.**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue no **Diretório Ativo do Azure** > **Segurança** > **Acesso Condicional.**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os utilizadores e **grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. Em **aplicações ou ações da Cloud** > **Incluir**, selecione **Office 365 Exchange Online** e Office **365 SharePoint Online**.
1. Em **Condições,** selecione **plataformas de dispositivos.**
   1. Definir **Configurar** para **Sim**.
   1. Inclua **Android** e **iOS.**
1. Em **Condições,** selecione **aplicações do Cliente (pré-visualização)** .
   1. Definir **Configurar** para **Sim**.
   1. Selecione **aplicativos Móveis e clientes de desktop** e clientes de **autenticação moderna.**
1. Sob **os controlos de acesso** > **Grant,** selecione as seguintes opções:
   - **Exigir aplicação de cliente aprovada**
   - **Exigir política de proteção de aplicativos (pré-visualização)**
   - **Exigir todos os controlos selecionados**
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Política para clientes Exchange ActiveSync que exigem a utilização de uma aplicação de cliente aprovada.**

1. Navegue no **Diretório Ativo do Azure** > **Segurança** > **Acesso Condicional.**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os utilizadores e **grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. Sob **aplicações ou ações cloud** > **Incluir**, selecione **Office 365 Exchange Online**.
1. Em **condições:**
   1. **Aplicativos para clientes (pré-visualização)** :
      1. Definir **Configurar** para **Sim**.
      1. Selecione **aplicações móveis e clientes de desktop** e clientes Exchange **ActiveSync.**
1. Sob **controlos de acesso** > **Grant**, selecione **Grant access**, **Requerer aplicação de cliente aprovada**, e selecione **Select**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 3: Configure a política de proteção de aplicações Intune para aplicações de iOS e clientes Android.**

Reveja o artigo Como criar e atribuir políticas de [proteção de aplicações,](/intune/apps/app-protection-policies)para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="next-steps"></a>Passos seguintes

[O que é acesso condicional?](overview.md)

[Componentes de acesso condicional](concept-conditional-access-policies.md)

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

