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
ms.openlocfilehash: feaa2471f2867257deb06ab32ed5fc0a26a0d37e
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443437"
---
# <a name="public-preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Antevisão pública: Azure Hybrid Benefit – como se aplica às máquinas virtuais Linux

## <a name="overview"></a>Descrição geral

O Azure Hybrid Benefit permite-lhe migrar mais facilmente as suas máquinas virtuais Red Hat Enterprise Linux (RHEL) e SUSE Linux Enterprise Server (SLES) para Azure utilizando a sua própria subscrição de software Red Hat ou SUSE pré-existente. Com este benefício, você só paga pelos custos de infraestrutura do seu VM porque a taxa de software é coberta pela sua subscrição RHEL ou SLES. O benefício é aplicável a todas as imagens pay-as-you-go (PAYG) da RHEL e do SLES Marketplace.

> [!IMPORTANT]
> A Azure Hybrid Benefit for Linux VMs está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Descrição de benefícios

Através do Azure Hybrid Benefit, pode migrar mais facilmente os seus servidores RHEL e SLES no local para Azure, convertendo os VMs RHEL e SLES PAYG existentes no Azure para trazer a sua própria subscrição (BYOS). Normalmente, os VM implantados a partir de imagens PAYG no Azure cobrarão uma taxa de infraestrutura, bem como uma taxa de software. Com o Azure Hybrid Benefit, os VMs PAYG podem ser convertidos para um modelo de faturação BYOS sem uma redistribuição, evitando qualquer risco de inatividade.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Azure Hybrid Benefit custou visualização em VMs Linux.":::

Ao permitir o benefício num RHEL ou SLES VM, deixará de ser cobrado pela taxa adicional de software normalmente incorrida num VM PAYG. Em vez disso, o seu VM começará a emitir uma taxa BYOS, que inclui apenas a taxa de hardware computacional e nenhuma taxa de software.

Se desejar, também pode converter um VM que tenha o benefício ativado de volta a um modelo de faturação PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Âmbito de elegibilidade do Benefício Híbrido Azure para os VMs Linux

A Azure Hybrid Benefit está disponível para todas as imagens RHEL e SLES Marketplace PAYG. O benefício ainda não está disponível para imagens RHEL ou SLES Marketplace BYOS ou imagens personalizadas.

Instâncias Reservadas, Anfitriões Dedicados e Benefícios Híbridos SQL não são elegíveis para o Benefício Híbrido Azure se já estiver a utilizar o benefício com os VMs Linux.

## <a name="how-to-get-started"></a>Como começar

A Azure Hybrid Benefit está atualmente em fase de pré-visualização para Os VMs Do Linux. Assim que tiver acesso à pré-visualização, poderá ativar o benefício utilizando o Azure CLI.

### <a name="public-preview"></a>Pré-visualização pública

A Azure Hybrid Benefit (para Linux) está agora em fase de pré-visualização pública. Pode utilizar os passos abaixo para permitir o benefício para as distribuições de Chapéu Vermelho e SUSE. 

### <a name="red-hat-customers"></a>Clientes da Red Hat

1.    Registe-se com o [programa Red Hat Cloud Access](https://aka.ms/rhel-cloud-access)
1.    Ativar as suas subscrições Azure para o Cloud Access e ativar as subscrições que contêm os VMs com que pretende utilizar o benefício
1.    Aplique o benefício aos seus VM existentes, quer através do Azure CLI
1.    Registe os seus VMs recebendo o benefício com uma fonte separada de atualizações


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

Para utilizar o Azure Hybrid Benefit para os seus VMs RHEL, tem primeiro de estar registado no programa Red Hat Cloud Access. Pode fazê-lo através do site Red Hat Cloud Access aqui. Uma vez ativado o benefício no seu VM, tem de registar o VM com a sua própria fonte de atualizações, quer com o Red Hat Subscription Manager quer com o Red Hat Satellite. Registar-se para atualizações garantirá que permanece em estado de apoio.

### <a name="suse"></a>SUSE

Para utilizar o Azure Hybrid Benefit para os seus VMs SLES, tem primeiro de estar registado no programa SUSE Public Cloud. Saiba mais sobre o programa aqui. Uma vez adquiridas as subscrições SUSE, tem de registar os seus VMs utilizando essas subscrições para a sua própria fonte de atualizações utilizando o SUSE Customer Center, o servidor de Ferramenta de Gestão de Subscrição ou o Gestor SUSE.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
*P: Posso usar um tipo de licença de "RHEL_BYOS" com uma imagem SLES, ou vice-versa?*

A: Não, não pode. Tentar introduzir um tipo de licença que corresponda incorretamente ao distro em execução no seu VM não atualizará nenhum metadados de faturação. No entanto, se introduzir acidentalmente o tipo de licença errada, atualizar o seu VM novamente para o tipo de licença correta ainda permitirá o benefício.

*P: Registei-me no Red Hat Cloud Access mas ainda não consigo permitir o benefício nos meus VMs RHEL. O que faço eu?*

R: Pode levar algum tempo para que o seu registo de subscrição do Red Hat Cloud Access se propague de Chapéu Vermelho para Azure. Se ainda estiver a ver o erro após um dia útil, contacte o suporte da Microsoft.

## <a name="common-issues"></a>Problemas comuns
Esta secção contém uma lista de questões comuns que podem ser encontradas e passos para mitigação.

| Erro | Mitigação |
| ----- | ---------- |
| "A ação não pôde ser concluída porque os nossos registos mostram que não habilitou com sucesso o Red Hat Cloud Access na sua subscrição do Azure...." | Para utilizar o benefício com os VMs RHEL, tem primeiro de registar as suas subscrições Azure com o Red Hat Cloud Access. Visite este link para saber mais sobre como registar as suas subscrições Azure para o Red Hat Cloud Access

## <a name="next-steps"></a>Passos seguintes
* Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para Azure Hybrid Benefit usando [o Azure CLI aqui.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
