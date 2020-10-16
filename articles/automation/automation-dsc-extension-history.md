---
title: Trabalhe com o histórico da versão da extensão da configuração do estado Azure Desired
description: Este artigo diz como trabalhar com o histórico da versão para a extensão de Configuração do Estado Desejado (DSC) em Azure.
ms.date: 07/22/2020
keywords: dsc, powershell, azul, extensão
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: b45512faf09cfe745023d29d32f89a4432cc3b2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87079762"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Trabalhe com o histórico da versão da extensão da configuração do estado Azure Desired

A extensão VM de Configuração do Estado Azure Desired (DSC) é atualizada conforme necessário para suportar melhorias e novas capacidades entregues pelo Azure, Windows Server e o Windows Management Framework (WMF) que inclui o Windows PowerShell.

Este artigo fornece informações sobre cada versão da Extensão VM Azure DSC, quais os ambientes que suporta, e comentários e observações sobre novas funcionalidades ou alterações.

## <a name="latest-version"></a>Versão mais recente

### <a name="version-280"></a>Versão 2.80

- **Data de lançamento:**
  - 26 de setembro, set-2019 (Azure) 6 de julho de 2020 (Azure China Vianet 21) 20 de julho de 2020 (Governo de Azure)
- **Suporte do SO:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Cliente Windows 7/8.1/10
  - Nano Server
- **Suporte do WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Atualização WMF 4.0
  - WMF 4.0
- **Ambiente:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Observações:** Não estão incluídas novas funcionalidades nesta versão.

### <a name="version-276"></a>Versão 2.76

- **Data de lançamento:**
  - 9 de maio de 2018 (Azure) / 21 de junho de 2018 (Azure China Vianet 21, Governo de Azure)
- **Suporte do SO:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Cliente Windows 7/8.1/10
  - Nano Server
- **Suporte do WMF:**
  - WMF 5.1
  - WMF 5.0 RTM
  - Atualização WMF 4.0
  - WMF 4.0
- **Ambiente:**
  - Azure
  - Azure China Vianet 21
  - Azure Government
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - Melhoria dos metadados de extensão para subestuas e outras correções de erros menores.

## <a name="supported-versions"></a>Versões suportadas

> [!WARNING]
> Versões 2.4 a 2.13 utilização WMF 5.0 Visualização pública cujos certificados de assinatura expiraram em agosto
> 2016. Para mais informações sobre este assunto, consulte [o blog post](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/).

### <a name="version-275"></a>Versão 2.75

- **Data de lançamento:** 5 de março de 2018
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - Após a recente mudança do GitHub para o TLS 1.2, não pode embarcar num VM para Azure Automation DSC utilizando modelos de Gestor de Recursos DIY disponíveis no Azure Marketplace ou utilizar a extensão DSC para obter qualquer configuração hospedada no GitHub. Verá um erro semelhante ao seguinte durante a implementação da extensão:

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

  - Na nova versão de extensão, o TLS 1.2 é agora aplicado. Ao implementar a extensão se já tiver o AutoUpgradeMinorVersion = verdadeiro no modelo de Gestor de Recursos, então a extensão será automaticamente melhorada para 2.75. Para atualizações manuais, especifique `TypeHandlerVersion = 2.75` no seu modelo de Gestor de Recursos.

### <a name="version-270---272"></a>Versão 2.70 - 2.72

- **Data de lançamento:** 13 de novembro de 2017
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - As correções de & melhorias que simplificam a utilização da DSC Azure Automation através do portal UI, bem como o modelo de Gestor de Recursos. Para obter mais informações, consulte o [Script de Configuração padrão](../virtual-machines/extensions/dsc-overview.md) na documentação da extensão do DSC.

### <a name="version-226"></a>Versão 2.26

- **Data de lançamento:** 9 de junho de 2017
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - Melhorias na telemetria.

### <a name="version-225"></a>Versão 2.25

- **Data de lançamento:** 2 de junho de 2017
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - Foram adicionadas várias correções de bugs e outras pequenas melhorias.

### <a name="version-224"></a>Versão 2.24

- **Data de lançamento:** 13 de abril de 2017
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - Expõe vM UUID & DSC Agent ID como metadados de extensão. Outras melhorias menores foram adicionadas.

### <a name="version-223"></a>Versão 2.23

- **Data de lançamento:** 15 de março de 2017
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - Foram adicionadas muitas correções de bugs e outras melhorias.

### <a name="version-222"></a>Versão 2.22

- **Data de lançamento:** 8 de fevereiro de 2017
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte do WMF:** WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o Quadro de Gestão do [Windows 5.1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - A Extensão DSC conta agora com suporte para o WMF 5.1.
  - Foram adicionadas pequenas melhorias.

### <a name="version-221"></a>Versão 2.21

- **Data de lançamento:** 2 de dezembro de 2016
- **Suporte do SO:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Nano Server
- **Suporte do WMF:** Pré-visualização WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído no Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot). Para o Nano Server, a função DSC é instalada no VM.
- **Novas funcionalidades:**
  - A extensão DSC está agora disponível no Nano Server. Esta versão contém principalmente alterações de código para executar a extensão no Nano Server.
  - Foram adicionadas pequenas melhorias.

