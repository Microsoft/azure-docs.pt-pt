---
title: (DEPRECIADO) Contentores de balanço de carga no cluster Azure DC/OS
description: Balanceamento de carga entre múltiplos contentores num cluster DC/OS do Azure Container Service.
author: rgardler
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a8f863f16888e6eca2dbc72c5dd612c38edbe46e
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273385"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(DEPRECIADO) Carregar contentores de balanço num cluster Azure Container Service DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, exploramos como criar um balanceador de carga interno num DC/OS gerido pelo Azure Container Service com o Marathon-LB. Esta configuração permite dimensionar as suas aplicações horizontalmente. Permite também tirar partido dos clusters de agentes públicos e privados, ao colocar os balanceadores de carga no cluster público e os contentores aplicacionais no cluster privado. Neste tutorial:

> [!div class="checklist"]
> * Configurar um Balanceador de Carga Marathon
> * Implementar uma aplicação com o balanceador de carga
> * Configurar um balanceador de carga do Azure

Precisa de um cluster DC/OS do ACS para concluir os passos neste tutorial. Se for necessário, [este script de exemplo](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) pode criar um para si.

Este tutorial requer a versão do módulo 2.0.4 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Descrição geral do balanceamento de carga

Existem duas camadas de balanceamento de carga num cluster DC/OS do Azure Container Service: 

O **Balanceador de Carga do Azure** fornece pontos de entrada pública (aqueles a que os utilizadores finais acedem). Um LB do Azure é fornecido automaticamente pelo Azure Container Service e está, por predefinição, configurado para expor as portas 80, 443 e 8080.

O **Balanceador de Carga Marathon (marathon lb)** encaminha pedidos de entrada para instâncias de contentor que fornecem assistência a esses pedidos. À medida que dimensionamos os contentores que fornecem o nosso serviço Web, o marathon-lb adapta-se dinamicamente. Este balanceador de carga não é fornecido por predefinição no seu Serviço de Contentor, mas é fácil instalar.

## <a name="configure-marathon-load-balancer"></a>Configurar o Balanceador de Carga Marathon

O Balanceador de Carga Marathon reconfigura-se dinamicamente, com base nos contentores que tenha implementado. É também resiliente à perda de um contentor ou de um agente. Se isto ocorrer, o Apache Mesos reinicia o contentor noutro local e o marathon-lb adapta-se.

Vá a [https://shell.azure.com](https://shell.azure.com) para abrir cloud Shell no seu navegador.

Execute o seguinte comando para instalar o balanceador de carga marathon no cluster do agente público.

```console
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Implementar a aplicação com balanceamento de carga

Agora que temos o pacote marathon-lb, podemos implementar o contentor aplicacional que queremos para balancear a carga. 

Em primeiro lugar, obtenha o FQDN dos agentes publicamente expostos.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Em seguida, crie um ficheiro com o nome *hello-web.json* e copie os seguintes conteúdos para o mesmo. A etiqueta `HAPROXY_0_VHOST` tem de ser atualizada com o FQDN dos agentes DC/OS. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Utilize a CLI de DC/OS para executar a aplicação. Por padrão, a Maratona implementa a aplicação para o cluster privado. Isto significa que a implementação acima só é acessível através do seu balanceador de carga, o que é normalmente o comportamento pretendido.

```console
dcos marathon app add hello-web.json
```

Após a implementação da aplicação, navegue para o FQDN do cluster do agente para ver a aplicação com balanceamento de carga.

![Imagem da aplicação com balanceamento de carga](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Configurar o Balanceador de Carga do Azure

Por predefinição, o Balanceador de Carga do Azure expõe as portas 80, 8080 e 443. Se utilizar uma das três seguintes portas (como podemos ver no exemplo acima) não tem de efetuar qualquer ação. Deve conseguir aceder ao FQDN do balanceador de carga do agente e, sempre que atualizar, irá aceder a um dos seus três servidores Web de uma maneira round-robin. 

Se utilizar uma porta diferente, terá de adicionar uma regra round-robin e uma sonda no balanceador de carga para a porta que utilizou. Pode fazê-lo a partir da [CLI do Azure](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), com os comandos `azure network lb rule create` e `azure network lb probe create`.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer o balanceamento de carga no ACS com os balanceadores de carga Marathon e do Azure, incluindo as seguintes ações:

> [!div class="checklist"]
> * Configurar um Balanceador de Carga Marathon
> * Implementar uma aplicação com o balanceador de carga
> * Configurar um balanceador de carga do Azure

Avance para o próximo tutorial para saber como integrar o armazenamento do Azure com o DC/OS, no Azure.

> [!div class="nextstepaction"]
> [Montar a Partilha de Ficheiros do Azure no cluster DC/OS](container-service-dcos-fileshare.md)
