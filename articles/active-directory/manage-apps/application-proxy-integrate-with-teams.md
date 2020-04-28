---
title: Aceder às aplicações de proxy da App Azure AD em Equipas [ Microsoft Docs
description: Utilize o Proxy de Aplicação AD Azure para aceder à sua aplicação no local através das Equipas microsoft.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67807762"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Aceda às suas aplicações no local através das Equipas microsoft

A Procuração de Aplicação de Diretório Ativo Azure dá-lhe um único sinal de inscrição para aplicações no local, independentemente de onde esteja. As Equipas Microsoft dinamizam os seus esforços colaborativos num só local. Integrar os dois em conjunto significa que os seus utilizadores podem ser produtivos com os seus companheiros de equipa em qualquer situação.

Os seus utilizadores podem adicionar aplicações em nuvem aos seus canais De Equipas [utilizando separadores,](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)mas e os sites do SharePoint ou a ferramenta de planeamento que estão hospedados no local? Procuração de aplicação é a solução. Podem adicionar aplicações publicadas através do Application Proxy aos seus canais utilizando os mesmos URLs externos que usam sempre para aceder remotamente às suas aplicações. E como a Application Proxy autentica através do Diretório Ativo Azure, os seus utilizadores obtêm uma única experiência de inscrição.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instale o conector Proxy de Aplicação e publique a sua aplicação

Se ainda não o fez, configure o [Proxy de Aplicação para o seu inquilino e instale o conector](application-proxy-add-on-premises-application.md). Em seguida, [publique o seu pedido no local](application-proxy-add-on-premises-application.md) para acesso remoto. Ao publicar a aplicação, tome nota do URL externo porque é usado para adicionar a app às Equipas.

Se já tem as suas aplicações publicadas mas não se lembra dos seus URLs externos, procure-as no [portal Azure](https://portal.azure.com). Iniciar sessão e depois navegar para**aplicações** >  **azure Ative Directory** > Enterprise**Todas as aplicações** > selecionar a sua aplicação > procuração de **aplicações**.

## <a name="add-your-app-to-teams"></a>Adicione a sua aplicação às Equipas

Assim que publicar a aplicação através do Application Proxy, informe os seus utilizadores de que podem adicioná-la como um separador diretamente nos seus canais de Equipas, e depois a aplicação está disponível para todos na equipa usarem. Mande-os seguir estes três passos:

1. Navegue para o canal Das Equipas onde **+** pretende adicionar esta aplicação e selecione para adicionar um separador.

   ![Selecione + adicionar um separador em Equipas](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selecione **website** a partir das opções do separador.

   ![Selecione website a partir do ecrã de separador Adicionar](./media/application-proxy-integrate-with-teams/website.png)

1. Dê um nome ao separador e detete o URL para o URL externo proxy de aplicação.

   ![Nomeie o separador e adicione o URL externo](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Uma vez que um membro de uma equipa adiciona a conta, aparece para todos no canal. Todos os utilizadores que tenham acesso à aplicação têm acesso único com as credenciais que utilizam para as Equipas microsoft. Qualquer utilizador que não tenha acesso à aplicação pode ver o separador em Equipas, mas está bloqueado até lhes dar permissões à aplicação no local e ao portal Azure publicado versão da app.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [publicar sites sharePoint no local](application-proxy-integrate-with-sharepoint-server.md) com procuração de aplicação.
- Configure as suas aplicações para utilizar [domínios personalizados](application-proxy-configure-custom-domain.md) para o seu URL externo.
