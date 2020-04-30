---
title: Glossário azul - Dicionário Azure Microsoft Docs
description: Use o glossário Azure para entender a terminologia da nuvem na plataforma Azure. Este pequeno dicionário Azure fornece definições para termos comuns de nuvem para Azure.
keywords: Dicionário azul, terminologia em nuvem, glossário Azure, definições de terminologia, termos de nuvem
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276481"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário Microsoft Azure: Um dicionário de terminologia em nuvem na plataforma Azure

O glossário do Microsoft Azure é um pequeno dicionário de terminologia em nuvem para a plataforma Azure. Veja também:

* [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) - Definições de serviços Azure e seus homólogos DaWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termos de computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/) - termos gerais da nuvem da indústria.

## <a name="account"></a>conta
Uma conta que é usada para aceder e gerir uma subscrição azure. É frequentemente referido como uma conta Azure embora uma conta possa ser qualquer uma delas: uma conta de trabalho, escola ou microsoft pessoal existente, ou um nome de utilizador do Office 365 e senha. Também pode criar uma conta para gerir uma subscrição Azure quando se inscrever para o [teste gratuito](https://azure.microsoft.com).  
Consulte [o Registo de uma subscrição Azure com a sua conta Office 365](cost-management-billing/manage/office-365-account-for-azure-subscription.md) e Contas que pode utilizar para iniciar [sessão](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>App API
Outro nome para [app Service](#app-service-app)app .

## <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Os recursos computacionais que o [Azure App Service](app-service/overview.md) fornece para hospedar um website ou aplicação web, API web ou backend de [aplicações móveis.](app-service-mobile/app-service-mobile-value-prop.md) As aplicações do App Service também são referidas como *App Services,* *aplicações web,* *aplicações API*e *aplicações móveis.*

## <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais que são geridas em conjunto para fornecer redundância e fiabilidade da aplicação. A utilização de um conjunto de disponibilidade garante que durante um evento de manutenção planeado ou não planeado, pelo menos uma máquina virtual está disponível.  
Ver [Gerir a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e gerir a disponibilidade de [máquinas virtuais Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Modelo de implantação clássico do Azure
Um dos dois modelos de [implantação utilizados](resource-manager-deployment-model.md) para a implantação de recursos no Azure (o novo modelo é o Azure Resource Manager). Alguns serviços Azure suportam apenas o modelo de implementação do Gestor de Recursos, alguns suportam apenas o modelo clássico de implementação, e alguns apoiam ambos. A documentação para cada serviço Azure especifica qual o modelo ou s que suportam.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Interface de linha de comando Azure (CLI)
Uma interface de linha de comando que pode ser usada para gerir os serviços Azure a partir do Windows, macOS e Linux.  Alguns serviços ou funcionalidades de serviço só podem ser geridos através do PowerShell ou do CLI. Ver [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Uma interface de linha de comando para gerir os serviços Azure através de uma linha de comando a partir de Computadores Windows. Alguns serviços ou funcionalidades de serviço só podem ser geridos através do PowerShell ou do CLI.
Ver [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Modelo de implementação do Gestor de Recursos Azure
Um dos dois modelos de [implementação utilizados](resource-manager-deployment-model.md) para implementar recursos no Microsoft Azure (o outro é o modelo clássico de implementação). Alguns serviços Azure suportam apenas o modelo de implementação do Gestor de Recursos, alguns suportam apenas o modelo clássico de implementação, e alguns apoiam ambos. A documentação para cada serviço Azure especifica qual o modelo ou s que suportam.

## <a name="fault-domain"></a>domínio de falha
A recolha de máquinas virtuais num conjunto de disponibilidade que pode eventualmente falhar ao mesmo tempo. Um exemplo é um grupo de máquinas numa prateleira que partilham uma fonte de energia comum e um interruptor de rede. Em Azure, as máquinas virtuais num conjunto de disponibilidade são automaticamente separadas em vários domínios de avaria.  
Ver [Gerir a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou gerir a disponibilidade de [máquinas virtuais Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>geo
Um limite definido para a residência de dados que normalmente contém duas ou mais regiões. As fronteiras podem estar dentro ou fora das fronteiras nacionais e são influenciadas pela regulamentação fiscal. Cada geo tem pelo menos uma região. Exemplos de geos são a Ásia-Pacífico e o Japão. Também chamado *de geografia.*  
Ver [Regiões de Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geo-replicação
O processo de replicação automática de conteúdos como bolhas, mesas e filas dentro de um par regional.  
Ver [Geo-Replicação Ativa para Base de Dados SQL Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Um ficheiro que contém o sistema operativo e a configuração da aplicação que pode ser usado para criar qualquer número de máquinas virtuais. No Azure existem dois tipos de imagens: imagem VM e imagem DE Os. Uma imagem VM inclui um sistema operativo e todos os discos ligados a uma máquina virtual quando a imagem é criada. Uma imagem de OS contém apenas um sistema operativo generalizado sem configurações de disco de dados.  
Ver [Navegar e selecionar imagens de máquinas virtuais do Windows em Azure com powerShell ou o CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>limites
O número de recursos que podem ser criados ou o referencial de desempenho que pode ser alcançado. Os limites estão tipicamente associados a subscrições, serviços e ofertas.  
Ver [limites de subscrição e serviço do Azure, quotas e constrangimentos](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>equilibrador de carga
Um recurso que distribui tráfego entre computadores numa rede. Em Azure, um equilibrador de carga distribui tráfego para máquinas virtuais definidas num conjunto de equilíbrio de carga. Um [equilibrador](load-balancer/load-balancer-overview.md) de carga pode ser virado para a Internet, ou pode ser interno.  

## <a name="mobile-app"></a>aplicativo móvel
Outro nome para [App Service App](#app-service-app)App .

## <a name="offer"></a>oferta
Os preços, créditos e termos conexos aplicáveis a uma subscrição do Azure.  
Consulte a página de detalhes da [oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
O portal web seguro usado para implantar e gerir serviços Azure.

## <a name="region"></a>.
Uma área dentro de um geo que não atravessa as fronteiras nacionais e contém um ou mais datacenters. Os preços, os serviços regionais e os tipos de oferta estão expostos ao nível da região. Uma região é tipicamente emparelhada com outra região, que pode estar a várias centenas de milhas de distância. Os pares regionais podem ser usados como um mecanismo para a recuperação de desastres e cenários de alta disponibilidade. Também referido como *localização.*  
Ver [Regiões de Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>recurso
Um item que faz parte da sua solução Azure. Cada serviço Azure permite-lhe implementar diferentes tipos de recursos, tais como bases de dados ou máquinas virtuais.   
Ver [visão geral do Gestor de Recursos azure](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>grupo de recursos
Um contentor em Gestor de Recursos que detém recursos relacionados para uma aplicação. O grupo de recursos pode incluir todos os recursos para uma aplicação, ou apenas os recursos que estão logicamente agrupados. Pode decidir como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.  
Ver [visão geral do Gestor de Recursos azure](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Modelo do Resource Manager
Um ficheiro JSON que define declarativamente um ou mais recursos Azure e que define dependências entre os recursos implantados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida.  
Ver [modelos de Gestor de Recursos Azure de Autoria](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>fornecedor de recursos
Um serviço que fornece os recursos que pode implementar e gerir através do Gestor de Recursos. Cada fornecedor de recursos oferece operações para trabalhar com os recursos implementados. Os fornecedores de recursos podem ser acedidos através do portal Azure, Azure PowerShell, e de vários SDKs de programação.  
Ver [visão geral do Gestor de Recursos azure](azure-resource-manager/management/overview.md)

## <a name="role"></a>papel
Um meio de controlo do acesso que pode ser atribuído a utilizadores, grupos e serviços. As funções são capazes de executar ações como criar, gerir e ler sobre os recursos do Azure.  
Ver [RBAC: Papéis incorporados](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>acordo de nível de serviço (SLA)
O acordo que descreve os compromissos da Microsoft para o uptime e conectividade. Cada serviço Azure tem um SLA específico.  
Ver [Acordos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>assinatura de acesso partilhado (SAS)
Uma assinatura que lhe permite conceder acesso limitado a um recurso, sem expor a chave da sua conta. Por exemplo, o [Azure Storage utiliza o SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para conceder ao cliente acesso a objetos como bolhas. [O IoT Hub usa a SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) para conceder aos dispositivos permissão para enviar telemetria.

## <a name="storage-account"></a>conta de armazenamento
Uma conta que lhe dá acesso aos serviços Azure Blob, Queue, Table e File no Armazenamento Azure. O nome da conta de armazenamento define o espaço de nome único para objetos de dados de Armazenamento Azure.  
Ver sobre contas de [armazenamento azure](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subscrição
Um acordo de cliente com a Microsoft que lhes permite obter serviços Azure. Os preços de subscrição e os termos conexos regem-se pela oferta escolhida para a subscrição.
Consulte o [Microsoft Online Subscription Agreement](https://azure.microsoft.com/support/legal/subscription-agreement/) e como as subscrições do [Azure estão associadas ao Diretório Ativo do Azure](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>etiqueta
Um termo indexante que lhe permite categorizar recursos de acordo com os seus requisitos de gestão ou faturação. Quando se tem uma coleção complexa de recursos, pode utilizar tags para visualizar esses ativos da forma que faz mais sentido. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento.  
Ver [Usar etiquetas para organizar os seus recursos Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>domínio de atualização
A recolha de máquinas virtuais num conjunto de disponibilidade que são atualizados ao mesmo tempo. As máquinas virtuais no mesmo domínio de atualização são reiniciadas em conjunto durante a manutenção planeada. O Azure nunca reinicia mais do que um domínio de atualização de cada vez. Também referido como um domínio de upgrade.  
Ver [Gerir a disponibilidade de máquinas virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e gerir a disponibilidade de [máquinas virtuais Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>máquina virtual
A implementação do software de um computador físico que gere um sistema operativo. Várias máquinas virtuais podem funcionar simultaneamente no mesmo hardware. Em Azure, as máquinas virtuais estão disponíveis em vários tamanhos.  
Ver [documentação de Máquinas Virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>extensão de máquina virtual
Um recurso que implementa comportamentos ou funcionalidades que ajudam outros programas a funcionar ou fornecem a capacidade de interagir com um computador em execução. Por exemplo, pode utilizar a extensão De acesso VM para repor ou modificar valores de acesso remoto numa máquina virtual Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Ver [sobre extensões e funcionalidades de máquinavirtual (Windows)](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [sobre extensões e funcionalidades de máquinavirtual (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>rede virtual
Uma rede que proporciona conectividade entre os seus recursos Azure que está isolada de todos os outros inquilinos do Azure. Um [Azure VPN Gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) permite-lhe estabelecer ligações entre redes virtuais e entre uma rede virtual e uma rede no local. Pode controlar totalmente os blocos de endereços IP, as definições de DNS, as políticas de segurança e as tabelas de rotas dentro de uma rede virtual.  
Ver [visão geral da rede virtual](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplicação Web
Outro nome para [App Service App](#app-service-app)App .

## <a name="see-also"></a>Consulte também

* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Centro de recursos de nuvem](https://azure.microsoft.com/resources/)  
* [Azure para a sua aplicação de negócios](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure no seu datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

