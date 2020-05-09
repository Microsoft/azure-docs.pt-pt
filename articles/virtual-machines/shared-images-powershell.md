---
title: Crie uma galeria de imagem partilhada com a Azure PowerShell
description: Saiba como usar o Azure PowerShell para criar uma Galeria de Imagem Partilhada em Azure
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b7625903c4640fdb17eacd29822fe6203e075826
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796996"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Crie uma galeria de imagens partilhada com a Azure PowerShell 

Uma [Galeria de Imagem Partilhada](./windows/shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. Você pode criar várias galerias para que você possa logicamente agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo completo de acesso baseado em papéis (RBAC). As imagens podem ser versonizadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com Imagens Geridas.

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Passos seguintes

Crie uma imagem a partir de um [VM,](image-version-vm-powershell.md)uma [imagem gerida,](image-version-managed-image-powershell.md)ou uma [imagem em outra galeria.](image-version-another-gallery-powershell.md)

[O Azure Image Builder (pré-visualização)](./windows/image-builder-overview.md) pode ajudar a automatizar a criação da versão de imagem, pode até usá-la para atualizar e criar uma nova versão de imagem a partir de uma versão de [imagem existente.](./windows/image-builder-gallery-update-image-version.md) 

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


