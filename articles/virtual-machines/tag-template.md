---
title: Como marcar um VM usando um modelo
description: Saiba como marcar uma máquina virtual usando um modelo.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897379"
---
# <a name="tagging-a-vm-using-a-template"></a>Marcação de um VM usando um modelo

Este artigo descreve como marcar um VM em Azure usando um modelo de Gestor de Recursos. As etiquetas são pares chave/valor definidos pelo utilizador que podem ser colocados diretamente num recurso ou num grupo de recursos. A Azure suporta atualmente até 50 tags por grupo de recursos e recursos. As etiquetas podem ser colocadas num recurso no momento da criação ou adicionadas a um recurso existente.

[Este modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) coloca etiquetas nos seguintes recursos: Compute (Máquina Virtual), Armazenamento (Conta de Armazenamento) e Rede (Endereço IP Público, Rede Virtual e Interface de Rede). Este modelo é para um Windows VM mas pode ser adaptado para VMs Linux.

Clique no botão **Implementar para Azure** a partir do [link do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Isto irá navegar para o [portal Azure](https://portal.azure.com/) onde pode implementar este modelo.

![Implantação simples com Tags](./media/tag/deploy-to-azure-tags.png)

Este modelo inclui as seguintes tags: *Departamento,* *Aplicação* e *Criado por*. Pode adicionar/editar estas tags diretamente no modelo se quiser nomes de identificação diferentes.

![Etiquetas de azul em um modelo](./media/tag/azure-tags-in-a-template.png)

Como pode ver, as etiquetas são definidas como pares chave/valor, separados por um cólon (:). As etiquetas devem ser definidas neste formato:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Guarde o ficheiro do modelo depois de terminar de editá-lo com as etiquetas à sua escolha.

Em seguida, na secção **De Parâmetros de Edição,** pode preencher os valores das suas etiquetas.

![Editar Tags no portal Azure](./media/tag/edit-tags-in-azure-portal.png)

Clique em **Criar** para implementar este modelo com os seus valores de marcação.

### <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a marcação dos seus recursos Azure, consulte [a Visão Geral do Gestor de Recursos da Azure](../azure-resource-manager/management/overview.md) e [a utilização de Tags para organizar os seus Recursos Azure.](../azure-resource-manager/management/tag-resources.md)
- Para ver como as etiquetas podem ajudá-lo a gerir o seu uso dos recursos Azure, consulte [compreender a sua Conta de Azure.](../cost-management-billing/understand/review-individual-bill.md)
