---
title: Gerir redes virtuais - Azure CLI - Azure Database for PostgreSQL - Servidor Flexível
description: Criar e gerir redes virtuais para Azure Database for PostgreSQL - Servidor Flexível utilizando o Azure CLI
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: eb22946bb3f0858a545d5b854afe48b2e1e61927
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97109237"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Criar e gerir redes virtuais para Azure Database for PostgreSQL - Servidor Flexível utilizando o Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

A azure Database for PostgreSQL - Flexible Server suporta dois tipos de métodos de conectividade de rede mutuamente exclusivos para se conectar ao seu servidor flexível. As duas opções são:

* Acesso público (endereços IP permitidos)
* Acesso privado (Integração de VNet)

Neste artigo, vamos focar-nos na criação de servidor PostgreSQL com **acesso Privado (VNet Integration)** utilizando o Azure CLI. Com *acesso privado (Integração VNet)*, pode implantar o seu servidor flexível na sua própria [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As Redes Virtuais Azure fornecem comunicação de rede privada e segura. No acesso privado, as ligações ao servidor PostgreSQL estão restritas apenas à sua rede virtual. Para saber mais sobre o assunto, consulte o [acesso privado (VNet Integration)](./concepts-networking.md#private-access-vnet-integration).

Na Base de Dados Azure para PostgreSQL - Servidor Flexível, só é possível implantar o servidor numa rede virtual e numa sub-rede durante a criação do servidor. Depois de o servidor flexível ser implantado numa rede virtual e numa sub-rede, não é possível movê-lo para outra rede virtual, sub-rede ou para *acesso público (endereços IP autorizados)*.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O [Azure Cloud Shell](../../cloud-shell/overview.md) é uma concha interativa gratuita que pode usar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode abrir o Cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e selecione **Enter** para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Terá de iniciar sessão na sua conta utilizando o comando [de login az.](/cli/azure/reference-index#az-login) Note a propriedade **ID,** que se refere ao **ID de subscrição** para a sua conta Azure.

```azurecli-interactive
az login
```

Selecione a subscrição específica sob a sua conta usando o comando [conjunto de conta az.](/cli/azure/account#az-account-set) Tome nota do valor de **ID** da saída de **login az** para usar como valor para o argumento de **subscrição** no comando. Se tiver várias subscrições, escolha a subscrição adequada na qual o recurso deve ser cobrado. Para obter toda a sua subscrição, utilize [a lista de conta az](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>Criar base de dados Azure para PostgreSQL - Servidor Flexível usando CLI
Pode utilizar o `az postgres flexible-server` comando para criar o servidor flexível com acesso privado *(Integração VNet)*. Este comando utiliza o acesso privado (VNet Integration) como método de conectividade padrão. Uma rede virtual e uma sub-rede serão criadas para si se nenhuma for fornecida. Também pode fornecer a rede virtual e sub-rede já existentes usando o id da sub-rede. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Existem várias opções para criar um servidor flexível usando OC, como mostrado nos exemplos abaixo.

>[!Important]
> A utilização deste comando delegará a sub-rede ao **Microsoft.DBforPostgreSQL/flexibleServers**. Esta delegação significa que apenas a Base de Dados Azure para servidores flexíveis postgresQL pode utilizar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.
>
Consulte a documentação de referência do Azure CLI <!--FIXME --> para a lista completa de parâmetros CLI configuráveis. Por exemplo, nos comandos abaixo pode especificar opcionalmente o grupo de recursos.

- Criar um servidor flexível utilizando rede virtual predefinido, sub-rede com prefixo de endereço prefixo padrão
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- Crie um servidor flexível utilizando rede virtual e sub-rede já existentes. Se a rede virtual e a sub-rede fornecidas não existirem, então a rede virtual e a sub-rede com prefixo de endereço prefixo de endereço precável serão criadas.
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- Crie um servidor flexível utilizando a rede virtual já existente, sub-rede e utilizando o ID da sub-rede. A sub-rede fornecida não deverá ter qualquer outro recurso implantado na sua e esta sub-rede será delegada na **Microsoft.DBforPostgreSQL/flexibleServers,** se ainda não delegada.
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e subscrição que o seu servidor flexível.

- Criar um servidor flexível utilizando uma nova rede virtual, sub-rede com prefixo de endereço não predefinido
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Consulte a [documentação de referência](/cli/azure/postgres/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [networking em Azure Database for PostgreSQL - Flexible Server](./concepts-networking.md).
- [Criar e gerir a Base de Dados Azure para postgreSQL - rede virtual de servidor flexível utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).
- Conheça mais sobre [a Base de Dados Azure para postgreSQL - Rede virtual de servidor flexível](./concepts-networking.md#private-access-vnet-integration).