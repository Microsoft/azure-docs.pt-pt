---
title: Proteção avançada de ameaças para o Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece encriptação de dados em repouso e como é implementado.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 0b37f9be5eb3313cc451e65f90d7f2713c68806d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845989"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Proteção avançada de ameaças para O Cosmos Db (Pré-visualização)

Advanced Threat Protection for Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas Do BD do Azure Cosmos. Esta camada de proteção permite-lhe lidar com ameaças, mesmo sem ser um perito em segurança, e integrá-las com sistemas centrais de monitorização de segurança.

Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail para administradores de subscrição, com detalhes da atividade suspeita e recomendações sobre como investigar e remediar as ameaças.

> [!NOTE]
>
> * Advanced Threat Protection for Azure Cosmos DB está atualmente disponível apenas para a API SQL.
> * A Proteção Avançada de Ameaças para o Azure Cosmos DB não está atualmente disponível nas regiões do governo de Azure e das nuvens soberanas.

Para uma experiência de investigação completa dos alertas de segurança, recomendamos permitir o [registo de diagnóstico no Azure Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/logging)que regista operações na própria base de dados, incluindo operações de CRUD em todos os documentos, contentores e bases de dados.

## <a name="threat-types"></a>Tipos de ameaças

Advanced Threat Protection for Azure Cosmos DB deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. Atualmente pode desencadear os seguintes alertas:

- Acesso a **partir de locais incomuns**: Este alerta é desencadeado quando há uma alteração no padrão de acesso a uma conta Azure Cosmos, onde alguém se ligou ao ponto final do Azure Cosmos DB a partir de uma localização geográfica incomum. Em alguns casos, o alerta deteta uma ação legítima, ou seja, uma nova aplicação ou operação de manutenção do desenvolvedor. Noutros casos, o alerta deteta uma ação maliciosa de um ex-funcionário, agressor externo, etc.

- **Extração invulgar de dados**: Este alerta é desencadeado quando um cliente está a extrair uma quantidade invulgar de dados de uma conta Azure Cosmos DB. Este pode ser o sintoma de alguma exfiltração de dados realizada para transferir todos os dados armazenados na conta para uma loja de dados externo.



## <a name="configure-advanced-threat-protection"></a>Configurar o Advanced Threat Protection

Pode configurar a proteção avançada contra ameaças de várias formas, descritas nas seguintes secções.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Lance o portal Azure em [https://portal.azure.com](https://portal.azure.com/) .

2. A partir da conta Azure Cosmos DB, a partir do menu **Definições,** **selecione Advanced security**.

    ![Configurar ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Na lâmina de configuração **de segurança avançada:**

    * Clique na opção **de Proteção de Ameaças Avançadas** para defini-la para **ON**.
    * Clique em **Guardar** para guardar a política do Advanced Threat Protection nova ou atualizada.   

### <a name="rest-api"></a>[API REST](#tab/rest-api)

Utilize comandos Rest API para criar, atualizar ou obter a definição avançada de proteção contra ameaças para uma conta Específica do Azure Cosmos DB.

* [Proteção avançada de ameaças - Criar](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Proteção avançada de ameaças - Obter](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize os seguintes cmdlets PowerShell:

* [Ativar o Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obter Proteção Avançada de Ameaças](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Deficiente Proteção avançada de ameaças](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="arm-template"></a>[Modelo ARM](#tab/arm-template)

Utilize um modelo de Gestor de Recursos Azure (ARM) para configurar cosmos DB com proteção de ameaça avançada ativada.
Para mais informações, consulte [Criar uma Conta CosmosDB com Proteção avançada de Ameaças.](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma Política Azure para permitir a Proteção Avançada de Ameaças para cosmos DB.

1. Lance a página Azure **Policy - Definições** e procure a implementação da política **de proteção de ameaças avançadas para a política de DB cosmos.**

    ![Política de Pesquisa](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Clique na **implementação de proteção avançada de ameaças para** a política cosmosDB e, em seguida, clique em **Atribuir**.

    ![Selecione Subscrição ou Grupo](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. A partir do campo **Scope,** clique nos três pontos, selecione uma subscrição Azure ou grupo de recursos e, em seguida, clique em **Select**.

    ![Página definições de políticas](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Introduza os outros parâmetros e clique em **Atribuir**.




## <a name="manage-atp-security-alerts"></a>Gerir alertas de segurança ATP

Quando ocorrem anomalias de atividade da Azure Cosmos DB, um alerta de segurança é desencadeado com informações sobre o evento de segurança suspeito. 

 A partir do Azure Security Center, pode rever e gerir os seus [alertas](../security-center/security-center-alerts-overview.md)de segurança atuais.  Clique num alerta específico no Centro de [Segurança](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para ver possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial. A imagem que se segue mostra um exemplo de detalhes de alerta fornecidos no Centro de Segurança.

 ![Detalhes da ameaça](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Uma notificação por e-mail também é enviada com os detalhes do alerta e ações recomendadas. A imagem que se segue mostra um exemplo de um e-mail de alerta.

 ![Detalhes do alerta](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Alertas ATP cosmos DB

 Para ver uma lista dos alertas gerados ao monitorizar as contas do Azure Cosmos DB, consulte a secção de [alertas Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) na documentação do Centro de Segurança Azure.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [diagnóstico de exploração madeireira em Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Saiba mais sobre [o Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
