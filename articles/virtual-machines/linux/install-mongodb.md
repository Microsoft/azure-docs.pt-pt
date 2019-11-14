---
title: Instalar o MongoDB em uma VM Linux com o CLI do Azure
description: Saiba como instalar e configurar o MongoDB em uma máquina virtual do Linux usando o CLI do Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 7f5fdd625eb49bfcac0bd58bca7a8415ac877517
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035999"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Como instalar e configurar o MongoDB em uma VM do Linux

O [MongoDB](https://www.mongodb.org) é um popular banco de dados NoSQL de software livre e de alto desempenho. Este artigo mostra como instalar e configurar o MongoDB em uma VM do Linux com o CLI do Azure. Exemplos são mostrados para detalhar como:

* [Instalar e configurar manualmente uma instância básica do MongoDB](#manually-install-and-configure-mongodb-on-a-vm)
* [Criar uma instância básica do MongoDB usando um modelo do Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Criar um cluster fragmentado do MongoDB complexo com conjuntos de réplicas usando um modelo do Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Instalar e configurar manualmente o MongoDB em uma VM
O MongoDB [fornece instruções de instalação](https://docs.mongodb.com/manual/administration/install-on-linux/) para Linux distribuições incluindo Red Hat/CentOS, Suse, Ubuntu e Debian. O exemplo a seguir cria uma VM *CentOS* . Para criar esse ambiente, você precisa das [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo a seguir cria uma VM chamada *myVM* com um usuário chamado *azureuser* usando a autenticação de chave pública SSH

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a VM usando seu próprio nome de usuário e o `publicIpAddress` listados na saída da etapa anterior:

```bash
ssh azureuser@<publicIpAddress>
```

Para adicionar as fontes de instalação do MongoDB, crie um arquivo de repositório **yum** da seguinte maneira:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Abra o arquivo de repositório do MongoDB para edição, como com `vi` ou `nano`. Adicione as seguintes linhas:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Instale o MongoDB usando **yum** da seguinte maneira:

```bash
sudo yum install -y mongodb-org
```

Por padrão, o SELinux é imposto em imagens CentOS que impedem que você acesse o MongoDB. Instale as ferramentas de gerenciamento de política e configure o SELinux para permitir que o MongoDB opere em sua porta TCP padrão 27017 da seguinte maneira:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Inicie o serviço MongoDB da seguinte maneira:

```bash
sudo service mongod start
```

Verifique a instalação do MongoDB conectando-se usando o cliente local `mongo`:

```bash
mongo
```

Agora, teste a instância do MongoDB adicionando alguns dados e, em seguida, pesquisando:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Se desejar, configure o MongoDB para iniciar automaticamente durante uma reinicialização do sistema:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Criar uma instância básica do MongoDB no CentOS usando um modelo
Você pode criar uma instância básica do MongoDB em uma única VM CentOS usando o seguinte modelo de início rápido do Azure do GitHub. Este modelo usa a extensão de script personalizado para Linux para adicionar um repositório **yum** à sua VM CentOS recém-criada e, em seguida, instalar o MongoDB.

* [Instância básica do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Para criar esse ambiente, você precisa das [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante o modelo do MongoDB com [AZ Group Deployment Create](/cli/azure/group/deployment). Quando solicitado, insira seus próprios valores exclusivos para *newStorageAccountName*, *dnsNameForPublicIP*e nome de usuário e senha de administrador:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Faça logon na VM usando o endereço DNS público da sua VM. Você pode exibir o endereço DNS público com [AZ VM show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH para sua VM usando seu próprio nome de usuário e endereço DNS público:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verifique a instalação do MongoDB conectando-se usando o cliente `mongo` local da seguinte maneira:

```bash
mongo
```

Agora, teste a instância adicionando alguns dados e pesquisando da seguinte maneira:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Criar um cluster fragmentado do MongoDB complexo no CentOS usando um modelo
Você pode criar um cluster fragmentado do MongoDB complexo usando o seguinte modelo de início rápido do Azure do GitHub. Este modelo segue as [práticas recomendadas de cluster fragmentado do MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) para fornecer redundância e alta disponibilidade. O modelo cria dois fragmentos, com três nós em cada conjunto de réplicas. Um conjunto de réplicas do servidor de configuração com três nós também é criado, além de dois servidores de roteador **mongos** para fornecer consistência aos aplicativos de todos os fragmentos.

* [Cluster de fragmentação do MongoDB no CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> A implantação desse cluster fragmentado MongoDB complexo requer mais de 20 núcleos, que normalmente é a contagem de núcleos padrão por região para uma assinatura. Abra uma solicitação de suporte do Azure para aumentar sua contagem de núcleos.

Para criar esse ambiente, você precisa das [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas a uma conta do Azure usando [AZ login](/cli/azure/reference-index). Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Em seguida, implante o modelo do MongoDB com [AZ Group Deployment Create](/cli/azure/group/deployment). Defina seus próprios nomes e tamanhos de recursos, quando necessário, como para *mongoAdminUsername*, *sizeOfDataDiskInGB*e *configNodeVmSize*:

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

Essa implantação pode levar mais de uma hora para implantar e configurar todas as instâncias de VM. O sinalizador `--no-wait` é usado no final do comando anterior para retornar o controle ao prompt de comando depois que a implantação do modelo for aceita pela plataforma do Azure. Em seguida, você pode exibir o status da implantação com [AZ Group Deployment show](/cli/azure/group/deployment). O exemplo a seguir exibe o status da implantação *myMongoDBCluster* no grupo de recursos *MyResource* Group:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Passos seguintes
Nesses exemplos, você se conecta à instância do MongoDB localmente a partir da VM. Se você quiser se conectar à instância do MongoDB de outra VM ou rede, verifique se as [regras de grupo de segurança de rede apropriadas foram criadas](nsg-quickstart.md).

Esses exemplos implantam o ambiente do MongoDB principal para fins de desenvolvimento. Aplique as opções de configuração de segurança necessárias para seu ambiente. Para obter mais informações, consulte os [documentos de segurança do MongoDB](https://docs.mongodb.com/manual/security/).

Para obter mais informações sobre como criar usando modelos, consulte a [visão geral de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Os modelos de Azure Resource Manager usam a extensão de script personalizado para baixar e executar scripts em suas VMs. Para obter mais informações, consulte [usando a extensão de script personalizado do Azure com máquinas virtuais do Linux](extensions-customscript.md).

