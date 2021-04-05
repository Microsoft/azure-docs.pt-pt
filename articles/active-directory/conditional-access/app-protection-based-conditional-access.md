---
title: Políticas de proteção de aplicativos com acesso condicional - Diretório Ativo Azure
description: Saiba como exigir a política de proteção de aplicações para acesso a aplicações na nuvem com acesso condicional no Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720cd46ac37a82f56aa37c0041ca8d92db177071
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575776"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>Como: Requer a política de proteção de aplicações e uma aplicação de cliente aprovada para acesso a aplicações na nuvem com Acesso Condicional

As pessoas usam regularmente os seus dispositivos móveis para tarefas pessoais e de trabalho. Ao mesmo tempo que asseguram que o pessoal pode ser produtivo, as organizações também querem evitar a perda de dados de aplicações potencialmente inseguras. Com o Acesso Condicional, as organizações podem restringir o acesso a aplicações de clientes aprovadas (modernas e capazes de autenticação) com políticas de proteção de aplicações Intune aplicadas a elas.

Este artigo apresenta três cenários para configurar políticas de acesso condicional para recursos como Microsoft 365, Exchange Online e SharePoint.

- [Cenário 1: Aplicações microsoft 365 exigem aplicações aprovadas com políticas de proteção de apps](#scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 2: Aplicações de navegador exigem aplicações aprovadas com políticas de proteção de apps](#scenario-2-browser-apps-require-approved-apps-with-app-protection-policies)
- [Cenário 3: Exchange Online e SharePoint exigem uma aplicação de cliente aprovada e política de proteção de aplicações](#scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy)

No Acesso Condicional, estas aplicações de clientes são conhecidas por estarem protegidas com uma política de proteção de aplicações. Mais informações sobre políticas de proteção de aplicações podem ser encontradas no artigo, [Visão geral das políticas de proteção de aplicações](/intune/apps/app-protection-policy)

> [!WARNING]
> Nem todas as aplicações são apoiadas como aplicações aprovadas ou políticas de proteção de aplicações de apoio. Para obter uma lista de aplicações elegíveis para clientes, consulte [o requisito da política de proteção da Aplicação.](concept-conditional-access-grant.md#require-app-protection-policy)

> [!NOTE]
> "Exigir um dos controlos selecionados" ao abrigo dos controlos de subvenção é como uma cláusula de OR. Isto é utilizado dentro da política para permitir que os utilizadores utilizem apps que suportem a **política de proteção** de aplicações Require ou exijam controlos aprovados de **concessão de aplicações** ao cliente. **Exigir que a política de proteção de aplicações** seja aplicada quando a aplicação suporta esse controlo. Para obter mais informações sobre quais as aplicações que suportam o controlo **da política de proteção de aplicações Require,** consulte [o requisito da política de proteção da aplicação](concept-conditional-access-grant.md#require-app-protection-policy).

## <a name="scenario-1-microsoft-365-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 1: Aplicações microsoft 365 exigem aplicações aprovadas com políticas de proteção de apps

Neste cenário, a Contoso decidiu que todo o acesso móvel aos recursos da Microsoft 365 deve utilizar aplicações de clientes aprovadas, como o Outlook mobile e o OneDrive, protegidos por uma política de proteção de apps antes de receber acesso. Todos os seus utilizadores já fazem sessão com credenciais AD AZure e têm licenças que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar os seguintes passos para exigir a utilização de uma aplicação de cliente aprovada em dispositivos móveis.

**Passo 1: Configurar uma política de acesso condicional Azure AD para a Microsoft 365**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365**.
1. Em **Condições**, selecione **plataformas do Dispositivo**.
   1. **Desconfigure** para **Sim**.
   1. Incluir **Android** e **iOS.**
1. Em **Condições**, selecione **aplicativos clientes**.
   1. **Desconfigure** para **Sim**.
   1. Selecione **aplicativos móveis e clientes de desktop** e desmarcar tudo o resto.
1. Sob **controlos de acesso**  >  **Grant**, selecione as seguintes opções:
   - **Requera uma aplicação de cliente aprovada**
   - **Pedir uma política de proteção de aplicações**
   - **Exigir um dos controlos selecionados**
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Configurar uma política de acesso condicional Azure AD para troca online com ActiveSync (EAS)**

Para a política de acesso condicional neste passo, configuure os seguintes componentes:

1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365 Exchange Online**.
1. Em **Condições,** selecione **aplicativos clientes:**
   1. **Desconfigure** para **Sim**.
   1. Selecione **clientes Exchange ActiveSync** e desmarcar tudo o resto.
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Require app protection **policy**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 3: Configurar a política de proteção de aplicações intune para aplicações de clientes iOS e Android**

Reveja o artigo [Como criar e atribuir políticas de proteção de aplicações](/intune/apps/app-protection-policies), para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="scenario-2-browser-apps-require-approved-apps-with-app-protection-policies"></a>Cenário 2: Aplicações de navegador exigem aplicações aprovadas com políticas de proteção de apps

Neste cenário, a Contoso decidiu que todos os acessos de navegação na Web a recursos da Microsoft 365 devem utilizar uma aplicação de clientes aprovada, como o Edge para iOS e Android, protegida por uma política de proteção de apps antes de receber acesso. Todos os seus utilizadores já fazem sessão com credenciais AD AZure e têm licenças que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar os seguintes passos para exigir a utilização de uma aplicação de cliente aprovada em dispositivos móveis.

**Passo 1: Configurar uma política de acesso condicional Azure AD para a Microsoft 365**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365**.
1. Em **Condições**, selecione **plataformas do Dispositivo**.
   1. **Desconfigure** para **Sim**.
   1. Incluir **Android** e **iOS.**
1. Em **Condições**, selecione **aplicativos clientes**.
   1. **Desconfigure** para **Sim**.
   1. Selecione **Browser** e desmarcar todo o resto.
1. Sob **controlos de acesso**  >  **Grant**, selecione as seguintes opções:
   - **Requera uma aplicação de cliente aprovada**
   - **Pedir uma política de proteção de aplicações**
   - **Exigir um dos controlos selecionados**
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Configurar a política de proteção de aplicações intune para aplicações de clientes iOS e Android**

Reveja o artigo [Como criar e atribuir políticas de proteção de aplicações](/intune/apps/app-protection-policies), para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="scenario-3-exchange-online-and-sharepoint-require-an-approved-client-app-and-app-protection-policy"></a>Cenário 3: Exchange Online e SharePoint exigem uma aplicação de cliente aprovada e política de proteção de aplicações

Neste cenário, a Contoso decidiu que os utilizadores só podem aceder a emails e dados do SharePoint em dispositivos móveis, desde que utilizem uma aplicação de clientes aprovada como o Outlook mobile protegida por uma política de proteção de aplicações antes de receberem acesso. Todos os seus utilizadores já fazem sessão com credenciais AD AZure e têm licenças que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar os três passos seguintes para exigir a utilização de uma aplicação de clientes aprovada em dispositivos móveis e clientes Exchange ActiveSync.

**Passo 1: Política para clientes modernos de autenticação moderna baseados em Android e iOS que exigem a utilização de uma aplicação de clientes aprovada e política de proteção de aplicações ao aceder ao Exchange Online e sharePoint.**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365 Exchange Online** e Office **365 SharePoint Online**.
1. Em **Condições**, selecione **plataformas do Dispositivo**.
   1. **Desconfigure** para **Sim**.
   1. Incluir **Android** e **iOS.**
1. Em **Condições**, selecione **aplicativos clientes**.
   1. **Desconfigure** para **Sim**.
   1. Selecione **aplicativos móveis e clientes de desktop** e desmarcar tudo o resto.
1. Sob **controlos de acesso**  >  **Grant**, selecione as seguintes opções:
   - **Requera uma aplicação de cliente aprovada**
   - **Pedir uma política de proteção de aplicações**
   - **Exigir um dos controlos selecionados**
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Política de clientes Dom ActiveSync que exigem a utilização de uma aplicação de cliente aprovada.**

1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Concluído**.
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365 Exchange Online**.
1. Em **Condições,** selecione **aplicativos clientes:**
   1. **Desconfigure** para **Sim**.
   1. Selecione **clientes Exchange ActiveSync** e desmarcar tudo o resto.
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Require app protection **policy**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 3: Configurar a política de proteção de aplicações intune para aplicações de clientes iOS e Android.**

Reveja o artigo [Como criar e atribuir políticas de proteção de aplicações](/intune/apps/app-protection-policies), para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="next-steps"></a>Passos seguintes

[O que é o Acesso Condicional?](overview.md)

[Componentes de acesso condicional](concept-conditional-access-policies.md)

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

