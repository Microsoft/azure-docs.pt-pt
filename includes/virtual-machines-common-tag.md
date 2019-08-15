---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "67184199"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Marcando uma máquina virtual por meio de modelos
Primeiro, vamos dar uma olhada na marcação por meio de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca marcas nos seguintes recursos: Computação (máquina virtual), armazenamento (conta de armazenamento) e rede (endereço IP público, rede virtual e interface de rede). Este modelo é para uma VM do Windows, mas pode ser adaptado para VMs do Linux.

Clique no botão **implantar no Azure** no [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Isso navegará até o [portal do Azure](https://portal.azure.com/) onde você pode implantar este modelo.

![Implantação simples com marcas](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Este modelo inclui as seguintes marcas: *Departamento*, *aplicativo*e *criado por*. Você pode adicionar/editar essas marcas diretamente no modelo se desejar nomes de marca diferentes.

![Marcas do Azure em um modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como você pode ver, as marcas são definidas como pares de chave/valor, separados por dois-pontos (:). As marcas devem ser definidas neste formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Salve o arquivo de modelo depois de terminar de editá-lo com as marcas de sua escolha.

Em seguida, na seção **Editar parâmetros** , você pode preencher os valores de suas marcas.

![Editar marcas no portal do Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique em **criar** para implantar esse modelo com os valores de marca.

## <a name="tagging-through-the-portal"></a>Marcação por meio do portal
Depois de criar seus recursos com marcas, você pode exibir, adicionar e excluir marcas no Portal.

Selecione o ícone de marcas para exibir suas marcas:

![Ícone de marcas no portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicione uma nova marca por meio do portal definindo seu próprio par de chave/valor e salve-o.

![Adicionar nova marca no portal do Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Agora, sua nova marca deve aparecer na lista de marcas do recurso.

![Nova marca salva em portal do Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

