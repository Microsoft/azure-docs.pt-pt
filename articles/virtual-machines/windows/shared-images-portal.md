---
title: Criar imagens de Máquina Virtual do Azure partilhadas para o Windows através do portal | Documentos da Microsoft
description: Saiba como utilizar o portal do Azure para criar e partilhar imagens de máquinas virtuais.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 93734045cd06f279f37d7725aa573a59c4ec0be9
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465707"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Criar uma galeria de imagem partilhada com o portal do Azure

R [Galeria de imagens de partilhado](shared-image-galleries.md) simplifica a imagem personalizada de partilha na sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser utilizadas para iniciar tarefas de implantação, como o pré-carregamento de aplicações, configurações de aplicação e outras configurações de SO. 

A Galeria de imagens partilhado permite que Compartilhe suas imagens VM personalizadas com outras pessoas na sua organização, numa ou em regiões, dentro de um inquilino do AAD. Escolha quais imagens que pretende partilhar, quais são as regiões que pretende tornar disponível no mesmos e a quem pretende partilhá-los com. Pode criar várias galerias, para que pode agrupar logicamente Imagens partilhadas. 

A galeria é um recurso de nível superior que fornece controlo de acesso completa baseada em funções (RBAC). Imagens podem ser atualizadas, e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A Galeria só funciona com imagens gerenciadas pelo.

O recurso da Galeria de imagens de partilhado tem vários tipos de recursos. Podemos será a utilizar ou criar neste artigo:

| Resource | Descrição|
|----------|------------|
| **Imagem gerida** | Esta é uma imagem básica que pode ser usada sozinha ou utilizada para criar uma **versão da imagem** na Galeria de imagens. Imagens geridas são criadas a partir de VMs generalizadas. Uma imagem gerida é um tipo especial de VHD que pode ser utilizado para fazer várias VMs e pode agora ser utilizado para criar versões de imagem partilhada. |
| **Galeria de imagens** | Como o Azure Marketplace, um **Galeria de imagens** é um repositório de gerenciamento e compartilhamento de imagens, mas controlar quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportar informações sobre a imagem e os requisitos para utilizá-lo internamente. Isto inclui se a imagem é Windows ou Linux, notas de versão e os requisitos de memória mínimos e máximos. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é o que utiliza para criar uma VM ao utilizar uma galeria. Pode ter várias versões de uma imagem, conforme necessário para o seu ambiente. Como uma imagem gerida, quando utiliza um **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser utilizadas várias vezes. |


## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, tem de ter uma imagem gerida existente. Pode seguir [Tutorial: Criar uma imagem personalizada de uma VM do Azure com o Azure PowerShell](tutorial-custom-images.md) para criar um, se necessário. Se a imagem gerida contém um disco de dados, o tamanho do disco de dados não pode ser mais de 1 TB.

Quando trabalhar com este artigo, substitua o grupo de recursos e a VM nomes onde necessário.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>Criar VMs a partir de uma imagem

Assim que a versão da imagem for concluída, pode criar uma ou mais VMs novas. 

> [!IMPORTANT]
> Não é possível utilizar o portal para implementar uma VM a partir de uma imagem no outro inquilino do azure. Para criar uma VM a partir de uma imagem partilhada entre inquilinos, tem de utilizar o [CLI do Azure](../linux/shared-images.md#create-a-vm) ou [Powershell](shared-images.md#create-vms-from-an-image).

Este exemplo cria uma VM com o nome *myVMfromImage*, na *myResourceGroup* no *E.U.A. Leste* datacenter.

1. Na página para a sua versão de imagem, selecione **Create VM** no menu na parte superior da página.
1. Para **grupo de recursos**, selecione **criar nova** e o tipo *myResourceGroup* para o nome.
1. Na **nome da Máquina Virtual**, tipo *myVM*.
1. Para **região**, selecione *E.U.A. Leste*.
1. Para **opções de disponibilidade**, deixe a predefinição *nenhuma redundância de infraestrutura necessária*.
1. O valor para **imagem** devem ser preenchidos automaticamente se tiver iniciado a partir da página para a versão da imagem.
1. Para **tamanho**, escolha um tamanho de VM a partir da lista de tamanhos disponíveis e, em seguida, clique em "Selecionar".
1. Em **Conta de administrador**, forneça um nome de utilizador, como *utilizadordoazure*, e uma palavra-passe. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Se pretender permitir o acesso remoto à VM, em **portas de entrada públicas**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (. 3389)** na lista suspensa. Se não pretender permitir o acesso remoto à VM, deixe **None** selecionado para **portas de entrada públicas**.
1. Quando tiver terminado, selecione o **rever + criar** na parte inferior da página.
1. Depois da VM passa na validação, selecione **criar** na parte inferior da página para iniciar a implementação.



## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

Se pretender eliminar recursos individuais, terá de eliminá-los na ordem inversa. Por exemplo, para eliminar uma definição de imagem, terá de eliminar todas as versões de imagem criadas a partir dessa imagem.

## <a name="next-steps"></a>Passos Seguintes

Também pode criar recursos de Galeria de imagens de partilhado através de modelos. Vários modelos de início rápido do Azure estão disponíveis: 

- [Criar uma galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre as galerias de imagem de partilhado, consulte a [descrição geral](shared-image-galleries.md). Caso se depare com problemas, consulte [resolução de problemas partilhado galerias de imagem](troubleshooting-shared-images.md).

