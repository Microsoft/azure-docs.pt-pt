---
title: Utilizar o Docker Compose
description: Como instalar e utilizar Docker e Compor em máquinas virtuais Linux com o Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/14/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9f824a3b7e1e5e482bbc04c448ca4930bf88678
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500951"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Começa com o Docker e o Compose para definir e executar uma aplicação multi-contentor em Azure
Com [a Compose,](https://github.com/docker/compose)utiliza-se um ficheiro de texto simples para definir uma aplicação composta por vários recipientes Docker. Em seguida, gira a sua aplicação num único comando que faz tudo para implantar o seu ambiente definido. Como exemplo, este artigo mostra-lhe como configurar rapidamente um blog WordPress com uma base de dados MariaDB SQL de backend num Ubuntu VM. Também pode usar o Compose para configurar aplicações mais complexas.

Este artigo foi testado pela última vez em 2/14/2019 usando a [Azure Cloud Shell](https://shell.azure.com/bash) e a versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Criar anfitrião Docker com Azure CLI
Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e faça login numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Em primeiro lugar, crie um grupo de recursos para o seu ambiente Docker com [criação de grupo az](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Crie um ficheiro nomeado *cloud-init.txt* e cole a seguinte configuração. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. 

```yaml
#include https://get.docker.com
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo a seguir cria um VM chamado *myDockerVM* e abre a porta 80 para o tráfego web.

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

Demora alguns minutos até a VM ser criada, os pacotes serem instalados e a aplicação ser iniciada. Existem tarefas em segundo plano que continuam em execução após a CLI do Azure o devolver à linha de comandos. Quando a VM tiver sido criada, tome nota do `publicIpAddress` apresentado pela CLI do Azure. 

                 

## <a name="install-compose"></a>Instalar Compor


SSH para o seu novo anfitrião Docker VM. Forneça o seu próprio endereço IP.

```bash
ssh azureuser@10.10.111.11
```

Instale compor no VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Criar um ficheiro de configuração docker-compose.yml
Crie um `docker-compose.yml` ficheiro de configuração para definir os recipientes Docker para funcionar no VM. O ficheiro especifica a imagem a ser executada em cada recipiente, variáveis ambientais necessárias e dependências, portas e as ligações entre contentores. Para obter mais informações sobre a sintaxe do ficheiro yml, consulte [a referência de ficheiro Compose](https://docs.docker.com/compose/compose-file/).

Crie um ficheiro *estivador.yml.* Use o seu editor de texto favorito para adicionar alguns dados ao ficheiro. O exemplo a seguir cria o ficheiro com um pedido `sensible-editor` para escolher um editor que deseja utilizar.

```bash
sensible-editor docker-compose.yml
```

Cole o seguinte exemplo no seu ficheiro Docker Compose. Esta configuração utiliza imagens do [Registo DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blogging de código aberto e gestão de conteúdos) e uma base de dados mariaDB SQL ligada. Insira o seu próprio *MYSQL_ROOT_PASSWORD.*

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

## <a name="start-the-containers-with-compose"></a>Inicie os recipientes com Compose
No mesmo diretório que o seu ficheiro *docker-compose.yml,* executar o seguinte comando (dependendo do seu ambiente, poderá ter de executar `docker-compose` `sudo` utilizando):

```bash
sudo docker-compose up -d
```

Este comando inicia os contentores Docker especificados em *estiva-compose.yml*. Leva um minuto ou dois para este passo completar. Verá uma saída semelhante à seguinte:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Para verificar se os recipientes estão de pé, escreva `sudo docker-compose ps` . Deverá ver algo como:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Agora pode ligar-se ao WordPress diretamente no VM na porta 80. Abra um navegador web e insira o nome do endereço IP do seu VM. Deverá agora ver o ecrã de arranque wordPress, onde pode completar a instalação e começar com a aplicação.

![Ecrã de partida WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Passos seguintes
* Consulte a [referência da linha de comando Compose](https://docs.docker.com/compose/reference/) e o guia do [utilizador](https://docs.docker.com/compose/) para obter mais exemplos de construção e implementação de aplicações multi-contentores.
* Use um modelo de Gestor de Recursos Azure, o seu próprio ou um contribuído da [comunidade,](https://azure.microsoft.com/documentation/templates/)para implementar um VM Azure com Docker e uma aplicação configurada com Compose. Por exemplo, o [blog Implementar um wordPress com](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) o modelo Docker usa Docker e Compose para implementar rapidamente o WordPress com um backend MySQL num Ubuntu VM.
* Tente integrar Docker Compose com um cluster Docker Swarm. Consulte [a Utilização de Compose com Enxame](https://docs.docker.com/compose/swarm/) para cenários.
