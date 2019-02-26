---
title: Trabalho de Runbook híbrida de automatização do Azure
description: Este artigo fornece informações sobre como instalar e utilizar o trabalho de Runbook híbrida, que é uma funcionalidade da automatização do Azure que pode utilizar para executar runbooks nas máquinas no seu local datacenter ou o fornecedor de cloud.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8fb1b5e06e694368cf80029fe5ce2dcf0749cc62
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818379"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatize a recursos no seu datacenter ou na cloud com o trabalho de Runbook híbrida

Os Runbooks na automatização do Azure poderá não ter acesso aos recursos noutras Clouds ou no seu ambiente no local porque eles são executados na plataforma cloud do Azure. Pode utilizar a funcionalidade de trabalho de Runbook híbrida da automatização do Azure para executar runbooks diretamente no computador que aloja a função e relativamente aos recursos no ambiente para gerir esses recursos locais. Runbooks são armazenados e gerenciados na automatização do Azure e, em seguida, entregue a um ou mais computadores atribuídos.

A imagem seguinte ilustra esta funcionalidade:

![Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)](media/automation-hybrid-runbook-worker/automation.png)

Cada função de trabalho de Runbook híbrida é um membro de um grupo de trabalho de Runbook híbrida que forem especificados durante a instalação do agente. Um grupo pode conter um único agente, mas é possível instalar vários agentes num grupo para elevada disponibilidade.

