---
title: Introdução à API de relatórios do Azure AD | Documentos da Microsoft
description: Como começar com o Azure Active Directory reporting API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ff3e530dae3a6db4b7c84292a25e83c11000baf
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437769"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introdução ao Azure Active Directory reporting API

O Azure Active Directory fornece-lhe diversas [relatórios](overview-reports.md), que contém informações úteis para aplicativos como sistemas SIEM, auditoria e ferramentas de business intelligence. 

Ao utilizar o Microsoft Graph API para relatórios do Azure AD, pode obter acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação.

Este artigo fornece uma descrição geral da API de geração de relatórios, incluindo formas de aceder ao mesmo.

Caso se depare com problemas, consulte [como obter suporte do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Pré-requisitos

Para aceder à API de geração de relatórios, com ou sem a intervenção do utilizador, terá de:

1. Atribuir funções (Administrador Global do leitor de segurança, administrador de segurança)
2. Registar uma aplicação
3. Conceder permissões
4. Recolher as definições de configuração

Para obter instruções detalhadas, consulte a [pré-requisitos para aceder ao Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Pontos finais da API 

O ponto final de API do Microsoft Graph para registos de auditoria está `https://graph.microsoft.com/beta/auditLogs/directoryAudits` e o ponto de final de API do Microsoft Graph para inícios de sessão é `https://graph.microsoft.com/beta/auditLogs/signIns`. Para obter mais informações, consulte a [referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) e [início de sessão referência da API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Além disso, pode utilizar o [eventos de risco do Identity Protection API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático aos deteções de segurança com o Microsoft Graph. Para obter mais informações, consulte [introdução ao Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  O **https:\/\/graph.windows.net\/\<nome do inquilino\>\/relatórios\/**  ponto final foi preterido. Utilize a nova API de pontos de extremidade descrito acima para acessar programaticamente os relatórios de atividade e segurança.
  
## <a name="apis-with-graph-explorer"></a>APIs com API do Graph

Pode utilizar o [MSGraph explorer](https://developer.microsoft.com/graph/graph-explorer) para verificar seu início de sessão e dados da API de auditoria. Certifique-se de iniciar sessão na sua conta com ambos os botões de início de sessão na IU do Explorador do gráfico e defina **AuditLog.Read.All** e **Directory.Read.All** permissões para o seu inquilino, conforme mostrado.   

![API do Graph](./media/concept-reporting-api/graph-explorer.png)

![Modificar as permissões da interface do Usuário](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Utilizar certificados para aceder a relatórios a API do Azure AD 

Utilize a API de relatórios do AD do Azure com certificados, se pretender recuperar dados de relatórios sem intervenção do utilizador.

Para obter instruções detalhadas, consulte [utilizar a API de relatórios do AD do Azure com certificados para obter dados](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Passos Seguintes

 * [Pré-requisitos para aceder à API de relatórios](howto-configure-prerequisites-for-reporting-api.md) 
 * [Obter dados a utilizar a API de relatórios do AD do Azure com certificados](tutorial-access-api-with-certificates.md)
 * [Resolver erros no Azure AD a API de relatórios](troubleshoot-graph-api.md)


