---
title: Gerir redes virtuais - Portal Azure - Base de Dados Azure para MySQL - Servidor Flexível
description: Criar e gerir redes virtuais para Azure Database for MySQL - Servidor Flexível utilizando o portal Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4906ce9f562910f0a087cd25167457ec1fb301ec
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110002"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Criar e gerir redes virtuais para Azure Database for MySQL - Servidor Flexível utilizando o portal Azure

> [!IMPORTANT]
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

O Servidor Flexível da Base de Dados do Azure para MySQL suporta dois tipos de métodos de conectividade de rede mutuamente exclusivos para ligar ao seu servidor flexível. As duas opções são:

- Acesso público (endereços IP permitidos)
- Acesso privado (Integração de VNet)

Neste artigo, vamos focar-nos na criação do servidor MySQL com **acesso privado (VNet Integration)** utilizando o portal Azure. Com acesso privado (Integração VNet), pode implantar o seu servidor flexível na sua própria [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As Redes Virtuais Azure fornecem comunicação de rede privada e segura. Com acesso privado, as ligações ao servidor MySQL estão restritas à sua rede virtual. Para saber mais sobre o assunto, consulte o [acesso privado (VNet Integration)](./concepts-networking.md#private-access-vnet-integration).

Pode implementar o seu servidor flexível numa sub-rede e rede virtual durante a criação do servidor. Após o servidor flexível ser implementado, não poderá movê-lo para outra rede virtual, sub-rede ou para *Acesso público (endereços IP permitidos)* .

## <a name="prerequisites"></a>Pré-requisitos
Para criar um servidor flexível numa rede virtual, é necessário:
- Uma [Rede Virtual](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e subscrição que o seu servidor flexível.

-  Para [delegar uma sub-rede](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) para **Microsoft.DBforMySQL/flexibleServers**. Esta delegação significa que apenas os Servidores Flexíveis da Base de Dados do Azure para MySQL podem utilizar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Criar base de dados Azure para o MySQL Flexible Server numa rede virtual já existente

1. **Selecione Criar um recurso** (+) no canto superior esquerdo do portal.
2. Selecione **Base de Dados**  >  **Azure Databases para o MySQL**. Também pode inserir **o MySQL** na caixa de pesquisa para encontrar o serviço.
3. Selecione **o servidor flexível** como opção de implementação.
4. Preencha o formulário **Básico.**
5. Vá ao **separador 'Rede'** para configurar a forma como pretende ligar-se ao seu servidor.
6. No **método conectividade,** selecione **Acesso Privado (Integração VNet)**. Vá à **Rede Virtual** e selecione a *rede virtual* já existente e *a Subnet* criada como parte dos pré-requisitos acima.
7. Selecione **Review + crie** para rever a configuração flexível do servidor.
8. Selecione **Criar** para aprovisionar o servidor. O provisionamento pode levar alguns minutos.

>[!Note]
> Depois de o servidor flexível ser implantado numa rede virtual e numa sub-rede, não é possível movê-lo para acesso público (endereços IP permitidos).

## <a name="next-steps"></a>Passos seguintes
- [Crie e gere a base de dados Azure para a rede virtual MySQL Flexible Server utilizando o Azure CLI](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre [networking em Azure Database para MySQL Flexible Server](./concepts-networking.md)
- Conheça mais sobre [a Base de Dados Azure para a rede virtual MySQL Flexible Server](./concepts-networking.md#private-access-vnet-integration).
