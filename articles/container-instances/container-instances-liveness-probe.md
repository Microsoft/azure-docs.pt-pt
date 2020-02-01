---
title: Configurar sonda de vivacidade na instância do recipiente
description: Saiba como configurar sondas de vivacidade para reiniciar recipientes pouco saudáveis em Instâncias de Contentores De Azure
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 566f7952aff1cf460272fbb418a2a0efff411881
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901908"
---
# <a name="configure-liveness-probes"></a>Configurar as pesquisas liveness

As aplicações contentorizadas podem ser executadas por longos períodos de tempo, resultando em estados avariados que podem ter de ser reparados reiniciando o recipiente. As instâncias de contentores Azure suportam sondas de vivacidade para que possa configurar os seus contentores dentro do seu grupo de contentores para reiniciar se a funcionalidade crítica não estiver a funcionar. A sonda de vivacidade comporta-se como uma [sonda kubernetes de vivacidade.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Este artigo explica como implantar um grupo de contentores que inclui uma sonda de vivacidade, demonstrando o reinício automático de um recipiente simulado e pouco saudável.

As instâncias de contentores Azure também suportam sondas de [prontidão,](container-instances-readiness-probe.md)que pode configurar para garantir que o tráfego chega a um contentor apenas quando está pronto para o mesmo.

> [!NOTE]
> Atualmente não é possível utilizar uma sonda de vivacidade num grupo de contentores implantado numa rede virtual.

## <a name="yaml-deployment"></a>Implantação yAML

Crie um ficheiro `liveness-probe.yaml` com o seguinte corte. Este ficheiro define um grupo de contentores que consiste num contentor NGNIX que eventualmente se torna insalubre.

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

Executar o seguinte comando para implantar este grupo de contentores com a configuração YAML acima:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Iniciar o comando

A implementação define um comando inicial a ser executado quando o recipiente começa a funcionar, definido pela propriedade `command`, que aceita uma variedade de cordas. Neste exemplo, iniciará uma sessão de bash e criará um ficheiro chamado `healthy` dentro do diretório `/tmp`, passando este comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Em seguida, dormirá durante 30 segundos antes de apagar o ficheiro e, em seguida, entra num sono de 10 minutos.

### <a name="liveness-command"></a>Comando de vivacidade

Esta implantação define um `livenessProbe` que suporta um comando de `exec` de vivacidade que funciona como a verificação de vivacidade. Se este comando sair com um valor não nulo, o contentor será morto e reiniciado, sinalizando que o ficheiro `healthy` não foi encontrado. Se este comando sair com sucesso com o código de saída 0, não serão tomadas medidas.

A propriedade `periodSeconds` designa o comando de vivacidade deve executar a cada 5 segundos.

## <a name="verify-liveness-output"></a>Verificar a produção de vivacidade

Nos primeiros 30 segundos, o ficheiro `healthy` criado pelo comando inicial existe. Quando o comando de vivacidade verifica a existência do ficheiro `healthy`, o código de estado devolve um zero, sinalizando o sucesso, pelo que não ocorre qualquer reinício.

Após 30 segundos, o `cat /tmp/healthy` começará a falhar, causando eventos pouco saudáveis e matando.

Estes eventos podem ser vistos a partir do portal Azure ou Do CLI Azure.

![Portal evento pouco saudável][portal-unhealthy]

Ao visualizar os eventos no portal Azure, eventos de tipo `Unhealthy` serão desencadeados com a falha do comando de vida. O evento seguinte será de tipo `Killing`, significando a eliminação de um recipiente para que um reinício possa começar. A contagem de reinício para os incrementos do recipiente cada vez que este evento ocorre.

Os reinícios são concluídos no local para que recursos como endereços IP públicos e conteúdos específicos do nó sejam preservados.

![Contador de reinício do portal][portal-restart]

Se a sonda de vivacidade falhar continuamente e desencadear demasiados recomeços, o seu recipiente entrará num atraso exponencial de recuo.

## <a name="liveness-probes-and-restart-policies"></a>Sondas de vivacidade e políticas de reinício

As políticas de reinício sobressaem o comportamento de reinício desencadeado por sondas de vivacidade. Por exemplo, se definir uma `restartPolicy = Never` *e* uma sonda de vivacidade, o grupo de contentores não reiniciará devido a uma verificação de vivacidade falhada. Em vez disso, o grupo de contentores aderirá à política de reinício de `Never`do grupo de contentores.

## <a name="next-steps"></a>Passos seguintes

Os cenários baseados em tarefas podem exigir uma sonda de vivacidade para permitir o reinício automático se uma função pré-requisito não estiver a funcionar corretamente. Para obter mais informações sobre a execução de contentores baseados em [tarefas, consulte executar tarefas contentorizadas em instâncias de contentores Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
