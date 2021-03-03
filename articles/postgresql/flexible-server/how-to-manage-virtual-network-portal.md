---
title: Gerir redes virtuais - Portal Azure - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Criar e gerir redes virtuais para Azure Database for PostgreSQL - Servidor Flexível utilizando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 746f15d2d712f4b571d3f27e3535c69f5f4f9732
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732773"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Criar e gerir redes virtuais para Azure Database for PostgreSQL - Servidor Flexível utilizando o portal Azure

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A azure Database for PostgreSQL - Flexible Server suporta dois tipos de métodos de conectividade de rede mutuamente exclusivos para se conectar ao seu servidor flexível. As duas opções são:

* Acesso público (endereços IP permitidos)
* Acesso privado (Integração de VNet)

Neste artigo, vamos focar-nos na criação de servidor PostgreSQL com **acesso Privado (VNet Integration)** utilizando o portal Azure. Com acesso privado (Integração VNet), pode implantar o seu servidor flexível na sua própria [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As Redes Virtuais Azure fornecem comunicação de rede privada e segura. Com acesso privado, as ligações ao servidor PostgreSQL estão restritas à sua rede virtual. Para saber mais sobre o assunto, consulte o [acesso privado (VNet Integration)](./concepts-networking.md#private-access-vnet-integration).

Pode implementar o seu servidor flexível numa sub-rede e rede virtual durante a criação do servidor. Após o servidor flexível ser implementado, não poderá movê-lo para outra rede virtual, sub-rede ou para *Acesso público (endereços IP permitidos)* .

## <a name="prerequisites"></a>Pré-requisitos
Para criar um servidor flexível numa rede virtual, é necessário:
- Uma [Rede Virtual](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e subscrição que o seu servidor flexível.

-  Para [delegar uma sub-rede](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) para **Microsoft.DBforPostgreSQL/flexibleServers**. Esta delegação significa que apenas a Base de Dados Azure para servidores flexíveis postgresQL pode utilizar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.
-  Adicione `Microsoft.Storage` ao ponto final de serviço para a sub-rede delegada em servidores flexíveis. Isto é feito executando os seguintes passos:
     1. Vá à sua página de rede virtual.
     2. Selecione o VNET no qual está a planear implantar o seu servidor flexível.
     3. Escolha a sub-rede que está delegada para servidor flexível.
     4. No ecrã pull-out, no **ponto final de Serviço,** escolha `Microsoft.storage` a partir do drop-down.
     5. Guarde as alterações.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Criar base de dados Azure para PostgreSQL - Servidor Flexível numa rede virtual já existente

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.
2. Selecione **Base de Dados**  >  **Azure Databases para PostgreSQL**. Também pode inserir **PostgreSQL** na caixa de pesquisa para encontrar o serviço.
3. Selecione **o servidor flexível** como opção de implementação.
4. Preencha o formulário **Básico.**
5. Vá ao **separador 'Rede'** para configurar a forma como pretende ligar-se ao seu servidor.
6. No **método conectividade,** selecione **Acesso Privado (Integração VNet)**. Vá à **Rede Virtual** e selecione a *rede virtual* já existente e *a Subnet* criada como parte dos pré-requisitos acima.
7. Selecione **Review + crie** para rever a configuração flexível do servidor.
8. Selecione **Criar** para aprovisionar o servidor. O provisionamento pode levar alguns minutos.

>[!Note]
> Depois de o servidor flexível ser implantado numa rede virtual e numa sub-rede, não é possível movê-lo para acesso público (endereços IP permitidos).
## <a name="next-steps"></a>Passos seguintes
- [Criar e gerir a Base de Dados Azure para postgreSQL - rede virtual de servidor flexível utilizando O Azure CLI](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre [networking em Azure Database for PostgreSQL - Servidor Flexível](./concepts-networking.md)
- Conheça mais sobre [a Base de Dados Azure para postgreSQL - Rede virtual de servidor flexível](./concepts-networking.md#private-access-vnet-integration).