### <a name="version-220"></a>Versão 2.20

- **Data de lançamento:** 2 de agosto de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** Pré-visualização WMF 5.1, WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Suporte para pré-visualização WMF 5.1. Quando foi publicada pela primeira vez, esta versão foi uma atualização opcional e teve de especificar Wmfversion = '5.1PP' nos modelos de Gestor de Recursos para instalar a pré-visualização do WMF 5.1.
    Wmfversion = 'mais recente' ainda instala o [WMF 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Para mais informações sobre a pré-visualização do WMF 5.1, consulte [este blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/).
  - Foram adicionadas pequenas outras correções e melhorias.

### <a name="version--219"></a>Versão 2.19

- **Data de lançamento:** 3 de junho de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Azure, Azure China Vianet 21, Governo de Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - A extensão do DSC está agora a bordo do Azure China Vianet 21. Esta versão contém principalmente correções para executar a extensão do Vianet Azure China 21.

### <a name="version-218"></a>Versão 2.18

- **Data de lançamento:** 3 de junho de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Faça o não bloqueio da telemetria quando ocorrer um erro durante o download do hotfix da telemetria (conhecido problema Azure DNS) ou durante a instalação.
  - Corrija para o problema intermitente onde a extensão para de processar a configuração após um reboot.
    Isto estava a fazer com que a extensão do DSC permanecesse em estado de "transição".
  - Foram adicionadas pequenas outras correções e melhorias.

### <a name="version-217"></a>Versão 2.17

- **Data de lançamento:** 26 de abril de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** WMF 5.0 RTM, Atualização WMF 4.0, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Suporte para atualização WMF 4.0. Para mais informações sobre a Atualização WMF 4.0, consulte [este blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/).
  - Relemca a lógica dos erros que ocorrem durante a instalação da extensão DSC ou durante a aplicação de uma instalação de extensão de registo de configuração DSC. Como parte desta alteração, a extensão retentará a instalação se uma instalação anterior falhar ou reencenar uma configuração DSC que já tinha falhado, por um máximo de três vezes até atingir o estado de conclusão (Sucesso/Erro) ou se um novo pedido chegar. Se a extensão falhar devido às definições inválidas do utilizador/entrada do utilizador, não se recandida. Neste caso, a extensão deve ser novamente invocada com um novo pedido e definições corretas do utilizador. Nota: A extensão do DSC depende do agente Azure VM para as retrações. O agente Azure VM invoca a extensão com o último pedido falhado até atingir um estado de sucesso ou erro.

### <a name="version-216"></a>Versão 2.16

- **Data de lançamento:** 21 de abril de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Melhoria no manuseamento de erros e outras correções de erros menores.
  - Nova propriedade nas definições de extensão DSC. 'ForcePullAndApply' em AdvancedOptions é adicionado para ativar a extensão DSC decretar configurações DSC quando o modo de atualização é Pull (ao contrário do modo push predefinido). Para mais informações, consulte [este blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/) para obter mais informações sobre as definições de Extensão DSC.

### <a name="version-215"></a>Versão 2.15

- **Data de lançamento:** 14 de março de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Na versão de extensão 2.14, foram incluídas alterações à instalação do WMF RTM. Ao atualizar da versão de extensão 2.13.2.0 para 2.14.0.0, poderá notar que alguns cmdlets DSC falham ou a sua configuração falha com um erro – 'Nenhuma instância encontrada com dados valores de propriedade'. Para obter mais informações, consulte as notas de lançamento do [DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc). As soluções alternativas para estas questões foram adicionadas na versão 2.15.
  - Infelizmente, se já instalou a versão 2.14 e está a desatenção de um dos dois problemas acima referidos, terá de executar estes passos manualmente. Numa sessão elevada da PowerShell:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Versão 2.14

- **Data de lançamento:** 25 de fevereiro de 2016
- **Suporte do SO:** Pré-visualização técnica do Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **Suporte do WMF:** WMF 5.0 RTM, WMF 4.0
- **Ambiente:** Rio Azure
- **Observações:** Esta versão utiliza o DSC como incluído na pré-visualização técnica do Windows Server 2016; para outros OSes do Windows, instala o [Windows Management Framework 5.0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (instalar o WMF requer um reboot).
- **Novas funcionalidades:**
  - Usa a WMF RTM.
  - Permite a recolha de dados de forma a melhorar a qualidade da extensão do DSC. Para mais informações, consulte [o blog.](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)
  - Fornece um formato de definições atualizado para a extensão num modelo de Gestor de Recursos. Para mais informações, consulte [o blog.](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)
  - Correções de insetos e outras melhorias.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o PowerShell DSC, consulte o [centro de documentação PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o [modelo de Gestor de Recursos para a extensão DSC](../virtual-machines/extensions/dsc-template.md).
- Para obter mais funcionalidades e recursos que possa gerir com o PowerShell DSC, consulte a [galeria PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Para obter detalhes sobre a passagem de parâmetros sensíveis para as configurações, consulte [Gerir as credenciais de forma segura com o manipulador de extensão DSC](../virtual-machines/extensions/dsc-credentials.md).
