---
title: Exemplos de modelos do Azure Resource Manager - Azure Container Instances
description: Exemplos de modelos do Azure Resource Manager do Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: bf9f2be8a0854a6968f3be6bfdaf3a59fc81dc76
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57728963"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelos do Azure Resource Manager do Azure Container Instances

Os modelos de exemplo seguintes implementam instâncias de contentor em diversas configurações.

Para opções de implementação, consulte a [implementação](#deployment) secção. Se quiser criar seus próprios modelos, o Azure Container Instances [referência de modelo do Resource Manager] [ ref] fornece detalhes sobre o formato de modelo e propriedades disponíveis.

## <a name="sample-templates"></a>Modelos de exemplo

| | |
|-|-|
| **Aplicações** ||
| [Wordpress][app-wp] | Cria um Web site WordPress e a respetiva base de dados MySQL numa instância de contentor. O conteúdo de site do WordPress e a base de dados MySQL persistem para um serviço ficheiros do Azure partilhar. |
| [Barra de navegação do MS SQL Server e do IIS][app-nav] | Implementa um único contentor do Windows com um Dynamics NAV com todas as funcionalidades autossuficiente / ambiente do Dynamics 365 Business Central. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implementa dois contentores de Linux que compartilham um volume de emptyDir. |
| [gitRepo][vol-gitrepo] | Implementa um contentor de Linux que clona um repositório do GitHub e monta-lo como um volume. |
| [secret][vol-secret] | Implementa o contentor do Linux com um certificado PFX montado como um volume secreto. |
| **Redes** ||
| [Contentor exposta por UDP][net-udp] | Implementa um contentor do Windows ou Linux que expõe uma porta UDP. |
| [Contentor do Linux com IP público][net-publicip] | Implementa um único contentor de Linux podem ser acedido através de um IP público. |
| [Implementar um grupo de contentor com uma rede virtual (pré-visualização)][net-vnet] | Implementa uma nova rede virtual, a sub-rede, o perfil de rede e o grupo de contentores. |
| **Recursos do Azure** ||
| [Criar conta de armazenamento do Azure e a partilha de ficheiros][az-files] | Utiliza a CLI do Azure numa instância de contentor para criar uma conta de armazenamento e uma partilha de ficheiros do Azure.

## <a name="deployment"></a>Implementação

Tem várias opções para implementar recursos com modelos do Resource Manager:

[CLI do Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portal do Azure][deploy-portal]

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