---
title: Configure uma galeria de imagens partilhada em Azure DevTest Labs Microsoft Docs
description: Saiba como configurar uma galeria de imagens partilhada em Azure DevTest Labs, que permite aos utilizadores aceder a imagens a partir de uma localização partilhada enquanto criam recursos laboratoriais.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7ec08fa741c1b52d3dd1d1e2b4247d3689190020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271044"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurar uma galeria de imagem partilhada no Azure DevTest Labs
A DevTest Labs agora suporta a funcionalidade [Da Galeria de Imagens Partilhadas.](../virtual-machines/windows/shared-image-galleries.md) Permite que os utilizadores de laboratório acedam a imagens a partir de um local partilhado enquanto criam recursos laboratoriais. Também ajuda a construir estrutura e organização em torno das suas imagens VM geridas por medida. A Galeria de Imagens Partilhadas suporta:

- Replicação global gerida de imagens
- Veragem e agrupamento de imagens para uma gestão mais fácil
- Disponibilize as suas imagens com contas de Armazenamento Redundante (ZRS) em regiões que suportam zonas de disponibilidade. O ZRS oferece uma melhor resiliência contra falhas zonais.
- Partilha entre subscrições, e até mesmo entre inquilinos, utilizando o controlo de acesso baseado em funções (RBAC).

Para mais informações, consulte [a documentação da Galeria de Imagens Partilhadas.](../virtual-machines/windows/shared-image-galleries.md) 
 
Se tiver um grande número de imagens geridas que precisa de manter e gostaria de as disponibilizar em toda a sua empresa, pode utilizar uma galeria de imagens partilhada como um repositório que facilita a atualização e partilha das suas imagens. Como dono de laboratório, pode anexar uma galeria de imagens partilhada existente ao seu laboratório. Uma vez anexada esta galeria, os utilizadores de laboratório podem criar máquinas a partir destas imagens mais recentes. Um dos principais benefícios desta funcionalidade é que a DevTest Labs pode agora aproveitar a partilha de imagens em laboratórios, através de subscrições e em todas as regiões. 

> [!NOTE]
> Para saber mais sobre os custos associados ao serviço Da Galeria de Imagens Partilhadas, consulte [Billing for Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Considerações
- Só se pode anexar uma galeria de imagens partilhada a um laboratório de cada vez. Se quiser anexar outra galeria, terá de separar a existente e anexar outra. 
- DevTest Labs não suporta o upload de imagens para a galeria através do laboratório. 
- Ao criar uma máquina virtual utilizando uma imagem de galeria de imagens partilhada, a DevTest Labs usa sempre a versão mais recente publicada desta imagem. No entanto, se uma imagem tiver várias versões, o utilizador pode optar por criar uma máquina a partir de uma versão anterior, indo ao separador definições Avançadas durante a criação de máquinas virtuais.  
- Embora a DevTest Labs faça automaticamente uma melhor tentativa para garantir que a galeria de imagens partilhada reproduz imagens para a região onde o Laboratório existe, nem sempre é possível. Para evitar que os utilizadores tenham problemas em criar VMs a partir destas imagens, certifique-se de que as imagens já estão replicadas na região do laboratório."

## <a name="use-azure-portal"></a>Utilizar o portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** no menu de navegação à esquerda.
1. Selecione **DevTest Labs** da lista.
1. Da lista de laboratórios, selecione o seu **laboratório.**
1. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo.
1. Selecione **Galerias de Imagem Partilhadas** em **bases de máquinas virtuais** no menu esquerdo.

    ![Menu de Galerias de Imagem Partilhadas](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Anexe uma galeria de imagens partilhada existente ao seu laboratório clicando no botão **Attach** e selecionando a sua galeria no dropdown.

    ![Ligar](./media/configure-shared-image-gallery/attach-options.png)
1. Depois de anexar a galeria de imagens, selecione-a para ir à galeria anexa. Configure a sua galeria para **permitir ou desativar** imagens partilhadas para a criação de VM. Selecione uma galeria de imagens da lista para configurá-la. 

    Por predefinição, **permita que todas as imagens sejam utilizadas como bases de máquinas virtuais** está definida para **Sim**. Significa que todas as imagens disponíveis na galeria de imagens partilhadas anexas estarão disponíveis para um utilizador de laboratório ao criar um novo VM de laboratório. Se o acesso a determinadas imagens tiver de ser restringido, **altere Deixe todas as imagens serem utilizadas como bases de máquinas virtuais** para **Nº**, selecione as imagens que pretende permitir ao criar VMs e, em seguida, selecione o botão **Guardar.**

    :::image type="content" source="./media/configure-shared-image-gallery/enable-disable.png" alt-text="Ativar ou desativar imagens":::

    > [!NOTE]
    > São suportadas imagens generalizadas e especializadas na galeria de imagens partilhadas. 
1. Os utilizadores de laboratório podem então criar uma máquina virtual utilizando as imagens ativadas clicando em **+Adicionar** e encontrando a imagem na página **base.**

    ![Utilizadores de laboratório](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Use o modelo de gestor de recursos Azure

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Anexe uma galeria de imagens partilhada ao seu laboratório
Se estiver a utilizar um modelo de Gestor de Recursos Azure para anexar uma galeria de imagens partilhada ao seu laboratório, tem de adicioná-la na secção de recursos do seu modelo de Gestor de Recursos, como mostra o seguinte exemplo:

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

Para obter um exemplo completo do modelo do Gestor de Recursos, consulte estas amostras de modelo do Gestor de Recursos no nosso repositório público GitHub: [Configuure uma galeria de imagens partilhada enquanto cria um laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>Utilizar a API REST

### <a name="get-a-list-of-labs"></a>Obtenha uma lista de laboratórios 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>Obtenha a lista de galerias de imagem partilhadas associadas a um laboratório

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Criar ou atualizar galeria de imagens partilhada

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

### <a name="list-images-in-a-shared-image-gallery"></a>Listar imagens numa galeria de imagens partilhada

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos sobre a criação de um VM utilizando uma imagem da galeria de imagens partilhada anexada: [Criar um VM utilizando uma imagem partilhada da galeria](add-vm-use-shared-image.md)
