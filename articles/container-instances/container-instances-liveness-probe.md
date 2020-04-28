---
title: Configurar sonda de vivacidade na instância do recipiente
description: Saiba como configurar sondas de vivacidade para reiniciar recipientes pouco saudáveis em Instâncias de Contentores De Azure
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76934168"
---
# <a name="configure-liveness-probes"></a>Configurar as pesquisas liveness

As aplicações contentorizadas podem ser executadas por longos períodos de tempo, resultando em estados avariados que podem ter de ser reparados reiniciando o recipiente. As instâncias de contentores Azure suportam sondas de vivacidade para que possa configurar os seus contentores dentro do seu grupo de contentores para reiniciar se a funcionalidade crítica não estiver a funcionar. A sonda de vivacidade comporta-se como uma [sonda kubernetes de vivacidade.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Este artigo explica como implantar um grupo de contentores que inclui uma sonda de vivacidade, demonstrando o reinício automático de um recipiente simulado e pouco saudável.

As instâncias de contentores Azure também suportam sondas de [prontidão,](container-instances-readiness-probe.md)que pode configurar para garantir que o tráfego chega a um contentor apenas quando está pronto para o mesmo.

> [!NOTE]
> Atualmente não é possível utilizar uma sonda de vivacidade num grupo de contentores implantado numa rede virtual.

## <a name="yaml-deployment"></a>Implantação yAML

Crie `liveness-probe.yaml` um ficheiro com o seguinte corte. Este ficheiro define um grupo de contentores que consiste num contentor NGNIX que eventualmente se torna insalubre.

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

A implementação `command` inclui uma propriedade que define um comando inicial que funciona quando o recipiente começa a funcionar. Esta propriedade aceita uma variedade de cordas. Este comando simula o contentor entrando num estado pouco saudável.

Primeiro, começa uma sessão de `healthy` bash `/tmp` e cria um ficheiro chamado dentro do diretório. Em seguida, dorme durante 30 segundos antes de apagar o ficheiro e, em seguida, entra num sono de 10 minutos:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Comando de vivacidade

Esta implantação `livenessProbe` define um `exec` que suporta um comando de vivacidade que funciona como a verificação de vivacidade. Se este comando sair com um valor não nulo, o recipiente `healthy` é morto e reiniciado, sinalizando que o ficheiro não foi encontrado. Se este comando sair com sucesso com o código de saída 0, não são tomadas medidas.

A `periodSeconds` propriedade designa o comando de vivacidade deve executar a cada 5 segundos.

## <a name="verify-liveness-output"></a>Verificar a produção de vivacidade

Nos primeiros 30 `healthy` segundos, o ficheiro criado pelo comando inicial existe. Quando o comando de `healthy` vivacidade verifica a existência do ficheiro, o código de estado devolve 0, sinalizando o sucesso, pelo que não ocorre reinício.

Após 30 segundos, o `cat /tmp/healthy` comando começa a falhar, causando eventos pouco saudáveis e matanças.

Estes eventos podem ser vistos a partir do portal Azure ou Do CLI Azure.

![Portal evento pouco saudável][portal-unhealthy]

Ao ver os eventos no portal `Unhealthy` Azure, eventos de tipo são desencadeados sobre a falha do comando de vida. O evento subsequente `Killing`é de tipo, significando a eliminação de um recipiente para que um reinício possa começar. A contagem de reinício para os incrementos do recipiente cada vez que este evento ocorre.

Os reinícios são concluídos no local para que os recursos como endereços IP públicos e conteúdos específicos do nó sejam preservados.

![Contador de reinício do portal][portal-restart]

Se a sonda de vivacidade falhar continuamente e desencadear demasiados recomeços, o seu recipiente entra num atraso exponencial.

## <a name="liveness-probes-and-restart-policies"></a>Sondas de vivacidade e políticas de reinício

As políticas de reinício sobressaem o comportamento de reinício desencadeado por sondas de vivacidade. Por exemplo, se `restartPolicy = Never` definir uma sonda de vivacidade *e* uma sonda de vivacidade, o grupo de contentores não recomeçará devido a uma verificação de vivacidade falhada. Em vez disso, o grupo de contentores `Never`adere à política de reinício do grupo de contentores de .

## <a name="next-steps"></a>Passos seguintes

Os cenários baseados em tarefas podem exigir uma sonda de vivacidade para permitir o reinício automático se uma função pré-requisito não estiver a funcionar corretamente. Para obter mais informações sobre a execução de contentores baseados em [tarefas, consulte executar tarefas contentorizadas em instâncias de contentores Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
