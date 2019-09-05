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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 5ce0a81f6c9c886fcbe9186dd7363c38170ca580
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382956"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Usar uma galeria de imagens compartilhadas no Azure Lab Services
Este artigo mostra como os professores/administrador de laboratório podem salvar uma imagem de máquina virtual de modelo para que ela seja reutilizada por outras pessoas. Essas imagens são salvas em uma [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md)do Azure. Como uma primeira etapa, o administrador do laboratório anexa uma galeria de imagens compartilhada existente à conta do laboratório. Depois que a Galeria de imagens compartilhada é anexada, os laboratórios criados na conta de laboratório podem salvar imagens na Galeria de imagens compartilhadas. Outros professores podem selecionar essa imagem na Galeria de imagens compartilhadas para criar um modelo para suas classes. 

## <a name="prerequisites"></a>Pré-requisitos
- Crie uma galeria de imagens compartilhada usando [Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).
- Você anexou a Galeria de imagens compartilhadas à conta do laboratório. Para obter as instruções passo a passo, consulte [como anexar ou desanexar a Galeria de imagens compartilhadas](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Salvar uma imagem na Galeria de imagens compartilhadas
Depois que uma galeria de imagens compartilhadas é anexada, um administrador de conta de laboratório ou um professor pode salvar uma imagem na Galeria de imagens compartilhadas para que possa ser reutilizada por outros professores. 

1. Na home page do laboratório, selecione **Salvar imagem** no bloco na seção **modelo** .

    ![Botão Salvar imagem](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  Na janela **Salvar imagem da máquina virtual** , insira um nome para a imagem e selecione **salvar**. 

    ![Janela Salvar imagem da máquina virtual](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. Verifique o status no bloco do laboratório. 

    ![Status da operação de salvar imagem](../media/how-to-use-shared-image-gallery/save-image-status.png)

 Você também pode carregar uma imagem na Galeria de imagens compartilhadas fora do contexto de um laboratório. Para obter mais informações, consulte [visão geral da Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Usar uma imagem da Galeria de imagens compartilhadas
Um professor/professor pode escolher uma imagem personalizada disponível na Galeria de imagens compartilhadas para o modelo durante a criação do novo laboratório.

![Usar a imagem de máquina virtual da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre galerias de imagens compartilhadas, consulte [Galeria de imagens compartilhadas](../../virtual-machines/windows/shared-image-galleries.md).
