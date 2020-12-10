---
title: Criar um conjunto do Azure Batch sem endereços IP públicos
description: Saiba como criar uma piscina sem endereços IP públicos
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007675"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Criar um conjunto do Azure Batch sem endereços IP públicos

Quando criar uma piscina Azure Batch, pode providenciar o pool de configuração de máquina virtual sem endereço IP público. Este artigo explica como configurar um pool de Lote sem endereços IP públicos.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Porquê utilizar uma piscina sem endereços IP públicos?

Por predefinição, todos os nós de computação num conjunto de configuração de máquina virtual Azure Batch são atribuídos a um endereço IP público. Este endereço é utilizado pelo serviço Batch para agendar tarefas e para comunicação com nós de computação, incluindo acesso de saída à internet.

Para restringir o acesso a estes nós e reduzir a descoberta destes nós a partir da internet, pode providenciar a piscina sem endereços IP públicos.

> [!IMPORTANT]
> O apoio a piscinas sem endereços IP públicos em Azure Batch está atualmente em pré-visualização pública para as seguintes regiões: França Central, Ásia Oriental, Central Ocidental, Eua Central, Eua Ocidental 2, Leste dos EUA, Norte da Europa, Leste dos EUA, América Central, Europa Ocidental, Norte-Americano, Eua Ocidental, Austrália Oriental, Japão Leste, Japão Ocidental.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- **Autenticação.** Para utilizar uma piscina sem endereços IP públicos dentro de uma [rede virtual,](./batch-virtual-network.md)a API do cliente Batch deve utilizar a autenticação do Azure Ative Directory (AD). O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md). Se não estiver a criar a sua piscina dentro de uma rede virtual, pode ser utilizada a autenticação AD AZure ou a autenticação baseada em chaves.

- **Um Azure VNet.** Se estiver a criar a sua piscina numa [rede virtual,](batch-virtual-network.md)siga estes requisitos e configurações. Para preparar um VNet com uma ou mais sub-redes com antecedência, pode utilizar o portal Azure PowerShell, a Interface Azure Command-Line (CLI) ou outros métodos.
  - A VNet tem de estar na mesma subscrição e região da conta do Batch utilizada para criar o conjunto.
  - A sub-rede especificada para o conjunto deve ter endereços IP não atribuídos suficientes para acomodar o número de VMs direcionadas para o conjunto; ou seja, a soma de propriedades `targetDedicatedNodes` e `targetLowPriorityNodes` do conjunto. Se a sub-rede não tiver endereços IP não atribuídos suficientes, o conjunto atribui parcialmente os nós de computação e ocorre um erro de redimensionamento.
  - Deve desativar as políticas de serviço de ligação privada e de rede de pontos finais. Isto pode ser feito utilizando O Azure CLI: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Para cada 100 nós dedicados ou de baixa prioridade, o Batch atribui um serviço de ligação privada e um equilibrador de carga. Estes recursos estão limitados pelas [quotas de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md) da subscrição. Para piscinas grandes, você pode precisar [solicitar um aumento de quota](batch-quota-limit.md#increase-a-quota) para um ou mais destes recursos. Além disso, não devem ser aplicados bloqueios de recursos a qualquer recurso criado pelo Batch, uma vez que tal impede a limpeza de recursos como resultado de ações iniciadas pelo utilizador, tais como a eliminação de uma piscina ou a redimensionamento para zero.

## <a name="current-limitations"></a>Limitações atuais

1. As piscinas sem endereços IP públicos devem utilizar a Configuração virtual da Máquina e não a Configuração de Serviços de Nuvem.
1. [A configuração personalizada do ponto final](pool-endpoint-configuration.md) para os nós de computação Batch não funciona com piscinas sem endereços IP públicos.
1. Como não existem endereços IP públicos, não pode [utilizar os seus próprios endereços IP públicos especificados](create-pool-public-ip.md) com este tipo de piscina.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Criar uma piscina sem endereços IP públicos no portal Azure

1. No portal do Azure, navegue para a sua conta do Batch.
1. Na janela **Definições** à esquerda, selecione **Pools**.
1. Na janela **Pools,** **selecione Adicionar**.
1. Na janela **Add Pool,** selecione a opção que pretende utilizar a partir do **dropdown do Tipo** de Imagem.
1. Selecione o **Editor/Oferta/Sku** correto da sua imagem.
1. Especifique as definições restantes necessárias, incluindo o tamanho do **nó,** **os nós dedicados ao alvo** e os nós de baixa **prioridade,** bem como quaisquer configurações opcionais desejadas.
1. Selecione opcionalmente uma rede virtual e uma sub-rede que pretende utilizar. Esta rede virtual deve estar no mesmo grupo de recursos que a piscina que está a criar.
1. No **tipo de provisionamento de endereço IP**, selecione **NoPublicIPAddresses**.

![Screenshot do ecrã da piscina Add com noPublicIPAddresses selecionados.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Utilize a API de Lote REST para criar uma piscina sem endereços IP públicos

O exemplo abaixo mostra como usar a [API AZure Batch REST](/rest/api/batchservice/pool/add) para criar uma piscina que utiliza endereços IP públicos.

### <a name="rest-api-uri"></a>URI da API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Corpo do pedido

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Acesso de saída à internet

Numa piscina sem endereços IP públicos, as suas máquinas virtuais não poderão aceder à internet pública a menos que configufique adequadamente a sua configuração de rede, como por exemplo através da utilização [de REDE VIRTUAL NAT](../virtual-network/nat-overview.md). Note que o NAT só permite o acesso de saída à internet a partir das máquinas virtuais na rede virtual. Os nós computacional criados em lote não serão acessíveis ao público, uma vez que não têm endereços IP públicos associados.

Outra forma de fornecer conectividade de saída é usar uma rota definida pelo utilizador (UDR). Isto permite-lhe encaminhar o tráfego para uma máquina de procuração que tem acesso público à Internet.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a criação de piscinas numa rede virtual.](batch-virtual-network.md)
- Saiba como [utilizar pontos finais privados com contas Batch](private-connectivity.md).
