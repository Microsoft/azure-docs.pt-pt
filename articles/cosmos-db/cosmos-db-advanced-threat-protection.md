---
title: Proteção Avançada de Ameaças para Azure Cosmos DB
description: Saiba como a Azure Cosmos DB fornece encriptação de dados em repouso e como é implementado.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: b73e7f8c13f621bc359a2ae79a725829420a3ecc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455600"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Proteção avançada de ameaças para Azure Cosmos DB (Pré-visualização)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Advanced Threat Protection for Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas DB do Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças, mesmo sem ser um perito em segurança, e integrá-las com sistemas centrais de monitorização de segurança.

Os alertas de segurança são acionados quando ocorrem anomalias de atividade. Estes alertas de segurança estão integrados no  [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail aos administradores de subscrição, com detalhes da atividade suspeita e recomendações sobre como investigar e remediar as ameaças.

> [!NOTE]
>
> * A Proteção Avançada de Ameaças para Azure Cosmos DB está atualmente disponível apenas para a API SQL.
> * A Proteção Avançada de Ameaças para Azure Cosmos DB não está atualmente disponível no governo de Azure e nas regiões de nuvem soberana.

Para uma experiência de investigação completa dos alertas de segurança, recomendamos permitir o registo de [diagnóstico em Azure Cosmos DB,](./monitor-cosmos-db.md)que regista operações na própria base de dados, incluindo operações CRUD em todos os documentos, contentores e bases de dados.

## <a name="threat-types"></a>Tipos de ameaças

A Advanced Threat Protection for Azure Cosmos DB deteta atividades anómalas que indiquem tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados. Atualmente pode desencadear os seguintes alertas:

- **Acesso a partir de locais incomuns**: Este alerta é desencadeado quando há uma alteração no padrão de acesso a uma conta Azure Cosmos, onde alguém se ligou ao ponto final do Azure Cosmos DB a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima, o que significa uma nova aplicação ou operação de manutenção do desenvolvedor. Noutros casos, o alerta deteta uma ação maliciosa de um ex-funcionário, agressor externo, etc.

- **Extração de dados incomum**: Este alerta é desencadeado quando um cliente está a extrair uma quantidade incomum de dados de uma conta DB da Azure Cosmos. Este pode ser o sintoma de alguma exfiltração de dados realizada para transferir todos os dados armazenados na conta para uma loja de dados externo.



## <a name="configure-advanced-threat-protection"></a>Configurar o Advanced Threat Protection

Pode configurar uma proteção avançada de ameaças de várias formas, descrita nas seguintes secções.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Lançar o portal Azure em  [https://portal.azure.com](https://portal.azure.com/) .

2. A partir da conta DB Azure Cosmos, a partir do menu **Definições,** selecione **Segurança Avançada**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="Configurar ATP":::

3. Na lâmina de configuração **avançada de segurança:**

    * Clique na opção **De Proteção de Ameaças Avançadas** para defini-la para **ON**.
    * Clique em **Guardar** para guardar a política do Advanced Threat Protection nova ou atualizada.   

### <a name="rest-api"></a>[API REST](#tab/rest-api)

Utilize comandos de API de repouso para criar, atualizar ou obter a definição de Proteção de Ameaças Avançadas para uma conta DB Azure Cosmos específica.

* [Proteção avançada de ameaças - Criar](/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção Avançada de Ameaças - Obter](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize os seguintes cmdlets PowerShell:

* [Ativar o Advanced Threat Protection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Obter proteção de ameaças avançadas](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Desativar a proteção de ameaças avançadas](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

### <a name="arm-template"></a>[Modelo ARM](#tab/arm-template)

Utilize um modelo Azure Resource Manager (ARM) para configurar o Cosmos DB com a Proteção avançada de ameaças ativada.
Para obter mais informações, consulte [Criar uma conta CosmosDB com Proteção Avançada de Ameaças.](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma política de Azure para permitir a Proteção Avançada de Ameaças para o Cosmos DB.

1. Lançar a página Azure **Policy - Definições** e procurar a política de implementação de ameaças avançadas para a política **de DB do Cosmos.**

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Política de Pesquisa"::: 

1. Clique na política **de Implementação de Proteção de Ameaças Avançadas para CosmosDB** e, em seguida, clique em **Atribuir**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Selecione subscrição ou grupo":::


1. A partir do campo **Scope,** clique nos três pontos, selecione uma subscrição ou grupo de recursos Azure e, em seguida, clique em **Select**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Página de Definições de Política":::


1. Introduza os outros parâmetros e clique em **Atribuir**.




## <a name="manage-atp-security-alerts"></a>Gerir alertas de segurança ATP

Quando ocorrem anomalias de atividade da Azure Cosmos DB, um alerta de segurança é desencadeado com informações sobre o evento de segurança suspeito. 

 A partir do Azure Security Center, pode rever e gerir os seus [atuais alertas de segurança](../security-center/security-center-alerts-overview.md).  Clique num alerta específico no [Centro de Segurança](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para ver possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial. A imagem que se segue mostra um exemplo de detalhes de alerta fornecidos no Centro de Segurança.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Detalhes da ameaça":::

Uma notificação por e-mail também é enviada com os detalhes de alerta e ações recomendadas. A imagem que se segue mostra um exemplo de um e-mail de alerta.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Detalhes do alerta":::

## <a name="cosmos-db-atp-alerts"></a>Alertas ATP do Cosmos DB

 Para ver uma lista dos alertas gerados ao monitorizar as contas DB da Azure Cosmos, consulte a secção [de alertas cosmos DB](../security-center/alerts-reference.md#alerts-azurecosmos) na documentação do Centro de Segurança Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o login de diagnóstico no Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Saiba mais sobre [o Azure Security Center](../security-center/security-center-introduction.md)