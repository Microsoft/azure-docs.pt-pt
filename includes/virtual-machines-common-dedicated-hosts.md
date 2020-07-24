---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 24e4554e2202c8b5452193e1b0f48cf6c8ada5dd
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133895"
---
## <a name="limitations"></a>Limitações

- Os conjuntos de escala de máquinas virtuais não são suportados atualmente em anfitriões dedicados.

## <a name="benefits"></a>Benefícios 

Reservar todo o hospedeiro proporciona os seguintes benefícios:

-   Isolamento de hardware ao nível do servidor físico. Nenhum outro VMs será colocado nos seus anfitriões. Os anfitriões dedicados são implantados nos mesmos centros de dados e partilham a mesma rede e infraestrutura de armazenamento subjacente que outros anfitriões não isolados.
-   Controlo sobre eventos de manutenção iniciados pela plataforma Azure. Embora a maioria dos eventos de manutenção tenha pouco ou nenhum impacto nas suas máquinas virtuais, existem algumas cargas de trabalho sensíveis onde cada segundo de pausa pode ter um impacto. Com anfitriões dedicados, pode optar por uma janela de manutenção para reduzir o impacto no seu serviço.
-   Com o benefício híbrido Azure, pode trazer as suas próprias licenças para Windows e SQL para Azure. A utilização dos benefícios híbridos proporciona-lhe benefícios adicionais. Para mais informações, consulte [a Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/)


## <a name="groups-hosts-and-vms"></a>Grupos, anfitriões e VMs  

![Vista dos novos recursos para anfitriões dedicados.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Um **grupo anfitrião** é um recurso que representa uma coleção de anfitriões dedicados. Você cria um grupo de anfitriões em uma região e uma zona de disponibilidade, e adiciona anfitriões a ele.

Um **hospedeiro** é um recurso, mapeado para um servidor físico num centro de dados Azure. O servidor físico é atribuído quando o hospedeiro é criado. Um hospedeiro é criado dentro de um grupo anfitrião. Um hospedeiro tem um SKU descrevendo quais tamanhos VM podem ser criados. Cada anfitrião pode hospedar vários VMs, de diferentes tamanhos, desde que sejam da mesma série de tamanho.

Ao criar um VM em Azure, pode selecionar qual o anfitrião dedicado a utilizar para o seu VM. Tem controlo total sobre quais os VMs colocados nos seus anfitriões.


## <a name="high-availability-considerations"></a>Considerações de Alta Disponibilidade 

Para uma elevada disponibilidade, deverá implementar vários VMs, distribuídos por vários anfitriões (no mínimo 2). Com a Azure Dedicated Hosts, você tem várias opções para providenciar a sua infraestrutura para moldar os seus limites de isolamento de falhas.

### <a name="use-availability-zones-for-fault-isolation"></a>Utilizar zonas de disponibilidade para isolamento de falhas

As zonas de disponibilidade são localizações físicas únicas dentro de uma região de Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Um grupo anfitrião é criado numa única zona de disponibilidade. Uma vez criados, todos os anfitriões serão colocados dentro dessa zona. Para obter uma elevada disponibilidade através de zonas, é necessário criar vários grupos de anfitriões (um por zona) e difundir os seus anfitriões em conformidade.

Se atribuir um grupo de anfitriões a uma zona de disponibilidade, todos os VMs criados nesse hospedeiro devem ser criados na mesma zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Utilizar domínios de avaria para isolamento de falhas

Um hospedeiro pode ser criado num domínio específico de falhas. Tal como o VM num conjunto de escala ou disponibilidade, os anfitriões em diferentes domínios de avaria serão colocados em diferentes racks físicos no centro de dados. Quando cria um grupo de anfitriões, é-lhe exigido que especifique a contagem de domínio de avaria. Ao criar anfitriões dentro do grupo anfitrião, atribua o domínio de avaria para cada anfitrião. Os VMs não requerem qualquer atribuição de domínio de avaria.

Os domínios de avaria não são os mesmos que a colocação. Ter o mesmo domínio de falhas para dois anfitriões não significa que estejam em proximidade uns com os outros.

Os domínios de avaria são examinados para o grupo anfitrião. Não deve assumir a anti-afinidade entre dois grupos anfitriões (a menos que estejam em diferentes zonas de disponibilidade).

Os VM implantados em anfitriões com diferentes domínios de avaria, terão os seus serviços de discos geridos subjacentes em vários selos de armazenamento, para aumentar a proteção contra o isolamento de falhas.

### <a name="using-availability-zones-and-fault-domains"></a>Utilização de zonas de disponibilidade e domínios de avaria

Pode usar ambas as capacidades juntas para conseguir um isolamento ainda maior. Neste caso, irá especificar a contagem de domínio de zona de disponibilidade e de avaria para cada grupo anfitrião, atribuir um domínio de avaria a cada um dos seus anfitriões no grupo e atribuir uma zona de disponibilidade a cada um dos seus VMs

O modelo de amostra do Gestor de Recursos encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) utiliza zonas e domínios de avaria para espalhar os anfitriões para máxima resiliência numa região.

## <a name="maintenance-control"></a>Controlo da manutenção

