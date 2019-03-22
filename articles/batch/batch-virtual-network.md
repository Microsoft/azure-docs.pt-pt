---
title: Aprovisionar um conjunto numa rede virtual - Azure Batch | Documentos da Microsoft
description: Como criar um conjunto do Batch numa rede virtual do Azure, para que nós de computação podem comunicar de forma segura com outras VMs na rede, tais como um servidor de ficheiros.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 5d725cdac93ebdb39437c0b9bcb619488fade0f7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090666"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Criar um conjunto do Batch do Azure numa rede virtual

Quando cria um conjunto do Azure Batch, pode aprovisionar o conjunto numa sub-rede de uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) (VNet) que especificou. Este artigo explica como configurar um conjunto do Batch numa VNet. 

## <a name="why-use-a-vnet"></a>Porquê utilizar uma VNet?

Um conjunto do Azure Batch tem definições para permitir que nós de computação para comunicar entre si - por exemplo, para executar tarefas de várias instâncias. Estas definições não necessitam de uma VNet separada. No entanto, por predefinição, os nós não consegue comunicar com máquinas virtuais que não fazem parte do conjunto do Batch, como um servidor de licenças ou um servidor de ficheiros. Para permitir que nós de computação do conjunto comunicar de forma segura com outras máquinas virtuais, ou com uma rede no local, pode aprovisionar o conjunto numa sub-rede de uma VNet do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* **Autenticação**. Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md). 

* **Uma VNet do Azure**. Consulte a secção seguinte para requisitos de VNet e a configuração. Para preparar uma VNet com uma ou mais sub-redes com antecedência, pode utilizar o portal do Azure, Azure PowerShell, a Interface de linha de comandos (CLI do Azure) ou outros métodos.  
  * Para criar uma VNet baseada no Azure Resource Manager, veja [criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Uma VNet baseada no Resource Manager é recomendada para novas implementações e só é suportada em conjuntos na configuração da Máquina Virtual.
  * Para criar uma VNet clássica, veja [criar uma rede virtual (clássica) com várias sub-redes](../virtual-network/create-virtual-network-classic.md). Uma rede virtual clássica só é suportada em conjuntos na configuração do serviços em nuvem.

## <a name="vnet-requirements"></a>Requisitos de VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Criar um conjunto com uma VNet no portal

Depois de criar a VNet e atribuído uma sub-rede para o mesmo, pode criar um conjunto do Batch com essa VNet. Siga estes passos para criar um conjunto a partir do portal do Azure: 

1. No portal do Azure, navegue para a sua conta do Batch. Esta conta tem de ser na mesma subscrição e região que o grupo de recursos que contém a VNet que pretende utilizar. 
2. Na **definições** janela à esquerda, selecione a **conjuntos** item de menu.
3. Na **agrupamentos** janela, selecione a **Add** comando.
4. No **adicionar conjunto** janela, selecione a opção que pretende utilizar a partir do **tipo de imagem** lista pendente. 
5. Selecione o correto **Editor/oferta/Sku** para sua imagem personalizada.
6. Especifique as restantes definições, incluindo o **tamanho de nó**, **nós dedicados de destino**, e **nós de prioridade de baixa**, bem como qualquer pretendido definições opcionais.
7. Na **rede Virtual**, selecione a rede virtual e a sub-rede que pretende utilizar.
  
   ![Adicionar conjunto com a rede virtual](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo utilizador para o túnel forçado

Poderá ter requisitos na sua organização para tráfego de redirecionamento (força) da Internet da sub-rede de volta para a sua localização no local para inspeção e registo. Pode ativar o túnel forçado para as sub-redes na sua VNet. 

Para garantir que os nós de computação do Azure Batch pool funcionam numa VNet que tem o ativada de túnel forçado, tem de adicionar o seguinte procedimento [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) para essa sub-rede:

* O serviço Batch precisa se comunicar connosco de computação do conjunto para o agendamento de tarefas. Para permitir esta comunicação, adicione uma rota definida pelo utilizador para cada endereço IP utilizado pelo serviço Batch na região onde existe a sua conta do Batch. Para obter a lista de endereços IP do serviço do Batch, contacte o suporte do Azure.

* Certifique-se de que o tráfego de saída para o armazenamento do Azure (especificamente, os URLs do formulário `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, e `<account>.blob.core.windows.net`) não está bloqueado através de seu dispositivo de rede no local.

Quando adiciona uma rota definida pelo utilizador, definir a rota para cada prefixo de endereço IP do Batch relacionado e defina **tipo de próximo salto** ao **Internet**. Veja o seguinte exemplo:

![Rota definida pelo utilizador](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral aprofundada do Batch, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md).
- Para obter mais informações sobre como criar uma rota definida pelo utilizador, consulte [criar uma rota definida pelo utilizador - portal do Azure](../virtual-network/tutorial-create-route-table-portal.md).
