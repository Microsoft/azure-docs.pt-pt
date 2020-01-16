---
title: Exemplos de modelo de Azure Resource Manager
description: Localizar exemplos de modelo de Azure Resource Manager para implantar instâncias de contêiner do Azure em diferentes configurações
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981652"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelos de Azure Resource Manager para instâncias de contêiner do Azure

Os modelos de exemplo a seguir implantam instâncias de contêiner em várias configurações.

Para obter opções de implantação, consulte a seção [implantação](#deployment) . Se você quiser criar seus próprios modelos, o modelo de referência do modelo do Azure instâncias de contêiner do [Resource Manager][ref] detalhes e as propriedades disponíveis.

## <a name="sample-templates"></a>Modelos de exemplo

| | |
|-|-|
| **Aplicações** ||
| [WordPress][app-wp] | Cria um site do WordPress e seu banco de dados MySQL em um grupo de contêineres. O conteúdo do site do WordPress e o banco de dados MySQL são persistidos em um compartilhamento de arquivos do Azure. Também cria um gateway de aplicativo para expor o acesso à rede pública ao WordPress. |
| [MS NAV com SQL Server e IIS][app-nav] | Implanta um único contêiner do Windows com um ambiente autocontido do Dynamics NAV/Dynamics 365 Business central totalmente incluído. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implanta dois contêineres do Linux que compartilham um volume emptyDir. |
| [gitRepo][vol-gitrepo] | Implanta um contêiner do Linux que clona um repositório do GitHub e o monta como um volume. |
| [secret][vol-secret] | Implanta um contêiner do Linux com um certificado PFX montado como um volume secreto. |
| **Redes** ||
| [Contêiner de UDP exposto][net-udp] | Implanta um contêiner do Windows ou Linux que expõe uma porta UDP. |
| [Contêiner do Linux com IP público][net-publicip] | Implanta um único contêiner do Linux acessível por meio de um IP público. |
| [Implantar um grupo de contêineres com uma rede virtual (versão prévia)][net-vnet] | Implanta uma nova rede virtual, sub-rede, perfil de rede e grupo de contêineres. |
| **Recursos do Azure** ||
| [Criar conta de armazenamento do Azure e compartilhamento de arquivos][az-files] | Usa o CLI do Azure em uma instância de contêiner para criar uma conta de armazenamento e um compartilhamento de arquivos do Azure.

## <a name="deployment"></a>Implementação

Você tem várias opções para implantar recursos com modelos do Resource Manager:

[CLI do Azure][deploy-cli]

[O Azure PowerShell][deploy-powershell]

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
