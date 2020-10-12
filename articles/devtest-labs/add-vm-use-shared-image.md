---
title: Adicione um VM usando uma imagem partilhada em Azure DevTest Labs Microsoft Docs
description: Saiba como adicionar uma máquina virtual (VM) utilizando uma imagem da galeria de imagens partilhada anexada em Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483861"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Adicione um VM usando uma imagem da galeria de imagens partilhada anexada
A Azure DevTest Labs permite-lhe anexar uma galeria de imagens partilhada ao seu laboratório e depois usar imagens na galeria como bases para os VMs que cria no laboratório. Para aprender a anexar uma galeria de imagens partilhada ao seu laboratório, consulte a [galeria de imagens partilhada Configure](configure-shared-image-gallery.md). Este artigo mostra-lhe como adicionar um VM ao seu laboratório usando uma imagem da galeria de imagens partilhada anexa como base. 

## <a name="azure-portal"></a>Portal do Azure
Nesta secção, aprende-se a usar o portal Azure para adicionar um VM ao seu laboratório com base numa imagem da galeria de imagens partilhada anexada. Esta secção não fornece instruções detalhadas passo a passo para a criação de um VM utilizando o portal Azure. Para estes detalhes, consulte [Criar um portal VM - Azure](devtest-lab-add-vm.md). Apenas destaca os passos onde seleciona uma imagem da galeria de imagens partilhada anexada e seleciona uma versão da imagem que pretende utilizar. 

Ao adicionar um VM ao seu laboratório, pode selecionar uma imagem da galeria de imagens partilhada anexada como uma imagem base: 

![Selecione uma imagem partilhada para a base](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Em seguida, no separador **Definições Avançadas** da página de **recursos do laboratório,** pode selecionar a versão da imagem que pretende utilizar como imagem base:

![Selecione versão de imagem](./media/add-vm-use-shared-image/select-version-shared-image.png)

Pode mudar para uma versão diferente da imagem após a criação do VM. 

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Se estiver a utilizar um modelo de Gestor de Recursos Azure para criar uma máquina virtual utilizando uma imagem de galeria de imagens partilhada, especifique um valor para o **SharedImageId** na secção **Propriedades.** Veja o seguinte exemplo: 

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

Para obter um exemplo completo do modelo do Gestor de Recursos, consulte [Criar uma máquina virtual utilizando uma](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) amostra de imagem partilhada no nosso repositório GitHub. 

## <a name="rest-api"></a>API REST

1. Primeiro, tens de obter a identificação da imagem na galeria de imagens partilhada. Uma maneira é listar todas as imagens na galeria de imagens partilhada anexa, utilizando o seguinte comando GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Invoque o método PUT em máquinas virtuais, passando o ID da imagem partilhada que recebeu da chamada anterior para o `properties.SharedImageId` .

## <a name="next-steps"></a>Passos seguintes
Para aprender a anexar uma galeria de imagens partilhada a um laboratório e configurá-la, consulte [a galeria de imagens partilhada Configure](configure-shared-image-gallery.md).