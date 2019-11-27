---
title: Configurar investigação de preparação na instância de contêiner
description: Saiba como configurar uma investigação para garantir que os contêineres nas instâncias de contêiner do Azure recebam solicitações somente quando estiverem prontos
ms.topic: article
ms.date: 10/17/2019
ms.openlocfilehash: 5ebbcdeee231e3e67abd6758485a12984137997e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533568"
---
# <a name="configure-readiness-probes"></a>Configurar pesquisas readiness

Para aplicativos em contêineres que atendem ao tráfego, talvez você queira verificar se o contêiner está pronto para lidar com solicitações de entrada. As instâncias de contêiner do Azure dão suporte a investigações de preparação para incluir configurações para que seu contêiner não possa ser acessado sob determinadas condições. A investigação de preparação se comporta como uma [investigação de preparação de kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Por exemplo, um aplicativo de contêiner pode precisar carregar um conjunto de dados grande durante a inicialização e você não deseja que ele receba solicitações durante esse tempo.

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de prontidão, para que um contêiner só receba tráfego quando a investigação tiver sucesso.

As instâncias de contêiner do Azure também dão suporte a [testes de vida](container-instances-liveness-probe.md), que podem ser configurados para fazer com que um contêiner não íntegro seja reiniciado automaticamente.

## <a name="yaml-configuration"></a>Configuração do YAML

Por exemplo, crie um arquivo de `readiness-probe.yaml` com o trecho a seguir que inclui uma investigação de prontidão. Esse arquivo define um grupo de contêineres que consiste em um contêiner que executa um pequeno aplicativo Web. O aplicativo é implantado a partir da imagem de `mcr.microsoft.com/azuredocs/aci-helloworld` pública. Esse aplicativo de contêiner também é demonstrado em guias de início rápido, como [implantar uma instância de contêiner no Azure usando o CLI do Azure](container-instances-quickstart.md).

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

### <a name="start-command"></a>Comando iniciar

O arquivo YAML inclui um comando inicial a ser executado quando o contêiner é iniciado, definido pela propriedade `command` que aceita uma matriz de cadeias de caracteres. Este comando simula uma hora em que o aplicativo Web é executado, mas o contêiner não está pronto. Primeiro, ele inicia uma sessão do Shell e executa um comando `node` para iniciar o aplicativo Web. Ele também inicia um comando para dormir por 240 segundos, após o qual ele cria um arquivo chamado `ready` dentro do diretório `/tmp`:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Comando de preparação

Esse arquivo YAML define um `readinessProbe` que dá suporte a um comando de prontidão de `exec` que atua como a verificação de preparação. Este exemplo de comando de preparação testa a existência do arquivo de `ready` no diretório `/tmp`.

Quando o arquivo de `ready` não existe, o comando de preparação é encerrado com um valor diferente de zero; o contêiner continua em execução, mas não pode ser acessado. Quando o comando for encerrado com êxito com o código de saída 0, o contêiner estará pronto para ser acessado. 

A propriedade `periodSeconds` designa que o comando de preparação deve ser executado a cada 5 segundos. A investigação de preparação é executada durante o tempo de vida do grupo de contêineres.

## <a name="example-deployment"></a>Exemplo de implantação

Execute o seguinte comando para implantar um grupo de contêineres com a configuração YAML anterior:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Exibir verificações de preparação

Neste exemplo, durante os primeiros 240 segundos, o comando de preparação falha quando verifica a existência do `ready` arquivo. O código de status retornado sinaliza que o contêiner não está pronto.

Esses eventos podem ser exibidos no portal do Azure ou CLI do Azure. Por exemplo, o portal mostra eventos do tipo `Unhealthy` são disparados na falha do comando de preparação. 

![Evento de não integridade do portal][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verificar preparação do contêiner

Depois de iniciar o contêiner, você pode verificar se ele não está acessível inicialmente. Após o provisionamento, obtenha o endereço IP do grupo de contêineres:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Tente acessar o site enquanto a investigação de prontidão falhar:

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

Após 240 segundos, o comando de preparação é executado com sucesso, sinalizando que o contêiner está pronto. Agora, quando você executa o comando `wget`, ele é bem sucedido:

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

Quando o contêiner estiver pronto, você também poderá acessar o aplicativo Web navegando até o endereço IP usando um navegador da Web.

> [!NOTE]
> A investigação de preparação continua a ser executada durante o tempo de vida do grupo de contêineres. Se o comando de preparação falhar em um momento posterior, o contêiner novamente se tornará inacessível. 
> 

## <a name="next-steps"></a>Passos seguintes

Uma investigação de prontidão pode ser útil em cenários que envolvem grupos de vários contêineres que consistem em contêineres dependentes. Para obter mais informações sobre cenários de vários contêineres, consulte [grupos de contêineres em instâncias de contêiner do Azure](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
