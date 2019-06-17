---
title: Resolução de problemas de atividade do Active Directory do Azure regista erros de pacote de conteúdos | Documentos da Microsoft
description: Fornece uma lista de mensagens de erro do pacote de conteúdos de atividade do Active Directory do Azure e nos passos para corrigi-los.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ee49ae56122fe596a4490914677d91d2f0348f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807523"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Resolução de problemas de atividade do Active Directory do Azure regista erros de pacote de conteúdos 

|  |
|--|
|Atualmente, o pacote de conteúdos do Power BI para o Azure AD utiliza as APIs do Azure AD Graph para obter dados do seu inquilino do Azure AD. Como resultado, poderá observar alguma disparidade entre os dados disponíveis no pacote de conteúdos e os dados obtidos com as [APIs do Microsoft Graph para relatórios](concept-reporting-api.md). |
|  |

Ao trabalhar com o pacote de conteúdos do Power BI para o Azure Active Directory (Azure AD), é possível que se deparar com os seguintes erros: 

- [Falha na atualização](troubleshoot-content-pack.md#refresh-failed) 
- [Falha ao atualizar as credenciais de origem de dados](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Importação de dados está a demorar demasiado tempo](#data-import-is-too-slow) 

Este artigo fornece informações sobre as possíveis causas e como corrigir estes erros.
 
## <a name="refresh-failed"></a>Falha na atualização 
 
**Como este erro aparece**: E-mail do Power BI ou no histórico de atualização do status de falha. 


| Causa | Como corrigir |
| ---   | ---        |
| Falha de erros podem ser provocados quando as credenciais dos utilizadores a ligar ao pacote de conteúdos tem sido repor mas não atualizadas nas definições de ligação do pacote de conteúdos de atualização. | No Power BI, localize o conjunto de dados correspondente para o dashboard de registos de atividade do Azure AD (**registos de atividades de diretório Active Directory do Azure**), selecione a atualização de agenda e, em seguida, introduza as credenciais do Azure AD. |
| Uma atualização pode falhar devido a grandes conjuntos de dados. | Atualmente, o pacote de conteúdos do Azure AD com o Power BI pode suportar apenas conjuntos de dados pequenos (menos de 500,00 linhas) devido a algumas limitações em torno de tempos limite no serviço Power BI. Se ocorrerem erros de limitação, ou se a atualização falhar devido a problemas de tempo limite, talvez porque está a tentar obter um conjunto de dados grandes. Reduza o período de tempo na consulta e tente novamente.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Falha ao atualizar as credenciais de origem de dados 
 
**Como este erro aparece**: No Power BI, quando se liga para o pacote de conteúdos de registos de atividade do Azure AD. 

| Causa | Como corrigir |
| ---   | ---        |
| O utilizador de ligação não é um administrador global ou um leitor de segurança ou um administrador de segurança. | Utilize uma conta que seja um administrador global ou um leitor de segurança ou um administrador de segurança para aceder os pacotes de conteúdos. |
| O inquilino não é um inquilino de Premium ou não tem pelo menos um utilizador com o ficheiro de licença de Premium. | [Enviar um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Importação de dados é muito lenta 
 
**Como este erro aparece**: No Power BI, depois de ligar o seu pacote de conteúdos, o processo de importação de dados começa a preparar o seu dashboard para a atividade do Azure AD registos. Verá a mensagem: **Importar dados...**  sem qualquer progresso adicional.  

| Causa | Como corrigir |
| ---   | ---        |
| Dependendo do tamanho do seu inquilino, este passo pode demorar entre alguns minutos e 30 minutos. | Se a mensagem não é alterado para mostrar o seu dashboard dentro de uma hora [enviar um pedido de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Passos Seguintes

* [Instalar o pacote de conteúdos do Power BI para relatórios do Azure AD](quickstart-install-power-bi-content-pack.md).
* [Utilizar o conteúdo de pacote para o Azure AD relatórios para visualizar os seus dados do Power BI](howto-power-bi-content-pack.md)
* [Como obter apoio para o Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
