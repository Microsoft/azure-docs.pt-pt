---
title: Visão geral do trabalhador do livro híbrido na automação azure
description: Este artigo fornece uma visão geral do Trabalhador do Livro Híbrido, que é uma característica da Automação Azure que pode usar para executar livros de corridas em máquinas no seu datacenter local ou fornecedor de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 0b36651a40267ec3ea8bfe7285c5f6c5d5c31562
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871650"
---
# <a name="hybrid-runbook-worker-overview"></a>Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)

Os livros de corridas da Azure Automation podem não ter acesso a recursos noutras nuvens ou no seu ambiente no local porque funcionam na plataforma de nuvem Azure. Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar livros de execução diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. Os livros de execução são armazenados e geridos na Azure Automation e depois entregues a um ou mais computadores atribuídos.

A imagem seguinte ilustra esta funcionalidade:

![Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)](media/automation-hybrid-runbook-worker/automation.png)

Um Trabalhador híbrido do livro de corridas pode executar o Windows ou o sistema operativo Linux. Para a monitorização, requer a utilização do Monitor Azure e de um agente Log Analytics para o sistema operativo suportado. Para mais informações, consulte [o Monitor Azure.](automation-runbook-execution.md#azure-monitor)

Cada Trabalhador De Runbook Híbrido é membro de um grupo híbrido runbook worker que especifica quando instala o agente. Um grupo pode incluir um único agente, mas você pode instalar vários agentes em um grupo para alta disponibilidade. Cada máquina pode acolher um trabalhador híbrido reportando a uma conta de Automação. 

Quando inicia um livro de corridas num Trabalhador Híbrido do Livro de Corridas, especifica o grupo em que funciona. Cada trabalhador do grupo faz sondagens À Azure Automation para ver se existem postos de trabalho disponíveis. Se um emprego está disponível, o primeiro trabalhador a conseguir o emprego aceita-o. O tempo de processamento da fila de trabalho depende do perfil e carga de hardware dos trabalhadores híbridos. Não pode especificar um trabalhador em particular. 

Utilize um Trabalhador de Runbook Híbrido em vez de uma caixa de [areia Azure](automation-runbook-execution.md#runbook-execution-environment) porque não tem muitos dos [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) da caixa de areia no espaço, memória ou tomadas de rede do disco. Os limites para um trabalhador híbrido estão apenas relacionados com os recursos próprios do trabalhador. 

> [!NOTE]
> Os trabalhadores híbridos do livro de corridas não estão limitados pelo limite de tempo [de partilha justa](automation-runbook-execution.md#fair-share) que as caixas de areia Azure têm. 

## <a name="hybrid-runbook-worker-installation"></a>Instalação híbrida do trabalhador do livro de corridas

O processo de instalação de um Trabalhador do Livro De Execução Híbrido depende do sistema operativo. A tabela abaixo define os tipos de implantação.

|Sistema Operativo  |Tipos de implantação  |
|---------|---------|
|Windows     | [Automatizada](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado é utilizar um livro de execução da Automatização Azure para automatizar completamente o processo de configuração de um computador Windows. O segundo método consiste em seguir um procedimento passo a passo para instalar manualmente e configurar a função. Para as máquinas Linux, executa um guião Python para instalar o agente na máquina.

## <a name="network-configuration"></a><a name="network-planning"></a>Configuração da rede

Para que o Trabalhador do Livro Híbrido se ligue e registe-se com a Automação Azure, deve ter acesso ao número de porta e URLs descritos nesta secção. O trabalhador deve também ter acesso às [portas e URLs necessários para](../azure-monitor/platform/agent-windows.md) que o agente Log Analytics se ligue ao espaço de trabalho Azure Monitor Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

São necessários os seguintes portes e URLs para o Trabalhador do Livro Híbrido:

* Porta: Apenas TCP 443 necessário para acesso à Internet de saída
* URL global: *.azure-automation.net
* URL global do US Gov – Virginia: *.azure-automation.us
* Serviço de\<agente:\>https:// workspaceId .agentsvc.azure-automation.net

Recomendamos que utilize os endereços listados ao definir [exceções](automation-runbook-execution.md#exceptions). Para endereços IP, pode descarregar os intervalos IP do [Microsoft Azure Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Este ficheiro é atualizado semanalmente, e tem as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

### <a name="dns-records-per-region"></a>Registos de DNS por região

Se tiver uma conta de Automação definida para uma região específica, pode restringir a comunicação do Trabalhador do Livro Híbrido a esse datacenter regional. A tabela seguinte fornece o registo de DNS para cada região.

| **Região** | **Registo dNS** |
| --- | --- |
| Austrália Central |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Ásia Sudeste |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| E.U.A.Oeste 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

Para obter uma lista de endereços IP da região em vez de nomes de região, descarregue o ficheiro XML do [endereço IP do Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) do Microsoft Download Center. Um ficheiro de endereço IP atualizado é publicado semanalmente. 

O ficheiro de endereço IP lista as gamas de endereços IP que são utilizadas nos datacenters do Microsoft Azure. Inclui gamas de cálculo, SQL e armazenamento, e reflete gamas atualmente implantadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são usadas nos centros de dados durante pelo menos uma semana.

É uma boa ideia descarregar o novo ficheiro ip todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em funcionamento no Azure. 

> [!NOTE]
> Se estiver a utilizar o Azure ExpressRoute, lembre-se de que o ficheiro de endereçoIP é utilizado para atualizar o anúncio do Protocolo de Gateway de Fronteira (BGP) do espaço Azure na primeira semana de cada mês.

### <a name="proxy-server-use"></a>Utilização do servidor Proxy

Se utilizar um servidor proxy para comunicação entre a Automação Azure e o agente Log Analytics, certifique-se de que os recursos adequados estão acessíveis. O prazo para pedidos dos serviços híbridos de trabalho e automação é de 30 segundos. Depois de três tentativas, um pedido falha. 

### <a name="firewall-use"></a>Utilização de firewall

Se utilizar uma firewall para restringir o acesso à internet, tem de configurar a firewall para permitir o acesso. Se utilizar o gateway Log Analytics como procuração, certifique-se de que está configurado para trabalhadores híbridos do livro de corridas. Consulte [a configuração da porta de entrada Log Analytics para Automação de Trabalhadores Híbridos](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

## <a name="update-management-on-hybrid-runbook-worker"></a>Gestão de Atualização no Trabalhador do Livro híbrido

Quando a [Azure](automation-update-management.md) Automation Update Management está ativada, qualquer computador ligado ao seu espaço de trabalho Log Analytics é automaticamente configurado como um Trabalhador de Livro Híbrido. Cada trabalhador pode suportar os livros de execução direcionados para a gestão da atualização. 

Um computador configurado desta forma não está registado em nenhum grupo híbrido de trabalho de runbook já definido na sua conta Deautomação. Pode adicionar o computador a um grupo híbrido de trabalho de runbook, mas deve utilizar a mesma conta tanto para a Atualização como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Endereços de Gestão de Atualização para Trabalhador de Livro de Corridas Híbrido

Além dos endereços padrão e portas que o Trabalhador do Livro híbrido necessita, a Atualização de Gestão precisa dos endereços na tabela seguinte. A comunicação a estes endereços utiliza a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="state-configuration-dsc-on-hybrid-runbook-worker"></a>Configuração do Estado (DSC) no Trabalhador do Livro de Execução Híbrido

Pode executar a função [de Configuração do Estado (DSC)](automation-dsc-overview.md) num Trabalhador híbrido do livro. Para gerir a configuração dos servidores que suportam o Trabalhador do Livro de Execução Híbrido, tem de adicionar os servidores como nós DSC. Para obter mais informações sobre o embarque, consulte [as máquinas onboard para gestão por Configuração estatal (DSC)](automation-dsc-onboarding.md).

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Livros de corridas em um trabalhador híbrido do livro de corridas

Você pode ter livros de execução que gerem recursos no computador local ou correm contra recursos no ambiente local onde um Trabalhador De Rrunde Híbrido é implantado. Neste caso, pode optar por executar os seus livros de execução sobre o trabalhador híbrido em vez de numa conta de Automação. Os livros de execução executados num Trabalhador de Livro Híbrido são idênticos em estrutura aos que executa na conta Automation. See [Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Empregos híbridos de trabalhador de runbook

Os empregos híbridos do Runbook Worker funcionam sob a conta **do Sistema** local no Windows ou na conta [de nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) no Linux. A Azure Automation trata de trabalhos em Trabalhadores de Livro Híbrido de forma um pouco diferente dos empregos executados em caixas de areia Azure. Ver O ambiente de execução do Livro de [Corridas.](automation-runbook-execution.md#runbook-execution-environment)

Se a máquina de anfitriões Hybrid Runbook Worker reiniciar, qualquer trabalho de resta em execução reinicia desde o início, ou a partir do último ponto de verificação para os livros de execução powerShell Workflow. Depois de um trabalho de rés-do-livro ser reiniciado mais de três vezes, está suspenso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Permissões de livro de corridas para um trabalhador híbrido do livro de corridas

Uma vez que acedem a recursos não-Azure, os livros de execução que funcionam num Trabalhador de Runbook Híbrido não podem utilizar o mecanismo de autenticação normalmente utilizado por livros de execução autenticando recursos Azure. Um livro de execução ou fornece a sua própria autenticação aos recursos locais, ou confunde a autenticação usando [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Também pode especificar uma conta Run As para fornecer um contexto de utilizador para todos os livros de execução.

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros de execução para automatizar processos no seu centro de dados no local ou em outro ambiente em nuvem, consulte [run run run book em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para aprender a resolver os seus Trabalhadores Híbridos, consulte os [Trabalhadores híbridos de runbook de resolução de problemas.](troubleshoot/hybrid-runbook-worker.md#general)