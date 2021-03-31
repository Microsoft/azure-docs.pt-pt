---
title: Configurar sonda de vida na instância do recipiente
description: Saiba como configurar sondas de vida para reiniciar recipientes não saudáveis em instâncias de contentores de Azure
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: befe9693be1413abf455d915814c53aab20db53c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169702"
---
# <a name="configure-liveness-probes"></a>Configurar as pesquisas liveness

As aplicações contentorizadas podem ser executadas por longos períodos de tempo, resultando em estados partidos que possam ter de ser reparados reiniciando o contentor. Azure Container Instances suporta sondas de vida para que possa configurar os seus recipientes dentro do seu grupo de contentores para reiniciar se a funcionalidade crítica não estiver a funcionar. A sonda de vida comporta-se como uma [sonda de vivacidade de Kubernetes.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Este artigo explica como implantar um grupo de contentores que inclui uma sonda de vida, demonstrando o reinício automático de um recipiente simulado.

Azure Container Instances também suporta [sondas de prontidão](container-instances-readiness-probe.md), que pode configurar para garantir que o tráfego atinge um contentor apenas quando estiver pronto para ele.

> [!NOTE]
> Atualmente não é possível utilizar uma sonda de vivaza num grupo de contentores implantado numa rede virtual.

## <a name="yaml-deployment"></a>Implantação YAML

Crie um `liveness-probe.yaml` ficheiro com o seguinte corte. Este ficheiro define um grupo de contentores que consiste num recipiente NGNIX que eventualmente se torna insalubre.

```yaml
apiVersion: 2019-12-01
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

A implantação inclui uma `command` propriedade que define um comando inicial que funciona quando o recipiente começa a funcionar pela primeira vez. Esta propriedade aceita uma variedade de cordas. Este comando simula o contentor entrando num estado pouco saudável.

Primeiro, inicia uma sessão de festa e cria um ficheiro chamado `healthy` dentro do `/tmp` diretório. Em seguida, dorme durante 30 segundos antes de apagar o ficheiro e, em seguida, entra num sono de 10 minutos:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Comando de vida

Esta implementação define um `livenessProbe` que suporta um comando de vida que funciona como o controlo da `exec` vivacidade. Se este comando sair com um valor não nulo, o recipiente é morto e reiniciado, sinalizando que o `healthy` ficheiro não foi encontrado. Se este comando sair com sucesso com o código de saída 0, não serão tomadas medidas.

A `periodSeconds` propriedade designa o comando de vida deve executar a cada 5 segundos.

## <a name="verify-liveness-output"></a>Verificar saída de vida

Nos primeiros 30 segundos, `healthy` o ficheiro criado pelo comando inicial existe. Quando o comando de vida verifica a `healthy` existência do ficheiro, o código de estado retorna 0, sinalizando o sucesso, para que não ocorra qualquer reinício.

Após 30 segundos, o `cat /tmp/healthy` comando começa a falhar, causando eventos insalubres e matando.

Estes eventos podem ser vistos a partir do portal Azure ou Azure CLI.

![Evento insalubre do portal][portal-unhealthy]

Ao ver os eventos no portal Azure, eventos de tipo `Unhealthy` são desencadeados por falha no comando da vivacidade. O evento subsequente é do `Killing` tipo, significando uma eliminação do contentor para que possa iniciar-se um reinício. A contagem de reinício para os incrementos do contentor cada vez que este evento ocorre.

Os recomeços são concluídos no local para que sejam preservados recursos como endereços IP públicos e conteúdos específicos do nó.

![Contador de reinicie o portal][portal-restart]

Se a sonda de vida falhar continuamente e ativar demasiados recomeços, o seu recipiente entra num atraso exponencial de desatura.

## <a name="liveness-probes-and-restart-policies"></a>Sondas de vivacidade e políticas de reinício

Reiniciar as políticas substitui o comportamento de reinício desencadeado pelas sondas de vivacidade. Por exemplo, se definir uma sonda `restartPolicy = Never` *e* uma sonda de vida, o grupo de contentores não recomeçará devido a uma verificação de vida falhada. Em vez disso, o grupo de contentores adere à política de reinício do grupo de contentores `Never` de .

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas podem exigir uma sonda de vida para permitir o reinício automático se uma função pré-necessária não estiver a funcionar corretamente. Para obter mais informações sobre o funcionamento de recipientes baseados em tarefas, consulte [executar tarefas contentorizadas em instâncias de contentores Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
