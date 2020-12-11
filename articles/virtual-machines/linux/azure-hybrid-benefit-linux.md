---
title: Azure Hybrid Benefit e Linux VMs
description: O Azure Hybrid Benefit permite-lhe economizar dinheiro nas suas máquinas virtuais Linux que estão a funcionar no Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 5b565a3e653d05ffcecfbc5718c74d07d8f45373
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109169"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Azure Hybrid Benefit – como se aplica às máquinas virtuais Linux

## <a name="overview"></a>Descrição geral

O Azure Hybrid Benefit é um benefício de licenciamento que o ajuda a reduzir significativamente os custos de funcionamento das suas máquinas virtuais Red Hat Enterprise Linux (RHEL) e SUSE Linux Enterprise Server (SLES) na nuvem. Com este benefício, você só paga pelos custos de infraestrutura do seu VM porque a taxa de software é coberta pela sua subscrição RHEL ou SLES. O benefício é aplicável a todas as imagens pay-as-you-go (PAYG) da RHEL e do SLES Marketplace.

> [!IMPORTANT]
> Azure Hybrid Benefit for Linux VMs já está disponível para o público


## <a name="benefit-description"></a>Descrição de benefícios

Através do Azure Hybrid Benefit, pode migrar mais facilmente os seus servidores RHEL e SLES no local para Azure, convertendo os VMs RHEL e SLES PAYG existentes no Azure para trazer a sua própria subscrição (BYOS). Normalmente, os VM implantados a partir de imagens PAYG no Azure cobrarão uma taxa de infraestrutura, bem como uma taxa de software. Com o Azure Hybrid Benefit, os VMs PAYG podem ser convertidos para um modelo de faturação BYOS sem uma redistribuição, evitando qualquer risco de inatividade.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit custou visualização em VMs Linux.":::

Ao permitir o benefício num RHEL ou SLES VM, deixará de ser cobrado pela taxa adicional de software normalmente incorrida num VM PAYG. Em vez disso, o seu VM começará a emitir uma taxa BYOS, que inclui apenas a taxa de hardware computacional e nenhuma taxa de software.

Se desejar, também pode converter um VM que tenha o benefício ativado de volta a um modelo de faturação PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Âmbito de elegibilidade do Benefício Híbrido Azure para os VMs Linux

A Azure Hybrid Benefit está disponível para todas as imagens RHEL e SLES Marketplace PAYG. O benefício ainda não está disponível para imagens RHEL ou SLES Marketplace BYOS ou imagens personalizadas.

Instâncias Reservadas, Anfitriões Dedicados e Benefícios Híbridos SQL não são elegíveis para o Benefício Híbrido Azure se já estiver a utilizar o benefício com os VMs Linux.

## <a name="how-to-get-started"></a>Como começar

### <a name="red-hat-customers"></a>Clientes da Red Hat

