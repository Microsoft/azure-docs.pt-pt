---
title: Azure Defender para SQL
description: Saiba mais sobre a funcionalidade de gestão das vulnerabilidades da sua base de dados e deteta atividades anómalas que possam indicar uma ameaça à sua base de dados na Base de Dados Azure SQL, Azure SQL Managed Instance ou Azure Synapse.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452322"
---
# <a name="azure-defender-for-sql"></a>Azure Defender para SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

O Azure Defender for SQL é um pacote unificado para capacidades avançadas de segurança SQL. O Azure Defender está disponível para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics. Inclui a funcionalidade para detetar e classificar dados confidenciais, analisar e mitigar potenciais vulnerabilidades da sua base de dados e detetar atividades anómalas que podem indicar uma ameaça à sua base de dados. Oferece uma localização única para ativar e gerir estas capacidades.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quais são os benefícios do Azure Defender para a SQL?

O Azure Defender fornece um conjunto de capacidades avançadas de segurança SQL, incluindo avaliação de vulnerabilidade SQL e Proteção Avançada de Ameaças.
- [A Avaliação de Vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, rastrear e ajudar a corrigir potenciais vulnerabilidades de base de dados. Ele fornece visibilidade para o seu estado de segurança, e inclui medidas accuíveis para resolver problemas de segurança e melhorar as fortificações da sua base de dados.
- A [Advanced Threat Protection](threat-detection-overview.md) deteta atividades anómalas indicadoras de tentativas potencialmente perigosas e invulgares de acesso ou exploração da sua base de dados. Monitoriza continuamente a sua base de dados para atividades suspeitas, e fornece alertas de segurança imediatos sobre potenciais vulnerabilidades, ataques de injeção Azure SQL e padrões anómalos de acesso à base de dados. Os alertas do Advanced Threat Protection fornecem detalhes sobre a atividade suspeita e recomendam ações de investigação e mitigação da ameaça.

Ative uma vez o Azure Defender para SQL para ativar todas estas funcionalidades incluídas. Com um clique, pode ativar o Azure Defender para todas as bases de dados do seu [servidor](logical-servers.md) em Azure ou na sua SQL Managed Instance. Ativar ou gerir as definições do Azure Defender requer pertencer à função [de gestor de segurança SQL,](../../role-based-access-control/built-in-roles.md#sql-security-manager) ou a uma das funções de administração da base de dados ou do servidor.

Para obter mais informações sobre o Azure Defender para preços SQL, consulte a página de preços do [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Ativar o Azure Defender 
Existem várias formas de ativar os planos do Azure Defender. Pode ative-lo ao nível da subscrição **(recomendado)** a partir de:

- [Centro de Segurança do Azure](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [Programaticamente com a API REST, Azure CLI, PowerShell ou Azure Policy](#enable-azure-defender-plans-programatically)

Em alternativa, pode ative-lo ao nível de recursos descrito no [Enable Azure Defender for Azure SQL Database ao nível dos recursos](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Ativar o Azure Defender para a Base de Dados Azure SQL ao nível de subscrição do Azure Security Center
Para ativar o Azure Defender para a Base de Dados Azure SQL ao nível da subscrição a partir do Centro de Segurança Azure:

1. A partir do [portal Azure,](https://portal.azure.com) **Abrir Centro de Segurança.**
1. A partir do menu do Security Center, **selecione Preços e configurações**.
1. Selecione a subscrição relevante.
1. Altere a definição do plano para **On**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Habilitação Azure Defender para Azure SQL Database ao nível da subscrição.":::

1. Selecione **Guardar**.


### <a name="enable-azure-defender-plans-programatically"></a>Ativar os planos do Azure Defender programaticamente 

A flexibilidade do Azure permite uma série de métodos programáticos para permitir planos do Azure Defender. 

Utilize qualquer uma das seguintes ferramentas para ativar o Azure Defender para a sua subscrição: 

| Método       | Instruções                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| API REST     | [Preços API](/rest/api/securitycenter/pricings)                                                                                                  |
| CLI do Azure    | [preços de segurança az](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy | [Preços do pacote](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Ativar o Azure Defender para a Base de Dados Azure SQL ao nível dos recursos

Recomendamos que o Azure Defender planeie a nível de subscrição e isso pode ajudar na criação de recursos desprotegidos. No entanto, se tiver uma razão organizacional para ativar o Azure Defender ao nível do servidor, utilize os seguintes passos:

1. A partir do [portal Azure,](https://portal.azure.com)abra o seu servidor ou caso gerido.
1. Sob a direção **de Segurança,** selecione **Security Center**.
1. Selecione **Enable Azure Defender para SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Ativar o Azure Defender para SQL a partir das bases de dados Azure SQL.":::

> [!NOTE]
> Uma conta de armazenamento é criada automaticamente e configurada para armazenar os resultados da sua **avaliação de vulnerabilidade.** Se já ativou o Azure Defender para outro servidor no mesmo grupo de recursos e região, então a conta de armazenamento existente é utilizada.
>
> O custo do Azure Defender está alinhado com o preço de nível padrão do Azure Security Center por nó, onde um nó é todo o servidor ou instância gerida. Está assim a pagar apenas uma vez por proteger todas as bases de dados do servidor ou por exemplo gerido com o Azure Defender. Pode experimentar o Azure Defender inicialmente com um teste gratuito.


## <a name="manage-azure-defender-settings"></a>Gerir as configurações do Azure Defender

Para ver e gerir as definições do Azure Defender:

1. A partir da área de **Segurança** do seu servidor ou instância gerida, selecione **Security Center**.

    Nesta página, você verá o estado de Azure Defender para SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Verificação do estado do Azure Defender para SQL dentro das bases de dados Azure SQL.":::

1. Se o Azure Defender for SQL estiver ativado, verá uma ligação **Configure** como mostrado no gráfico anterior. Para editar as definições de Azure Defender para SQL, selecione **Configure**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Definições para Azure Defender para SQL.":::

1. Faça as alterações necessárias e **selecione Guardar**.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Avaliação de Vulnerabilidades](sql-vulnerability-assessment.md)
- Saiba mais sobre [a Proteção Avançada de Ameaças](threat-detection-configure.md)
- Saiba mais sobre [o Azure Security Center](../../security-center/security-center-introduction.md)