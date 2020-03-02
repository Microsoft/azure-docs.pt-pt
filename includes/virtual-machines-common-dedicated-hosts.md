---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 870d6f8441bf395ba0c02dccf8f3d1c6434410c6
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78208195"
---
## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são atualmente suportados em anfitriões dedicados.
- As seguintes séries VM são suportadas: DSv3, ESv3 e FSv2. 

## <a name="benefits"></a>Benefícios 

Reservar todo o anfitrião proporciona os seguintes benefícios:

-   Isolamento de hardware ao nível do servidor físico. Nenhum outro VMs será colocado nos seus anfitriões. Os anfitriões dedicados são implantados nos mesmos centros de dados e partilham a mesma rede e infraestruturas de armazenamento subjacentes que outros hospedeiros não isolados.
-   Controlo sobre os eventos de manutenção iniciados pela plataforma Azure. Embora a maioria dos eventos de manutenção tenha pouco ou nenhum impacto nas suas máquinas virtuais, existem algumas cargas de trabalho sensíveis onde cada segundo de pausa pode ter um impacto. Com anfitriões dedicados, pode optar por uma janela de manutenção para reduzir o impacto no seu serviço.
-   Com o benefício híbrido Azure, pode levar as suas próprias licenças para Windows e SQL para o Azure. A utilização dos benefícios híbridos proporciona-lhe benefícios adicionais. Para mais informações, consulte [O Benefício Híbrido Azure.](https://azure.microsoft.com/pricing/hybrid-benefit/)


## <a name="groups-hosts-and-vms"></a>Grupos, anfitriões e VMs  

![Vista dos novos recursos para anfitriões dedicados.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Um **grupo de anfitriões** é um recurso que representa uma coleção de anfitriões dedicados. Cria-se um grupo de acolhimento numa região e numa zona de disponibilidade e adiciona-lhe anfitriões.

Um **hospedeiro** é um recurso, mapeado para um servidor físico num centro de dados Do Azure. O servidor físico é atribuído quando o hospedeiro é criado. Um hospedeiro é criado dentro de um grupo de anfitriões. Um hospedeiro tem um SKU descrevendo quais tamanhos vm podem ser criados. Cada anfitrião pode hospedar vários VMs, de diferentes tamanhos, desde que sejam da mesma série de tamanhos.

Ao criar um VM em Azure, pode selecionar qual anfitrião dedicado a utilizar para o seu VM. Tem controlo total sobre quais os VMs colocados nos seus anfitriões.


## <a name="high-availability-considerations"></a>Considerações de alta disponibilidade 

Para uma elevada disponibilidade, deve implementar vários VMs, distribuídos por vários anfitriões (mínimo de 2). Com o Azure Dedicado Hosts, tem várias opções para fornecer a sua infraestrutura para moldar os seus limites de isolamento de falhas.

### <a name="use-availability-zones-for-fault-isolation"></a>Utilize Zonas de Disponibilidade para isolamento de falhas

As zonas de disponibilidade são locais físicos únicos dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Um grupo de anfitriões é criado numa única zona de disponibilidade. Uma vez criados, todos os anfitriões serão colocados dentro dessa zona. Para obter uma elevada disponibilidade em todas as zonas, você precisa criar vários grupos de acolhimento (um por zona) e espalhar seus anfitriões em conformidade.

Se atribuir um grupo de anfitriões a uma zona de disponibilidade, todos os VMs criados nesse hospedeiro devem ser criados na mesma zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Utilizar domínios de falha para isolamento de falhas

Um hospedeiro pode ser criado num domínio de falha específico. Tal como o VM num conjunto de escala ou disponibilidade, os anfitriões em diferentes domínios de avaria serão colocados em diferentes prateleiras físicas no centro de dados. Quando cria um grupo de anfitriões, é necessário especificar a contagem de domínio de avaria. Ao criar anfitriões dentro do grupo anfitrião, atribua domínio de falha para cada anfitrião. Os VMs não requerem qualquer atribuição de domínio de avaria.

Os domínios de avaria não são os mesmos que a collocalização. Ter o mesmo domínio de falha para dois anfitriões não significa que estejam próximos uns dos outros.

Os domínios de avaria são remetos ao grupo anfitrião. Não deve assumir qualquer suposição sobre a anti-afinidade entre dois grupos de acolhimento (a menos que estejam em diferentes zonas de disponibilidade).

VMs implantados em anfitriões com diferentes domínios de falha, terão os seus serviços de discos geridos subjacentes em vários selos de armazenamento, para aumentar a proteção de isolamento de falhas.

### <a name="using-availability-zones-and-fault-domains"></a>Utilização de Zonas de Disponibilidade e Domínios de Falhas

Pode usar ambas as capacidades em conjunto para alcançar ainda mais isolamento de falhas. Neste caso, irá especificar a zona de disponibilidade e a contagem de domínio de avaria em cada grupo anfitrião, atribuir um domínio de falha a cada um dos seus anfitriões no grupo, e atribuir uma zona de disponibilidade a cada um dos seus VMs

O modelo de amostra do Gestor de Recursos encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) usa zonas e domínios de falha para espalhar hospedeiros para máxima resiliência numa região.

## <a name="maintenance-control"></a>Controlo da manutenção

A infraestrutura que suporta as suas máquinas virtuais pode ocasionalmente ser atualizada para melhorar a fiabilidade, desempenho, segurança e para lançar novas funcionalidades. A plataforma Azure tenta minimizar o impacto da manutenção da plataforma sempre que possível, mas os clientes com cargas de trabalho *sensíveis* à manutenção não podem tolerar mesmo poucos segundos que o VM precisa de ser congelado ou desligado para manutenção.

**O Control de Manutenção** oferece aos clientes a opção de ignorar as atualizações regulares da plataforma agendadas nos seus anfitriões dedicados e, em seguida, aplicá-la no momento da sua escolha dentro de uma janela de 35 dias.

> [!NOTE]
>  O controlo de manutenção está atualmente em pré-visualização pública. Para mais informações, consulte as atualizações de Controlo com controlo de **manutenção utilizando [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) ou [PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)** .

## <a name="capacity-considerations"></a>Considerações sobre capacidade

Uma vez que um hospedeiro dedicado é aprovisionado, O Azure atribui-o ao servidor físico. Isto garante a disponibilidade da capacidade quando precisa de fornecer o seu VM. O Azure utiliza toda a capacidade da região (ou zona) para escolher um servidor físico para o seu anfitrião. Significa também que os clientes podem esperar ser capazes de crescer a sua pegada de anfitrião dedicada sem a preocupação de ficar sem espaço no cluster.

## <a name="quotas"></a>Quotas

Existe um limite de quota padrão de 3000 vCPUs para hospedeiros dedicados, por região. Mas o número de anfitriões que pode implantar também é limitado pela quota para a família VM tamanho usada para o hospedeiro. Por exemplo, uma subscrição **Pay-as-you-go** só pode ter uma quota de 10 vCPUs disponíveis para a série de tamanho Dsv3, na região leste dos EUA. Neste caso, é necessário solicitar um aumento de quota para pelo menos 64 vCPUs antes de poder implementar um hospedeiro dedicado. Selecione o botão **de aumento** de pedido no canto superior direito para apresentar um pedido, se necessário.

![Screenshot da página de utilização e quotas no portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Para mais informações, consulte [as quotas vCPU](/azure/virtual-machines/windows/quotas)da máquina virtual.

O teste gratuito e as subscrições da MSDN não têm quota para os Anfitriões Dedicados do Azure.

## <a name="pricing"></a>Preços

Os utilizadores são cobrados por anfitrião dedicado, independentemente do número de VMs que são implantados. Na sua declaração mensal, verá um novo tipo de recursos faturados de anfitriões. Os VMs de um anfitrião dedicado ainda serão mostrados na sua declaração, mas terão um preço de 0.

O preço de acolhimento é definido com base na família VM, tipo (tamanho do hardware) e região. Um preço de acolhimento é relativo ao maior tamanho VM suportado no hospedeiro.

O licenciamento, armazenamento e utilização da rede de software são faturados separadamente do anfitrião e vMs. Não há alteração nos itens de faturação.

Para mais informações, consulte [o preço do Anfitrião Dedicado Azure.](https://aka.ms/ADHPricing)

Você também pode economizar em custos com um [Caso Reservado de Anfitriões Dedicados Azure] (.. /artigos/máquinas virtuais/prepay-dedicado-dedicado-anfitriões-instâncias reservadas.md).
 
## <a name="vm-families-and-hardware-generations"></a>Famílias VM e gerações de hardware

Um SKU é definido para um hospedeiro e representa a série e tipo de tamanho VM. Pode misturar vários VMs de diferentes tamanhos dentro de um único hospedeiro, desde que sejam da mesma série de tamanhos. O tipo é a geração de hardware atualmente disponível na região.

Diferentes `types` para a mesma série VM serão de diferentes fornecedores de CPU e têm diferentes gerações de CPU e número de núcleos.

Consulte a [página](https://aka.ms/ADHPricing) de preços do anfitrião para saber mais.

Anfitriões dedicados apoiam os seguintes tipos de anfitriões SKU:: DSv3_Type1 e ESv3_Type1

 
## <a name="host-life-cycle"></a>Ciclo de vida de hospedeiro


O Azure monitoriza e gere o estado de saúde dos seus anfitriões. Os seguintes estados serão devolvidos quando consultar o seu anfitrião:

| Estado de Funcionamento   | Descrição       |
|----------|----------------|
| Anfitrião disponível     | Não há problemas conhecidos com o seu anfitrião.   |
| Hospedeiro sob investigação  | Estamos tendo alguns problemas com o anfitrião que estamos investigando. Este é um estado de transição necessário para que o Azure tente identificar o âmbito e a causa principal para a questão identificada. As máquinas virtuais que estão a funcionar no hospedeiro podem ser impactadas. |
| Desalocar pendente de anfitrião   | O Azure não pode restaurar o hospedeiro num estado saudável e pedir-lhe que recoloque as suas máquinas virtuais para fora deste hospedeiro. Se `autoReplaceOnFailure` estiver ativada, as suas máquinas virtuais são *de serviço curadas* para um hardware saudável. Caso contrário, a sua máquina virtual pode estar a funcionar num hospedeiro que está prestes a falhar.|
| Anfitrião deallocated  | Todas as máquinas virtuais foram removidas do hospedeiro. Já não está a ser cobrado por este hospedeiro, uma vez que o hardware foi retirado da rotação.   |

