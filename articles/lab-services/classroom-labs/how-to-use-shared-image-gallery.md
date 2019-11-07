---
title: Usar uma galeria de imagens compartilhadas no Azure Lab Services | Microsoft Docs
description: Saiba como configurar uma conta de laboratório para usar uma galeria de imagens compartilhadas para que um usuário possa compartilhar uma imagem com outra e outro usuário possa usar a imagem para criar uma VM de modelo no laboratório.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: f438c32deb7e923f08396b0580d807d6e5b5e69a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585057"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como os professores/administrador de laboratório podem salvar uma imagem de máquina virtual de modelo para que ela seja reutilizada por outras pessoas. Essas imagens são salvas em uma [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md)do Azure. Como uma primeira etapa, o administrador do laboratório anexa uma galeria de imagens compartilhada existente à conta do laboratório. Depois que a Galeria de imagens compartilhada é anexada, os laboratórios criados na conta de laboratório podem salvar imagens na Galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem na Galeria de imagens compartilhadas para criar um modelo para suas classes. 

## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens compartilhada usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).
- Você anexou a Galeria de imagens compartilhadas à conta do laboratório. Para obter as instruções passo a passo, consulte [como anexar ou desanexar a Galeria de imagens compartilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na Galeria de imagens compartilhadas
Depois que uma galeria de imagens compartilhadas é anexada, um administrador de conta de laboratório ou um professor pode salvar uma imagem na Galeria de imagens compartilhadas para que possa ser reutilizada por outros professores. 

1. Na página **modelo** do laboratório, selecione **exportar para a Galeria de imagens compartilhadas** na barra de ferramentas.

    ![Botão Salvar imagem](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Na caixa de diálogo **exportar para a Galeria de imagens compartilhadas** , insira um **nome para a imagem**e, em seguida, selecione **Exportar**. 

    ![Caixa de diálogo Exportar para Galeria de imagens compartilhadas](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Você pode ver o progresso dessa operação na página **modelo** . Esta operação pode levar algum tempo. 

    ![Exportação em andamento](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Quando a operação de exportação for bem-sucedida, você verá a seguinte mensagem:

    ![Exportação concluída](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Você também pode carregar uma imagem na Galeria de imagens compartilhadas fora do contexto de um laboratório. Para obter mais informações, consulte [visão geral da Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usar uma imagem da Galeria de imagens compartilhadas
Um professor/professor pode escolher uma imagem personalizada disponível na Galeria de imagens compartilhadas para o modelo durante a criação do novo laboratório.

![Usar a imagem de máquina virtual da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
