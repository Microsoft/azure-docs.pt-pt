---
title: Utilizar o Docker Compose numa VM do Linux no Azure | Documentos da Microsoft
description: Como instalar e utilizar o Docker e Compose em máquinas de virtuais do Linux com a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007183"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Introdução ao Docker e o Compose para definir e executar uma aplicação de vários contentores no Azure
Com o [Compose](https://github.com/docker/compose), utilizar um ficheiro de texto simples para definir uma aplicação constituída por vários contentores do Docker. , Em seguida, crie a sua aplicação com um único comando que faz tudo para implementar o seu ambiente definido. Por exemplo, este artigo mostra-lhe como configurar rapidamente um blogue do WordPress com um back-end da base de dados do MariaDB SQL numa VM do Ubuntu. Também pode utilizar o Compose para configurar as aplicações mais complexas.

Este artigo foi testado pela última vez sobre o uso de 2019/14/2 a [Azure Cloud Shell](https://shell.azure.com/bash) e o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) versão 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Criar anfitrião do Docker com a CLI do Azure
Instalar a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) e para iniciar sessão no Azure através da conta [início de sessão az](/cli/azure/reference-index).

Primeiro, crie um grupo de recursos para o seu ambiente do Docker com [criar grupo az](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Crie um ficheiro denominado *cloud-Init* e cole a seguinte configuração. Introduza `sensible-editor cloud-init.txt` para criar o ficheiro e ver uma lista dos editores disponíveis. 

```yaml
#include https://get.docker.com
```

Agora, crie uma VM com [az vm create](/cli/azure/vm#az-vm-create). Utilize o parâmetro `--custom-data` para passar o ficheiro de configuração de inicialização da cloud. Forneça o caminho completo para a configuração do *cloud-init.txt*, se tiver guardado o ficheiro fora do diretório de trabalho atual. O exemplo seguinte cria uma VM com o nome *myDockerVM* e abre-se a porta 80 ao tráfego da web.

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

                 

## <a name="install-compose"></a>Instalação de composição


SSH para o anfitrião do Docker nova VM. Fornece seu próprio endereço IP.

```bash
ssh azureuser@10.10.111.11
```

Compor instalar na VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Criar um ficheiro de configuração de docker-Compose
Criar um `docker-compose.yml` ficheiro de configuração para definir os contentores de Docker para executar na VM. O ficheiro Especifica a imagem para serem executadas em cada contentor, as variáveis de ambiente necessárias e as dependências, portas e as ligações entre contentores. Para obter detalhes sobre a sintaxe do arquivo yml, consulte [Compose referência do arquivo](https://docs.docker.com/compose/compose-file/).

Criar uma *docker-Compose* ficheiro. Utilize o seu editor de texto favorito para adicionar alguns dados para o ficheiro. O exemplo seguinte cria o ficheiro com uma linha de comandos para `sensible-editor` para escolher um editor que deseja usar.

```bash
sensible-editor docker-compose.yml
```

Cole o seguinte exemplo no seu ficheiro do Docker Compose. Esta configuração utiliza imagens a partir da [DockerHub registo](https://registry.hub.docker.com/_/wordpress/) para instalar o WordPress (o código-fonte aberto escrevendo em seu blog e conteúdo de sistema de gerenciamento) e um back-end ligado base de dados MariaDB SQL. Introduza o seu próprio *MYSQL_ROOT_PASSWORD*.

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

## <a name="start-the-containers-with-compose"></a>Iniciar os contentores com Compose
No mesmo diretório do seu *docker-Compose* arquivo, execute o seguinte comando (dependendo do seu ambiente, poderá ter de executar `docker-compose` usando `sudo`):

```bash
sudo docker-compose up -d
```

Este comando inicia os contentores do Docker especificados no *docker-Compose*. Demora um minuto ou dois para que este passo concluir. Vai ver um resultado semelhante ao seguinte:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Para verificar que os contentores de cópia de segurança, escreva `sudo docker-compose ps`. Deverá ver algo como:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Pode agora ligar wordpress diretamente na VM na porta 80. Abra um browser e introduza o nome do endereço IP da sua VM. Agora, deverá ver o WordPress tela iniciar, onde pode concluir a instalação e começar a utilizar com o aplicativo.

![Ecrã inicial do WordPress](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Passos Seguintes
* Veja a [Compose referência da linha de comandos](https://docs.docker.com/compose/reference/) e [Guia do usuário](https://docs.docker.com/compose/) para obter mais exemplos de criação e implementação de aplicações de vários contentores.
* Utilizar um modelo Azure Resource Manager, optar por seu próprio ou um contributo do [Comunidade](https://azure.microsoft.com/documentation/templates/), para implementar uma VM do Azure com o Docker e um aplicativo configurado com a composição. Por exemplo, o [implementação de um blogue de WordPress com o Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) modelo utiliza o Docker e o Compose para implementar rapidamente o WordPress com um back-end MySQL numa VM do Ubuntu.
* Experimente a integrar o Docker Compose com um cluster Docker Swarm. Ver [usando Compose com o Swarn](https://docs.docker.com/compose/swarm/) para cenários.

