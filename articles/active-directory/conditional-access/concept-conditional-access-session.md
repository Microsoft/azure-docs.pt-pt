---
title: Controlos de sessão na política de acesso condicional - Diretório Ativo Azure
description: O que são controlos de sessão numa política de acesso condicional azure AD
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
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671892"
---
# <a name="conditional-access-session"></a>Acesso Condicional: Sessão

Dentro de uma política de Acesso Condicional, um administrador pode utilizar os controlos de sessão para permitir experiências limitadas dentro de aplicações específicas na nuvem.

![Política de Acesso Condicional com controlo de subvenções que requer a autenticação de vários fatores](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrições impostas à aplicação

As organizações podem usar este controlo para exigir que o Azure AD passe informações do dispositivo para as aplicações em nuvem selecionadas. A informação do dispositivo permite que as aplicações em nuvem saibam se uma ligação é iniciada a partir de um dispositivo compatível ou unido. Este controlo apenas suporta o SharePoint Online e o Exchange Online como aplicações em nuvem selecionadas. Quando selecionada, a aplicação cloud utiliza as informações do dispositivo para fornecer aos utilizadores, dependendo do estado do dispositivo, com uma experiência limitada ou completa.

Para obter mais informações sobre a utilização e configuração de restrições impostas pela aplicação, consulte os seguintes artigos:

- [Permitindo acesso limitado com O SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Permitir o acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Controlo de aplicações de acesso condicional

O Controlo de Aplicações de Acesso Condicional utiliza uma arquitetura de procuração inversa e está exclusivamente integrado com o Azure AD Conditional Access. O Azure AD Conditional Access permite-lhe impor controlos de acesso nas aplicações da sua organização com base em determinadas condições. As condições definem quem (utilizador ou grupo de utilizadores) e o que (quais aplicações na nuvem) e onde (quais localizações e redes) é aplicada uma política de Acesso Condicional. Depois de ter determinado as condições, pode direcionar os utilizadores para o [Microsoft Cloud App Security,](/cloud-app-security/what-is-cloud-app-security) onde pode proteger dados com controlo de aplicações de acesso condicional aplicando controlos de acesso e sessão.

O Controlo de Aplicações de Acesso Condicional permite que o acesso e as sessões de aplicações ao utilizador sejam monitorizados e controlados em tempo real com base nas políticas de acesso e sessão. As políticas de acesso e sessão são utilizadas dentro do portal Cloud App Security para aperfeiçoar filtros e definir ações a tomar sobre um utilizador. Com as políticas de acesso e sessão, pode:

- Evitar a exfiltração de dados: Pode bloquear o download, corte, cópia e impressão de documentos sensíveis em, por exemplo, dispositivos não geridos.
- Proteja no download: Em vez de bloquear o download de documentos sensíveis, pode exigir que os documentos sejam rotulados e protegidos com proteção de informação Azure. Esta ação garante que o documento está protegido e o acesso ao utilizador é restrito numa sessão potencialmente arriscada.
- Evite o upload de ficheiros não rotulados: Antes de um ficheiro sensível ser carregado, distribuído e utilizado por outros, é importante certificar-se de que o ficheiro tem a etiqueta e a proteção adequadas. Pode garantir que os ficheiros não rotulados com conteúdo sensível estão bloqueados de serem carregados até que o utilizador classifique o conteúdo.
- Monitorize as sessões de utilizador para o cumprimento: Os utilizadores de risco são monitorizados quando iniciam o seu login nas aplicações e as suas ações são registadas a partir de dentro da sessão. Pode investigar e analisar o comportamento do utilizador para perceber onde, e em que condições, as políticas de sessão devem ser aplicadas no futuro.
- Acesso ao bloco: Pode bloquear granularmente o acesso a aplicações específicas e utilizadores dependendo de vários fatores de risco. Por exemplo, pode bloqueá-los se estiverem a usar certificados de cliente como forma de gestão do dispositivo.
- Atividades personalizadas de bloco: Algumas aplicações têm cenários únicos que carregam risco, por exemplo, enviando mensagens com conteúdo sensível em apps como Microsoft Teams ou Slack. Neste tipo de cenários, pode digitalizar mensagens para conteúdos sensíveis e bloqueá-las em tempo real.

Para mais informações, consulte o artigo Implementar controlo de aplicações de [acesso condicional para aplicações em destaque.](/cloud-app-security/proxy-deployment-aad)

## <a name="sign-in-frequency-preview"></a>Frequência de inversição (Pré-visualização)

A frequência de inatividade define o período de tempo antes de um utilizador ser convidado a iniciar o seu sessão quando tentar aceder a um recurso.

A definição de frequência sessão funciona com aplicações que implementaram protocolos OAUTH2 ou OIDC de acordo com as normas. A maioria das aplicações nativas da Microsoft para Windows, Mac e Mobile, incluindo as seguintes aplicações web, cumprem a definição.

- Palavra, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal de administração O365
- Exchange Online
- SharePoint e OneDrive
- Clientes web de equipas
- Dynamics CRM Online
- Portal do Azure

Para mais informações, consulte o artigo [Configure gestão](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)da sessão de autenticação com Acesso Condicional .

## <a name="persistent-browser-session-preview"></a>Sessão persistente do navegador (Pré-visualização)

Uma sessão de navegador persistente permite que os utilizadores permaneçam inscritos após o fecho e reabertura da janela do navegador.

Para mais informações, consulte o artigo [Configure gestão](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)da sessão de autenticação com Acesso Condicional .

## <a name="next-steps"></a>Passos seguintes

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo só de relatório](concept-conditional-access-report-only.md)
