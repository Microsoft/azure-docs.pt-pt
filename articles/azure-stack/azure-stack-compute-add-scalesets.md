---
title: Disponibilizar conjuntos de dimensionamento de máquinas virtuais no Azure Stack | Documentos da Microsoft
description: Saiba como um operador da cloud pode adicionar conjuntos de dimensionamento de máquinas virtuais no Azure Stack Marketplace
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: d7b2c0a39d6d7287b3f956d824239a40e373ea36
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594768"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Disponibilizar conjuntos de dimensionamento de máquinas virtuais no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*
  
Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure Stack. Pode usá-los para implementar e gerir um conjunto de máquinas virtuais idênticas. Com todas as máquinas virtuais configuradas da mesma forma, os conjuntos de dimensionamento não requerem o aprovisionamento prévio das máquinas virtuais. É mais fácil criar serviços em grande escala destinados a big compute, macrodados e cargas de trabalho em contentores.

Este artigo orienta-o no processo de disponibilizar conjuntos de dimensionamento no Azure Stack Marketplace. Depois de concluir este procedimento, os seus utilizadores podem adicionar conjuntos de dimensionamento de máquina virtual para suas assinaturas.

Conjuntos de dimensionamento de máquinas virtuais no Azure Stack são semelhantes aos conjuntos de dimensionamento de máquinas virtuais no Azure. Para obter mais informações, veja os vídeos seguintes:

