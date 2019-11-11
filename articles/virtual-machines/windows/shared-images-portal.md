---
title: Criar imagens compartilhadas da máquina virtual do Azure para Windows usando o portal | Microsoft Docs
description: Saiba como usar portal do Azure para criar e compartilhar imagens de máquinas virtuais.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 8dac15a39355f3faf749327431423629fa6ce09b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903660"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Criar uma galeria de imagens compartilhada usando o portal do Azure

Uma [Galeria de imagens compartilhadas](shared-image-galleries.md) simplifica o compartilhamento de imagens personalizadas em sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para inicializar tarefas de implantação, como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações de so. 

A Galeria de imagens compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas em sua organização, dentro ou entre regiões, dentro de um locatário do AAD. Escolha quais imagens você deseja compartilhar, em quais regiões você deseja disponibilizá-las e com quem você deseja compartilhá-las. Você pode criar várias galerias para que seja possível agrupar logicamente imagens compartilhadas. 

A galeria é um recurso de nível superior que fornece RBAC (controle de acesso baseado em função) completo. As imagens podem ter controle de versão e você pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com imagens gerenciadas.

O recurso da Galeria de imagens compartilhadas tem vários tipos de recursos. Usaremos ou compilaremos esses artigos neste artigo:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerenciada** | Uma imagem básica que pode ser usada sozinha ou usada para criar uma **versão de imagem** em uma galeria de imagens. As imagens gerenciadas são criadas a partir de VMs [generalizadas](shared-image-galleries.md#generalized-and-specialized-images) . Uma imagem gerenciada é um tipo especial de VHD que pode ser usado para fazer várias VMs e agora pode ser usado para criar versões de imagens compartilhadas. |
| **Instantânea** | Uma cópia de um VHD que pode ser usado para criar uma **versão de imagem**. Os instantâneos podem ser obtidos de uma VM [especializada](shared-image-galleries.md#generalized-and-specialized-images) (uma que não tenha sido generalizada) em seguida, usados sozinhos ou com instantâneos de discos de dados, para criar uma versão de imagem especializada.
| **Galeria de imagens** | Como o Azure Marketplace, uma **Galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas em uma galeria e contêm informações sobre a imagem e os requisitos para usá-la em sua organização. Você pode incluir informações como se a imagem é generalizada ou especializada, o sistema operacional, os requisitos mínimos e máximos de memória e notas de versão. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão de imagem** é o que você usa para criar uma VM ao usar uma galeria. Você pode ter várias versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão de imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. As versões de imagem podem ser usadas várias vezes. |

<br>


> [!IMPORTANT]
> As imagens especializadas estão atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitações de visualização conhecidas** As VMs só podem ser criadas a partir de imagens especializadas usando o portal ou a API. O não é um suporte de CLI ou PowerShell para a versão prévia.

## <a name="before-you-begin"></a>Antes de começar

Para concluir o exemplo neste artigo, você deve ter uma imagem gerenciada existente de uma VM generalizada ou um instantâneo de uma VM especializada. Você pode seguir [o tutorial: criar uma imagem personalizada de uma VM do Azure com Azure PowerShell](tutorial-custom-images.md) para criar uma imagem gerenciada ou [criar um instantâneo](snapshot-copy-managed-disk.md) para uma VM especializada. Para imagens gerenciadas e instantâneos, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar com este artigo, substitua os nomes do grupo de recursos e da VM quando necessário.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Criar VMs

Agora você pode criar uma ou mais novas VMs. Este exemplo cria uma VM chamada *myVM*, no *MyResource*, no datacenter *leste dos EUA* .

1. Vá para a definição de imagem. Você pode usar o filtro de recursos para mostrar todas as definições de imagem disponíveis.
1. Na página da definição de imagem, selecione **criar VM** no menu na parte superior da página.
1. Para **grupo de recursos**, selecione **criar novo** e digite grupo de *recursos* para o nome.
1. Em **nome da máquina virtual**, digite *myVM*.
1. Para **região**, selecione *leste dos EUA*.
1. Para **as opções de disponibilidade**, deixe o padrão de *nenhuma redundância de infraestrutura necessária*.
1. O valor da **imagem** será preenchido automaticamente com a versão da imagem `latest` se você tiver iniciado na página para a definição da imagem.
1. Para **tamanho**, escolha um tamanho de VM na lista de tamanhos disponíveis e escolha **selecionar**.
1. Em **conta de administrador**, se a imagem tiver sido generalizada, você precisará fornecer um nome de usuário, como *azureuser* e uma senha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Se a imagem for especializada, os campos de nome de usuário e senha ficarão esmaecidos porque o nome de usuário e a senha da VM de origem são usados.
1. Se você quiser permitir o acesso remoto à VM, em **portas de entrada públicas**, escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** na lista suspensa. Se você não quiser permitir o acesso remoto à VM, deixe **nenhuma** selecionada para **portas de entrada públicas**.
1. Quando tiver terminado, selecione o botão **revisar + criar** na parte inferior da página.
1. Depois que a VM passar na validação, selecione **criar** na parte inferior da página para iniciar a implantação.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

Se você quiser excluir recursos individuais, será necessário excluí-los na ordem inversa. Por exemplo, para excluir uma definição de imagem, você precisa excluir todas as versões de imagem criadas por meio dessa imagem.

## <a name="next-steps"></a>Passos seguintes

Você também pode criar recursos da Galeria de imagens compartilhadas usando modelos. Há vários modelos de início rápido do Azure disponíveis: 

- [Criar uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem em uma galeria de imagens compartilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem em uma galeria de imagens compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM com base na versão da imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para obter mais informações sobre galerias de imagens compartilhadas, consulte a [visão geral](shared-image-galleries.md). Se você tiver problemas, consulte a [solução de problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).

