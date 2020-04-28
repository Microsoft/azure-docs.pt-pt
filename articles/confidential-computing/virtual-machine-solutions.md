---
title: Soluções de computação confidencial azure em máquinas virtuais
description: Conheça soluções de computação confidencial azure em máquinas virtuais.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: e574ac33e5f7da814c4bd813fc1c083c7cb4c2c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187890"
---
# <a name="solutions-on-azure-virtual-machines"></a>Soluções em máquinas virtuais Azure

Este artigo cobre informações sobre a implementação de máquinas virtuais de computação confidencial do Azure (VMs) que executam processadores Intel apoiados pela [Extensão](https://software.intel.com/sgx) da Guarda de Software Intel (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Tamanhos de VM de computação confidencial Azure

As máquinas virtuais de computação confidencial Azure são projetadas para proteger a confidencialidade e integridade dos seus dados e código enquanto são processados na nuvem 

[Série DCsv2](../virtual-machines/dcv2-series.md) VMs são a mais recente e mais recente família de tamanho sinuoso confidencial. Estes VMs suportam uma maior variedade de capacidades de implantação, têm 2x a Cache de Página enclave (EPC) e uma maior seleção de tamanhos em comparação com os nossos VMs série dc. Os VMs da série DC estão atualmente em [pré-visualização](../virtual-machines/sizes-previous-gen.md#preview-dc-series) e serão depreciados e não incluídos na disponibilidade geral.

Comece a implementar um VM série DCsv2 através do mercado comercial da Microsoft seguindo o [tutorial quickstart](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Tamanhos e regiões disponíveis atuais

Para obter uma lista de todos os tamanhos de VM de computação confidencial geralmente disponíveis nas regiões disponíveis e nas zonas de disponibilidade, executar o seguinte comando no [Azure CLI:](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

A partir de abril de 2020, estas UsK estão disponíveis nas seguintes regiões e zonas de disponibilidade:

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

Para uma visão mais detalhada dos tamanhos acima, execute o seguinte comando:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>Considerações sobre implementação

Siga um tutorial de arranque rápido para implantar uma máquina virtual dCsv2-Series em menos de 10 minutos. 

- **Subscrição Azure** – Para implementar uma instância de VM de computação confidencial, considere uma subscrição pay-as-you-go ou outra opção de compra. Se estiver a usar uma [conta azure gratuita,](https://azure.microsoft.com/free/)não terá quota para a quantidade adequada de núcleos de computação Azure.

- **Preços e disponibilidade regional** - Encontre os preços dos VMs da Série DCsv2 na página de preços das [máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter disponibilidade nas regiões de Azure.


- **Quota de cores** – Poderá ser necessário aumentar a quota de núcleos na sua subscrição Azure a partir do valor predefinido. A sua subscrição também pode limitar o número de núcleos que pode implantar em certas famílias de tamanho VM, incluindo a Série DCsv2. Para solicitar um aumento de quota, [abra um pedido de apoio](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) ao cliente online gratuitamente. Nota, os limites de predefinição podem variar dependendo da sua categoria de subscrição.

  > [!NOTE]
  > Contacte o Suporte Azure se tiver necessidades de capacidade em larga escala. As quotas azure são limites de crédito, não garantias de capacidade. Independentemente da sua quota, só é cobrado por núcleos que usa.
  
- **Redimensionamento** – Devido ao hardware especializado, só é possível redimensionar instâncias de computação confidenciais dentro da mesma família de tamanho. Por exemplo, só é possível redimensionar um VM da série DCsv2 de um tamanho da série DCsv2 para outro. A redimensionamento de um tamanho de computação não confidencial para um tamanho de computação confidencial não é suportada.  

- **Imagem** – Para fornecer suporte à Extensão da Guarda de Software Intel (Intel SGX) em instâncias confidenciais de cálculo, todas as implementações precisam de ser executadas em imagens da Geração 2. A computação confidencial Azure suporta cargas de trabalho em execução em Ubuntu 18.04 Gen 2, Ubuntu 16.04 Gen 2 e Windows Server 2016 Gen 2. Leia sobre [o apoio à geração 2 VMs no Azure](../virtual-machines/linux/generation-2.md) para saber mais sobre cenários apoiados e não apoiados. 

- **Armazenamento** – Discos de dados de máquinas virtuais de computação confidencial Azure e os nossos discos efémeros os efémeros estão nos discos NVMe. As instâncias suportam apenas discos Premium SSD e Standard SSD, não Ultra SSD ou Standard HDD. O tamanho virtual da máquina **DC8_v2** não suporta armazenamento Premium. 

- **Encriptação do disco** – As instâncias confidenciais de computação não suportam a encriptação do Disco neste momento. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações de elevada disponibilidade e recuperação de desastres

Ao utilizar máquinas virtuais em Azure, é responsável por implementar uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade. 

A computação confidencial azure não suporta a redundância da zona através de Zonas de Disponibilidade neste momento. Para obter a maior disponibilidade e redundância para computação confidencial, utilize [Conjuntos de Disponibilidade](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy). Devido às restrições de hardware, os Conjuntos de Disponibilidade para instâncias confidenciais de computação só podem ter um máximo de 10 domínios de atualização. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Implantação através de um modelo de gestor de recursos azure 

O Azure Resource Manager é o serviço de implementação e gestão do Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua subscrição Azure. Utiliza funcionalidades de gestão, como controlo de acesso, fechaduras e etiquetas, para proteger e organizar os seus recursos após a implantação.

Para saber sobre os modelos do Gestor de Recursos Azure, consulte a visão geral da [implementação](../azure-resource-manager/templates/overview.md)do modelo .

Para implantar um VM série DCsv2 num modelo ARM utilizará o [recurso Máquina Virtual](../virtual-machines/windows/template-description.md). Tem de garantir que especifica as propriedades corretas para **vmSize** e para a sua **imagemReference**.

### <a name="vm-size"></a>Tamanho da VM

Especifique um dos seguintes tamanhos no seu modelo ARM no recurso Máquina Virtual. Esta corda é colocada como **vmSize** em **propriedades**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Imagem gen2 OS

Em **propriedades,** também terá de fazer referência a uma imagem em **armazenamentoPerfil**. Utilize *apenas uma das* seguintes imagens para a sua **imagemReference**.

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Passos Seguintes 

Neste artigo aprendeu sobre as qualificações e configurações necessárias na criação de máquina virtual de computação confidencial. Agora pode dirigir-se ao Azure Marketplace para implantar um VM série DCsv2.

> [!div class="nextstepaction"]
> [Implementar uma máquina virtual série DCsv2 no Mercado Azure](quick-create-marketplace.md)