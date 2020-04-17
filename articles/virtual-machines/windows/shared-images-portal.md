---
title: Criar uma Galeria de Imagem Partilhada Azure usando o portal
description: Aprenda a usar o portal Azure para criar e partilhar imagens de máquinas virtuais.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 83cdae95d43884647e257cbf1808222a542a212e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458101"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Criar uma Galeria de Imagem Partilhada Azure usando o portal

Uma [Galeria de Imagem Partilhada](shared-image-galleries.md) simplifica a partilha de imagens personalizadas em toda a sua organização. As imagens personalizadas são como imagens do marketplace, mas são criadas por si. Imagens personalizadas podem ser usadas para tarefas de implementação de botas como aplicações de pré-carregamento, configurações de aplicações e outras configurações de SO. 

A Galeria de Imagem Partilhada permite-lhe partilhar as suas imagens VM personalizadas com outras da sua organização, dentro ou em todas as regiões, dentro de um inquilino da AAD. Escolha quais as imagens que pretende partilhar, quais as regiões em que as quer disponibilizar e com quem as quer partilhar. Você pode criar várias galerias para que você possa logicamente agrupar imagens partilhadas. 

A galeria é um recurso de alto nível que fornece um controlo completo de acesso baseado em papéis (RBAC). As imagens podem ser versonizadas e pode optar por replicar cada versão de imagem para um conjunto diferente de regiões do Azure. A galeria só funciona com Imagens Geridas.

A funcionalidade Da Galeria de Imagem Partilhada tem vários tipos de recursos. Vamos usar ou construir estes neste artigo:

| Recurso | Descrição|
|----------|------------|
| **Imagem gerida** | Uma imagem básica que pode ser usada sozinha ou usada para criar uma versão de **imagem** numa galeria de imagens. As imagens geridas são criadas a partir de VMs [generalizados.](shared-image-galleries.md#generalized-and-specialized-images) Uma imagem gerida é um tipo especial de VHD que pode ser usado para fazer vários VMs e agora pode ser usado para criar versões de imagem partilhada. |
| **Instantâneo** | Uma cópia de um VHD que pode ser usado para fazer uma versão de **imagem**. Os instantâneos podem ser retirados de um VM [especializado](shared-image-galleries.md#generalized-and-specialized-images) (que não foi generalizado) e depois utilizados sozinhos ou com instantâneos de discos de dados, para criar uma versão de imagem especializada.
| **Galeria de imagens** | Tal como o Azure Marketplace, uma galeria de **imagens** é um repositório para gerir e partilhar imagens, mas controla quem tem acesso. |
| **Definição de imagem** | As imagens são definidas dentro de uma galeria e transportam informações sobre a imagem e requisitos para a sua utilização dentro da sua organização. Pode incluir informações como se a imagem é generalizada ou especializada, o sistema operativo, requisitos mínimos e máximos de memória e notas de lançamento. É uma definição de um tipo de imagem. |
| **Versão de imagem** | Uma **versão de imagem** é o que se usa para criar um VM quando se utiliza uma galeria. Pode ter várias versões de uma imagem necessária para o seu ambiente. Como uma imagem gerida, quando se usa uma versão de **imagem** para criar um VM, a versão de imagem é usada para criar novos discos para o VM. As versões de imagem podem ser usadas várias vezes. |

<br>


> [!IMPORTANT]
> Imagens especializadas estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .
>
> **Limitações de pré-visualização conhecidas** Os VMs só podem ser criados a partir de imagens especializadas utilizando o portal ou API. O suporte não é CLI ou PowerShell para a pré-visualização.

## <a name="before-you-begin"></a>Antes de começar

Para completar o exemplo neste artigo, deve ter uma imagem gerida existente de um VM generalizado, ou uma imagem de um VM especializado. Pode seguir o [Tutorial: Criar uma imagem personalizada de um Azure VM com A Azure PowerShell](tutorial-custom-images.md) para criar uma imagem gerida, ou [criar um instantâneo](snapshot-copy-managed-disk.md) para um VM especializado. Tanto para imagens geridas como para imagens, o tamanho do disco de dados não pode ser superior a 1 TB.

Ao trabalhar através deste artigo, substitua o grupo de recursos e os nomes VM sempre que necessário.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Criar VMs

Agora pode criar um ou mais novos VMs. Este exemplo cria um VM chamado *myVM*, no *myResourceGroup,* no centro de dados *dos EUA Oriental.*

1. Vá para a sua definição de imagem. Pode utilizar o filtro de recursos para mostrar todas as definições de imagem disponíveis.
1. Na página para a sua definição de imagem, selecione **Criar VM** a partir do menu na parte superior da página.
1. Para **o grupo Recursos,** selecione **Criar novo** e digitar *myResourceGroup* para o nome.
1. No nome da **máquina virtual,** digite *myVM*.
1. Para **a Região,** selecione *East US*.
1. Para **opções de disponibilidade,** deixe o padrão de não ser necessário um despedimento de *infraestrutura*.
1. O valor para **Imagem** é `latest` automaticamente preenchido com a versão de imagem se tiver começado a partir da página para a definição de imagem.
1. Para **Tamanho,** escolha um tamanho VM na lista de tamanhos disponíveis e, em seguida, escolha **Select**.
1. Na **conta do Administrador,** se a imagem foi generalizada, é necessário fornecer um nome de utilizador, como *o azureuser* e uma palavra-passe. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm). Se a sua imagem foi especializada, o nome de utilizador e os campos de palavra-passe ficarão acinzentados porque o nome de utilizador e a palavra-passe para a fonte VM são utilizados.
1. Se pretender permitir o acesso remoto ao VM, sob **portas de entrada pública,** escolha **permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** a partir da queda. Se não quiser permitir o acesso remoto ao VM, deixe **nenhum** selecionado para **portas de entrada pública**.
1. Quando terminar, selecione o botão **Rever + criar** na parte inferior da página.
1. Depois do VM passar a validação, selecione **Criar** na parte inferior da página para iniciar a implementação.


## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, pode eliminar o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a máquina virtual, selecione **Eliminar** e confirme o nome do grupo de recursos a eliminar.

Se pretender eliminar recursos individuais, tem de os eliminar por ordem inversa. Por exemplo, para eliminar uma definição de imagem, é necessário eliminar todas as versões de imagem criadas a partir dessa imagem.

## <a name="next-steps"></a>Passos seguintes

Também pode criar recurso da Galeria de Imagem Partilhada utilizando modelos. Existem vários modelos Azure Quickstart disponíveis: 

- [Criar uma Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma Definição de Imagem numa Galeria de Imagem Partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem numa galeria de imagem partilhada](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar um VM a partir da versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Para mais informações sobre galerias de imagem partilhadas, consulte a [visão geral.](shared-image-galleries.md) Se tiver problemas, veja as galerias de [imagens partilhadas de Troubleshooting.](troubleshooting-shared-images.md)

