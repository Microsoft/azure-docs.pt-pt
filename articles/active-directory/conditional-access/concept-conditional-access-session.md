---
title: Controlos de sessão na política de acesso condicional - Azure Ative Directory
description: O que são controlos de sessão numa política de acesso condicional AD Azure
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
ms.openlocfilehash: 2a0089d246169ad4215075662500794e7143940e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601814"
---
# <a name="conditional-access-session"></a>Acesso Condicional: Sessão

Dentro de uma política de Acesso Condicional, um administrador pode recorrer a controlos de sessão para permitir experiências limitadas dentro de aplicações de nuvem específicas.

![Política de acesso condicional com controlo de subvenções que requer autenticação de vários fatores](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrições impostas à aplicação

As organizações podem usar este controlo para exigir que o Azure AD passe informações do dispositivo para as aplicações de nuvem selecionadas. As informações do dispositivo permitem que as aplicações na nuvem saibam se uma ligação é iniciada a partir de um dispositivo em conformidade ou ligado ao domínio. Este controlo suporta apenas o SharePoint Online e o Exchange Online como aplicações de nuvem selecionadas. Quando selecionada, a aplicação cloud utiliza as informações do dispositivo para fornecer aos utilizadores, dependendo do estado do dispositivo, uma experiência limitada ou completa.

Para obter mais informações sobre a utilização e configuração das restrições impostas pela aplicação, consulte os seguintes artigos:

- [Permitir acesso limitado com o SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Permitir acesso limitado com o Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Controlo de aplicações de acesso condicional

O Controlo de Aplicações de Acesso Condicional utiliza uma arquitetura de procuração inversa e está exclusivamente integrado com acesso condicional Azure AD. O Azure AD Conditional Access permite-lhe impor controlos de acesso nas aplicações da sua organização com base em determinadas condições. As condições definem quem (utilizador ou grupo de utilizadores) e o que (que aplicações na nuvem) e onde (a que localizações e redes) é aplicada uma política de Acesso Condicional. Depois de ter determinado as condições, pode encaminhar os utilizadores para a [Microsoft Cloud App Security,](/cloud-app-security/what-is-cloud-app-security) onde pode proteger dados com o Controlo de Aplicações de Acesso Condicional, aplicando controlos de acesso e sessão.

O Controlo de Aplicações de Acesso Condicional permite que o acesso e sessões da aplicação do utilizador sejam monitorizados e controlados em tempo real com base nas políticas de acesso e sessão. As políticas de acesso e sessão são utilizadas dentro do portal Cloud App Security para aperfeiçoar ainda mais os filtros e definir ações a serem tomadas num utilizador. Com as políticas de acesso e sessão, pode:

- Evitar a exfiltração de dados: Pode bloquear o descarregamento, corte, cópia e impressão de documentos sensíveis em, por exemplo, dispositivos não geridos.
- Proteja no download: Em vez de bloquear o descarregamento de documentos sensíveis, pode exigir que os documentos sejam rotulados e protegidos com a Azure Information Protection. Esta ação garante que o documento está protegido e o acesso ao utilizador é restringido numa sessão potencialmente arriscada.
- Evitar o upload de ficheiros não rotulados: Antes de um ficheiro sensível ser carregado, distribuído e utilizado por outros, é importante certificar-se de que o ficheiro tem a etiqueta e proteção adequadas. Pode certificar-se de que os ficheiros não rotulados com conteúdo sensível estão bloqueados de serem carregados até que o utilizador classifique o conteúdo.
- Monitorize as sessões de utilizador para o cumprimento: Os utilizadores arriscados são monitorizados quando assinam em apps e as suas ações são registadas dentro da sessão. Pode investigar e analisar o comportamento do utilizador para entender onde e em que condições, as políticas de sessão devem ser aplicadas no futuro.
- Acesso ao bloco: Pode bloquear granularmente o acesso a aplicações e utilizadores específicos, dependendo de vários fatores de risco. Por exemplo, pode bloqueá-los se estiverem a usar certificados de cliente como forma de gestão de dispositivos.
- Bloqueie atividades personalizadas: Algumas aplicações têm cenários únicos que carregam riscos, por exemplo, o envio de mensagens com conteúdo sensível em apps como microsoft teams ou Slack. Neste tipo de cenários, pode digitalizar mensagens para conteúdo sensível e bloqueá-las em tempo real.

Para obter mais informações, consulte o artigo [Implementar Controlo de Aplicações de Acesso Condicional para aplicações em destaque.](/cloud-app-security/proxy-deployment-aad)

## <a name="sign-in-frequency"></a>Frequência de inscrição

A frequência de inscrição define o período de tempo antes de um utilizador ser convidado a iniciar novamente o seu sing quando tenta aceder a um recurso.

A definição de frequência de início funciona com aplicações que implementaram protocolos OAUTH2 ou OIDC de acordo com as normas. A maioria das aplicações nativas da Microsoft para Windows, Mac e Mobile, incluindo as seguintes aplicações web, cumprem a definição.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portal microsoft 365 Admin
- Exchange Online
- SharePoint e OneDrive
- Teams cliente web
- Dynamics CRM Online
- Portal do Azure

Para mais informações, consulte o artigo [Configurar gestão da sessão de autenticação com Acesso Condicional.](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

## <a name="persistent-browser-session"></a>Sessão de navegador persistente

Uma sessão de navegador persistente permite que os utilizadores permaneçam assinados após fechar e reabrir a janela do navegador.

Para mais informações, consulte o artigo [Configurar gestão da sessão de autenticação com Acesso Condicional.](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)

## <a name="next-steps"></a>Passos seguintes

- [Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

- [Modo só de relatório](concept-conditional-access-report-only.md)
