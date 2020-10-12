---
title: Visão geral do trabalhador do runbook híbrido da Azure Automation
description: Este artigo fornece uma visão geral do Trabalhador de Runbook Híbrido, que pode usar para executar livros de execução em máquinas no seu datacenter local ou fornecedor de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 09/14/2020
ms.topic: conceptual
ms.openlocfilehash: f5dc9305df8ce0e26e13738d605849fa75cc53a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087898"
---
# <a name="hybrid-runbook-worker-overview"></a>Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)

Os runbooks na Azure Automation podem não ter acesso a recursos noutras nuvens ou no seu ambiente no local porque funcionam na plataforma cloud Azure. Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar runbooks diretamente na máquina que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. Os runbooks são armazenados e geridos na Azure Automation e depois entregues a uma ou mais máquinas atribuídas.

A imagem a seguir ilustra esta funcionalidade:

![Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)](media/automation-hybrid-runbook-worker/automation.png)

Um Trabalhador De Runbook Híbrido pode funcionar tanto no Windows como no sistema operativo Linux. Depende do relatório do [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) para um espaço de trabalho do Azure Monitor [Log Analytics](../azure-monitor/platform/design-logs-deployment.md). O espaço de trabalho não é apenas para monitorizar a máquina para o sistema operativo suportado, mas também para descarregar os componentes necessários para o Trabalhador de Runbook Híbrido.

Cada Trabalhador de Runbook Híbrido é membro de um grupo híbrido de trabalhador de runbook que especifica quando instala o agente. Um grupo pode incluir um único agente, mas pode instalar vários agentes num grupo para uma alta disponibilidade. Cada máquina pode hospedar um trabalhador híbrido reportando a uma conta Automation.

Quando iniciar um livro de execução num Trabalhador de Runbook Híbrido, especifica o grupo em que funciona. Cada trabalhador do grupo vota Azure Automation para ver se há empregos disponíveis. Se houver um emprego disponível, o primeiro trabalhador a conseguir o emprego aceita-o. O tempo de processamento da fila de trabalhos depende do perfil e da carga do trabalhador híbrido. Não se pode especificar um trabalhador em particular.

