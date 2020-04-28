---
title: Adicione um VM usando uma imagem partilhada em Azure DevTest Labs [ Microsoft Docs
description: Saiba como adicionar uma máquina virtual (VM) usando uma imagem da galeria de imagens partilhadas anexada em Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68775594"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Adicione um VM usando uma imagem da galeria de imagens partilhadas anexada
A Azure DevTest Labs permite-lhe anexar uma galeria de imagens partilhada ao seu laboratório e depois usar imagens na galeria como bases para os VMs que cria no laboratório. Para aprender a anexar uma galeria de imagens partilhada ao seu laboratório, consulte a Galeria de [Imagens Partilhadas Configure](configure-shared-image-gallery.md). Este artigo mostra-lhe como adicionar um VM ao seu laboratório usando uma imagem da galeria de imagens partilhada anexa como base. 

## <a name="azure-portal"></a>Portal do Azure
Nesta secção, aprende-se a usar o portal Azure para adicionar um VM ao seu laboratório com base numa imagem da galeria de imagens partilhadas anexada. Esta secção não fornece instruções detalhadas passo a passo para criar um VM utilizando o portal Azure. Para obter esses detalhes, consulte [Criar um portal VM - Azure.](devtest-lab-add-vm.md) Apenas destaca os passos em que seleciona uma imagem da galeria de imagens partilhadas anexada e seleciona uma versão da imagem que pretende utilizar. 

Ao adicionar um VM ao seu laboratório, pode selecionar uma imagem da galeria de imagens partilhadas anexada como imagem base: 

![Selecione uma imagem partilhada para a base](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Em seguida, no separador de **definições Avançada** da página de recursos do **laboratório Create,** pode selecionar a versão da imagem que pretende utilizar como imagem base:

![Selecione versão de imagem](./media/add-vm-use-shared-image/select-version-shared-image.png)

Pode mudar para utilizar uma versão diferente da imagem após a criação do VM. 

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Se estiver a usar um modelo de Gestor de Recursos Azure para criar uma máquina virtual utilizando uma imagem partilhada de galeria de imagens, especifique um valor para o **ImageId partilhado** na secção **Propriedades.** Veja o seguinte exemplo: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Para obter um exemplo completo do modelo do Gestor de Recursos, consulte Criar uma máquina virtual utilizando uma amostra de imagem partilhada da galeria de [imagens](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) no nosso repositório GitHub. 

## <a name="rest-api"></a>API REST

1. Primeiro, tens de obter a identificação da imagem na galeria de imagens partilhadas. Uma maneira é listar todas as imagens na galeria de imagens partilhadas anexada utilizando o seguinte comando GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Invoque o método PUT nas máquinas virtuais, passando a identificação da imagem partilhada que recebeu da chamada anterior para a `properties.SharedImageId`.

## <a name="next-steps"></a>Passos seguintes
Para aprender a anexar uma galeria de imagens partilhada a um laboratório e configurá-la, consulte a Galeria de [Imagens Partilhadas Configure](configure-shared-image-gallery.md).