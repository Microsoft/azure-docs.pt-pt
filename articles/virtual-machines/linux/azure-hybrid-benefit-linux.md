---
title: Azure Hybrid Benefit e Linux VMs
description: Saiba como o Azure Hybrid Benefit pode ajudá-lo a economizar dinheiro nas suas máquinas virtuais Linux que estão a funcionar no Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machines
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 73747222b9131fa85ae6ac01c9dedd5b0bbe1d63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543415"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Como o Azure Hybrid Benefit se aplica às máquinas virtuais Linux

O Azure Hybrid Benefit é um benefício de licenciamento que o ajuda a reduzir significativamente os custos de funcionamento das suas máquinas virtuais Red Hat Enterprise Linux (RHEL) e SUSE Linux Enterprise Server (SLES) na nuvem. Com este benefício, paga apenas os custos de infraestrutura do seu VM porque a sua subscrição RHEL ou SLES cobre a taxa de software. O benefício está disponível para todas as imagens do RHEL e do SLES Marketplace pay-as-you-go (PAYG).

A Azure Hybrid Benefit for Linux VMs já está disponível ao público.

## <a name="benefit-description"></a>Descrição de benefícios

Através do Azure Hybrid Benefit, pode migrar os seus servidores RHEL e SLES para Azure, convertendo os VMs RHEL e SLES PAYG existentes no Azure para trazer a faturação da sua própria subscrição (BYOS). Normalmente, os VM implantados a partir de imagens PAYG no Azure cobrarão uma taxa de infraestrutura e uma taxa de software. Com o Azure Hybrid Benefit, os VMs PAYG podem ser convertidos para um modelo de faturação BYOS sem uma redistribuição, para que possa evitar qualquer risco de inatividade.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit custou visualização em VMs Linux.":::

Depois de ativar o benefício em RHEL ou SLES VM, deixará de ser cobrado pela taxa adicional de software normalmente incorrida num PAYG VM. Em vez disso, o seu VM começará a acumular uma taxa BYOS, que inclui apenas a taxa de hardware computacional e nenhuma taxa de software.

Também pode optar por converter um VM que tenha tido o benefício ativado nele de volta para um modelo de faturação PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Âmbito de elegibilidade do Benefício Híbrido Azure para os VMs Linux

A Azure Hybrid Benefit está disponível para todas as imagens RHEL e SLES PAYG do Azure Marketplace. O benefício ainda não está disponível para imagens RHEL ou SLES BYOS ou imagens personalizadas do Azure Marketplace.

Azure Casos de Anfitrião Dedicado, e benefícios híbridos SQL não são elegíveis para Azure Hybrid Benefit se você já estiver usando o benefício com Os VMs Linux.

## <a name="get-started"></a>Introdução

### <a name="red-hat-customers"></a>Clientes da Red Hat

A Azure Hybrid Benefit for RHEL está disponível para clientes da Red Hat que satisfaçam ambos estes critérios:

