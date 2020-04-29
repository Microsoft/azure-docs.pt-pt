---
title: Utilizar o Docker Compose
description: Como instalar e utilizar O Docker e a Composição em máquinas virtuais Linux com o Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78970311"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Começar com Docker e Compose para definir e executar uma aplicação multi-contentor em Azure
Com [composição,](https://github.com/docker/compose)utilize um simples ficheiro de texto para definir uma aplicação composta por vários contentores Docker. Em seguida, gira a sua aplicação num único comando que faz tudo para implantar o seu ambiente definido. Como exemplo, este artigo mostra-lhe como configurar rapidamente um blog WordPress com uma base de dados MariaDB SQL de backend num Ubuntu VM. Também pode usar o Compose para configurar aplicações mais complexas.

Este artigo foi testado pela última vez em 2/14/2019 utilizando a [Azure Cloud Shell](https://shell.azure.com/bash) e a versão [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Crie o anfitrião Docker com o Azure CLI
Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e inicie sessão numa conta Azure utilizando [login az](/cli/azure/reference-index).

Primeiro, crie um grupo de recursos para o seu ambiente Docker com grupo [az criar](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Crie um ficheiro chamado *cloud-init.txt* e colá-se na seguinte configuração. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. 

```yaml
#include https://get.docker.com
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo seguinte cria um VM chamado *myDockerVM* e abre a porta 80 para o tráfego web.

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

                 

## <a name="install-compose"></a>Instalar compor


SSH para o seu novo anfitrião Docker VM. Forneça o seu próprio endereço IP.

```bash
ssh azureuser@10.10.111.11
```

Instale a Composição no VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Crie um ficheiro de configuração docker-compose.yml
Crie `docker-compose.yml` um ficheiro de configuração para definir os recipientes Docker para funcionar no VM. O ficheiro especifica a imagem a ser executada em cada recipiente, variáveis ambientais necessárias e dependências, portos e as ligações entre contentores. Para obter mais detalhes sobre a sintaxe de ficheiros yml, consulte a referência do [ficheiro Compor](https://docs.docker.com/compose/compose-file/).

Crie um ficheiro *docker-compose.yml.* Utilize o seu editor de texto favorito para adicionar alguns dados ao ficheiro. O exemplo seguinte cria o `sensible-editor` ficheiro com um pedido para escolher um editor que deseja utilizar.

```bash
sensible-editor docker-compose.yml
```

Colhe o seguinte exemplo no seu ficheiro Docker Compose. Esta configuração utiliza imagens do [Registo DockerHub](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o sistema de blogging de código aberto e gestão de conteúdos) e uma base de dados MariaDB SQL ligada. Insira o seu próprio *MYSQL_ROOT_PASSWORD.*

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

## <a name="start-the-containers-with-compose"></a>Inicie os recipientes com composição
No mesmo diretório que o seu ficheiro *docker-compose.yml,* execute o seguinte comando `docker-compose` `sudo`(dependendo do seu ambiente, poderá ter de executar usando):

```bash
sudo docker-compose up -d
```

Este comando inicia os recipientes Docker especificados em *docker-compose.yml*. Leva um minuto ou dois para este passo ser concluído. Verá uma saída semelhante à seguinte:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Para verificar se os recipientes `sudo docker-compose ps`estão em cima, escreva . Deverá ver algo semelhante ao seguinte:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Agora pode ligar-se diretamente ao WordPress no VM na porta 80. Abra um navegador web e introduza o nome de endereço IP do seu VM. Deverá agora ver o ecrã de arranque do WordPress, onde pode completar a instalação e começar com a aplicação.

![Ecrã de início WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Passos seguintes
* Consulte a [referência da linha de comando compor](https://docs.docker.com/compose/reference/) e o guia do [utilizador](https://docs.docker.com/compose/) para obter mais exemplos de construção e implementação de aplicações multi-contentores.
* Utilize um modelo de Gestor de Recursos Azure, quer o seu próprio ou outro contribuído da [comunidade,](https://azure.microsoft.com/documentation/templates/)para implementar um VM Azure com Docker e uma aplicação criada com composição. Por exemplo, o [deploy a um blog WordPress com](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) o modelo Docker usa Docker e Compose para implementar rapidamente o WordPress com um backend MySQL em um Ubuntu VM.
* Tente integrar Docker Compose com um grupo de Docker Swarm. Ver [Usar compor com Enxame](https://docs.docker.com/compose/swarm/) para cenários.

