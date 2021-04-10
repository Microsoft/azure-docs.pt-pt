---
title: Criar imagens VM partilhadas do Azure Linux utilizando o portal
description: Aprenda a usar o portal Azure para criar e partilhar imagens de máquinas virtuais Linux.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 90ed91caa1c4b71ae90b86f5b0783a6d5c1c669e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552801"
---
# <a name="create-a-shared-image-gallery-using-the-portal"></a>Criar uma Galeria de Imagens Partilhadas utilizando o portal

Uma [Galeria de Imagens Partilhadas](../shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SISTEMA. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras pessoas da sua organização, dentro ou em regiões, dentro de um inquilino Azure AD. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. Pode criar várias galerias para que possa, logicamente, agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo de acesso baseado em funções Azure (Azure RBAC). As imagens podem ser versadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com Imagens Geridas.

A funcionalidade Image Gallery partilhada tem vários tipos de recursos. Vamos usá-los ou construí-los neste artigo:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>





## <a name="before-you-begin"></a>Antes de começar

Para completar o exemplo neste artigo, você deve ter uma imagem gerida existente de um VM generalizado, ou uma foto de um VM especializado. Pode seguir [tutorial: Criar uma imagem personalizada de um Azure VM com Azure PowerShell](tutorial-custom-images.md) para criar uma imagem gerida, ou [criar um instantâneo](../windows/snapshot-copy-managed-disk.md) para um VM especializado. Tanto para imagens geridas como para instantâneos, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar neste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms"></a>Criar VMs 

Agora pode criar um ou mais VMs novos. Este exemplo cria um VM chamado *myVMfromImage,* no *myResourceGroup* no Centro de Dados *dos EUA.*

1. Vá para a sua definição de imagem. Pode utilizar o filtro de recursos para mostrar todas as definições de imagem disponíveis.
1. Na página para a definição de imagem, **selecione Criar VM** a partir do menu no topo da página.
1. Para **o grupo de recursos,** selecione Criar **novo** e digite *myResourceGroup* para o nome.
1. Em **nome da máquina virtual,** escreva *myVM*.
1. Para **a Região**, selecione *East US*.
1. Para **opções de disponibilidade,** deixe o padrão de Não necessidade de *redundância de infraestrutura*.
1. O valor para **Imagem** é automaticamente preenchido com a `latest` versão de imagem se você começar a partir da página para a definição de imagem.
1. Para **tamanho**, escolha um tamanho VM da lista de tamanhos disponíveis e, em seguida, escolha **Selecione**.
1. Na **conta do Administrador**, se a origem VM foi generalizada, insira o seu nome de **utilizador** e a chave **pública SSH**. Se a origem VM foi especializada, estas opções serão acinzentados porque a informação da fonte VM é utilizada.
1. Se pretender permitir o acesso remoto ao VM, **em portas de entrada pública,** escolha Localizar portas **selecionadas** e, em seguida, selecione **SSH (22)** a partir do drop-down. Se não pretender permitir o acesso remoto ao VM, deixe **nenhum** selecionado para **portas de entrada pública**.
1. Quando terminar, selecione o botão **'Rever +'** na parte inferior da página.
1. Depois da validação de passes VM, **selecione Criar** na parte inferior da página para iniciar a implementação.


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

Se pretender eliminar recursos individuais, tem de os eliminar por ordem inversa. Por exemplo, para eliminar uma definição de imagem, é necessário eliminar todas as versões de imagem criadas a partir dessa imagem.

## <a name="next-steps"></a>Passos seguintes

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](../shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](../troubleshooting-shared-images.md)