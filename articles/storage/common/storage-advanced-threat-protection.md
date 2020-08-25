---
title: Configure a proteção avançada de ameaças
titleSuffix: Azure Storage
description: Configure a proteção avançada de ameaças para o Azure Storage para detetar anomalias na atividade da conta e ser notificado de tentativas potencialmente nocivas de acesso à sua conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e037607d1f86e6df4d3f5b12e29ba8fde447ebc9
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757936"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Configure a proteção avançada de ameaças para o armazenamento de Azure

A proteção avançada de ameaças para o Azure Storage fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Esta camada de proteção permite-lhe lidar com ameaças sem ser um perito em segurança ou gerir sistemas de monitorização de segurança.

Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail aos administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças.

O serviço ingere registos de recursos de leitura, escrita e eliminação de pedidos para armazenamento blob e para Ficheiros Azure (pré-visualização) para deteção de ameaças. Para investigar os alertas de proteção avançada de ameaças, pode visualizar a atividade de armazenamento relacionada usando o Armazenamento Analytics Logging. Para obter mais informações, consulte **o Configure logging** in [Monitor uma conta de armazenamento no portal Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Disponibilidade

A proteção avançada de ameaças para o Azure Storage está atualmente disponível para armazenamento de Blob, Ficheiros Azure (pré-visualização) e Azure Data Lake Storage Gen2 (pré-visualização). Os tipos de conta que suportam a proteção de ameaças avançadas incluem contas de armazenamento de v2 para fins gerais, blob de bloco e blob. A proteção avançada de ameaças está disponível em todas as nuvens públicas e nuvens do governo dos EUA, mas não em outras regiões de nuvens soberanas ou do Governo Azure.

Contas com espaços hierárquicos habilitados para operações de suporte de armazenamento de data lake usando tanto as APIs de armazenamento de Azure Blob como as APIs de armazenamento de data lake. A ações de ficheiros Azure suportam transações sobre SMB.

Para obter detalhes sobre os preços, incluindo um teste gratuito de 30 dias, consulte a página de preços do [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

A lista que se segue resume a disponibilidade de proteção avançada de ameaças para o armazenamento Azure:

- Estado de libertação:
  - [Armazenamento blob](https://azure.microsoft.com/services/storage/blobs/) (disponibilidade geral)
  - [Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (pré-visualização suporta transações SMB e REST)
  - Azure Data Lake Storage Gen2 (pré-visualização)
- Nuvens:<br>
    nuvens comerciais ✔<br>
    ✔ Gov dos EUA<br>
    ✘ China Gov, Outro Gov

## <a name="set-up-advanced-threat-protection"></a>Criar proteção avançada de ameaças

Pode configurar uma proteção avançada de ameaças de várias formas, descrita nas seguintes secções.

### <a name="azure-security-center"></a>[Centro de Segurança do Azure](#tab/azure-security-center)

Quando subscreve o nível Standard no Azure Security Center, a proteção avançada de ameaças é automaticamente configurada em todas as suas contas de armazenamento. Pode ativar ou desativar a proteção avançada de ameaças para as suas contas de armazenamento sob uma subscrição específica da seguinte forma:

1. Lançar **Centro de Segurança Azure** no portal [Azure](https://portal.azure.com).
1. A partir do menu principal, clique em **Definições de preços &**.
1. Clique na subscrição que pretende ativar ou desativar a proteção contra ameaças para as suas contas de armazenamento.

    ![Selecionar subscrição](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Clique **no nível de preços**.
1. Na **secção de preços Select por** tipo de recurso, na linha de contas de **armazenamento,** clique em **Ativação** ou **Desativada**.

    ![Ativar ATP no Centro de Segurança](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Clique em **Guardar**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Lançar o [portal Azure](https://portal.azure.com/).
1. Navegue para a sua conta de Armazenamento Azure. Em **Definições**, selecione **Segurança Avançada**.
1. Selecione o link **Definições** na página de configuração de segurança avançada.
1. Desa estava a **segurança avançada** para **ON**.
1. Clique em **Guardar** para guardar a nova ou atualizada política.

    ![Ligue a proteção avançada de ameaças do Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="template"></a>[Modelo](#tab/template)

Utilize um modelo de Gestor de Recursos Azure para implementar uma conta de Armazenamento Azure com uma proteção avançada de ameaças ativada. Para obter mais informações, consulte [a conta de Armazenamento com uma proteção avançada de ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma Política Azure para permitir uma proteção avançada de ameaças através de contas de armazenamento sob uma subscrição específica ou grupo de recursos.

1. Lançar a página Azure **Policy - Definições.**

1. Procure a política **de Proteção de Ameaças Avançadas de Implantação nas Contas de Armazenamento.**

     ![Política de Pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selecione uma subscrição ou grupo de recursos Azure.

    ![Selecione subscrição ou grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribua a apólice.

    ![Página de Definições de Política](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[API REST](#tab/rest-api)

Utilize comandos de API de repouso para criar, atualizar ou obter a definição avançada de proteção contra ameaças para uma conta de armazenamento específica.

* [Proteção avançada de ameaças - Criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada de ameaças - Obter](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize os seguintes cmdlets PowerShell:

* [Permitir proteção avançada de ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Obtenha proteção avançada de ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Desativar a proteção de ameaças avançadas](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Explore anomalias de segurança

Quando ocorrem anomalias na atividade de armazenamento, recebe uma notificação por e-mail com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* O nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As causas potenciais
* Os passos de investigação
* Os passos de reparação

O e-mail também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial.

![Azure Storage e-mail avançado de alerta de proteção de ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Pode rever e gerir os seus atuais alertas de segurança a partir do azulejo de [alertas](../../security-center/security-center-managing-and-responding-alerts.md)de segurança do Azure Security Center . Clicar num alerta específico fornece detalhes e ações para investigar a ameaça atual e abordar ameaças futuras.

![Azure Storage e-mail avançado de alerta de proteção de ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alertas de segurança

Os alertas são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Para obter uma lista de alertas para o Armazenamento Azure, consulte a secção **de Armazenamento** na [proteção de ameaças para serviços de dados no Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [Registos em contas de Armazenamento Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Saiba mais sobre [o Azure Security Center](../../security-center/security-center-intro.md)
