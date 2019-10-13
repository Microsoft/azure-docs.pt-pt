---
title: Segurança de dados avançada-banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a funcionalidade para descobrir e classificar dados confidenciais, gerenciar suas vulnerabilidades de banco de dado e detectar atividades anormais que podem indicar uma ameaça ao seu banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: a8b380355d7eee6b79bff01d29160fdd3b42800d
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285812"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Segurança de dados avançada para o banco de dado SQL do Azure

A segurança de dados avançada é um pacote unificado para recursos avançados de segurança do SQL. Ele inclui a funcionalidade para descobrir e classificar dados confidenciais, identificandor e reduzir possíveis vulnerabilidades de banco de dado e detectar atividades anormais que podem indicar uma ameaça ao seu banco de dados. Oferece uma localização única para ativar e gerir estas capacidades.

## <a name="overview"></a>Visão geral

O ADS (Advanced Data Security) fornece um conjunto de recursos avançados de segurança do SQL, incluindo a descoberta de dados & classificação, avaliação de vulnerabilidade e proteção avançada contra ameaças.

- A [& de descoberta de dados](sql-database-data-discovery-and-classification.md) (atualmente em versão prévia) fornece recursos incorporados ao banco de dado SQL do Azure para descobrir, classificar, rotular & proteger os dados confidenciais em seus bancos. Pode ser utilizada para oferecer visibilidade sobre o estado de classificação da base de dados e para controlar o acesso aos dados confidenciais na base de dados e além dos respetivos limites.
- A [avaliação de vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, acompanhar e ajudá-lo a corrigir possíveis vulnerabilidades no banco de dados. Oferece visibilidade sobre o estado de segurança e inclui passos acionáveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- A [proteção avançada contra ameaças](sql-database-threat-detection-overview.md) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar seu banco de dados. Monitoriza continuamente a sua base de dados quanto a atividades suspeitas e apresenta alertas de segurança imediata em potenciais vulnerabilidades, ataques de injeção SQL e padrões de acesso de base de dados anómalos. Alertas de proteção avançada contra ameaças fornecem detalhes da atividade suspeita e recomendam a ação de como investigar e mitigar a ameaça.

Habilite anúncios do SQL uma vez para habilitar todos esses recursos incluídos. Com um clique, você pode habilitar anúncios para todos os bancos de dados em seu servidor de banco de dados SQL ou instância gerenciada. Habilitar ou gerenciar as configurações de anúncios requer que pertençam à função do [Gerenciador de segurança do SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) , função de administrador do banco de dados SQL ou função de administrador do SQL Server. 

Os preços do ADS se alinham com a camada Standard da central de segurança do Azure, em que cada servidor de banco de dados SQL protegido ou instância gerenciada é contado como um nó. Recursos recentemente protegidos se qualificam para uma avaliação gratuita da camada Standard da central de segurança. Para obter mais informações, consulte a [página de preços da central de segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Introdução com anúncios

As etapas a seguir o ajudarão a começar a usar o ADS.

## <a name="1-enable-ads"></a>1. habilitar anúncios

Habilite anúncios navegando até **segurança de dados avançada** no título de **segurança** do servidor do banco de dados SQL ou da instância gerenciada. Para habilitar anúncios para todos os bancos de dados no servidor de banco de dados ou instância gerenciada, clique em **habilitar segurança de dados avançada no servidor**.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da verificação de **avaliação de vulnerabilidade** . Se você já tiver habilitado o ADS para outro servidor no mesmo grupo de recursos e região, a conta de armazenamento existente será usada.

![Habilitar anúncios](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> O custo dos anúncios é alinhado com o preço da camada Standard da central de segurança do Azure por nó, em que um nó é o servidor do banco de dados SQL inteiro ou a instância gerenciada. Portanto, você está pagando apenas uma vez para proteger todos os bancos de dados no servidor de banco de dados ou instância gerenciada com anúncios. Você pode experimentar anúncios inicialmente com uma avaliação gratuita.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. iniciar a classificação de dados, o controle de vulnerabilidades e a investigação de alertas de ameaça

Clique no cartão de **classificação & de descoberta de dados** para ver as colunas confidenciais recomendadas para classificar e classificar seus dados com rótulos de sensibilidade persistentes. Clique no cartão de **avaliação de vulnerabilidade** para exibir e gerenciar análises de vulnerabilidades e relatórios e para acompanhar o estatura de segurança. Se alertas de segurança tiverem sido recebidos, clique no cartão **proteção avançada contra ameaças** para exibir detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas em sua assinatura do Azure por meio da página alertas de segurança da central de segurança do Azure.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. gerenciar as configurações de anúncios no servidor do banco de dados SQL ou na instância gerenciada

Para exibir e gerenciar as configurações do ADS, navegue até **segurança de dados avançada** no título de **segurança** do servidor do banco de dados SQL ou da instância gerenciada. Nessa página, você pode habilitar ou desabilitar anúncios e modificar as configurações de avaliação de vulnerabilidade e proteção avançada contra ameaças para todo o servidor do banco de dados SQL ou instância gerenciada.

![Configurações do servidor](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. gerenciar configurações de anúncios para um banco de dados SQL

Para substituir as configurações de anúncios de um banco de dados específico, marque a caixa de seleção **habilitar segurança de dados avançada no nível do banco de dado** . Use esta opção somente se você tiver um requisito específico para receber alertas separados de proteção avançada contra ameaças ou resultados de avaliação de vulnerabilidade para o banco de dados individual, no lugar do ou além dos alertas e resultados recebidos para todos os bancos de dado no servidor de banco de dados ou instância gerenciada.

Depois que a caixa de seleção for selecionada, você poderá definir as configurações relevantes para esse banco de dados.
 
![Configurações de proteção avançada contra ameaças e banco de dados](./media/sql-advanced-protection/database_threat_detection_settings.png) 

As configurações de segurança de dados avançadas para seu servidor de banco de dado ou instância gerenciada também podem ser acessadas no painel de banco de dados ADS. Clique em **configurações** no painel ADS principal e clique em **exibir configurações avançadas do servidor de segurança de dados**. 

![Configurações do banco de dados](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Passos seguintes 

- Saiba mais sobre a [classificação de & de descoberta de dados](sql-database-data-discovery-and-classification.md) 
- Saiba mais sobre a [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) 
- Saiba mais sobre a [proteção avançada contra ameaças](sql-database-threat-detection.md)
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
