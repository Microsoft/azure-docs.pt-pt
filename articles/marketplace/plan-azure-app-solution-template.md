---
title: Planeie um modelo de solução para uma oferta de aplicação Azure
description: Saiba o que é necessário para criar um plano de modelo de solução para uma nova oferta de aplicações Azure utilizando o portal de marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: c8ab076392fd9d0bb3a0c780272938918bb69d14
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370182"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Planeie um modelo de solução para uma oferta de aplicação Azure

Este artigo explica os requisitos para a publicação de um plano de modelo de solução para uma oferta de aplicação Azure. Um plano de modelo de solução é um dos dois tipos de planos apoiados pelas ofertas da Azure Application. Para obter informações sobre a diferença entre estes dois tipos de planos, consulte [tipos de planos.](plan-azure-application-offer.md#plans) Se ainda não o fez, leia [Plano uma oferta de candidatura a Azure.](plan-azure-application-offer.md)

O tipo de plano de modelo de solução requer um [modelo de Gestor de Recursos Azure (modelo ARM)](/azure/azure-resource-manager/templates/overview.md) para implantar automaticamente a sua infraestrutura de solução.

## <a name="solution-template-requirements"></a>Requisitos do modelo de solução

| Requisitos | Detalhes |
| ------------ | ------------- |
| Faturação e medição | Os planos de modelo de solução não são transacionáveis, mas podem ser usados para implementar ofertas de VM pagas que são faturadas através do mercado comercial da Microsoft. Os recursos que o modelo ARM da solução implementa são configurados na subscrição Azure do cliente. As máquinas virtuais pay-as-you-go são transacionadas com o cliente através da Microsoft e faturadas através da subscrição Azure do cliente. <br><br> Para a faturação da sua própria licença (BYOL), embora a Microsoft faça contas de custos de infraestrutura que são incorridos na subscrição do cliente, transaciona diretamente as suas taxas de licenciamento de software com o cliente. |
| Disco rígido virtual compatível com Azure (VHD) | Os VMs devem ser construídos em Windows ou Linux. Para obter mais informações, consulte:<ul><li>[Criar um ativo técnico Azure VM](/azure/marketplace/partner-center-portal/vm-certification-issues-solutions#how-to-address-a-vulnerability-or-exploit-in-a-vm-offer.md) (para VHDs windows)</li><li>[Distribuição linux endossada em Azure](/azure/virtual-machines/linux/endorsed-distros.md) (para Linux VHDs).</li></ul> |
| Atribuição de utilização dos clientes | É necessária a atribuição de utilização do cliente em todos os modelos de solução que são publicados no Azure Marketplace. Para obter mais informações sobre a atribuição de utilização do cliente e como a capacitar, consulte a [atribuição de utilização do cliente do parceiro Azure](azure-partner-customer-usage-attribution.md). |
| Utilizar discos geridos | [Os discos geridos](/azure/virtual-machines/windows/managed-disks-overview.md) são a opção padrão para discos de infraestrutura persistidos como um VMs de serviço (IaaS) em Azure. Deve utilizar discos geridos em modelos de solução.<ul><li>Para atualizar os seus modelos de solução, siga as orientações em [Utilizar discos geridos nos modelos Azure Resource Manager](/azure/virtual-machines/using-managed-disks-template-deployments.md), e use as [amostras fornecidas](https://github.com/Azure/azure-quickstart-templates).</li><li>Para publicar o VHD como uma imagem no Azure Marketplace, importe o VHD subjacente dos discos geridos para uma conta de armazenamento utilizando a [Azure PowerShell](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md) ou o [Azure CLI](/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md)</ul> |
| Pacote de implantação | Vai precisar de um pacote de implementação que permitirá aos clientes implementarem o seu plano. Se criar vários planos que exijam a mesma configuração técnica, pode utilizar o mesmo pacote de plano. Para mais detalhes, consulte a secção seguinte: Pacote de implementação. |
|||

## <a name="deployment-package"></a>Pacote de implantação

O pacote de implementação contém todos os ficheiros de modelo necessários para este plano, bem como quaisquer recursos adicionais, embalados como um ficheiro .zip.

Todas as aplicações Azure devem incluir estes dois ficheiros na pasta raiz de um arquivo .zip:

- Um ficheiro de modelo do Gestor de Recursos nomeado [mainTemplate.jsem](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md). Este modelo define os recursos para implantar na subscrição Azure do cliente. Para exemplos de modelos de Gestor de Recursos, consulte a [galeria de modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates/) ou o [gitHub correspondente: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) repo.
- Uma definição de interface de utilizador para a experiência de criação de aplicações Azure chamada [createUiDefinition.jsem](/azure/azure-resource-manager/managed-application-createuidefinition-overview.md). Na interface de utilizador, especifique os elementos que permitem aos consumidores fornecer valores de parâmetros.

Os tamanhos máximos de ficheiro suportados são:

- Até 1 Gb no tamanho total comprimido .zip archive
- Até 1 Gb para qualquer ficheiro individual não comprimido dentro do arquivo .zip

Todas as novas ofertas de aplicações da Azure também devem incluir uma [atribuição de utilização do cliente parceiro Azure](azure-partner-customer-usage-attribution.md) GUID.

## <a name="azure-regions"></a>Regiões do Azure

Pode publicar o seu plano na região pública de Azure, região do Governo de Azure, ou em ambos. Antes de publicar para o [Governo Azure,](/azure/azure-government/documentation-government-manage-marketplace-partners.md)teste e valide o seu plano no ambiente, uma vez que certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste do julgamento do [Governo Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

Você, como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo Azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Para obter uma lista de países e regiões apoiados pelo mercado comercial, consulte a disponibilidade geográfica e o [apoio cambial.](marketplace-geo-availability-currencies.md)

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para consciencializar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem diretamente no programa ou links para descrições da sua conformidade com eles nos seus próprios websites. Estas ligações visíveis apenas aos clientes do Governo Azure.

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver o seu plano

Pode configurar cada plano para ser visível para todos (público) ou apenas para um público específico (privado). Pode criar até 100 planos e até 45 deles podem ser privados. Pode querer criar um plano privado para oferecer diferentes opções de preços ou configurações técnicas a clientes específicos.

Você concede acesso a um plano privado usando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição que você atribui. Pode adicionar um máximo de 10 IDs de subscrição manualmente ou até 10.000 IDs de subscrição usando um . Ficheiro CSV. Os IDs de assinatura Azure são representados como GUIDs e as letras devem ser minúsculas.

> [!NOTE]
> Se publicar um plano privado, pode mudar a sua visibilidade para público mais tarde. No entanto, uma vez publicado um plano público, não se pode mudar a sua visibilidade para privado.

Para planos de modelo de solução, também pode optar por esconder o plano do Azure Marketplace. É melhor fazê-lo se o plano for implementado apenas indiretamente através de outro modelo de solução ou aplicação gerida.

> [!NOTE]
> Os planos privados não são suportados com subscrições Azure estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

Para mais informações, consulte [ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

## <a name="next-steps"></a>Passos seguintes

- [Como criar uma oferta de aplicação Azure no mercado comercial](create-new-azure-apps-offer.md)
