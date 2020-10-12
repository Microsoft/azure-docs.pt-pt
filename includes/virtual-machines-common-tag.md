---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 196dfdc045fd60e4a253857087177f478f50ea24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050243"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Marcando uma máquina virtual através de modelos
Primeiro, vamos olhar para a marcação através de modelos. [Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca etiquetas nos seguintes recursos: Compute (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede). Este modelo é para um Windows VM mas pode ser adaptado para VMs Linux.

Clique no botão **Implementar para Azure** a partir do [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Isto irá navegar para o [portal Azure](https://portal.azure.com/) onde pode implementar este modelo.

![Implantação simples com Tags](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Este modelo inclui as seguintes tags: *Departamento,* *Aplicação*e *Criado por*. Pode adicionar/editar estas tags diretamente no modelo se quiser nomes de identificação diferentes.

![Etiquetas de azul em um modelo](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Como pode ver, as etiquetas são definidas como pares chave/valor, separados por um cólon (:). As etiquetas devem ser definidas neste formato:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Guarde o ficheiro do modelo depois de terminar de editá-lo com as etiquetas à sua escolha.

Em seguida, na secção **De Parâmetros de Edição,** pode preencher os valores das suas etiquetas.

![Editar Tags no portal Azure](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Clique em **Criar** para implementar este modelo com os seus valores de marcação.

## <a name="tagging-through-the-portal"></a>Marcação através do Portal
Depois de criar os seus recursos com tags, pode ver, adicionar e eliminar tags no portal.

Selecione o ícone de tags para ver as suas etiquetas:

![Ícone de tags no portal Azure](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Adicione uma nova etiqueta através do portal definindo o seu próprio par Chave/Valor e guarde-a.

![Adicione nova Tag no portal Azure](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

A sua nova etiqueta deve agora aparecer na lista de tags para o seu recurso.

![Nova Tag salva no portal Azure](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

