---
title: Suporte para avaliação de Hiper-V em Migração Azure
description: Saiba mais sobre o suporte para avaliação hyper-V com a Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 9c1228992d71e56b9118e88967478e619c14959a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393313"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação de Hiper-V

Este artigo resume as definições de suporte e limitações para avaliar os VMs Hiper-V com [o Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se procura informações sobre vMs hiper-V migratórios para Azure, reveja a matriz de apoio à [migração.](migrate-support-matrix-hyper-v-migration.md)

## <a name="overview"></a>Descrição geral

Para avaliar as máquinas no local para migração para Azure com este artigo, adicione a ferramenta Azure Migrate: Server Assessment a um projeto Azure Migrate. Implante o [aparelho Azure Migrate](migrate-appliance.md). O aparelho descobre continuamente as máquinas no local e envia dados de configuração e desempenho para o Azure. Após a descoberta da máquina, você reúne máquinas descobertas em grupos, e faz uma avaliação para um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 VMs Hiper-V num único [projeto.](migrate-support-matrix.md#azure-migrate-projects)
**Limites do projeto** | Pode criar vários projetos numa subscrição do Azure. Um projeto pode incluir VMware VMs, Hiper-V VMs e servidores físicos, até aos limites de avaliação.
**Descoberta** | O aparelho Azure Migrate pode descobrir até 5000 VMs Hiper-V.<br/><br/> O aparelho pode ligar até 300 hospedeiros Hyper-V.
**Avaliação** | Pode adicionar até 35.000 máquinas num único grupo.<br/><br/> Você pode avaliar até 35.000 VMs numa única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre avaliações.



## <a name="hyper-v-host-requirements"></a>Requisitos de hospedeiro hiper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação do hospedeiro**       | O hospedeiro Hyper-V pode ser autónomo ou implantado num aglomerado. |
| **Permissões**           | Precisa de permissões de administrador no hospedeiro hyper-V. <br/> Em alternativa, se não quiser atribuir permissões ao Administrador, crie uma conta de utilizador local ou de domínio e adicione o utilizador a estes grupos- Utilizadores de Gestão Remota, Administradores Hiper-V e Utilizadores do Monitor de Desempenho. |
| **Sistema operativo anfitrião** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/> Não pode avaliar as VMs localizadas em sistemas anfitriões Hyper-V com o Windows Server 2012. |
| **RemopowerShell**   | Deve ser ativado em cada hospedeiro. |
| **Réplica hiper-V**       | Se utilizar a Réplica Hyper-V (ou tiver vários VMs com os mesmos identificadores VM) e descobrir tanto os VMs originais como os replicados utilizando o Azure Migrate, a avaliação gerada pela Azure Migrate pode não ser exata. |


## <a name="hyper-v-vm-requirements"></a>Requisitos de VM hiper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os sistemas operativos [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) que são suportados pelo Azure. |
| **Serviços de Integração**       | Os [Serviços de Integração Hiper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar a funcionar em VMs que avalia, de modo a capturar informações do sistema operativo. |


## <a name="azure-migrate-appliance-requirements"></a>Requisitos de aplicação do Azure Migrate

A Azure Migrate utiliza o [aparelho Migratório Azure](migrate-appliance.md) para descoberta e avaliação. O aparelho para Hyper-V funciona num VM Hiper-V e é implantado utilizando um VHD Hiper-V comprimido que você descarrega a partir do portal Azure. 

- Conheça os [requisitos](migrate-appliance.md#appliance---hyper-v) do aparelho para hyper-V.
- Saiba mais sobre [os URLs](migrate-appliance.md#url-access) a que o aparelho precisa de aceder.

## <a name="port-access"></a>Acesso portuário

O quadro seguinte resume os requisitos do porto para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Aparelho** | Ligações de entrada na porta TCP 3389 para permitir ligações remotas ao aparelho.<br/> Ligações de entrada na porta 44368 para aceder remotamente à aplicação de gestão de aparelhos utilizando o URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Ligações de saída nas portas 443 (HTTPS), 5671 e 5672 (AMQP) para enviar metadados de descoberta e desempenho para o Azure Migrate.
**Hospedeiro/cluster hiper-V** | Ligações de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para puxar metadados de configuração e desempenho dos VMs hiper-V utilizando uma sessão de Modelo de Informação Comum (CIM).

## <a name="agent-based-dependency-visualization"></a>Visualização da dependência baseada em agente

[A visualização da dependência](concepts-dependency-visualization.md) ajuda-o a visualizar dependências através de máquinas que pretende avaliar e migrar. Para visualização, requisitos e limitações baseados em agentes são resumidos na tabela seguinte


**Requisito** | **Detalhes**
--- | ---
**Implementação** | Antes de implementar a visualização da dependência, deverá ter um projeto Azure Migrate no local, com a ferramenta Azure Migrate: Server Assessment adicionada ao projeto. Implementa a visualização da dependência depois de instalar um aparelho Azure Migrate para descobrir as suas máquinas no local.<br/><br/> A visualização da dependência não está disponível no Governo de Azure.
**Mapa do Serviço** | A visualização da dependência baseada no agente utiliza a solução [Service Map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) em [registos do Monitor Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Para implementar, associa um novo ou existente espaço de trabalho log analytics com um projeto Azure Migrate.
**Área de trabalho do Log Analytics** | O espaço de trabalho deve estar na mesma subscrição que o projeto Azure Migrate.<br/><br/> A Azure Migrate apoia espaços de trabalho residentes nas regiões leste dos EUA, Sudeste Asiático e Europa Ocidental.<br/><br/>  O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
**Encargos** | A solução Service Map não incorre em quaisquer encargos nos primeiros 180 dias (desde o dia em que associou o espaço de trabalho log Analytics ao projeto Azure Migrate).<br/><br/> Após 180 dias, serão aplicados os encargos padrão do Log Analytics.<br/><br/> A utilização de qualquer outra solução que não o Mapa de Serviço sintetizado no espaço de trabalho associado do Log Analytics incorrerá em cargas padrão de Log Analytics.<br/><br/> Se eliminar o projeto Azure Migrate, o espaço de trabalho não é eliminado com ele. Após a exclusão do projeto, o Service Map não é gratuito e cada nó será cobrado de acordo com o nível pago de log analytics espaço de trabalho.
**Agentes** | A visualização da dependência baseada no agente requer que sejam instalados dois agentes em cada máquina que pretende analisar.<br/><br/> - [o agente de monitorização da Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [- agente de dependência.](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent) 
**Ligação à Internet** | Se as máquinas não estiverem ligadas à internet, é necessário instalar a porta de entrada log analytics.


## <a name="next-steps"></a>Passos seguintes

[Prepare-se para avaliação de VM hiper-V](tutorial-prepare-hyper-v.md)
