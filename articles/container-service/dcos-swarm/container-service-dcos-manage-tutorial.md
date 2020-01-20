---
title: PRETERIDO Tutorial do serviço de contêiner do Azure – gerenciar DC/so
description: Tutorial do Azure Container Service – Gerir DC/OS
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1c9b3bfdbe7aff203efa6b36f0e40cb65aba1175
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278344"
---
# <a name="deprecated-azure-container-service-tutorial---manage-dcos"></a>PRETERIDO Tutorial do serviço de contêiner do Azure – gerenciar DC/so

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS oferece uma plataforma distribuída para executar aplicações modernas em contentores. Com o Azure Container Service, o aprovisionamento de um cluster DC/OS pronto para produção é simples e rápido. Este início rápido detalha os passos básicos necessários para implementar um DC/OS do cluster e executar carga de trabalho básica.

> [!div class="checklist"]
> * Criar um cluster de ACS DC/OS
> * Ligar ao cluster
> * Instalar o DC/OS CLI
> * Implementar uma aplicação no cluster
> * Dimensionar uma aplicação no cluster
> * Dimensionar nós de cluster DC/OS
> * Gestão de DC/OS básica
> * Eliminar o cluster DC/OS

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-dcos-cluster"></a>Criar cluster DC/OS

Primeiro, crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *westeurope*.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Em seguida, crie um cluster DC/OS, com o comando [az acs create](/cli/azure/acs#az-acs-create).

O exemplo seguinte cria uma cluster DC/OS com o nome *myDCOSCluster* e cria chaves SSH caso estas ainda não existam. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli
az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

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

## <a name="install-dcos-cli"></a>Instalar DC/SO CLI

Instalar a DC/OS cli com o comando [az acs dcos install-cli](/cli/azure/acs/dcos#az-acs-dcos-install-cli). Se estiver a utilizar o Azure CloudShell, a DC/OS CLI já está instalada. Se estiver a executar a CLI do Azure no macOS ou Linux, é necessário executar o comando com sudo.

```azurecli
az acs dcos install-cli
```

Antes da CLI poder ser utilizada com o cluster, tem de ser configurada para utilizar o túnel SSH. Para tal, execute o seguinte comando, ajustando a porta, se necessário.

```azurecli
dcos config set core.dcos_url http://localhost
```

## <a name="run-an-application"></a>Executar uma aplicação

O mecanismo de agendamento predefinido para um cluster ACS DC/OS é o Marathon. O Marathon serve para iniciar uma aplicação e gerir o estado da aplicação no cluster DC/OS. Para agendar uma aplicação através do Marathon, crie um ficheiro denominado **marathon-app.json** e copie o seguinte conteúdo para o mesmo. 

```json
{
  "id": "demo-app-private",
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
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
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

Quando o valor da coluna **TAREFAS** muda de *0/1* a *1/1*, a implementação de aplicação é concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     0/1    ---       ---      False      DOCKER   None
```

## <a name="scale-marathon-application"></a>Dimensionar a aplicação Marathon

No exemplo anterior, foi criada uma aplicação de instância única. Para atualizar esta implementação, de modo a que três instâncias da aplicação estejam disponíveis, abra o ficheiro **marathon-app.json** e atualize a propriedade de instância para 3.

```json
{
  "id": "demo-app-private",
  "cmd": null,
  "cpus": 1,
  "mem": 32,
  "disk": 0,
  "instances": 3,
  "container": {
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ]
    },
    "type": "DOCKER"
  }
}
```

Atualize a aplicação com o comando `dcos marathon app update`.

```azurecli
dcos marathon app update demo-app-private < marathon-app.json
```

Para ver o estado de implementação da aplicação, execute o seguinte comando.

```azurecli
dcos marathon app list
```

Quando o valor da coluna **TAREFAS** muda de *1/3* a *3/1*, a implementação de aplicação é concluída.

```azurecli
ID     MEM  CPUS  TASKS  HEALTH  DEPLOYMENT  WAITING  CONTAINER  CMD   
/test   32   1     1/3    ---       ---      False      DOCKER   None
```

## <a name="run-internet-accessible-app"></a>Executar aplicação acessível na Internet

O cluster de ACS DC/OS é constituído por dois conjuntos de nó, um público que é acessível na Internet e um privado que não está acessível na Internet. O conjunto predefinido são nós privados, que foram utilizados no último exemplo.

Para tornar uma aplicação acessível na Internet, implemente-as no conjunto de nós públicos. Para tal, atribua ao objeto `acceptedResourceRoles` um valor de `slave_public`.

Crie um ficheiro com o nome **nginx-public.json** e copie os seguintes conteúdos para o mesmo.

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
dcos marathon app add nginx-public.json
```

Obtenha o endereço IP público dos agentes de cluster público DC/OS.

```azurecli 
az network public-ip list --resource-group myResourceGroup --query "[?contains(name,'dcos-agent')].[ipAddress]" -o tsv
```

Navegar para este endereço devolve o site NGINX predefinido.

![NGINX](./media/container-service-dcos-manage-tutorial/nginx.png)

## <a name="scale-dcos-cluster"></a>Dimensionar cluster DC/OS

Nos exemplos anteriores, foi escalada uma aplicação para múltiplas instâncias. A infraestrutura de DC/OS também pode ser escalada para dispor de mais ou menos capacidade de computação. Isto é feito com o comando [az acs scale](/cli/azure/acs#az-acs-scale). 

Para ver a contagem atual de agentes DC/OS, utilize o comando [az acs show](/cli/azure/acs#az-acs-show).

```azurecli
az acs show --resource-group myResourceGroup --name myDCOSCluster --query "agentPoolProfiles[0].count"
```

Para aumentar a contagem para 5, utilize o comando [az acs scale](/cli/azure/acs#az-acs-scale). 

```azurecli
az acs scale --resource-group myResourceGroup --name myDCOSCluster --new-agent-count 5
```

## <a name="delete-dcos-cluster"></a>Eliminar cluster DC/OS

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o Grupo de Recursos, o cluster DC/OS e todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a tarefa de gestão básica do DC/OS, incluindo o seguinte. 

> [!div class="checklist"]
> * Criar um cluster de ACS DC/OS
> * Ligar ao cluster
> * Instalar o DC/OS CLI
> * Implementar uma aplicação no cluster
> * Dimensionar uma aplicação no cluster
> * Dimensionar nós de cluster DC/OS
> * Eliminar o cluster DC/OS

Avance para o próximo tutorial para saber mais sobre a aplicação de balanceamento de carga no DC/OS no Azure. 

> [!div class="nextstepaction"]
> [Fazer o balanceamento de carga de aplicações](container-service-load-balancing.md)
