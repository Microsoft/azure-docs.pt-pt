---
title: Começar com a AZure AD reportando API / Microsoft Docs
description: Como começar com o Azure Ative Directory reportando API
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
ms.openlocfilehash: cf2e3f98bd96c09fe741a24a1332443c876e2e61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89225915"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Começar com o Diretório Ativo Azure reportando API

O Azure Ative Directory fornece-lhe uma variedade de [relatórios,](overview-reports.md)contendo informações úteis para aplicações como sistemas SIEM, auditoria e ferramentas de inteligência empresarial. 

Ao utilizar a API do Gráfico da Microsoft para relatórios AD do Azure, pode obter acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação.

Este artigo fornece-lhe uma visão geral da API que reporta, incluindo formas de aceder ao mesmo.

Se tiver problemas, veja [como obter apoio para o Azure Ative Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).

## <a name="prerequisites"></a>Pré-requisitos

Para aceder à API de reporte, com ou sem intervenção do utilizador, é necessário:

1. Atribuir funções (Leitor de Segurança, Administrador de Segurança, Administrador Global)
2. Registar uma aplicação
3. Conceder permissões
4. Recolher configurações de configuração

Para obter instruções detalhadas, consulte os [pré-requisitos para aceder ao Diretório Ativo Azure.](howto-configure-prerequisites-for-reporting-api.md) 

## <a name="api-endpoints"></a>Pontos finais da API 

O ponto final da Microsoft Graph API para registos de auditoria é `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` e o ponto final da Microsoft Graph API para iniciar sessão é `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Para obter mais informações, consulte a [referência da API](/graph/api/resources/directoryaudit?view=graph-rest-1.0) de auditoria e [a referência API de acesso](/graph/api/resources/signIn?view=graph-rest-1.0)a sinal .

Além disso, pode utilizar a [API de deteção de risco de Proteção de Identidade](/graph/api/resources/identityriskevent?view=graph-rest-beta) para obter acesso programático a deteções de segurança utilizando o Microsoft Graph. Para obter mais informações, consulte [Começar com a Azure Ative Directory Identity Protection e microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>APIs com Microsoft Graph Explorer

Pode utilizar o explorador de [gráficos](https://developer.microsoft.com/graph/graph-explorer) da Microsoft para verificar o seu sômis e auditar dados de API. Certifique-se de iniciar sposição na sua conta usando ambos os botões de inscrição no UI do Graph Explorer e de definir **AuditLog.Read.All** and **Directory.Read.Todas as** permissões para o seu inquilino, como mostrado.   

![Explorador de Gráficos](./media/concept-reporting-api/graph-explorer.png)

![Modificar permissões UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Utilize certificados para aceder à API de reporte de Azure 

Utilize a API de Reporte AZURE com certificados se pretender recuperar dados de reporte sem a intervenção do utilizador.

Para obter instruções detalhadas, consulte [obter dados utilizando a API de reporte da Azure AD reportando com certificados](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Passos seguintes

 * [Pré-requisitos para aceder à API de reporte](howto-configure-prerequisites-for-reporting-api.md) 
 * [Utilizar a API de Relatórios do Azure AD com certificados para obter dados](tutorial-access-api-with-certificates.md)
 * [Erros de resolução de problemas na AZure AD reportando API](troubleshoot-graph-api.md)