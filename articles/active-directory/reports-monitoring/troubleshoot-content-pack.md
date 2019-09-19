---
title: Solução de problemas Azure Active Directory log de atividades erros do pacote de conteúdo | Microsoft Docs
description: Fornece uma lista de mensagens de erro do pacote de conteúdo do Azure Active Directory Activity e as etapas para corrigi-los.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e50f2b92318ada729ad8e3405af8403f31d7b6e
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129291"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Solucionando problemas de erros do pacote de conteúdo de logs de atividades Azure Active Directory 

|  |
|--|
|Atualmente, o pacote de conteúdos do Power BI para o Azure AD utiliza as APIs do Azure AD Graph para obter dados do seu inquilino do Azure AD. Como resultado, poderá observar alguma disparidade entre os dados disponíveis no pacote de conteúdos e os dados obtidos com as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

Ao trabalhar com o pacote de conteúdo do Power BI para Azure Active Directory (AD do Azure), é possível que você execute os seguintes erros: 

- [Falha na atualização](troubleshoot-content-pack.md#refresh-failed) 
- [Falha ao atualizar as credenciais da fonte de dados](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [A importação de dados está demorando muito](#data-import-is-too-slow) 

Este artigo fornece informações sobre as possíveis causas e como corrigir esses erros.
 
## <a name="refresh-failed"></a>A atualização falhou 
 
**Como esse erro é**exibido: Email de Power BI ou status com falha no histórico de atualização. 


| Causa | Como corrigir |
| ---   | ---        |
| Erros de falha na atualização podem ser causados quando as credenciais dos usuários que se conectam ao pacote de conteúdo tiverem sido redefinidas, mas não atualizadas nas configurações de conexão do pacote de conteúdo. | Em Power BI, localize o conjunto de registros correspondente ao painel de logs de atividades do Azure AD (**logs de atividades do Azure Active Directory**), escolha agendar atualização e insira suas credenciais do Azure AD. |
| Uma atualização pode falhar devido a grandes conjuntos de altos. | Atualmente, o pacote de conteúdo do Azure AD com o Power BI pode dar suporte apenas a pequenos conjuntos de dados (menos de 500.000 linhas) devido a algumas limitações em relação aos tempos limite no serviço Power BI. Se você encontrar erros de limitação ou se a atualização falhar devido a problemas de tempo limite, isso pode ocorrer porque você está tentando buscar um grande conjunto de grandes. Reduza o período de tempo na consulta e tente novamente.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais da fonte de dados 
 
**Como esse erro é**exibido: No Power BI, quando você se conecta ao pacote de conteúdo dos logs de atividades do Azure AD. 

| Causa | Como corrigir |
| ---   | ---        |
| O usuário que está se conectando não é um administrador global ou um leitor de segurança ou um administrador de segurança. | Use uma conta que seja um administrador global ou um leitor de segurança ou um administrador de segurança para acessar os pacotes de conteúdo. |
| Seu locatário não é um locatário Premium ou não tem pelo menos um usuário com o arquivo de licença Premium. | [Arquivo um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>A importação de dados está muito lenta 
 
**Como esse erro é**exibido: No Power BI, depois de conectar o pacote de conteúdo, o processo de importação de dados começa a preparar seu painel para os logs de atividades do Azure AD. Verá a mensagem: **Importando dados...** sem nenhum progresso adicional.  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do seu locatário, essa etapa pode levar de alguns minutos a 30 minutos. | Se a mensagem não for alterada para mostrar o painel em uma hora, faça [um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Passos seguintes

* [Instale o pacote de conteúdo do Power bi para relatórios do Azure ad](quickstart-install-power-bi-content-pack.md).
* [Use Power BI pacote de conteúdo para relatórios do Azure AD para visualizar seus dados](howto-power-bi-content-pack.md)
* [Como obter apoio para o Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
