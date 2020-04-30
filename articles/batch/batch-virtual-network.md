---
title: Disponibilize uma piscina numa rede virtual - Azure Batch [ Lote Azul ] Microsoft Docs
description: Como criar um pool de Lote numa rede virtual Azure para que os nós de computação possam comunicar de forma segura com outros VMs na rede, como um servidor de ficheiros.
ms.topic: article
ms.date: 04/03/2020
ms.custom: seodec18
ms.openlocfilehash: 616118d5f75f9bfa6d97d89baac9d7ea9186cd5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82111900"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Crie uma piscina de Lote Azure numa rede virtual

Ao criar uma piscina de Lote Azure, pode fornecer a piscina numa subnet de uma [rede virtual Azure](../virtual-network/virtual-networks-overview.md) (VNet) que especifica. Este artigo explica como configurar uma piscina de lote num VNet.

## <a name="why-use-a-vnet"></a>Por que usar um VNet?

Um conjunto de Lotes Azure tem configurações para permitir que nós de computação se comuniquem entre si - por exemplo, executar tarefas de várias instâncias. Estas definições não requerem um VNet separado. No entanto, por padrão, os nós não podem comunicar com máquinas virtuais que não fazem parte do pool do Batch, como um servidor de licença ou um servidor de ficheiros. Para permitir que os nós de computação de piscina saem com segurança com outras máquinas virtuais, ou com uma rede no local, você pode fornecer a piscina em uma subnet de um Azure VNet.

## <a name="prerequisites"></a>Pré-requisitos

* **Autenticação**. Para utilizar uma VNet do Azure, a API do cliente do Batch tem de utilizar a autenticação do Azure Active Directory. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).

* **Um Azure VNet.** Consulte a seguinte secção para obter requisitos e configuração VNet. Para preparar um VNet com uma ou mais subredes com antecedência, pode utilizar o portal Azure, o Azure PowerShell, a Interface de Linha de Comando Azure (CLI) ou outros métodos.
  * Para criar um VNet baseado em Gestor de Recursos Azure, consulte [Criar uma rede virtual](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Um VNet baseado em Gestor de Recursos é recomendado para novas implementações, e é suportado apenas em piscinas na configuração da Máquina Virtual.
  * Para criar um VNet clássico, consulte [Criar uma rede virtual (clássica) com várias subredes.](../virtual-network/create-virtual-network-classic.md) Um VNet clássico é suportado apenas em piscinas na configuração cloud Services.

## <a name="vnet-requirements"></a>Requisitos de VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Crie uma piscina com um VNet no portal

Uma vez criado o seu VNet e atribuído uma sub-rede a ele, pode criar uma piscina de Lote com esse VNet. Siga estes passos para criar uma piscina a partir do portal Azure: 

1. No portal do Azure, navegue para a sua conta do Batch. Esta conta deve estar na mesma subscrição e região que o grupo de recursos que contém o VNet que pretende utilizar.
2. Na janela **Definições** à esquerda, selecione o item do menu **Pools.**
3. Na janela **Pools,** selecione o comando **Adicionar.**
4. Na janela **Add Pool,** selecione a opção que pretende utilizar a partir da queda do **Tipo de Imagem.**
5. Selecione o **Editor/Oferta/Sku** correto para a sua imagem personalizada.
6. Especifique as definições restantes, incluindo o tamanho do **nó,** **os nós dedicados**ao alvo e os nós de **baixa prioridade,** bem como quaisquer configurações opcionais desejadas.
7. Na **Rede Virtual,** selecione a rede virtual e a sub-rede que pretende utilizar.

   ![Adicione piscina com rede virtual](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Rotas definidas pelo utilizador para túneis forçados

Pode ter requisitos na sua organização para redirecionar (força) tráfego ligado à Internet da subnet de volta ao local de instalação para inspeção e exploração madeireira. Pode ter permitido um túnel forçado para as subredes no seu VNet.

Para garantir que os seus nós de computação de piscina Azure Batch funcionam num VNet que tenha forçado a colocação de túneis, deve adicionar as seguintes [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) para essa sub-rede:

* O serviço Batch precisa de comunicar com os nossos nódos de cálculo de piscina para tarefas de agendamento. Para ativar esta comunicação, adicione uma rota definida pelo utilizador para cada endereço IP utilizado pelo serviço 'Lote' na região onde existe a sua conta Batch. Para saber como obter a lista de endereços IP do serviço Batch, consulte [as etiquetas de serviço no local](../virtual-network/service-tags-overview.md). Os endereços IP do serviço de `BatchNodeManagement` lote serão associados com a etiqueta de serviço (ou a variante regional que corresponde à região da sua conta Batch).

* Certifique-se de que o tráfego de saída para `<account>.table.core.windows.net`o `<account>.queue.core.windows.net`Armazenamento `<account>.blob.core.windows.net`Azure (especificamente, URLs do formulário , e ) não está bloqueado através do seu aparelho de rede no local.

Quando adicionar uma rota definida pelo utilizador, defina a rota para cada prefixo de endereço IP do Lote e coloque o **tipo de lúpulo seguinte** na **Internet**. Veja o seguinte exemplo:

![Rota definida pelo utilizador](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Os endereços IP do serviço de lote podem ser alterados ao longo do tempo. Para evitar uma interrupção devido a uma alteração de endereço IP, sugerimos que estabeleça um processo periódico para atualizar automaticamente os endereços IP do serviço de lote e mantê-los atualizados na tabela de rotas.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral aprofundada do Lote, consulte Desenvolver soluções de [computação paralela em larga escala com lote](batch-api-basics.md).
- Para mais informações sobre a criação de uma rota definida pelo utilizador, consulte [Criar uma rota definida pelo utilizador - portal Azure](../virtual-network/tutorial-create-route-table-portal.md).
