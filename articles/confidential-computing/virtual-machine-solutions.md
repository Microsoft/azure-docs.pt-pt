---
title: Soluções de computação confidencial Azure em máquinas virtuais
description: Saiba mais sobre soluções de computação confidencial da Azure em máquinas virtuais.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818967"
---
# <a name="solutions-on-azure-virtual-machines"></a>Soluções em máquinas virtuais Azure

Este artigo abrange informações sobre a implementação de máquinas virtuais de computação confidencial (VMs) que executam processadores Intel apoiados pela [Intel Software Guard Extension](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Tamanhos VM de computação confidencial Azure

As máquinas virtuais de computação confidencial da Azure são projetadas para proteger a confidencialidade e a integridade dos seus dados e código enquanto são processadas na nuvem 

[Série DCsv2](../virtual-machines/dcv2-series.md) Os VMs são a mais recente e mais recente família de computação confidencial. Estes VMs suportam uma maior variedade de capacidades de implementação, têm 2x a Cache da Página do Enclave (EPC) e uma maior seleção de tamanhos em comparação com os nossos VMs DC-Series. Os [VMs da Série DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) estão atualmente em pré-visualização e serão depreciados e não incluídos na disponibilidade geral.

Comece a implementar um VM DCsv2-Series através do mercado comercial da Microsoft seguindo o [tutorial de arranque rápido](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Tamanhos e regiões disponíveis atuais

Para obter uma lista de todos os tamanhos de VM confidenciais geralmente disponíveis em regiões disponíveis e zonas de disponibilidade, executar o seguinte comando no [Azure CLI](/cli/azure/install-azure-cli-windows):

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Para uma visão mais detalhada dos tamanhos acima, executar o seguinte comando:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Requisitos dedicados do anfitrião
Implantando um **Standard_DC8_v2** tamanho de máquina virtual no DCSv2-Series família VM ocupará o anfitrião completo e não será partilhado com outros inquilinos ou subscrições. Esta família VM SKU proporciona o isolamento que você pode precisar para satisfazer os requisitos regulamentares de conformidade e segurança que normalmente são cumpridos por ter um serviço de hospedo dedicado. Quando escolher **Standard_DC8_v2** SKU, o servidor de anfitrião físico irá alocar todos os recursos de hardware disponíveis, incluindo a memória EPC apenas à sua máquina virtual. Por favor, note que esta funcionalidade existe através do design de infraestruturas e todas as funcionalidades do **Standard_DC8_v2** serão suportadas. Esta implementação não é a mesma que o serviço [Azure Dedicad Host](../virtual-machines/dedicated-hosts.md) que é fornecido por outras Famílias Azure VM.


## <a name="deployment-considerations"></a>Considerações sobre implementação

Siga um tutorial de arranque rápido para implementar uma máquina virtual DCsv2-Series em menos de 10 minutos. 

- **Subscrição do Azure** – Para implementar uma instância VM de computação confidencial, considere uma subscrição pay-as-you-go ou outra opção de compra. Se estiver a utilizar uma [conta Azure gratuita,](https://azure.microsoft.com/free/)não terá quota para a quantidade apropriada de núcleos computacional Azure.

- **Preços e disponibilidade regional** - Encontre os preços dos VMs DCsv2-Series na [página de preços da máquina virtual](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para obter disponibilidade nas regiões de Azure.


- **Cota de núcleos** – Poderá ter de aumentar a quota de núcleos na sua subscrição Azure a partir do valor predefinido. A sua subscrição também pode limitar o número de núcleos que pode implementar em certas famílias de tamanho VM, incluindo a Série DCsv2. Para solicitar um aumento de quota, [abra gratuitamente um pedido de apoio](../azure-portal/supportability/per-vm-quota-requests.md) ao cliente online. Nota: os limites predefinidos podem variar dependendo da sua categoria de subscrição.

  > [!NOTE]
  > Contacte o Suporte Azure se tiver necessidades de capacidade em larga escala. As quotas azure são limites de crédito, não garantias de capacidade. Independentemente da sua quota, só é cobrado pelos núcleos que usa.
  
- **Redimensionamento** – Devido ao seu hardware especializado, só é possível redimensionar casos confidenciais de computação dentro da mesma família. Por exemplo, só é possível redimensionar um VM da série DCsv2 de um tamanho da série DCsv2 para outro. Redimensionar de um tamanho de computação não confidencial para um tamanho de computação confidencial não é suportado.  

- **Imagem** – Para fornecer suporte à Extensão da Intel Software Guard (Intel SGX) em casos confidenciais de computação, todas as implementações devem ser executadas em imagens da Geração 2. A azure confidential computing suporta cargas de trabalho em execução em Ubuntu 18.04 Gen 2, Ubuntu 20.04 Gen 2, Windows Server 2019 gen2 e Windows Server 2016 Gen 2. Leia sobre [o suporte para a geração 2 VMs em Azure](../virtual-machines/generation-2.md) para saber mais sobre cenários suportados e não apoiados. 

- **Armazenamento** – Discos de dados de máquinas virtuais de computação confidencial Azure e os nossos discos efémeros de SISTEMA estão em discos NVMe. As instâncias suportam apenas discos Premium SSD e SSD Standard, não Ultra SSD ou HdD Standard. O tamanho da máquina virtual **DC8_v2** não suporta armazenamento Premium. 

- **Encriptação de** discos – As instâncias de computação confidencial não suportam a encriptação do disco neste momento. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Elevada disponibilidade e considerações de recuperação de desastres

Ao utilizar máquinas virtuais em Azure, é responsável pela implementação de uma solução de alta disponibilidade e recuperação de desastres para evitar qualquer tempo de inatividade. 

A azure computação confidencial não suporta redundância de zona através de Zonas de Disponibilidade neste momento. Para obter a maior disponibilidade e redundância para computação confidencial, utilize [Conjuntos de Disponibilidade](../virtual-machines/availability-set-overview.md). Devido às restrições de hardware, os Conjuntos de Disponibilidade para instâncias confidenciais de computação só podem ter um máximo de 10 domínios de atualização. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Implementação com o modelo de gestor de recursos Azure (ARM)

O Azure Resource Manager é o serviço de implementação e gestão do Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua subscrição Azure. Pode utilizar funcionalidades de gestão, como controlo de acessos, fechaduras e tags, para proteger e organizar os seus recursos após a implantação.

Para saber mais sobre os modelos ARM, consulte [a visão geral da implementação do modelo](../azure-resource-manager/templates/overview.md).

Para implementar um VM DCsv2-Series num modelo ARM, utilizará o [recurso Máquina Virtual](../virtual-machines/windows/template-description.md). Certifique-se de que especifica as propriedades corretas para **o vmSize** e para a sua **imagemReferência**.

### <a name="vm-size"></a>Tamanho da VM

Especifique um dos seguintes tamanhos no seu modelo ARM no recurso Máquina Virtual. Esta corda é colocada como **vmSize** em **propriedades.**

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Imagem de Gen2 OS

Em **propriedades,** também terá de fazer referência a uma imagem em **armazenamentoProfile.** Utilize *apenas uma das* seguintes imagens para a sua **imagemReferência**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
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
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Passos seguintes 

Neste artigo, aprendeu sobre as qualificações e configurações necessárias para criar uma máquina virtual de computação confidencial. Agora pode dirigir-se ao Microsoft Azure Marketplace para implementar um VM DCsv2-Series.

> [!div class="nextstepaction"]
> [Implementar uma máquina virtual DCsv2-Series no Mercado Azure](quick-create-marketplace.md)