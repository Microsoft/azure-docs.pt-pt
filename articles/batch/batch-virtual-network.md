---
title: Provisionamento de uma piscina em uma rede virtual
description: Como criar um pool de Lote numa rede virtual Azure para que os nós de computação possam comunicar de forma segura com outros VMs na rede, como um servidor de ficheiros.
ms.topic: how-to
ms.date: 01/13/2021
ms.custom: seodec18
ms.openlocfilehash: bedb0cbd826e2891560320ed11c0ba28e07f8e88
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165761"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Criar uma piscina Azure Batch numa rede virtual

Quando criar uma piscina Azure Batch, pode providenciar a piscina numa sub-rede de uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md) (VNet) que especifique. Este artigo explica como configurar uma piscina de lote num VNet.

## <a name="why-use-a-vnet"></a>Porquê usar um VNet?

Os nós computacional numa piscina podem comunicar uns com os outros, tais como executar tarefas de várias instâncias, sem necessitar de um VNet separado. No entanto, por padrão, os nós numa piscina não podem comunicar com máquinas virtuais que estão fora da piscina, como servidores de licença ou servidores de ficheiros.

Para permitir que os nós computacional se comuniquem de forma segura com outras máquinas virtuais, ou com uma rede no local, pode providenciar a piscina numa sub-rede de um Azure VNet.

## <a name="prerequisites"></a>Pré-requisitos

- **Autenticação.** Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).

- **Um Azure VNet.** Consulte a seguinte secção para os requisitos e configuração do VNet. Para preparar um VNet com uma ou mais sub-redes com antecedência, pode utilizar o portal Azure PowerShell, a Interface Azure Command-Line (CLI) ou outros métodos.
  - Para criar um VNet baseado em Recursos Azure, consulte [criar uma rede virtual.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Um VNet baseado em Gestor de Recursos é recomendado para novas implementações, e é suportado apenas em piscinas que usam configuração de máquina virtual.
  - Para criar um VNet clássico, consulte [Criar uma rede virtual (clássica) com várias sub-redes.](/previous-versions/azure/virtual-network/create-virtual-network-classic) Um VNet clássico é suportado apenas em piscinas que usam configuração de serviços cloud.

## <a name="vnet-requirements"></a>Requisitos de VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Criar uma piscina com um VNet no portal Azure

Uma vez criado o seu VNet e atribuído uma sub-rede, pode criar uma piscina de Lote com esse VNet. Siga estes passos para criar uma piscina a partir do portal Azure: 

1. No portal do Azure, navegue para a sua conta do Batch. Esta conta deve estar na mesma subscrição e região que o grupo de recursos que contém o VNet que pretende utilizar.
2. Na janela **Definições** à esquerda, selecione o item do menu **Pools.**
3. Na janela **Pools,** **selecione Adicionar**.
4. Na janela **Add Pool,** selecione a opção que pretende utilizar a partir do **dropdown do Tipo** de Imagem.
5. Selecione o **Editor/Oferta/Sku** correto para a sua imagem personalizada.
6. Especifique as definições restantes necessárias, incluindo o tamanho do **nó,** **os nós dedicados ao alvo** e os nós de baixa **prioridade,** bem como quaisquer configurações opcionais desejadas.
7. Na **Rede Virtual,** selecione a rede virtual e a sub-rede que pretende utilizar.

   ![Adicionar piscina com rede virtual](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Vias definidas pelo utilizador para túneis forçados

Você pode ter requisitos na sua organização para redirecionar (forçar) o tráfego ligado à internet da sub-rede de volta para o seu local de inspeção e registo. Além disso, pode ter ativado um túnel forçado para as sub-redes do seu VNet.

Para garantir que os nós da sua piscina funcionam num VNet que tenha ativado o túnel forçado, deve adicionar as seguintes [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) (UDR) para essa sub-rede:

- O serviço Batch precisa de comunicar com nós para agendar tarefas. Para ativar esta comunicação, adicione um UDR para cada endereço IP utilizado pelo serviço Batch na região onde existe a sua conta Batch. Para obter a lista de endereços IP do serviço Batch, consulte [as etiquetas de serviço no local.](../virtual-network/service-tags-overview.md)

- Certifique-se de que o tráfego de saída para a Azure Storage (especificamente, URLs do formulário `<account>.table.core.windows.net` , e ) não está bloqueado pela sua rede no `<account>.queue.core.windows.net` `<account>.blob.core.windows.net` local.

Quando adicionar um UDR, defina a rota para cada prefixo de endereço IP do lote relacionado e defina **o tipo de próximo lúpulo** para a **Internet**.

![Rota definida pelo utilizador](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Os endereços IP do serviço Batch podem ser alterados ao longo do tempo. Para evitar interrupções devido a uma alteração de endereço IP, crie um processo para atualizar automaticamente os endereços IP do serviço Batch e mantê-los atualizados na sua tabela de rotas.

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Saiba como [criar uma rota definida pelo utilizador no portal Azure](../virtual-network/tutorial-create-route-table-portal.md).
