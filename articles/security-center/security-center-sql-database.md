---
title: Central de segurança do Azure e serviço do banco de dados SQL do Azure | Microsoft Docs
description: Este artigo mostra como a central de segurança pode ajudá-lo a proteger seus bancos de dados no Azure SQL Database.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704616"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Central de segurança do Azure e serviço do banco de dados SQL do Azure
O [Centro de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Este artigo mostra como a central de segurança pode ajudá-lo a proteger seus bancos de dados no Azure SQL Database.

## <a name="why-use-security-center"></a>Porquê utilizar o Centro de Segurança?
A central de segurança ajuda você a proteger os dados no SQL Database, fornecendo visibilidade sobre a segurança de todos os seus servidores e bancos de dados. Com a central de segurança, você pode:

* Defina políticas para a criptografia e a auditoria do banco de dados SQL.
* Monitore a segurança dos recursos do banco de dados SQL em todas as suas assinaturas.
* Identifique e corrija rapidamente problemas de segurança.
* Integre alertas da [detecção de ameaças do banco de dados SQL do Azure](../sql-database/sql-database-threat-detection.md).

Além de ajudar a proteger seus recursos do banco de dados SQL, a central de segurança também fornece monitoramento e gerenciamento de segurança para máquinas virtuais do Azure, serviços de nuvem, serviços de aplicativos, redes virtuais e muito mais. Saiba mais sobre a central de segurança [aqui](security-center-intro.md).

## <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. A camada gratuita da central de segurança está habilitada com sua assinatura. Para obter mais informações sobre as camadas gratuita e Standard da central de segurança, consulte [preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

A central de segurança dá suporte ao acesso baseado em função. Para saber mais sobre o RBAC (controle de acesso baseado em função) no Azure, consulte [Azure Active Directory controle de acesso baseado em função](../role-based-access-control/role-assignments-portal.md). As perguntas frequentes sobre a central de segurança fornecem informações sobre [como as permissões são tratadas na central de segurança](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Aceder ao Centro de Segurança
Pode aceder ao Centro de Segurança a partir do [Portal do Azure](https://azure.microsoft.com/features/azure-portal/). [Entre no portal](https://portal.azure.com/) e selecione a **opção central de segurança**.

![Opção da central de segurança][1]

A folha **central de segurança** é aberta.
![Folha da central de segurança][2]

## <a name="set-security-policy"></a>Definir política de segurança
Uma política de segurança define o conjunto de controles que são recomendados para recursos dentro da assinatura ou grupo de recursos especificado. Na central de segurança, você define políticas para suas assinaturas ou grupos de recursos de acordo com as necessidades de segurança da sua empresa e os tipos de aplicativos ou a sensibilidade dos dados em cada assinatura.

Você pode definir uma política para mostrar recomendações para a auditoria do SQL e a TDE (Transparent Data Encryption) do SQL.

* Quando você ativa a **detecção de ameaças e auditoria do SQL**, a central de segurança recomenda que a auditoria de acesso ao banco de dados do Azure seja habilitada para fins de conformidade, detecção avançada e investigação.
* Quando você ativa o **SQL Transparent Data Encryption**, a central de segurança recomenda que a criptografia em repouso seja habilitada para seu banco de dados SQL do Azure, backups associados e arquivos de log de transações.

Para definir uma política de segurança, selecione o bloco **política** na folha central de segurança. Na folha **política de segurança** , selecione a assinatura na qual você deseja habilitar a política de segurança. Selecione **política de prevenção** e ative as recomendações de segurança que você deseja usar nessa assinatura.
![Política de segurança][3]

Para saber mais, consulte [definir políticas de segurança](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Gerenciar recomendação de segurança
O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Quando o Centro de Segurança identifica potenciais vulnerabilidades de segurança, cria recomendações. As recomendações orientam-no no processo de configuração de controlos necessários.

Depois de definir uma política de segurança, a central de segurança analisa o estado de segurança de seus recursos para identificar possíveis vulnerabilidades. As recomendações são exibidas em um formato de tabela onde cada linha representa uma recomendação específica. Use a tabela a seguir como referência para ajudá-lo a entender as recomendações disponíveis para o banco de dados SQL do Azure e o que cada recomendação faz se você aplicá-la. A seleção de uma recomendação leva você a um artigo que explica como implementar a recomendação na central de segurança.

| Recomendação | Descrição |
| --- | --- |
| [Habilitar auditoria e detecção de ameaças em servidores SQL](security-center-enable-auditing-on-sql-servers.md) |Recomenda que você ative a auditoria e detecção de ameaças para servidores de banco de dados SQL. (Somente serviço do banco de dados SQL. Não inclui Microsoft SQL Server em execução em suas máquinas virtuais.) |
| [Habilitar auditoria e detecção de ameaças em bancos de dados SQL](security-center-enable-auditing-on-sql-databases.md) |Recomenda que você ative a auditoria e detecção de ameaças para bancos de dados do banco de dados SQL. (Somente serviço do banco de dados SQL. Não inclui Microsoft SQL Server em execução em suas máquinas virtuais.) |
| [Ativar a Encriptação de Dados Transparente](security-center-enable-transparent-data-encryption.md) |Recomenda que você habilite a criptografia para bancos de dados SQL. (Somente serviço do banco de dados SQL.) |

Para ver as recomendações para os recursos do Azure, selecione o bloco **recomendações** na folha central de segurança. Na folha **recomendações** , selecione uma recomendação para ver os detalhes. Neste exemplo, vamos selecionar habilitar a **auditoria & detecção de ameaças em servidores SQL**.

![Recomendações][4]

Conforme mostrado abaixo, a central de segurança mostra os servidores SQL em que a auditoria e a detecção de ameaças não estão habilitadas. Depois de ativar a auditoria, você pode definir configurações de detecção de ameaças e configurações de email para receber alertas de segurança. A detecção de ameaças alerta você quando detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados. Os alertas são exibidos no painel central de segurança.
![Auditoria e deteção de ameaças][5]

Siga as etapas em [detecção de ameaças do banco de dados SQL no portal do Azure](../sql-database/sql-database-threat-detection-portal.md) para ativar e configurar a detecção de ameaças e para configurar a lista de emails que receberão alertas de segurança após a detecção de atividades anômalas.

Para saber mais sobre as recomendações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorizar o estado de funcionamento da segurança
Depois de ativar [políticas de segurança](tutorial-security-policy.md) para os recursos de uma subscrição, o Centro de Segurança irá analisar a segurança dos seus recursos para identificar potenciais vulnerabilidades.  Você pode exibir o estado de segurança de seus recursos no bloco **integridade da segurança do recurso** . Quando você clica em **dados** no bloco **integridade da segurança do recurso** , a folha recursos de **dados** é aberta com recomendações do SQL para problemas como a auditoria e a Transparent Data Encryption não estar habilitado. Também tem recomendações para o estado de funcionamento geral da base de dados.
![Integridade da segurança de recursos][6]

Para saber mais, consulte [monitoramento de integridade de segurança](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gerir e responder a alertas de segurança
A central de segurança coleta, analisa e integra automaticamente os dados de log da [detecção de ameaças do SQL do Azure](../sql-database/sql-database-threat-detection.md), bem como outros recursos do Azure, para detectar ameaças reais e reduzir os falsos positivos. Uma lista de alertas de segurança prioritários é apresentada no Centro de Segurança juntamente com as informações necessárias para investigar rapidamente o problema e fornecer recomendações sobre como remediar um ataque.

Para ver os alertas, selecione o bloco **alertas de segurança** na folha central de segurança. Na folha **alertas de segurança** , selecione um alerta para saber mais sobre os eventos que dispararam o alerta e quais são as etapas que você precisa executar para corrigir um ataque. Neste exemplo, vamos selecionar **possível injeção de SQL**.
![Alertas de segurança][7]

Conforme mostrado abaixo, a central de segurança fornece detalhes adicionais que oferecem informações sobre o que disparou o alerta, o recurso de destino, quando aplicável o endereço IP de origem e recomendações sobre como corrigi-lo.
![Possível injeção de SQL][8]

Para saber mais, consulte [Gerenciando e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Passos Seguintes
* [Perguntas frequentes da central de segurança](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço.
* [Guia de planejamento e operações da central de segurança](security-center-planning-and-operations-guide.md) -siga um conjunto de etapas e tarefas para otimizar o uso da central de segurança com base nos requisitos de segurança e no modelo de gerenciamento de nuvem de sua organização.
* [Segurança de dados da central de segurança](security-center-data-security.md) – saiba como a central de segurança coleta e processa dados sobre seus recursos do Azure, incluindo informações de configuração, metadados, logs de eventos, arquivos de despejo de memória e muito mais.
* [Lidando com incidentes de segurança](security-center-incident.md) – saiba como usar o recurso de alerta de segurança na central de segurança para ajudá-lo a lidar com incidentes de segurança.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
