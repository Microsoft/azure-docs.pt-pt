---
title: Utilize uma galeria de imagens partilhada nos Serviços Azure Lab ! Microsoft Docs
description: Aprenda a configurar uma conta de laboratório para usar uma galeria de imagens partilhada para que um utilizador possa partilhar uma imagem com outro e outro utilizador pode usar a imagem para criar um VM modelo no laboratório.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 04e3764b095706d091bb72baaae77f5a4016fd28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052839"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Use uma galeria de imagens partilhada nos Serviços Azure Lab
Este artigo mostra como os educadores/administradores de laboratório podem guardar uma imagem de máquina virtual modelo para uma [galeria de imagens partilhada](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) para que possa ser usada por outros para criar laboratórios. 

> [!IMPORTANT]
> Durante a utilização de uma Galeria de Imagens Partilhadas, a Azure Lab Services suporta apenas imagens com menos de 128 GB de ESPAÇO DISCO OS. Imagens com mais de 128 GB de espaço em disco ou vários discos não serão mostradas na lista de imagens de máquinas virtuais durante a criação de laboratório.

## <a name="scenarios"></a>Cenários
Aqui estão os dois cenários apoiados por esta característica: 

- Um administrador de contas de laboratório anexa uma galeria de imagens partilhada à conta do laboratório, e envia uma imagem para a galeria de imagens partilhada fora do contexto de um laboratório. Depois, os criadores de laboratório podem usar essa imagem da galeria de imagens partilhada para criar laboratórios. 
- Uma conta de laboratório anexa uma galeria de imagens partilhada à conta do laboratório. Um criador de laboratório (instrutor) guarda a imagem personalizada do seu laboratório para a galeria de imagens partilhada. Em seguida, outros criadores de laboratório podem selecionar esta imagem a partir da galeria de imagens partilhada para criar um modelo para os seus laboratórios. 

    Quando uma imagem é guardada numa galeria de imagens partilhada, a Azure Lab Services replica a imagem guardada para outras regiões disponíveis na mesma [geografia.](https://azure.microsoft.com/global-infrastructure/geographies/) Garante que a imagem está disponível para laboratórios criados noutras regiões na mesma geografia. Guardar imagens para uma galeria de imagens partilhadas incorre num custo adicional, que inclui o custo de todas as imagens replicadas. Este custo é separado do custo de utilização dos Serviços Azure Lab. Para obter mais informações sobre os preços da Galeria de Imagens Partilhadas, consulte [a Galeria de Imagens Partilhadas – Billing]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens partilhada utilizando [a Azure PowerShell](../virtual-machines/windows/shared-images.md) ou [a Azure CLI](../virtual-machines/linux/shared-images.md).
- Anexou a galeria de imagens partilhada à conta do laboratório. Para obter instruções passo a passo, consulte [como anexar ou desprender a galeria de imagens partilhada](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Guarde uma imagem para a galeria de imagens partilhada
Depois de anexar uma galeria de imagens partilhada, um administrador de conta de laboratório ou um educador pode guardar uma imagem para a galeria de imagens partilhada para que possa ser reutilizada por outros educadores. 

1. Na página **do modelo** para o laboratório, selecione Export to Shared **Image Gallery** na barra de ferramentas.

    ![Guardar botão de imagem](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. No diálogo **Export to Shared Image Gallery,** insira um nome para a **imagem**e, em seguida, selecione **Export**. 

    ![Exportação para diálogo da Galeria de Imagens Partilhadas](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Pode ver o progresso desta operação na página **Modelo.** Esta operação pode demorar algum tempo. 

    ![Exportação em curso](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação for bem sucedida, vê a seguinte mensagem:

    ![Exportação concluída](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Depois de guardar a imagem para a galeria de imagens partilhada, pode usar essa imagem da galeria ao criar outro laboratório. Também pode enviar uma imagem para a galeria de imagens partilhada fora do contexto de um laboratório. Para mais informações, consulte [a visão geral da galeria de imagens partilhada.](../virtual-machines/windows/shared-images.md) 

    > [!IMPORTANT]
    > Quando [guarda uma imagem de modelo de um laboratório](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) em Azure Lab Services para uma galeria de imagens partilhada, a imagem é enviada para a galeria como uma imagem **especializada.** [As imagens especializadas](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) mantêm informações específicas da máquina e perfis de utilizador. Você ainda pode enviar diretamente uma imagem generalizada para a galeria fora dos Serviços Azure Lab.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Use uma imagem da galeria de imagens partilhada
Um educador pode escolher uma imagem personalizada disponível na galeria de imagens partilhada para o modelo durante a criação de um novo laboratório.

![Use a imagem da máquina virtual da galeria](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Pode criar um modelo VM baseado em imagens **generalizadas** e **especializadas** nos Serviços Azure Lab. 


## <a name="next-steps"></a>Passos seguintes
Para mais informações sobre galerias de imagens partilhadas, consulte [a galeria de imagens partilhada.](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
