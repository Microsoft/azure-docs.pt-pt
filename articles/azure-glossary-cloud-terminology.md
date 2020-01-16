---
title: Glossário do Azure-dicionário do Azure | Microsoft Docs
description: Use o Glossário do Azure para entender a terminologia de nuvem na plataforma do Azure. Este pequeno dicionário do Azure fornece definições para termos comuns de nuvem para o Azure.
keywords: Dicionário do Azure, terminologia de nuvem, Glossário do Azure, definições de terminologia, termos de nuvem
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 7c80ba6b15cec08da9bcefea243493a824ef66bd
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978765"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário de Microsoft Azure: um dicionário de terminologia de nuvem na plataforma do Azure

O Glossário de Microsoft Azure é um pequeno dicionário de terminologia de nuvem para a plataforma do Azure. Veja também:

* [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definições de serviços do Azure e suas contrapartes AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termos de computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -termos gerais de nuvem do setor.

## <a name="account"></a>conta
Uma conta que é usada para acessar e gerenciar uma assinatura do Azure. Geralmente, ele é conhecido como uma conta do Azure, embora uma conta possa ser qualquer uma delas: um trabalho existente, escolar ou pessoal conta Microsoft ou um nome de usuário e senha do Office 365. Você também pode criar uma conta para gerenciar uma assinatura do Azure ao se inscrever para a [avaliação gratuita](https://azure.microsoft.com).  
Consulte [inscrever-se para uma assinatura do Azure com sua conta do Office 365](cost-management-billing/manage/office-365-account-for-azure-subscription.md) e [contas que você pode usar para entrar](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplicação de API
Outro nome para o [aplicativo do serviço de aplicativo](#app-service-app).

## <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Os recursos de computação que o [serviço Azure app](app-service/overview.md) fornece para hospedar um site ou aplicativo Web, uma API da Web ou um [back-end de aplicativo móvel](app-service-mobile/app-service-mobile-value-prop.md). Os aplicativos do serviço de aplicativo também são chamados de *serviços de aplicativos*, *aplicativos Web*, *aplicativos de API*e *aplicativos móveis*.

## <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais que são gerenciadas em conjunto para fornecer confiabilidade e redundância de aplicativos. O uso de um conjunto de disponibilidade garante que, durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual esteja disponível.  
Consulte [gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Modelo de implantação clássico do Azure
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usados para implantar recursos no Azure (o novo modelo é Azure Resource Manager). Alguns serviços do Azure dão suporte apenas ao modelo de implantação do Gerenciador de recursos, alguns dão suporte apenas ao modelo de implantação clássico e alguns dão suporte a ambos. A documentação para cada serviço do Azure especifica a quais modelos eles dão suporte.

## <a name="cli"></a>CLI (interface de linha de comando) do Azure
Uma interface de linha de comando que pode ser usada para gerenciar os serviços do Azure do Windows, macOS e Linux.  Alguns serviços ou recursos de serviço podem ser gerenciados somente por meio do PowerShell ou da CLI. Consulte [CLI do Azure](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Uma interface de linha de comando para gerenciar os serviços do Azure por meio de uma linha de comando de computadores Windows. Alguns serviços ou recursos de serviço podem ser gerenciados somente por meio do PowerShell ou da CLI.
Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Modelo de implantação de Azure Resource Manager
Um dos dois [modelos de implantação](resource-manager-deployment-model.md) usados para implantar recursos no Microsoft Azure (o outro é o modelo de implantação clássico). Alguns serviços do Azure dão suporte apenas ao modelo de implantação do Gerenciador de recursos, alguns dão suporte apenas ao modelo de implantação clássico e alguns dão suporte a ambos. A documentação para cada serviço do Azure especifica a quais modelos eles dão suporte.

## <a name="fault-domain"></a>domínio de falha
A coleção de máquinas virtuais em um conjunto de disponibilidade que pode falhar ao mesmo tempo. Um exemplo é um grupo de computadores em um rack que compartilham uma fonte de energia e um comutador de rede comuns. No Azure, as máquinas virtuais em um conjunto de disponibilidade são separadas automaticamente entre vários domínios de falha.  
Consulte [gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>graficamente
Um limite definido para residência de dados que normalmente contém duas ou mais regiões. Os limites podem estar dentro ou fora das bordas nacionais e são influenciados pela regulamentação fiscal. Cada área geográfica tem pelo menos uma região. Exemplos de áreas geográficas são Pacífico Asiático e Japão. Também chamada de *geografia*.  
Confira [regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>replicação geográfica
O processo de replicação automática de conteúdo como BLOBs, tabelas e filas em um par regional.  
Consulte [replicação geográfica ativa para o banco de dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>imagem
Um arquivo que contém o sistema operacional e a configuração do aplicativo que podem ser usados para criar qualquer número de máquinas virtuais. No Azure, há dois tipos de imagens: imagem de VM e imagem do sistema operacional. Uma imagem de VM inclui um sistema operacional e todos os discos anexados a uma máquina virtual quando a imagem é criada. Uma imagem do so contém apenas um sistema operacional generalizado sem configurações de disco de dados.  
Consulte [navegar e selecionar imagens de máquina virtual do Windows no Azure com o PowerShell ou a CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limites
O número de recursos que podem ser criados ou o parâmetro de comparação de desempenho que pode ser obtido. Os limites normalmente são associados a assinaturas, serviços e ofertas.  
Confira [assinatura do Azure e limites de serviço, cotas e restrições](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>balanceador de carga
Um recurso que distribui o tráfego de entrada entre computadores em uma rede. No Azure, um balanceador de carga distribui o tráfego para máquinas virtuais definidas em um conjunto de balanceadores de carga. Um [balanceador de carga](load-balancer/load-balancer-overview.md) pode ser voltado para a Internet ou pode ser interno.  

## <a name="mobile-app"></a>aplicativo móvel
Outro nome para o [aplicativo do serviço de aplicativo](#app-service-app).

## <a name="offer"></a>oferta
Os preços, os créditos e os termos relacionados aplicáveis a uma assinatura do Azure.  
Consulte a [página de detalhes da oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
O portal da Web seguro usado para implantar e gerenciar serviços do Azure.

## <a name="region"></a>.
Uma área em uma geografia que não cruza bordas nacionais e contém um ou mais datacenters. Preços, serviços regionais e tipos de oferta são expostos no nível de região. Uma região normalmente é emparelhada com outra região, que pode ter até várias centenas de quilômetros de distância. Os pares regionais podem ser usados como um mecanismo para a recuperação de desastres e cenários de alta disponibilidade. Também conhecido como *local*.  
Confira [regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
Um item que faz parte de sua solução do Azure. Cada serviço do Azure permite que você implante diferentes tipos de recursos, como bancos de dados ou máquinas virtuais.   
Consulte a [visão geral de Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>grupo de recursos
Um contêiner no Gerenciador de recursos que contém recursos relacionados para um aplicativo. O grupo de recursos pode incluir todos os recursos de um aplicativo ou apenas os recursos que estão logicamente agrupados. Pode decidir como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.  
Consulte a [visão geral de Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="arm-template"></a>Modelo do Resource Manager
Um arquivo JSON que declarativamente define um ou mais recursos do Azure e que define as dependências entre os recursos implantados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida.  
Consulte [criação de modelos de Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>provedor de recursos
Um serviço que fornece os recursos que você pode implantar e gerenciar por meio do Resource Manager. Cada fornecedor de recursos oferece operações para trabalhar com os recursos implementados. Os provedores de recursos podem ser acessados por meio do portal do Azure, Azure PowerShell e vários SDKs de programação.  
Consulte a [visão geral de Azure Resource Manager](azure-resource-manager/management/overview.md)

## <a name="role"></a>role
Um meio para controlar o acesso que pode ser atribuído a usuários, grupos e serviços. As funções são capazes de executar ações como criar, gerenciar e ler recursos do Azure.  
Confira [RBAC: funções internas](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>SLA (contrato de nível de serviço)
O contrato que descreve os compromissos da Microsoft quanto ao tempo de atividade e à conectividade. Cada serviço do Azure tem um SLA específico.  
Consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>assinatura de acesso compartilhado (SAS)
Uma assinatura que permite que você conceda acesso limitado a um recurso, sem expor sua chave de conta. Por exemplo, o [armazenamento do Azure usa SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para conceder acesso de cliente a objetos como BLOBs. O [Hub IOT usa SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) para conceder aos dispositivos permissão para enviar telemetria.

## <a name="storage-account"></a>conta de armazenamento
Uma conta que fornece acesso aos serviços de BLOB, fila, tabela e arquivo do Azure no armazenamento do Azure. O nome da conta de armazenamento define o namespace exclusivo para objetos de dados do armazenamento do Azure.  
Consulte [sobre as contas de armazenamento do Azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subscrição
Um contrato do cliente com a Microsoft que permite que eles obtenham os serviços do Azure. Os preços da assinatura e os termos relacionados são regidos pela oferta escolhida para a assinatura.
Consulte [contrato de assinatura do Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) e [como as assinaturas do Azure estão associadas a Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tag
Um termo de indexação que permite categorizar recursos de acordo com seus requisitos de gerenciamento ou cobrança. Quando você tem uma coleção complexa de recursos, pode usar marcas para visualizar esses ativos da maneira que faz mais sentido. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento.  
Consulte [usando marcas para organizar os recursos do Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>atualizar domínio
A coleção de máquinas virtuais em um conjunto de disponibilidade que são atualizadas ao mesmo tempo. As máquinas virtuais no mesmo domínio de atualização são reiniciadas juntas durante a manutenção planejada. O Azure nunca reinicia mais de um domínio de atualização por vez. Também conhecido como um domínio de atualização.  
Consulte [gerenciar a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerenciar a disponibilidade de máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>máquina virtual
A implementação de software de um computador físico que executa um sistema operacional. Várias máquinas virtuais podem ser executadas simultaneamente no mesmo hardware. No Azure, as máquinas virtuais estão disponíveis em uma variedade de tamanhos.  
Consulte a [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>extensão da máquina virtual
Um recurso que implementa comportamentos ou recursos que ajudam outros programas a funcionar ou a oferecer a capacidade de interagir com um computador em execução. Por exemplo, você pode usar a extensão de acesso à VM para redefinir ou modificar os valores de acesso remoto em uma máquina virtual do Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Consulte [sobre recursos e extensões de máquina virtual (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [sobre recursos e extensões de máquina virtual (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>rede virtual
Uma rede que fornece conectividade entre os recursos do Azure que são isolados de todos os outros locatários do Azure. Um [Gateway de VPN do Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) permite estabelecer conexões entre redes virtuais e entre uma rede virtual e uma rede local. Você pode controlar totalmente os blocos de endereços IP, as configurações de DNS, as políticas de segurança e as tabelas de rotas em uma rede virtual.  
Consulte [visão geral da rede virtual](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplicação Web
Outro nome para o [aplicativo do serviço de aplicativo](#app-service-app).

## <a name="see-also"></a>Ver também

* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Centro de recursos de nuvem](https://azure.microsoft.com/resources/)  
* [Azure para seu aplicativo de negócios](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure em seu datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

