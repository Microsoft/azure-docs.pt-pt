---
title: Gerir redes virtuais - Azure CLI - Azure Database for MySQL - Servidor Flexível
description: Criar e gerir redes virtuais para Azure Database for MySQL - Servidor Flexível utilizando o Azure CLI
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 70cb1297c4b47f22f9eb5cc6992e6fcd6c58b364
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545043"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Criar e gerir redes virtuais para Azure Database for MySQL - Servidor Flexível utilizando o Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

O Servidor Flexível da Base de Dados do Azure para MySQL suporta dois tipos de métodos de conectividade de rede mutuamente exclusivos para ligar ao seu servidor flexível. As duas opções são:

- Acesso público (endereços IP permitidos)
- Acesso privado (Integração de VNet)

Neste artigo, vamos focar-nos na criação do servidor MySQL com **acesso privado (VNet Integration)** utilizando o Azure CLI. Com *acesso privado (Integração VNet)* , pode implantar o seu servidor flexível na sua própria [Rede Virtual Azure.](../../virtual-network/virtual-networks-overview.md) As Redes Virtuais Azure fornecem comunicação de rede privada e segura. No acesso privado, as ligações ao servidor MySQL estão restritas apenas à sua rede virtual. Para saber mais sobre o assunto, consulte o [acesso privado (VNet Integration)](./concepts-networking.md#private-access-vnet-integration).

Na Base de Dados Azure para o MySQL Flexible Server, só é possível implantar o servidor numa rede virtual e numa sub-rede durante a criação do servidor. Depois de o servidor flexível ser implantado numa rede virtual e numa sub-rede, não é possível movê-lo para outra rede virtual, sub-rede ou para *acesso público (endereços IP autorizados)* .

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

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Criar base de dados Azure para o MySQL Flexible Server utilizando o CLI
Pode utilizar o `az mysql flexible-server` comando para criar o servidor flexível com acesso privado *(Integração VNet)* . Este comando utiliza o acesso privado (VNet Integration) como método de conectividade padrão. Uma rede virtual e uma sub-rede serão criadas para si se nenhuma for fornecida. Também pode fornecer a rede virtual e sub-rede já existentes utilizando o ID da sub-rede. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Existem várias opções para criar um servidor flexível usando OC, como mostrado nos exemplos abaixo.

>[!Important]
> A utilização deste comando delegará a sub-rede ao **Microsoft.DBforMySQL/flexibleServers** . Esta delegação significa que apenas os Servidores Flexíveis da Base de Dados do Azure para MySQL podem utilizar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.
>

Consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. Por exemplo, nos comandos abaixo pode especificar opcionalmente o grupo de recursos.

- Criar um servidor flexível utilizando rede virtual predefinido, sub-rede com prefixo de endereço prefixo prefixo prefixo padrão
    ```azurecli-interactive
    az mysql flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- Crie um servidor flexível utilizando a rede virtual já existente, sub-rede e utilizando o ID da sub-rede. A sub-rede fornecida não deverá ter qualquer outro recurso implantado na sua e esta sub-rede será delegada na **Microsoft.DBforMySQL/flexibleServers,** se ainda não delegada.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e subscrição que o seu servidor flexível.
<!--
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
Consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [networking na Base de Dados Azure para o MySQL Flexible Server](./concepts-networking.md).
- [Crie e gere a base de dados Azure para a rede virtual mySQL Flexible Server utilizando o portal Azure](./how-to-manage-virtual-network-portal.md).
- Conheça mais sobre [a Base de Dados Azure para a rede virtual MySQL Flexible Server](./concepts-networking.md#private-access-vnet-integration).