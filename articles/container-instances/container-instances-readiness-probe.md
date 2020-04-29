---
title: Configurar sonda de prontidão na instância do recipiente
description: Saiba como configurar uma sonda para garantir que os contentores em Casos de Contentores Azure recebam pedidos apenas quando estiverem prontos
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76935683"
---
# <a name="configure-readiness-probes"></a>Configurar pesquisas readiness

Para aplicações contentorizadas que servem o tráfego, é melhor verificar se o seu contentor está pronto para lidar com os pedidos de entrada. As instâncias de contentores Azure suportam sondas de prontidão para incluir configurações para que o seu recipiente não possa ser acedido sob determinadas condições. A sonda de prontidão comporta-se como uma sonda de [prontidão kubernetes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) Por exemplo, uma aplicação de contentores pode precisar de carregar um grande conjunto de dados durante o arranque, e você não quer que receba pedidos durante este tempo.

Este artigo explica como implantar um grupo de contentores que inclui uma sonda de prontidão, para que um contentor só receba tráfego quando a sonda for bem sucedida.

As instâncias de contentores Azure também suportam sondas de [vivacidade,](container-instances-liveness-probe.md)que pode configurar para fazer com que um recipiente pouco saudável reinicie automaticamente.

> [!NOTE]
> Atualmente não é possível utilizar uma sonda de prontidão num grupo de contentores implantado numa rede virtual.

## <a name="yaml-configuration"></a>Configuração YAML

Como exemplo, crie um `readiness-probe.yaml` ficheiro com o seguinte corte que inclua uma sonda de prontidão. Este ficheiro define um grupo de contentores que consiste num contentor que executa uma pequena aplicação web. A aplicação é implantada a partir da imagem pública. `mcr.microsoft.com/azuredocs/aci-helloworld` Esta aplicação contentorizada também é demonstrada na [implantação de um contentor em Azure utilizando o Azure CLI](container-instances-quickstart.md) e outros quickstarts.

```yaml
apiVersion: 2018-10-01
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

A implementação `command` inclui uma propriedade que define um comando inicial que funciona quando o recipiente começa a funcionar. Esta propriedade aceita uma variedade de cordas. Este comando simula uma época em que a aplicação web funciona, mas o recipiente não está pronto. 

Primeiro, inicia uma sessão `node` de conchas e executa um comando para iniciar a aplicação web. Também inicia um comando para dormir durante 240 segundos, após o qual cria um ficheiro chamado `ready` dentro do `/tmp` diretório:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando de prontidão

Este ficheiro YAML `readinessProbe` define um `exec` que suporta um comando de prontidão que funciona como a verificação de prontidão. Este exemplo de comando de prontidão testa para a existência do `ready` ficheiro no `/tmp` diretório.

Quando `ready` o ficheiro não existe, o comando de prontidão sai com um valor não zero; o recipiente continua a funcionar, mas não pode ser acedido. Quando o comando sai com sucesso com o código de saída 0, o recipiente está pronto para ser acedido. 

A `periodSeconds` propriedade designa o comando de prontidão deve executar a cada 5 segundos. A sonda de prontidão funciona durante toda a vida do grupo de contentores.

## <a name="example-deployment"></a>Implantação de exemplo

Executar o seguinte comando para implantar um grupo de contentores com a configuração YAML anterior:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Ver verificações de prontidão

Neste exemplo, durante os primeiros 240 segundos, o comando `ready` de prontidão falha quando verifica a existência do ficheiro. O código de estado devolveu sinais de que o contentor não está pronto.

Estes eventos podem ser vistos a partir do portal Azure ou Do CLI Azure. Por exemplo, o portal `Unhealthy` mostra que os eventos de tipo são desencadeados após a falha do comando de prontidão. 

![Portal evento pouco saudável][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verificar a prontidão do contentor

Depois de iniciar o recipiente, pode verificar se não está acessível inicialmente. Após o fornecimento, obtenha o endereço IP do grupo de contentores:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Tente aceder ao site enquanto a sonda de prontidão falha:

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

Após 240 segundos, o comando de prontidão é bem sucedido, sinalizando que o recipiente está pronto. Agora, quando dirige `wget` o comando, tem sucesso:

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

Quando o recipiente estiver pronto, também pode aceder à aplicação web navegando para o endereço IP através de um navegador web.

> [!NOTE]
> A sonda de prontidão continua a funcionar durante toda a vida do grupo de contentores. Se o comando de prontidão falhar mais tarde, o recipiente torna-se novamente inacessível. 
> 

## <a name="next-steps"></a>Passos seguintes

Uma sonda de prontidão pode ser útil em cenários que envolvam grupos multi-contentores que consistem em contentores dependentes. Para obter mais informações sobre cenários multi-contentores, consulte grupos de [contentores em instâncias de contentores de Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
