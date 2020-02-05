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
ms.openlocfilehash: 934a4fdf7cabb89c4b98c7800252cb24d4e23abf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013809"
---
## <a name="limitations"></a>Limitações

- Atualmente, não há suporte para conjuntos de dimensionamento de máquinas virtuais em hosts dedicados.
- As seguintes séries VM são suportadas: DSv3, ESv3 e FSv2. 

## <a name="benefits"></a>Vantagens 

Reservar o host inteiro oferece os seguintes benefícios:

-   Isolamento de hardware no nível do servidor físico. Nenhuma outra VM será colocada nos hosts. Os hosts dedicados são implantados nos mesmos data centers e compartilham a mesma rede e a infraestrutura de armazenamento subjacente que outros hosts não isolados.
-   Controle sobre os eventos de manutenção iniciados pela plataforma Azure. Embora a maioria dos eventos de manutenção tenha pouco ou nenhum impacto em suas máquinas virtuais, há algumas cargas de trabalho confidenciais em que cada segundo de pausa pode ter um impacto. Com hosts dedicados, você pode aceitar uma janela de manutenção para reduzir o impacto em seu serviço.
-   Com o benefício híbrido do Azure, você pode colocar suas próprias licenças para Windows e SQL no Azure. O uso dos benefícios híbridos oferece benefícios adicionais. Para obter mais informações, consulte [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupos, hosts e VMs  

![Exibição dos novos recursos para hosts dedicados.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Um **grupo de hosts** é um recurso que representa uma coleção de hosts dedicados. Você cria um grupo de hosts em uma região e uma zona de disponibilidade e adiciona hosts a ele.

Um **host** é um recurso, mapeado para um servidor físico em um data center do Azure. O servidor físico é alocado quando o host é criado. Um host é criado dentro de um grupo de hosts. Um host tem uma SKU que descreve quais tamanhos de VM podem ser criados. Cada host pode hospedar várias VMs, de tamanhos diferentes, contanto que sejam da mesma série de tamanho.

Ao criar uma VM no Azure, você pode selecionar qual host dedicado usar para sua VM. Você tem controle total sobre quais VMs são colocadas em seus hosts.


## <a name="high-availability-considerations"></a>Considerações sobre alta disponibilidade 

Para alta disponibilidade, você deve implantar várias VMs, distribuídas entre vários hosts (mínimo de 2). Com os hosts dedicados do Azure, você tem várias opções para provisionar sua infraestrutura para formatar seus limites de isolamento de falha.

### <a name="use-availability-zones-for-fault-isolation"></a>Usar Zonas de Disponibilidade para o isolamento de falhas

As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Um grupo de hosts é criado em uma única zona de disponibilidade. Depois de criada, todos os hosts serão colocados dentro dessa zona. Para obter alta disponibilidade entre zonas, você precisa criar vários grupos de hosts (um por zona) e distribuir os hosts adequadamente.

Se você atribuir um grupo de hosts a uma zona de disponibilidade, todas as VMs criadas nesse host deverão ser criadas na mesma zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Usar domínios de falha para o isolamento de falhas

Um host pode ser criado em um domínio de falha específico. Assim como a VM em um conjunto de dimensionamento ou conjunto de disponibilidade, os hosts em diferentes domínios de falha serão colocados em diferentes racks físicos na data center. Ao criar um grupo de hosts, é necessário especificar a contagem de domínios de falha. Ao criar hosts dentro do grupo de hosts, você atribui um domínio de falha para cada host. As VMs não exigem nenhuma atribuição de domínio de falha.

Os domínios de falha não são os mesmos que a colocação. Ter o mesmo domínio de falha para dois hosts não significa que eles estão em proximidade entre si.

Os domínios de falha são incluídos no escopo do grupo de hosts. Você não deve fazer nenhuma suposição sobre a antiafinidade entre dois grupos de hosts (a menos que eles estejam em zonas de disponibilidade diferentes).

As VMs implantadas em hosts com domínios de falha diferentes terão seus serviços de discos gerenciados subjacentes em vários carimbos de armazenamento, para aumentar a proteção de isolamento de falhas.

### <a name="using-availability-zones-and-fault-domains"></a>Usando Zonas de Disponibilidade e domínios de falha

Você pode usar ambos os recursos juntos para obter ainda mais isolamento de falha. Nesse caso, você especificará a zona de disponibilidade e a contagem de domínios de falha em para cada grupo de hosts, atribuirá um domínio de falha a cada um dos hosts no grupo e atribuirá uma zona de disponibilidade a cada uma de suas VMs

O modelo de exemplo do Resource Manager encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) usa zonas e domínios de falha para distribuir hosts para obter máxima resiliência em uma região.

## <a name="maintenance-control"></a>Controlo da manutenção

Às vezes, a infraestrutura de suporte às suas máquinas virtuais pode ser atualizada para melhorar a confiabilidade, o desempenho, a segurança e a inicialização de novos recursos. A plataforma Azure tenta minimizar o impacto da manutenção da plataforma sempre que possível, mas os clientes com cargas de trabalho *sensíveis à manutenção* não podem tolerar até poucos segundos que a VM precisa ser congelada ou desconectada para manutenção.

O **controle de manutenção** fornece aos clientes uma opção para ignorar atualizações de plataforma regulares agendadas em seus hosts dedicados e, em seguida, aplicá-las no momento de sua escolha dentro de uma janela sem interrupção de 35 dias.

> [!NOTE]
>  O controlo de manutenção está atualmente em pré-visualização pública. Para mais informações, consulte as atualizações de Controlo com controlo de **manutenção utilizando [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) ou [PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)** .

## <a name="capacity-considerations"></a>Considerações sobre capacidade

Depois que um host dedicado é provisionado, o Azure o atribui ao servidor físico. Isso garante a disponibilidade da capacidade quando você precisa provisionar sua VM. O Azure usa toda a capacidade na região (ou zona) para escolher um servidor físico para o host. Isso também significa que os clientes podem esperar poder aumentar seu espaço de host dedicado sem a preocupação de ficar sem espaço no cluster.

## <a name="quotas"></a>Quotas

Há um limite de cota padrão de 3000 vCPUs para hosts dedicados, por região. Porém, o número de hosts que você pode implantar também é limitado pela cota para a família de tamanho de VM usada para o host. Por exemplo, uma assinatura **paga conforme o uso** pode ter apenas uma cota de 10 vCPUs disponíveis para a série de tamanho de Dsv3, na região leste dos EUA. Nesse caso, você precisa solicitar um aumento de cota para pelo menos 64 vCPUs antes de poder implantar um host dedicado. Selecione o botão **solicitar aumento** no canto superior direito para arquivar uma solicitação, se necessário.

![Captura de tela da página uso e cotas no portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Para obter mais informações, consulte [cotas de vCPU de máquina virtual](/azure/virtual-machines/windows/quotas).

A avaliação gratuita e as assinaturas do MSDN não têm cota para hosts dedicados do Azure.

## <a name="pricing"></a>Preços

Os usuários são cobrados por host dedicado, independentemente de quantas VMs são implantadas. Em sua declaração mensal, você verá um novo tipo de recurso Faturável de hosts. As VMs em um host dedicado ainda serão mostradas em sua instrução, mas terão um preço de 0.

O preço do host é definido com base na família de VMs, tipo (tamanho do hardware) e região. Um preço de host é relativo ao maior tamanho de VM com suporte no host.

O licenciamento de software, o armazenamento e o uso de rede são cobrados separadamente do host e das VMs. Não há nenhuma alteração nesses itens faturáveis.

Para obter mais informações, consulte [preços do host dedicado do Azure](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>Famílias de VMs e gerações de hardware

Uma SKU é definida para um host e representa a série de tamanho da VM e o tipo. Você pode misturar várias VMs de tamanhos diferentes em um único host, desde que eles tenham a mesma série de tamanho. O tipo é a geração de hardware disponível atualmente na região.

Diferentes `types` para a mesma série de VMs serão de fornecedores de CPU diferentes e terão diferentes gerações de CPU e número de núcleos.

Consulte a página de [preços](https://aka.ms/ADHPricing) do host para saber mais.

Anfitriões dedicados apoiam os seguintes tipos de anfitriões SKU:: DSv3_Type1 e ESv3_Type1

 
## <a name="host-life-cycle"></a>Ciclo de vida do host


O Azure monitora e gerencia o status de integridade de seus hosts. Os seguintes Estados serão retornados quando você consultar o host:

| Estado da Saúde   | Descrição       |
|----------|----------------|
| Host disponível     | Não há problemas conhecidos com o host.   |
| Host em investigação  | Estamos com alguns problemas com o host que estamos procurando. Esse é um estado de transição necessário para que o Azure tente e identifique o escopo e a causa raiz do problema identificado. As máquinas virtuais em execução no host podem ser afetadas. |
| Desalocação pendente do host   | O Azure não pode restaurar o host de volta para um estado íntegro e pedir que você reimplante suas máquinas virtuais fora desse host. Se o *`autoReplaceOnFailure` estiver habilitado* , suas máquinas virtuais serão reparadas para o hardware íntegro. Caso contrário, sua máquina virtual pode estar em execução em um host que está prestes a falhar.|
| Host desalocado  | Todas as máquinas virtuais foram removidas do host. Você não está mais sendo cobrado por esse host, pois o hardware foi retirado da rotação.   |

