---
title: Utilizar o PowerShell para carregar o Centro de segurança do Azure e a proteger sua rede | Documentos da Microsoft
description: Este documento descreve o processo de integração do Azure do Centro de segurança utilizando cmdlets do PowerShell.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2018
ms.author: rkarlin
ms.openlocfilehash: 73043680ea7b8b63a329d0a457449b635b7b80f2
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223755"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatizar a inclusão do Centro de segurança do Azure com o PowerShell

Pode proteger cargas de trabalho do Azure através de programação, utilizando o módulo do PowerShell do Centro de segurança do Azure.
Com o PowerShell permite-lhe automatizar tarefas e evitar o erro humano inerente a tarefas manuais. Isso é especialmente útil em Implantações em grande escala que envolvam dezenas de subscrições com centenas de milhares de recursos – todos os quais tem de estar protegidos desde o início.

Centro de segurança do Azure do integração com o PowerShell permite-lhe automatizar a gestão dos seus recursos do Azure e de integração e adicionar os controles de segurança necessários através de programação.

Este artigo fornece um script do PowerShell de exemplo que pode ser modificado e utilizado no seu ambiente para implementar o Centro de segurança nas suas subscrições. 

Neste exemplo, vamos ativar o Centro de segurança de uma subscrição com o ID: d07c0080-170c-4c24-861d-9c817742786c e aplicar as definições recomendadas, que fornecem um elevado nível de proteção, implementando o escalão Standard do Centro de segurança, que fornece capacidades de deteção e proteção avançada contra ameaças:

1. Definir o [nível padrão do ASC de proteção](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Defina a área de trabalho do Log Analytics para o qual o agente de monitorização da Microsoft irá enviar os dados que coleta nas VMs associadas à subscrição – neste exemplo, uma área de trabalho de definidas pelo utilizador existentes (myWorkspace).

3. Ativar o automática do agente Centro de segurança de aprovisionamento que [implementa o Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Definir da organização [CISO como o contacto de segurança para os alertas ASC e eventos notáveis](security-center-provide-security-contact-details.md).

6. Centro de segurança a atribuir [predefinido de políticas de segurança](tutorial-security-policy.md).

## <a name="prerequisites"></a>Pré-requisitos

Estes passos devem ser efetuados antes de executar os cmdlets do Centro de segurança:

1.  Executar o PowerShell como administrador.
2.  Execute os seguintes comandos do PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Integrar o Centro de segurança com o PowerShell

1.  Registe as suas subscrições para o fornecedor de recursos do Centro de segurança:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Opcional: Definir o nível de cobertura (escalão de preço) das subscrições (se não definido, o escalão de preço é definido para gratuito):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Configure uma área de trabalho do Log Analytics para o qual os agentes irão comunicar. Tem de ter uma área de trabalho do Log Analytics que já criou, que VMs a subscrição irão reportar. Pode definir várias subscrições para reportar a mesma área de trabalho. Se não definido, será utilizada a área de trabalho predefinida.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Instalação de aprovisionamento automático do Microsoft Monitoring Agent nas suas VMs do Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Recomenda-se para ativar o aprovisionamento automático para se certificar de que as máquinas virtuais do Azure são automaticamente protegidas pelo centro de segurança do Azure.
    >

5.  Opcional: É altamente recomendável que definir os detalhes de contacto de segurança para as subscrições sua integração, que será utilizada como os destinatários de alertas e notificações geradas pelo centro de segurança:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Atribua a iniciativa de política do Centro de segurança predefinida:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Agora com êxito integrado do Azure Security Center com o PowerShell!

Agora, pode utilizar estes cmdlets do PowerShell com scripts de automatização para iterar por meio de programação em subscrições e recursos. Isto poupa tempo e reduz a probabilidade de erro humano. Pode utilizá-lo [script de exemplo](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) como referência.






## <a name="see-also"></a>Consulte também
Para saber mais sobre como pode utilizar o PowerShell para automatizar a adesão ao centro de segurança, consulte o artigo seguinte:

* [Az.Security](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Security/Commands.Security/help/Az.Security.md).

Para saber mais sobre o Centro de segurança, consulte o artigo seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
