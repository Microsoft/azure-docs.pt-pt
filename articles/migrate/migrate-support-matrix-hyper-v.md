---
title: Suporte para avaliação de Hiper-V em Migração Azure
description: Conheça o suporte para avaliação hyper-V com avaliação do servidor migratório Azure
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 61d8e635a32024ba5afabb34fefa5bf169e2911f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336917"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação de Hiper-V

Este artigo resume os requisitos pré-requisitos e requisitos de apoio para avaliar os VMs hiper-V em preparação para a migração para Azure. Se quiser migrar VMs Hiper-V para Azure, reveja a matriz de apoio à [migração](migrate-support-matrix-hyper-v-migration.md).

Avalia os VMs Hiper-V com a ferramenta de avaliação do [servidor Azure Migrate:Server.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Cria-se um projeto Azure Migrate e, em seguida, adiciona-se a ferramenta ao projeto. Depois de adicionada a ferramenta, implante o [aparelho Migratório Azure](migrate-appliance.md). O aparelho descobre continuamente as máquinas no local e envia metadados de máquinas e dados de desempenho para o Azure. Após a descoberta da máquina, você reúne máquinas descobertas em grupos, e faz uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação** | Você pode descobrir e avaliar até 35.000 VMs Hiper-V em um único [projeto Azure Migrate](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Além de Hiper-VMs, um projeto pode incluir VMware VMs e servidores físicos, até os limites de avaliação para cada um.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 5000 VMs Hiper-V.<br/><br/> O aparelho pode ligar até 300 hospedeiros Hyper-V.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Você pode avaliar até 35.000 VMs numa única avaliação para um grupo.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.



## <a name="hyper-v-host-requirements"></a>Requisitos de hospedeiro hiper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Anfitrião Hyper-V**       | O hospedeiro Hyper-V pode ser autónomo ou implantado num aglomerado.<br/><br/> O anfitrião Hyper-V pode executar o Windows Server 2019, o Windows Server 2016 ou o Windows Server 2012 R2.<br/> Não pode avaliar as VMs localizadas em sistemas anfitriões Hyper-V com o Windows Server 2012.
| **Permissões**           | Precisa de permissões do Administrador no hospedeiro hyper-V. <br/> Se não quiser atribuir permissões ao Administrador, crie uma conta de utilizador local ou de domínio e adicione a conta de utilizador a estes grupos- Utilizadores de Gestão Remota, Administradores Hiper-V e Utilizadores do Monitor de Desempenho. |
| **Comunicação remota do PowerShell**   | [O remo PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) deve ser ativado em cada hospedeiro Hyper-V. |
| **Réplica do Hyper-V**       | Se utilizar a Réplica Hyper-V (ou tiver vários VMs com os mesmos identificadores VM), e descobrir tanto os VMs originais como os replicados utilizando o Azure Migrate, a avaliação gerada pela Azure Migrate pode não ser exata. |


## <a name="hyper-v-vm-requirements"></a>Requisitos de VM hiper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema Operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) suportados pelo Azure. |
| **Serviços de Integração**       | Os [Serviços de Integração Hiper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar a funcionar em VMs que avalia, de modo a capturar informações do sistema operativo. |


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. Pode utilizar o aparelho utilizando um VHd Hiper-V comprimido que descarrega a partir do portal ou utilizando um script PowerShell.

- Conheça os [requisitos](migrate-appliance.md#appliance---hyper-v) do aparelho para hyper-V.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos do porto para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389 para permitir ligações remotas ao aparelho.<br/><br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Ligações de saída nas portas 443 (HTTPS), para enviar metadados de descoberta e desempenho para o Azure Migrate.
**Hospedeiro/cluster hiper-V** | Ligações de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS), para retirar metadados e dados de desempenho para VMs Hiper-V utilizando uma sessão de Modelo de Informação Comum (CIM).

## <a name="agentless-dependency-analysis-requirements"></a>Requisitos de análise de dependência sem agente

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência sem agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de configurar um aparelho Azure Migrate para descobrir as suas máquinas VMWare no local.<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> [Aprenda](how-to-set-up-appliance-vmware.md) a configurar o aparelho Azure Migrate para avaliação de VMware VMs.
**Suporte vm** | Atualmente suportado apenas para VMware VMs.
**VMs do Windows** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bit).
**Conta do Windows** |  Para análise de dependência, o aparelho Azure Migrate necessita de uma conta local ou de administrador de domínio para aceder a VMs windows.
**VMs do Linux** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debiano 7, 8<br/> Oracle Linux 6, 7<br/> Centos 5, 6, 7.
**Conta Linux** | Para análise de dependência, nas máquinas Linux o aparelho Azure Migrate precisa de uma conta de utilizador com privilégio raiz.<br/><br/> Alternadamente, a conta de utilizador necessita destas permissões em ficheiros /bin/netstat e /bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.
**Agentes necessários** | Não é necessário um agente nas máquinas que queira analisar.
**Ferramentas VMware** | As Ferramentas VMware (mais tardar 10.2) devem ser instaladas e em funcionamento em cada VM que pretende analisar.
**vCenter Server** | A visualização da dependência necessita de uma conta vCenter Server com acesso apenas para leitura, e privilégios habilitados para Máquinas Virtuais > Operações de Hóspedes. **ANFITRIÕES ESXi**: Em ESXi acolhe VMs em execução que pretende analisar, o aparelho Azure Migrate deve ser capaz de ligar à porta TCP 443.

## <a name="agent-based-dependency-analysis-requirements"></a>Requisitos de análise da dependência baseados em agentes

[A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar dependências entre máquinas no local que pretende avaliar e migrar para Azure. A tabela resume os requisitos para a criação de uma análise de dependência baseada no agente. 

**Requisito** | **Detalhes** 
--- | --- 
**Antes da implantação** | Deverá ter um projeto Azure Migrate em vigor, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto.<br/><br/>  Implementa a visualização da dependência depois de criar um aparelho Azure Migrate para descobrir as suas máquinas no local<br/><br/> [Aprenda](create-manage-projects.md) a criar um projeto pela primeira vez.<br/> [Aprenda](how-to-assess.md) a adicionar uma ferramenta de avaliação a um projeto existente.<br/> Aprenda a configurar o aparelho Azure Migrate para avaliação de [Hiper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)ou servidores físicos.
**Azure Government** | A visualização da dependência não está disponível no Governo de Azure.
**Log Analytics** | A Azure Migrate utiliza a solução [Service Map](../operations-management-suite/operations-management-suite-service-map.md) em [registos do Monitor Azure](../log-analytics/log-analytics-overview.md) para visualização da dependência.<br/><br/> Associa um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado. <br/><br/> O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou na Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.<br/><br/> O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migração e o nome do projeto.
**Agentes necessários** | Em cada máquina que pretende analisar, instale os seguintes agentes:<br/><br/> O agente de monitorização da [Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> O [agente da Dependência.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Se as máquinas no local não estiverem ligadas à internet, é necessário descarregar e instalar o gateway Log Analytics.<br/><br/> Saiba mais sobre a instalação do [agente dependency](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) e [do MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Custos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (a partir do dia em que associa o espaço de trabalho log Analytics ao projeto Azure Migrate)/<br/><br/> Após 180 dias, aplicar-se-ão as cobranças padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em [encargos padrão](https://azure.microsoft.com/pricing/details/log-analytics/) para o Log Analytics.<br/><br/> Quando o projeto Azure Migrate é eliminado, o espaço de trabalho não é eliminado juntamente com ele. Após a exclusão do projeto, o uso do Mapa de Serviço sem graça, e cada nó será cobrado de acordo com o nível pago do espaço de trabalho Log Analytics/<br/><br/>Se tiver projetos que criou antes da disponibilidade geral da Azure Migrate (GA- 28 de fevereiro de 2018), poderá ter incorrido em custos adicionais do Mapa de Serviços. Para garantir o pagamento após 180 dias, recomendamos que crie um novo projeto, uma vez que os espaços de trabalho existentes antes da GA ainda são responsáveis.
**Gestão** | Quando regista agentes no espaço de trabalho, utiliza a identificação e a chave fornecidas pelo projeto Azure Migrate.<br/><br/> Pode utilizar o espaço de trabalho log Analytics fora do Azure Migrate.<br/><br/> Se eliminar o projeto azure migrate associado, o espaço de trabalho não é eliminado automaticamente. [Elimine-o manualmente](../azure-monitor/platform/manage-access.md).<br/><br/> Não elimine o espaço de trabalho criado pela Azure Migrate, a menos que apague o projeto Azure Migrate. Se o fizer, a funcionalidade de visualização da dependência não funcionará como esperado.
**Conectividade Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar a porta de entrada log analytics.

## <a name="next-steps"></a>Passos seguintes

[Prepare-se para avaliação de VM hiper-V](tutorial-prepare-hyper-v.md)
