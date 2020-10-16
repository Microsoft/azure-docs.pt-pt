---
title: Amostras de modelo do gestor de recursos Azure
description: Encontre amostras de modelo do Gestor de Recursos Azure para implementar instâncias de contentores Azure em diferentes configurações
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: 6c487087b39244178643fd81364150ceb3ac4a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169651"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelos de gestor de recursos Azure para instâncias de contentores Azure

Os seguintes modelos de amostra implantam instâncias de contentores em várias configurações.

Para obter opções de implantação, consulte a secção [De Implementação.](#deployment) Se você quiser criar os seus próprios modelos, o modelo de design de design de design de dados [do modelo do][ref] Azure Container Instances e as propriedades disponíveis.

## <a name="sample-templates"></a>Modelos de exemplo

| Modelo | Descrição |
|-|-|
| **Aplicações** ||
| [WordPress][app-wp] | Cria um website WordPress e a sua base de dados MySQL num grupo de contentores. O conteúdo do site WordPress e a base de dados MySQL são persistidos a uma partilha de Ficheiros Azure. Também cria uma porta de aplicação para expor o acesso da rede pública ao WordPress. |
| [MS NAV com SQL Server e IIS][app-nav] | Implementa um único recipiente Windows com um ambiente de Centro de Negócios Dynamics NAV / Dynamics 365 totalmente apresentado. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implanta dois recipientes Linux que partilham um volume deDir vazio. |
| [gitRepo][vol-gitrepo] | Implanta um recipiente Linux que clona um repo GitHub e o monta como um volume. |
| [segredo][vol-secret] | Implanta um recipiente Linux com um cert PFX montado como um volume secreto. |
| **Redes** ||
| [Recipiente exposto pela UDP][net-udp] | Implanta um recipiente Windows ou Linux que expõe uma porta UDP. |
| [Recipiente Linux com IP público][net-publicip] | Implanta um único recipiente Linux acessível através de um IP público. |
| [Implementar um grupo de contentores com uma rede virtual][net-vnet] | Implementa uma nova rede virtual, sub-rede, perfil de rede e grupo de contentores. |
| **Recursos do Azure** ||
| [Criar conta de armazenamento Azure e partilha de ficheiros][az-files] | Utiliza o CLI Azure num caso de contentor para criar uma conta de armazenamento e uma partilha de Ficheiros Azure.

## <a name="deployment"></a>Implementação

Tem várias opções para a implementação de recursos com modelos de Gestor de Recursos:

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
