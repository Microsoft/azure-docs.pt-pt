---
title: Gerir regras de firewall - Azure CLI - Azure Database for MySQL - Servidor Flexível
description: Crie e gere as regras de firewall para Azure Database for MySQL - Servidor Flexível utilizando a linha de comando Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4c699b00ebbecdbee985babe47c036cce6e0cb4b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109969"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Criar e gerir a Base de Dados Azure para o MySQL - Regras de firewall do servidor flexível utilizando o Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública

O Servidor Flexível da Base de Dados do Azure para MySQL suporta dois tipos de métodos de conectividade de rede mutuamente exclusivos para ligar ao seu servidor flexível. As duas opções são:

- Acesso público (endereços IP permitidos)
- Acesso privado (Integração de VNet)

Neste artigo, vamos focar-nos na criação do servidor MySQL com **acesso público (endereços IP autorizados)** usando O Azure CLI e forneceremos uma visão geral sobre os comandos Azure CLI que pode utilizar para criar, atualizar, excluir, listar e mostrar regras de firewall após a criação do servidor. Com *acesso público (endereços IP autorizados)*, as ligações ao servidor MySQL estão restritas apenas a endereços IP permitidos. Os endereços IP do cliente devem ser permitidos nas regras de firewall. Para saber mais sobre o assunto, consulte o [acesso do Público (endereços IP autorizados)](./concepts-networking.md#public-access-allowed-ip-addresses). As regras de firewall podem ser definidas no momento da criação do servidor (recomendado), mas também podem ser adicionadas mais tarde.

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

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Crie a regra de firewall durante a criação flexível do servidor usando o Azure CLI

Pode utilizar o `az mysql flexible-server --public access` comando para criar o servidor flexível com acesso público *(endereços IP autorizados)* e configurar as regras de firewall durante a criação de um servidor flexível. Pode utilizar o comutador de acesso público para fornecer os endereços IP autorizados que poderão **ligar-se** ao servidor. Pode fornecer endereços IP únicos ou variados para ser incluído na lista permitida de IPs. O intervalo de endereços IP deve ser separado e não contém quaisquer espaços. Existem várias opções para criar um servidor flexível usando OC, como mostrado no exemplo abaixo.

Consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. Por exemplo, nos comandos abaixo pode especificar opcionalmente o grupo de recursos.

- Crie um servidor flexível com acesso público e adicione o endereço IP do cliente para ter acesso ao servidor
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Crie um servidor flexível com acesso público e adicione a gama de endereços IP para ter acesso a este servidor

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Crie um servidor flexível com acesso público e permita que as aplicações a partir de endereços IP do Azure se conectem ao seu servidor flexível
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Esta opção configura a firewall para permitir o acesso público dos serviços e recursos da Azure a este servidor, incluindo ligações a partir das subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
    >
- Crie um servidor flexível com acesso público e permita todo o endereço IP
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > O comando acima irá criar uma regra de firewall com endereço IP inicial=0.0.0.0, end ENDEREÇO IP=255.255.255.255 e nenhum endereço IP será bloqueado. Qualquer anfitrião na Internet pode aceder a este servidor. Recomenda-se vivamente a utilização desta regra apenas temporariamente e apenas em servidores de teste que não contenham dados sensíveis.

- Criar um servidor flexível com acesso público e sem endereço IP
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > não recomendamos criar um servidor sem regras de firewall. Se não adicionar quaisquer regras de firewall, nenhum cliente poderá ligar-se ao servidor.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Criar e gerir a regra de firewall após a criação do servidor
O comando **az mysql de regra de firewall do servidor flexível** é usado a partir do CLI Azure para criar, eliminar, listar, mostrar e atualizar as regras de firewall.

Comandos:
- **criar**: Criar uma regra de firewall de servidor flexível.
- **lista**: Listar as regras flexíveis de firewall do servidor.
- **atualização**: Atualize uma regra de firewall flexível do servidor.
- **show**: Mostrar os detalhes de uma regra de firewall do servidor flexível.
- **eliminar**: Elimine uma regra de firewall flexível do servidor.

Consulte a [documentação de referência](/cli/azure/mysql/flexible-server) do Azure CLI para a lista completa de parâmetros CLI configuráveis. Por exemplo, nos comandos abaixo pode especificar opcionalmente o grupo de recursos.

### <a name="create-a-firewall-rule"></a>Criar uma regra de firewall
Utilize o `az mysql flexible-server firewall-rule create` comando para criar uma nova regra de firewall no servidor.
Para permitir o acesso a uma série de endereços IP, forneça o endereço IP como endereço IP inicial e endereço IP final, como neste exemplo.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Para permitir o acesso a um único endereço IP, basta fornecer um único endereço IP, como neste exemplo.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Para permitir que as aplicações a partir de endereços IP do Azure se conectem ao seu servidor flexível, forneça o endereço IP 0.0.0.0 como o IP inicial, como neste exemplo.
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Esta opção configura a firewall para permitir o acesso público dos serviços e recursos da Azure a este servidor, incluindo ligações a partir das subscrições de outros clientes. Quando selecionar esta opção, certifique-se de que as suas permissões de início de sessão e de utilizador limitam o acesso apenas a utilizadores autorizados.
> 

Após o sucesso, cada uma cria a saída de comando lista os detalhes da regra de firewall que criou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

### <a name="list-firewall-rules"></a>Listar regras de firewall 
Utilize o `az mysql flexible-server firewall-rule list` comando para listar as regras de firewall do servidor existentes no servidor. Note que o atributo nome do servidor está especificado no interruptor **de nome.**
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
A saída lista as regras, se houver, no formato JSON (por padrão). Pode utilizar o interruptor de tabela de saída** para obter os resultados num formato de mesa mais legível.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Atualizar uma regra de firewall
Utilize o `az mysql flexible-server firewall-rule update` comando para atualizar uma regra de firewall existente no servidor. Forneça o nome da regra de firewall existente como entrada, bem como o endereço IP inicial e os atributos de endereço IP final para atualizar.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que atualizou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

> [!NOTE]
> Se a regra de firewall não existir, a regra é criada pelo comando de atualização.

### <a name="show-firewall-rule-details"></a>Mostrar detalhes da regra da firewall
Utilize o `az mysql flexible-server firewall-rule show` comando para mostrar os detalhes da regra de firewall existentes a partir do servidor. Forneça o nome da regra de firewall existente como entrada.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Após o sucesso, a saída de comando lista os detalhes da regra de firewall que especificou, no formato JSON (por padrão). Em caso algum caso, existe uma falha, a saída mostra o texto da mensagem de erro.

### <a name="delete-a-firewall-rule"></a>Eliminar uma regra de firewall
Utilize o `az mysql flexible-server firewall-rule delete` comando para eliminar uma regra de firewall existente do servidor. Forneça o nome da regra de firewall existente.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Após o sucesso, não há saída. Após falha, o texto da mensagem de erro aparece.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [Networking na Base de Dados Azure para o MySQL Flexible Server](./concepts-networking.md)
- Conheça mais sobre [a Base de Dados Azure para as regras de firewall do MySQL Flexible Server](./concepts-networking.md#public-access-allowed-ip-addresses)
- [Crie e gere a base de dados Azure para as regras de firewall do Servidor Flexível MySQL utilizando o portal Azure](./how-to-manage-firewall-portal.md).