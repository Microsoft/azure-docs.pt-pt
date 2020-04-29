---
title: Começar com a AD Azure reportando API [ Microsoft Docs
description: Como começar com o Diretório Ativo Azure reportando API
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
ms.openlocfilehash: 56f240a5191dd483f89889f3ffe13b1819ca1e53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78399319"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Começar com o Diretório Ativo Azure reportando API

O Azure Ative Directory fornece-lhe uma variedade de [relatórios,](overview-reports.md)contendo informações úteis para aplicações como sistemas SIEM, auditoria e ferramentas de inteligência empresarial. 

Ao utilizar o Microsoft Graph API para relatórios AD Azure, pode obter acesso programático aos dados através de um conjunto de APIs baseados em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação.

Este artigo fornece-lhe uma visão geral da API reportando, incluindo formas de aceder ao mesmo.

Se tiver problemas, veja como obter apoio para o [Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Pré-requisitos

Para aceder à API reporte, com ou sem intervenção do utilizador, é necessário:

1. Atribuir funções (Security Reader, Security Admin, Global Admin)
2. Registar uma aplicação
3. Conceder permissões
4. Recolher configurações de configuração

Para obter instruções detalhadas, consulte os [pré-requisitos para aceder ao Diretório Ativo Azure reportando API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Pontos Finais da API 

O ponto final da Microsoft Graph `https://graph.microsoft.com/beta/auditLogs/directoryAudits` API para registos de auditoria é `https://graph.microsoft.com/beta/auditLogs/signIns`e o ponto final da Microsoft Graph API para iniciar sessão é . Para mais informações, consulte a referência da [API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) de auditoria e a [referência de api.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signIn)

Além disso, pode utilizar a API de deteção de risco de [Proteção de Identidade](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) para obter acesso programático a deteções de segurança usando o Microsoft Graph. Para mais informações, consulte [Start start with Azure Ative Directory Identity Protection and Microsoft Graph](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>APIs com Microsoft Graph Explorer

Pode utilizar o explorador do [Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) para verificar os seus dados de sessão e auditoria da API. Certifique-se de que insere na sua conta utilizando ambos os botões de início de sessão no Graph Explorer UI e desemace **o AuditLog.Read.All** and **Directy.Read.Todas as** permissões para o seu inquilino, conforme mostrado.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Modificar permissões UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Utilize certificados para aceder à AD Azure reportando API 

Utilize a APi de Reporte AD Azure com certificados se pretender recuperar dados de reporte sem a intervenção do utilizador.

Para obter instruções detalhadas, consulte Obtenha dados utilizando a APi de [Reporte aD Azure com certificados](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Passos seguintes

 * [Pré-requisitos para aceder à API reportando](howto-configure-prerequisites-for-reporting-api.md) 
 * [Utilizar a API de Relatórios do Azure AD com certificados para obter dados](tutorial-access-api-with-certificates.md)
 * [Erros de resolução de problemas na AD Azure reportam API](troubleshoot-graph-api.md)


