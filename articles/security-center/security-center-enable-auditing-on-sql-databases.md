---
title: Ativar auditoria e deteção de ameaças nas bases de dados SQL no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar a recomendação do Centro de segurança do Azure **ativar a auditoria e deteção de ameaças nas bases de dados do SQL**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1108265101f37433860d0112e4e80aee0002ab5c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127230"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Ativar auditoria e deteção de ameaças nas bases de dados SQL no Centro de segurança do Azure
Centro de segurança do Azure irá recomendar que Ativar deteção de ameaças e auditoria de todas as bases de dados do SQL se a auditoria e deteção de ameaças já não está ativada. Auditoria e ameaças, deteção pode ajudá-lo a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações empresariais ou suspeitas de violações de segurança.

Uma vez que ativou a auditoria pode configurar as definições de deteção de ameaças e mensagens de e-mail para receber alertas de segurança. Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados. Isto permite-lhe detetar e responder a potenciais ameaças à medida que ocorrem.

Esta recomendação aplica-se ao serviço do SQL do Azure, apenas; ele não inclui SQL em execução nas suas máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **recomendações** painel, selecione **ativar auditoria e deteção de ameaças nas bases de dados do SQL**.  Esta ação abre o **ativar auditoria e deteção de ameaças nas bases de dados do SQL** painel.

   ![Ativar a auditoria em bases de dados SQL][1]
2. Selecione uma base de dados SQL para ativar a auditoria em. Esta ação abre o **deteção de ameaças e auditoria** painel.

3. Sobre o **auditoria e deteção de ameaças** painel, selecione **ON** sob **auditoria**.

   ![Ativar auditoria e deteção de ameaças][2]
4. Siga os passos em [deteção de ameaças da base de dados SQL no portal do Azure](../sql-database/sql-database-threat-detection-portal.md) para ativar e configurar a deteção de ameaças e para configurar a lista de mensagens de e-mail que irá receber alertas de segurança após a deteção de atividades anómalas.

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação do Centro de segurança "ativar auditoria e deteção de ameaças nas bases de dados SQL." Para saber mais sobre como proteger a base de dados SQL, consulte o seguinte:

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
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
