---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182265"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Marcação de uma máquina virtual através de modelos
Primeiro, vamos ver a marcação através de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca etiquetas nos seguintes recursos: Computação (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede). Este modelo é para um VM do Windows, mas pode ser adaptado para VMs Linux.

Clique no botão **'Enviar para Azure'** a partir do link do [modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Isto irá navegar para o [portal Azure](https://portal.azure.com/) onde você pode implementar este modelo.

![Implantação simples com tags](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Este modelo inclui as seguintes tags: *Departamento,* *Aplicação,* e *Criado por*. Pode adicionar/editar estas tags diretamente no modelo, se quiser diferentes nomes de etiquetas.

![Tags azure em um modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como pode ver, as etiquetas são definidas como pares chave/valor, separados por um cólon (:). As etiquetas devem ser definidas neste formato:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Guarde o ficheiro do modelo depois de terminar de o editar com as etiquetas à sua escolha.

Em seguida, na secção Parâmetros de **Edição,** pode preencher os valores das suas etiquetas.

![Editar tags no portal Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique **em Criar** para implementar este modelo com os valores da etiqueta.

## <a name="tagging-through-the-portal"></a>Marcação através do Portal
Depois de criar os seus recursos com tags, pode visualizar, adicionar e apagar etiquetas no portal.

Selecione o ícone de tags para visualizar as suas etiquetas:

![Ícone de tags no portal Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicione uma nova etiqueta através do portal definindo o seu próprio par chave/valor e guarde-o.

![Adicione nova Tag no portal Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

A sua nova etiqueta deve agora figurar na lista de etiquetas para o seu recurso.

![Nova Tag guardada no portal Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

