---
title: Visão geral dos VMs do Windows em Azure
description: Visão geral das máquinas virtuais do Windows em Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d0973682a62b17a21557727a8d5eb8fcb7ec7ef1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203376"
---
# <a name="windows-virtual-machines-in-azure"></a>Máquinas virtuais do Windows no Azure

As Máquinas Virtuais (VMs) do Azure são um dos vários tipos de [recursos informáticos a pedido](/azure/architecture/guide/technology-choices/compute-decision-tree), dimensionáveis que o Azure oferece. Normalmente, escolher uma VM, se precisar de mais controlo sobre o ambiente informático que as outras opções oferecem. Este artigo dá-lhe informações sobre o que deve considerar antes de criar uma VM, como criá-la e geri-la.

Uma VM do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a VM. No entanto, ainda tem de manter a VM a realizar tarefas, como configurar, aplicar patches e instalar o software que é executado na mesma.

As máquinas virtuais do Azure podem ser utilizadas de várias formas. Alguns exemplos incluem:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma forma rápida e fácil de criar um computador com as configurações específicas necessárias para codificar e testar uma aplicação.
* **Aplicações na nuvem** – uma vez que a procura da sua aplicação pode variar, em termos económicos, poderá fazer sentido executá-la numa VM no Azure. Paga pelas VMs adicionais quando precisar delas e encerra-as quando não precisar.
* **Datacenter expandido** – as máquinas virtuais numa rede virtual do Azure podem ser facilmente ligadas à rede da sua organização.

O número de VMs que a aplicação utiliza pode ser vertical e horizontalmente aumentado para o valor que for preciso para satisfazer as necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é preciso ter em conta antes de criar uma VM?
Existem sempre inúmeras [considerações de design](/azure/architecture/reference-architectures/n-tier/windows-vm) quando está a desenvolver uma infraestrutura de aplicação no Azure. Estes aspetos de uma VM são importantes e devem ser ponderados antes de começar:

* Os nomes dos recursos da aplicação
* A localização onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operativo que a VM executa
* A configuração da VM depois de iniciar
* Os recursos relacionados que a VM precisa

### <a name="locations"></a>Localizações
Todos os recursos criados no Azure são distribuídos em várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é designada por **localização** ao criar uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela apresenta algumas das formas de obter uma lista de localizações disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione uma localização da lista ao criar uma VM. |
| Azure PowerShell |Use o comando [Get-AzLocation.](/powershell/module/az.resources/get-azlocation) |
| API REST |Utilize a operação [Listar localizações](/rest/api/resources/subscriptions). |
| CLI do Azure |Utilize a operação [az account list-locations](/cli/azure/account). |

### <a name="singapore-data-residency"></a>Residência de dados de Singapura

Em Azure, a funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente apenas disponível na Região do Sudeste Asiático (Singapura) da Asia Pacific Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo. Para mais informações, consulte [o Trust Center.](https://azuredatacentermap.azurewebsites.net/)

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço líder da indústria de 99,9% para máquinas virtuais de instância única, desde que implemente a VM com armazenamento premium para todos os discos.  Para a sua implementação se qualificar para o Contrato de Nível de Serviço de VM de 99,95% standard, continua a ter de implementar duas ou mais VMs que executem a sua carga de trabalho num conjunto de disponibilidade. Um conjunto de disponibilidade garante que as suas VMs são distribuídas em vários domínios de falha nos datacenters do Azure, bem como implementadas em anfitriões com diferentes janelas de manutenção. O [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) completo explica a disponibilidade garantida do Azure em termos globais.


## <a name="vm-size"></a>Tamanho da VM
O [tamanho](../sizes.md) da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. O Azure disponibiliza uma vasta variedade de tamanhos para suportar muitos tipos de utilizações.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) com base no tamanho e sistema operativo do VM. Para horas parciais, o Azure cobra apenas os minutos utilizados. O armazenamento tem um preço à parte e é cobrado separadamente.

