---
title: Função de Trabalho de Runbook Híbrida da Automatização do Azure
description: Este artigo fornece informações sobre a instalação e utilização do Hybrid Runbook Worker, que é uma característica da Azure Automation que pode utilizar para executar livros de execução em máquinas no seu datacenter local ou fornecedor de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 861c7ffa647c8d2f37b32c359253ca991eeb314f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457710"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatizar recursos no seu datacenter ou nuvem utilizando o Trabalhador do Livro de Corridas Híbrido

Os livros de corridas da Azure Automation podem não ter acesso a recursos noutras nuvens ou no seu ambiente no local porque funcionam na plataforma de nuvem Azure. Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar livros de execução diretamente no computador que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. Os livros de execução são armazenados e geridos na Azure Automation e depois entregues a um ou mais computadores atribuídos.

A imagem seguinte ilustra esta funcionalidade:

![Descrição geral das Funções de Trabalho de Runbook Híbridas (Hybrid Runbook Worker overview)](media/automation-hybrid-runbook-worker/automation.png)

Cada Trabalhador De Runbook Híbrido é membro de um grupo híbrido runbook worker que especifica quando instala o agente. Um grupo pode incluir um único agente, mas você pode instalar vários agentes em um grupo para alta disponibilidade. Cada máquina pode acolher um Trabalhador Híbrido reportando a uma conta de Automação.

