---
title: Instalar o MongoDB numa VM do Linux com a CLI do Azure | Documentos da Microsoft
description: Saiba como instalar e configurar o MongoDB num iusando de máquina virtual do Linux a CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 5fadf23cc1fc2e1a6092c48033580d398fc689a0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012804"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Como instalar e configurar o MongoDB numa VM do Linux

[MongoDB](https://www.mongodb.org) é um banco popular dados NoSQL de código-fonte aberto de alto desempenho. Este artigo mostra-lhe como instalar e configurar o MongoDB numa VM do Linux com a CLI do Azure. Exemplos são mostrados como esse detalhe para:

* [Instalar e configurar uma instância de MongoDB básica manualmente](#manually-install-and-configure-mongodb-on-a-vm)
* [Criar uma instância de MongoDB básica com um modelo do Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Criar uma MongoDB complexa, os conjuntos de cluster em partição horizontal com a réplica com um modelo do Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar o MongoDB numa VM manualmente
MongoDB [fornecem instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para distribuições de Linux, incluindo o Red Hat / CentOS, SUSE, Ubuntu e Debian. O exemplo seguinte cria um *CentOS* VM. Para criar este ambiente, tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index).

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome *myVM* com um usuário chamado *azureuser* com autenticação de chave pública SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a VM com o seu próprio nome de utilizador e a `publicIpAddress` listado no resultado do passo anterior:

```bash
ssh azureuser@<publicIpAddress>
```

Para adicionar as origens de instalação para o MongoDB, crie uma **yum** ficheiro de repositório da seguinte forma:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Abra o ficheiro de repositório de MongoDB para edição, tal como com `vi` ou `nano`. Adicione as seguintes linhas:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Instalar MongoDB usando **yum** da seguinte forma:

```bash
sudo yum install -y mongodb-org
```

Por predefinição, SELinux é aplicada a imagens de CentOS que o impede de acessar o MongoDB. Instalar ferramentas de gestão de política e configurar SELinux para permitir que o MongoDB operar em sua porta TCP padrão 27017 da seguinte forma:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço de MongoDB da seguinte forma:

```bash
sudo service mongod start
```

Certifique-se a instalação do MongoDB ao ligar ao local `mongo` cliente:

```bash
mongo
```

Agora, teste a instância de MongoDB ao adicionar alguns dados e, em seguida, procurar:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se assim o desejar, configure o MongoDB para iniciar automaticamente durante um reinício do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar instância de MongoDB básica em CentOS através de um modelo
Pode criar uma instância de MongoDB básica numa única VM CentOS utilizando o modelo de início rápido do Azure seguinte do GitHub. Este modelo utiliza a extensão de Script personalizado para Linux para adicionar um **yum** repositório à sua VM recém-criada do CentOS e, em seguida, instalar MongoDB.

* [Instância de MongoDB básica no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Para criar este ambiente, tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implemente o modelo de MongoDB com [criar a implementação do grupo az](/cli/azure/group/deployment). Quando lhe for pedido, introduza os seus próprios valores exclusivos para *newStorageAccountName*, *dnsNameForPublicIP*e nome de utilizador administrador e a palavra-passe:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Inicie sessão na VM com o endereço DNS público da sua VM. Pode ver o endereço DNS público com [show de vm de az](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH para a VM com o seu próprio nome de utilizador e o endereço DNS público:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Certifique-se a instalação do MongoDB ao ligar ao local `mongo` cliente da seguinte forma:

```bash
mongo
```

Agora, teste a instância ao adicionar alguns dados e pesquisa da seguinte forma:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um Cluster de em partição horizontal do MongoDB complexos no CentOS através de um modelo
Pode criar um cluster em partição horizontal MongoDB complexo usando o seguinte modelo de início rápido do Azure a partir do GitHub. Este modelo segue a [práticas recomendadas de cluster em partição horizontal MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer redundância e elevada disponibilidade. O modelo cria duas partições horizontais, com três nós em cada conjunto de réplicas. Também é criada uma réplica do servidor de configuração definida com três nós, mais dois **mongos** servidores de router para fornecer consistência para aplicativos de entre as partições horizontais.

* [Cluster de fragmentação do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Este cluster em partição horizontal do MongoDB complexo a implementação requer mais de 20 núcleos, que normalmente é o número de núcleos de padrão por região para uma subscrição. Abra um pedido de suporte do Azure para aumentar o número de núcleos.

Para criar este ambiente, tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implemente o modelo de MongoDB com [criar a implementação do grupo az](/cli/azure/group/deployment). Defina seu próprio recurso nomes e onde necessário, tais como para os tamanhos *mongoAdminUsername*, *sizeOfDataDiskInGB*, e *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Esta implementação pode demorar mais de uma hora para implementar e configurar todas as instâncias VM. O `--no-wait` sinalizador é utilizado no final do comando anterior para retornar o controle ao prompt de comando depois da implementação do modelo foi aceite pela plataforma do Azure. Em seguida, pode ver o estado de implementação com [show de implementação do grupo az](/cli/azure/group/deployment). O seguinte exemplo mostra o estado para o *myMongoDBCluster* implementação no *myResourceGroup* grupo de recursos:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Passos Seguintes
Estes exemplos, vai ligar à instância de MongoDB localmente da VM. Se pretender ligar à instância de MongoDB a partir de outra VM ou de rede, certifique-se a apropriado [regras do grupo de segurança de rede são criadas](nsg-quickstart.md).

Estes exemplos implante o ambiente de MongoDB de núcleos para fins de desenvolvimento. Aplicam-se as opções de configuração de segurança necessárias para o seu ambiente. Para obter mais informações, consulte a [docs de segurança do MongoDB](https://docs.mongodb.com/manual/security/).

Para obter mais informações sobre como criar através de modelos, consulte a [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Os modelos Azure Resource Manager utilizam a extensão de Script personalizado para transferir e executar scripts nas suas VMs. Para obter mais informações, consulte [com a extensão de Script personalizado do Azure com máquinas virtuais do Linux](extensions-customscript.md).