* [Mark Russinovich talks Azure Scale Sets (Mark Russinovich fala sobre os conjuntos de dimensionamento do Azure)](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets with Guy Bowerman (Conjuntos de Dimensionamento de Máquinas Virtuais, com Guy Bowerman)](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

No Azure Stack, conjuntos de dimensionamento de máquina virtual não suportam o dimensionamento automático. Pode adicionar mais instâncias para um conjunto de dimensionamento com modelos do Resource Manager, a CLI ou o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

* **O Marketplace:** Registe o Azure Stack com o Azure global para ativar a disponibilidade de itens no Marketplace. Siga as instruções em [registar o Azure Stack com o Azure](azure-stack-registration.md).
* **Imagem do sistema operativo:** Antes de um conjunto de dimensionamento de máquinas virtuais (VMSS) pode ser criado, tem de transferir as imagens de VM para utilização no VMSS do [do Azure Stack Marketplace](azure-stack-download-azure-marketplace-item.md). As imagens já devem estar presentes antes de um utilizador pode criar um novo VMSS.

## <a name="use-the-azure-stack-portal"></a>Utilizar o portal do Azure Stack

>[!IMPORTANT]  
> As informações nesta secção aplicam-se ao utilizar a versão do Azure Stack 1808 ou posterior. Se tiver uma versão 1807 ou anterior, consulte [adicionar o conjunto de dimensionamento de Máquina Virtual (antes da 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Inicie sessão no portal do Azure Stack. Em seguida, vá para **todos os serviços**, em seguida, **conjuntos de dimensionamento de máquinas virtuais**e, em **de COMPUTAÇÃO**, selecione **conjuntos de dimensionamento de máquinas virtuais**.
   ![Conjuntos de dimensionamento de máquinas virtuais selecionadas](media/azure-stack-compute-add-scalesets/all-services.png)

2. Selecione criar ***conjuntos de dimensionamento de máquinas virtuais***.
   ![Criar um conjunto de dimensionamento de máquinas virtuais](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Preencha os campos vazios, escolha de entre as listas pendentes para **a imagem de disco do sistema operativo**, **subscrição**, e **tamanho da instância**. Selecione **Sim** para **utilizar discos geridos**. Em seguida, clique em **Criar**.
    ![Configurar e criar](media/azure-stack-compute-add-scalesets/create.png)

4. Para ver o seu novo dimensionamento de máquinas virtuais do conjunto, aceda a **todos os recursos**, procure o nome do conjunto de dimensionamento da máquina virtual e, em seguida, selecione o seu nome na pesquisa.
   ![Ver o conjunto de dimensionamento](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Adicionar o conjunto de dimensionamento de Máquina Virtual (anterior à versão 1808)

>[!IMPORTANT]  
> As informações nesta secção aplicam-se ao utilizar uma versão do Azure Stack anterior 1808. Se utilizar a versão 1808 ou posterior, consulte [utilizar o portal do Azure Stack](#use-the-azure-stack-portal).

1. Abra a pilha do Azure Marketplace e ligar ao Azure. Selecione **gestão de Marketplace**, em seguida, clique em **+ adicionar a partir do Azure**.

    ![Gestão do Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Adicionar e transfira o item do marketplace o conjunto de dimensionamento de máquinas virtuais.

    ![Conjunto de Dimensionamento da Máquina Virtual](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Atualizar imagens num conjunto de dimensionamento de Máquina Virtual

Depois de criar um conjunto de dimensionamento de máquinas virtuais, os usuários podem atualizar imagens no conjunto sem ter de ser recriadas de conjunto de dimensionamento de dimensionamento. O processo para atualizar uma imagem depende dos seguintes cenários:

1. Modelo de implementação do conjunto de dimensionamento de máquina virtual especifica **mais recente** para **versão**:  

   Quando o `version` está definido como **mais recente** no `imageReference` secção do modelo para uma escala definido, dimensione as operações sobre a utilização do conjunto de dimensionamento, a versão mais recente disponível da imagem do conjunto de dimensionamento instâncias. Depois de aumentar verticalmente está concluída, pode eliminar as instâncias de conjuntos de dimensionamento da máquina virtual mais antigas. Os valores para `publisher`, `offer`, e `sku` permanecem inalterados.

   O exemplo JSON seguinte especifica `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Antes de aumentar verticalmente, pode utilizar uma nova imagem, tem de transferir a nova imagem:  

   * Quando a imagem no Marketplace é uma versão mais recente que a imagem no conjunto de dimensionamento, transferir a nova imagem, que substitui a imagem mais antiga. Depois da imagem é substituída, um utilizador pode avançar para aumentar verticalmente.

   * Quando a versão da imagem no Marketplace é o mesmo que a imagem no conjunto de dimensionamento, elimine a imagem que está a ser utilizada no conjunto de dimensionamento e, em seguida, transfira a nova imagem. Durante o tempo entre a remoção da imagem original e o download da nova imagem, não é possível aumentar verticalmente.

     Este processo é necessário para voltar a agregar imagens que utilizam o formato de ficheiro disperso, introduzido com a versão 1803.

2. Modelo de implementação de conjunto de dimensionamento de máquinas virtuais **não especifica mais recente** para **versão** e especifica um número de versão em vez disso:  

    Se baixar uma imagem com uma versão mais recente (que altera a versão disponível), o conjunto de dimensionamento não é possível aumentar verticalmente. Isso acontece por design, como a versão de imagem especificada no modelo de conjunto de dimensionamento tem de estar disponível.  

Para obter mais informações, consulte [discos do sistema operativo e imagens](./user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Dimensionar um conjunto de dimensionamento de máquina virtual

Pode aumentar o tamanho de um *conjunto de dimensionamento de máquina virtual* para que seja maior ou menor.  

1. No portal, selecione o conjunto de dimensionamento e, em seguida, selecione **Scaling**.

2. Utilize a barra de slide para definir o novo nível de dimensionamento para este conjunto de dimensionamento de máquina virtual e, em seguida, clique em **guardar**.

     ![O conjunto de dimensionamento](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Remover um conjunto de dimensionamento de máquinas virtuais

Para remover um item da galeria o conjunto de dimensionamento de máquinas virtuais, execute o seguinte comando do PowerShell:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> O item da Galeria não pode ser removido imediatamente. Poderá ter de atualizar o portal várias vezes antes do item mostra como removido do Marketplace.

## <a name="next-steps"></a>Passos Seguintes

* [Transferir itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md)