---
title: Visão geral da configuração do estado da Azure Automation
description: Este artigo fornece uma visão geral da Configuração do Estado da Automação Azure.
keywords: powershell dsc, configuração de estado desejado, powershell dsc azul
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 01/26/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 752d7f86941967c218b3a57fa163698b9f502057
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897025"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da configuração do estado da Azure Automation

A Azure Automation State Configuration é um serviço de gestão de configuração Azure que lhe permite escrever, gerir e compilar [configurações](/powershell/scripting/dsc/configurations/configurations) de Configuração do Estado (DSC) de PowerShell Desired State para nós em qualquer datacenter de nuvem ou no local. O serviço também importa [recursos DSC,](/powershell/scripting/dsc/resources/resources)e atribui configurações para os nós alvo, tudo na nuvem. Pode aceder à Configuração do Estado da Automação Azure no portal Azure selecionando a **configuração do Estado (DSC)** em **Gestão de Configuração**.

Pode utilizar a Configuração do Estado da Automação Azure para gerir uma variedade de máquinas:

- Máquinas virtuais do Azure
- Máquinas virtuais Azure (clássicas)
- Máquinas de Windows físicas/virtuais no local, ou numa nuvem que não seja a Azure (incluindo instâncias AWS EC2)
- Máquinas Linux físicas/virtuais no local, em Azure, ou numa nuvem que não seja a Azure

Se não estiver pronto para gerir a configuração da máquina a partir da nuvem, pode utilizar a Configuração do Estado da Automação Azure como ponto final apenas de relatório. Esta funcionalidade permite-lhe definir configurações (push) através de DSC e ver detalhes de reporte na Azure Automation.

> [!NOTE]
> A gestão de VMs Azure com Configuração do Estado da Automação Azure está incluída sem custos adicionais se a versão instalada de extensão de configuração do estado Azure VM desejada for superior a 2.70. Para mais informações, consulte [**a página de preços da Automação.**](https://azure.microsoft.com/pricing/details/automation/)

## <a name="why-use-azure-automation-state-configuration"></a>Porquê usar a configuração do Estado da Automação Azure

A Azure Automation State Configuration proporciona várias vantagens sobre a utilização de DSC fora de Azure. Este serviço permite a escalabilidade em milhares de máquinas de forma rápida e fácil a partir de uma localização central e segura. Pode ativar facilmente as máquinas, atribuí-las configurações declarativas e ver relatórios que mostrem a conformidade de cada máquina com o estado pretendido especificado.

O serviço de Configuração estatal Azure Automation é para dSC o que os runbooks da Azure Automation são para scripts PowerShell. Por outras palavras, da mesma forma que a Azure Automation ajuda a gerir scripts PowerShell, também ajuda a gerir as configurações do DSC.

### <a name="built-in-pull-server"></a>Servidor de puxar incorporado

A Azure Automation State Configuration fornece um servidor de puxar DSC semelhante ao [Serviço DSC do Windows Feature](/powershell/scripting/dsc/pull-server/pullserver). Os nós-alvo podem receber automaticamente configurações, conformes com o estado pretendido e informar sobre a sua conformidade. O servidor de puxar incorporado na Azure Automation elimina a necessidade de configurar e manter o seu próprio servidor de puxar. A Azure Automation pode visar máquinas virtuais ou físicas de Windows ou Linux, na nuvem ou no local.

### <a name="management-of-all-your-dsc-artifacts"></a>Gestão de todos os seus artefactos DSC

A Azure Automation State Configuration traz a mesma camada de gestão para a [Configuração de Estado Desejada powerShell](/powershell/scripting/dsc/overview/overview) que oferece para scripts PowerShell. A partir do portal Azure ou a partir de PowerShell, pode gerir todas as suas configurações, recursos e nós de alvo.

![Screenshot da página Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>Importação de dados de reporte em registos do Monitor Azure

Os nós que são geridos com a Configuração do Estado da Automação Azure enviam dados detalhados do estado de reporte para o servidor de puxar incorporado. Pode configurar a Configuração do Estado da Automação Azure para enviar estes dados para o seu espaço de trabalho Log Analytics. Consulte [a configuração do Estado da Automação forward Azure reportando dados para registos do Monitor Azure](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os requisitos nesta secção ao utilizar a Configuração do Estado da Automação Azure.

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo

Para os nóns que executam o Windows, as seguintes versões são suportadas:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>O produto autónomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) SKU não contém uma implementação de DSC. Assim, não pode ser gerido pela PowerShell DSC ou pela Azure Automation State Configuration.

Para os nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas na [documentação do DSC powerShell](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós Windows em execução em Azure, [o WMF 5.1](/powershell/scripting/wmf/setup/install-configure) é instalado quando as máquinas estão ativadas. Para os nós que executam o Windows Server 2012 e o Windows 7, [o WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency) está ativado.

Para todos os nós Linux em execução em Azure, [o PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) é instalado quando as máquinas estão ativadas.

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>Configuração de redes privadas

Consulte [a configuração da rede de automação Azure](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) para obter informações detalhadas sobre as portas, URLs e outros detalhes de rede necessários para os nós numa rede privada.

#### <a name="proxy-support"></a>Apoio proxy

O suporte proxy para o agente DSC está disponível na versão 1809 do Windows e posteriormente. Esta opção é ativada através da definição dos valores `ProxyURL` e propriedades no script de `ProxyCredential` [metaconfiguration](automation-dsc-onboarding.md#generate-dsc-metaconfigurations) utilizado para registar nós.

>[!NOTE]
>A Azure Automation State Configuration não fornece suporte de procuração DSC para versões anteriores do Windows.

Para os nós Linux, o agente DSC suporta o proxy e utiliza a `http_proxy` variável para determinar o URL. Para saber mais sobre o suporte a procuração, consulte [gerar metaconfiguções de DSC](automation-dsc-onboarding.md#generate-dsc-metaconfigurations).

#### <a name="dns-records-per-region"></a>Registos dns por região

Recomenda-se a utilização dos endereços listados nos [registos DNS por tabela da região](how-to/automation-region-dns-records.md) ao definir exceções.

## <a name="next-steps"></a>Passos seguintes

- Para começar, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para aprender a ativar os nós, consulte [Ativar a Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
