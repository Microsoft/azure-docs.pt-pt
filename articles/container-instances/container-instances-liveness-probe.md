---
title: Configurar testes de vida em instâncias de contêiner do Azure
description: Saiba como configurar testes de vida para reiniciar contêineres não íntegros em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 7f9696e9803e9ab168c59b6c5e7413a4f754a6ae
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904436"
---
# <a name="configure-liveness-probes"></a>Configurar as pesquisas liveness

Os aplicativos em contêineres podem ser executados por longos períodos de tempo, resultando em Estados desfeitos que talvez precisem ser reparados reiniciando o contêiner. As instâncias de contêiner do Azure dão suporte a investigações de vida para que você possa configurar seus contêineres dentro do grupo de contêineres para reiniciar se a funcionalidade crítica não estiver funcionando. A investigação de tempo de vida se comporta como uma [investigação de vida kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de tempo de vida, demonstrando a reinicialização automática de um contêiner não íntegro simulado.

As instâncias de contêiner do Azure também dão suporte a [investigações de preparação](container-instances-readiness-probe.md), que podem ser configuradas para garantir que o tráfego atinja um contêiner somente quando ele estiver pronto para ele.

## <a name="yaml-deployment"></a>Implantação do YAML

Crie um arquivo de `liveness-probe.yaml` com o trecho a seguir. Esse arquivo define um grupo de contêineres que consiste em um contêiner NGNIX que eventualmente se torna não íntegro.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Execute o seguinte comando para implantar esse grupo de contêineres com a configuração YAML acima:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Comando iniciar

A implantação define um comando inicial a ser executado quando o contêiner começa a ser executado pela primeira vez, definido pela propriedade `command`, que aceita uma matriz de cadeias de caracteres. Neste exemplo, ele iniciará uma sessão de bash e criará um arquivo chamado `healthy` dentro do diretório `/tmp` passando este comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Em seguida, ele será suspenso por 30 segundos antes de excluir o arquivo e, em seguida, entrará em uma suspensão de 10 minutos.

### <a name="liveness-command"></a>Comando de vida

Essa implantação define um `livenessProbe` que dá suporte a um comando de `exec` de tempo de vida que atua como a verificação de tempo de vida. Se esse comando for encerrado com um valor diferente de zero, o contêiner será encerrado e reiniciado, sinalizando que o arquivo de `healthy` não pôde ser encontrado. Se esse comando for encerrado com êxito com o código de saída 0, nenhuma ação será executada.

A propriedade `periodSeconds` designa que o comando de vida deve ser executado a cada 5 segundos.

## <a name="verify-liveness-output"></a>Verificar a saída de vida

Nos primeiros 30 segundos, o arquivo de `healthy` criado pelo comando Iniciar existe. Quando o comando de tempo de vida verifica a existência do `healthy` arquivo, o código de status retorna um zero, sinalizando o êxito e, portanto, não ocorre uma reinicialização.

Após 30 segundos, a `cat /tmp/healthy` começará a falhar, fazendo com que eventos não íntegros e de eliminação ocorram.

Esses eventos podem ser exibidos no portal do Azure ou CLI do Azure.

![Evento de não integridade do portal][portal-unhealthy]

Ao exibir os eventos no portal do Azure, os eventos do tipo `Unhealthy` serão disparados após a falha do comando de vida. O evento subsequente será do tipo `Killing`, significando uma exclusão de contêiner para que uma reinicialização possa começar. A contagem de reinicialização para o contêiner é incrementada toda vez que esse evento ocorre.

As reinicializações são concluídas no local para que os recursos como endereços IP públicos e conteúdos específicos do nó sejam preservados.

![Contador de reinicialização do portal][portal-restart]

Se a investigação de vida falhar continuamente e disparar muitas reinicializações, o contêiner entrará em um atraso de retirada exponencial.

## <a name="liveness-probes-and-restart-policies"></a>Testes de vida e políticas de reinicialização

As políticas de reinicialização substituem o comportamento de reinicialização acionado por investigações de vida. Por exemplo, se você definir um `restartPolicy = Never` *e* uma investigação de tempo de vida, o grupo de contêineres não será reiniciado devido a uma verificação de falha de vida. Em vez disso, o grupo de contêineres aderirá à política de reinicialização do grupo de contêineres de `Never`.

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas podem exigir uma investigação de vida para habilitar reinicializações automáticas se uma função de pré-requisito não estiver funcionando corretamente. Para obter mais informações sobre como executar contêineres baseados em tarefas, consulte [executar tarefas em contêineres em instâncias de contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
