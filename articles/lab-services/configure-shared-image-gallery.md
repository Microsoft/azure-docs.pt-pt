---
title: Configure uma galeria de imagens partilhada seletiva nos Laboratórios Azure DevTest [ Microsoft Docs
description: Saiba como configurar uma galeria de imagens partilhadas em Azure DevTest Labs
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77589322"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurar uma galeria de imagem partilhada no Azure DevTest Labs
A DevTest Labs agora suporta a funcionalidade Da Galeria de [Imagem Partilhada.](../virtual-machines/windows/shared-image-galleries.md) Permite que os utilizadores de laboratório acedam a imagens de um local partilhado enquanto criam recursos laboratoriais. Também o ajuda a construir estrutura e organização em torno das suas imagens VM personalizadas. A galeria de imagens partilhadas apresenta suportes:

- Replicação global gerida de imagens
- Versão e agrupamento de imagens para uma gestão mais fácil
- Disponibilize as suas imagens com contas de Armazenamento Redundante da Zona (ZRS) em regiões que suportem zonas de disponibilidade. O ZRS oferece uma melhor resiliência contra falhas zonais.
- Partilha de subscrições, e até entre inquilinos, utilizando o controlo de acesso baseado em papéis (RBAC).

Para mais informações, consulte a documentação da [Galeria de Imagens Partilhadas.](../virtual-machines/windows/shared-image-galleries.md) 
 
Se tiver um grande número de imagens geridas que precisa de manter e gostaria de as disponibilizar em toda a sua empresa, pode utilizar uma galeria de imagens partilhada como um repositório que facilita a atualização e partilha das suas imagens. Como dono de laboratório, pode anexar uma galeria de imagens partilhadas existente ao seu laboratório. Uma vez anexada esta galeria, os utilizadores de laboratório podem criar máquinas a partir destas imagens mais recentes. Um dos principais benefícios desta funcionalidade é que a DevTest Labs pode agora aproveitar a partilha de imagens em laboratórios, através de subscrições e em todas as regiões. 

> [!NOTE]
> Para saber sobre os custos associados ao serviço da Galeria de Imagem Partilhada, consulte [a Billing for Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Considerações
- Só se pode anexar uma galeria de imagens partilhada a um laboratório de cada vez. Se quiser anexar outra galeria, terá de separar a existente e anexar outra. 
- ATualmente, a DevTest Labs suporta imagens generalizadas na galeria de imagens partilhadas.
- A Tualmente, a DevTest Labs não suporta o envio de imagens para a galeria através do laboratório. 
- Ao criar uma máquina virtual utilizando uma imagem partilhada de galeria de imagens, a DevTest Labs usa sempre a versão mais recente publicada desta imagem. No entanto, se uma imagem tiver várias versões, o utilizador pode optar por criar uma máquina a partir de uma versão anterior, indo para o separador de definições Avançadas durante a criação de máquinas virtuais.  
- Embora a DevTest Labs faça automaticamente a melhor tentativa para garantir que a galeria de imagens partilhadas reproduz imagens para a região em que o Laboratório existe, nem sempre é possível. Para evitar que os utilizadores tenham problemas em criar VMs a partir destas imagens, certifique-se de que as imagens já estão replicadas para a região do laboratório."

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** no menu de navegação à esquerda.
1. Selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o seu **laboratório.**
1. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo.
1. Selecione Galerias de Imagem Partilhada sob bases de **máquinas** **virtuais** no menu esquerdo.

    ![Menu de Galerias de Imagem Partilhada](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Fixe uma galeria de imagens partilhadas existente ao seu laboratório clicando no botão **Attach** e selecionando a sua galeria no dropdown.

    ![Ligar](./media/configure-shared-image-gallery/attach-options.png)
1. Vá à galeria anexa e configure a sua galeria para **permitir ou desativar** imagens partilhadas para a criação de VM. Selecione uma galeria de imagens da lista para configurá-la. 

    Por predefinição, **permita que todas as imagens sejam utilizadas como bases** de máquinas virtuais estão definidas para **Sim**. Significa que todas as imagens disponíveis na galeria de imagens partilhadas anexas estarão disponíveis para um utilizador de laboratório ao criar um novo VM de laboratório. Se o acesso a determinadas imagens tiver de ser restringido, **altere Permitir que todas as imagens sejam utilizadas como bases** de máquinas virtuais para **Não**, e selecione as imagens que pretende permitir ao criar VMs e, em seguida, selecione o botão **Guardar.**

    ![Ativar ou desativar](./media/configure-shared-image-gallery/enable-disable.png)
1. Os utilizadores do laboratório podem então criar uma máquina virtual utilizando as imagens ativadas clicando em **+Add** e encontrando a imagem na página **base de escolha.**

    ![Utilizadores de laboratório](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Use o modelo de Gestor de Recursos Azure

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Fixe uma galeria de imagens partilhadas ao seu laboratório
Se estiver a usar um modelo do Gestor de Recursos Azure para anexar uma galeria de imagens partilhada ao seu laboratório, precisa adicioná-la sob a secção de recursos do seu modelo de Gestor de Recursos, como mostra o seguinte exemplo:

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

Para obter um exemplo completo do modelo do Gestor de Recursos, consulte estas amostras de modelo do Gestor de Recursos no nosso repositório público gitHub: Configure uma galeria de [imagens partilhadas enquanto cria um laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-api"></a>Utilizar API

### <a name="shared-image-galleries---create-or-update"></a>Galerias de imagem partilhadas - criar ou atualizar

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

### <a name="shared-image-galleries-images---list"></a>Imagens de Galerias de Imagem Partilhada - Lista 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos sobre a criação de um VM usando uma imagem da galeria de imagens partilhadas anexada: [Criar um VM usando uma imagem partilhada da galeria](add-vm-use-shared-image.md)
