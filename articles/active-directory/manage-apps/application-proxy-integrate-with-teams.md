---
title: Aceder a aplicações de Proxy de aplicações do Azure AD no Teams | Documentos da Microsoft
description: Utilize o Proxy de aplicações do Azure AD para aceder à aplicação no local através do Microsoft Teams.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037e005993a54e525560571a6d893197af99b6a0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807762"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Aceder às suas aplicações no local através do Microsoft Teams

Proxy de aplicação do Active Directory do Azure oferece o início de sessão único a aplicações no local, independentemente de onde estiver. Microsoft Teams simplifica a seus esforços de colaboração num único lugar. Integrar os dois em conjunto, significa que os usuários podem ser produtivos com seus colegas de equipe em qualquer situação.

Os utilizadores podem adicionar aplicações na cloud aos seus canais de equipas [utilizando separadores](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), mas e quanto a sites do SharePoint ou a ferramenta de planejamento que estão alojadas no local? Proxy de aplicações é a solução. Eles podem adicionar aplicações publicadas através do Proxy de aplicações aos seus canais com os mesmos URLs externos utilizam sempre para acessar seus aplicativos remotamente. E porque o Proxy de aplicações efetua a autenticação no Azure Active Directory, os utilizadores obtêm uma experiência de início de sessão único.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instalar o conector do Proxy de aplicações e publicar a sua aplicação

Se ainda não o fez, [configurar o Proxy de aplicações para o seu inquilino e instalar o conector](application-proxy-add-on-premises-application.md). Em seguida, [publicar a sua aplicação no local](application-proxy-add-on-premises-application.md) para acesso remoto. Quando estiver a publicar a aplicação, anote o URL externo porque é utilizada para adicionar a aplicação para as Equipes.

Se já tiver as suas aplicações publicadas, mas não se lembrar de suas URLs externos, examiná-los [portal do Azure](https://portal.azure.com). Iniciar sessão, em seguida, navegue até **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** > selecione a sua aplicação >  **Proxy de aplicações**.

## <a name="add-your-app-to-teams"></a>Adicionar a sua aplicação para as Equipes

Depois de publicar a aplicação através do Proxy de aplicações, informar os utilizadores que pode adicioná-lo como um separador diretamente em seus canais de equipas e, em seguida, a aplicação está disponível para todas as pessoas da Equipe a utilizar. Tê-los a seguir estes três passos:

1. Navegue para o canal do Teams para adicionar esta aplicação e selecione onde pretende **+** para adicionar um separador.

   ![Selecione + para adicionar um separador no Teams](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selecione **Web site** entre as opções de separador.

   ![Selecione o Web site a partir de adicionar um ecrã do separador](./media/application-proxy-integrate-with-teams/website.png)

1. Dê um nome de separador e definir o URL para o URL externo de Proxy de aplicações.

   ![O separador nome e adicione o URL externo](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Assim que um membro de uma equipe adiciona o separador, esta é apresentada para todos os utilizadores no canal. Quaisquer utilizadores que têm acesso à aplicação obtém acesso de início de sessão único com as credenciais que utilizam para o Microsoft Teams. Qualquer usuário que não tem acesso à aplicação pode ver o separador no Teams, mas está bloqueado até que lhes fornece permissões à aplicação no local e a versão publicada da aplicação do portal do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [publicar sites do SharePoint no local](application-proxy-integrate-with-sharepoint-server.md) proxy de aplicações.
- Configurar as suas aplicações utilizem [domínios personalizados](application-proxy-configure-custom-domain.md) para o URL externo.
