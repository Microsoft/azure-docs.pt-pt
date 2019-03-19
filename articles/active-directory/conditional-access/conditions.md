---
title: Quais são condições no acesso condicional do Azure Active Directory? | Microsoft Docs
description: Saiba como as condições são usadas no acesso condicional do Azure Active Directory para acionar uma política.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0245fa060f22b2f59a037af5323397409a59da69
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58165939"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Quais são condições no acesso condicional do Azure Active Directory? 

Pode controlar a forma como os utilizadores aceder a suas aplicações na cloud utilizando [acesso condicional do Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). Uma política de acesso condicional, vai definir a resposta (", em seguida, faça isso") para o motivo para acionar a política ("quando isso acontece"). 

![Motivo e resposta](./media/conditions/10.png)


No contexto do acesso condicional, **quando isso acontece** denomina-se um **condição**. **Em seguida, fazer isso** denomina-se um **controlo de acesso**. A combinação de suas condições e seus controles de acesso representa uma política de acesso condicional.

![Política de acesso condicional](./media/conditions/61.png)


Condições que ainda não configurado numa política de acesso condicional não são aplicadas. Algumas condições são [obrigatório](best-practices.md) para aplicar uma política de acesso condicional para um ambiente. 

Este artigo é uma visão geral das condições e como são utilizados numa política de acesso condicional. 

## <a name="users-and-groups"></a>Utilizadores e grupos

A condição de utilizadores e grupos é obrigatória de uma política de acesso condicional. Na sua política, pode, selecione **todos os utilizadores** ou selecione os utilizadores e grupos específicos.

![Utilizadores e grupos](./media/conditions/111.png)

Quando seleciona **todos os utilizadores**, a política é aplicada a todos os utilizadores no diretório, incluindo utilizadores convidados.

Quando **selecionar utilizadores e grupos**, pode definir as seguintes opções:

* **Todos os utilizadores convidados** destina-se uma política para utilizadores convidados B2B. Esta condição corresponde a uma conta de utilizador que tenha as **userType** atributo definido como **convidado**. Utilize esta definição quando uma política tem de ser aplicadas assim que a conta é criada num fluxo de convite no Azure AD.

* **Funções de diretório** destina-se uma política com base na atribuição de função de um utilizador. Esta condição dá suporte a funções de diretório, como **Administrador Global** ou **administrador de palavras-passe**.

* **Utilizadores e grupos** conjuntos específicos de utilizadores destina-se. Por exemplo, pode selecionar um grupo que contenha todos os membros do departamento de RH, quando uma aplicação de RH está selecionada como a aplicação na cloud. Um grupo pode ser qualquer tipo de grupo no Azure AD, incluindo os grupos de segurança e a distribuição dinâmicos ou atribuídos.

Também pode excluir utilizadores ou grupos específicos de uma política. Um caso de uso comum é a contas de serviço se a sua política impõe a autenticação multifator (MFA). 

Conjuntos específicos de utilizadores o direcionamento é útil para a implementação de uma nova política. Numa nova política, deve visar apenas um conjunto inicial de utilizadores para validar o comportamento da política. 



## <a name="cloud-apps"></a>Aplicações na cloud 

