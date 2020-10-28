---
title: Configure Azure Defender para armazenamento
titleSuffix: Azure Storage
description: Configure O Azure Defender para armazenamento para detetar anomalias na atividade da conta e ser notificado de tentativas potencialmente nocivas de aceder à sua conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: 0bda32aaab301fe9ed685f0bfd6d4596fab4e5db
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789017"
---
# <a name="configure-azure-defender-for-storage"></a>Configure Azure Defender para armazenamento

O Azure Defender for Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Esta camada de proteção permite-lhe lidar com ameaças sem ser um perito em segurança ou gerir sistemas de monitorização de segurança.

Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail aos administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças.

O serviço ingere registos de recursos de leitura, escrita e eliminação de pedidos para armazenamento blob e para ficheiros Azure para deteção de ameaças. Para investigar alertas do Azure Defender, pode visualizar a atividade de armazenamento relacionada usando o Armazenamento Analytics Logging. Para obter mais informações, consulte **o Configure logging** in [Monitor uma conta de armazenamento no portal Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Disponibilidade

O Azure Defender for Storage está atualmente disponível para armazenamento blob, ficheiros Azure e Azure Data Lake Storage Gen2. Os tipos de conta que suportam o Azure Defender incluem contas de armazenamento de uso geral v2, block blob e Blob. O Azure Defender for Storage está disponível em todas as nuvens públicas e nuvens do governo dos EUA, mas não em outras regiões de nuvens soberanas ou do Governo Azure.

Contas com espaços hierárquicos habilitados para operações de suporte de armazenamento de data lake usando tanto as APIs de armazenamento de Azure Blob como as APIs de armazenamento de data lake. A ações de ficheiros Azure suportam transações sobre SMB.

Para obter detalhes sobre os preços, incluindo um teste gratuito de 30 dias, consulte a página de preços do [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

A lista que se segue resume a disponibilidade do Azure Defender para armazenamento:

- Estado de libertação:
  - [Armazenamento blob](https://azure.microsoft.com/services/storage/blobs/) (disponibilidade geral)
  - [Ficheiros Azure](../files/storage-files-introduction.md) (disponibilidade geral)
  - Azure Data Lake Storage Gen2 (disponibilidade geral)
- Nuvens:<br>
    nuvens comerciais ✔<br>
    ✔ Gov dos EUA<br>
    ✘ China Gov, Outro Gov

## <a name="set-up-azure-defender"></a>Criar Azure Defender

Pode configurar o Azure Defender para armazenamento de várias formas, descritas nas seguintes secções.

### <a name="azure-security-center"></a>[Centro de Segurança do Azure](#tab/azure-security-center)

Quando subscreve o nível Standard no Azure Security Center, o Azure Defender é automaticamente configurado em todas as suas contas de armazenamento. Pode ativar ou desativar o Azure Defender para as suas contas de armazenamento sob uma subscrição específica da seguinte forma:

1. Lançar **Centro de Segurança Azure** no portal [Azure](https://portal.azure.com).
1. A partir do menu principal, em **Gestão,** **selecione definições de preços &** .
1. Selecione a subscrição para a qual deseja ativar ou desativar o Azure Defender.
1. Selecione **Azure Defender** para ativar o Azure Defender para a subscrição.
1. No **plano Select Azure Defender por tipo de recurso,** localize a linha **de armazenamento** e selecione **Ativado** na coluna **Plano.**
1. Guarde as alterações.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

O Azure Defender encontra-se agora ativado para todas as contas de armazenamento nesta subscrição.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Lançar o [portal Azure](https://portal.azure.com/).
1. Navegue até à sua conta de armazenamento. Em **Definições** , selecione **Segurança Avançada** .
1. Selecione **Ative Azure Defender para armazenamento** .

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

O Azure Defender está agora ativado para esta conta de armazenamento.

### <a name="template"></a>[Modelo](#tab/template)

Utilize um modelo de Gestor de Recursos Azure para implementar uma conta de Armazenamento Azure com o Azure Defender ativado. Para obter mais informações, consulte [a conta de Armazenamento com uma proteção avançada de ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma Política Azure para permitir que o Azure Defender entre contas de armazenamento sob uma subscrição ou grupo de recursos específicos.

1. Lançar a página Azure **Policy - Definições.**
1. Procure a política **de Implementação do Defensor Azure sobre contas de armazenamento.**

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

1. Selecione uma subscrição ou grupo de recursos Azure.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

1. Atribua a apólice.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

### <a name="rest-api"></a>[API REST](#tab/rest-api)

Utilize comandos de API de repouso para criar, atualizar ou obter a definição do Azure Defender para uma conta de armazenamento específica.

- [Proteção avançada de ameaças - Criar](/rest/api/securitycenter/advancedthreatprotection/create)
- [Proteção avançada de ameaças - Obter](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize os seguintes cmdlets PowerShell:

- [Permitir proteção avançada de ameaças](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Obtenha proteção avançada de ameaças](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Desativar a proteção de ameaças avançadas](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Explore anomalias de segurança

Quando ocorrem anomalias na atividade de armazenamento, recebe uma notificação por e-mail com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

- A natureza da anomalia
- O nome da conta de armazenamento
- A hora do evento
- O tipo de armazenamento
- As causas potenciais
- Os passos de investigação
- Os passos de reparação

O e-mail também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

Pode rever e gerir os seus atuais alertas de segurança a partir do azulejo de [alertas](../../security-center/security-center-managing-and-responding-alerts.md)de segurança do Azure Security Center . Clicar num alerta específico fornece detalhes e ações para investigar a ameaça atual e abordar ameaças futuras.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

## <a name="security-alerts"></a>Alertas de segurança

Os alertas são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Para obter uma lista de alertas para o armazenamento Azure, consulte [alertas para o armazenamento Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Registos em contas de Armazenamento Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Saiba mais sobre [o Azure Security Center](../../security-center/security-center-introduction.md)