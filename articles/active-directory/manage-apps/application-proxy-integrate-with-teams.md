---
title: Aceda a aplicativo Azure AD app Proxy em equipas | Microsoft Docs
description: Utilize o Azure AD Application Proxy para aceder à sua aplicação no local através das Equipas microsoft.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/05/2017
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c44716f045340022c871501609cf582015ba20f
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256614"
---
# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>Aceda às suas aplicações no local através do Microsoft Teams

O Azure Ative Directory Application Proxy dá-lhe um único sinal de inscrição para aplicações no local, independentemente de onde esteja. As Equipas microsoft dinamizam os seus esforços colaborativos num só local. Integrar os dois juntos significa que os seus utilizadores podem ser produtivos com os seus companheiros de equipa em qualquer situação.

Os seus utilizadores podem adicionar aplicações em nuvem aos seus canais De equipas [usando separadores](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US), mas e os sites SharePoint ou a ferramenta de planeamento que estão hospedados no local? Application Proxy é a solução. Podem adicionar aplicações publicadas através do Application Proxy aos seus canais utilizando os mesmos URLs externos que usam sempre para aceder remotamente às suas apps. E como o Application Proxy autentica através do Azure Ative Directory, os seus utilizadores obtêm uma única experiência de inscrição.

## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>Instale o conector Proxy da Aplicação e publique a sua aplicação

Se ainda não o fez, [configure o Application Proxy para o seu inquilino e instale o conector](application-proxy-add-on-premises-application.md). Em seguida, [publique o seu pedido no local](application-proxy-add-on-premises-application.md) para acesso remoto. Ao publicar a aplicação, tome nota do URL externo porque é usado para adicionar a app às Equipas.

Se já tem as suas aplicações publicadas mas não se lembra dos seus URLs externos, procure-as no [portal Azure.](https://portal.azure.com) Iniciar sação e, em seguida, navegar para aplicações **Azure Ative Directory**  >  **Enterprise**  >  **Todas as aplicações** > selecione a sua aplicação > **Procuração de Aplicação**.

## <a name="add-your-app-to-teams"></a>Adicione o seu aplicativo às Equipas

Assim que publicar a aplicação através do Application Proxy, informe os seus utilizadores de que podem adicioná-la como separador diretamente nos seus canais De Equipas, e depois a aplicação está disponível para todos na equipa utilizarem. Que sigam estes três passos:

1. Navegue para o canal Teams onde pretende adicionar esta aplicação e selecione **+** para adicionar um separador.

   ![Selecione + para adicionar um separador em Equipas](./media/application-proxy-integrate-with-teams/add-tab.png)

1. Selecione **Website** a partir das opções de separador.

   ![Selecione o Site a partir do ecrã de separador Adicionar um separador](./media/application-proxy-integrate-with-teams/website.png)

1. Dê ao separador um nome e desaprote o URL para o URL externo do Application Proxy.

   ![Nomeie o separador e adicione o URL externo](./media/application-proxy-integrate-with-teams/tab-name-url.png)

Uma vez que um membro de uma equipa adiciona a conta, aparece para todos no canal. Todos os utilizadores que tenham acesso à aplicação têm acesso único com as credenciais que utilizam para as Equipas da Microsoft. Qualquer utilizadores que não tenham acesso à app podem ver o separador em Equipas, mas estão bloqueados até que lhes dê permissões para a app no local e para a versão publicada do portal Azure da aplicação.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [publicar no local sites SharePoint](application-proxy-integrate-with-sharepoint-server.md) com Application Proxy.
- Configure as suas aplicações para utilizar [domínios personalizados](application-proxy-configure-custom-domain.md) para o seu URL externo.
