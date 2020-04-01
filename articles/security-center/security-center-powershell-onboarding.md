---
title: A bordo do Azure Security Center com powerShell
description: Este documento acompanha-o através do processo de embarque no Azure Security Center utilizando cmdlets PowerShell.
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
ms.openlocfilehash: b471fbb62862cd48ebbb239d65b563aa109ef629
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435481"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizar o embarque do Azure Security Center usando o PowerShell

Pode fixar as suas cargas de trabalho Azure programáticamente, utilizando o módulo PowerShell do Centro de Segurança Azure.
A utilização do PowerShell permite-lhe automatizar tarefas e evitar o erro humano inerente às tarefas manuais. Isto é especialmente útil em implementações em larga escala que envolvem dezenas de subscrições com centenas e milhares de recursos – todos os quais devem ser assegurados desde o início.

O Onboarding Azure Security Center utilizando o PowerShell permite-lhe automatizar programaticamente o embarque e a gestão dos seus recursos Azure e adicionar os controlos de segurança necessários.

Este artigo fornece um script PowerShell de amostra que pode ser modificado e usado no seu ambiente para lançar o Security Center através das suas subscrições. 

Neste exemplo, permitiremos o Centro de Segurança numa subscrição com ID: d07c0080-170c-4c24-861d-9c81742786c e aplicaras as definições recomendadas que proporcionam um alto nível de proteção, implementando o nível padrão de Centro de Segurança, que fornece capacidades avançadas de proteção e deteção de ameaças:

1. Desempor o [nível padrão de proteção do Centro de Segurança.](https://azure.microsoft.com/pricing/details/security-center/) 
 
2. Detete o espaço de trabalho do Log Analytics para o qual o agente Log Analytics enviará os dados que recolhe nos VMs associados à subscrição – neste exemplo, um espaço de trabalho definido pelo utilizador existente (myWorkspace).

3. Ativar o fornecimento automático de agentes do Security Center que [implementa o agente Log Analytics](security-center-enable-data-collection.md#auto-provision-mma).

5. Detete o CISO da organização [como o contacto de segurança para alertas do Centro de Segurança e eventos notáveis.](security-center-provide-security-contact-details.md)

6. Atribuir as políticas [de segurança padrão](tutorial-security-policy.md)do Centro de Segurança.

## <a name="prerequisites"></a>Pré-requisitos

Estes passos devem ser realizados antes de executar o Centro de Segurança cmdlets:

1.  Executar powerShell como administrador.
2.  Executar os seguintes comandos no PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Centro de Segurança a bordo usando PowerShell

1.  Registe as suas assinaturas no Fornecedor de Recursos do Centro de Segurança:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Opcional: Definir o nível de cobertura (nível de preços) das assinaturas (se não definido, o nível de preços é definido para Free):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Configure um espaço de trabalho de Log Analytics ao qual os agentes irão reportar. Você deve ter um espaço de trabalho Log Analytics que você já criou, que os VMs da subscrição irão reportar. Pode definir várias subscrições para reportar ao mesmo espaço de trabalho. Se não for definido, o espaço de trabalho padrão será utilizado.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Instalação automática do agente Log Analytics nos seus VMs Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Recomenda-se que o fornecimento automático garanta que as suas máquinas virtuais Azure estão automaticamente protegidas pelo Azure Security Center.
    >

5.  Opcional: É altamente recomendável que defina os dados de contacto de segurança para as subscrições que você está a bordo, que serão usados como destinatários de alertas e notificações gerados pelo Security Center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Atribuir a iniciativa política padrão do Centro de Segurança:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Agora abordou com sucesso no Azure Security Center com a PowerShell!

Agora pode usar estes cmdlets PowerShell com scripts de automação para iterar programáticamente através de subscrições e recursos. Isto poupa tempo e reduz a probabilidade de erro humano. Pode utilizar este [script de amostra](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) como referência.






## <a name="see-also"></a>Consulte também
Para saber mais sobre como pode usar o PowerShell para automatizar o embarque no Security Center, consulte o seguinte artigo:

* [Az.Security.](https://docs.microsoft.com/powershell/module/az.security)

Para saber mais sobre o Centro de Segurança, consulte o seguinte artigo:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas](security-center-managing-and-responding-alerts.md) de segurança no Centro de Segurança Azure -- Aprenda a gerir e responder a alertas de segurança.