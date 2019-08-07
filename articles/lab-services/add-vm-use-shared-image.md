---
title: Adicionar uma VM usando uma imagem compartilhada no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar uma VM (máquina virtual) usando uma imagem da Galeria de imagens compartilhada anexada no Azure DevTest Labs
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
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775594"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Adicionar uma VM usando uma imagem da Galeria de imagens compartilhada anexada
Azure DevTest Labs permite anexar uma galeria de imagens compartilhada ao seu laboratório e, em seguida, usar imagens na galeria como bases para as VMs criadas no laboratório. Para saber como anexar uma galeria de imagens compartilhada ao laboratório, consulte [Configurar Galeria de imagens](configure-shared-image-gallery.md)compartilhadas. Este artigo mostra como adicionar uma VM ao seu laboratório usando uma imagem da Galeria de imagens compartilhada anexada como uma base. 

## <a name="azure-portal"></a>Portal do Azure
Nesta seção, você aprenderá a usar o portal do Azure para adicionar uma VM ao seu laboratório com base em uma imagem da Galeria de imagens compartilhada anexada. Esta seção não fornece instruções passo a passo detalhadas para criar uma VM usando o portal do Azure. Para esses detalhes, consulte [criar uma VM-portal do Azure](devtest-lab-add-vm.md). Ele realça apenas as etapas em que você seleciona uma imagem da Galeria de imagens compartilhada anexada e seleciona uma versão da imagem que deseja usar. 

Ao adicionar uma VM ao seu laboratório, você pode selecionar uma imagem da Galeria de imagens compartilhada anexada como uma imagem base: 

![Selecionar uma imagem compartilhada para a base](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Em seguida, na guia **Configurações avançadas** da página **criar recurso do laboratório** , você pode selecionar a versão da imagem que deseja usar como a imagem base:

![Selecionar versão da imagem](./media/add-vm-use-shared-image/select-version-shared-image.png)

Você pode alternar para o uso de uma versão diferente da imagem após a criação da VM. 

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Se você estiver usando um modelo de Azure Resource Manager para criar uma máquina virtual usando uma imagem da Galeria de imagens compartilhada, especifique um valor para **sharedImageId** na seção **Propriedades** . Veja o seguinte exemplo: 

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

Para obter um exemplo completo de modelo do Resource Manager, consulte [criar uma máquina virtual usando uma amostra de imagem da Galeria de imagens compartilhada](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) em nosso repositório github. 

## <a name="rest-api"></a>API REST

1. Primeiro, você precisa obter a ID da imagem na Galeria de imagens compartilhadas. Uma maneira é listar todas as imagens na Galeria de imagens compartilhada anexada usando o comando GET a seguir. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Invoque o método PUT em máquinas virtuais passando a ID da imagem compartilhada que você recebeu da chamada anterior para o `properties.SharedImageId`.

## <a name="next-steps"></a>Passos Seguintes
Para saber como anexar uma galeria de imagens compartilhada a um laboratório e configurá-la, consulte [Configurar Galeria de imagens](configure-shared-image-gallery.md)compartilhadas.