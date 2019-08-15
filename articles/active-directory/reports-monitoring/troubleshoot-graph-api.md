---
title: Solucionar erros na API de relatórios Azure Active Directory | Microsoft Docs
description: Fornece uma resolução para erros ao chamar Azure Active Directory APIs de relatório.
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f1f34dcece9acb20d0db091152b24b26cb9fa2
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989536"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Solucionar erros na API de relatório Azure Active Directory

Este artigo lista as mensagens de erro comuns que você pode encontrar ao acessar os relatórios de atividade usando o MS API do Graph e as etapas para sua resolução.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>erro de servidor interno HTTP 500 ao acessar o ponto de extremidade Microsoft Graph v2

No momento, não há suporte para o ponto de extremidade Microsoft Graph v2-certifique-se de acessar os logs de atividade usando o ponto de extremidade Microsoft Graph v1.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Erro: Falha ao obter funções de usuário do AD Graph

Você pode receber essa mensagem de erro ao tentar acessar as entradas usando o Gerenciador de gráficos. Verifique se você está conectado à sua conta usando os dois botões de entrada na interface do usuário do Graph Explorer, conforme mostrado na imagem a seguir. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Erro: Falha ao fazer a verificação de licença Premium do grafo do AD 

Se você encontrar essa mensagem de erro ao tentar acessar entradas usando o Graph Explorer, escolha **Modificar permissões** abaixo de sua conta na barra de navegação à esquerda e selecione **tarefas. ReadWrite** e **Directory. Read. All**. 

![Modificar a interface do usuário de permissões](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Erro: Nenhum locatário é B2C ou o locatário não tem licença Premium

O acesso a relatórios de entrada requer uma licença Azure Active Directory Premium 1 (P1). Se você vir essa mensagem de erro ao acessar entradas, certifique-se de que seu locatário esteja licenciado com uma licença do Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Erro: O usuário não está nas funções permitidas 

Se você vir essa mensagem de erro ao tentar acessar logs de auditoria ou entradas usando a API, verifique se sua conta faz parte da função leitor de **segurança** ou **leitor de relatório** em seu locatário Azure Active Directory. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Erro: O aplicativo não tem a permissão ' ler dados do diretório ' do AAD 

Siga as etapas em [pré-requisitos para acessar a API de relatório de Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja sendo executado com o conjunto certo de permissões. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Erro: O aplicativo não possui a permissão ' ler todos os dados de log de auditoria ' da API MSGraph

Siga as etapas em [pré-requisitos para acessar a API de relatório de Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) para garantir que seu aplicativo esteja sendo executado com o conjunto certo de permissões. 

## <a name="next-steps"></a>Próximos Passos

[Usar a referência](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
de API de auditoria[usar a referência de API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
