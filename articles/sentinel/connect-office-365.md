---
title: Ligue os dados do Gabinete 365 ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar os dados do Office 365 ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252521"
---
# <a name="connect-data-from-office-365-logs"></a>Ligar dados do Office 365 Logs



Pode transmitir registos de auditoria do [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) para o Azure Sentinel com um único clique. Pode transmitir registos de auditoria do seu Office 365 para o seu espaço de trabalho Azure Sentinel no mesmo inquilino. O conector de registo de atividade sonorizador do Office 365 fornece informações sobre as atividades de utilizador em curso. Obterá informações sobre vários utilizadores, administradores, sistemas e ações de política e eventos do Office 365. Ao ligar os logs do Office 365 ao Azure Sentinel pode utilizar estes dados para visualizar dashboards, criar alertas personalizados e melhorar o seu processo de investigação.

> [!IMPORTANT]
> Se tiver uma licença E3, antes de poder aceder a dados através da API de Atividade de Gestão do Office 365, deve permitir a exploração de auditoria unificada para a sua organização office 365. Faça-o ligando o registo de auditoria do Office 365. Para obter instruções, consulte a pesquisa de registo de auditoria do [Turn Office 365 dentro ou fora](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Consulte o [Office 365 management Activity API reference,](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)para mais informações.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ser administrador global ou administrador de segurança do seu inquilino.
- O seu inquilino deve ter uma auditoria unificada ativada. Os inquilinos com licenças Do Office 365 E3 ou E5 têm auditoria unificada viavida por defeito. <br>Se o seu inquilino não tiver uma destas licenças, deve permitir uma auditoria unificada ao seu inquilino utilizando um destes métodos:
    - [Utilizando o cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) e ativar o parâmetro "UnificadoAuditLogIngestionEnabled").
    - [Utilização do UI do Centro de Segurança e Conformidade](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Ligar ao Office 365

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, clique no azulejo **365 do Office.**

2. Se ainda não o tiver ativado, pode fazê-lo indo à lâmina dos **Conectores** de Dados e selecionando o conector **do Office 365.** Aqui pode clicar na página do **Conector Aberto** e na secção de configuração **configuração** configuração selecione todos os registos de atividade do Office 365 que pretende ligar ao Azure Sentinel. 
   > [!NOTE]
   > Se já ligou vários inquilinos numa versão previamente suportada do conector Office 365 em Azure Sentinel, poderá visualizar e modificar quais os registos que recolhe de cada inquilino. Você não poderá adicionar inquilinos adicionais, mas você pode remover inquilinos anteriormente adicionados.
3. Para utilizar o esquema relevante no Log Analytics para os registos do Office 365, procure **officeActivity**.


## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Office 365 ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)

