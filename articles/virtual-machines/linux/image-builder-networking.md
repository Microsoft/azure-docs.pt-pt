---
title: Opções de networking do Azure Image Builder Service
description: Compreenda as opções de networking ao implementar o Azure VM Image Builder Service
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669438"
---
# <a name="azure-image-builder-service-networking-options"></a>Opções de networking do Azure Image Builder Service

Tem de escolher implementar o Azure Image Builder com ou sem um VNET existente.

## <a name="deploy-without-specifying-an-existing-vnet"></a>Implementar sem especificar um VNET existente

Se não especificar um VNET existente, o Azure Image Builder cria um VNET e uma sub-rede no grupo de recursos de preparação. Um recurso IP público é usado com um grupo de segurança de rede para restringir o tráfego de entrada ao serviço Azure Image Builder. O IP público facilita o canal para comandos Azure Image Builder durante a construção de imagens. Uma vez concluída a construção, o VM, IP público, discos e VNET são eliminados. Para utilizar esta opção, não especifique quaisquer propriedades VNET.

## <a name="deploy-using-an-existing-vnet"></a>Implementar utilizando um VNET existente

Se especificar um VNET e uma sub-rede, o Azure Image Builder implementa o VM de construção para o seu VNET escolhido. Pode aceder a recursos acessíveis no seu VNET. Também pode criar um VNET siloed que não esteja ligado a qualquer outro VNET. Se especificar um VNET, o Azure Image Builder não utiliza um endereço IP público. A comunicação do Azure Image Builder Service para a construção VM utiliza a tecnologia Azure Private Link.

Para mais informações, consulte um dos seguintes exemplos:

* [Utilize o Azure Image Builder para VMs windows permitindo o acesso a um Azure VNET existente](../windows/image-builder-vnet.md)
* [Use O Azure Image Builder para VMs Linux permitindo o acesso a um Azure VNET existente](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>O que é a Ligação Privada do Azure?

O Azure Private Link fornece conectividade privada de uma rede virtual para a plataforma Azure como um serviço (PaaS), serviços de parceiros de propriedade do cliente ou Microsoft. Simplifica a arquitetura da rede e assegura a ligação entre pontos finais em Azure, eliminando a exposição de dados à internet pública. Para mais informações, consulte a [documentação private link.](../../private-link/index.yml)

### <a name="required-permissions-for-an-existing-vnet"></a>Permissões necessárias para um VNET existente

O Azure Image Builder requer permissões específicas para utilizar um VNET existente. Para obter mais informações, consulte [permissões do Serviço de Construtores de Imagem Configure Azure utilizando permissões do Serviço de Construtores de Imagem Azure da Azure ou](image-builder-permissions-cli.md) [Configure Image Builder Service utilizando o PowerShell](image-builder-permissions-powershell.md).

### <a name="what-is-deployed-during-an-image-build"></a>O que é implantado durante uma construção de imagem?

Utilizando um VNET existente significa que o Azure Image Builder implementa um VM adicional (proxy VM) e um Balançador de Carga Azure (ALB) que está ligado à Ligação Privada. O tráfego do serviço AIB atravessa a ligação privada com a ALB. O ALB comunica com o VM proxy utilizando a porta 60001 para Linux OS ou 60000 para o Windows OS. O proxy encaminha comandos para a construção VM usando a porta 22 para Linux OS ou 5986 para o Windows OS.

> [!NOTE]
> O VNET deve estar na mesma região que a região do Serviço de Construtores de Imagem Azure.
> 

### <a name="why-deploy-a-proxy-vm"></a>Porquê implantar um VM proxy?

Quando um VM sem IP público está por trás de um Balancer de Carga Interna, não tem acesso à Internet. O Balançador de Carga Azure utilizado para o VNET é interno. O proxy VM permite o acesso à Internet para a construção de VM durante as construções. Os grupos de segurança de rede associados podem ser usados para restringir o acesso vm de construção.

O tamanho VM de procuração implantado é Standard A1_v2 para além da construção VM. O proxy VM é utilizado para enviar comandos entre o Serviço de Construtor de Imagem Azure e o VM de construção. As propriedades proxy VM não podem ser alteradas, incluindo o tamanho ou o SO. Não é possível alterar as propriedades proxy VM para as construções de imagens Windows e Linux.

### <a name="image-template-parameters-to-support-vnet"></a>Parâmetros do modelo de imagem para suportar VNET
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| Definições | Descrição |
|---------|---------|
| name | (Opcional) Nome de uma rede virtual pré-existente. |
| subnetName | Nome da sub-rede dentro da rede virtual especificada. Deve ser especificado se e somente se o *nome* for especificado. |
| resourceGroupName | Nome do grupo de recursos que contém a rede virtual especificada. Deve ser especificado se e somente se o *nome* for especificado. |

O serviço private Link requer um IP a partir do VNET e da sub-rede. Atualmente, a Azure não apoia políticas de rede nestes IPs. Por conseguinte, as políticas de rede têm de ser desativadas na sub-rede. Para mais informações, consulte a [documentação private link.](../../private-link/index.yml)

### <a name="checklist-for-using-your-vnet"></a>Lista de verificação para utilização do seu VNET

1. Permitir que o Esquilibrador de Carga (ALB) comunique com o VM proxy num NSG
    * [Exemplo AZ CLI](image-builder-vnet.md#add-network-security-group-rule)
    * [Exemplo do PowerShell](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. Desativar a Política de Serviços Privados na sub-rede
    * [Exemplo AZ CLI](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [Exemplo do PowerShell](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Permitir que o Azure Image Builder crie um ALB e adicione VMs ao VNET
    * [Exemplo AZ CLI](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [Exemplo do PowerShell](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Permitir ao Azure Image Builder ler/escrever imagens de origem e criar imagens
    * [Exemplo AZ CLI](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [Exemplo do PowerShell](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Certifique-se de que está a utilizar o VNET na mesma região que a região do Serviço de Construtores de Imagem Azure.


## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [a visão geral do Azure Image Builder](../image-builder-overview.md).