Quando inicia um livro de corridas num Trabalhador Híbrido do Livro de Corridas, especifica o grupo em que funciona. Cada trabalhador do grupo faz sondagens À Azure Automation para ver se existem postos de trabalho disponíveis. Se um emprego está disponível, o primeiro trabalhador a conseguir o emprego aceita-o. O tempo de processamento da fila de trabalho depende do perfil e carga do trabalhador híbrido. Não pode especificar um trabalhador em particular. Os trabalhadores híbridos não partilham muitos dos limites que as caixas de areia Azure têm. Não têm os mesmos limites para o espaço do disco, memória ou tomadas de rede. Os trabalhadores híbridos do livro de corridas só são limitados pelos recursos do próprio Trabalhador híbrido. Além disso, os Trabalhadores híbridos da Runbook não partilham o limite de 180 minutos de tempo de [partilha justo](automation-runbook-execution.md#fair-share) que as caixas de areia Azure têm. Para saber mais sobre os limites de serviço para caixas de areia Azure e Trabalhadores híbridos de runbook, consulte os [limites de](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)trabalho.

## <a name="install-a-hybrid-runbook-worker"></a>Instale um Trabalhador Híbrido do Livro de Corridas

O processo de instalação de um Trabalhador do Livro De Execução Híbrido depende do sistema operativo. A tabela abaixo define os tipos de implantação.

|SO  |Tipos de implantação  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manual](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

O método de instalação recomendado é utilizar um livro de execução automation para automatizar completamente o processo de configuração de um computador Windows. O segundo método consiste em seguir um procedimento passo a passo para instalar manualmente e configurar a função. Para as máquinas Linux, executa um guião Python para instalar o agente na máquina.

> [!NOTE]
> Para gerir a configuração dos seus servidores que suportam a função de Trabalhador do Livro de Execução Híbrido com a Configuração de Estado Desejado (DSC), precisa de adicionar os servidores como nós DSC. Para obter mais informações sobre o seu embarque para gestão com a DSC, consulte [máquinas de embarque para gestão pela Azure Automation DSC](automation-dsc-onboarding.md).
>
>Se ativar a [solução De Gestão de Atualizações,](automation-update-management.md)qualquer computador ligado ao seu espaço de trabalho Azure Log Analytics é automaticamente configurado como um Trabalhador de Runbook Híbrido para suportar os livros incluídos nesta solução. No entanto, o computador não está registado em nenhum grupo de TrabalhadorEs Híbridos já definido na sua conta De automação. O computador pode ser adicionado a um grupo híbrido Runbook Worker na sua conta Automation para apoiar os livros de automação desde que esteja a usar a mesma conta tanto para a solução como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 do Hybrid Runbook Worker.

Reveja as [informações para planear](#network-planning) a sua rede antes de começar a implementar um Trabalhador Híbrido do Livro de Corridas. Depois de implementar com sucesso o trabalhador, reveja os livros de [execução run num Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros de execução para automatizar processos no seu centro de dados no local ou em outro ambiente de nuvem.

O computador pode ser adicionado a um grupo híbrido Runbook Worker na sua conta Automation para apoiar os livros de automação desde que esteja a usar a mesma conta tanto para a solução como para a associação do grupo Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Remova um trabalhador híbrido do livro de corridas de um computador no local

Pode remover um Trabalhador do Livro Híbrido de um computador no local, tal como descrito nesta secção para Windows e Linux.

### <a name="remove-the-worker-on-windows"></a>Remova o trabalhador no Windows

1. No portal Azure, vá à sua conta de Automação.
2. Em **definições de conta,** selecione **Teclas** e note os valores para **URL** e **Chave de Acesso Primário**.

3. Abra uma sessão PowerShell no modo Administrador e execute o seguinte comando com o seu URL e valores-chave de acesso primário. Utilize `Verbose` o parâmetro para um registo detalhado do processo de remoção. Para remover máquinas velhas do seu grupo `machineName` Hybrid Worker, utilize o parâmetro opcional.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Remova o trabalhador em Linux

Pode utilizar o `ls /var/opt/microsoft/omsagent` comando do Trabalhador do Livro híbrido para obter o ID do espaço de trabalho. É criada uma pasta que é nomeada com o ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Este código não remove o agente Log Analytics para o Linux do computador. Apenas remove a funcionalidade e configuração da função Hybrid Runbook Worker.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido runbook worker, você primeiro precisa remover o Trabalhador do Livro Híbrido de todos os computadores que é membro do grupo. Em seguida, utilize os seguintes passos para remover o grupo:

1. Abra a conta Automation no portal Azure.
2. Selecione **grupos de trabalhadores híbridos** no âmbito **da Automatização de Processos.** Selecione o grupo que pretende eliminar. A página de propriedades para aquele grupo aparece.

   ![Página Propriedades](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Na página de propriedades do grupo selecionado, selecione **Eliminar**. Uma mensagem pede-lhe para confirmar esta ação. Selecione **Sim** se tiver a certeza de que pretende continuar.

   ![Mensagem de confirmação](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Este processo pode levar alguns segundos para terminar. Pode acompanhar o progresso em **Notificações**, no menu.

## <a name="configure-your-network"></a><a name="network-planning"></a>Configure a sua rede

### <a name="hybrid-worker-role"></a>Papel do Trabalhador Híbrido

Para que o Trabalhador do Livro Híbrido se ligue e registe-se com a Automação Azure, deve ter acesso ao número de porta e aos URLs descritos nesta secção. Este acesso está em cima das [portas e URLs necessários para](../azure-monitor/platform/agent-windows.md) que o agente Log Analytics se ligue aos registos do Monitor Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se utilizar um servidor proxy para comunicação entre o agente e o serviço De automação Azure, certifique-se de que os recursos adequados estão acessíveis. O prazo para pedidos do Trabalhador do Livro Híbrido e dos serviços de Automação é de 30 segundos. Após 3 tentativas, o pedido falhará. Se utilizar uma firewall para restringir o acesso à internet, tem de configurar a firewall para permitir o acesso. Se utilizar o gateway Log Analytics como procuração, certifique-se de que está configurado para trabalhadores híbridos. Para obter instruções sobre como fazê-lo, consulte [Configure a porta de entrada log Analytics para automação de trabalhadores híbridos](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

São necessários os seguintes portes e URLs para que o trabalho híbrido do runbook seja comunicado com a Automação:

* Porta: Só é necessário tCP 443 para acesso à Internet de saída.
* URL global: *.azure-automation.net
* URL global do US Gov – Virginia: *.azure-automation.us
* Serviço de\<agente:\>https:// workspaceId .agentsvc.azure-automation.net

É aconselhável usar os endereços listados na definição de exceções. Para endereços IP, pode descarregar os intervalos IP do [Microsoft Azure Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Este ficheiro é atualizado semanalmente, e tem as gamas atualmente implementadas e quaisquer alterações futuras nas gamas IP.

Se tiver uma conta de Automação definida para uma região específica, pode restringir a comunicação a esse datacenter regional. O quadro seguinte fornece o registo dNS para cada região:

| **Região** | **Registo dNS** |
| --- | --- |
| E.U.A. Centro-Oeste | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| E.U.A. Centro-Sul |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| E.U.A. Leste 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| E.U.A.Oeste 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa ocidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa do Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Ásia Sudeste |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Austrália Central |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Leste da Austrália |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes de região, descarregue o ficheiro XML do [endereço IP do Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) do Microsoft Download Center.

> [!NOTE]
> O ficheiro IP do Azure Datacenter XML lista as gamas de endereços IP que são utilizadas nos datacenters do Microsoft Azure. O ficheiro inclui gamas de cálculo, SQL e armazenamento.
>
>Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete as gamas atualmente implantadas e quaisquer alterações futuras nas gamas IP. As novas gamas que aparecem no ficheiro não são usadas nos centros de dados durante pelo menos uma semana.
>
> É uma boa ideia descarregar o novo ficheiro XML todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em funcionamento no Azure. Os utilizadores do Azure ExpressRoute devem notar que este ficheiro é utilizado para atualizar o anúncio do Protocolo border gateway (BGP) do espaço Azure na primeira semana de cada mês.

### <a name="update-management"></a>Gestão de Atualizações

Além dos endereços e portas padrão que o Trabalhador do Livro híbrido necessita, são necessários os seguintes endereços especificamente para gestão de atualizações. A comunicação a estes endereços é feita sobre a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros de execução para automatizar processos no seu centro de dados no local ou em outro ambiente em nuvem, consulte [run run run book em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Para aprender a resolver os seus Trabalhadores Híbridos, consulte os [Trabalhadores híbridos de runbook de resolução de problemas.](troubleshoot/hybrid-runbook-worker.md#general)