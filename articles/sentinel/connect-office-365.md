---
title: Conectar dados do Office 365 ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Office 365 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: df5aade7244f69e7264f901364ecc164351eec50
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815794"
---
# <a name="connect-data-from-office-365-logs"></a>Conectar dados de logs do Office 365



Você pode transmitir logs de auditoria do [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) para o Azure Sentinel com um único clique. Você pode transmitir logs de auditoria de vários locatários para um único espaço de trabalho no Azure Sentinel. O conector do log de atividades do Office 365 fornece informações sobre atividades de usuário contínuas. Você obterá informações sobre várias ações de usuário, de administrador, de sistema e de política e eventos do Office 365. Conectando os logs do Office 365 ao Azure Sentinel, você pode usar esses dados para exibir painéis, criar alertas personalizados e melhorar o processo de investigação.

> [!IMPORTANT]
> Se você tiver uma licença E3, antes de poder acessar dados por meio da API de atividade de gerenciamento do Office 365, você deverá habilitar o log de auditoria unificada para sua organização do Office 365. Faça isso ativando o log de auditoria do Office 365. Para obter instruções, consulte [Ativar ou desativar a pesquisa de log de auditoria do Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Consulte [referência de API de atividade de gerenciamento do Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ser um administrador global ou administrador de segurança em seu locatário
- No computador, do qual você fez logon no Azure Sentinel para criar a conexão, verifique se a porta 4433 está aberta para o tráfego da Web. Essa porta pode ser fechada novamente depois que a conexão é feita com êxito.
- Se seu locatário não tiver uma licença do Office 365 E3 ou do Office 365 e5, você deverá habilitar a auditoria unificada em seu locatário usando um destes processos:
    - [Usando o cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e habilitar o parâmetro "UnifiedAuditLogIngestionEnabled").
    - [Ou usando a interface do usuário do centro de conformidade e segurança](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Ligar ao Office 365

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco do **Office 365** .

2. Se você ainda não o tiver habilitado, poderá fazer isso acessando a folha **conectores de dados** e selecionando conector **do Office 365** . Aqui você pode clicar na **página abrir conector** e na seção configuração rotulada **habilitar a solução do Office 365 em seu espaço de trabalho** , use o botão **instalar solução** para habilitá-la. Se já tiver sido habilitado, ele será identificado na tela de conexão como já habilitado.
1. O Office 365 permite que você transmita dados de vários locatários para o Azure Sentinel. Para cada locatário ao qual você deseja se conectar, adicione o locatário em **conectar locatários ao Azure Sentinel**. 
1. Uma tela de Active Directory é aberta. Você será solicitado a autenticar com um usuário administrador global em cada locatário que deseja conectar ao Azure Sentinel e fornecer permissões ao Azure Sentinel para ler seus logs. 
5. Na lista de locatários, você veria a ID do diretório do Azure AD (ID do locatário) e duas caixas de seleção para logs do Exchange e do SharePoint. Você pode selecionar qualquer um ou todos os serviços listados que deseja ingerir no sentinela. Atualmente, o Azure Sentinel dá suporte aos logs do Exchange e do SharePoint nos serviços do Office365 existentes.

4. Depois de selecionar os serviços (Exchange, SharePoint etc.), você pode clicar em salvar no quadro de adição de locatário na página. 

3. Para usar o esquema relevante no Log Analytics para os logs do Office 365, pesquise **OfficeActivity**.


## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar o Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

