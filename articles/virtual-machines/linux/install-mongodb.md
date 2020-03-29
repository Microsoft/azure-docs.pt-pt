---
title: Instale o MongoDB num Linux VM com o Azure CLI
description: Aprenda a instalar e configurar o MongoDB numa máquina virtual Linux que utiliza o Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: e1bc7c8a6f97d6dc6bb1d6cb54825425244b2158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944877"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Como instalar e configurar o MongoDB num VM Linux

[MongoDB](https://www.mongodb.org) é uma popular base de dados noSQL de alto desempenho. Este artigo mostra-lhe como instalar e configurar o MongoDB num VM Linux com o Azure CLI. Exemplos são mostrados com esse detalhe como:

* [Instale e configure manualmente uma instância básica de MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Crie uma instância básica de MongoDB usando um modelo de Gestor de Recursos](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Crie um complexo cluster mongoDB com conjuntos de réplicas usando um modelo de Gestor de Recursos](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instale e configure manualmente MongoDB num VM
MongoDB [fornece instruções](https://docs.mongodb.com/manual/administration/install-on-linux/) de instalação para distros Linux, incluindo Chapéu Vermelho / CentOS, SUSE, Ubuntu e Debian. O exemplo seguinte cria um *CentOS* VM. Para criar este ambiente, necessita do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria um VM chamado *myVM* com um utilizador chamado *azureuser* usando a autenticação de chave pública SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para o VM utilizando o `publicIpAddress` seu próprio nome de utilizador e a lista da saída a partir do passo anterior:

```bash
ssh azureuser@<publicIpAddress>
```

Para adicionar as fontes de instalação para o MongoDB, crie um ficheiro de repositório **yum** da seguinte forma:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Abra o ficheiro de repo MongoDB `vi` para `nano`edição, como com ou . Adicione as seguintes linhas:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Instale o MongoDB utilizando **o yum** da seguinte forma:

```bash
sudo yum install -y mongodb-org
```

Por padrão, o SELinux é aplicado em imagens CentOS que o impedem de aceder ao MongoDB. Instale ferramentas de gestão de políticas e configure o SELinux para permitir que a MongoDB opere na sua porta TCP padrão 27017 da seguinte forma:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço MongoDB da seguinte forma:

```bash
sudo service mongod start
```

Verifique a instalação MongoDB ligando-a utilizando o cliente local: `mongo`

```bash
mongo
```

Agora teste a instância mongoDB adicionando alguns dados e, em seguida, pesquisando:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se desejar, configure o MongoDB para iniciar automaticamente durante um reboot do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Crie a instância básica de MongoDB no CentOS usando um modelo
Você pode criar uma instância básica de MongoDB em um único CentOS VM usando o seguinte modelo de quickstart Azure do GitHub. Este modelo utiliza a extensão custom Script para o Linux adicionar um repositório **yum** ao seu recém-criado CentOS VM e, em seguida, instalar O MongoDB.

* [Caso MongoDB básico no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Para criar este ambiente, necessita do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante o modelo MongoDB com a criação de implementação do [grupo Az](/cli/azure/group/deployment). Quando solicitado, introduza os seus valores únicos para *o novo StorageAccountName,* *dnsNameForPublicIP*e admin username e password:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Inicie sessão no VM utilizando o endereço Público DNS do seu VM. Pode ver o endereço público do DNS com [o az vm show:](/cli/azure/vm)

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH para o seu VM utilizando o seu próprio nome de utilizador e endereço DNS público:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verifique a instalação MongoDB ligando-a utilizando o cliente local `mongo` da seguinte forma:

```bash
mongo
```

Agora teste a ocorrência adicionando alguns dados e procurando da seguinte forma:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Crie um complexo Cluster Sharded MongoDB no CentOS usando um modelo
Você pode criar um complexo cluster mongoDB fragmentos usando o seguinte modelo de quickstart Azure do GitHub. Este modelo segue as [melhores práticas do cluster MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer redundância e alta disponibilidade. O modelo cria dois fragmentos, com três nós em cada conjunto de réplicas. Também é criado um conjunto de réplicas de servidor de config com três nós, além de dois servidores de router **mongos** para fornecer consistência às aplicações de todos os fragmentos.

* [Cluster MongoDB Sharding no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> A implantação deste complexo cluster mongoDB fragmentos requer mais de 20 núcleos, que é tipicamente a contagem de base padrão por região para uma subscrição. Abra um pedido de apoio Azure para aumentar a sua contagem de núcleos.

Para criar este ambiente, necessita do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante o modelo MongoDB com a criação de implementação do [grupo Az](/cli/azure/group/deployment). Defina os seus próprios nomes e tamanhos de recursos sempre que necessário, como para *mongoAdminUsername,* *sizeOfDataDiskInGB*e *configNodeVmSize*:

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

Esta implementação pode demorar mais de uma hora a implementar e configurar todas as instâncias vm. A `--no-wait` bandeira é utilizada no final do comando anterior para devolver o controlo ao pedido de comando uma vez que a implantação do modelo tenha sido aceite pela plataforma Azure. Em seguida, pode visualizar o estado de implantação com o programa de implantação do [grupo Az](/cli/azure/group/deployment). O exemplo que se segue vê o estado da implementação do *myMongoDBCluster* no grupo de recursos *myResourceGroup:*

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Passos seguintes
Nestes exemplos, liga-se à instância MongoDB localmente a partir do VM. Se pretender ligar-se à instância MongoDB a partir de outra VM ou rede, certifique-se de que [são criadas as regras](nsg-quickstart.md)adequadas do Grupo de Segurança da Rede .

Estes exemplos implantam o ambiente mongoDB central para fins de desenvolvimento. Aplique as opções de configuração de segurança necessárias para o seu ambiente. Para mais informações, consulte os médicos de [segurança do MongoDB](https://docs.mongodb.com/manual/security/).

Para obter mais informações sobre a criação de modelos, consulte a visão geral do [Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md).

Os modelos do Gestor de Recursos Azure usam a extensão do script personalizado para descarregar e executar scripts nos seus VMs. Para mais informações, consulte [A extensão do script personalizado Azure com máquinas virtuais Linux](extensions-customscript.md).

