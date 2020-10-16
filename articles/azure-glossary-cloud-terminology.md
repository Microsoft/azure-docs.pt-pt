---
title: Glossário Azure - Dicionário Azure / Microsoft Docs
description: Use o glossário Azure para entender a terminologia em nuvem na plataforma Azure. Este dicionário Azure curto fornece definições para termos comuns em nuvem para Azure.
keywords: Dicionário azul, terminologia em nuvem, glossário azul, definições de terminologia, termos de nuvem
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
ms.openlocfilehash: 63adb5d6898fd467a0557aa147e54f814c167e72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400139"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure glossário: Um dicionário de terminologia em nuvem na plataforma Azure

O glossário microsoft Azure é um pequeno dicionário de terminologia em nuvem para a plataforma Azure. Veja também:

* [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Definições de serviços Azure e seus homólogos AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termos de computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/) em nuvem - termos gerais da indústria em nuvem.

## <a name="account"></a>conta
Uma conta que é usada para aceder e gerir uma subscrição do Azure. É frequentemente referido como uma conta Azure, embora uma conta possa ser qualquer uma delas: um trabalho existente, escola ou conta pessoal da Microsoft. Também pode criar uma conta para gerir uma subscrição do Azure quando se inscreve para o [teste gratuito.](https://azure.microsoft.com)  
Consulte [a inscrição para uma subscrição do Azure com a sua conta Microsoft 365](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) e [contas que pode utilizar para iniciar scontabilidade](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>App API
Outro nome para [app Service service](#app-service-app).

## <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Os recursos computacional que [o Azure App Service](app-service/overview.md) fornece para hospedar um site ou aplicação web, API web ou [backend de aplicações móveis](app-service-mobile/app-service-mobile-value-prop.md). As aplicações do Serviço de Aplicações também são referidas como *Serviços de Aplicações,* *aplicações web,* *aplicações API*e *aplicações móveis.*

## <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais que são geridas em conjunto para fornecer redundância e fiabilidade de aplicações. A utilização de um conjunto de disponibilidade garante que durante um evento de manutenção planeado ou não planeado esteja disponível pelo menos uma máquina virtual.  
Ver [Gerir a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e Gerir a disponibilidade de [máquinas virtuais Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Modelo de implementação clássico Azure
Um dos dois modelos de [implantação usados](resource-manager-deployment-model.md) para implantar recursos no Azure (o novo modelo é Azure Resource Manager). Alguns serviços Azure suportam apenas o modelo de implementação do Gestor de Recursos, alguns suportam apenas o modelo clássico de implementação, e alguns suportes ambos. A documentação de cada serviço Azure especifica quais os modelos que suportam.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Interface de linha de comandos (CLI) do Azure
Uma interface de linha de comando que pode ser usada para gerir os serviços Azure a partir de Windows, macOS e Linux.  Alguns serviços ou funcionalidades de serviço só podem ser geridos através do PowerShell ou do CLI. Ver [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Uma interface de linha de comando para gerir os serviços Azure através de uma linha de comando a partir de Computadores Windows. Alguns serviços ou funcionalidades de serviço só podem ser geridos através do PowerShell ou do CLI.
Ver [Como instalar e configurar a Azure PowerShell](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Modelo de implementação do Gestor de Recursos Azure
Um dos dois modelos de [implementação usados](resource-manager-deployment-model.md) para implementar recursos no Microsoft Azure (o outro é o modelo clássico de implementação). Alguns serviços Azure suportam apenas o modelo de implementação do Gestor de Recursos, alguns suportam apenas o modelo clássico de implementação, e alguns suportes ambos. A documentação de cada serviço Azure especifica quais os modelos que suportam.

## <a name="fault-domain"></a>domínio de falhas
A recolha de máquinas virtuais num conjunto de disponibilidade que pode falhar ao mesmo tempo. Um exemplo é um grupo de máquinas num rack que partilham uma fonte de energia comum e um interruptor de rede. Em Azure, as máquinas virtuais num conjunto de disponibilidade são automaticamente separadas em vários domínios de avaria.  
Ver [Gerir a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou Gerir a disponibilidade de [máquinas virtuais Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>geo
Um limite definido para a residência de dados que normalmente contém duas ou mais regiões. As fronteiras podem estar dentro ou fora das fronteiras nacionais e são influenciadas pela regulamentação fiscal. Cada geo tem pelo menos uma região. Exemplos de geos são a Ásia-Pacífico e o Japão. Também chamada *geografia.*  
Ver [Regiões de Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geo-replicação
O processo de replicação automática de conteúdos, tais como bolhas, mesas e filas dentro de um par regional.  
Consulte [Geo-Replication Ativa para Base de Dados Azure SQL](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Um ficheiro que contém o sistema operativo e a configuração da aplicação que pode ser usado para criar qualquer número de máquinas virtuais. Em Azure existem dois tipos de imagens: imagem VM e imagem de SO. Uma imagem VM inclui um sistema operativo e todos os discos ligados a uma máquina virtual quando a imagem é criada. Uma imagem de SO contém apenas um sistema operativo generalizado sem configurações de disco de dados.  
Consulte [a Navigate e selecione imagens de máquinas virtuais do Windows em Azure com o PowerShell ou o CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limites
O número de recursos que podem ser criados ou o benchmark de desempenho que pode ser alcançado. Os limites estão tipicamente associados a subscrições, serviços e ofertas.  
Ver [limites de subscrição e serviços da Azure, quotas e constrangimentos](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>balançador de carga
Um recurso que distribui o tráfego de entrada entre computadores numa rede. Em Azure, um equilibrador de carga distribui o tráfego por máquinas virtuais definidas num conjunto de equilibradores de carga. Um [equilibrador](load-balancer/load-balancer-overview.md) de carga pode ser virado para a Internet, ou pode ser interno.  

## <a name="mobile-app"></a>aplicativo móvel
Outro nome para [App Service App](#app-service-app).

## <a name="offer"></a>oferta
Os preços, créditos e termos conexos aplicáveis a uma subscrição da Azure.  
Consulte a página de detalhes da [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
O portal web seguro utilizado para implementar e gerir os serviços Azure.

## <a name="region"></a>region
Uma área dentro de um geo que não atravessa as fronteiras nacionais e contém um ou mais centros de dados. Os preços, os serviços regionais e os tipos de oferta estão expostos a nível regional. Uma região é tipicamente emparelhada com outra região, que pode estar a várias centenas de milhas de distância. Os pares regionais podem ser usados como um mecanismo para a recuperação de desastres e cenários de alta disponibilidade. Também referido como *local.*  
Ver [Regiões de Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>recurso
Um item que faz parte da sua solução Azure. Cada serviço Azure permite-lhe implementar diferentes tipos de recursos, tais como bases de dados ou máquinas virtuais.   
Ver [visão geral do Gestor de Recursos Azure](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>grupo de recursos
Um contentor em Gestor de Recursos que detém recursos relacionados para uma aplicação. O grupo de recursos pode incluir todos os recursos para uma aplicação, ou apenas os recursos que estão logicamente agrupados. Pode decidir como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.  
Ver [visão geral do Gestor de Recursos Azure](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Modelo do Resource Manager
Um ficheiro JSON que define declarativamente um ou mais recursos Azure e que define dependências entre os recursos implantados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida.  
Ver [modelos de gestor de recursos Azure](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>fornecedor de recursos
Um serviço que fornece os recursos que pode implantar e gerir através do Gestor de Recursos. Cada fornecedor de recursos oferece operações para trabalhar com os recursos implementados. Os fornecedores de recursos podem ser acedidos através do portal Azure PowerShell e de vários SDKs de programação.  
Ver [visão geral do Gestor de Recursos Azure](azure-resource-manager/management/overview.md)

## <a name="role"></a>papel
Um meio de controlo do acesso que pode ser atribuído a utilizadores, grupos e serviços. As funções são capazes de executar ações como criar, gerir e ler sobre os recursos do Azure.  
Ver [RBAC: Funções incorporadas](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>contrato de nível de serviço (SLA)
O acordo que descreve os compromissos da Microsoft para o tempo e conectividade. Cada serviço Azure tem um SLA específico.  
Ver [Acordos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>assinatura de acesso partilhado (SAS)
Uma assinatura que lhe permite conceder acesso limitado a um recurso, sem expor a chave da sua conta. Por exemplo, [o Azure Storage utiliza SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para garantir o acesso do cliente a objetos como bolhas. [O IoT Hub utiliza SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) para conceder permissão aos dispositivos para enviar telemetria.

## <a name="storage-account"></a>conta de armazenamento
Uma conta que lhe dá acesso aos serviços Azure Blob, Queue, Table e File no Azure Storage. O nome da conta de armazenamento define o espaço de nome exclusivo para objetos de dados de armazenamento Azure.  
Ver [Contas de armazenamento Azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subscrição
Um acordo de cliente com a Microsoft que lhes permite obter serviços Azure. Os preços de subscrição e os termos conexos são regidos pela oferta escolhida para a subscrição.
Consulte [o Microsoft Online Subscription Agreement](https://azure.microsoft.com/support/legal/subscription-agreement/) e como as [subscrições do Azure estão associadas ao Azure Ative Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>etiqueta
Um termo de indexação que lhe permite categorizar recursos de acordo com os seus requisitos de gestão ou faturação. Quando se tem uma coleção complexa de recursos, pode usar tags para visualizar esses ativos da forma que faz mais sentido. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento.  
Consulte [a utilização de tags para organizar os seus recursos Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>atualização do domínio
A recolha de máquinas virtuais num conjunto de disponibilidade que são atualizados ao mesmo tempo. As máquinas virtuais no mesmo domínio de atualização são reiniciadas em conjunto durante a manutenção planeada. O Azure nunca reinicia mais do que um domínio de atualização de cada vez. Também referido como um domínio de upgrade.  
Ver [Gerir a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e Gerir a disponibilidade de [máquinas virtuais Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>máquina virtual
A implementação do software de um computador físico que executa um sistema operativo. Várias máquinas virtuais podem funcionar simultaneamente no mesmo hardware. Em Azure, as máquinas virtuais estão disponíveis em vários tamanhos.  
Ver [documentação de máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>extensão de máquina virtual
Um recurso que implementa comportamentos ou funcionalidades que ajudam outros programas a trabalhar ou fornecem a capacidade de interagir com um computador em execução. Por exemplo, pode utilizar a extensão VM Access para redefinir ou modificar valores de acesso remoto numa máquina virtual Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Ver [Sobre extensões e funcionalidades de máquinas virtuais (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [sobre extensões e funcionalidades de máquinas virtuais (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>rede virtual
Uma rede que proporciona conectividade entre os seus recursos Azure que está isolada de todos os outros inquilinos da Azure. [Um Gateway Azure VPN](vpn-gateway/vpn-gateway-about-vpngateways.md) permite estabelecer ligações entre redes virtuais e entre uma rede virtual e uma rede no local. Pode controlar totalmente os blocos de endereços IP, definições de DNS, políticas de segurança e tabelas de rotas dentro de uma rede virtual.  
Ver [Visão Geral da Rede Virtual](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplicação Web
Outro nome para [App Service App](#app-service-app).

## <a name="see-also"></a>Consulte também

* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Centro de recursos de nuvem](https://azure.microsoft.com/resources/)  
* [Azure para a sua aplicação de negócio](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure no seu datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

