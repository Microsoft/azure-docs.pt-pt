---
description: Conheça o histórico da versão para a extensão de Configuração do Estado Desejado (DSC) em Azure.
ms.date: 06/21/2018
keywords: dsc, powershell, azul, extensão
title: Histórico de versão de extensão Azure DSC
author: mgoedtel
ms.author: magoedte
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: 207b8f7467753a8693bbabe6dd3f7a890307cf21
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120707"
---
# <a name="azure-desired-state-configuration-extension-version-history"></a>Histórico de extensão de extensão de configuração do Estado do Azure

A extensão de Configuração do Estado Do Desejo Azure (DSC) vM é atualizada conforme necessário para suportar melhorias e novas capacidades entregues pelo Azure, Windows Server e a Estrutura de Gestão do Windows (WMF) que inclui o Windows PowerShell.

Este artigo fornecerá informações sobre cada versão da Extensão VM Azure DSC, que ambientes suporta, e comentários e comentários sobre novas funcionalidades ou alterações.

## <a name="latest-version"></a>Versão mais recente

### <a name="version-276"></a>Versão 2.76

- **Data de lançamento:**
  - 9 de maio de 2018 (Azure) [ 21 de junho de 2018 (Azure China, Governo Azure)
- **Suporte para os Sistemas Operativos:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Cliente Windows 7/8.1/10
  - Nano Server
- **Suporte wmf:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Atualização WMF 4.0
  - WMF 4.0
- **Ambiente:**
  - Azure
  - Azure China
  - Azure Government
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - Melhoria dos metadados de extensão para o subestatuto e outras correções de erros menores.

## <a name="supported-versions"></a>Versões suportadas

> [!WARNING]
> Versões 2.4 a 2.13 utilizam WMF 5.0 Visualização Pública cujos certificados de assinatura expiraram em agosto
> 2016. Para mais informações sobre este assunto, consulte o [blog post](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Versão 2.75

- **Data de lançamento:** 5 de março de 2018
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - Após a recente mudança do GitHub para TLS 1.2, não pode embarcar um VM para ODSC de Automação Azure usando modelos de Gestor de Recursos DIY disponíveis no Azure Marketplace ou usar extensão DSC para obter qualquer config hospedado no GitHub. Verá um erro semelhante ao seguinte durante a implementação da extensão:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - Na nova versão de extensão, o TLS 1.2 é agora aplicado. Ao implementar a extensão se já tivesse o AutoUpgradeMinorVersion = verdadeiro no modelo de Gestor de Recursos, então a extensão será automaticamente atualizada para 2.75. Para atualizações manuais, especifique `TypeHandlerVersion = 2.75` no seu modelo de Gestor de Recursos.

### <a name="version-270---272"></a>Versão 2.70 - 2.72

- **Data de lançamento:** 13 de novembro de 2017
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - O bug corrige & melhorias que simplificam a utilização da Automatização DSC Azure através do portal UI, bem como do modelo de Gestor de Recursos. Para mais informações, consulte o [Script de Configuração Predefinido](/azure/virtual-machines/extensions/dsc-overview) na documentação de extensão dSC.

### <a name="version-226"></a>Versão 2.26

- **Data de lançamento:** 9 de junho de 2017
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - Melhorias de telemetria.

### <a name="version-225"></a>Versão 2.25

- **Data de lançamento:** 2 de junho de 2017
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - Foram adicionadas várias correções de bugs e outras pequenas melhorias.

### <a name="version-224"></a>Versão 2.24

- **Data de lançamento:** 13 de abril de 2017
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - Expõe o VM UUID & DSC Agent ID como metadados de extensão. Foram acrescentadas outras melhorias menores.

### <a name="version-223"></a>Versão 2.23

- **Data de lançamento:** Março 15, 2017
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - Foram adicionadas muitas correções de bugs e outras melhorias.

### <a name="version-222"></a>Versão 2.22

- **Data de lançamento:** 8 de fevereiro de 2017
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte wmf:** WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (a instalação do WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - A extensão DSC tem agora suporte para WMF 5.1.
  - Foram acrescentadas outras melhorias menores.

### <a name="version-221"></a>Versão 2.21

- **Data de lançamento:** 2 de dezembro de 2016
- **Suporte para os Sistemas Operativos:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte wmf:** Pré-visualização WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC está instalada no VM.
- **Novas funcionalidades:**
  - A extensão DSC já está disponível no Nano Server. Esta versão contém principalmente alterações de código para executar a extensão no Servidor Nano.
  - Foram acrescentadas outras melhorias menores.

### <a name="version-220"></a>Versão 2.20

- **Data de lançamento:** 2 de agosto de 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** Pré-visualização WMF 5.1, WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Suporte para Pré-visualização WMF 5.1. Quando foi publicada pela primeira vez, esta versão foi uma atualização opcional e teve de especificar wmfversion = '5.1PP' nos modelos do Gestor de Recursos para instalar a pré-visualização WMF 5.1.
    Wmfversion = 'mais recente' ainda instala o [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Para mais informações sobre a pré-visualização do WMF 5.1, consulte [este blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Foram acrescentadas outras correções e melhorias menores.

### <a name="version--219"></a>Versão 2.19

- **Data de lançamento:** 3 de junho de 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure, Azure China, Governo Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - A extensão do DSC está agora a bordo da Azure China. Esta versão contém principalmente correções para executar a extensão sobre a China Azure.

### <a name="version-218"></a>Versão 2.18

- **Data de lançamento:** 3 de junho de 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Faça o não bloqueio da telemetria quando ocorrer um erro durante o download de hotfix de telemetria (problema de DNS conhecido do Azure) ou durante a instalação.
  - Corrija para o problema intermitente onde a extensão impede a configuração do processamento após um reboot.
    Isto estava a fazer com que a extensão do DSC permanecesse em estado de "transição".
  - Foram acrescentadas outras correções e melhorias menores.

### <a name="version-217"></a>Versão 2.17

- **Data de lançamento:** 26 de abril de 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** WMF 5.0 RTM, WMF 4.0 Update, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Suporte para Atualização WMF 4.0. Para mais informações sobre wMF 4.0 Update, consulte [este blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Volte a tentar a lógica dos erros ocorridos durante a instalação da extensão DSC ou durante a aplicação de uma instalação de extensão de instalação de instalação de uma instalação de extensão de configuração DSC. Como parte desta alteração, a extensão irá voltar a tentar a instalação se uma instalação anterior falhar ou reencenar uma configuração DSC que já tinha falhado, durante um máximo três vezes até atingir o estado de conclusão (Sucesso/Erro) ou se um novo pedido chegar. Se a extensão falhar devido a configurações inválidas do utilizador/entrada do utilizador, não se retenta. Neste caso, a extensão precisa de ser novamente invocada com um novo pedido e definições corretas do utilizador. Nota: A extensão DSC depende do agente Azure VM para as repetições. O agente Azure VM invoca a extensão com o último pedido falhado até atingir um estado de sucesso ou erro.

### <a name="version-216"></a>Versão 2.16

- **Data de lançamento:** 21 de abril de 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Melhoria no manuseamento de erros e outras correções de erros menores.
  - Nova propriedade em definições de extensão DSC. 'ForcePullAndApply' em AdvancedOptions é adicionado para ativar a extensão DSC deencenar configurações DSC quando o modo de atualização é Pull (em oposição ao modo de impulso predefinido). Para mais informações, consulte [este blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/) para obter mais informações sobre as definições de Extensão DSC.

### <a name="version-215"></a>Versão 2.15

- **Data de lançamento:** Março 14, 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Na versão de extensão 2.14, foram incluídas alterações para instalar o WMF RTM. Ao atualizar a partir da versão de extensão 2.13.2.0 para 2.14.0.0, poderá notar que algumas cmdlets DSC falham ou a sua configuração falha com um erro – 'No Instance found with given property values'. Para mais informações, consulte as [notas de lançamento do DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). As sobras para estas questões foram adicionadas na versão 2.15.
  - Infelizmente, se já instalou a versão 2.14 e está a deparar com um dos dois problemas acima referidos, terá de realizar estes passos manualmente. Numa sessão elevada da PowerShell:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Versão 2.14

- **Data de lançamento:** 25 de fevereiro de 2016
- **Suporte para os Sistemas Operativos:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte wmf:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Azure
- **Observações:** Esta versão utiliza o DSC tal como incluído na Pré-visualização Técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Usa wmf RTM.
  - Permite a recolha de dados de forma a melhorar a qualidade da extensão DSC. Para mais informações, consulte [o blog.](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)
  - Fornece um formato de definições atualizado para a extensão num modelo de Gestor de Recursos. Para mais informações, consulte [o blog.](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)
  - Correções de bugs e outras melhorias.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações sobre o PowerShell DSC, vá ao centro de [documentação PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o modelo do Gestor de [Recursos para a extensão DSC](/azure/virtual-machines/extensions/dsc-template).
- Para obter mais funcionalidades que possa gerir utilizando o PowerShell DSC e para obter mais recursos DSC, navegue na [galeria PowerShell.](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)
- Para mais detalhes sobre a passagem de parâmetros sensíveis em configurações, consulte [Gerir as credenciais de forma segura com o manipulador de extensão DSC](/azure/virtual-machines/extensions/dsc-credentials).
