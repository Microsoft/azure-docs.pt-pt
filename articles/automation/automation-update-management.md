---
title: Gerenciamento de Atualizações solução no Azure
description: Este artigo destina-se a ajudá-lo a entender como usar a solução de Gerenciamento de Atualizações do Azure para gerenciar atualizações para seus computadores Windows e Linux.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1751e8d67f59285d011df33a2d4d1d6d8abcec6a
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376069"
---
# <a name="update-management-solution-in-azure"></a>Gerenciamento de Atualizações solução no Azure

Você pode usar a solução Gerenciamento de Atualizações na automação do Azure para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure, em ambientes locais ou em outros provedores de nuvem. Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e gerir o processo de instalação de atualizações necessárias para os servidores.

Você pode habilitar Gerenciamento de Atualizações para máquinas virtuais diretamente da sua conta de automação do Azure. Para saber como habilitar Gerenciamento de Atualizações para máquinas virtuais de sua conta de automação, consulte [gerenciar atualizações para várias máquinas virtuais](manage-update-multi.md). Você também pode habilitar Gerenciamento de Atualizações para uma máquina virtual na página de máquina virtual no portal do Azure. Esse cenário está disponível para máquinas virtuais [Linux](../virtual-machines/linux/tutorial-config-management.md#enable-update-management) e [Windows](../virtual-machines/windows/tutorial-config-management.md#enable-update-management) .

> [!NOTE]
> A solução Gerenciamento de Atualizações requer a vinculação de um espaço de trabalho Log Analytics à sua conta de automação. Para obter uma lista definitiva de regiões com suporte, consulte [mapeamentos de espaço de trabalho do Azure](./how-to/region-mappings.md). Os mapeamentos de região não afetam a capacidade de gerenciar máquinas virtuais em uma região separada do que a sua conta de automação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>Descrição geral da solução

Os computadores gerenciados pelo Gerenciamento de Atualizações usam as seguintes configurações para executar implantações de avaliação e atualização:

* Microsoft Monitoring Agent (MMA) para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux
* Função de Trabalho de Runbook Híbrida da Automatização
* Microsoft Update ou Windows Server Update Services (WSUS) para computadores Windows

O diagrama a seguir mostra uma exibição conceitual do comportamento e do fluxo de dados com o modo como a solução avalia e aplica atualizações de segurança a todos os computadores Windows Server e Linux conectados em um espaço de trabalho:

![Fluxo do processo de Gerenciamento de Atualizações](./media/automation-update-management/update-mgmt-updateworkflow.png)

Gerenciamento de Atualizações pode ser usado para integrar de forma nativa computadores em várias assinaturas no mesmo locatário.

Depois que um pacote é liberado, leva 2-3 horas para que o patch seja exibido para os computadores Linux para avaliação. Para computadores Windows, leva 12-15 horas para que o patch seja mostrado para avaliação após sua liberação.

Depois que um computador conclui uma verificação de conformidade de atualização, o agente encaminha as informações em massa para Azure Monitor logs. Em um computador Windows, a verificação de conformidade é executada a cada 12 horas por padrão.

Além do agendamento da verificação, a verificação de conformidade da atualização é iniciada dentro de 15 minutos do MMA que está sendo reiniciado, antes da instalação da atualização e após a instalação da atualização.

Para um computador Linux, a verificação de conformidade é executada a cada hora por padrão. Se o agente MMA for reiniciado, uma verificação de conformidade será iniciada dentro de 15 minutos.

A solução relata o quão atualizado o computador é baseado em qual fonte você está configurado para sincronizar. Se o computador com Windows estiver configurado para relatar para o WSUS, dependendo de quando o WSUS foi sincronizado pela última vez com o Microsoft Update, os resultados poderão ser diferentes do que as atualizações da Microsoft mostram. Esse comportamento é o mesmo para computadores Linux configurados para relatar a um repositório local, em vez de a um repositório público.

> [!NOTE]
> Para relatar corretamente ao serviço, Gerenciamento de Atualizações exige que determinadas URLs e portas sejam habilitadas. Para saber mais sobre esses requisitos, confira [planejamento de rede para Hybrid Workers](automation-hybrid-runbook-worker.md#network-planning).

Pode criar uma implementação agendada para implementar e instalar atualizações de software em computadores que precisam das atualizações. As atualizações classificadas como *opcionais* não são incluídas no escopo de implantação para computadores Windows. Somente as atualizações necessárias são incluídas no escopo de implantação.

A implantação agendada define quais computadores de destino recebem as atualizações aplicáveis, especificando explicitamente computadores ou selecionando um [grupo](../azure-monitor/platform/computer-groups.md) de computadores baseado em pesquisas de log de um conjunto específico de computadores ou uma [consulta do Azure](automation-update-management-query-logs.md) Isso seleciona dinamicamente as VMs do Azure com base em critérios especificados. Esses grupos são diferentes da [configuração de escopo](../azure-monitor/insights/solution-targeting.md), que é usada apenas para determinar quais computadores obtêm os pacotes de gerenciamento que habilitam a solução.

Você também especifica uma agenda para aprovar e definir um período de tempo durante o qual as atualizações podem ser instaladas. Esse período de tempo é chamado de janela de manutenção. Vinte minutos da janela de manutenção é reservado para reinicializações se uma reinicialização for necessária e você tiver selecionado a opção de reinicialização apropriada. Se a aplicação de patch demorar mais do que o esperado e houver menos de vinte minutos na janela de manutenção, uma reinicialização não ocorrerá.

As atualizações são instaladas por runbooks na Automatização do Azure. Você não pode exibir esses runbooks e os runbooks não exigem nenhuma configuração. Quando uma implantação de atualização é criada, a implantação de atualização cria uma agenda que inicia um runbook de atualização mestre no momento especificado para os computadores incluídos. O runbook mestre inicia um runbook filho em cada agente para instalar as atualizações necessárias.

Na data e hora especificadas na implantação da atualização, os computadores de destino executam a implantação em paralelo. Antes da instalação, uma verificação é executada para verificar se as atualizações ainda são necessárias. Para computadores cliente do WSUS, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará.

Não há suporte para um computador registrado para Gerenciamento de Atualizações em mais de um espaço de trabalho Log Analytics (hospedagem múltipla).

## <a name="clients"></a>Clientes

### <a name="supported-client-types"></a>Tipos de cliente com suporte

A tabela a seguir mostra uma lista de sistemas operacionais com suporte para avaliações de atualização. A aplicação de patch requer um Hybrid Runbook Worker. Para obter informações sobre requisitos de Hybrid Runbook Worker, consulte os guias de instalação do [Windows HRW](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) e [Linux HRW](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker).

|Sistema operativo  |Notas  |
|---------|---------|
|Windows Server 2019 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2016 (datacenter/Data Center Core/Standard)<br><br>Windows Server 2012 R2 (datacenter/padrão)<br><br>Windows Server 2012<br><br>Windows Server 2008 R2 (RTM e SP1 Standard)||
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A aplicação de patches baseada em classificação requer ' yum ' para retornar dados de segurança que o CentOS não está pronto para uso. Para obter mais informações sobre aplicação de patch com base em classificação no CentOS, consulte [Atualizar classificações no Linux](#linux-2)          |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14, 4 LTS, 16, 4 LTS e 18, 4 (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

> [!NOTE]
> Os conjuntos de dimensionamento de máquinas virtuais do Azure podem ser gerenciados com Gerenciamento de Atualizações. Gerenciamento de Atualizações funciona nas próprias instâncias e não na imagem base. Você precisará agendar as atualizações de forma incremental, como não atualizar todas as instâncias de VM ao mesmo tempo.
> Nós VMSS podem ser adicionados seguindo as etapas em [carregar um computador não Azure](automation-tutorial-installed-software.md#onboard-a-non-azure-machine).

### <a name="unsupported-client-types"></a>Tipos de cliente sem suporte

A tabela a seguir lista os sistemas operacionais que não têm suporte:

|Sistema operativo  |Notas  |
|---------|---------|
|Cliente Windows     | Não há suporte para sistemas operacionais cliente (como o Windows 7 e Windows 10).        |
|Windows Server 2016 nano Server     | Não suportado.       |
|Nós do serviço kubernetes do Azure | Não suportado. Use o processo de aplicação de patch detalhado em [aplicar segurança e atualizações de kernel a nós do Linux no serviço kubernetes do Azure (AKs)](../aks/node-updates-kured.md)|

### <a name="client-requirements"></a>Requisitos do cliente

As informações a seguir descrevem os requisitos de cliente específicos do sistema operacional.  Você também deve examinar o [planejamento de rede](#ports) para obter mais diretrizes.

#### <a name="windows"></a>Windows

Os agentes do Windows devem ser configurados para se comunicar com um servidor WSUS ou devem ter acesso ao Microsoft Update.

Você pode usar Gerenciamento de Atualizações com System Center Configuration Manager. Para saber mais sobre cenários de integração, consulte [integrar System Center Configuration Manager com gerenciamento de atualizações](oms-solution-updatemgmt-sccmintegration.md#configuration). O [agente do Windows](../azure-monitor/platform/agent-windows.md) é necessário. O agente será instalado automaticamente se você estiver integrando uma máquina virtual do Azure.

Por padrão, as máquinas virtuais do Windows que são implantadas no Azure Marketplace são definidas para receber atualizações automáticas do serviço Windows Update. Esse comportamento não é alterado quando você adiciona essa solução ou adiciona máquinas virtuais do Windows ao seu espaço de trabalho. Se você não gerenciar ativamente as atualizações usando essa solução, o comportamento padrão (para aplicar atualizações automaticamente) será aplicado.

> [!NOTE]
> É possível que um usuário modifique Política de Grupo para que as reinicializações do computador só possam ser executadas pelo usuário, não pelo sistema. Os computadores gerenciados podem ficar presos, se Gerenciamento de Atualizações não tiver direitos para reinicializar o computador sem interação manual do usuário.
>
> Para obter mais informações, consulte [definir configurações de política de grupo para atualizações automáticas](https://docs.microsoft.com/en-us/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Para o Linux, o computador deve ter acesso a um repositório de atualização. O repositório de atualizações pode ser privado ou público. O TLS 1,1 ou o TLS 1,2 é necessário para interagir com Gerenciamento de Atualizações. Não há suporte para um agente Log Analytics para Linux configurado para relatar mais de um espaço de trabalho de Log Analytics com esta solução.  O computador também deve ter o Python 2. x instalado.

Para obter informações sobre como instalar o agente de Log Analytics para Linux e baixar a versão mais recente, consulte [log Analytics Agent para Linux](https://github.com/microsoft/oms-agent-for-linux). Para obter informações sobre como instalar o agente do Log Analytics para Windows, consulte [Microsoft Monitoring Agent para Windows](../log-analytics/log-analytics-windows-agent.md).

As máquinas virtuais que foram criadas a partir das imagens de Red Hat Enterprise Linux sob demanda (RHEL) que estão disponíveis no Azure Marketplace são registradas para acessar a [RHUI (infraestrutura de atualização do Red Hat)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) implantada no Azure. Qualquer outra distribuição do Linux deve ser atualizada a partir do repositório de arquivos online da distribuição, seguindo os métodos com suporte da distribuição.

## <a name="permissions"></a>Permissões

Para criar e gerenciar implantações de atualização, você precisa de permissões específicas. Para saber mais sobre essas permissões, consulte [acesso baseado em função-gerenciamento de atualizações](automation-role-based-access-control.md#update-management).

## <a name="solution-components"></a>Componentes da solução

A solução consiste nos seguintes recursos. Os recursos são adicionados à sua conta de automação. Eles são agentes conectados diretamente ou em um grupo de gerenciamento conectado a Operations Manager.

### <a name="hybrid-worker-groups"></a>Grupos de Função de Trabalho Híbrida

Depois de habilitar essa solução, qualquer computador com Windows conectado diretamente ao seu espaço de trabalho Log Analytics será automaticamente configurado como um Hybrid Runbook Worker para dar suporte aos runbooks incluídos nesta solução.

Cada computador Windows gerenciado pela solução é listado no painel **grupos do Hybrid Worker** como um **grupo de trabalho híbrido do sistema** para a conta de automação. As soluções usam o nome de *host*da Convenção de nomenclatura FQDN_GUID. Você não pode direcionar esses grupos com runbooks em sua conta. Eles falharão se você tentar. Esses grupos têm o objetivo de dar suporte apenas à solução de gerenciamento.

Você pode adicionar os computadores Windows a um grupo de Hybrid Runbook Worker em sua conta de automação para dar suporte a runbooks de automação se usar a mesma conta para a solução e a associação de grupo de Hybrid Runbook Worker. Essa funcionalidade foi adicionada na versão 7.2.12024.0 do Hybrid Runbook Worker.

### <a name="management-packs"></a>Pacotes de gestão

Se o grupo de gerenciamento do System Center Operations Manager estiver conectado a um espaço de trabalho do Log Analytics, os pacotes de gerenciamento a seguir serão instalados no Operations Manager. Esses pacotes de gerenciamento também são instalados em computadores com Windows conectados diretamente depois que você adiciona a solução. Você não precisa configurar ou gerenciar esses pacotes de gerenciamento.

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

> [!NOTE]
> Se você tiver um grupo de gerenciamento Operations Manager 1807 ou 2019 com agentes configurados no nível do grupo de gerenciamento para ser associado a um espaço de trabalho, a solução alternativa atual para que eles apareçam é substituir **IsAutoRegistrationEnabled** por **true** em a regra **Microsoft. IntelligencePacks. AzureAutomation. HybridAgent. init** .

Para obter mais informações sobre como os pacotes de gerenciamento de solução são atualizados, consulte [conectar Operations Manager a logs de Azure monitor](../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Para sistemas com o agente do Operations Manager, para poder ser totalmente gerenciados pelo Gerenciamento de Atualizações, o agente precisa ser atualizado para o Microsoft Monitoring Agent. Para saber como atualizar o agente, consulte [como atualizar um agente de Operations Manager](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents). Para ambientes que usam Operations Manager, é necessário que você esteja executando o System Center Operations Manager 2012 R2 UR 14 ou posterior.

## <a name="data-collection"></a>Recolha de dados

### <a name="supported-agents"></a>Agentes suportados

A tabela a seguir descreve as fontes conectadas que têm suporte nesta solução:

| Origem ligada | Suportadas | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Windows e, em seguida, inicia a instalação de atualizações necessárias. |
| Agentes do Linux |Sim |A solução coleta informações sobre atualizações do sistema de agentes do Linux e, em seguida, inicia a instalação de atualizações necessárias em distribuições com suporte. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br/>Não é necessária uma conexão direta do agente de Operations Manager para os logs do Azure Monitor. Os dados são encaminhados do grupo de gerenciamento para o espaço de trabalho Log Analytics. |

### <a name="collection-frequency"></a>Frequência da recolha

Uma verificação é executada duas vezes por dia para cada computador gerenciado do Windows. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o status foi alterado. Se o status for alterado, uma verificação de conformidade será iniciada.

Uma verificação é executada a cada hora para cada computador Linux gerenciado.

Pode levar entre 30 minutos e 6 horas para que o painel exiba dados atualizados de computadores gerenciados.

A Azure Monitor média registra o uso de dados de um computador usando Gerenciamento de Atualizações é de aproximadamente 25 MB por mês. Esse valor é apenas uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

## <a name="ports"></a>Planejamento de rede

Os endereços a seguir são necessários especificamente para Gerenciamento de Atualizações. A comunicação com esses endereços ocorre na porta 443.

|Público do Azure  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

Para computadores Windows, você também deve permitir o tráfego para qualquer ponto de extremidade exigido pelo Windows Update.  Você pode encontrar uma lista atualizada de pontos de extremidade necessários em [problemas relacionados ao http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se você tiver um [servidor de Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)local, também deverá permitir o tráfego para o servidor especificado em sua [chave do WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Para computadores Red Hat Linux, consulte [os IPs para os servidores de distribuição de conteúdo RHUI para os](../virtual-machines/linux/update-infrastructure-redhat.md#the-ips-for-the-rhui-content-delivery-servers) pontos de extremidade necessários. Para outras distribuições do Linux, consulte a documentação do provedor.

Para obter mais informações sobre as portas que o Hybrid Runbook Worker requer, consulte [Hybrid Worker portas de função](automation-hybrid-runbook-worker.md#hybrid-worker-role).

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, você pode baixar os [intervalos de IP do Microsoft Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Siga as instruções em [conectar computadores sem acesso à Internet](../azure-monitor/platform/gateway.md) para configurar computadores que não têm acesso à Internet.

## <a name="view-update-assessments"></a>Exibir avaliações de atualização

Na sua conta de automação, selecione **Gerenciamento de atualizações** para exibir o status de seus computadores.

Esta exibição fornece informações sobre seus computadores, atualizações ausentes, implantações de atualização e implantações de atualização agendada. Na **coluna conformidade**, você pode ver a última vez em que o computador foi avaliado. Na coluna **prontidão do agente de atualização** , você pode ver se a integridade do agente de atualização. Se houver um problema, selecione o link para acessar a documentação de solução de problemas que pode ajudá-lo a saber quais etapas executar para corrigir o problema.

Para executar uma pesquisa de logs que retorna informações sobre a máquina, atualização ou implantação, selecione o item na lista. O painel **pesquisa de log** é aberto com uma consulta para o item selecionado:

![Gerenciamento de Atualizações exibição padrão](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Exibir atualizações ausentes

Selecione **atualizações ausentes** para exibir a lista de atualizações ausentes em seus computadores. Cada atualização é listada e pode ser selecionada. As informações sobre o número de computadores que exigem a atualização, o sistema operacional e um link para obter mais informações são mostradas. O painel **pesquisa de log** mostra mais detalhes sobre as atualizações.

![Atualizações ausentes](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificações de atualização

As tabelas a seguir listam as classificações de atualização no Gerenciamento de Atualizações, com uma definição para cada classificação.

### <a name="windows"></a>Windows

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas     | Uma atualização para um problema específico que aborda um bug crítico não relacionado à segurança.        |
|Atualizações de segurança     | Uma atualização para um problema específico de um produto e relacionado à segurança.        |
|Update rollups     | Um conjunto cumulativo de hotfixes que são empacotados em conjunto para facilitar a implantação.        |
|Pacotes de funcionalidades     | Novos recursos do produto que são distribuídos fora de uma versão do produto.        |
|Service packs     | Um conjunto cumulativo de hotfixes que são aplicados a um aplicativo.        |
|Atualizações de definições     | Uma atualização para vírus ou outros arquivos de definição.        |
|Ferramentas     | Um utilitário ou recurso que ajuda a concluir uma ou mais tarefas.        |
|Atualizações     | Uma atualização para um aplicativo ou arquivo que está instalado no momento.        |

### <a name="linux-2"></a>Linux

|Classificação  |Descrição  |
|---------|---------|
|Atualizações críticas e de segurança     | Atualizações para um problema específico ou um problema específico do produto, relacionado à segurança.         |
|Outras atualizações     | Todas as outras atualizações que não são críticas por natureza ou não são atualizações de segurança.        |

Para o Linux, Gerenciamento de Atualizações pode distinguir entre atualizações críticas e de segurança na nuvem enquanto exibe dados de avaliação devido ao enriquecimento de dados na nuvem. Para aplicação de patch, Gerenciamento de Atualizações depende dos dados de classificação disponíveis no computador. Ao contrário de outras distribuições, o CentOS não tem essas informações disponíveis prontamente. Se você tiver computadores CentOS configurados de forma a retornar dados de segurança para o comando a seguir, Gerenciamento de Atualizações poderá aplicar patches com base nas classificações.

```bash
sudo yum -q --security check-update
```

Atualmente, não há método com suporte para habilitar a classificação nativa-disponibilidade de dados no CentOS. Neste momento, apenas o suporte de melhor esforço é fornecido aos clientes que podem ter habilitado isso por conta própria.

## <a name="integrate-with-system-center-configuration-manager"></a>Integrar no System Center Configuration Manager

Os clientes que investiram em System Center Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e da maturidade de Configuration Manager para ajudá-los a gerenciar atualizações de software. Configuration Manager faz parte do ciclo de gerenciamento de atualização de software (SUM).

Para saber como integrar a solução de gerenciamento com o System Center Configuration Manager, consulte [integrar System Center Configuration Manager com gerenciamento de atualizações](oms-solution-updatemgmt-sccmintegration.md).

### <a name="third-party-patches-on-windows"></a>Patches de terceiros no Windows

Gerenciamento de Atualizações se baseia no repositório de atualização configurado localmente para corrigir os sistemas Windows com suporte. Este é o WSUS ou o Windows Update. Ferramentas como [System Center Updates Publisher](/sccm/sum/tools/updates-publisher) (Updates Publisher) permitem que você publique atualizações personalizadas no WSUS. Esse cenário permite que Gerenciamento de Atualizações patch de máquinas que usam System Center Configuration Manager como seu repositório de atualizações com software de terceiros. Para saber como configurar o Updates Publisher, consulte [instalar o Updates Publisher](/sccm/sum/tools/install-updates-publisher).

## <a name="patch-linux-machines"></a>Corrigir computadores Linux

As seções a seguir explicam possíveis problemas com a aplicação de patch do Linux.

### <a name="unexpected-os-level-upgrades"></a>Atualizações inesperadas no nível do sistema operacional

Em algumas variantes do Linux, como Red Hat Enterprise Linux, as atualizações no nível do sistema operacional podem ocorrer por meio de pacotes. Isso pode levar a Gerenciamento de Atualizações execuções em que o número de versão do sistema operacional é alterado. Como Gerenciamento de Atualizações usa os mesmos métodos para atualizar os pacotes que um administrador usaria localmente no computador Linux, esse comportamento é intencional.

Para evitar a atualização da versão do sistema operacional por meio de Gerenciamento de Atualizações execuções, use o recurso de **exclusão** .

Em Red Hat Enterprise Linux, o nome do pacote a ser excluído é redhat-Release-Server. x86_64.

![Pacotes a serem excluídos para Linux](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>Os patches críticos/de segurança não são aplicados

Ao implantar atualizações em um computador Linux, você pode selecionar classificações de atualização. Isso filtra as atualizações que são aplicadas ao computador que atende aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como Gerenciamento de Atualizações executa o enriquecimento de atualização na nuvem, algumas atualizações podem ser sinalizadas em Gerenciamento de Atualizações como tendo impacto sobre a segurança, mesmo que o computador local não tenha essas informações. Como resultado, se você aplicar atualizações críticas a um computador Linux, pode haver atualizações que não estão marcadas como tendo impacto na segurança nesse computador e as atualizações não são aplicadas.

No entanto, Gerenciamento de Atualizações ainda pode relatar que a máquina não está em conformidade porque tem informações adicionais sobre a atualização relevante.

A implantação de atualizações por classificação de atualização não funciona no CentOS de fora da caixa. Para implantar atualizações para CentOS corretamente, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para o SUSE, selecionar *apenas* "outras atualizações" como a classificação pode resultar em algumas atualizações de segurança também sendo instaladas se as atualizações de segurança relacionadas ao zypper (Gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, talvez seja necessário executar novamente a implantação da atualização. Para verificar, verifique o log de atualizações.

### <a name="multi-tenant"></a>Implantações de atualização entre locatários

Se você tiver computadores em outro relatório de locatário do Azure para Gerenciamento de Atualizações que você precisa aplicar patch, você precisará usar a solução alternativa a seguir para obtê-los agendados. Você pode usar o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) com a opção `-ForUpdate` para criar um agendamento e usar o cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) e passar os computadores no outro locatário para o parâmetro `-NonAzureComputer`. O exemplo a seguir mostra um exemplo de como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="onboard"></a>Habilitar Gerenciamento de Atualizações

Para iniciar a aplicação de patches em sistemas, você precisa habilitar a solução Gerenciamento de Atualizações. Há várias maneiras de carregar computadores para Gerenciamento de Atualizações. A seguir estão as maneiras recomendadas e com suporte para integrar a solução:

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [De navegar por vários computadores](automation-onboard-solutions-from-browse.md)
* [Da sua conta de automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de automação do Azure](automation-onboard-solutions.md)

## <a name="next-steps"></a>Passos seguintes

Continue no tutorial para saber como gerenciar atualizações para suas máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para suas VMs do Windows do Azure](automation-tutorial-update-management.md)

* Use pesquisas de log em [logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados de atualização detalhados.
* [Criar alertas](automation-tutorial-update-management.md#configure-alerts) para status de implantação de atualização.

* Para saber como interagir com Gerenciamento de Atualizações por meio da API REST, consulte [configurações de atualização de software](/rest/api/automation/softwareupdateconfigurations)
* Para saber como solucionar problemas de seu Gerenciamento de Atualizações, consulte [solução de problemas gerenciamento de atualizações](troubleshoot/update-management.md)
