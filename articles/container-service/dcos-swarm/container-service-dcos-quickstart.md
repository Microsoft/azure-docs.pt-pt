---
title: (PRETERIDO) Azure Container Service início rápido - implementar Cluster DC/OS
description: Guia de Introdução do Azure Container Service - Implementar Cluster do DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: quickstart
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: efaf82c3f378f572c289b587dbe5df1923a58c62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457061"
---
# <a name="deprecated-deploy-a-dcos-cluster"></a>(PRETERIDO) Implementar um cluster DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS oferece uma plataforma distribuída para executar aplicações modernas em contentores. Com o Azure Container Service, o aprovisionamento de um cluster DC/OS pronto para produção é simples e rápido. Este guia de introdução detalha os passos básicos necessários para implementar um DC/OS do cluster e executar carga de trabalho básica.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure 

Inicie sessão na sua subscrição do Azure com o comando [az login](/cli/azure/reference-index#az-login) e siga as instruções no ecrã.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-dcos-cluster"></a>Criar cluster DC/OS

Crie um cluster DC/OS, com o comando [az acs create](/cli/azure/acs#az-acs-create).

O exemplo seguinte cria uma cluster DC/OS com o nome *myDCOSCluster* e cria chaves SSH caso estas ainda não existam. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli
az acs create --orchestrator-type dcos --resource-group myResourceGroup --name myDCOSCluster --generate-ssh-keys
```

Em alguns casos, como numa versão de avaliação limitada, uma subscrição do Azure tem acesso limitado aos recursos do Azure. Caso a implementação falhe devido a um número limitado de núcleos disponíveis, reduza a contagem de agentes pré-definida ao adicionar `--agent-count 1` ao comando [az acs create](/cli/azure/acs#az-acs-create). 

Após alguns minutos, o comando é concluído e devolve informações sobre a implementação.

## <a name="connect-to-dcos-cluster"></a>Ligar ao cluster DC/OS

Depois de criar um cluster DC/OS, pode aceder ao mesmo através de um túnel SSH. Execute os seguintes comandos para devolver o endereço IP público do DC/OS. Este endereço IP é armazenado numa variável e utilizado no próximo passo.

```azurecli
ip=$(az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-master')].[ipAddress]" -o tsv)
```

Para criar o túnel SSH, execute o seguinte comando e siga as instruções no ecrã. Se a porta 80 já está a ser utilizada, o comando falha. Atualize a porta em túnel para uma que não esteja a ser utilizada, como `85:localhost:80`. 

```azurecli
sudo ssh -i ~/.ssh/id_rsa -fNL 80:localhost:80 -p 2200 azureuser@$ip
```

O túnel SSH, pode ser testado, ao navegar até `http://localhost`. Se uma porta que não a 80 tiver sido utilizada, ajuste a localização para corresponder. 

Se o túnel SSH foi criado com êxito, é devolvido o portal DC/OS.

![IU DCOS](./media/container-service-dcos-quickstart/dcos-ui.png)

## <a name="install-dcos-cli"></a>Instalar DC/SO CLI

A interface de linha de comandos do DC/OS é utilizada para gerir um cluster DC/OS a partir da linha de comandos. Instalar a DC/OS cli com o comando [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Se estiver a utilizar o Azure CloudShell, a DC/OS CLI já está instalada. 

Se estiver a executar a CLI do Azure no macOS ou Linux, é necessário executar o comando com sudo.

```azurecli
az acs dcos install-cli
```

Antes da CLI poder ser utilizada com o cluster, tem de ser configurada para utilizar o túnel SSH. Para tal, execute o seguinte comando, ajustando a porta, se necessário.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Executar uma aplicação

O mecanismo de agendamento predefinido para um cluster ACS DC/OS é o Marathon. O Marathon serve para iniciar uma aplicação e gerir o estado da aplicação no cluster DC/OS. Para agendar uma aplicação através do Marathon, crie um ficheiro denominado *marathon-app.json* e copie o seguinte conteúdo para o mesmo. 

```json
{
  "id": "demo-app",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 1,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  },
  "acceptedResourceRoles": [
    "slave_public"
  ]
}
```

Execute o seguinte comando para agendar a aplicação para ser executada no cluster DC/OS.

```azurecli
dcos marathon app add marathon-app.json
```

Para ver o estado de implementação da aplicação, execute o seguinte comando.

```azurecli
dcos marathon app list
```

Quando o valor da coluna **A AGUARDAR** muda de *Verdadeiro* para *Falso*, a implementação de aplicação é concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/1    ---       ---      False      DOCKER   None
```

Obtenha o endereço IP público dos agentes de cluster DC/OS.

```azurecli
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Navegar para este endereço devolve o site NGINX predefinido.

![NGINX](./media/container-service-dcos-quickstart/nginx.png)

## <a name="delete-dcos-cluster"></a>Eliminar cluster DC/OS

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o Grupo de Recursos, o cluster DC/OS e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de introdução, implementou um cluster DC/OS e executou um contentor de Docker simples no cluster. Para saber mais sobre o Azure Container Service, continue o tutorial do ACS.

> [!div class="nextstepaction"]
> [Gerir um Cluster de ACS DC/OS](container-service-dcos-manage-tutorial.md)