---
title: Visão geral do trabalhador do runbook híbrido da Azure Automation
description: Este artigo fornece uma visão geral do Trabalhador de Runbook Híbrido, que pode usar para executar livros de execução em máquinas no seu datacenter local ou fornecedor de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: 7bd9560399a2b2b377cb543a4f829883bcbdf7c8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183775"
---
# <a name="hybrid-runbook-worker-overview"></a>Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)

Os runbooks na Azure Automation podem não ter acesso a recursos noutras nuvens ou no seu ambiente no local porque funcionam na plataforma cloud Azure. Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar runbooks diretamente na máquina que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. Os runbooks são armazenados e geridos na Azure Automation e depois entregues a uma ou mais máquinas atribuídas.

A imagem a seguir ilustra esta funcionalidade:

![Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)](media/automation-hybrid-runbook-worker/automation.png)

Existem dois tipos de Trabalhadores runbook - sistema e utilizador. A tabela a seguir descreve a diferença entre eles.

|Tipo | Description |
|-----|-------------|
|**Sistema** |Suporta um conjunto de livros de execução ocultos utilizados pela funcionalidade De Gestão de Atualização que são projetados para instalar atualizações especificadas pelo utilizador nas máquinas Windows e Linux.<br> Este tipo de Trabalhador de Runbook Híbrido não é membro de um grupo híbrido de trabalhadores runbook, e, portanto, não executam runbooks que visam um grupo de trabalhadores runbook. |
|**Utilizador** |Suporta livros de execução definidos pelo utilizador destinados a funcionar diretamente na máquina Windows e Linux que são membros de um ou mais grupos de Trabalhadores runbook. |

Um Trabalhador De Runbook Híbrido pode funcionar no Windows ou no sistema operativo Linux, e esta função depende do [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) reportando a um espaço de trabalho Azure Monitor Log [Analytics](../azure-monitor/platform/design-logs-deployment.md). O espaço de trabalho não é apenas para monitorizar a máquina para o sistema operativo suportado, mas também para descarregar os componentes necessários para instalar o Trabalhador de Runbook Híbrido.

Quando a Azure Automation [Update Management](./update-management/overview.md) estiver ativada, qualquer máquina ligada ao seu espaço de trabalho Log Analytics é automaticamente configurada como um trabalhador de runbook híbrido do sistema.

Cada utilizador Hybrid Runbook Worker é membro de um grupo híbrido de trabalho de runbook que especifica quando instala o trabalhador. Um grupo pode incluir um único trabalhador, mas você pode incluir vários trabalhadores em um grupo para alta disponibilidade. Cada máquina pode hospedar um Trabalhador De Runbook Híbrido reportando a uma conta de Automação; não é possível registar o trabalhador híbrido em várias contas de Automação. Isto porque um trabalhador híbrido só pode ouvir empregos a partir de uma única conta de Automação. Para as máquinas que hospedam o sistema Hybrid Runbook trabalhador gerido pela Update Management, podem ser adicionadas a um grupo híbrido de trabalhador runbook. Mas deve utilizar a mesma conta de Automação tanto para a Gestão de Atualização como para a associação híbrida do grupo de trabalhadores runbook.

Quando iniciar um livro de execução num utilizador Hybrid Runbook Worker, especifica o grupo em que funciona. Cada trabalhador do grupo vota Azure Automation para ver se há empregos disponíveis. Se houver um emprego disponível, o primeiro trabalhador a conseguir o emprego aceita-o. O tempo de processamento da fila de trabalhos depende do perfil e da carga do trabalhador híbrido. Não se pode especificar um trabalhador em particular. O trabalhador híbrido trabalha num mecanismo de votação (a cada 30 segundos) e segue uma ordem de primeiro a chegar, primeiro a servir. Dependendo de quando um trabalho foi empurrado, qualquer que seja o trabalhador híbrido pings o serviço de Automação pega o trabalho. Um único trabalhador híbrido pode geralmente conseguir quatro empregos por ping (isto é, a cada 30 segundos). Se a sua taxa de trabalhos de empurrar é superior a quatro por 30 segundos, então há uma alta possibilidade de outro trabalhador híbrido no grupo híbrido Runbook Worker ter escolhido o emprego.

