---
title: Utilize uma galeria de imagens partilhada nos Serviços de Laboratório Azure  Microsoft Docs
description: Aprenda a configurar uma conta de laboratório para usar uma galeria de imagens partilhada para que um utilizador possa partilhar uma imagem com outro e outro utilizador possa usar a imagem para criar um Modelo VM no laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 79aea7d8a4a969e0ccd42ef94d77a9aa396ab91c
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591324"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Use uma galeria de imagens partilhada nos Serviços de Laboratório Azure
Este artigo mostra como professores/administrador de laboratório podem guardar uma imagem de máquina virtual modelo para que seja reutilizada por outros. Estas imagens são guardadas numa galeria de [imagens partilhada selada](../../virtual-machines/windows/shared-image-galleries.md)em Azure. Como primeiro passo, o administrador do laboratório anexa uma galeria de imagens partilhadas existente à conta do laboratório. Uma vez anexada a galeria de imagens partilhadas, os laboratórios criados na conta de laboratório podem guardar imagens para a galeria de imagens partilhada. Outros professores podem selecionar esta imagem a partir da galeria de imagens partilhadas para criar um modelo para as suas aulas. 

> [!NOTE]
> Atualmente, a Azure Lab Services apoia a criação de VMs com base apenas em imagens **VM generalizadas** (imagens não especializadas) numa galeria de imagens partilhadas. 

## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens partilhada utilizando o [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [o Azure CLI](../../virtual-machines/linux/shared-images.md).
- Anexou a galeria de imagens partilhada à conta do laboratório. Para instruções passo a passo, consulte Como anexar ou separar a galeria de [imagens partilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Guarde uma imagem para a galeria de imagens partilhadas
Depois de anexar uma galeria de imagens partilhadas, um administrador de uma conta de laboratório ou um professor pode guardar uma imagem para a galeria de imagens partilhada para que possa ser reutilizada por outros professores. 

1. Na página **Modelo** para o laboratório, selecione **Export to Shared Image Gallery** na barra de ferramentas.

    ![Guardar botão de imagem](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. No diálogo **exportado para a Galeria** de Imagem Partilhada, introduza um **nome para a imagem**, e, em seguida, selecione **Export .** 

    ![Exportação para diálogo da Galeria de Imagem Partilhada](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Pode ver o progresso desta operação na página **'Modelo'.** Esta operação pode demorar algum dia. 

    ![Exportação em curso](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação for bem sucedida, consulte a seguinte mensagem:

    ![Exportação concluída](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Também pode enviar uma imagem para a galeria de imagens partilhada fora do contexto de um laboratório. Para mais informações, consulte a visão geral da galeria de [imagens partilhadas.](../../virtual-machines/windows/shared-images.md) 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Use uma imagem da galeria de imagens partilhadas
Um professor/professor pode escolher uma imagem personalizada disponível na galeria de imagens partilhadas para o modelo durante a criação de um novo laboratório.

![Use imagem de máquina virtual da galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre galerias de imagem partilhadas, consulte a galeria de [imagens partilhadas.](../../virtual-machines/windows/shared-image-galleries.md)