A infraestrutura que suporta as suas máquinas virtuais pode ocasionalmente ser atualizada para melhorar a fiabilidade, desempenho, segurança e lançar novas funcionalidades. A plataforma Azure tenta minimizar o impacto da manutenção da plataforma sempre que possível, mas os clientes com cargas de trabalho *sensíveis* à manutenção não podem tolerar mesmo alguns segundos que o VM precisa de ser congelado ou desligado para manutenção.

**O Controlo de Manutenção** oferece aos clientes a opção de ignorar as atualizações regulares da plataforma programadas nos seus anfitriões dedicados e, em seguida, aplicá-lo no momento da sua escolha dentro de uma janela de 35 dias.

Para obter mais informações, consulte ['Gerir as atualizações da plataforma' com o Controlo de Manutenção.](https://docs.microsoft.com/azure/virtual-machines/maintenance-control)

## <a name="capacity-considerations"></a>Considerações de capacidade

Uma vez que um hospedeiro dedicado é provisionado, Azure atribui-o ao servidor físico. Isto garante a disponibilidade da capacidade quando precisa de abastecer o seu VM. O Azure utiliza toda a capacidade da região (ou zona) para escolher um servidor físico para o seu anfitrião. Significa também que os clientes podem esperar poder aumentar a sua pegada de hospedeiro dedicada sem a preocupação de ficar sem espaço no cluster.

## <a name="quotas"></a>Quotas

Existem dois tipos de quota que são consumidos quando se implanta um hospedeiro dedicado.

1. Quota de anfitrião vCPU dedicada. O limite de quota padrão é de 3000 vCPUs, por região.
1. Quota familiar do tamanho de VM. Por exemplo, uma subscrição **Pay-as-you-go** pode ter apenas uma quota de 10 vCPUs disponíveis para a série de tamanhoSV3, na região leste dos EUA. Para implementar um anfitrião dedicado Dsv3, você precisaria solicitar um aumento de quota para pelo menos 64 vCPUs antes de poder implantar o anfitrião dedicado. 

Para solicitar um aumento de quota, crie um pedido de apoio no [portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

O fornecimento de um hospedeiro dedicado consumirá tanto o vCPU de hospedeiro dedicado como a quota vCPU da família VM, mas não consumirá o vCPU regional.


![Screenshot da página de utilização e quotas no portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Para mais informações, consulte [as quotas vCPU da máquina virtual.](/azure/virtual-machines/windows/quotas)

As assinaturas de teste gratuito e MSDN não têm quota para anfitriões dedicados Azure.

## <a name="pricing"></a>Preços

Os utilizadores são carregados por anfitrião dedicado, independentemente de quantos VMs são implantados. Na sua declaração mensal, verá um novo tipo de anfitriões de recursos faturado. Os VMs de um anfitrião dedicado ainda serão mostrados na sua declaração, mas terão um preço de 0.

O preço do anfitrião é definido com base na família VM, tipo (tamanho do hardware) e região. Um preço de anfitrião é relativo ao maior tamanho VM suportado no hospedeiro.

O licenciamento de software, armazenamento e utilização da rede são faturados separadamente do anfitrião e VMs. Não há nenhuma alteração nos itens de faturação.

Para mais informações, consulte [os preços do Anfitrião Dedicado Azure.](https://aka.ms/ADHPricing)

Também pode economizar em custos com uma [Instância Reservada de Anfitriões Dedicados Azure.](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
 
## <a name="sizes-and-hardware-generations"></a>Tamanhos e gerações de hardware

Um SKU é definido para um hospedeiro e representa a série e tipo de tamanho VM. Pode misturar vários VMs de diferentes tamanhos num único hospedeiro, desde que sejam da mesma série de tamanho. 

O *tipo* é a geração de hardware. Diferentes tipos de hardware para a mesma série VM serão de diferentes fornecedores de CPU e têm diferentes gerações de CPU e número de núcleos. 

Os tamanhos e os tipos de hardware variam por região. Consulte a [página de preços do](https://aka.ms/ADHPricing) anfitrião para saber mais.


## <a name="host-life-cycle"></a>Ciclo de vida do anfitrião


Azure monitoriza e gere o estado de saúde dos seus anfitriões. Os seguintes estados serão devolvidos quando consultar o seu anfitrião:

| Estado da Saúde   | Descrição       |
|----------|----------------|
| Anfitrião Disponível     | Não há problemas conhecidos com o seu anfitrião.   |
| Hospedeiro sob investigação  | Estamos tendo alguns problemas com o anfitrião que estamos procurando. Este é um estado transitório necessário para que a Azure tente identificar o âmbito e a causa principal para a questão identificada. As máquinas virtuais que estão a funcionar no hospedeiro podem ser afetadas. |
| Anfitrião pendente Deallocate   | O Azure não pode restaurar o hospedeiro de volta a um estado saudável e pedir-lhe para recolocar as suas máquinas virtuais fora deste hospedeiro. Se `autoReplaceOnFailure` estiver ativado, as suas máquinas virtuais são *curadas* para hardware saudável. Caso contrário, a sua máquina virtual pode estar a funcionar num hospedeiro que está prestes a falhar.|
| Oferta de anfitriãolocada  | Todas as máquinas virtuais foram removidas do hospedeiro. Já não está a ser cobrado por este hospedeiro, uma vez que o hardware foi retirado da rotação.   |

