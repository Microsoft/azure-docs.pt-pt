---
title: Aplicativos de cliente aprovados com Acesso Condicional - Diretório Ativo Azure
description: Saiba como exigir aplicações de clientes aprovadas para acesso a aplicações na nuvem com acesso condicional no Diretório Ativo Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c9cd790edcb186ed2f80d467076512cd558ca40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253397"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Como: Exigir aplicativos de cliente aprovados para acesso a aplicativos na nuvem com Acesso Condicional

As pessoas usam regularmente os seus dispositivos móveis para tarefas pessoais e de trabalho. Ao mesmo tempo que asseguram que o pessoal pode ser produtivo, as organizações também querem evitar a perda de dados de aplicações potencialmente inseguras. Com Acesso Condicional, as organizações podem restringir o acesso a aplicações de clientes aprovadas (capazes de autenticação moderna).

Este artigo apresenta dois cenários para configurar políticas de acesso condicional para recursos como Office 365, Exchange Online e SharePoint Online.

- [Cenário 1: As aplicações do Office 365 exigem uma aplicação de cliente aprovada](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Cenário 2: Exchange Online e SharePoint Online exigem uma aplicação de cliente aprovada](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

No Acesso Condicional, esta funcionalidade é conhecida como exigindo uma aplicação de cliente aprovada. Para obter uma lista de aplicações de clientes aprovadas, consulte [o requisito da aplicação do cliente aprovado.](concept-conditional-access-grant.md#require-approved-client-app)

> [!NOTE]
> Para exigir aplicações de clientes aprovadas para dispositivos iOS e Android, estes dispositivos devem primeiro registar-se em AZure AD.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Cenário 1: As aplicações do Office 365 exigem uma aplicação de cliente aprovada

Neste cenário, a Contoso decidiu que os utilizadores que usam dispositivos móveis podem aceder a todos os serviços do Office 365 desde que utilizem aplicações de clientes aprovadas, como o Outlook mobile, OneDrive e Microsoft Teams. Todos os seus utilizadores já fazem sessão com credenciais AD AZure e têm licenças que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar os três passos seguintes para exigir a utilização de uma aplicação de cliente aprovada em dispositivos móveis.

**Passo 1: Política para clientes modernos de autenticação moderna baseados em Android e iOS que exigem a utilização de uma aplicação de cliente aprovada ao aceder ao Exchange Online.**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações cloud**  >  **Inclua**, selecione **Office 365 (pré-visualização)**.
1. Em **Condições**, selecione **plataformas do Dispositivo**.
   1. **Desconfigure** para **Sim**.
   1. Incluir **Android** e **iOS.**
1. Em **Condições**, selecione **aplicações do Cliente (pré-visualização)**.
   1. **Desconfigure** para **Sim**.
   1. Selecione **aplicativos móveis e clientes de desktop** e **clientes de autenticação moderna.**
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Exija **uma aplicação de cliente aprovada**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Configurar uma política de acesso condicional Azure AD para troca online com ActiveSync (EAS)**

1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365 Exchange Online**.
1. Em **Condições:**
   1. **Aplicativos de cliente (pré-visualização)**:
      1. **Desconfigure** para **Sim**.
      1. Selecione **aplicativos móveis e clientes de desktop** e **clientes Exchange ActiveSync**.
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Exija **uma aplicação de cliente aprovada**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 3: Configurar a política de proteção de aplicações intune para aplicações de clientes iOS e Android.**

Reveja o artigo [Como criar e atribuir políticas de proteção de aplicações](/intune/apps/app-protection-policies), para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Cenário 2: Exchange Online e SharePoint Online exigem uma aplicação de cliente aprovada

Neste cenário, a Contoso decidiu que os utilizadores só podem aceder a emails e dados do SharePoint em dispositivos móveis, desde que utilizem uma aplicação de clientes aprovada como o Outlook mobile. Todos os seus utilizadores já fazem sessão com credenciais AD AZure e têm licenças que incluem Azure AD Premium P1 ou P2 e Microsoft Intune.

As organizações devem completar os três passos seguintes para exigir a utilização de uma aplicação de clientes aprovada em dispositivos móveis e clientes Exchange ActiveSync.

**Passo 1: Política para clientes modernos de autenticação moderna baseados em Android e iOS que exigem a utilização de uma aplicação de cliente aprovada ao aceder ao Exchange Online e SharePoint Online.**

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365 Exchange Online** e Office **365 SharePoint Online**.
1. Em **Condições**, selecione **plataformas do Dispositivo**.
   1. **Desconfigure** para **Sim**.
   1. Incluir **Android** e **iOS.**
1. Em **Condições**, selecione **aplicações do Cliente (pré-visualização)**.
   1. **Desconfigure** para **Sim**.
   1. Selecione **aplicativos móveis e clientes de desktop** e **clientes de autenticação moderna.**
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Exija **uma aplicação de cliente aprovada**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 2: Política de clientes Dom ActiveSync que exigem a utilização de uma aplicação de cliente aprovada.**

1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores** ou os **Utilizadores e grupos específicos** a que pretende aplicar esta política. 
   1. Selecione **Done** (Concluído).
1. No **âmbito de aplicações ou ações cloud**  >  **Inclua**, selecione Office **365 Exchange Online**.
1. Em **Condições:**
   1. **Aplicativos de cliente (pré-visualização)**:
      1. **Desconfigure** para **Sim**.
      1. Selecione **aplicativos móveis e clientes de desktop** e **clientes Exchange ActiveSync**.
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**, Exija **uma aplicação de cliente aprovada**, e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar e ativar a sua política.

**Passo 3: Configurar a política de proteção de aplicações intune para aplicações de clientes iOS e Android.**

Reveja o artigo [Como criar e atribuir políticas de proteção de aplicações](/intune/apps/app-protection-policies), para medidas para criar políticas de proteção de aplicações para Android e iOS. 

## <a name="next-steps"></a>Próximos passos

[O que é o Acesso Condicional?](overview.md)

[Componentes de acesso condicional](concept-conditional-access-policies.md)

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)
