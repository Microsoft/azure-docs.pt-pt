---
title: Configurar uma galeria de imagens compartilhada no Azure DevTest Labs | Microsoft Docs
description: Saiba como configurar uma galeria de imagens compartilhada no Azure DevTest Labs
services: devtest-lab
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
ms.openlocfilehash: 80610168e0d293b65626da71ee349f25e456576b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774559"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurar uma galeria de imagem partilhada no Azure DevTest Labs
O DevTest Labs agora dá suporte ao recurso da [Galeria de imagens](../virtual-machines/windows/shared-image-galleries.md) compartilhadas. Permite aos utilizadores do laboratório aceder a imagens de uma localização partilhada durante a criação de recursos de laboratório. Ajuda-o ainda a criar a estrutura e a organização em torno das imagens de VM com gestão personalizada. O recurso da Galeria de imagens compartilhadas dá suporte a:

- Replicação global gerida de imagens
- Criação de versões e agrupamento de imagens para gestão mais fácil
- Torne as suas imagens bastante disponíveis com contas de Armazenamento com Redundância entre Zonas (ZRS) que suportam zonas de disponibilidade. O ZRS oferece uma melhor resiliência em relação a falhas zonais.
- Partilha entre subscrições e inquilinos com o controlo de acesso baseado em funções (RBAC).

Para obter mais informações, consulte a [documentação da Galeria de imagens](../virtual-machines/windows/shared-image-galleries.md)compartilhadas. 
 
Se tiver um elevado número de imagens geridas que precisa de manter e se quiser disponibilizá-las na sua empresa, pode utilizar uma galeria de imagens partilhadas como repositório que facilita a atualização e a partilha das suas imagens. Como proprietário do laboratório, pode anexar uma galeria de imagens partilhadas existente ao seu laboratório. Depois de a galeria estar anexada, os utilizadores do laboratório podem criar máquinas a partir das imagens mais recentes. Uma vantagem essencial desta funcionalidade é o DevTest Labs conseguir agora tirar partido da partilha de imagens entre laboratórios, subscrições e regiões. 

## <a name="considerations"></a>Considerações
- Você só pode anexar uma galeria de imagens compartilhadas a um laboratório por vez. Se desejar anexar outra galeria, você precisará desanexar a existente e anexar outra. 
- Atualmente, o DevTest Labs não dá suporte ao carregamento de imagens para a Galeria por meio do laboratório. 
- Ao criar uma máquina virtual usando uma imagem da Galeria de imagens compartilhada, o DevTest Labs sempre usa a versão mais recente publicada desta imagem.
- Embora o DevTest Labs faça automaticamente uma melhor tentativa de garantir que a Galeria de imagens compartilhadas replique imagens para a região em que o laboratório existe, nem sempre é possível. Para evitar que os usuários tenham problemas para criar VMs a partir dessas imagens, verifique se as imagens já estão replicadas para a região do laboratório. "

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços** no menu de navegação à esquerda.
1. Selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione seu **laboratório**.
1. Selecione **configuração e políticas** na seção **configurações** no menu à esquerda.
1. Selecione **galerias de imagens** compartilhadas em **bases da máquina virtual** no menu à esquerda.

    ![Menu de galerias de imagens compartilhadas](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Anexe uma galeria de imagens compartilhada existente ao seu laboratório clicando no botão **anexar** e selecionando a galeria na lista suspensa.

    ![Anexar](./media/configure-shared-image-gallery/attach-options.png)
1. Vá para a Galeria anexada e configure sua galeria para **habilitar ou desabilitar** imagens compartilhadas para a criação da VM. Selecione uma galeria de imagens na lista para configurá-la. 

    Por padrão, **permitir que todas as imagens sejam usadas como bases da máquina virtual** é definido como **Sim**. Isso significa que todas as imagens disponíveis na Galeria de imagens compartilhada anexada estarão disponíveis para um usuário de laboratório durante a criação de uma nova VM de laboratório. Se o acesso a determinadas imagens precisar ser restrito, altere **permitir que todas as imagens sejam usadas como bases da máquina virtual** como **não**e selecione as imagens que você deseja permitir ao criar VMs e, em seguida, selecione o botão **salvar** .

    ![Habilitar ou desabilitar](./media/configure-shared-image-gallery/enable-disable.png)
1. Os usuários do laboratório podem, então, criar uma máquina virtual usando as imagens habilitadas clicando em **+ Adicionar** e localizando a imagem na página **escolher sua base** .

    ![Utilizadores de laboratório](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Utilizar o modelo do Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Anexar uma galeria de imagens compartilhadas ao seu laboratório
Se você estiver usando um modelo de Azure Resource Manager para anexar uma galeria de imagens compartilhada ao laboratório, será necessário adicioná-lo na seção recursos do seu modelo do Resource Manager, conforme mostrado no exemplo a seguir:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Para obter um exemplo completo de modelo do Resource Manager, consulte estes exemplos de modelo do Resource Manager em nosso repositório GitHub público: [Configure uma galeria de imagens compartilhada ao criar um laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Usar API

### <a name="shared-image-galleries---create-or-update"></a>Galerias de imagens compartilhadas – criar ou atualizar

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Imagens da galerias de imagens compartilhadas-lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Passos seguintes
Consulte os artigos a seguir sobre como criar uma VM usando uma imagem da Galeria de imagens compartilhada anexada: [Criar uma VM usando uma imagem compartilhada da Galeria](add-vm-use-shared-image.md)
