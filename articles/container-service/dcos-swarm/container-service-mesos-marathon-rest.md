---
title: (DEPRECIADO) Gerir o cluster Azure DC/OS com Marathon REST API
description: Desloque os contentores para um cluster de serviço de contentores Azure DC/OS utilizando a Marathon REST API.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3492f35d54dd3ee61ab8d29a3af06e4998bbd477
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277789"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(DEPRECIADO) Gestão de contentores DC/OS através da Marathon REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação. Embora os quadros estejam disponíveis para muitas cargas de trabalho populares, este documento faz com que você tenha começado a criar e escalar as implementações de contentores usando a Marathon REST API. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de avançarmos, necessita de um cluster DC/OS configurado no Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

* [Implementar um cluster do Serviço de Contentor do Azure](container-service-deployment.md)
* [Ligar a um cluster do Serviço de Contentor do Azure](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Aceder às APIs DC/OS
Depois de estar ligado ao cluster do Serviço de Contentores Azure, pode\/aceder ao DC/OS e às APIs de REST relacionadas através de http: /localhost:local-port. Os exemplos neste documento partem do princípio de que está a utilizar um túnel na porta 80. Por exemplo, os pontos finais da Maratona podem\/ser alcançados em URIs começando com http: /localhost/maratona/v2/. 

Para obter mais informações sobre as diversas APIs, consulte a documentação do Mesosphere da [API do Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) e da [API do Chronos](https://mesos.github.io/chronos/docs/api.html), bem como a documentação do Apache da [API do Mesos Scheduler](https://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Reunir informações do DC/OS e do Marathon
Antes de colocar contentores no cluster DC/OS, recolha algumas informações sobre o cluster DC/OS, tais como os nomes e o estado dos agentes DC/OS. Para tal, consulte o ponto final `master/slaves` da API REST do DC/OS. Se tudo correr bem, a consulta devolve uma lista de agentes DC/OS e as diversas propriedades de cada um.

```bash
curl http://localhost/mesos/master/slaves
```

Agora, utilize o ponto final `/apps` do Marathon para verificar a existência de implementações de aplicações atuais no cluster DC/OS. Se for um cluster novo, vê uma matriz de aplicações vazia.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatado para Docker
Você implanta recipientes formatados por Docker através da Marathon REST API usando um ficheiro JSON que descreve a implementação pretendida. A amostra seguinte implanta um recipiente Nginx para um agente privado no cluster. 

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

Para implantar um recipiente formatado docker, guarde o ficheiro JSON num local acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o nome do`marathon.json` ficheiro JSON (neste exemplo).

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

## <a name="reach-the-container"></a>Chegar ao recipiente

Pode verificar se o Nginx está a funcionar num contentor num dos agentes privados do agrupamento. Para encontrar o hospedeiro e o porto onde o contentor está em funcionamento, consulta maratona para as tarefas de corrida: 

```bash
curl localhost/marathon/v2/tasks
```

Encontre o `host` valor da saída (um `10.32.0.x`endereço IP semelhante `ports`ao), e o valor de .


Faça agora uma ligação terminal SSH (não uma ligação túnel) à gestão FQDN do cluster. Uma vez ligados, faça o seguinte `host` pedido, substituindo os valores corretos de e: `ports`

```bash
curl http://host:ports
```

A saída do servidor Nginx é semelhante à seguinte:

![Nginx do recipiente](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Dimensionar os contentores
Pode utilizar a Marathon API para escalar ou escalar em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

A partir da sua ligação de túnel, execute o seguinte comando para escalar a aplicação.

> [!NOTE]
> O URI é\/http: /localhost/maratona/v2/apps/ seguido do ID da aplicação à escala. Se estiver a utilizar a amostra Nginx que é\/fornecida aqui, o URI seria http: /localhost/marathon/v2/apps/nginx.

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Por fim, consulte o ponto final do Marathon quanto a aplicações. Vê que existem agora três contentores Nginx.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Comandos do PowerShell equivalentes
Pode efetuar estas mesmas ações utilizando comandos do PowerShell num sistema Windows.

Para recolher informações sobre o cluster DC/OS, tais como nomes de agentes e estatuto de agente, executar o seguinte comando:

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

Para implantar um recipiente formatado docker, guarde o ficheiro JSON num local acessível. Em seguida, para implementar o contentor, execute o comando seguinte. Especifique o caminho para`marathon.json` o ficheiro JSON (neste exemplo).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Também pode utilizar a API do Marathon para aumentar ou reduzir horizontalmente em implementações de aplicações. No exemplo anterior, foi implementada uma instância de uma aplicação. Vamos aumentar horizontalmente para três instâncias de uma aplicação. Para tal, crie um ficheiro JSON utilizando o seguinte texto JSON e armazene-o numa localização acessível.

```json
{ "instances": 3 }
```

Executar o seguinte comando para escalar a aplicação:

> [!NOTE]
> O URI é\/http: /localhost/maratona/v2/apps/ seguido do ID da aplicação à escala. Se estiver a utilizar a amostra Nginx fornecida\/aqui, o URI seria http: /localhost/maratona/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Passos seguintes
* [Leia mais sobre os pontos finais do Mesos HTTP](https://mesos.apache.org/documentation/latest/endpoints/)
* [Leia mais sobre a Maratona REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

