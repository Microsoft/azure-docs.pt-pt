---
title: Usar o PowerShell para carregar a central de segurança do Azure e proteger sua rede | Microsoft Docs
description: Este documento orienta você pelo processo de integração da central de segurança do Azure usando cmdlets do PowerShell.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 8e2f7b87efe89166175748cec310f24575b7f102
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201216"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizar a integração da central de segurança do Azure usando o PowerShell

Você pode proteger suas cargas de trabalho do Azure programaticamente, usando o módulo PowerShell da central de segurança do Azure.
O uso do PowerShell permite automatizar tarefas e evitar o erro humano inerente às tarefas manuais. Isso é especialmente útil em implantações em larga escala que envolvem dezenas de assinaturas com centenas e milhares de recursos – todos eles devem ser protegidos desde o início.

A integração da central de segurança do Azure usando o PowerShell permite automatizar programaticamente a integração e o gerenciamento de recursos do Azure e adicionar os controles de segurança necessários.

Este artigo fornece um exemplo de script do PowerShell que pode ser modificado e usado em seu ambiente para distribuir a central de segurança em suas assinaturas. 

Neste exemplo, Habilitaremos a central de segurança em uma assinatura com ID: d07c0080-170C-4C24-861d-9c817742786c e aplicaremos as configurações recomendadas que fornecem um alto nível de proteção, implementando a camada Standard da central de segurança, que fornece proteção avançada contra ameaças e recursos de detecção:

1. Defina o [nível de proteção padrão ASC](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Defina o espaço de trabalho Log Analytics ao qual o Microsoft Monitoring Agent enviará os dados coletados pelas VMs associadas à assinatura – neste exemplo, um espaço de trabalho definido pelo usuário existente (MyWorkspace).

3. Ative o provisionamento automático de agente da central de segurança, que [implanta o Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Defina o ciso da organização [como o contato de segurança para alertas ASC e eventos notáveis](security-center-provide-security-contact-details.md).

6. Atribua [as políticas de segurança padrão](tutorial-security-policy.md)da central de segurança.

## <a name="prerequisites"></a>Pré-requisitos

Essas etapas devem ser executadas antes de executar os cmdlets da central de segurança:

1.  Execute o PowerShell como administrador.
2.  Execute os seguintes comandos no PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Central de segurança integrada usando o PowerShell

1.  Registre suas assinaturas no provedor de recursos da central de segurança:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Opcional: Defina o nível de cobertura (tipo de preço) das assinaturas (se não definido, o tipo de preço é definido como livre):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Configure um espaço de trabalho Log Analytics para o qual os agentes serão relatados. Você deve ter um espaço de trabalho Log Analytics que você já criou, que as VMs da assinatura relatarão. Você pode definir várias assinaturas para relatar para o mesmo espaço de trabalho. Se não estiver definido, o espaço de trabalho padrão será usado.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Instalação de provisionamento automático do Microsoft Monitoring Agent em suas VMs do Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > É recomendável habilitar o provisionamento automático para garantir que suas máquinas virtuais do Azure sejam protegidas automaticamente pela central de segurança do Azure.
    >

5.  Opcional: É altamente recomendável que você defina os detalhes de contato de segurança para as assinaturas que você carregar, que serão usadas como os destinatários de alertas e notificações gerados pela central de segurança:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Atribua a iniciativa de política da central de segurança padrão:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Agora você integrou com êxito a central de segurança do Azure com o PowerShell!

Agora você pode usar esses cmdlets do PowerShell com scripts de automação para iterar programaticamente em assinaturas e recursos. Isso poupa tempo e reduz a probabilidade de erro humano. Você pode usar este [script de exemplo](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) como referência.






## <a name="see-also"></a>Consulte também
Para saber mais sobre como você pode usar o PowerShell para automatizar a integração à central de segurança, consulte o seguinte artigo:

* [AZ. Security](https://docs.microsoft.com/powershell/module/az.security).

Para saber mais sobre a central de segurança, consulte o seguinte artigo:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
