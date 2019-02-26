---
title: Glossário do Azure - dicionário do Azure | Documentos da Microsoft
description: Utilize o glossário do Azure para compreender a terminologia de cloud na plataforma do Azure. Este breve dicionário do Azure fornece definições de termos comuns da cloud para o Azure.
keywords: Dicionário do Azure, a terminologia de cloud, Glossário do Azure, definições terminológicas, termos de cloud
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
ms.openlocfilehash: 9a93786759941def4cf8677509b1b2565cac5090
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823863"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossário do Microsoft Azure: Um dicionário de terminologia de cloud na plataforma do Azure

O glossário do Microsoft Azure é um breve dicionário de terminologia de cloud para a plataforma Azure. Veja também:

* [Microsoft Azure e Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definições de serviços do Azure e de suas contrapartes AWS.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* [Termos relacionados com computação em nuvem](https://azure.microsoft.com/overview/cloud-computing-dictionary/) -termos de cloud do setor geral.

## <a name="account"></a>conta
Uma conta que é utilizada para aceder e gerir uma subscrição do Azure. Ele é frequentemente referido como uma conta do Azure apesar de uma conta pode ser qualquer um destes: uma existente, instituição de ensino, ou pessoais conta Microsoft, ou um nome de utilizador do Office 365 e palavra-passe. Também pode criar uma conta para gerir uma subscrição do Azure quando se inscreve no [avaliação gratuita](https://azure.microsoft.com).  
Ver [Inscreva-se uma subscrição do Azure com a sua conta do Office 365](billing/billing-use-existing-office-365-account-azure-subscription.md) e [contas que pode utilizar para iniciar sessão](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="api-app"></a>Aplicação de API
Outro nome para o [aplicação de serviço de aplicações](#app-service-app).

## <a name="app-service-app"></a>Aplicação do Serviço de Aplicações
Os recursos de computação que [App Service do Azure](app-service/overview.md) fornece para alojar uma site ou aplicação web, web API, ou [back-end de aplicação móvel](app-service-mobile/app-service-mobile-value-prop.md). Aplicações de serviço de aplicações são também denominadas *dos serviços de aplicações*, *aplicações web*, *aplicações API*, e *aplicações móveis*.

## <a name="availability-set"></a>conjunto de disponibilidade
Uma coleção de máquinas virtuais que são geridos em conjunto para fornecer redundância de aplicativos e a confiabilidade. A utilização de um conjunto de disponibilidade garante que, durante um evento de manutenção planeada ou, pelo menos uma máquina virtual está disponível.  
Ver [gerir a disponibilidade das máquinas de virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerir a disponibilidade das máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="classic-model"></a>Modelo de implementação clássica do Azure
Uma de duas [modelos de implementação](resource-manager-deployment-model.md) utilizados para implementar recursos no Azure (o novo modelo é o Azure Resource Manager). Alguns serviços do Azure suportam apenas o modelo de implementação do Resource Manager, alguns suportam apenas o modelo de implementação clássica e alguns de suportar. A documentação para cada serviço do Azure Especifica qual modelo (s) que suportam.

## <a name="cli"></a>Interface de linha de comandos (CLI) do Azure
Uma interface de linha de comandos que pode ser utilizada para gerir serviços do Azure a partir do Windows, macOS e Linux.  Alguns serviços ou funcionalidades de serviço podem ser geridas apenas através do PowerShell ou a CLI. Consulte [da CLI do Azure](/cli/azure)

## <a name="powershell"></a>Azure PowerShell
Uma interface de linha de comandos para gerir serviços do Azure através de uma linha de comandos a partir de Windows PCs. Alguns serviços ou funcionalidades de serviço podem ser geridas apenas através do PowerShell ou a CLI.
Consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

## <a name="arm-model"></a>Modelo de implementação do Azure Resource Manager
Uma de duas [modelos de implementação](resource-manager-deployment-model.md) utilizados para implementar recursos no Microsoft Azure (a outra é o modelo de implementação clássica). Alguns serviços do Azure suportam apenas o modelo de implementação do Resource Manager, alguns suportam apenas o modelo de implementação clássica e alguns de suportar. A documentação para cada serviço do Azure Especifica qual modelo (s) que suportam.

## <a name="fault-domain"></a>Domínio de falha
A coleção de máquinas virtuais num conjunto de disponibilidade que possivelmente pode falhar ao mesmo tempo. Um exemplo é um grupo de máquinas num bastidor que partilham um comutador de rede e fonte de energia comum. No Azure, as máquinas virtuais num conjunto de disponibilidade automaticamente estão separadas através de vários domínios de falha.  
Ver [gerir a disponibilidade das máquinas de virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [gerir a disponibilidade das máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>geo
Um limite definido para residência de dados que normalmente contém duas ou mais regiões. Os limites podem estar dentro ou além das fronteiras nacionais e são influenciados por regulamento de imposto. Cada geo tem pelo menos uma região. Exemplos de áreas geográficas são Ásia-Pacífico e Japão. Também denominado *geografia*.  
Consulte [regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>Replicação geográfica
O processo de replicar automaticamente o conteúdo, como blobs, tabelas e filas dentro de um par regional.  
Consulte [Georreplicação ativa para a base de dados SQL do Azure](sql-database/sql-database-geo-replication-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
Um ficheiro que contém o sistema operativo e a configuração da aplicação que pode ser utilizada para criar qualquer número de máquinas virtuais. No Azure, existem dois tipos de imagens: Imagem de VM e imagem do SO. Uma imagem de VM inclui um sistema operativo e todos os discos ligados a uma máquina virtual quando a imagem é criada. Uma imagem de SO contém apenas um sistema de operativo generalizado com nenhuma configuração de disco de dados.  
Consulte [navegar e selecionadas imagens de máquinas virtuais do Windows no Azure com o PowerShell ou a CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>Limites
O número de recursos que podem ser criados ou o parâmetro de comparação de desempenho que pode ser alcançado. Limites são normalmente associadas a subscrições, serviços e ofertas.  
Consulte [subscrição do Azure e limites do serviço, quotas e restrições](azure-subscription-service-limits.md)

## <a name="load-balancer"></a>balanceador de carga
Um recurso que distribui o tráfego de entrada entre computadores numa rede. No Azure, um balanceador de carga distribui o tráfego para as máquinas virtuais definidas num conjunto de Balanceador de carga. R [Balanceador de carga](load-balancer/load-balancer-overview.md) pode ser o acesso à internet ou pode ser interno.  

## <a name="mobile-app"></a>aplicação móvel
Outro nome para o [aplicação serviço de aplicações](#app-service-app).

## <a name="offer"></a>oferta
Os preços, créditos e termos relacionados a aplicáveis a uma subscrição do Azure.  
Consulte o [página de detalhes da oferta do Azure](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
O portal web seguro utilizado para implementar e gerir serviços do Azure.

## <a name="region"></a>Região
Uma área dentro de uma geo que não fronteiras nacionais não cruzado e contém um ou mais datacenters. Preços, serviços regionais e tipos de oferta são expostos ao nível da região. Uma região normalmente é emparelhada com outra região, o que pode ser até centenas de quilômetros distância. Pares regionais podem ser utilizados como um mecanismo de recuperação após desastre e cenários de elevada disponibilidade. Também conhecido como *localização*.  
Consulte [regiões do Azure](best-practices-availability-paired-regions.md)

## <a name="resource"></a>Recurso
Um item que faz parte de sua solução do Azure. Cada serviço do Azure permite-lhe implementar os diferentes tipos de recursos, como bases de dados ou máquinas virtuais.   
Consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="resource-group"></a>grupo de recursos
Um contentor no Gestor de recursos que retém recursos relacionados para uma aplicação. O grupo de recursos pode incluir todos os recursos para uma aplicação ou apenas os recursos que estão logicamente agrupados em conjunto. Pode decidir como pretende atribuir recursos a grupos de recursos com base no que é mais adequado para a sua organização.  
Consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="arm-template"></a>Modelo do Resource Manager
Um ficheiro JSON que define declarativamente um ou mais recursos do Azure e que define dependências entre os recursos implementados. O modelo pode ser utilizado para implementar os recursos de forma consistente e repetida.  
Consulte [modelos Authoring Azure Resource Manager](resource-group-authoring-templates.md)

## <a name="resource-provider"></a>Fornecedor de recursos
Um serviço que fornece os recursos que pode implementar e gerir através do Resource Manager. Cada fornecedor de recursos oferece operações para trabalhar com os recursos implementados. Fornecedores de recursos podem ser acedidos através do portal do Azure, Azure PowerShell e vários SDKs de programação.  
Consulte [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md)

## <a name="role"></a>função
Um meio para controlar o acesso que pode ser atribuído a utilizadores, grupos e serviços. As funções são capazes de realizar ações, como criar, gerir e ler em recursos do Azure.  
Consulte [RBAC: Funções incorporadas](role-based-access-control/built-in-roles.md)

## <a name="sla"></a>contrato de nível de serviço (SLA)
O contrato que descreve o compromisso quanto da Microsoft do tempo de atividade e conectividade. Cada serviço do Azure tem um SLA específico.  
Consulte [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/)

## <a name="sas"></a>assinatura de acesso partilhado (SAS)
Uma assinatura, que permite-lhe conceder acesso limitado a um recurso, sem expor a sua chave de conta. Por exemplo, [armazenamento do Azure utiliza SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para conceder acesso de cliente a objetos, como blobs. [O IoT Hub utiliza SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) para conceder permissão para enviar a telemetria de dispositivos.

## <a name="storage-account"></a>conta de armazenamento
Uma conta que dá-lhe aceder aos serviços de Blobs do Azure, fila, tabela e ficheiro no armazenamento do Azure. O nome da conta de armazenamento define o espaço de nomes exclusivo para objetos de dados do armazenamento do Azure.  
Consulte [sobre as contas de armazenamento](storage/common/storage-create-storage-account.md)

## <a name="subscription"></a>subscrição
Um contrato de cliente com a Microsoft permite-lhes obter serviços do Azure. O preço da subscrição e os termos relacionados são regidos pela oferta escolhida para a subscrição.
Ver [contrato de subscrição Online da Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/) e [subscrições do Azure como estão associadas com o Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>etiqueta
Um termo de indexação que lhe permite categorizar os recursos de acordo com os requisitos de gestão ou faturação. Quando tem uma coleção complexa de recursos, pode utilizar etiquetas de visualizar esses elementos da forma que faz mais sentido. Por exemplo, pode etiquetar recursos que desempenham uma função semelhante na sua organização ou pertencem ao mesmo departamento.  
Consulte [utilizar etiquetas para organizar os recursos do Azure](resource-group-using-tags.md)

## <a name="update-domain"></a>Domínio de atualização
A coleção de máquinas virtuais num conjunto de disponibilidade que são atualizadas ao mesmo tempo. Máquinas virtuais no mesmo domínio de atualização são reiniciadas em conjunto durante a manutenção planeada. O Azure nunca reinicia mais de um domínio de atualização de cada vez. Também conhecido como um domínio de atualização.  
Ver [gerir a disponibilidade das máquinas de virtuais do Windows](virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [gerir a disponibilidade das máquinas virtuais do Linux](virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vm"></a>Máquina virtual
A implementação de software de um computador físico que executa um sistema operativo. Várias máquinas virtuais pode executar em simultâneo no mesmo hardware. No Azure, máquinas virtuais estão disponíveis numa variedade de tamanhos.  
Consulte [documentação das máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="vm-extension"></a>extensão da máquina virtual
Um recurso que implementa a comportamentos ou recursos que o ajudam a outros programas oferecem a possibilidade de interagir com um computador em execução ou profissional. Por exemplo, poderia usar a extensão de acesso à VM, repor ou modificar os valores de acesso remoto numa máquina virtual do Azure.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Ver [sobre as funcionalidades (Windows) e extensões de máquinas virtuais](virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [sobre extensões da máquina virtual e os recursos (Linux)](virtual-machines/linux/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="vnet"></a>Rede virtual
Uma rede que fornece conectividade entre os recursos do Azure que se encontra isolada de outros inquilinos do Azure. Uma [Gateway de VPN do Azure](vpn-gateway/vpn-gateway-about-vpngateways.md) permite-lhe estabelecer ligações entre redes virtuais e entre uma rede virtual e uma rede no local. Pode controlar totalmente os blocos de endereços IP, definições de DNS, as políticas de segurança e tabelas de rotas dentro de uma rede virtual.  
Consulte [descrição geral da rede Virtual](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Aplicação Web
Outro nome para o [aplicação serviço de aplicações](#app-service-app).

## <a name="see-also"></a>Consulte também

* [Introdução ao Azure](https://azure.microsoft.com/get-started/)
* [Central de recursos de cloud](https://azure.microsoft.com/resources/)  
* [Azure para a sua aplicação de negócio](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure no seu datacenter](https://azure.microsoft.com/overview/business-apps-on-azure/)