- Ter subscrições RHEL ativas ou não utilizadas que sejam elegíveis para utilização em Azure
- Ter ativado uma ou mais dessas subscrições para uso em Azure com o programa [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Certifique-se de que a subscrição correta foi ativada no programa de acesso à [nuvem.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

Para começar a usar o benefício para o Chapéu Vermelho:

1. Ative uma ou mais das suas subscrições RHEL elegíveis para utilização no Azure utilizando a interface do [cliente Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   As subscrições Azure que fornece durante o processo de ativação do Red Hat Cloud Access serão então autorizadas a utilizar a funcionalidade Azure Hybrid Benefit.
1. Aplique o Azure Hybrid Benefit em qualquer um dos seus VMs RHEL PAYG existentes e quaisquer novos VMs RHEL que implemente a partir de imagens PAYG do Azure Marketplace. Pode utilizar o portal Azure ou o Azure CLI para permitir o benefício.
1. Siga [os próximos passos recomendados](https://access.redhat.com/articles/5419341) para configurar fontes de atualização para os seus VMs RHEL e para as diretrizes de conformidade com a subscrição RHEL.


### <a name="suse-customers"></a>Clientes SUSE

Para começar a utilizar o benefício para a SUSE:

1. Registe-se no Programa SUSE Public Cloud.
1. Aplique o benefício aos seus VMs recém-criados ou existentes através do portal Azure ou Azure CLI.
1. Registe os seus VMs que estão a receber o benefício com uma fonte separada de atualizações.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Ativar e desativar o benefício no portal Azure

Pode ativar o benefício nos VM existentes visitando a opção **Configuração** à esquerda e seguindo os passos lá. Pode permitir o benefício em novos VMs durante a experiência de criação de VM.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Exemplo do portal Azure para permitir o benefício para um VM existente:
1. Visite [o portal Microsoft Azure](https://portal.azure.com/)
1. Aceda à página 'Criar uma Máquina Virtual' no portal.
 ![AHB ao criar VM](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Clique na caixa de verificação para ativar a conversão de AHB e utilize licenças de acesso à nuvem.
 ![AHB ao criar caixa de verificação VM](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Crie uma Máquina Virtual seguindo o próximo conjunto de instruções
1. Verifique a lâmina **de configuração** e verá a opção ativada. 
![Lâmina de configuração AHB após a criação](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Exemplo do portal Azure para permitir o benefício durante a criação de VM:
1. Visite [o portal Microsoft Azure](https://portal.azure.com/)
1. Abra a página Máquina Virtual na qual pretende aplicar a conversão.
1. Vá à opção **Configuração** à esquerda. Verá a secção de licenciamento. Para ativar a conversão AHB, verifique o botão de rádio "Sim" e verifique a caixa de verificação de confirmação.
![Lâmina de configuração AHB após a criação](./media/azure-hybrid-benefit/create-configuration-blade.png)

>[!NOTE]
> Se criou um **Snapshot Personalizado** ou uma Imagem Partilhada **(SIG)** de uma imagem DE MERCADO RHEL ou SLES PAYG Marketplace, só pode utilizar o Azure CLI para ativar o Azure Hybrid Benefit. Esta é conhecida a limitação e atualmente não existe uma linha temporal para fornecer esta capacidade também no Portal Azure.

## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Ativar e desativar o benefício no CLI Azure

Pode utilizar o `az vm update` comando para atualizar os VM existentes. Para os VMs RHEL, executar o comando com um `--license-type` parâmetro de `RHEL_BYOS` . Para os VMs SLES, executar o comando com um `--license-type` parâmetro de `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Exemplo do CLI para permitir o benefício
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Exemplo do CLI para desativar o benefício
Para desativar o benefício, utilize um `--license-type` valor `None` de:

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Exemplo do CLI para permitir o benefício num grande número de VMs
Para ativar o benefício num grande número de VMs, pode utilizar o `--ids` parâmetro no Azure CLI:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Os exemplos a seguir mostram dois métodos para obter uma lista de IDs de recursos: um ao nível do grupo de recursos e outro ao nível da subscrição.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Aplicar o Benefício Híbrido Azure na VM criar tempo
Além de aplicar o Benefício Híbrido Azure aos VMs existentes, pode invocá-lo no momento da criação de VM. Os benefícios de fazê-lo são três vezes:
- Pode for-se o PAYG e o BYOS VMs utilizando a mesma imagem e processo.
- Permite futuras alterações no modo de licenciamento, algo que não está disponível com uma imagem apenas BYOS ou se trouxer o seu próprio VM.
- O VM será ligado à Infraestrutura de Atualização do Chapéu Vermelho (RHUI) por padrão, para garantir que permanece atualizado e seguro. Pode alterar o mecanismo atualizado após a sua implantação a qualquer momento.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Verifique o estado de Benefício Híbrido Azure de um VM
Pode ver o estado de Benefício Híbrido Azure de um VM utilizando o CLI Azure ou utilizando o Serviço de Metadados de Instância Azure.

### <a name="azure-cli"></a>CLI do Azure

Pode usar o `az vm get-instance-view` comando para este fim. Procure um `licenseType` campo na resposta. Se o `licenseType` campo existir e o valor for `RHEL_BYOS` `SLES_BYOS` ou, o seu VM tem o benefício ativado.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

A partir do próprio VM, pode consultar os metadados atestados no Serviço de Metadados de Instância Azure para determinar o valor do `licenseType` VM. Um `licenseType` valor de ou `RHEL_BYOS` `SLES_BYOS` indicará que o seu VM tem o benefício ativado. [Saiba mais sobre metadados atestados.](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformidade

### <a name="red-hat"></a>Red Hat

Os clientes que utilizam o Azure Hybrid Benefit para a RHEL concordam com os [termos legais](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) padrão e [a declaração de privacidade](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associada às ofertas RHEL do Azure Marketplace.

Os clientes que utilizam o Azure Hybrid Benefit para a RHEL têm três opções para fornecer atualizações de software e patches a esses VMs:

- [Infraestrutura de atualização de chapéu vermelho](../workloads/redhat/redhat-rhui.md) (opção padrão)
- Servidor de satélite de chapéu vermelho
- Gestor de assinatura de chapéu vermelho

Os clientes que escolherem a opção RHUI podem continuar a usar o RHUI como principal fonte de atualização para os seus VMs Azure Hybrid Benefit RHEL sem anexar subscrições RHEL a esses VMs. Os clientes que escolherem a opção RHUI são responsáveis por garantir o cumprimento da subscrição RHEL.

Os clientes que escolherem o Red Hat Satellite Server ou o Red Hat Subscription Manager devem remover a configuração RHUI e, em seguida, anexar uma subscrição RHEL ativada pelo Cloud Access aos seus VMs RHEL de Benefício Híbrido Azure.  

Para obter mais informações sobre a conformidade com a subscrição da Red Hat, atualizações de software e fontes para VMs Azure Hybrid Benefit RHEL, consulte o artigo da Red Hat sobre a [utilização de subscrições RHEL com Azure Hybrid Benefit](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Para utilizar o Azure Hybrid Benefit para os seus VMs SLES e para obter informações sobre a mudança do SLES PAYG para BYOS ou passar de SLES BYOS para PAYG, consulte [a SUSE Linux Enterprise e a Azure Hybrid Benefit.](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/) 

## <a name="azure-hybrid-benefit-on-reserved-instances-is-in-preview"></a>Azure Hybrid Benefit em Instâncias Reservadas está em Pré-Visualização

As Reservas Azure (Azure Reserved Virtual Machine Instances) ajudam-no a economizar dinheiro comprometendo-se a planos de um ano ou três anos para vários produtos. Pode saber mais sobre [instâncias reservadas aqui.](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations) O Benefício Híbrido Azure está disponível em Pré-visualização para [A1314(RIs)](https://review.docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations#charges-covered-by-reservation). Isto significa que se você tiver comprado custos de computação a uma taxa descontada usando RI, você pode aplicar benefício AHB sobre os custos de licenciamento para RHEL e SUSE em cima dele. As medidas para aplicar o benefício AHB para uma instância RI permanecem exatamente as mesmas que para um VM regular.
![AHB para RIs](./media/azure-hybrid-benefit/reserved-instances.png)

>[!NOTE]
>Se já adquiriu reservas para o software RHEL ou SUSE PAYG no Azure Marketplace, por favor, aguarde que o mandato da reserva esteja concluído antes de utilizar o Azure Hybrid Benefit..


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
*P: Posso usar um tipo de licença `RHEL_BYOS` com uma imagem SLES, ou vice-versa?*

A: Não, não pode. Tentar introduzir um tipo de licença que corresponda incorretamente à distribuição em execução no seu VM não atualizará nenhum metadados de faturação. Mas se introduzir acidentalmente o tipo de licença errada, atualizar o seu VM novamente para o tipo de licença correta ainda permitirá o benefício.

*P: Registei-me no Red Hat Cloud Access mas ainda não posso permitir o benefício nos meus VMs RHEL. O que devo fazer?*

R: Pode levar algum tempo para que o seu registo de subscrição do Red Hat Cloud Access se propague de Chapéu Vermelho para Azure. Se ainda vir o erro após um dia útil, contacte o suporte da Microsoft.

*P: Implementei um VM usando a "imagem dourada" do RHEL BYOS. Posso converter a faturação nestas imagens da BYOS para PAYG?*

A: Não, não pode. O Azure Hybrid Benefit suporta a conversão apenas em imagens pay-as-you-go.

*P: Enviei a minha própria imagem RHEL do local (via Azure Migrate, Azure Site Recovery, ou de outra forma) para Azure. Posso converter a faturação nestas imagens da BYOS para PAYG?*

A: Não, não pode. A capacidade Azure Hybrid Benefit está atualmente disponível apenas para imagens RHEL e SLES no Azure Marketplace. 

*P: Enviei a minha própria imagem RHEL do local (via Azure Migrate, Azure Site Recovery, ou de outra forma) para Azure. Preciso de fazer alguma coisa para me beneficiar do Azure Hybrid Benefit?*

A: Não, não tem. As imagens RHEL que faz o upload já são consideradas BYOS, e são cobradas apenas pelos custos de infraestrutura da Azure. Você é responsável pelos custos de subscrição da RHEL, assim como você é para os seus ambientes no local. 

*P: Posso utilizar o Azure Hybrid Benefit em VMs implantados a partir de imagens Azure Marketplace RHEL e SLES SAP?*

A: Sim, pode. Pode utilizar o tipo de licença `RHEL_BYOS` para VMs RHEL e `SLES_BYOS` para conversões de VMs implantados a partir de imagens Azure Marketplace RHEL e SLES SAP.

*P: Posso utilizar o Azure Hybrid Benefit em conjuntos de escala de máquinas virtuais para RHEL e SLES?*

R: Sim, O Benefício Híbrido Azure em conjuntos de escala de máquinas virtuais para RHEL e SLES está em pré-visualização. Você pode [saber mais sobre este benefício e como usá-lo aqui.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/azure-hybrid-benefit-linux-vmss) 

*P: Posso utilizar o Azure Hybrid Benefit em instâncias reservadas para RHEL e SLES?*

R: Sim, a Azure Hybrid Benefit em instância reservada para RHEL e SLES está em pré-visualização. Você pode [saber mais sobre este benefício e como usá-lo aqui.](#azure-hybrid-benefit-on-reserved-instances-is-in-preview)

*P: Posso utilizar o Azure Hybrid Benefit numa máquina virtual implantada para o SQL Server em imagens RHEL?*

A: Não, não pode. Não há nenhum plano para apoiar estas máquinas virtuais.

*P: Posso utilizar o Azure Hybrid Benefit na minha assinatura RHEL Virtual Data Center?*

A: Não, não pode. A VDC não é suportada em Azure, incluindo a AHB.  
 

## <a name="common-problems"></a>Problemas comuns
Esta secção enumera problemas comuns que poderá encontrar e passos para atenuar.

| Erro | Mitigação |
| ----- | ---------- |
| "A ação não pôde ser concluída porque os nossos registos mostram que não habilitou com sucesso o Red Hat Cloud Access na sua subscrição do Azure...." | Para utilizar o benefício com os VMs RHEL, tem primeiro de [registar as suas subscrições Azure com o Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Passos seguintes
* [Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para benefício híbrido Azure utilizando o CLI Azure](/cli/azure/vm)
