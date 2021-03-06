---
title: Implemente o ElasticSearch numa máquina virtual de desenvolvimento no Azure
description: Instale a Pilha Elástica (ALCE) num desenvolvimento Linux VM em Azure
services: virtual-machines
author: rloutlaw
manager: justhe
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 3ce28efa30b04a2a762c39f47dac739633eac209
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818481"
---
# <a name="install-the-elastic-stack-elk-on-an-azure-vm"></a>Instale a Pilha Elástica (ALCE) num VM Azure

Este artigo explica como implementar o [Elasticsearch](https://www.elastic.co/products/elasticsearch), o [Logstash](https://www.elastic.co/products/logstash) e o [Kibana](https://www.elastic.co/products/kibana) numa VM do Ubuntu no Azure. Para ver a Pilha Elástica em ação, opcionalmente, pode ligar ao Kibana e trabalhar com alguns dados de registo de exemplo. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu num grupo de recursos do Azure
> * Instale o Elasticsearch, o Logstash e o Kibana na VM
> * Enviar dados de exemplo para o Elasticsearch com o Logstash 
> * Abrir portas e trabalhar com dados na consola do Kibana


 Esta implementação é adequada para o desenvolvimento básico com a Pilha Elástica. Para obter mais informações sobre a Pilha Elástica, incluindo as recomendações para um ambiente de produção, veja a [Documentação elástica](https://www.elastic.co/guide/index.html) e o [Centro de Arquitetura do Azure](/azure/architecture/elasticsearch/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [z vm create](/cli/azure/vm). 

O exemplo seguinte cria uma VM com o nome *myVM* e cria chaves SSH caso estas ainda não existam numa localização chave predefinida. Para utilizar um conjunto específico de chaves, utilize a opção `--ssh-key-value`.  

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo. Tome nota do `publicIpAddress`. Este endereço é utilizado para aceder à VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Se ainda não souber o endereço IP público da sua VM, execute o comando [az network public-ip list](/cli/azure/network/public-ip):

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Utilize o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP público correto da sua máquina virtual. Neste exemplo, o endereço IP é *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Instalar a Pilha Elástica

Importe a chave de assinatura do Elasticsearch e atualize a sua lista de origens APT para incluir o repositório do pacote Elástico:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Instale o Java Virtual na VM e configure a variável JAVA_HOME - isto é preciso para os componentes da Pilha Elástica serem executados.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Execute os seguintes comandos para atualizar as origens do pacote Ubuntu e instale o Elasticsearch, o Kibana e o Logstash.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> As instruções de instalação detalhadas, incluindo os esquemas de diretório e a configuração inicial, são mantidas na [Documentação do Elástico](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Iniciar o Elasticsearch 

Inicie o Elasticsearch na VM com o seguinte comando:

```bash
sudo systemctl start elasticsearch.service
```

Este comando não produz nenhuma saída, por isso certifique-se de que o Elasticsearch está em execução na VM com este comando `curl`:

```bash
sudo curl -XGET 'localhost:9200/'
```

Se o Elasticsearch estiver em execução, veja o resultado da seguinte forma:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Iniciar o Logstash e adicionar dados ao Elasticsearch

Inicie o Logstash com o seguinte comando:

```bash 
sudo systemctl start logstash.service
```

Teste o Logstash no modo interativo para se certificar de que está a funcionar corretamente:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Este é um [pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) básico de Logstash que ecoa a entrada padrão para a saída padrão. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Configure o Logstash para reencaminhar as mensagens de kernel desta VM para o Elasticsearch. Crie um novo ficheiro num diretório vazio denominado `vm-syslog-logstash.conf` e cole na seguinte configuração do Logstash:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Teste esta configuração e envie os dados do syslog para o Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Verá as entradas do syslog no seu terminal ecoadas à medida que são enviadas para o Elasticsearch. Utilize `CTRL+C` para sair do Logstash, assim que tiver enviado alguns dados.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Iniciar o Kibana e visualizar os dados no Elasticsearch

Edite `/etc/kibana/kibana.yml` e altere o endereço IP do Kibana, para que possa aceder a partir do seu browser.

```bash
server.host: "0.0.0.0"
```

Inicie o Kibana com o seguinte comando:

```bash
sudo systemctl start kibana.service
```

Abra a porta 5601 partir da CLI do Azure para permitir o acesso remoto à consola do Kibana:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Abra a consola do Kibana e selecione **Criar**, para gerar um índice predefinido com base nos dados do syslog enviados anteriormente para o Elasticsearch . 

![Screenshot que mostra a consola Kibana e destaca o botão Criar.](media/elasticsearch-install/kibana-index.png)

Selecione **Detetar** na consola do Kibana para pesquisar, procurar e filtrar nos eventos do syslog.

![Procurar eventos do Syslog no Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou a Pilha Elástica numa VM de desenvolvimento no Azure. Aprendeu a:

> [!div class="checklist"]
> * Criar uma VM do Ubuntu num grupo de recursos do Azure
> * Instale o Elasticsearch, o Logstash e o Kibana na VM
> * Enviar dados de exemplo para o Elasticsearch a partir do Logstash 
> * Abrir portas e trabalhar com dados na consola do Kibana