O Azure Hybrid Benefit for RHEL está disponível para clientes que tenham subscrições RHEL ativas/não utilizadas que sejam elegíveis para uso no Azure e que tenham ativado uma ou mais dessas subscrições para uso em Azure com o programa [Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 

1.  Ative uma ou mais das suas subscrições RHEL elegíveis para utilização no Azure utilizando a interface do [cliente Red Hat Cloud Access](https://access.redhat.com/management/cloud).
1.  As subscrições Azure que forneceu durante o processo de ativação do Red Hat Cloud Access serão então autorizadas a utilizar a funcionalidade Azure Hybrid Benefit.
1.  Aplique o Benefício Híbrido Azure em qualquer um dos seus VMs RHEL PAYG existentes, bem como quaisquer novos VMs RHEL que implemente a partir de imagens PAYG do Azure Marketplace.
1.  Siga [os próximos passos recomendados](https://access.redhat.com/articles/5419341) para configurar fontes de atualização para os seus VMs RHEL e para as diretrizes de conformidade com a subscrição RHEL.


### <a name="suse-customers"></a>Clientes SUSE

1.    Registe-se no programa SUSE Public Cloud
1.    Aplique o benefício aos seus VM existentes, quer através do Azure CLI
1.    Registe os seus VMs recebendo o benefício com uma fonte separada de atualizações


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Ativar e desativar o benefício no CLI Azure

Pode utilizar o comando 'az vm update' para atualizar os VM existentes. Para os VMs RHEL, executar o comando com um parâmetro do tipo licença de "RHEL_BYOS". Para os SLES VMs, executar o comando com um parâmetro tipo licença de "SLES_BYOS".

#### <a name="cli-example-to-enable-the-benefit"></a>Exemplo do CLI para permitir o benefício:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Exemplo do CLI para desativar o benefício:
Para desativar o benefício, utilize um valor do tipo licença de "Nenhum"
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Exemplo do CLI para permitir o benefício num grande número de VMs
Para ativar o benefício num grande número de VMs, pode utilizar o `--ids` parâmetro no CLI Azure.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Os exemplos a seguir mostram dois métodos para obter uma lista de IDs de recursos – um ao nível do grupo de recursos, um ao nível da subscrição.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Verifique o estado AHB de um VM
Pode ver o estado AHB de um VM de duas formas: utilizar o Azure CLI ou utilizar o Serviço de Metadados de Instância Azure (Azure IMDS).


### <a name="azure-cli"></a>CLI do Azure

O `az vm get-instance-view` comando pode ser utilizado para este fim. Procure um campo de licençaType na resposta. Se o campo de licençaType existe e o valor é 'RHEL_BYOS' ou 'SLES_BYOS', então o seu VM tem o benefício ativado.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

A partir do próprio VM, poderá consultar os Metadados Attestados do IMDS para determinar o IMDS Attested Metadados para determinar o IMDS AttestEd Metadas para determinar o IMM's licenseType. Um valor de licençaType de 'RHEL_BYOS' ou 'SLES_BYOS' indicará que o seu VM tem o benefício ativado. Saiba mais sobre metadados atestados [aqui](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformidade

### <a name="red-hat"></a>Red Hat

Os clientes que utilizam o Benefício Híbrido Azure para a RHEL concordam com os [termos legais](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) padrão e [a declaração de privacidade](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associada às ofertas RHEL do Azure Marketplace.

Os clientes que utilizam o Azure Hybrid Benefit para a RHEL têm três opções para fornecer atualizações de software e patches a esses VMs:

1.  [Infraestrutura de atualização de chapéu vermelho (RHUI)](../workloads/redhat/redhat-rhui.md) (opção padrão)
1.  Servidor de satélite de chapéu vermelho
1.  Gestor de assinatura de chapéu vermelho

Os clientes que escolherem a opção RHUI, podem continuar a utilizar o RHUI como principal fonte de atualização para os seus VMS AHB RHEL sem anexar subscrições RHEL a esses VMs.  Os clientes que escolherem a opção RHUI são responsáveis por garantir o cumprimento da subscrição RHEL.

Os clientes que escolherem o Red Hat Satellite Server ou o Red Hat Subscription Manager devem remover a configuração RHUI e, em seguida, anexar uma subscrição RHEL ativada pelo Cloud Access aos seus VM(s) AHB RHEL.  

Mais informações sobre a conformidade com a subscrição da Red Hat, atualizações de software e fontes para VMs RHEL AHB podem ser encontradas [aqui](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Para utilizar o Azure Hybrid Benefit para os seus VMs SLES, tem primeiro de estar registado no programa SUSE Public Cloud. Saiba mais sobre o programa aqui. Uma vez adquiridas as subscrições SUSE, tem de registar os seus VMs utilizando essas subscrições para a sua própria fonte de atualizações utilizando o SUSE Customer Center, o servidor de Ferramenta de Gestão de Subscrição ou o Gestor SUSE.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
*P: Posso usar um tipo de licença de "RHEL_BYOS" com uma imagem SLES, ou vice-versa?*

A: Não, não pode. Tentar introduzir um tipo de licença que corresponda incorretamente ao distro em execução no seu VM não atualizará nenhum metadados de faturação. No entanto, se introduzir acidentalmente o tipo de licença errada, atualizar o seu VM novamente para o tipo de licença correta ainda permitirá o benefício.

*P: Registei-me no Red Hat Cloud Access mas ainda não consigo permitir o benefício nos meus VMs RHEL. O que faço eu?*

R: Pode levar algum tempo para que o seu registo de subscrição do Red Hat Cloud Access se propague de Chapéu Vermelho para Azure. Se ainda estiver a ver o erro após um dia útil, contacte o suporte da Microsoft.

*P: Implementei um VM usando um RHEL BYOS "Imagem Dourada". Posso converter a faturação nestas imagens da BYOS para PAYG?*

A: Não, não pode. O Azure Hybrid Benefit suporta a conversão apenas em imagens Pay-As-You-Go.

*P: Implementei um VM usando um RHEL BYOS "Imagem Dourada". Posso converter a faturação nestas imagens da BYOS para PAYG?*

A: Não, não pode. O Azure Hybrid Benefit suporta a conversão apenas em imagens Pay-As-You-Go.

*P: Carreguei a minha própria imagem RHEL de on-prem (via Azure Migrate, ASR ou outra) para Azure. Posso converter a faturação nestas imagens da BYOS para PAYG?*

A: Não, não pode. A capacidade Azure Hybrid Benefit está disponível apenas para imagens RHEL e SLES Marketplace hoje em dia. 

*P: Carreguei a minha própria imagem RHEL de on-prem (via Azure Migrate, ASR ou outra) para Azure. Preciso de fazer alguma coisa para me beneficiar do Azure Hybrid Benefit?*

A: Não, não tem. As imagens RHEL que envia já são consideradas BYOS, e só são cobradas pelos custos de infraestrutura Azure. Você é responsável pelos custos de subscrições RHEL, tal como faz para os seus ambientes no local. 

*P: Posso utilizar o Azure Hybrid Benefit em VMs implantados a partir de imagens Marketplace RHEL e SLES SAP?*

A: Sim, pode. Pode utilizar o tipo de licença de 'RHEL_BYOS' para VMs RHEL e 'SLES_BYOS' para conversões de VMs implantados a partir de imagens Marketplace RHEL e SLES SAP.

*P: Posso utilizar o Azure Hybrid Benefit em Conjunto de Escala de Máquina Virtual (VMSS) para RHEL e SLES?*

A: Não, não pode. A VMSS não está no âmbito da Azure Hybrid Benefit hoje em dia para a RHEL e a SLES atualmente.

*P: Posso utilizar o Benefício Híbrido Azure em Instâncias Reservadas (RIs) para RHEL e SLES?*

A: Não, não pode. As IIS não estão no âmbito da Azure Hybrid Benefit hoje em dia para a RHEL e sles atualmente.

*P: Posso utilizar o Azure Hybrid Benefit numa Máquina Virtual implantada para O SERVIDOR SQL em imagens RHEL?*

A: Não, não pode. Não há nenhum plano para apoiá-los.
 

## <a name="common-issues"></a>Problemas comuns
Esta secção contém uma lista de questões comuns que podem ser encontradas e passos para mitigação.

| Erro | Mitigação |
| ----- | ---------- |
| "A ação não pôde ser concluída porque os nossos registos mostram que não habilitou com sucesso o Red Hat Cloud Access na sua subscrição do Azure...." | Para utilizar o benefício com os VMs RHEL, tem primeiro de registar as suas subscrições Azure com o Red Hat Cloud Access. Visite este link para saber mais sobre como registar as suas subscrições Azure para o Red Hat Cloud Access

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para Azure Hybrid Benefit usando [o Azure CLI aqui.](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)