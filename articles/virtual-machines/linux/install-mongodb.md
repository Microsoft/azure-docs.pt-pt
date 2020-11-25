---
title: Instale o MongoDB num Linux VM com o Azure CLI
description: Saiba como instalar e configurar o MongoDB numa máquina virtual Linux que indo ao Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49a0e48977393aeab7ff93b79e28acc55a87b51a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016187"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Como instalar e configurar o MongoDB num Linux VM

[MongoDB](https://www.mongodb.org) é uma base de dados noSQL de alta origem. Este artigo mostra-lhe como instalar e configurar o MongoDB num Linux VM com o Azure CLI. São mostrados exemplos que detalham como:

* [Instale e configuure manualmente uma instância básica do MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Crie um exemplo básico do MongoDB usando um modelo de Gestor de Recursos](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Crie um complexo cluster de cacos MongoDB com conjuntos de réplicas usando um modelo de Gestor de Recursos](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instale e configuure manualmente o MongoDB num VM
O MongoDB [fornece instruções](https://docs.mongodb.com/manual/administration/install-on-linux/) de instalação para distros Linux, incluindo Chapéu Vermelho / CentOS, SUSE, Ubuntu e Debian. O exemplo a seguir cria um *CentOS* VM. Para criar este ambiente, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria um VM chamado *myVM* com um utilizador chamado *azureuser* usando a autenticação de chave pública SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para o VM usando o seu próprio nome de utilizador e o `publicIpAddress` listado na saída do passo anterior:

```bash
ssh azureuser@<publicIpAddress>
```

Para adicionar as fontes de instalação para o MongoDB, crie um ficheiro de repositório **yum** da seguinte forma:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Abra o ficheiro de repo mongoDB para edição, como `vi` com ou `nano` . Adicione as seguintes linhas:

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

Por predefinição, o SELinux é aplicado em imagens CentOS que o impedem de aceder ao MongoDB. Instale ferramentas de gestão de políticas e configuure a SELinux para permitir que a MongoDB opere na sua porta TCP 27017 padrão da seguinte forma:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço MongoDB da seguinte forma:

```bash
sudo service mongod start
```

Verifique a instalação do MongoDB ligando-o utilizando o `mongo` cliente local:

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

Se desejar, configura o MongoDB para iniciar automaticamente durante um reboot do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Crie uma instância básica do MongoDB no CentOS usando um modelo
Você pode criar uma instância mongoDB básica em um único CentOS VM usando o modelo de arranque rápido Azure seguinte do GitHub. Este modelo utiliza a extensão de Script Personalizado para o Linux adicionar um repositório **yum** ao seu VM CentOS recém-criado e, em seguida, instalar o MongoDB.

* [Instância básica do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Para criar este ambiente, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implemente o modelo MongoDB com [a criação de grupo az](/cli/azure/group/deployment). Quando solicitado, insira os seus próprios valores únicos para *newStorageAccountName*, *dnsNameForPublicIP*, e nome de utilizador e senha de administração:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Inicie sessão no VM utilizando o endereço DNS público do seu VM. Pode ver o endereço DNS público com [az vm show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH para o seu VM usando o seu próprio nome de utilizador e endereço DNS público:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verifique a instalação do MongoDB ligando-o utilizando o cliente local `mongo` da seguinte forma:

```bash
mongo
```

Agora teste o caso adicionando alguns dados e pesquisando da seguinte forma:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Crie um complexo Cluster Sharded MongoDB no CentOS usando um modelo
Você pode criar um complexo cluster de aço mongoDB usando o modelo de arranque rápido Azure seguinte do GitHub. Este modelo segue as [melhores práticas do cluster de MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para proporcionar redundância e alta disponibilidade. O modelo cria dois fragmentos, com três nós em cada conjunto de réplicas. Um conjunto de réplica de servidor config com três nós também é criado, além de dois servidores de router **mongos** para fornecer consistência às aplicações de todos os fragmentos.

* [Cluster de Fragmentos de MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> A implementação deste complexo cluster de aço mongoDB requer mais de 20 núcleos, que é tipicamente a contagem de núcleo padrão por região para uma subscrição. Abra um pedido de apoio Azure para aumentar a sua contagem de núcleos.

Para criar este ambiente, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implemente o modelo MongoDB com [a criação de grupo az](/cli/azure/group/deployment). Defina os seus próprios nomes e tamanhos de recursos sempre que necessário, tais como *para mongoAdminUsername,* *tamanhoSDiskInGB,* e *configNodeVmSize:*

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

Esta implantação pode demorar mais de uma hora a implantar e configurar todas as instâncias VM. A `--no-wait` bandeira é utilizada no final do comando anterior para devolver o controlo à solicitação de comando uma vez que a implantação do modelo tenha sido aceite pela plataforma Azure. Em seguida, pode visualizar o estado de implantação com [o show de implantação do grupo AZ](/cli/azure/group/deployment). O exemplo a seguir vê o estado da implantação *myMongoDBCluster* no grupo de recursos *myResourceGroup:*

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Passos seguintes
Nestes exemplos, liga-se ao caso MongoDB localmente a partir do VM. Se pretender ligar-se à instância MongoDB a partir de outra VM ou rede, certifique-se de que as regras adequadas [do Grupo de Segurança da Rede são criadas](nsg-quickstart.md).

Estes exemplos implantam o ambiente mongoDB central para fins de desenvolvimento. Aplique as opções de configuração de segurança necessárias para o seu ambiente. Para mais informações, consulte os [documentos de segurança do MongoDB.](https://docs.mongodb.com/manual/security/)

Para obter mais informações sobre a criação de modelos, consulte a [visão geral do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md).

Os modelos do Gestor de Recursos Azure usam a Extensão de Script Personalizada para descarregar e executar scripts nos seus VMs. Para obter mais informações, consulte [utilizar a extensão de script personalizada Azure com máquinas virtuais Linux](../extensions/custom-script-linux.md).
