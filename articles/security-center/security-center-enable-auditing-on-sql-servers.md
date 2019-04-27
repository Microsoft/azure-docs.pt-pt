---
title: Ativar auditoria e deteção de ameaças em servidores SQL no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar a recomendação do Centro de segurança do Azure **ativar auditoria e deteção de ameaças em servidores SQL**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 5b41af83122d74fc766e6c5179d98803979269f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704661"
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Ativar auditoria e deteção de ameaças em servidores SQL no Centro de segurança do Azure
Centro de segurança do Azure irá recomendar que ativar a auditoria e deteção de ameaças para todas as bases de dados nos seus servidores SQL do Azure, se a auditoria já não está ativada. Auditoria e ameaças, deteção pode ajudá-lo a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações empresariais ou suspeitas de violações de segurança.

Uma vez que ativou a auditoria pode configurar as definições de deteção de ameaças e mensagens de e-mail para receber alertas de segurança. Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados. Isto permite-lhe detetar e responder a potenciais ameaças à medida que ocorrem.

Esta recomendação aplica-se ao serviço do SQL do Azure, apenas; ele não inclui o SQL Server em execução nas suas máquinas virtuais nos serviços de infraestrutura do Azure (IaaS do Azure).

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **recomendações** painel, selecione **ativar auditoria e deteção de ameaças em servidores SQL**.  Esta ação abre o **ativar auditoria e deteção de ameaças em servidores SQL** painel.

   ![Ativar a auditoria em servidores SQL][1]
2. Selecione um servidor SQL para ativar a auditoria e deteção de ameaças. Esta ação abre o **deteção de ameaças e auditoria** painel.

3. Sobre o **auditoria e deteção de ameaças** painel, selecione **ON** sob **auditoria**.

   ![Ativar a auditoria definições][2]
4. Siga os passos em [auditoria de base de dados SQL no portal do Azure](../sql-database/sql-database-auditing-portal.md) para configurar o armazenamento onde serão armazenados os seus registos de auditoria. Conta de armazenamento da subscrição para a recolha de dados é a conta de armazenamento predefinida.
5. Siga os passos em [introdução à SQL Database Threat Detection](../sql-database/sql-database-threat-detection.md) para ativar e configurar a deteção de ameaças e para configurar a lista de mensagens de e-mail que irá receber alertas de segurança após a deteção de atividades anómalas.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a centro de segurança recomendação "ativar auditoria e deteção de ameaças em servidores SQL." Para saber mais sobre como proteger a base de dados SQL, consulte o seguinte:

* [Securing your SQL Database (Proteger a sua Base de Dados SQL)](../sql-database/sql-database-security-overview.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
