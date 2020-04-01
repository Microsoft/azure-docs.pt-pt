---
title: Configurar a proteção avançada contra ameaças
titleSuffix: Azure Storage
description: Configure a proteção avançada contra ameaças para o Armazenamento Azure para detetar anomalias na atividade da conta e ser notificada de tentativas potencialmente prejudiciais de acesso à sua conta.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 195f4b5057c0e2d644ab44cc4c32e97c8662d36d
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422785"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Configure proteção avançada contra ameaças para armazenamento azure

A proteção avançada contra ameaças para o Armazenamento Azure fornece uma camada adicional de inteligência de segurança que deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Esta camada de proteção permite-lhe lidar com ameaças sem ser um perito em segurança ou gerir sistemas de monitorização de segurança.

Os alertas de segurança são desencadeados quando ocorrem anomalias na atividade. Estes alertas de segurança estão integrados no [Azure Security Center,](https://azure.microsoft.com/services/security-center/)e são também enviados por e-mail para administradores de subscrição, com detalhes de atividades suspeitas e recomendações sobre como investigar e remediar ameaças.

> [!NOTE]
> A proteção avançada contra ameaças para o Armazenamento Azure está atualmente disponível apenas para armazenamento blob. Este serviço está disponível nas nuvens do governo dos EUA, mas nenhuma outra região soberana ou azure nuvem de nuvem. Para obter detalhes sobre preços, incluindo um teste gratuito de 30 dias, consulte a página de preços do Centro de [Segurança Azure](https://azure.microsoft.com/pricing/details/security-center/).

A proteção avançada contra ameaças para o Armazenamento Azure ingere registos de diagnóstico de leitura, escrita e exclusão de pedidos ao armazenamento blob para deteção de ameaças. Para investigar os alertas de proteção avançada contra ameaças, pode visualizar a atividade de armazenamento relacionada utilizando o Storage Analytics Logging. Para mais informações, consulte **A configuração** de registo sessão no Monitor uma conta de [armazenamento no portal Azure](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Criar proteção avançada contra ameaças

Pode configurar a proteção avançada contra ameaças de várias formas, descritas nas seguintes secções.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Lance o [portal Azure.](https://portal.azure.com/)
1. Navegue para a sua conta de Armazenamento Azure. Em **Definições,** selecione **Segurança Avançada**.
1. Selecione o link **Definições** na página de configuração de segurança avançada.
1. Definir **segurança avançada** para **ON**.
1. Clique em **Guardar** para salvar a nova ou atualizada política.

    ![Ligue o Azure Storage avançado proteção contra ameaças](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Centro de Segurança do Azure](#tab/azure-security-center)

Quando subscreve o nível Standard no Azure Security Center, a proteção avançada de ameaças é automaticamente configurada em todas as suas contas de armazenamento. Pode ativar ou desativar a proteção avançada de ameaças para as suas contas de armazenamento sob uma subscrição específica da seguinte forma:

1. Lance o **Azure Security Center** no [portal Azure.](https://portal.azure.com)
1. A partir do menu principal, clique em **Definições de Preços &**.
1. Clique na subscrição que pretende ativar ou desativar a proteção contra ameaças para as suas contas de armazenamento.

    ![Selecionar subscrição](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Clique no **nível de preços**.
1. No **nível** de preços Select por secção de tipo de recurso, na linha de **contas de armazenamento,** clique em **Ativado** ou **Desativado**.

    ![Ativar ATP no Centro de Segurança](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Clique em **Guardar**.

### <a name="template"></a>[Modelo](#tab/template)

Utilize um modelo de Gestor de Recursos Azure para implementar uma conta de Armazenamento Azure com proteção de ameaça avançada ativada. Para mais informações, consulte a [conta de Armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Utilize uma Política Azure para permitir uma proteção avançada contra ameaças através de contas de armazenamento sob um grupo específico de subscrição ou recursos.

1. Lançar a página Política Azure **- Definições.**

1. Procure a implementação da política **de proteção** avançada de ameaças em contas de armazenamento.

     ![Política de Pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selecione um grupo de subscrição ou recursos Azure.

    ![Selecione Subscrição ou Grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribuir a apólice.

    ![Página definições de políticas](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[API REST](#tab/rest-api)

Utilize comandos Rest API para criar, atualizar ou obter a definição avançada de proteção contra ameaças para uma conta de armazenamento específica.

* [Proteção avançada contra ameaças - Criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças - Obter](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize os seguintes cmdlets PowerShell:

* [Permitir a proteção avançada de ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Obter proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Desativar a proteção avançada de ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Explore anomalias de segurança

Quando ocorrerem anomalias na atividade de armazenamento, recebe uma notificação por e-mail com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* O nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As causas potenciais
* As diligências de investigação
* Os passos de reparação

O e-mail também inclui detalhes sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial.

![E-mail avançado de alerta de proteção contra ameaças do Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Pode rever e gerir os seus atuais alertas de segurança a partir do azure Security Center alerta o [azulejo](../../security-center/security-center-managing-and-responding-alerts.md). Clicar num alerta específico fornece detalhes e ações para investigar a ameaça atual e abordar ameaças futuras.

![E-mail avançado de alerta de proteção contra ameaças do Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Alertas de segurança

Os alertas são gerados por tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Para obter uma lista de alertas para o Armazenamento Azure, consulte a secção **de Armazenamento** em [Proteção de Ameaças para serviços](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)de dados no Azure Security Center .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [logs nas contas de Armazenamento Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Saiba mais sobre [o Centro de Segurança Azure](../../security-center/security-center-intro.md)
