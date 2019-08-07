---
title: Advanced Threat Protection para o Armazenamento do Microsoft Azure
description: Configure a proteção avançada contra ameaças do armazenamento do Azure para detectar anomalias na atividade da conta e notificá-lo de tentativas potencialmente prejudiciais de acessar sua conta.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 7d4f36be51591d6be2b4c42eb8a8950ab52a0258
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782567"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection para o Armazenamento do Microsoft Azure

O Advanced Threat Protection para o Armazenamento do Azure proporciona uma camada adicional de inteligência de segurança que deteta tentativas invulgares e potencialmente danosas de acesso ou exploração de contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança. 

Os alertas de segurança são disparados quando ocorrem anomalias na atividade.  Esses alertas de segurança são integrados à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/)e também são enviados por email para administradores de assinatura, com detalhes de atividades suspeitas e recomendações sobre como investigar e corrigir ameaças.

> [!NOTE]
> * A proteção avançada contra ameaças para o armazenamento do Azure está disponível no momento apenas para o armazenamento de BLOBs.
> * Para obter detalhes de preços, incluindo uma avaliação gratuita de 30 dias, consulte a [página de preços da central de segurança do Azure]( https://azure.microsoft.com/pricing/details/security-center/).
> * O recurso ATP para armazenamento do Azure não está disponível atualmente nas regiões Azure governamental e soberanas Cloud.

A proteção avançada contra ameaças para o armazenamento do Azure ingere logs de diagnóstico de solicitações de leitura, gravação e exclusão para o armazenamento de BLOB para detecção de ameaças. Para investigar os alertas da proteção avançada contra ameaças, você pode exibir a atividade de armazenamento relacionada usando o log de Análise de Armazenamento. Para obter mais informações, consulte como [Configurar o log de análise de armazenamento](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Configurar a proteção avançada contra ameaças 

### <a name="using-the-portal"></a>Utilizar o portal

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Navegue até a página de configuração da conta de armazenamento do Azure que você deseja proteger. Na página **configurações** , selecione **proteção avançada contra ameaças**.

3. Na folha configuração da **proteção avançada contra ameaças**
    * Ativar a proteção avançada contra *ameaças*
    * Clique em **salvar** para salvar a política de proteção avançada contra ameaças nova ou atualizada. (Os preços na imagem são apenas para fins de exemplo.)

![Ativar a proteção avançada contra ameaças do armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>O Centro de segurança do Azure

Quando você assina a camada Standard na central de segurança do Azure, a proteção avançada contra ameaças é configurada automaticamente em todas as suas contas de armazenamento. Você pode habilitar ou desabilitar a proteção avançada contra ameaças para suas contas de armazenamento em uma assinatura específica da seguinte maneira:

1. Inicie a **central de segurança do Azure** no [portal do Azure](https://portal.azure.com).
1. No menu principal, clique em **preços & configurações**.
1. Clique na assinatura que você deseja habilitar ou desabilitar a proteção contra ameaças para suas contas de armazenamento.

    ![Selecionar subscrição](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Clique em **tipo de preço**.
1. Na seção **selecionar camada de preços por tipo de recurso** , na **linha contas de armazenamento** , clique em **habilitado** ou **desabilitado**.

    ![Habilitar ATP na central de segurança](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Clique em **Guardar**.

### <a name="using-azure-resource-manager-templates"></a>Usando modelos de Azure Resource Manager

Use um modelo de Azure Resource Manager para implantar uma conta de armazenamento do Azure com a proteção avançada contra ameaças habilitada. Para obter mais informações, consulte [conta de armazenamento com proteção avançada contra ameaças](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Usando Azure Policy

Use um Azure Policy para habilitar a proteção avançada contra ameaças em contas de armazenamento em uma assinatura ou grupo de recursos específico.

1. Inicie a página de **definições de política** do Azure.

1. Procure a política **implantar proteção avançada contra ameaças em contas de armazenamento** .

     ![Política de pesquisa](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Selecione uma assinatura do Azure ou um grupo de recursos.

    ![Selecionar assinatura ou grupo](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Atribua a política.

    ![Página Definições de política](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Com a API REST
Use comandos da API REST para criar, atualizar ou obter a configuração de proteção avançada contra ameaças para uma conta de armazenamento específica.

* [Proteção avançada contra ameaças – criar](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Proteção avançada contra ameaças-obter](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Use os seguintes cmdlets do PowerShell:

  * [Habilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Obtenha proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Desabilitar a proteção avançada contra ameaças](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Explorar anomalias de segurança

Quando ocorrerem anomalias de atividade de armazenamento, você receberá uma notificação por email com informações sobre o evento de segurança suspeito. Os detalhes do evento incluem:

* A natureza da anomalia
* O nome da conta de armazenamento
* A hora do evento
* O tipo de armazenamento
* As possíveis causas 
* As etapas de investigação
* As etapas de correção


O email também inclui detalhes sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial.

![Email de alerta da proteção avançada contra ameaças do armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Você pode examinar e gerenciar seus alertas de segurança atuais no [bloco alertas de segurança](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts)da central de segurança do Azure. Clicar em um alerta específico fornece detalhes e ações para investigar a ameaça atual e tratar futuras ameaças.

![Email de alerta da proteção avançada contra ameaças do armazenamento do Azure](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Alertas de proteção

Os alertas são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Para obter uma lista desses alertas, consulte alertas [do armazenamento do Azure](../../security-center/security-center-alerts-data-services.md#azure-storage)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [logs em contas de armazenamento do Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Saiba mais sobre a [central de segurança do Azure](../../security-center/security-center-intro.md)
