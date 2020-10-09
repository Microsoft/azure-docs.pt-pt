---
title: Proteção Avançada de Ameaças para Azure Cosmos DB
description: Saiba como a Azure Cosmos DB fornece encriptação de dados em repouso e como é implementado.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 0affd1660a88421f6df24bc5ef2e00497dae32a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85119275"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Proteção avançada de ameaças para Azure Cosmos DB (Pré-visualização)

A Advanced Threat Protection for Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas DB do Azure Cosmos. Esta camada de proteção permite-lhe abordar ameaças, mesmo sem ser um perito em segurança, e integrá-las com sistemas centrais de monitorização de segurança.

Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no  [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail aos administradores de subscrição, com detalhes da atividade suspeita e recomendações sobre como investigar e remediar as ameaças.

> [!NOTE]
>
> * A Proteção Avançada de Ameaças para Azure Cosmos DB está atualmente disponível apenas para a API SQL.
> * A Proteção Avançada de Ameaças para Azure Cosmos DB não está atualmente disponível no governo de Azure e nas regiões de nuvem soberana.

Para uma experiência de investigação completa dos alertas de segurança, recomendamos permitir o registo de [diagnóstico em Azure Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/logging)que regista operações na própria base de dados, incluindo operações CRUD em todos os documentos, contentores e bases de dados.

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

* [Proteção avançada de ameaças - Criar](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Proteção Avançada de Ameaças - Obter](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize os seguintes cmdlets PowerShell:

* [Ativar o Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obter proteção de ameaças avançadas](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Desativar a proteção de ameaças avançadas](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="arm-template"></a>[Modelo ARM](#tab/arm-template)

Utilize um modelo Azure Resource Manager (ARM) para configurar o Cosmos DB com a Proteção avançada de ameaças ativada.
Para obter mais informações, consulte [Criar uma conta CosmosDB com Proteção Avançada de Ameaças.](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma política de Azure para permitir a Proteção Avançada de Ameaças para o Cosmos DB.

1. Lançar a página Azure **Policy - Definições** e procurar a política de implementação de ameaças avançadas para a política **de DB do Cosmos.**

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Configurar ATP"::: 

1. Clique na política **de Implementação de Proteção de Ameaças Avançadas para CosmosDB** e, em seguida, clique em **Atribuir**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Configurar ATP":::


1. A partir do campo **Scope,** clique nos três pontos, selecione uma subscrição ou grupo de recursos Azure e, em seguida, clique em **Select**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Configurar ATP":::


1. Introduza os outros parâmetros e clique em **Atribuir**.




## <a name="manage-atp-security-alerts"></a>Gerir alertas de segurança ATP

Quando ocorrem anomalias de atividade da Azure Cosmos DB, um alerta de segurança é desencadeado com informações sobre o evento de segurança suspeito. 

 A partir do Azure Security Center, pode rever e gerir os seus [atuais alertas de segurança](../security-center/security-center-alerts-overview.md).  Clique num alerta específico no [Centro de Segurança](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para ver possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial. A imagem que se segue mostra um exemplo de detalhes de alerta fornecidos no Centro de Segurança.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Configurar ATP":::

Uma notificação por e-mail também é enviada com os detalhes de alerta e ações recomendadas. A imagem que se segue mostra um exemplo de um e-mail de alerta.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Configurar ATP":::

## <a name="cosmos-db-atp-alerts"></a>Alertas ATP do Cosmos DB

 Para ver uma lista dos alertas gerados ao monitorizar as contas DB da Azure Cosmos, consulte a secção [de alertas cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) na documentação do Centro de Segurança Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o login de diagnóstico no Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Saiba mais sobre [o Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
