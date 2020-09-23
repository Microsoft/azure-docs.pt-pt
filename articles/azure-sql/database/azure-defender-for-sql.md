---
title: Azure Defender para SQL
description: Saiba mais sobre a funcionalidade de gestão das vulnerabilidades da sua base de dados e deteta atividades anómalas que possam indicar uma ameaça à sua base de dados na Base de Dados Azure SQL, Azure SQL Managed Instance ou Azure Synapse.
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
ms.date: 09/21/2020
ms.openlocfilehash: b789cd423d2260ce709a02ffb3ac5ea500997609
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938627"
---
# <a name="azure-defender-for-sql"></a>Azure Defender para SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


O Azure Defender for SQL é um pacote unificado para capacidades avançadas de segurança SQL. O Azure Defender está disponível para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics. Inclui a funcionalidade para detetar e classificar dados confidenciais, analisar e mitigar potenciais vulnerabilidades da sua base de dados e detetar atividades anómalas que podem indicar uma ameaça à sua base de dados. Oferece uma localização única para ativar e gerir estas capacidades.

## <a name="overview"></a>Descrição geral

O Azure Defender fornece um conjunto de capacidades avançadas de segurança SQL, incluindo avaliação de vulnerabilidade SQL e Proteção Avançada de Ameaças.
- [A Avaliação de Vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, rastrear e ajudar a corrigir potenciais vulnerabilidades de base de dados. Ele fornece visibilidade para o seu estado de segurança, e inclui medidas accuíveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- A [Advanced Threat Protection](threat-detection-overview.md) deteta atividades anómalas indicadoras de tentativas potencialmente perigosas e invulgares de acesso ou exploração da sua base de dados. Monitoriza continuamente a sua base de dados para atividades suspeitas, e fornece alertas de segurança imediatos sobre potenciais vulnerabilidades, ataques de injeção Azure SQL e padrões anómalos de acesso à base de dados. Os alertas do Advanced Threat Protection fornecem detalhes sobre a atividade suspeita e recomendam ações de investigação e mitigação da ameaça.

Ativar o Azure Defender para o SQL uma vez para ativar todas estas funcionalidades incluídas. Com um clique, pode ativar o Azure Defender para todas as bases de dados do seu [servidor](logical-servers.md) em Azure ou na sua SQL Managed Instance. Ativar ou gerir as definições do Azure Defender requer pertencer à função [de gestor de segurança SQL,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ou a uma das funções de administração da base de dados ou do servidor.

Para obter mais informações sobre o Azure Defender para preços SQL, consulte a página de preços do [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Começando com Azure Defender

Os passos seguintes começam com o Azure Defender.

## <a name="1-enable-azure-defender"></a>1. Ativar o Defensor Azure

O Azure Defender pode ser acedido através do [portal Azure](https://portal.azure.com). Ativar o Azure Defender navegando para o **Centro de Segurança** sob o título de **Segurança** para o seu servidor ou caso gerido.

> [!NOTE]
> Uma conta de armazenamento é criada automaticamente e configurada para armazenar os resultados da sua **avaliação de vulnerabilidade.** Se já ativou o Azure Defender para outro servidor no mesmo grupo de recursos e região, então a conta de armazenamento existente é utilizada.
>
> O custo do Azure Defender está alinhado com o preço de nível padrão do Azure Security Center por nó, onde um nó é todo o servidor ou instância gerida. Está assim a pagar apenas uma vez por proteger todas as bases de dados do servidor ou por exemplo gerido com o Azure Defender. Pode experimentar o Azure Defender inicialmente com um teste gratuito.

## <a name="2-start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Comece a rastrear vulnerabilidades e a investigar alertas de ameaças

Clique no cartão **de Avaliação de Vulnerabilidade** para visualizar e gerir verificações de vulnerabilidades e relatórios e para rastrear a sua estatura de segurança. Se os alertas de segurança tiverem sido recebidos, clique no cartão **de Proteção de Ameaças Avançadas** para ver os detalhes dos alertas e para ver um relatório consolidado sobre todos os alertas na sua assinatura Azure através da página de alertas de segurança do Azure Security Center.

## <a name="3-manage-azure-defender-settings"></a>3. Gerir as definições do Azure Defender

Para visualizar e gerir as definições do Azure Defender, navegue para o **Centro de Segurança** sob o título de **Segurança** para o seu servidor ou caso gerido. Nesta página, pode ativar ou desativar o Azure Defender e modificar a avaliação de vulnerabilidades e as definições de Proteção de Ameaças Avançadas para todo o seu servidor ou instância gerida.

## <a name="4-manage-azure-defender-settings-for-a-database"></a>4. Gerir as definições do Azure Defender para uma base de dados

Para anular as definições do Azure Defender para uma determinada base de dados, verifique o **Enable Azure Defender para SQL na caixa de verificação de nível de base de dados.** Utilize esta opção apenas se tiver um requisito específico para receber alertas separados de Proteção de Ameaças Avançadas ou resultados de avaliação de vulnerabilidade para cada base de dados, em vez dos alertas e resultados recebidos para todas as bases de dados do servidor ou caso gerido.

Uma vez selecionada a caixa de verificação, pode configurar as definições relevantes para esta base de dados.

O Azure Defender para as definições DE SQL para o seu servidor ou instância gerida também pode ser alcançado a partir do painel de base de dados do Azure Defender. Clique em **Definições** no painel principal do Azure Defender e, em seguida, clique em **Ver Defender Azure para as definições do servidor SQL**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a avaliação da vulnerabilidade](sql-vulnerability-assessment.md)
- Saiba mais sobre [a Proteção Avançada de Ameaças](threat-detection-configure.md)
- Saiba mais sobre [o Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
