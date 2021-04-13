---
title: Azure Automanage para máquinas virtuais FAQ
description: Respostas a perguntas frequentes sobre a Azure Automanage para máquinas virtuais.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: f5a9ff7661fda372631d1bb912b1c137b37c7e07
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363364"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Perguntas frequentes para Azure Automanage para VMs

Este artigo fornece respostas a algumas das perguntas mais comuns sobre [a Azure Automanage para máquinas virtuais.](automanage-virtual-machines.md)

Se a sua questão Azure não for abordada neste artigo, visite os fóruns Azure sobre [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou publicar [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Também pode submeter um pedido de apoio ao Azure. Para submeter um pedido de apoio, na página de suporte do [Azure,](https://azure.microsoft.com/support/options/)selecione **Obter suporte**.


## <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage para máquinas virtuais

**Quais são todos os pré-requisitos necessários para permitir a Azure Automanage?**

São os seguintes pré-requisitos para permitir a auto-produção da Azure:
- Versões suportadas [do Windows Server](automanage-windows-server.md#supported-windows-server-versions) e [distros Linux](automanage-linux.md#supported-linux-distributions-and-versions)
- Os VMs devem estar numa região apoiada
- O utilizador deve ter permissões corretas
- VMs definidos não escala apenas
- Automanage não suporta subscrições sandbox neste momento

**Qual é a permissão do Azure RBAC para permitir a auto-produção?**

Se estiver a ativar a auto-gestão num VM com uma Conta de Auto-gestão existente, precisa de uma função de Contribuinte para o Grupo de Recursos onde reside o VM.

Se estiver a utilizar uma nova Conta de Autoadministração ao habilitar, tem de ter a função De Proprietário ou ter função de Administrador de Acesso ao Utilizador + para a subscrição.


**Que regiões são apoiadas?**

A lista completa das regiões apoiadas está disponível [aqui.](./automanage-virtual-machines.md#supported-regions)


**Que capacidades automatiza a Azure Automanage?**

Automanagem matricula, configura e monitoriza ao longo do ciclo de vida do VM os serviços listados [aqui](automanage-virtual-machines.md).

**A Azure Automanage funciona com VMs ativados pelo Azure Arc?**

A automanagem atualmente não suporta VMs ativados pelo Arco.

**Posso personalizar configurações na Azure Automanage?**

Os clientes podem personalizar configurações para serviços específicos, como a retenção de Backup Azure, através de preferências de configuração. Para obter a lista completa de configurações que podem ser alteradas, consulte a nossa documentação [aqui.](automanage-virtual-machines.md#customizing-an-environment-using-preferences)


**A Azure Automanage funciona com os VMs Linux e Windows?**

Sim, consulte as [versões](automanage-windows-server.md#supported-windows-server-versions) suportadas do Windows Server e [os distros Linux](automanage-linux.md#supported-linux-distributions-and-versions).


**Posso aplicar seletivamente a auto-produção apenas num conjunto de VMs?**

A auto-mutilação pode ser ativada com clique e simplicidade de ponto em VMs novos e existentes selecionados. A auto-produção também pode ser desativada a qualquer momento.


**A Azure Automanage suporta VMs num conjunto de balanças de máquinas virtuais?**

Não, a Azure Automanage não suporta atualmente VMs num Conjunto de Escala de Máquina Virtual.


**Quanto custa a Azure Automanage?**

A Azure Automanage está disponível sem custos adicionais na pré-visualização pública. Os recursos Azure anexados, como o Azure Backup, incorrerão em custos.


**Posso aplicar a auto-produção através da política do Azure?**

Sim, temos uma política incorporada que aplicará automaticamente a Automanage a todos os VMs dentro do seu âmbito definido. Também especificará a configuração ambiental (DevTest ou Produção) juntamente com a sua conta Automanage. Saiba mais sobre como permitir a auto-produção através da política da Azure [aqui.](virtual-machines-policy-enable.md)


**O que é uma conta de auto-produção?**

A Conta De Gestão Automática é um MSI (Identidade de Serviço Gerido) que fornece o contexto de segurança ou a identidade sob a qual ocorrem as operações automatizadas.


**Ao ativar a auto-produção, tem impacto em VMs adicionais para além dos VM(s) que selecionei?**

Se o seu VM estiver ligado a um espaço de trabalho log analytics existente, reutilizaremos esse espaço de trabalho para aplicar estas soluções: Change Tracking, Inventory e Update Management. Todos os VMs ligados a esse espaço de trabalho terão essas soluções ativadas.


**Posso mudar o ambiente do meu VM?**

Neste momento, terá de desativar a auto-produção para esse VM e, em seguida, reativar a auto-produção com o ambiente e preferências pretendidos.


**Se o meu VM já estiver configurado para um serviço, como a Update Management, a Automanage irá reconfigurá-lo?**
Não, a auto-produção não vai reconfigurá-lo. Começaremos a monitorizar os recursos associados a esse serviço de deriva.


**Porque é que o meu VM tem um estatuto falhado no portal auto-piloto?**

Se vir o estado como *Falhado,* pode resolver problemas de várias formas:
* Vá aos **grupos de Recursos**, selecione o seu grupo de recursos, clique em **Implementações** e veja o estado *de Falha* lá juntamente com detalhes de erro.
* Vá a **Subscrições**, selecione o seu grupo de recursos, clique em **Implementações** e veja o estado *de Falha* lá juntamente com detalhes de erro.
* Também pode visitar o registo de atividade de um VM, que conterá uma entrada para "Criar ou Atualizar atribuições de perfis de configuração". Isto também pode conter mais detalhes sobre a sua implementação.

**Como posso conseguir apoio para a auto-produção?**

Pode arquivar um [bilhete de caso de suporte técnico.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Para a opção **Serviço,** procure e selecione *Automanagem* sob a secção *de Monitorização e Gestão.*


## <a name="next-steps"></a>Passos seguintes

Tente ativar a auto-produção de máquinas virtuais no portal Azure.

> [!div class="nextstepaction"]
> [Ativar a auto-produção de máquinas virtuais no portal Azure](quick-create-virtual-machines-portal.md)