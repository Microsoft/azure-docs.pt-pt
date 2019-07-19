---
title: Exemplos de modelo de Azure Resource Manager-instâncias de contêiner do Azure
description: Exemplos de modelo de Azure Resource Manager para instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 2089f024e1de2e92f6e401549c5876e26db17ebb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325700"
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

[Azure PowerShell][deploy-powershell]

[Azure portal][deploy-portal]

[REST API][deploy-rest]

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
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
