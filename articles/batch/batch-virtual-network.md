---
title: Provisionar um pool em uma rede virtual – lote do Azure | Microsoft Docs
description: Como criar um pool do lote em uma rede virtual do Azure para que os nós de computação possam se comunicar com segurança com outras VMs na rede, como um servidor de arquivos.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 78f29bacaadac5f01e4a8dd26bf03b2bda84f2bf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577569"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Criar um pool do lote do Azure em uma rede virtual

Ao criar um pool do lote do Azure, você pode provisionar o pool em uma sub-rede de uma VNet ( [rede virtual](../virtual-network/virtual-networks-overview.md) ) do Azure que você especificar. Este artigo explica como configurar um pool do lote em uma VNet. 

## <a name="why-use-a-vnet"></a>Por que usar uma VNet?

Um pool do lote do Azure tem configurações para permitir que nós de computação se comuniquem entre si-por exemplo, para executar tarefas de várias instâncias. Essas configurações não exigem uma VNet separada. No entanto, por padrão, os nós não podem se comunicar com máquinas virtuais que não fazem parte do pool do lote, como um servidor de licença ou um servidor de arquivos. Para permitir que os nós de computação do pool se comuniquem de forma segura com outras máquinas virtuais, ou com uma rede local, você pode provisionar o pool em uma sub-rede de uma VNet do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* **Autenticação**. Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md). 

* **Uma VNet do Azure**. Consulte a seção a seguir para obter os requisitos e a configuração da VNet. Para preparar uma VNet com uma ou mais sub-redes com antecedência, você pode usar o portal do Azure, Azure PowerShell, a CLI (interface de linha de comando) do Azure ou outros métodos.  
  * Para criar uma VNet baseada em Azure Resource Manager, consulte [criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Uma VNet baseada no Resource Manager é recomendada para novas implantações e tem suporte apenas em pools na configuração da máquina virtual.
  * Para criar uma VNet clássica, consulte [criar uma rede virtual (clássica) com várias sub-redes](../virtual-network/create-virtual-network-classic.md). Há suporte para uma VNet clássica apenas em pools na configuração de serviços de nuvem.

## <a name="vnet-requirements"></a>Requisitos de VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Criar um pool com uma VNet no portal

Depois de criar sua VNet e atribuir uma sub-rede a ela, você poderá criar um pool do lote com essa VNet. Siga estas etapas para criar um pool do portal do Azure: 

1. No portal do Azure, navegue para a sua conta do Batch. Essa conta deve estar na mesma assinatura e região que o grupo de recursos que contém a VNet que você pretende usar. 
2. Na janela **configurações** à esquerda, selecione o item de menu **pools** .
3. Na janela **pools** , selecione o comando **Adicionar** .
4. Na janela **Adicionar pool** , selecione a opção que você pretende usar na lista suspensa **tipo de imagem** . 
5. Selecione o **Publicador/oferta/SKU** correto para sua imagem personalizada.
6. Especifique as configurações necessárias restantes, incluindo o **tamanho do nó**, os **nós dedicados de destino**e os **nós de baixa prioridade**, bem como as configurações opcionais desejadas.
7. Em **rede virtual**, selecione a rede virtual e a sub-rede que você deseja usar.
  
   ![Adicionar pool com rede virtual](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo usuário para túnel forçado

Você pode ter requisitos em sua organização para redirecionar (forçar) o tráfego vinculado à Internet da sub-rede de volta para seu local para inspeção e registro em log. Você pode ter habilitado o túnel forçado para as sub-redes em sua VNet. 

Para garantir que os nós de computação do pool do lote do Azure funcionem em uma VNet com túnel forçado habilitado, você deve adicionar as seguintes [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) para essa sub-rede:

* O serviço de lote precisa se comunicar com nós de computação de pool para tarefas de agendamento. Para habilitar essa comunicação, adicione uma rota definida pelo usuário para cada endereço IP usado pelo serviço de lote na região em que sua conta do lote existe. Para saber como obter a lista de endereços IP do serviço de lote, consulte [marcas de serviço no local](../virtual-network/service-tags-overview.md)

* Verifique se o tráfego de saída para o armazenamento do Azure (especificamente, as URLs do formato `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`e `<account>.blob.core.windows.net`) não está bloqueado por meio de seu dispositivo de rede local.

Quando você adiciona uma rota definida pelo usuário, defina a rota para cada prefixo de endereço IP do lote relacionado e defina o **tipo do próximo salto** como **Internet**. Veja o seguinte exemplo:

![Rota definida pelo utilizador](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).
- Para saber mais sobre como criar uma rota definida pelo usuário, confira [criar uma portal do Azure de rota definida pelo usuário](../virtual-network/tutorial-create-route-table-portal.md).