Para controlar a distribuição de runbooks em Trabalhadores De Runbook Híbridos e quando ou como os trabalhos são desencadeados, você pode registar o trabalhador híbrido contra diferentes grupos híbridos de trabalhadores de runbook dentro da sua conta de Automação. Direcione os postos de trabalho contra o grupo ou grupos específicos, a fim de apoiar o seu acordo de execução.

Utilize um Trabalhador de Runbook Híbrido em vez de uma caixa de [areia Azure](automation-runbook-execution.md#runbook-execution-environment) porque não tem muitos dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) da caixa de areia no espaço do disco, na memória ou nas tomadas de rede. Os limites para um trabalhador híbrido estão apenas relacionados com os recursos próprios do trabalhador.

> [!NOTE]
> Os trabalhadores híbridos não estão limitados pelo justo limite de tempo de [partilha](automation-runbook-execution.md#fair-share) que as caixas de areia Azure têm.

## <a name="hybrid-runbook-worker-installation"></a>Instalação híbrida do trabalhador runbook

O processo de instalação de um utilizador Hybrid Runbook Worker depende do sistema operativo. A tabela abaixo define os tipos de implantação.

|Sistema Operativo  |Tipos de implantação  |
|---------|---------|
|Windows     | [Automatizada](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manual](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado para uma máquina Windows é utilizar um manual da Azure Automation para automatizar completamente o processo de configuração. Se isso não for viável, pode seguir um procedimento passo a passo para instalar e configurar manualmente o papel. Para máquinas Linux, você executou um script Python para instalar o agente na máquina.

## <a name="network-planning"></a><a name="network-planning"></a>Planeamento da rede

Para que um sistema e utilizador Hybrid Runbook Worker se conectem e registem com a Azure Automation, deve ter acesso ao número de porta e URLs descritos nesta secção. O trabalhador também deve ter acesso às [portas e URLs necessários para](../azure-monitor/platform/agent-windows.md) que o agente Log Analytics se conecte ao espaço de trabalho Azure Monitor Log Analytics.

São necessários os seguintes portos e URLs para o Trabalhador de Runbook Híbrido:

* Porto: Apenas TCP 443 necessário para acesso à Internet de saída
* URL global: `*.azure-automation.net`
* URL global de Us Gov Virginia: `*.azure-automation.us`
* Serviço de agente: `https://<workspaceId>.agentsvc.azure-automation.net`

Se tiver uma conta Automation que está definida para uma região específica, pode restringir a comunicação do Trabalhador Do Runbook Híbrido a esse datacenter regional. Reveja os [registos DNS utilizados pela Azure Automation](how-to/automation-region-dns-records.md) para obter os registos DNS necessários.

### <a name="proxy-server-use"></a>Uso do servidor proxy

Se utilizar um servidor proxy para comunicação entre a Azure Automation e as máquinas que executam o agente Log Analytics, certifique-se de que os recursos adequados estão acessíveis. O tempo limite para pedidos dos serviços híbridos de trabalho e automação é de 30 segundos. Após três tentativas, um pedido falha.

### <a name="firewall-use"></a>Uso de firewall

Se utilizar uma firewall para restringir o acesso à Internet, tem de configurar a firewall para permitir o acesso. Se utilizar o gateway Log Analytics como procuração, certifique-se de que está configurado para trabalhadores híbridos runbook. Consulte a porta de [entrada do Log Analytics para trabalhadores híbridos da Automação.](../azure-monitor/platform/gateway.md)

### <a name="service-tags"></a>Etiquetas de serviço

A Azure Automation suporta tags de serviço de rede virtual Azure, começando com a tag de serviço [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules) de rede ou [Azure Firewall](../firewall/service-tags.md). As etiquetas de serviço podem ser utilizadas no lugar de endereços IP específicos quando cria regras de segurança. Ao especificar o nome da etiqueta de serviço **GuestAndHybridManagement**  no campo de origem ou destino apropriado de uma regra, pode permitir ou negar o tráfego para o serviço Dem automação. Esta etiqueta de serviço não suporta permitir um maior controlo granular limitando as gamas IP a uma região específica.

A etiqueta de serviço para o serviço Azure Automation apenas fornece IPs utilizados para os seguintes cenários:

* Trigger webhooks de dentro da sua rede virtual
* Permitir que trabalhadores híbridos runbook ou agentes de configuração do Estado no seu VNet comuniquem com o serviço de Automação

>[!NOTE]
>A tag de serviço **GuestAndHybridManagement** atualmente não suporta a execução de trabalho de runbook numa caixa de areia Azure, apenas diretamente em um Trabalhador De Runbook Híbrido.

## <a name="support-for-impact-level-5-il5"></a>Suporte para o Nível de Impacto 5 (IL5)

O Azure Automation Hybrid Runbook Worker pode ser usado no Governo Azure para suportar cargas de trabalho de nível de impacto 5 em qualquer uma das duas configurações seguintes:

* [Máquina virtual isolada.](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines) Quando implantados, consomem todo o hospedeiro físico para aquela máquina, proporcionando o nível de isolamento necessário para suportar as cargas de trabalho IL5.

* [Azure Dedicated Hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-hosts), que fornece servidores físicos capazes de hospedar uma ou mais máquinas virtuais, dedicadas a uma subscrição do Azure.

>[!NOTE]
>O isolamento computo através do papel de Trabalhador de Runbook Híbrido está disponível para nuvens do Governo Azure Comercial e dos EUA. 

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de gestão de atualização para trabalhador de runbook híbrido

Para além dos endereços padrão e portas necessários para o Trabalhador de Runbook Híbrido, a Update Management tem requisitos adicionais de configuração de rede descritos na secção de planeamento da [rede.](./update-management/overview.md#ports)

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Configuração do Estado da Automação Azure em um trabalhador de runbook híbrido

Pode executar [a configuração do Estado da Automação Azure](automation-dsc-overview.md) num Trabalhador de Runbook Híbrido. Para gerir a configuração dos servidores que suportam o Trabalhador de Runbook Híbrido, tem de adicionar os servidores como nós DSC. Ver [Ativar máquinas para gestão através da Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limites do trabalhador do runbook

O número máximo de grupos híbridos por Conta de Automação é de 4000, e é aplicável tanto para os trabalhadores híbridos & utilizador. Se tiver mais de 4.000 máquinas para gerir, recomendamos a criação de contas de Automação adicionais.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks em um trabalhador de runbook híbrido

Você pode ter runbooks que gerem recursos na máquina local ou correr contra recursos no ambiente local onde um utilizador Híbrido Runbook Worker é implantado. Neste caso, pode optar por executar os seus livros no trabalhador híbrido em vez de numa conta Automation. Os runbooks executados num Trabalhador De Runbook Híbrido são idênticos em estrutura àqueles que você gere na conta Automation. Consulte [os runbooks de execução num trabalhador de runbook híbrido](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Empregos de trabalhadores de runbook híbridos

Os empregos híbridos do Runbook Worker são geridos sob a conta **do Sistema** local no Windows ou na conta [de nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A Azure Automation lida com empregos em Trabalhadores De Runbook Híbridos diferentes dos empregos geridos em caixas de areia Azure. Consulte [o ambiente de execução do Runbook](automation-runbook-execution.md#runbook-execution-environment).

Se a máquina de anfitrião Do Trabalhador Do Runbook Híbrido reiniciar, qualquer trabalho de runbook em execução reinicia desde o início ou do último ponto de verificação para os livros de fluxo de trabalho PowerShell. Depois de um trabalho de runbook ser reiniciado mais de três vezes, é suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de runbook para um trabalhador de runbook híbrido

Uma vez que acedem a recursos não-Azure, os runbooks em execução num utilizador Hybrid Runbook Worker não podem usar o mecanismo de autenticação normalmente utilizado por runbooks autenticando recursos Azure. Um livro de aplicação fornece a sua própria autenticação aos recursos locais, ou configura a autenticação utilizando [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Também pode especificar uma conta Run As para fornecer um contexto de utilizador para todos os livros.

## <a name="view-system-hybrid-runbook-workers"></a>Ver sistema Híbrido Runbook Workers

Depois da funcionalidade de Gestão de Atualização estar ativada nas máquinas Windows ou Linux, pode inventariar a lista do grupo de Trabalhadores de Runbook Híbridos do sistema no portal Azure. Pode visualizar até 2.000 trabalhadores no portal selecionando o grupo de **trabalhadores híbridos do sistema** de separadores a partir da opção Grupo de **Trabalhadores Híbridos** do painel de trabalho à esquerda para a conta de Automação selecionada.

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