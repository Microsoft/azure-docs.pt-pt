---
title: Crie imagens VM Azure Linux partilhadas usando o portal
description: Aprenda a usar o portal Azure para criar e partilhar imagens de máquinas virtuais do Linux.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 28cdc96020d085c6f44c8b6818aa76dd7eb29891
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789006"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Criar uma Galeria de Imagem Partilhada Azure usando o portal

Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. Você pode criar várias galerias para que você possa logicamente agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo completo de acesso baseado em papéis (RBAC). As imagens podem ser versonizadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com Imagens Geridas.

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos. Vamos usar ou construir estes neste artigo:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>Antes de começar

Para completar o exemplo neste artigo, deve ter uma imagem gerida existente de um VM generalizado, ou uma imagem de um VM especializado. Pode seguir o [Tutorial: Criar uma imagem personalizada de um Azure VM com A Azure PowerShell](tutorial-custom-images.md) para criar uma imagem gerida, ou [criar um instantâneo](../windows/snapshot-copy-managed-disk.md) para um VM especializado. Tanto para imagens geridas como para imagens, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Criar VMs 

Agora pode criar um ou mais novos VMs. Este exemplo cria um VM chamado *myVMfromImage,* no *myResourceGroup* no centro de dados *dos EUA Orientais.*

1. Vá para a sua definição de imagem. Pode utilizar o filtro de recursos para mostrar todas as definições de imagem disponíveis.
1. Na página para a sua definição de imagem, selecione **Criar VM** a partir do menu na parte superior da página.
1. Para **o grupo Recursos,** selecione **Criar novo** e digitar *myResourceGroup* para o nome.
1. No nome da **máquina virtual,** digite *myVM*.
1. Para **a Região,** selecione *East US*.
1. Para **opções de disponibilidade,** deixe o padrão de não ser necessário um despedimento de *infraestrutura*.
1. O valor para **Imagem** é `latest` automaticamente preenchido com a versão de imagem se tiver começado a partir da página para a definição de imagem.
1. Para **Tamanho,** escolha um tamanho VM na lista de tamanhos disponíveis e, em seguida, escolha **Select**.
1. Na **conta Do Administrador**, se a fonte VM foi generalizada, introduza o seu nome de **utilizador** e a chave **pública SSH**. Se a fonte VM foi especializada, estas opções serão acinzentadas porque a informação da fonte VM é utilizada.
1. Se pretender permitir o acesso remoto ao VM, sob **portas de entrada pública,** escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** a partir da queda. Se não quiser permitir o acesso remoto ao VM, deixe **nenhum** selecionado para **portas de entrada pública**.
1. Quando terminar, selecione o botão **Rever + criar** na parte inferior da página.
1. Depois do VM passar a validação, selecione **Criar** na parte inferior da página para iniciar a implementação.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

Se pretender eliminar recursos individuais, tem de os eliminar por ordem inversa. Por exemplo, para eliminar uma definição de imagem, é necessário eliminar todas as versões de imagem criadas a partir dessa imagem.

## <a name="next-steps"></a>Passos seguintes

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)

