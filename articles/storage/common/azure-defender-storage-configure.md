---
title: Configurar o Azure Defender para Armazenamento
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
ms.openlocfilehash: cdfc54b1eca3b07202148b7099884a04f35939ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698149"
---
# <a name="configure-azure-defender-for-storage"></a>Configurar o Azure Defender para Armazenamento

O Azure Defender para Armazenamento fornece uma camada adicional de informações de segurança que deteta tentativas invulgares e potencialmente nocivas de acesso ou de exploração das contas de armazenamento. Esta camada de proteção permite-lhe resolver ameaças sem ser um especialista em segurança ou gerir sistemas de monitorização de segurança.

Os alertas de segurança são acionados quando ocorrem anomalias de atividade. Estes alertas de segurança estão integrados no [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail aos administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças.

O serviço ingere registos de recursos de leitura, escrita e eliminação de pedidos para armazenamento blob e para ficheiros Azure para deteção de ameaças. Para investigar alertas do Azure Defender, pode visualizar a atividade de armazenamento relacionada usando o Armazenamento Analytics Logging. Para obter mais informações, consulte **o Configure logging** in [Monitor uma conta de armazenamento no portal Azure](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Disponibilidade

Atualmente, o Azure Defender para Armazenamento está disponível para o Armazenamento de blobs, Ficheiros do Azure e Azure Data Lake Storage Gen2. Os tipos de conta que suportam o Azure Defender incluem: fins gerais v2, blob de blocos e Contas de armazenamento de blobs. O Azure Defender para Armazenamento está disponível em todas as clouds públicas e do governo dos EUA, mas não noutras regiões soberanas na cloud do Azure Government.

Contas com os espaços de nomes hierárquicos ativados para transações de suporte do Data Lake Storage que utilizam as APIs de armazenamento de Blobs do Azure e as APIs do Data Lake Storage. O ficheiro do Azure partilha transações de suporte através do SMB.

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
1. A partir do menu principal, em **Gestão,** **selecione definições de preços &**.
1. Selecione a subscrição para a qual deseja ativar ou desativar o Azure Defender.
1. Selecione **Azure Defender** para ativar o Azure Defender para a subscrição.
1. No **plano Select Azure Defender por tipo de recurso,** localize a linha **de armazenamento** e selecione **Ativado** na coluna **Plano.**
1. Guarde as alterações.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Screenshot mostrando como ativar O Azure Defender para armazenamento no Centro de Segurança":::

O Azure Defender encontra-se agora ativado para todas as contas de armazenamento nesta subscrição.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Lançar o [portal Azure](https://portal.azure.com/).
1. Navegue até à sua conta de armazenamento. Em **Definições**, selecione **Segurança avançada**.
1. Selecione **Ativar Azure Defender para Armazenamento**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Screenshot mostrando como ativar o Azure Defender para uma conta de Armazenamento Azure":::

O Azure Defender está agora ativado para esta conta de armazenamento.

### <a name="template"></a>[Modelo](#tab/template)

Utilize um modelo de Gestor de Recursos Azure para implementar uma conta de Armazenamento Azure com o Azure Defender ativado. Para obter mais informações, consulte [a conta de Armazenamento com uma proteção avançada de ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma Política Azure para permitir que o Azure Defender entre contas de armazenamento sob uma subscrição ou grupo de recursos específicos.

1. Lançar a página Azure **Policy - Definições.**
1. Procure a política **de Implementação do Defensor Azure sobre contas de armazenamento.**

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Aplicar a política para permitir contas de Armazenamento do Azure Defender":::

1. Selecione uma subscrição ou grupo de recursos Azure.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Selecione a subscrição ou grupo de recursos para o âmbito de aplicação da política ":::

1. Atribua a apólice.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Atribuir política para permitir o Azure Defender para armazenamento":::

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

## <a name="explore-security-anomalies"></a>Explorar anomalias de segurança

Após a ocorrência de anomalias de atividade de armazenamento, receberá uma notificação de e-mail com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

- O tipo de anomalia
- O nome da conta de armazenamento
- A hora do evento
- O tipo de armazenamento
- Potenciais causas
- Os passos de investigação
- Os passos de remediação

O email também inclui detalhes sobre as possíveis causas e as ações recomendadas para investigar e mitigar a potencial ameaça.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Azure Defender para armazenamento e-mail de alerta":::

Pode rever e gerir os seus atuais alertas de segurança a partir do azulejo de [alertas](../../security-center/security-center-managing-and-responding-alerts.md)de segurança do Azure Security Center . Clicar num alerta específico fornece detalhes e ações para investigar a ameaça atual e resolver ameaças futuras.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Alerta de Azure Defender para armazenamento":::

## <a name="security-alerts"></a>Alertas de segurança

Os alertas são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Para obter uma lista de alertas para o armazenamento Azure, consulte [alertas para o armazenamento Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Registos em contas de Armazenamento Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Saiba mais sobre [o Azure Security Center](../../security-center/security-center-introduction.md)