Utilize um Trabalhador de Runbook Híbrido em vez de uma caixa de [areia Azure](automation-runbook-execution.md#runbook-execution-environment) porque não tem muitos dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) da caixa de areia no espaço do disco, na memória ou nas tomadas de rede. Os limites para um trabalhador híbrido estão apenas relacionados com os recursos próprios do trabalhador.

> [!NOTE]
> Os trabalhadores híbridos não estão limitados pelo justo limite de tempo de [partilha](automation-runbook-execution.md#fair-share) que as caixas de areia Azure têm.

## <a name="hybrid-runbook-worker-installation"></a>Instalação híbrida do trabalhador runbook

O processo de instalação de um Trabalhador De Runbook Híbrido depende do sistema operativo. A tabela abaixo define os tipos de implantação.

|Sistema Operativo  |Tipos de implantação  |
|---------|---------|
|Windows     | [Automatizada](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado é utilizar um manual de automação Azure para automatizar completamente o processo de configuração de uma máquina Windows. Se isso não for viável, pode seguir um procedimento passo a passo para instalar e configurar manualmente o papel. Para máquinas Linux, você executou um script Python para instalar o agente na máquina.

## <a name="network-planning"></a><a name="network-planning"></a>Planeamento da rede

Para que o Trabalhador de Runbook Híbrido se conecte e registe com a Azure Automation, deve ter acesso ao número de porta e URLs descritos nesta secção. O trabalhador também deve ter acesso às [portas e URLs necessários para](../azure-monitor/platform/agent-windows.md) que o agente Log Analytics se conecte ao espaço de trabalho Azure Monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

São necessários os seguintes portos e URLs para o Trabalhador de Runbook Híbrido:

* Porto: Apenas TCP 443 necessário para acesso à Internet de saída
* URL global: `*.azure-automation.net`
* URL global de Us Gov Virginia: `*.azure-automation.us`
* Serviço de agente: `https://<workspaceId>.agentsvc.azure-automation.net`

Se tiver uma conta Automation que está definida para uma região específica, pode restringir a comunicação do Trabalhador Do Runbook Híbrido a esse datacenter regional. Reveja os [registos DNS utilizados pela Azure Automation](how-to/automation-region-dns-records.md) para obter os registos DNS necessários.

### <a name="proxy-server-use"></a>Uso do servidor proxy

Se utilizar um servidor proxy para comunicação entre a Azure Automation e as máquinas que executam o agente Log Analytics, certifique-se de que os recursos adequados estão acessíveis. O tempo limite para pedidos dos serviços híbridos de trabalho e automação é de 30 segundos. Após três tentativas, um pedido falha.

### <a name="firewall-use"></a>Uso de firewall

Se utilizar uma firewall para restringir o acesso à internet, tem de configurar a firewall para permitir o acesso. Se utilizar o gateway Log Analytics como procuração, certifique-se de que está configurado para trabalhadores híbridos runbook. Consulte a porta de [entrada do Log Analytics para trabalhadores híbridos da Automação.](../azure-monitor/platform/gateway.md)

### <a name="service-tags"></a>Etiquetas de serviço

A Azure Automation suporta tags de serviço de rede virtual Azure, começando com a tag de serviço [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/security-overview.md#security-rules) de rede ou [Azure Firewall](../firewall/service-tags.md). As etiquetas de serviço podem ser utilizadas no lugar de endereços IP específicos quando cria regras de segurança. Ao especificar o nome da etiqueta de serviço **GuestAndHybridManagement**  no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço Dem automação. Esta etiqueta de serviço não suporta permitir um maior controlo granular limitando as gamas IP a uma região específica.

A etiqueta de serviço para o serviço Azure Automation apenas fornece IPs utilizados para os seguintes cenários:

* Trigger webhooks de dentro da sua rede virtual
* Permitir que trabalhadores híbridos runbook ou agentes de configuração do Estado no seu VNet comuniquem com o serviço de Automação

>[!NOTE]
>A tag de serviço **GuestAndHybridManagement** atualmente não suporta a execução de trabalho de runbook numa caixa de areia Azure, apenas diretamente em um Trabalhador De Runbook Híbrido.

## <a name="support-for-impact-level-5-il5"></a>Suporte para o Nível de Impacto 5 (IL5)

O Azure Automation Hybrid Runbook Worker pode ser usado no Governo Azure para suportar cargas de trabalho de nível de impacto 5 em qualquer uma das duas configurações seguintes:

* [Máquina virtual isolada.](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines) Quando implantados, consomem todo o hospedeiro físico para que o VM forneça o nível de isolamento necessário para suportar as cargas de trabalho IL5.

* [Azure Dedicated Hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), que fornece servidores físicos capazes de hospedar uma ou mais máquinas virtuais, dedicadas a uma subscrição do Azure.

>[!NOTE]
>O isolamento computo através do papel de Trabalhador de Runbook Híbrido está disponível para nuvens do Governo Azure Comercial e dos EUA. 

## <a name="update-management-on-hybrid-runbook-worker"></a>Gestão de atualização no trabalhador de runbook híbrido

Quando a Azure Automation [Update Management](update-management/update-mgmt-overview.md) estiver ativada, qualquer máquina ligada ao seu espaço de trabalho Log Analytics é automaticamente configurada como Um Trabalhador De Runbook Híbrido. Cada trabalhador pode suportar livros de execução direcionados para a gestão de atualizações.

Uma máquina configurada desta forma não está registada em nenhum grupo híbrido de trabalhador de runbook já definido na sua conta Automation. Pode adicionar a máquina a um grupo híbrido de trabalhador runbook, mas tem de utilizar a mesma conta tanto para a Gestão de Atualização como para a associação do grupo Híbrido Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de gestão de atualização para trabalhador de runbook híbrido

Além dos endereços e portas padrão necessários para o Trabalhador de Runbook Híbrido, a Update Management tem requisitos adicionais de configuração de rede descritos na secção de planeamento da [rede.](update-management/update-mgmt-overview.md#ports)

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Configuração do Estado da Automação Azure em um trabalhador de runbook híbrido

Pode executar [a configuração do Estado da Automação Azure](automation-dsc-overview.md) num Trabalhador de Runbook Híbrido. Para gerir a configuração dos servidores que suportam o Trabalhador de Runbook Híbrido, tem de adicionar os servidores como nós DSC. Ver [Ativar máquinas para gestão através da Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em um trabalhador de runbook híbrido

Você pode ter livros que gerem recursos na máquina local ou correr contra recursos no ambiente local onde um Trabalhador De Runbook Híbrido é implantado. Neste caso, pode optar por executar os seus livros no trabalhador híbrido em vez de numa conta Automation. Os runbooks executados num Trabalhador De Runbook Híbrido são idênticos em estrutura àqueles que você gere na conta Automation. Consulte [os runbooks de execução num trabalhador de runbook híbrido](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Empregos de trabalhadores de runbook híbridos

Os empregos híbridos do Runbook Worker são geridos sob a conta **do Sistema** local no Windows ou na conta [de nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A Azure Automation lida com empregos em Trabalhadores De Runbook Híbridos de forma um pouco diferente dos empregos geridos em caixas de areia Azure. Consulte [o ambiente de execução do Runbook](automation-runbook-execution.md#runbook-execution-environment).

Se a máquina de anfitrião Do Trabalhador Do Runbook Híbrido reiniciar, qualquer trabalho de runbook em execução reinicia desde o início ou do último ponto de verificação para os livros de fluxo de trabalho PowerShell. Depois de um trabalho de runbook ser reiniciado mais de três vezes, é suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um trabalhador de runbook híbrido

Uma vez que acedem a recursos não-Azure, os runbooks em execução num Trabalhador De Runbook Híbrido não podem usar o mecanismo de autenticação normalmente utilizado por runbooks autenticando recursos Azure. Um livro de aplicação fornece a sua própria autenticação aos recursos locais, ou configura a autenticação utilizando [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Também pode especificar uma conta Run As para fornecer um contexto de utilizador para todos os livros.

## <a name="view-hybrid-runbook-workers"></a>Ver Trabalhadores de Runbook Híbridos

Depois da funcionalidade de Gestão de Atualização estar ativada em servidores windows ou VMs, pode inventariar a lista do grupo de Trabalhadores de Runbook Híbridos do sistema no portal Azure. Pode visualizar até 2.000 trabalhadores no portal selecionando o grupo de **trabalhadores híbridos do sistema** de separadores a partir da opção Grupo de **Trabalhadores Híbridos** do painel de trabalho à esquerda para a conta de Automação selecionada.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Página de grupos de trabalhadores híbridos do sistema de contas de automação" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Se tiver mais de 2.000 trabalhadores híbridos, para obter uma lista de todos eles, pode executar o seguinte script PowerShell:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem, consulte [runbooks Runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md).

* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas.](troubleshoot/hybrid-runbook-worker.md#general)
