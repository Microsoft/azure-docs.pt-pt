---
title: Função de Trabalho de Runbook Híbrida da Automatização do Azure
description: Este artigo fornece informações sobre como instalar e usar Hybrid Runbook Worker, que é um recurso da automação do Azure que você pode usar para executar runbooks em computadores em seu datacenter local ou provedor de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: cb1444261a2ba4810f4fddb3d7aa3bc172f09654
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120915"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizar recursos em seu datacenter ou nuvem usando Hybrid Runbook Worker

Os Runbooks na automação do Azure podem não ter acesso a recursos em outras nuvens ou em seu ambiente local porque eles são executados na plataforma de nuvem do Azure. Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. Os Runbooks são armazenados e gerenciados na automação do Azure e entregues a um ou mais computadores atribuídos.

A imagem a seguir ilustra essa funcionalidade:

![Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)](media/automation-hybrid-runbook-worker/automation.png)

Cada Hybrid Runbook Worker é um membro de um grupo de Hybrid Runbook Worker que você especifica ao instalar o agente. Um grupo pode incluir um único agente, mas você pode instalar vários agentes em um grupo para alta disponibilidade. Cada computador pode hospedar um Hybrid Worker relatórios para uma conta de automação.

Ao iniciar um runbook em um Hybrid Runbook Worker, você especifica o grupo em que ele é executado. Cada trabalho no grupo sonda a automação do Azure para ver se há trabalhos disponíveis. Se um trabalho estiver disponível, o primeiro operador para fazer o trabalho o levará. O tempo de processamento da fila de trabalhos depende do perfil de hardware do Hybrid Worker e do carregamento. Você não pode especificar um trabalho específico. Hybrid runbook Workers não compartilham muitos dos limites que as áreas restritas do Azure têm. Eles não têm os mesmos limites de espaço em disco, memória ou soquetes de rede. Hybrid runbook Workers só são limitados pelos recursos no Hybrid Runbook Worker em si. Além disso, Hybrid runbook Workers não compartilham o limite de tempo de [compartilhamento justo](automation-runbook-execution.md#fair-share) de 180 minutos que as áreas restritas do Azure fazem. Para saber mais sobre os limites de serviço para as áreas restritas do Azure e Hybrid runbook Workers, consulte a página [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de trabalho.

## <a name="install-a-hybrid-runbook-worker"></a>Instalar um Hybrid Runbook Worker

O processo para instalar uma Hybrid Runbook Worker depende do sistema operacional. A tabela a seguir contém links para os métodos que você pode usar para a instalação do.

Para instalar e configurar um Hybrid Runbook Worker do Windows, você pode usar dois métodos. O método recomendado é usar um runbook de automação para automatizar completamente o processo de configuração de um computador com Windows. O segundo método é seguir um procedimento passo a passo para instalar e configurar manualmente a função. Para computadores Linux, você executa um script Python para instalar o agente no computador.

|SO  |Tipos de implantação  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Para gerenciar a configuração dos servidores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você precisa adicioná-los como nós DSC. Para obter mais informações sobre como integrá-los para gerenciamento com DSC, consulte [máquinas de integração para gerenciamento pelo Azure DSC de automação](automation-dsc-onboarding.md).
>
>Se você habilitar a [solução gerenciamento de atualizações](automation-update-management.md), qualquer computador conectado ao seu espaço de trabalho do Azure log Analytics será configurado automaticamente como um Hybrid runbook Worker para dar suporte a runbooks incluídos nesta solução. No entanto, o computador não está registrado com nenhum grupo de Hybrid Worker já definido em sua conta de automação. O computador pode ser adicionado a um grupo de Hybrid Runbook Worker em sua conta de automação para dar suporte a runbooks de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

Examine as [informações para planejar sua rede](#network-planning) antes de começar a implantar um Hybrid runbook Worker. Depois de implantar o trabalho com êxito, examine [executar runbooks em um Hybrid runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

O computador pode ser adicionado a um grupo de Hybrid Runbook Worker em sua conta de automação para dar suporte a runbooks de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

## <a name="remove-a-hybrid-runbook-worker"></a>Remover um Hybrid Runbook Worker

Você pode remover um ou mais Hybrid runbook Workers de um grupo ou pode remover o grupo, dependendo de seus requisitos. Para remover um Hybrid Runbook Worker de um computador local, use as seguintes etapas:

1. Na portal do Azure, acesse sua conta de automação.
2. Em **configurações da conta**, selecione **chaves** e anote os valores para **URL** e **chave de acesso primária**. Você precisa dessas informações para a próxima etapa.

### <a name="windows"></a>Windows

Abra uma sessão do PowerShell no modo de administrador e execute o comando a seguir. Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Para remover computadores obsoletos do seu grupo de Hybrid Worker, use o parâmetro opcional `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Você pode usar o comando `ls /var/opt/microsoft/omsagent` na Hybrid Runbook Worker para obter o workspaceid. Há uma pasta no diretório em que o nome da pasta é a ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Esse código não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de Hybrid Worker

Para remover um grupo, primeiro você precisa remover o Hybrid Runbook Worker de cada computador que seja membro do grupo usando o procedimento mostrado anteriormente. Em seguida, use as seguintes etapas para remover o grupo:

1. Abra a conta de automação no portal do Azure.
2. Em **automação de processo**, selecione **grupos de Hybrid Worker**. Selecione o grupo que você deseja excluir. A página Propriedades desse grupo é exibida.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página Propriedades do grupo selecionado, selecione **excluir**. Uma mensagem solicita que você confirme essa ação. Selecione **Sim** se você tiver certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Esse processo pode levar vários segundos para ser concluído. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="network-planning"></a>Configurar sua rede

### <a name="hybrid-worker-role"></a>Hybrid Worker função

Para que o Hybrid Runbook Worker se conecte e se registre na automação do Azure, ele deve ter acesso ao número da porta e às URLs descritas nesta seção. Esse acesso está no topo das [portas e URLs necessárias para Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) se conectar a logs de Azure monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se você usar um servidor proxy para comunicação entre o agente e o serviço de automação do Azure, verifique se os recursos apropriados estão acessíveis. O tempo limite para solicitações da Hybrid Runbook Worker e dos serviços de automação é de 30 segundos. Após 3 tentativas, a solicitação falhará. Se você usar um firewall para restringir o acesso à Internet, deverá configurar o firewall para permitir o acesso. Se você usar o gateway de Log Analytics como um proxy, verifique se ele está configurado para Hybrid Workers. Para obter instruções sobre como fazer isso, consulte [Configurar o gateway de log Analytics para trabalhos híbridos de automação](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

A porta e as URLs a seguir são necessárias para que a função de Hybrid Runbook Worker se comunique com a automação:

* Porta: somente TCP 443 é necessário para acesso à Internet de saída.
* URL global: *. azure-automation.net
* URL global de US Gov-Virgínia: *. azure-automation.us
* Serviço do agente: https://\<workspaceid\>. agentsvc.azure-automation.net

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, você pode baixar os [intervalos de IP do Microsoft Azure datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de automação definida para uma região específica, poderá restringir a comunicação com esse datacenter regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
| --- | --- |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| E.U.A. Oeste 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrália Central |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP de região em vez de nomes de região, baixe o arquivo XML do [endereço IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) no centro de download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do datacenter do Azure lista os intervalos de endereços IP que são usados nos data centers Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é lançado semanalmente. O arquivo reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos data centers por pelo menos uma semana.
>
> É uma boa ideia baixar o novo arquivo XML a cada semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio de Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês.

### <a name="update-management"></a>Gestão de Atualizações

Além dos endereços padrão e das portas que o Hybrid Runbook Worker requer, os endereços a seguir são necessários especificamente para Gerenciamento de Atualizações. A comunicação com esses endereços é feita pela porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Passos seguintes

* Para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem, consulte [executar runbooks em um Hybrid runbook Worker](automation-hrw-run-runbooks.md).
* Para saber como solucionar problemas de seus Hybrid runbook Workers, consulte [Solucionando problemas de Hybrid runbook Workers](troubleshoot/hybrid-runbook-worker.md#general)