Quando inicia um runbook numa função de trabalho de Runbook híbrida, especifique o grupo que é executado. Cada função de trabalho no grupo de consulta de automatização do Azure para ver se todas as tarefas estão disponíveis. Se uma tarefa estiver disponível, a primeira função de trabalho para obter a tarefa demora-lo. O tempo de processamento da fila de tarefas depende o perfil de hardware de trabalho híbrida e a carga. Não é possível especificar uma função de trabalho específica. Os Runbook Workers híbridos não partilham muitos dos limites que tenham de áreas de segurança do Azure. Não têm os mesmos limites de espaço em disco, memória ou de soquetes de rede. Os Runbook Workers híbridos são apenas limitados pelos recursos na função de trabalho de Runbook híbrida em si. Além disso, os Runbook Workers híbridos não partilham o minuto 180 [justa](automation-runbook-execution.md#fair-share) limite de tempo que fazer de áreas de segurança do Azure. Para saber mais sobre os limites de serviço para áreas de segurança do Azure e os Runbook Workers híbridos, consulte a tarefa [limites](../azure-subscription-service-limits.md#automation-limits) página.

## <a name="install-a-hybrid-runbook-worker"></a>Instalar uma função de trabalho de Runbook híbrida

O processo para instalar uma função de trabalho de Runbook híbrida depende do sistema operacional. A tabela seguinte contém ligações para os métodos que pode utilizar para a instalação.

Para instalar e configurar um Runbook Worker híbrido do Windows, pode usar dois métodos. O método recomendado é utilizar um runbook da automatização para automatizar completamente o processo de configuração de um computador Windows. O segundo método está a seguir um procedimento passo a passo para instalar e configurar a função manualmente. Para máquinas do Linux, executa um script de Python para instalar o agente na máquina.

|SO  |Tipos de implementação  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Para gerir a configuração dos seus servidores que suportam a função de trabalho de Runbook híbrida com o Desired State Configuration (DSC), terá de adicioná-los como nós de DSC. Para obter mais informações sobre integração-los para a gestão com DSC, veja [integrar computadores para gestão pelo Azure Automation DSC](automation-dsc-onboarding.md).
>
>Se ativar a [solução de gestão de atualizações](automation-update-management.md), qualquer computador que está ligado à sua área de trabalho do Log Analytics do Azure é automaticamente configurado como uma função de trabalho de Runbook híbrida para suportar os runbooks incluídos nesta solução. No entanto, o computador não está registado com quaisquer grupos de função de trabalho híbrida já definidos na sua conta de automatização. O computador pode ser adicionado a um grupo de trabalho de Runbook híbrida na conta de automatização para suportar runbooks de automatização, desde que estiver a utilizar a mesma conta para a solução e a associação ao grupo de trabalho de Runbook híbrida. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da função de trabalho de Runbook de híbrida.

Reveja os [informações para planeamento da sua rede](#network-planning) antes de começar a implementar uma função de trabalho de Runbook híbrida. Depois de implementar com êxito a função de trabalho, reveja [executar runbooks numa função de trabalho de Runbook híbrida](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos no seu datacenter no local ou outro ambiente de cloud.

## <a name="remove-a-hybrid-runbook-worker"></a>Remover uma função de trabalho de Runbook híbrida

Pode remover um ou mais operadores de Runbook híbrida de um grupo ou pode remover o grupo, dependendo das suas necessidades. Para remover uma função de trabalho de Runbook híbrida de um computador no local, utilize os seguintes passos:

1. No portal do Azure, aceda à sua conta de automatização.
2. Sob **definições de conta**, selecione **chaves** e anote os valores para **URL** e **chave de acesso primária**. Estas informações são necessárias para a próxima etapa.

### <a name="windows"></a>Windows

Abra uma sessão do PowerShell no modo de administrador e execute o seguinte comando. Utilize o **-Verbose** mudar para um log detalhado do processo de remoção.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Para remover máquinas obsoletas do seu grupo de função de trabalho híbrida, utilize o opcional `machineName` parâmetro.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Pode usar o comando `ls /var/opt/microsoft/omsagent` na função de trabalho de Runbook híbrida para obter o workspaceid. Existe uma pasta no diretório no qual o nome da pasta é a área de trabalho ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Esse código não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração de função de trabalho de Runbook híbrida.

## <a name="remove-a-hybrid-worker-group"></a>Remover um grupo de função de trabalho híbrida

Para remover um grupo, tem primeiro de remover a função de trabalho de Runbook híbrida de cada computador que seja membro do grupo, utilizando o procedimento mostrado anteriormente. Em seguida, utilize os seguintes passos para remover o grupo:

1. Abra a conta de automatização no portal do Azure.
2. Sob **automatização de processos**, selecione **grupos de trabalho híbrido**. Selecione o grupo que pretende eliminar. É apresentada a página de propriedades para esse grupo.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades para o grupo selecionado, selecione **eliminar**. Uma mensagem lhe pedir para confirmar esta ação. Selecione **Sim** se tiver a certeza de que deseja continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este processo pode demorar vários segundos a concluir. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="network-planning"></a>Configurar a rede

### <a name="hybrid-worker-role"></a>Função de trabalho híbrida

Para o Runbook Worker híbrido ligar e registar com os registos do Azure Monitor, tem de ter acesso para o número de porta e os URLs descritos nesta secção. Este acesso é na parte superior para o [portas e URLs necessários para o Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) para ligar aos registos do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se utilizar um servidor proxy para comunicação entre o agente e o serviço do Azure Monitor, certifique-se de que os recursos adequados estão acessíveis. Se utilizar uma firewall para restringir o acesso à internet, tem de configurar a firewall para permitir o acesso. Se utilizar o gateway do Log Analytics como um proxy, certifique-se de que está configurado para funções de trabalho híbridas. Para obter instruções sobre como fazer isso, consulte [configurar o gateway do Log Analytics para funções de trabalho de híbrida de automatização](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway#configure-for-automation-hybrid-workers).

Os seguintes URLs e portas são necessárias para a função de trabalho de Runbook híbrida comunicar com a automatização:

* Porta: Apenas a TCP 443 é necessária para acesso de internet de saída.
* Global URL: *. Azure-Automation.NET
* URL global do US Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Recomenda-se para utilizar os endereços listados quando definir exceções. Para endereços IP, pode baixar o [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente e tem os intervalos implementados atualmente e as alterações futuras para os intervalos de IP.

Se tiver uma conta de automatização que está definida para uma região específica, pode restringir a comunicação para esse datacenter regional. A tabela seguinte fornece o registo DNS para cada região:

| **Região** | **Registo de DNS** |
| --- | --- |
| EUA Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| EUA Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| EUA Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| EUA Oeste 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Reino Unido Sul | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gov (US) - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes de região, transfira o [endereço IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653) arquivo XML do Microsoft Download Center.

> [!NOTE]
> O ficheiro XML de endereço IP do Datacenter do Azure apresenta uma lista de intervalos de endereços IP que são utilizados nos datacenters do Microsoft Azure. O arquivo inclui os intervalos de computação, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete os intervalos implementados atualmente e as alterações futuras para os intervalos de IP. Os novo intervalos que aparecem no ficheiro não são utilizados nos centros de dados para, pelo menos, uma semana.
>
> É uma boa idéia para transferir o ficheiro XML novo todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em execução no Azure. Os utilizadores do Azure ExpressRoute devem observar que este ficheiro é utilizado para atualizar o anúncio de protocolo BGP (Border Gateway) do espaço Azure na primeira semana de cada mês.

### <a name="update-management"></a>Gestão de Atualizações

Com base no padrão endereços e portas que requer a função de trabalho de Runbook híbrida, os seguintes endereços são obrigatórios especificamente para a gestão de atualizações. Comunicação para estes endereços é feita através da porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Resolução de problemas

Para saber como resolver problemas de suas funções de trabalho de Runbook híbridas, consulte [resolução de problemas os Runbook Workers híbridos](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>Passos Seguintes

Para saber como configurar os seus runbooks para automatizar processos no seu datacenter no local ou outro ambiente de cloud, veja [executar runbooks numa função de trabalho de Runbook híbrida](automation-hrw-run-runbooks.md).

