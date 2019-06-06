---
title: Configurar uma galeria de imagem partilhada no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como configurar uma galeria de imagem partilhada no Azure DevTest Labs
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: fba969b70ae052c928f33888d3c93eb7683ae9f7
ms.sourcegitcommit: ec7b0bf593645c0d1ef401a3350f162e02c7e9b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2019
ms.locfileid: "66455788"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurar uma galeria de imagem partilhada no Azure DevTest Labs
DevTest Labs agora suporta o [Galeria de imagens de partilhado](/virtual-machines/windows/shared-image-galleries.md) funcionalidade. Permite aos utilizadores do laboratório aceder a imagens de uma localização partilhada durante a criação de recursos de laboratório. Ajuda-o ainda a criar a estrutura e a organização em torno das imagens de VM com gestão personalizada. Suporta a funcionalidade de Galeria de imagens de partilhado:

- Replicação global gerida de imagens
- Criação de versões e agrupamento de imagens para gestão mais fácil
- Torne as suas imagens bastante disponíveis com contas de Armazenamento com Redundância entre Zonas (ZRS) que suportam zonas de disponibilidade. O ZRS oferece uma melhor resiliência em relação a falhas zonais.
- Partilha entre subscrições e inquilinos com o controlo de acesso baseado em funções (RBAC).

Para obter mais informações, consulte [documentação de Galeria de imagens de partilhado](../virtual-machines/windows/shared-image-galleries.md). 
 
Se tiver um elevado número de imagens geridas que precisa de manter e se quiser disponibilizá-las na sua empresa, pode utilizar uma galeria de imagens partilhadas como repositório que facilita a atualização e a partilha das suas imagens. Como proprietário do laboratório, pode anexar uma galeria de imagens partilhadas existente ao seu laboratório. Depois de a galeria estar anexada, os utilizadores do laboratório podem criar máquinas a partir das imagens mais recentes. Uma vantagem essencial desta funcionalidade é o DevTest Labs conseguir agora tirar partido da partilha de imagens entre laboratórios, subscrições e regiões. 

## <a name="considerations"></a>Considerações
- Apenas pode anexar uma galeria de imagens partilhado a um laboratório de cada vez. Se pretende anexar a Galeria de outro, terá de anular a exposição a já existente e ligar a outra. 
- DevTest Labs não suporta atualmente o carregamento de imagens na Galeria por meio do laboratório. 
- Ao criar uma máquina virtual utilizando uma imagem da Galeria de imagem partilhada, o DevTest Labs utiliza sempre a versão publicada mais recente dessa imagem.
- Embora o DevTest Labs faz automaticamente uma tentativa de utilizações para garantir a Galeria de imagem partilhada replica imagens para a região na qual existe o laboratório, não é sempre possível. Para evitar os utilizadores com dificuldades em criar VMs a partir destas imagens, certifique-se que as imagens já são replicadas para a região do laboratório."

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços** no menu de navegação à esquerda.
1. Selecione **DevTest Labs** da lista.
1. Na lista de laboratórios, selecione seu **laboratório**.
1. Selecione **Konfigurace a zásady** no **definições** secção no menu da esquerda.
1. Selecione **galerias de imagem partilhada** sob **bases de Máquina Virtual** no menu da esquerda.

    ![Menu de galerias de imagem partilhada](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Anexar uma galeria de imagem partilhada existente para o laboratório ao clicar no **Attach** botão e ao selecionar a sua galeria na lista pendente.

    ![Ligar](./media/configure-shared-image-gallery/attach-options.png)
1. Vá para a Galeria anexada e configure a sua galeria para **ativar ou desativar** partilhado imagens para a criação da VM.

    ![Ativar ou desativar](./media/configure-shared-image-gallery/enable-disable.png)
1. Os utilizadores de laboratório, em seguida, podem criar uma máquina virtual com as imagens ativadas clicando em **+ adicionar** e encontrar a imagem no **escolha a sua base de** página.

    ![Utilizadores de laboratório](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Utilizar o modelo do Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Anexar uma galeria de imagem partilhada para seu laboratório
Se estiver a utilizar um modelo Azure Resource Manager para anexar uma galeria de imagem partilhada para seu laboratório, terá de adicioná-lo sob a seção resources do modelo do Resource Manager, conforme mostrado no exemplo a seguir:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Para obter um exemplo de modelo do Resource Manager completo, veja estes exemplos de modelos do Resource Manager no nosso repositório do GitHub público: [Configurar uma galeria de imagem partilhada durante a criação de um laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Criar uma VM com uma imagem a partir da Galeria de imagem partilhada
Se estiver a utilizar um modelo Azure Resource Manager para criar uma máquina virtual utilizando uma imagem da Galeria de imagem partilhada, utilize o exemplo seguinte:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Para saber mais, veja estes exemplos de modelos do Resource Manager no nosso GitHub público.
[Criar uma máquina virtual utilizando uma imagem da Galeria de imagem partilhada](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Utilizar a API

- Utilize a versão de 2018-10-15-a pré-visualização da API.
- Para anexar sua galeria, envie a solicitação, conforme mostrado no seguinte fragmento:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Para ver todas as imagens na Galeria de imagens partilhado, pode listar todas as imagens partilhadas, juntamente com suas IDs de recurso por

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Para criar uma VM com imagens partilhadas, pode executar um PUT em máquinas virtuais e nas propriedades da máquina virtual, passar o ID das imagens partilhados que obteve da chamada anterior. Para as propriedades. SharedImageId


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos sobre artefatos:

- [Especifique os artefactos obrigatórios para o laboratório](devtest-lab-mandatory-artifacts.md)
- [Criar artefacto personalizado](devtest-lab-artifact-author.md)
- [Adicionar um repositório de artefactos a um laboratório](devtest-lab-artifact-author.md)
- [Diagnosticar falhas de artefactos](devtest-lab-troubleshoot-artifact-failure.md)
