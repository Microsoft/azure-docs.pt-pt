---
title: Criar uma Galeria de Imagens Partilhada com Azure PowerShell
description: Saiba como usar a Azure PowerShell para criar uma Galeria de Imagens Partilhada em Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 877439ddba6ffaf2fd060c29f28c25c18694605d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555572"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Criar uma galeria de imagens partilhada com a Azure PowerShell 

Uma [Galeria de Imagens Partilhadas](./shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SISTEMA. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras pessoas da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. Pode criar várias galerias para que possa, logicamente, agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo de acesso baseado em funções Azure (Azure RBAC). As imagens podem ser versadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com Imagens Geridas.

A funcionalidade Image Gallery partilhada tem vários tipos de recursos. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Passos seguintes

Crie uma imagem a partir de um [VM,](image-version-vm-powershell.md)uma [imagem gerida,](image-version-managed-image-powershell.md)ou uma [imagem noutra galeria.](image-version-another-gallery-powershell.md)

[O Azure Image Builder (pré-visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação de versão de imagem, podendo até usá-la para atualizar e [criar uma nova versão de imagem a partir de uma versão de imagem existente.](./windows/image-builder-gallery-update-image-version.md) 

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)