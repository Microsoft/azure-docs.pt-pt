---
title: Erros de resolução de problemas no Azure Ative Directory reportando API Microsoft Docs
description: Fornece-lhe uma resolução de erros ao ligar para a Azure Ative Reporty Reporting APIs.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: eec5c5a3d810fdd2d561313e3a355e872fb525c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608097"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Erros de resolução de problemas no Azure Ative Directory reportando API

Este artigo lista as mensagens de erro comuns que pode encontrar ao aceder a relatórios de atividade utilizando a API do Gráfico da Microsoft e passos para a sua resolução.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Erro do servidor interno 500 HTTP ao aceder ao ponto final do Microsoft Graph V2

Não suportamos atualmente o ponto final do Microsoft Graph v2 - certifique-se de aceder aos registos de atividade usando o ponto final do Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: Nenhum inquilino é B2C ou inquilino não tem licença premium

Aceder a relatórios de inscrição requer uma licença Azure Ative Directory premium 1 (P1). Se vir esta mensagem de erro ao aceder a insiná-lo, certifique-se de que o seu inquilino é licenciado com uma licença Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Erro: O utilizador não está nas funções permitidas 

Se vir esta mensagem de erro enquanto tenta aceder a registos de auditoria ou de login utilizando a API, certifique-se de que a sua conta faz parte da função **de Leitor** de Segurança ou Leitor de **Relatórios** no seu inquilino Azure Ative Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: Aplicação em falta AAD 'Ler dados de directório' 

Por favor, siga os passos nos [Pré-requisitos para aceder à API de reporte de relatórios Azure Ative](howto-configure-prerequisites-for-reporting-api.md) para garantir que a sua aplicação está a funcionar com o conjunto de permissões adequado. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Erro: Aplicação em falta da API do Gráfico do Microsoft 'Leia todos os dados do registo de auditoria'

Por favor, siga os passos nos [Pré-requisitos para aceder à API de reporte de relatórios Azure Ative](howto-configure-prerequisites-for-reporting-api.md) para garantir que a sua aplicação está a funcionar com o conjunto de permissões adequado. 

## <a name="next-steps"></a>Passos Seguintes

[Utilize a referência](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 API de auditoria [Utilize a referência API do relatório de atividades de inscrição](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
