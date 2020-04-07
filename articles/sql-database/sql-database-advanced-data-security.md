---
title: Segurança de dados avançada
description: Conheça a funcionalidade para descobrir e classificar dados sensíveis, gerir as vulnerabilidades da sua base de dados e detetar atividades anómalas que possam indicar uma ameaça à sua base de dados Azure SQL.
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
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677456"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Segurança avançada de dados para base de dados Azure SQL

A segurança avançada de dados é um pacote unificado para capacidades avançadas de segurança SQL. Inclui a funcionalidade para detetar e classificar dados confidenciais, analisar e mitigar potenciais vulnerabilidades da sua base de dados e detetar atividades anómalas que podem indicar uma ameaça à sua base de dados. Oferece uma localização única para ativar e gerir estas capacidades.

## <a name="overview"></a>Descrição geral

A segurança avançada de dados (ADS) fornece um conjunto de capacidades avançadas de segurança SQL, incluindo a descoberta de dados & classificação, avaliação de vulnerabilidade e Proteção avançada de ameaças.

- [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) fornece capacidades incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular & reportar os dados sensíveis nas suas bases de dados. Pode ser utilizada para oferecer visibilidade sobre o estado de classificação da base de dados e para controlar o acesso aos dados confidenciais na base de dados e além dos respetivos limites.
- A [Avaliação da Vulnerabilidade](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode detetar, controlar e ajudar a corrigir potenciais vulnerabilidades da base de dados. Oferece visibilidade sobre o estado de segurança e inclui passos acionáveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- A [Advanced Threat Protection](sql-database-threat-detection-overview.md) deteta atividades anómalas indicadoras de tentativas potencialmente perigosas e invulgares de acesso ou exploração da sua base de dados. Monitoriza continuamente a sua base de dados quanto a atividades suspeitas e apresenta alertas de segurança imediata em potenciais vulnerabilidades, ataques de injeção SQL e padrões de acesso de base de dados anómalos. Os alertas do Advanced Threat Protection fornecem detalhes sobre a atividade suspeita e recomendam ações de investigação e mitigação da ameaça.

Ative o SQL ADS uma vez para ativar todas estas funcionalidades incluídas. Com um clique, pode ativar o ADS para todas as bases de dados do seu servidor de Base de Dados SQL ou na instância gerida. Ativar ou gerir as definições de ADS requer pertencer à função de gestor de [segurança SQL,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) papel de administração de base de dados SQL ou função de administração de servidor SQL. 

Os preços do ADS alinham-se com o nível padrão do Azure Security Center, onde cada servidor de base de dados SQL protegido ou instância gerida é contado como um nó. Os recursos recentemente protegidos qualificam-se para um teste gratuito do nível padrão do Centro de Segurança. Para mais informações, consulte a página de preços do [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Começar com ADS

Os seguintes passos começam com a ADS.

## <a name="1-enable-ads"></a>1. Ativar anúncios

Ative a ADS navegando para Advanced **Data Security** sob a rubrica **De Segurança** para o seu servidor de base de dados SQL ou instância gerida. Para ativar o ADS para todas as bases de dados no servidor de base de dados ou na instância gerida, clique **em Ativar a Segurança avançada de Dados no servidor**.

> [!NOTE]
> Uma conta de armazenamento é criada e configurada automaticamente para armazenar os resultados da sua avaliação de **vulnerabilidade.** Se já ativou a ADS para outro servidor no mesmo grupo de recursos e região, então a conta de armazenamento existente é utilizada.

![Ativar anúncios](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> O custo do ADS está alinhado com os preços padrão do Azure Security Center por nó, onde um nó é todo o servidor de base de dados SQL ou instância gerida. Está assim a pagar apenas uma vez por proteger todas as bases de dados no servidor de base de dados ou por exemplo gerido com ADS. Pode experimentar a ADS inicialmente com um julgamento gratuito.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Comece a classificar dados, rastrear vulnerabilidades e investigar alertas de ameaças

Clique no cartão **de classificação & Descoberta de Dados** para ver colunas sensíveis recomendadas para classificar e classificar os seus dados com etiquetas de sensibilidade persistentes. Clique no cartão **de Avaliação de Vulnerabilidades** para visualizar e gerir verificações e relatórios de vulnerabilidade e para acompanhar a sua estatura de segurança. Se os alertas de segurança tiverem sido recebidos, clique no cartão avançado de **proteção de ameaças** para ver detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas na sua subscrição Azure através da página de alertas de segurança do Centro de Segurança Azure.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Gerir as definições de ADS no seu servidor de base de dados SQL ou na instância gerida

Para visualizar e gerir as definições de ADS, navegue para **Advanced Data Security** sob a rubrica De **Segurança** para o seu servidor de base de dados SQL ou instância gerida. Nesta página, pode ativar ou desativar o ADS e modificar a avaliação de vulnerabilidade e as definições avançadas de proteção contra ameaças para todo o servidor de base de dados SQL ou instância gerida.

![Definições do servidor](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Gerir as definições de ADS para uma base de dados SQL

Para anular as definições de ADS para uma determinada base de dados, verifique a Segurança avançada de **dados ativada na** caixa de verificação de nível de base de dados. Utilize esta opção apenas se tiver um requisito específico para receber alertas separados de Proteção de Ameaças Avançadas ou resultados de avaliação de vulnerabilidade para a base de dados individual, em vez de ou além dos alertas e resultados recebidos para todas as bases de dados no servidor de base de dados ou na instância gerida.

Uma vez selecionada a caixa de verificação, pode configurar as definições relevantes para esta base de dados.
 
![Bases de dados e definições avançadas de proteção contra ameaças](./media/sql-advanced-protection/database_threat_detection_settings.png) 

As definições avançadas de segurança de dados para o seu servidor de base de dados ou instância gerida também podem ser alcançadas a partir do painel de dados ADS. Clique em **Definições** no painel ADS principal e, em seguida, clique em **Ver definições avançadas do servidor de segurança de dados**. 

![Definições de base de dados](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Passos seguintes 

- Saiba mais sobre a [classificação de & de Descoberta](sql-database-data-discovery-and-classification.md) de Dados 
- Saiba mais sobre [avaliação](sql-vulnerability-assessment.md) de vulnerabilidade 
- Saiba mais sobre [proteção avançada de ameaças](sql-database-threat-detection.md)
- Saiba mais sobre [o Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
