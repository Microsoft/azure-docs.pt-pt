---
title: Amostras de modelo de gestor de recursos azure
description: Encontre amostras de modelo do Gestor de Recursos Azure para implementar instâncias de contentores Azure em diferentes configurações
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75981652"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelos de Gestor de Recursos Azure para instâncias de contentores azure

Os seguintes modelos de amostra implantam instâncias de contentores em várias configurações.

Para opções de implementação, consulte a secção [de implantação.](#deployment) Se quiser criar os seus próprios modelos, o modelo de referência do Gestor de [Recursos][ref] de Casos de Contentores Azure e as propriedades disponíveis.

## <a name="sample-templates"></a>Modelos de exemplo

| | |
|-|-|
| **Aplicações** ||
| [WordPress][app-wp] | Cria um website WordPress e a sua base de dados MySQL num grupo de contentores. O conteúdo do site WordPress e a base de dados MySQL persistem numa partilha de Ficheiros Azure. Também cria uma porta de aplicação para expor o acesso da rede pública ao WordPress. |
| [MS NAV com SQL Server e IIS][app-nav] | Implementa um único recipiente Windows com um ambiente Central de Negócios NaV / Dynamics 365. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implanta dois contentores Linux que partilham um volume dir vazio. |
| [gitRepo][vol-gitrepo] | Implanta um recipiente Linux que clona um repo GitHub e o monta como um volume. |
| [segredo][vol-secret] | Implanta um recipiente Linux com um certificado PFX montado como um volume secreto. |
| **Redes** ||
| [Recipiente exposto à UDP][net-udp] | Implementa um recipiente Windows ou Linux que expõe uma porta UDP. |
| [Recipiente linux com IP público][net-publicip] | Implanta um único recipiente Linux acessível através de um IP público. |
| [Implementar um grupo de contentores com uma rede virtual (pré-visualização)][net-vnet] | Implementa uma nova rede virtual, subnet, perfil de rede e grupo de contentores. |
| **Recursos do Azure** ||
| [Criar conta de armazenamento Azure e partilha de ficheiros][az-files] | Utiliza o Azure CLI numa instância de contentores para criar uma conta de armazenamento e uma partilha de Ficheiros Azure.

## <a name="deployment"></a>Implementação

Tem várias opções para implementar recursos com modelos de Gestor de Recursos:

[CLI do Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portal do Azure][deploy-portal]

[API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
