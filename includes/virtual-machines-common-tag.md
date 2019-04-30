---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108309"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Identificação de uma Máquina Virtual através de modelos
Em primeiro lugar, vamos examinar a marcação por meio de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca as etiquetas nos recursos seguintes: (Máquina Virtual) de computação, armazenamento (conta de armazenamento) e rede (endereço IP público, rede Virtual e Interface de rede). Este modelo é para uma VM do Windows, mas pode ser adaptado para VMs do Linux.

Clique nas **implementar no Azure** botão a [ligação do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Isto irá navegar para o [portal do Azure](https://portal.azure.com/) onde pode implementar este modelo.

![Implementação simples com etiquetas](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Esse modelo inclui as seguintes tags: *Departamento*, *aplicativo*, e *criados por*. Pode adicionar/editar estas etiquetas diretamente no modelo se gostaria de ter nomes de etiquetas diferentes.

![Etiquetas do Azure num modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como pode ver, as etiquetas são definidas como pares chave/valor, separados por vírgula (:). As etiquetas tem de ser definidas no seguinte formato:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Guarde o ficheiro de modelo depois de concluir a editá-lo com as etiquetas da sua preferência.

Em seguida, no **Editar parâmetros** secção, pode preencher os valores para as suas etiquetas.

![As etiquetas de edição no portal do Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique em **criar** para implementar este modelo com os seus valores de etiqueta.

## <a name="tagging-through-the-portal"></a>Etiquetagem através do Portal
Depois de criar os seus recursos com etiquetas, pode ver, adicionar e eliminar etiquetas no portal.

Selecione o ícone de etiquetas para ver as suas etiquetas:

![Ícone de etiquetas no portal do Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicionar uma nova etiqueta através do portal, definindo o seu próprio par de chave/valor e guardá-lo.

![Adicionar etiqueta nova no portal do Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

A nova etiqueta deverá agora aparecer na lista de etiquetas para o seu recurso.

![Nova etiqueta que guardou no portal do Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

