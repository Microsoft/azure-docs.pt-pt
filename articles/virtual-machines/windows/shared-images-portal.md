---
title: Criar uma Galeria de Imagens Partilhadas Azure usando o portal
description: Aprenda a usar o portal Azure para criar e partilhar imagens de máquinas virtuais.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: 25cd75035a814fd718cc1101e6575f78c50f105e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879702"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Criar uma Galeria de Imagens Partilhadas Azure usando o portal

Uma [Galeria de Imagens Partilhadas](../shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. As imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SISTEMA. 

A Galeria de Imagens Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras pessoas da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, em que regiões quer disponibilizá-las e com quem quer partilhá-las. Pode criar várias galerias para que possa, logicamente, agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo de acesso baseado em funções Azure (Azure RBAC). As imagens podem ser versadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria funciona apenas com Imagens Geridas.

A funcionalidade Image Gallery partilhada tem vários tipos de recursos. Vamos usá-los ou construí-los neste artigo:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


Ao trabalhar neste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Criar VMs

Agora pode criar um ou mais VMs novos. Este exemplo cria um VM chamado *myVM,* no *myResourceGroup,* no centro de dados *dos EUA.*

1. Vá para a sua definição de imagem. Pode utilizar o filtro de recursos para mostrar todas as definições de imagem disponíveis.
1. Na página para a definição de imagem, **selecione Criar VM** a partir do menu no topo da página.
1. Para **o grupo de recursos,** selecione Criar **novo** e digite *myResourceGroup* para o nome.
1. Em **nome da máquina virtual,** escreva *myVM*.
1. Para **a Região**, selecione *East US*.
1. Para **opções de disponibilidade,** deixe o padrão de Não necessidade de *redundância de infraestrutura*.
1. O valor para **Imagem** é automaticamente preenchido com a `latest` versão de imagem se você começar a partir da página para a definição de imagem.
1. Para **tamanho**, escolha um tamanho VM da lista de tamanhos disponíveis e, em seguida, escolha **Selecione**.
1. Na **conta administrador**, se a imagem foi generalizada, é necessário fornecer um nome de utilizador, como *azureuser* e uma palavra-passe. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Se a sua imagem foi especializada, os campos de nome de utilizador e palavra-passe ficarão acinzentados porque o nome de utilizador e a palavra-passe para a origem VM são utilizados.
1. Se pretender permitir o acesso remoto ao VM, **em portas de entrada pública,** escolha Localizar portas **selecionadas** e, em seguida, selecionar **RDP (3389)** a partir do drop-down. Se não pretender permitir o acesso remoto ao VM, deixe **nenhum** selecionado para **portas de entrada pública**.
1. Quando terminar, selecione o botão **'Rever +'** na parte inferior da página.
1. Depois da validação de passes VM, **selecione Criar** na parte inferior da página para iniciar a implementação.


## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

Se pretender eliminar recursos individuais, tem de os eliminar por ordem inversa. Por exemplo, para eliminar uma definição de imagem, é necessário eliminar todas as versões de imagem criadas a partir dessa imagem.

## <a name="next-steps"></a>Próximos passos

Também pode criar recursos da Galeria de Imagens Partilhadas utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar um Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma Versão de Imagem num Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir de uma Versão de Imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [Visão Geral.](../shared-image-galleries.md) Se tiver problemas, consulte [as galerias de imagem partilhadas .](../troubleshooting-shared-images.md)