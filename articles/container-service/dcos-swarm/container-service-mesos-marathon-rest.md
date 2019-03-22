---
title: (PRETERIDO) Gerir o cluster DC/OS do Azure com a API REST do Marathon
description: Implemente contentores num cluster de DC/OS do Azure Container Service com a API de REST do Marathon.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 567890f3beec1eff30effeec0ce23284c5fee141
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109295"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(PRETERIDO) Gestão de contentores DC/OS através da API de REST do Marathon

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação. Embora as estruturas estejam disponíveis para muitas cargas de trabalho populares, este documento apresenta-lhe criar e dimensionar as implementações de contentores com a API de REST do Marathon. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de avançarmos, necessita de um cluster DC/OS configurado no Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

* [Implementar um cluster do Azure Container Service](container-service-deployment.md)
* [Ligar a um cluster do Azure Container Service](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Aceder às APIs do DC/OS
Assim que estiver ligado ao cluster do Azure Container Service, pode acessar o DC/OS e APIs REST relacionadas através de http://localhost:local-port. Os exemplos neste documento partem do princípio de que está a utilizar um túnel na porta 80. Por exemplo, os pontos de extremidade do Marathon podem ser contatados pelo URIs a partir do `http://localhost/marathon/v2/`. 

Para obter mais informações sobre as diversas APIs, consulte a documentação do Mesosphere da [API do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e da [API do Chronos](https://mesos.github.io/chronos/docs/api.html), bem como a documentação do Apache da [API do Mesos Scheduler](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Reunir informações do DC/OS e do Marathon
Antes de implementar contentores no cluster DC/OS, recolha algumas informações sobre o cluster DC/OS, tais como os nomes e o estado dos agentes DC/OS. Para tal, consulte o ponto final `master/slaves` da API REST do DC/OS. Se tudo correr bem, a consulta devolve uma lista de agentes DC/OS e as diversas propriedades de cada um.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, utilize o ponto final `/apps` do Marathon para verificar a existência de implementações de aplicações atuais no cluster DC/OS. Se for um cluster novo, vê uma matriz de aplicações vazia.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatado para Docker
Implementar contentores formatados para Docker através da API de REST do Marathon utilizando um ficheiro JSON que descreve a implementação pretendida. O exemplo seguinte implementa o contentor do Nginx para um agente privado no cluster. 

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

Para implementar um contentor formatado para Docker, armazene o ficheiro JSON numa localização acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o nome do ficheiro JSON (`marathon.json` neste exemplo).

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

## <a name="reach-the-container"></a>Alcançar o contentor

Pode verificar que o Nginx está em execução num contentor de um dos agentes no cluster privados. Para localizar o anfitrião e a porta onde o contentor está em execução, consulte o Marathon quanto as tarefas em execução: 

```bash
curl localhost/marathon/v2/tasks
```

Encontrar o valor de `host` no resultado (um endereço IP semelhante `10.32.0.x`) e o valor de `ports`.


Agora fazer uma ligação de terminal SSH (não uma ligação de túnel) para a gestão FQDN do cluster. Assim que estiver ligado, fazer o pedido seguinte, substituindo os valores corretos de `host` e `ports`:

```bash
curl http://host:ports
```

A saída de servidor Nginx é semelhante ao seguinte:

![Nginx a partir do contentor](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Dimensionar os contentores
Pode utilizar a API do Marathon para aumentar ou reduzir horizontalmente em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

A partir da sua ligação de túnel, execute o seguinte comando para aumentar horizontalmente a aplicação.

> [!NOTE]
> O URI é http://localhost/marathon/v2/apps/ seguido do ID de aplicação para dimensionamento. Se estiver a utilizar o exemplo Nginx for fornecido aqui, o URI seria http://localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto final do Marathon quanto a aplicações. Vê que existem agora três contentores Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Comandos do PowerShell equivalentes
Pode efetuar estas mesmas ações utilizando comandos do PowerShell num sistema Windows.

Para recolher informações sobre o cluster DC/OS, tais como nomes de agente e o estado do agente, execute o seguinte comando:

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

Para implementar um contentor formatado para Docker, armazene o ficheiro JSON numa localização acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o caminho para o ficheiro JSON (`marathon.json` neste exemplo).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Também pode utilizar a API do Marathon para aumentar ou reduzir horizontalmente em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

Execute o seguinte comando para aumentar horizontalmente a aplicação:

> [!NOTE]
> O URI é http://localhost/marathon/v2/apps/ seguido do ID de aplicação para dimensionamento. Se estiver a utilizar o exemplo Nginx facultado aqui, o URI seria http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Passos Seguintes
* [Leia mais sobre os pontos finais HTTP do Mesos](https://mesos.apache.org/documentation/latest/endpoints/)
* [Leia mais sobre a API de REST do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html)