Uma aplicação na cloud é um Web site ou serviço. Web sites protegidos pelo Proxy de aplicações do Azure AD também são aplicações na cloud. Para obter uma descrição detalhada das aplicações na cloud suportados, consulte [atribuições de aplicações da cloud](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

O **aplicações na cloud** condição é obrigatória numa política de acesso condicional. Na sua política, pode, selecione **todas as aplicações na cloud** ou selecione as aplicações específicas.

![Incluem aplicações na cloud](./media/conditions/03.png)

Selecione:

- **Todas as aplicações na cloud** às políticas de linha de base para se aplicam a toda a organização. Utilize esta selecção para políticas que exigem a autenticação multifator quando é detetado um risco de início de sessão para qualquer aplicação na cloud. Uma política aplicada ao **todas as aplicações na cloud** aplica-se para o acesso a todos os Web sites e serviços. Esta definição não se limita às aplicações de cloud que são apresentadas na **selecionar aplicações** lista. 

- **Selecionar aplicações** para serviços específicos do destino pela sua política. Por exemplo, pode exigir que os utilizadores tenham uma [dispositivo em conformidade](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) para aceder ao SharePoint Online. Esta política também é aplicada a outros serviços, quando acede a conteúdo do SharePoint. Um exemplo é o Microsoft Teams. 

Pode excluir aplicações específicas de uma política. No entanto, estas aplicações estão ainda sujeitos as políticas aplicadas para os serviços que acedem. 



## <a name="sign-in-risk"></a>Risco de início de sessão

Um risco de início de sessão é um indicador da probabilidade (alta, média ou baixa) que um início de sessão não foi efetuado pelo proprietário legítimo de uma conta de utilizador. O Azure AD calcula que o nível de risco de início de sessão durante um usuário estejam início de sessão-no. Pode usar o nível de risco de início de sessão calculados como condição de uma política de acesso condicional.

![Níveis de risco de início de sessão](./media/conditions/22.png)

Para utilizar esta condição, tem de ter [do Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) ativada.
 
Casos de utilização comuns para esta condição são as políticas que tenham as proteções seguintes: 

- Impedir que os utilizadores com um início de sessão de alto risco. Esta proteção impede que potencialmente não legítimos os utilizadores acedem às suas aplicações na cloud. 
- Exigir a autenticação multifator para os utilizadores com um risco de início de sessão médio. Ao impor a autenticação multifator, pode fornecer confiança adicional que o início de sessão é feito pelo proprietário legítimo de uma conta.

Para obter mais informações, consulte [bloquear o acesso quando é detetado um risco de sessão](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Plataformas de dispositivos

A plataforma do dispositivo é caracterizada por sistema operativo que é executado no seu dispositivo. O Azure AD identifica a plataforma utilizando as informações fornecidas pelo dispositivo, por exemplo, o agente de utilizador. Esta informação é não verificada. Recomendamos que todas as plataformas têm uma política aplicada às mesmas. A política deve ou bloquear o acesso, exigir a conformidade com políticas do Microsoft Intune ou exigir que o dispositivo ser associado a um domínio. A predefinição é aplicar uma política a todas as plataformas de dispositivo. 


![Configurar as plataformas de dispositivo](./media/conditions/24.png)

Para obter uma lista de plataformas de dispositivos suportados, consulte [condição de plataforma de dispositivo](technical-reference.md#device-platform-condition).


Caso de utilização de uma comum para esta condição é uma política que restringe o acesso às suas aplicações na cloud para [dispositivos geridos](require-managed-devices.md). Para obter mais cenários, incluindo a condição de plataforma do dispositivo, consulte [acesso condicional com base na aplicação do Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Estado do dispositivo

A condição de estado do dispositivo exclui híbridos que associados ao Azure AD e a dispositivos marcado como conforme a partir de uma política de acesso condicional. 


![Configurar o estado do dispositivo](./media/conditions/112.png)

Esta condição é útil quando uma política deve ser aplicada apenas a um dispositivo não gerido para fornecer segurança de sessão adicionais. Por exemplo, apenas impor o controlo de sessão do Microsoft Cloud App Security quando um dispositivo for não gerido. 

## <a name="locations"></a>Localizações

Ao utilizar localizações, pode definir condições com base no qual foi tentada uma ligação. 

![Configurar localizações](./media/conditions/25.png)

Casos de utilização comuns para esta condição são as políticas com as proteções seguintes:

- Exigir a autenticação multifator para os utilizadores que acedem um serviço, quando estiverem desconectados da rede corporativa.  

- Bloquear o acesso para os utilizadores que acedem um serviço de países ou regiões específicos. 

Para obter mais informações, consulte [o que é a condição de localização no acesso condicional do Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Aplicações do cliente

Por predefinição, uma política de acesso condicional aplica-se para as seguintes aplicações:

- **[Aplicações de browser](technical-reference.md#supported-browsers)**  -aplicações de Browser incluem Web sites através de SAML, WS-Federation e OpenID Connect protocolos SSO de web. Isto também se aplica a qualquer serviço web ou sites que foi registado como um cliente confidencial do OAuth. Por exemplo, o site do SharePoint do Office 365. 

- **[Móveis e aplicações de ambiente de trabalho que utilizam autenticação moderna](technical-reference.md#supported-mobile-applications-and-desktop-clients)**  -estas aplicações incluem as aplicações de ambiente de trabalho do Office e aplicações de telemóvel. 


Além disso, pode direcionar uma política para aplicações de cliente específico que não estiver a utilizar autenticação moderna, por exemplo:

- **[Clientes do Exchange ActiveSync](conditions.md#exchange-activesync-clients)**  - quando um blocos de política com o Exchange ActiveSync, os utilizadores afetados recebem um e-mail de quarentena único com informações sobre por que estão bloqueados. Se necessário, o e-mail inclui instruções para inscrever o respetivo dispositivo com o Intune.

- **[Outros clientes](block-legacy-authentication.md)**  -estas aplicações incluem os clientes que utilizam autenticação básica com protocolos de email, como o IMAP, MAPI, POP, SMTP e aplicações do Office mais antigas que não utilizam autenticação moderna. Para obter mais informações, consulte [como a autenticação moderna funciona para aplicações de cliente do Office 2013 e Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Aplicações do cliente](./media/conditions/41.png)

Casos de utilização comuns para esta condição são as políticas com os seguintes requisitos:

- **[Exigir um dispositivo gerido](require-managed-devices.md)**  para aplicativos móveis e de ambiente de trabalho que transfere dados para um dispositivo. Ao mesmo tempo, permita o acesso ao browser a partir de qualquer dispositivo. Este cenário impede que o salvamento e sincronização de documentos para um dispositivo não gerido. Com esse método, pode reduzir a probabilidade de perda de dados se o dispositivo é perdido ou roubado.

- **[Exigir um dispositivo gerido](require-managed-devices.md)**  para aplicações que utilizam o ActiveSync para aceder ao Exchange Online.

- **[Autenticação de legado do bloco](block-legacy-authentication.md)**  para o Azure AD (outros clientes)

- Bloquear o acesso de aplicações web, mas permitir o acesso das aplicações móveis e de Desktops.



### <a name="exchange-activesync-clients"></a>Clientes do Exchange ActiveSync

Só pode selecionar **clientes do Exchange ActiveSync** se:


- Microsoft Office 365 Exchange Online é a única aplicação na cloud que selecionou.

    ![Aplicações na cloud](./media/conditions/32.png)

- Não tem outras condições configuradas numa política. No entanto, pode restringir o âmbito desta condição se aplique apenas aos [plataformas suportadas](technical-reference.md#device-platform-condition).
 
    ![Aplicar política apenas a plataformas suportadas](./media/conditions/33.png)


Quando o acesso é bloqueado porque um [dispositivo gerido](require-managed-devices.md) é necessário, os utilizadores afetados obterem um único e-mail-os para utilizar o Intune. 

Se uma aplicação aprovada for necessária, os utilizadores afetados obtém diretrizes para instalar e utilizar o cliente móvel do Outlook.

Em outros casos, por exemplo, se a MFA é necessária, os utilizadores afetados são bloqueados, porque os clientes que utilizam a autenticação básica não suportam a MFA.

Só pode filtrar esta definição para utilizadores e grupos. Ele não dá suporte convidados ou funções. Se uma condição de convidado ou a função estiver configurada, todos os utilizadores estão bloqueados porque o acesso condicional não é possível determinar se deve aplicar a política para o usuário, ou não.


 Para obter mais informações, consulte:

- [Configurar o SharePoint Online e Exchange Online para o acesso condicional do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [O Azure Active Directory com base na aplicação acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>Passos Seguintes

- Para saber como configurar uma política de acesso condicional, consulte [início rápido: Exigir a MFA para aplicações específicas com acesso condicional do Azure Active Directory](app-based-mfa.md).

- Para configurar políticas de acesso condicional para o seu ambiente, consulte a [melhores práticas para acesso condicional no Azure Active Directory](best-practices.md). 

