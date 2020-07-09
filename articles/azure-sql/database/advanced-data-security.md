---
title: Segurança de dados avançada
description: Saiba mais sobre a funcionalidade de descoberta e classificação de dados sensíveis, gestão das vulnerabilidades da sua base de dados e deteção de atividades anómalas que possam indicar uma ameaça à sua base de dados na Base de Dados Azure SQL, Azure SQL Managed Instance ou Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 53765ee97f0f253db4df4ecca3c1c90d6068fb07
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85983999"
---
# <a name="advanced-data-security"></a>Segurança de dados avançada
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Advanced Data Security (ADS) é um pacote unificado para capacidades avançadas de segurança SQL. ADS está disponível para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics. Inclui a funcionalidade para detetar e classificar dados confidenciais, analisar e mitigar potenciais vulnerabilidades da sua base de dados e detetar atividades anómalas que podem indicar uma ameaça à sua base de dados. Oferece uma localização única para ativar e gerir estas capacidades.

## <a name="overview"></a>Descrição geral

A ADS fornece um conjunto de capacidades avançadas de segurança SQL, incluindo a Classificação & de Descoberta de Dados, Avaliação de Vulnerabilidades SQL e Proteção Avançada de Ameaças.
- [Data Discovery & Classification](data-discovery-and-classification-overview.md) fornece capacidades incorporadas na Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse para descobrir, classificar, rotular e reportar os dados sensíveis nas suas bases de dados. Pode ser utilizada para oferecer visibilidade sobre o estado de classificação da base de dados e para controlar o acesso aos dados confidenciais na base de dados e além dos respetivos limites.
- [A Avaliação de Vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, rastrear e ajudar a corrigir potenciais vulnerabilidades de base de dados. Ele fornece visibilidade para o seu estado de segurança, e inclui medidas accuíveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- A [Advanced Threat Protection](threat-detection-overview.md) deteta atividades anómalas indicadoras de tentativas potencialmente perigosas e invulgares de acesso ou exploração da sua base de dados. Monitoriza continuamente a sua base de dados para atividades suspeitas, e fornece alertas de segurança imediatos sobre potenciais vulnerabilidades, ataques de injeção Azure SQL e padrões anómalos de acesso à base de dados. Os alertas do Advanced Threat Protection fornecem detalhes sobre a atividade suspeita e recomendam ações de investigação e mitigação da ameaça.

Ativar a Segurança Avançada de Dados uma vez para ativar todas estas funcionalidades incluídas. Com um clique, pode ativar ADS para todas as bases de dados do seu [servidor](logical-servers.md) em Azure ou na sua SQL Managed Instance. Ativar ou gerir as definições de ADS requer pertencer à função [de gestor de segurança SQL,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ou a uma das funções de administração da base de dados ou do servidor.

Os preços dos ADS alinham-se com o nível padrão do Azure Security Center, onde cada servidor protegido ou instância gerida é contado como um nó. Os recursos recentemente protegidos qualificam-se para um teste gratuito do nível padrão do Centro de Segurança. Para mais informações, consulte a [página de preços do Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Começar com a ADS

Os seguintes passos começam com a ADS.

## <a name="1-enable-ads"></a>1. Ativar ADS

Ativar o ADS navegando para a **Advanced Data Security** sob o título de **Segurança** para o seu servidor ou caso gerido.

> [!NOTE]
> Uma conta de armazenamento é criada automaticamente e configurada para armazenar os resultados da sua **avaliação de vulnerabilidade.** Se já ativou o ADS para outro servidor no mesmo grupo de recursos e região, então a conta de armazenamento existente é utilizada.

![Ativar ADS](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> O custo do ADS está alinhado com o preço padrão do Azure Security Center por nó, onde um nó é todo o servidor ou instância gerida. Está, assim, a pagar apenas uma vez por proteger todas as bases de dados do servidor ou por exemplo gerido com ADS. Pode experimentar a ADS inicialmente com um teste gratuito.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Comece a classificar dados, rastrear vulnerabilidades e investigar alertas de ameaças

Clique no cartão **de classificação & Data Discovery** para ver colunas sensíveis recomendadas para classificar e classificar os seus dados com etiquetas de sensibilidade persistentes. Clique no cartão **de Avaliação de Vulnerabilidade** para visualizar e gerir verificações de vulnerabilidades e relatórios e para rastrear a sua estatura de segurança. Se os alertas de segurança tiverem sido recebidos, clique no cartão **de Proteção de Ameaças Avançadas** para ver os detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas na sua assinatura Azure através da página de alertas de segurança do Azure Security Center.

## <a name="3-manage-ads-settings"></a>3. Gerir as definições de ADS

Para visualizar e gerir as definições de ADS, navegue para **Segurança Avançada de Dados** sob o título **de Segurança** para o seu servidor ou instância gerida. Nesta página, pode ativar ou desativar o ADS e modificar a avaliação de vulnerabilidades e as definições de Proteção de Ameaças Avançadas para todo o seu servidor ou instância gerida.

![Definições do servidor](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-database"></a>4. Gerir as definições de ADS para uma base de dados

Para substituir as definições de ADS para uma determinada base de dados, consulte a Segurança avançada de dados na caixa **de verificação do nível da base de dados.** Utilize esta opção apenas se tiver um requisito específico para receber alertas separados de Proteção de Ameaças Avançadas ou resultados de avaliação de vulnerabilidade para cada base de dados, em vez dos alertas e resultados recebidos para todas as bases de dados do servidor ou caso gerido.

Uma vez selecionada a caixa de verificação, pode configurar as definições relevantes para esta base de dados.

![Definições de base de dados e proteção de ameaças avançadas](./media/advanced-data-security/database_threat_detection_settings.png)

As definições avançadas de segurança de dados para o seu servidor ou instância gerida também podem ser alcançadas a partir do painel de base de dados ADS. Clique em **Definições** no painel PRINCIPAL de ADS e, em seguida, clique em **Ver Ver Definições avançadas do servidor de Segurança de Dados**.

![Definições de base de dados](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a Classificação & data Discovery](data-discovery-and-classification-overview.md)
- Saiba mais sobre [a avaliação da vulnerabilidade](sql-vulnerability-assessment.md)
- Saiba mais sobre [a Proteção Avançada de Ameaças](threat-detection-configure.md)
- Saiba mais sobre [o Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
