---
title: Utilizar uma galeria de imagem partilhada do Azure Lab Services | Documentos da Microsoft
description: Saiba como configurar uma conta de laboratório para utilizar uma galeria de imagem partilhada para que um utilizador pode partilhar uma imagem com os outros e outro utilizador pode utilizar a imagem para criar um modelo de VM no laboratório.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412857"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utilizar uma galeria de imagem partilhada do Azure Lab Services
Este artigo mostra como o administrador de professores/laboratório pode economizar uma imagem de máquina virtual do modelo para sua reutilização por outras pessoas. Estas imagens são guardadas no Azure [Galeria de imagem partilhada](../../virtual-machines/windows/shared-image-galleries.md). Como primeiro passo, o administrador de laboratório anexa uma galeria de imagem partilhada existente para a conta de laboratório. Depois da Galeria de imagem partilhada é anexada, laboratórios criados da conta de laboratório podem guardar imagens na Galeria de imagem partilhada. Outros professores podem selecionar esta imagem a partir da Galeria de imagem partilhada para criar um modelo para suas classes. 

## <a name="prerequisites"></a>Pré-requisitos
- Criar uma galeria de imagem partilhada utilizando [do Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).
- A Galeria de imagem partilhada ligou-se para a conta de laboratório. Para obter instruções passo a passo, consulte [como anexar ou desanexar partilhado Galeria de imagens](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Guardar uma imagem na Galeria de imagem partilhada
Depois de uma galeria de imagem partilhada é anexada, um professor pode salvar ou carregar uma imagem na Galeria de imagem partilhada para que possa ser reutilizado por outros professores. Para obter instruções para carregar uma imagem à Galeria de imagens partilhado, consulte [descrição geral da Galeria de imagens de partilhado](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Curently, a interface de utilizador (IU) de laboratórios de sala de aula não suporta a guardar uma imagem de laboratório para a Galeria de imagem partilhada. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utilizar uma imagem a partir da Galeria de imagem partilhada
Professor/professora podem escolher uma imagem personalizada, disponível na Galeria de imagens partilhado para o modelo durante a criação do novo laboratório.

![Utilizar imagem de máquina virtual a partir da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as galerias de imagem partilhada, consulte [Galeria de imagem partilhada](../../virtual-machines/windows/shared-image-galleries.md).