## <a name="vm-limits"></a>Limites da VM
A subscrição tem [limites de quota](../../azure-resource-manager/management/azure-subscription-service-limits.md) predefinidos num local que pode afetar a implementação de muitas VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região. Os limites podem ser aumentados ao [preencher um pedido de suporte a pedir um aumento](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Discos do sistema operativo e imagens
As máquinas virtuais utilizam [discos rígidos virtuais (VHDs)](../managed-disks-overview.md) para armazenar o seu sistema operativo (OS) e dados. Os VHDs também são utilizados para as imagens que pode escolher para instalar um SO. 

O Azure oferece muitas [imagens do marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1) para utilizar com diversas versões e tipos de sistemas operativos do Windows Server. A imagens do marketplace são identificadas pelo publicador da imagem, oferta, sku e versão (normalmente, a versão especificada é a mais recente). Apenas os sistemas operativos de 64 bits são suportados. Para obter mais informações sobre os sistemas operativos convidados suportados, as funções e funcionalidades, veja [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte do software do servidor da Microsoft para máquinas virtuais do Microsoft Azure).

Esta tabela mostra algumas formas para encontrar as informações de uma imagem.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Os valores são especificados automaticamente ao selecionar uma imagem a utilizar. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) *-Localização*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) *-Localização* *-Editor editorName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) *-Localização* *-Editor publisherName* -Oferta *Dename* |
| APIs REST |[Listar publicadores de imagem](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Listar ofertas da imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Listar skus da imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| CLI do Azure |[az vm lista de editores --](/cli/azure/vm/image) *localização*<BR>[az vm image list-offers](/cli/azure/vm/image) --Localização *location* --Publicador *publisherName*<BR>[az vm lista de imagens-skus](/cli/azure/vm) *--localização* *--editorname* -- *oferta de oferta*|

Pode optar por [carregar e utilizar a sua própria imagem](upload-generalized-managed.md) e quando o fizer, o nome do publicador, a oferta e o sku não são utilizados.

### <a name="extensions"></a>Extensões
As [extensões](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) da VM fornecem capacidades adicionais à sua VM através da configuração pós-implementação e de tarefas automatizadas.

Estas tarefas comuns podem ser realizadas com extensões:

* **Executar scripts personalizados** – a [Extensão de Script Personalizado](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) ajuda-o a configurar as cargas de trabalho na VM ao executar o script quando a VM está aprovisionada.
* **Implementar e gerir configurações** – a [Extensão da Configuração do Estado Pretendido (DSC) do PowerShell](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) ajuda-o a configurar o DSC numa VM para gerir configurações e ambientes.
* **Recolher dados de diagnóstico** – a [Extensão do Diagnóstico do Azure](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) ajuda-o a configurar a VM para recolher dados de diagnóstico que podem ser utilizados para monitorizar o estado de funcionamento da aplicação.

### <a name="related-resources"></a>Recursos relacionados
Os recursos nesta tabela são utilizados pela VM e têm de existir ou ser criados quando a VM é criada.

| Recurso | Obrigatório | Descrição |
| --- | --- | --- |
| [Grupo de recursos](../../azure-resource-manager/management/overview.md) |Sim |A VM tem de estar contida num grupo de recursos. |
| [Conta de armazenamento](../../storage/common/storage-account-create.md) |Sim |A VM precisa da conta de armazenamento para armazenar os respetivos discos rígidos virtuais. |
| [Rede virtual](../../virtual-network/virtual-networks-overview.md) |Sim |A VM tem de ser um membro de uma rede virtual. |
| [Endereço IP público](../../virtual-network/public-ip-addresses.md) |No |A VM pode ter um endereço IP público atribuído para aceder ao mesmo remotamente. |
| [Interface de rede](../../virtual-network/virtual-network-network-interface.md) |Sim |A VM precisa da interface de rede para comunicar na rede. |
| [Discos de dados](attach-managed-disk-portal.md) |No |A VM pode incluir discos de dados para expandir as capacidades de armazenamento. |


## <a name="data-residency"></a>Residência dos dados

Em Azure, a funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Região Ásia-Pacífico Geo e Brasil Sul (Estado de São Paulo) do Brasil Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo. Para mais informações, consulte [o Trust Center.](https://azuredatacentermap.azurewebsites.net/)


## <a name="next-steps"></a>Passos seguintes

Crie o seu primeiro VM!

- [Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [CLI do Azure](quick-create-cli.md)