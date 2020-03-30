---
title: Erros de resolução de problemas no Diretório Ativo da Azure reportam API / Microsoft Docs
description: Fornece-lhe uma resolução de erros ao chamar AApi de Relatório de Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1fb4f49e4f9ad41f971d869873200e6180b5cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399283"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Erros de resolução de problemas no Diretório Ativo da Azure reportam API

Este artigo lista as mensagens de erro comuns que pode encontrar ao aceder a relatórios de atividade utilizando a API do Microsoft Graph e passos para a sua resolução.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Erro do servidor interno http 500 ao aceder ao ponto final do Microsoft Graph V2

Não suportamos atualmente o ponto final do Microsoft Graph v2 - certifique-se de aceder aos registos de atividade utilizando o ponto final do Microsoft Graph v1.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: Nenhum inquilino é B2C ou inquilino não tem licença premium

O acesso aos relatórios de entrada requer uma licença azure Ative Diretório premium 1 (P1). Se vir esta mensagem de erro ao aceder a inscrições, certifique-se de que o seu inquilino está licenciado com uma licença Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Erro: O utilizador não está nas funções permitidas 

Se vir esta mensagem de erro ao tentar aceder a registos de auditoria ou logins utilizando a API, certifique-se de que a sua conta faz parte do papel do Leitor de **Segurança** ou do Leitor de **Relatórios** no seu inquilino do Diretório Ativo Azure. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: Falha de aplicação AAD 'Ler dados de directório' 

Siga os passos nos [Pré-requisitos para aceder à API](howto-configure-prerequisites-for-reporting-api.md) de reporte ativo do Azure para garantir que a sua aplicação está a funcionar com o conjunto certo de permissões. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Erro: Falha de aplicação Microsoft Graph API 'Leia todos os dados de registo de auditoria'

Siga os passos nos [Pré-requisitos para aceder à API](howto-configure-prerequisites-for-reporting-api.md) de reporte ativo do Azure para garantir que a sua aplicação está a funcionar com o conjunto certo de permissões. 

## <a name="next-steps"></a>Passos Seguintes

[Utilize a referência](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
api audita[Utilize o relatório de atividades de inatividade de acesso a referência API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
