---
title: Configurar sonda de prontidão na instância do recipiente
description: Saiba como configurar uma sonda para garantir que os contentores em Azure Container Instances recebem pedidos apenas quando estão prontos
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169668"
---
# <a name="configure-readiness-probes"></a>Configurar pesquisas readiness

Para aplicações contentorizadas que servem o tráfego, é melhor verificar se o seu recipiente está pronto para lidar com os pedidos de entrada. Azure Container Instances suporta sondas de prontidão para incluir configurações para que o seu recipiente não possa ser acedido sob determinadas condições. A sonda de prontidão comporta-se como uma [sonda de prontidão Kubernetes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) Por exemplo, uma aplicação de contentores pode precisar de carregar um grande conjunto de dados durante o arranque, e não pretende que ela receba pedidos durante este tempo.

Este artigo explica como implantar um grupo de contentores que inclui uma sonda de prontidão, de modo que um recipiente só recebe tráfego quando a sonda tiver sucesso.

Azure Container Instances também suporta [sondas de vida,](container-instances-liveness-probe.md)que pode configurar para fazer com que um recipiente insalubre reinicie automaticamente.

> [!NOTE]
> Atualmente não é possível utilizar uma sonda de prontidão num grupo de contentores implantado numa rede virtual.

## <a name="yaml-configuration"></a>Configuração YAML

Como exemplo, crie um `readiness-probe.yaml` ficheiro com o seguinte corte que inclua uma sonda de prontidão. Este ficheiro define um grupo de contentores que consiste num contentor que executa uma pequena aplicação web. A aplicação é implementada a partir da `mcr.microsoft.com/azuredocs/aci-helloworld` imagem pública. Esta aplicação contentorizada também é demonstrada no [Deploy a container instance in Azure usando o Azure CLI](container-instances-quickstart.md) e outros quickstarts.

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Iniciar o comando

A implantação inclui uma `command` propriedade que define um comando inicial que funciona quando o recipiente começa a funcionar pela primeira vez. Esta propriedade aceita uma variedade de cordas. Este comando simula uma altura em que a aplicação web funciona, mas o contentor não está pronto. 

Primeiro, inicia uma sessão de shell e executa um `node` comando para iniciar a aplicação web. Também inicia um comando para dormir durante 240 segundos, após o qual cria um ficheiro chamado `ready` dentro do `/tmp` diretório:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando de prontidão

Este ficheiro YAML define um `readinessProbe` que suporta um comando de `exec` prontidão que funciona como a verificação de prontidão. Este exemplo de testes de comando de prontidão para a existência do `ready` ficheiro no `/tmp` diretório.

Quando o `ready` ficheiro não existe, o comando de prontidão sai com um valor não nulo; o recipiente continua a funcionar mas não pode ser acedido. Quando o comando sair com sucesso com o código de saída 0, o recipiente está pronto para ser acedido. 

A `periodSeconds` propriedade designa o comando de prontidão deve executar a cada 5 segundos. A sonda de prontidão dura a vida do grupo de contentores.

## <a name="example-deployment"></a>Implantação de exemplo

Executar o seguinte comando para implantar um grupo de contentores com a configuração YAML anterior:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Ver verificações de prontidão

Neste exemplo, durante os primeiros 240 segundos, o comando de prontidão falha quando verifica a `ready` existência do ficheiro. O código de estado devolveu sinais de que o contentor não está pronto.

Estes eventos podem ser vistos a partir do portal Azure ou Azure CLI. Por exemplo, o portal mostra que eventos de tipo `Unhealthy` são desencadeados por falha no comando de prontidão. 

![Evento insalubre do portal][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verificar a prontidão do contentor

Depois de iniciar o recipiente, pode verificar se não está acessível inicialmente. Após o provisionamento, obtenha o endereço IP do grupo de contentores:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Tente aceder ao site enquanto a sonda de preparação falha:

```bash
wget <ipAddress>
```

A saída mostra que o site não está acessível inicialmente:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Após 240 segundos, o comando de prontidão tem sucesso, sinalizando que o recipiente está pronto. Agora, quando se dirige o `wget` comando, tem sucesso:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Quando o recipiente estiver pronto, também pode aceder à aplicação web navegando para o endereço IP utilizando um navegador web.

> [!NOTE]
> A sonda de prontidão continua a funcionar durante toda a vida do grupo de contentores. Se o comando de prontidão falhar mais tarde, o recipiente volta a ficar inacessível. 
> 

## <a name="next-steps"></a>Passos seguintes

Uma sonda de prontidão pode ser útil em cenários que envolvam grupos multi-contentores que consistem em contentores dependentes. Para obter mais informações sobre cenários de vários contentores, consulte [os grupos de contentores em instâncias de contentores Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
