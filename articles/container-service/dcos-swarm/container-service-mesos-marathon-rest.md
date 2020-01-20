---
title: PRETERIDO Gerenciar o cluster de DC/so do Azure com a API REST do Marathon
description: Implante contêineres em um cluster DC/OS do serviço de contêiner do Azure usando a API REST do amMarathon.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277789"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>PRETERIDO Gerenciamento de contêiner de DC/so por meio da API REST do amMarathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação. Embora as estruturas estejam disponíveis para muitas cargas de trabalho populares, este documento permite que você comece a criar e dimensionar implantações de contêiner usando a API REST do amMarathon. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de avançarmos, necessita de um cluster DC/OS configurado no Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

* [Implementar um cluster do Azure Container Service](container-service-deployment.md)
* [Ligar a um cluster do Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Acessar as APIs do DC/OS
Depois que você estiver conectado ao cluster do serviço de contêiner do Azure, poderá acessar o DC/so e as APIs REST relacionadas por meio de http:\//localhost: local-Port. Os exemplos neste documento partem do princípio de que está a utilizar um túnel na porta 80. Por exemplo, os pontos de extremidade Marathon podem ser acessados em URIs começando com http:\//localhost/Marathon/v2/. 

Para obter mais informações sobre as diversas APIs, consulte a documentação do Mesosphere da [API do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e da [API do Chronos](https://mesos.github.io/chronos/docs/api.html), bem como a documentação do Apache da [API do Mesos Scheduler](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Reunir informações do DC/OS e do Marathon
Antes de implantar contêineres no cluster DC/OS, reúna algumas informações sobre o cluster DC/OS, como os nomes e o status dos agentes DC/OS. Para tal, consulte o ponto final `master/slaves` da API REST do DC/OS. Se tudo correr bem, a consulta devolve uma lista de agentes DC/OS e as diversas propriedades de cada um.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, utilize o ponto final `/apps` do Marathon para verificar a existência de implementações de aplicações atuais no cluster DC/OS. Se for um cluster novo, vê uma matriz de aplicações vazia.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatado para Docker
Você implanta contêineres formatados pelo Docker por meio da API REST do amMarathon usando um arquivo JSON que descreve a implantação pretendida. O exemplo a seguir implanta um contêiner Nginx em um agente privado no cluster. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implantar um contêiner formatado do Docker, armazene o arquivo JSON em um local acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o nome do arquivo JSON (`marathon.json` neste exemplo).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

O resultado é semelhante ao seguinte:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Agora, se consultar o Marathon quanto a aplicações, esta nova aplicação é apresentada no resultado.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Alcance o contêiner

Você pode verificar se o Nginx está em execução em um contêiner em um dos agentes privados no cluster. Para localizar o host e a porta em que o contêiner está em execução, consulte Marathon para as tarefas em execução: 

```bash
curl localhost/marathon/v2/tasks
```

Localize o valor de `host` na saída (um endereço IP semelhante a `10.32.0.x`) e o valor de `ports`.


Agora, faça uma conexão de terminal SSH (não uma conexão encapsulada) para o FQDN de gerenciamento do cluster. Uma vez conectado, faça a seguinte solicitação, substituindo os valores corretos de `host` e `ports`:

```bash
curl http://host:ports
```

A saída do servidor Nginx é semelhante ao seguinte:

![Nginx do contêiner](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Dimensionar os contentores
Você pode usar a API Marathon para escalar horizontalmente ou reduzir horizontalmente as implantações de aplicativo. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

Em sua conexão encapsulada, execute o comando a seguir para escalar horizontalmente o aplicativo.

> [!NOTE]
> O URI é http:\//localhost/Marathon/v2/apps/seguido pela ID do aplicativo a ser dimensionado. Se você estiver usando o exemplo Nginx fornecido aqui, o URI seria http:\//localhost/Marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto final do Marathon quanto a aplicações. Vê que existem agora três contentores Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Comandos do PowerShell equivalentes
Pode efetuar estas mesmas ações utilizando comandos do PowerShell num sistema Windows.

Para coletar informações sobre o cluster DC/OS, como nomes de agente e status do agente, execute o seguinte comando:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Os contentores formatados para Docker são implementados através do Marathon utilizando um ficheiro JSON que descreve a implementação pretendida. O exemplo seguinte implementa o contentor Nginx, a porta de enlace 80 do agente DC/OS para a porta 80 do contentor.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Para implantar um contêiner formatado do Docker, armazene o arquivo JSON em um local acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o caminho para o arquivo JSON (`marathon.json` neste exemplo).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Também pode utilizar a API do Marathon para aumentar ou reduzir horizontalmente em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

Execute o seguinte comando para escalar horizontalmente o aplicativo:

> [!NOTE]
> O URI é http:\//localhost/Marathon/v2/apps/seguido pela ID do aplicativo a ser dimensionado. Se você estiver usando o exemplo Nginx fornecido aqui, o URI seria http:\//localhost/Marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Passos seguintes
* [Leia mais sobre os pontos de extremidade HTTP mesos](https://mesos.apache.org/documentation/latest/endpoints/)
* [Leia mais sobre a API REST do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)

