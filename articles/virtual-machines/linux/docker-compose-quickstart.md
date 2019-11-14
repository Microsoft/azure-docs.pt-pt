---
title: Usar Docker Compose em uma VM do Linux no Azure
description: Como instalar e usar o Docker e o Compose em máquinas virtuais Linux com o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 4f2f12e0124743ad31e083cf4ece83bcb608bce0
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036277"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introdução ao Docker e ao Compose para definir e executar um aplicativo de vários contêineres no Azure
Com o [Compose](https://github.com/docker/compose), você usa um arquivo de texto simples para definir um aplicativo que consiste em vários contêineres do Docker. Em seguida, você cria seu aplicativo em um único comando que faz tudo para implantar seu ambiente definido. Por exemplo, este artigo mostra como configurar rapidamente um blog do WordPress com um banco de dados SQL MariaDB de back-end em uma VM Ubuntu. Você também pode usar o Compose para configurar aplicativos mais complexos.

Este artigo foi testado pela última vez em 2/14/2019 usando o [Azure cloud Shell](https://shell.azure.com/bash) e a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) versão 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Criar host do Docker com o CLI do Azure
Instale o [CLI do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Primeiro, crie um grupo de recursos para o ambiente do Docker com [AZ Group Create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Crie um arquivo chamado *Cloud-init. txt* e cole a configuração a seguir. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. 

```yaml
#include https://get.docker.com
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo a seguir cria uma VM chamada *myDockerVM* e abre a porta 80 para o tráfego da Web.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Demora alguns minutos até a VM ser criada, os pacotes serem instalados e a aplicação ser iniciada. Existem tarefas em segundo plano que continuam a ser executadas após a CLI do Azure devolver o utilizador à linha de comandos. Quando a VM tiver sido criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. 

                 

## <a name="install-compose"></a>Instalar compor


SSH para sua nova VM de host do Docker. Forneça seu próprio endereço IP.

```bash
ssh azureuser@10.10.111.11
```

Instale o Compose na VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Criar um arquivo de configuração Docker-Compose. yml
Crie um arquivo de configuração `docker-compose.yml` para definir os contêineres do Docker a serem executados na VM. O arquivo especifica a imagem a ser executada em cada contêiner, as variáveis de ambiente e as dependências necessárias, as portas e os links entre os contêineres. Para obter detalhes sobre a sintaxe de arquivo yml, consulte [referência de arquivo de composição](https://docs.docker.com/compose/compose-file/).

Crie um arquivo *Docker-Compose. yml* . Use seu editor de texto favorito para adicionar alguns dados ao arquivo. O exemplo a seguir cria o arquivo com um prompt para que `sensible-editor` escolha um editor que você deseja usar.

```bash
sensible-editor docker-compose.yml
```

Cole o exemplo a seguir em seu arquivo de Docker Compose. Essa configuração usa imagens do [registro DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o blog de software livre e o sistema de gerenciamento de conteúdo) e um banco de dados SQL MariaDB de back-end vinculado. Insira seu próprio *MYSQL_ROOT_PASSWORD*.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Iniciar os contêineres com Compose
No mesmo diretório que o arquivo *Docker-Compose. yml* , execute o seguinte comando (dependendo do seu ambiente, talvez seja necessário executar `docker-compose` usando `sudo`):

```bash
sudo docker-compose up -d
```

Esse comando inicia os contêineres do Docker especificados em *Docker-Compose. yml*. Demora um ou dois minutos para que essa etapa seja concluída. Você verá uma saída semelhante à seguinte:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Para verificar se os contêineres estão ativos, digite `sudo docker-compose ps`. Você deverá ver algo como:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Agora você pode se conectar ao WordPress diretamente na VM na porta 80. Abra um navegador da Web e insira o nome do endereço IP da sua VM. Agora você deve ver a tela iniciar do WordPress, na qual você pode concluir a instalação e começar a usar o aplicativo.

![Tela inicial do WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Passos seguintes
* Confira a [referência de linha de comando do Compose](https://docs.docker.com/compose/reference/) e o [Guia do usuário](https://docs.docker.com/compose/) para obter mais exemplos de criação e implantação de aplicativos de vários contêineres.
* Use um modelo de Azure Resource Manager, seu próprio ou um que seja contribuído pela [comunidade](https://azure.microsoft.com/documentation/templates/), para implantar uma VM do Azure com o Docker e um aplicativo configurado com o Compose. Por exemplo, o modelo [implantar um blog do WordPress com o Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) usa o Docker e o Compose para implantar rapidamente o WordPress com um back-end MySQL em uma VM do Ubuntu.
* Tente integrar Docker Compose com um cluster Docker Swarm. Consulte [usando compor com Swarm](https://docs.docker.com/compose/swarm/) para cenários.

