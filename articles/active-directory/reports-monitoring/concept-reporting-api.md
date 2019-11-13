---
title: Introdução à API de relatórios do AD do Azure | Microsoft Docs
description: Como começar a usar a API de relatório do Azure Active Directory
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
ms.openlocfilehash: c841195cacd8c8d7698f69435572587bf04c3d67
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74008366"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introdução à API de relatório do Azure Active Directory

Azure Active Directory fornece uma variedade de [relatórios](overview-reports.md), contendo informações úteis para aplicativos como sistemas Siem, auditoria e ferramentas de Business Intelligence. 

Usando a API de Microsoft Graph para relatórios do Azure AD, você pode obter acesso programático aos dados por meio de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação.

Este artigo fornece uma visão geral da API de relatório, incluindo maneiras de acessá-la.

Se você encontrar problemas, consulte [como obter suporte para Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Pré-requisitos

Para acessar a API de relatório, com ou sem a intervenção do usuário, você precisa:

1. Atribuir funções (leitor de segurança, administrador de segurança, administrador global)
2. Registar uma aplicação
3. Conceder permissões
4. Reunir definições de configuração

Para obter instruções detalhadas, consulte os [pré-requisitos para acessar a API do Azure Active Directory Reporting](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Pontos de extremidade de API 

O ponto de extremidade de API Microsoft Graph para logs de auditoria é `https://graph.microsoft.com/beta/auditLogs/directoryAudits` e o ponto de extremidade de API Microsoft Graph para entradas é `https://graph.microsoft.com/beta/auditLogs/signIns`. Para obter mais informações, consulte a referência da [API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) e a [referência da API de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn).

Além disso, você pode usar a [API de detecções de risco da proteção de identidade](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático a detecções de segurança usando o Microsoft Graph. Para obter mais informações, consulte Introdução [ao Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/graph-get-started.md). 

> [!NOTE]
>  O **https:\/\/graph.windows.net\/\<nome do locatário\>\/relatórios**\/ponto de extremidade foi preterido. Use os novos pontos de extremidade de API descritos acima para acessar programaticamente os relatórios de atividade e de segurança.
  
## <a name="apis-with-graph-explorer"></a>APIs com o Graph Explorer

Você pode usar o [MSGraph Explorer](https://developer.microsoft.com/graph/graph-explorer) para verificar seus dados de API de entrada e auditoria. Certifique-se de entrar em sua conta usando os dois botões de entrada na interface do usuário do Graph Explorer e defina as permissões **AuditLog. Read. All** e **Directory. Read. All** para seu locatário, conforme mostrado.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Modificar a interface do usuário de permissões](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Usar certificados para acessar a API de relatórios do Azure AD 

Use a API de relatórios do Azure AD com certificados se você planeja recuperar dados de relatórios sem intervenção do usuário.

Para obter instruções detalhadas, consulte [obter dados usando a API de relatórios do Azure AD com certificados](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Passos seguintes

 * [Pré-requisitos para acessar a API de relatórios](howto-configure-prerequisites-for-reporting-api.md) 
 * [Obter dados usando a API de relatórios do Azure AD com certificados](tutorial-access-api-with-certificates.md)
 * [Solucionar erros na API de relatórios do Azure AD](troubleshoot-graph-api.md)


