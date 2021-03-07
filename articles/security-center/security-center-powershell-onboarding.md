---
title: A bordo do Centro de Segurança Azure com PowerShell
description: Este documento acompanha-o através do processo de ativação do Centro de Segurança Azure com cmdlets PowerShell.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 25639509cfb689e025ea4388f302378648af8ba6
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439498"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizar o embarque no Azure Security Center usando o PowerShell

Pode fixar as suas cargas de trabalho Azure programáticamente, utilizando o módulo PowerShell do Centro de Segurança Azure.
A utilização do PowerShell permite-lhe automatizar tarefas e evitar o erro humano inerente a tarefas manuais. Isto é especialmente útil em implantações em larga escala que envolvem dezenas de subscrições com centenas e milhares de recursos – todos os quais devem ser garantidos desde o início.

O Centro de Segurança Azure que utiliza o PowerShell permite-lhe automatizar programáticamente o embarque e a gestão dos seus recursos Azure e adicionar os controlos de segurança necessários.

Este artigo fornece uma amostra do script PowerShell que pode ser modificada e usada no seu ambiente para lançar o Security Center através das suas subscrições. 

Neste exemplo, permitiremos ao Centro de Segurança uma subscrição com ID: d07c0080-170c-4c24-861d-9c817742786c e aplicaremos as definições recomendadas que proporcionam um elevado nível de proteção, permitindo ao Azure Defender, que fornece capacidades avançadas de proteção e deteção de ameaças:

1. Ativar [o Azure Defender](azure-defender.md). 
 
2. Desabroque o espaço de trabalho Log Analytics para o qual o agente Log Analytics enviará os dados que recolhe nos VMs associados à subscrição – neste exemplo, um espaço de trabalho definido pelo utilizador existente (myWorkspace).

3. Ativar o fornecimento automático do agente do Security Center que [implementa o agente Log Analytics](security-center-enable-data-collection.md#auto-provision-mma).

5. Desa ciso da organização [como o contacto de segurança para alertas do Centro de Segurança e eventos notáveis](security-center-provide-security-contact-details.md).

6. Atribua as políticas de [segurança padrão](tutorial-security-policy.md)do Centro de Segurança.

## <a name="prerequisites"></a>Pré-requisitos

Estes passos devem ser realizados antes de executar os cmdlets do Centro de Segurança:

1. Executar PowerShell como administrador.

1. Executar os seguintes comandos em PowerShell:
      
    ```Set-ExecutionPolicy -ExecutionPolicy AllSigned```

    ```Install-Module -Name Az.Security -Force```

## <a name="onboard-security-center-using-powershell"></a>Centro de Segurança a bordo usando PowerShell

1. Registe as suas subscrições no Fornecedor de Recursos do Centro de Segurança:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'```

1. Opcional: Desconfiem do nível de cobertura (Azure Defender on/off) das subscrições. Se não for indefinida, o Defender está desligado:

    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```

    ```Set-AzSecurityPricing -Name "default" -PricingTier "Standard"```

1. Configure um espaço de trabalho Log Analytics ao qual os agentes irão reportar. Você deve ter um espaço de trabalho Log Analytics que você já criou, que os VMs da subscrição reportarão. Pode definir várias subscrições para reportar ao mesmo espaço de trabalho. Se não estiver definido, o espaço de trabalho predefinido será utilizado.

    ```Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"```

1. Instalação automática do agente Log Analytics nos seus VMs Azure:
    
    ```Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"```
    
    ```Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision```

    > [!NOTE]
    > Recomenda-se que o fornecimento automático se certifique de que as suas máquinas virtuais Azure estão automaticamente protegidas pelo Azure Security Center.
    >

1. Opcional: É altamente recomendado que defina os dados de contacto de segurança para as subscrições que está a bordo, que serão utilizados como destinatários de alertas e notificações geradas pelo Security Center:

    ```Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert```

1. Atribua a iniciativa de política do Centro de Segurança predefinido:

    ```Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'```

    ```$Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'```

Conseguiu embarcar no Centro de Segurança Azure com a PowerShell.

Agora pode utilizar estes cmdlets PowerShell com scripts de automação para iterar programáticamente através de subscrições e recursos. Isto poupa tempo e reduz a probabilidade de erro humano. Pode utilizar este [script de amostra](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) como referência.




## <a name="see-also"></a>Ver também
Para saber mais sobre como pode utilizar o PowerShell para automatizar o embarque no Security Center, consulte o seguinte artigo:

* [Az.Security](/powershell/module/az.security)

Para saber mais sobre o Centro de Segurança, consulte o seguinte artigo:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder aos alertas de segurança no Azure Security Center--](security-center-managing-and-responding-alerts.md) Aprender a gerir e responder aos alertas de segurança.