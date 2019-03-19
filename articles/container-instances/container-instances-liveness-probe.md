---
title: Configurar sondas de liveness no Azure Container Instances
description: Saiba como configurar sondas de liveness para reiniciar o mau estado de funcionamento contentores no Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 89b76fc68c113b7931894c0cf003ffd846c646ab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57899728"
---
# <a name="configure-liveness-probes"></a>Configurar as pesquisas liveness

Aplicações em contentores podem ser executada durante longos períodos de tempo, resultando em Estados quebrados, que poderão ter de ser reparada reiniciando o contentor. O Azure Container Instances suporta sondas de liveness para incluir configurações para que o seu contentor pode reiniciar se funcionalidades críticas não está a funcionar.

Este artigo explica como implementar um grupo de contentores que inclui uma a sonda de atividade, demonstrando o reinício automático de um contentor de mau estado de funcionamento simulado.

## <a name="yaml-deployment"></a>Implementação de YAML

Criar um `liveness-probe.yaml` ficheiro pelo seguinte fragmento. Esse arquivo define um grupo de contentores que consiste num contêiner NGNIX que, eventualmente, fica danificado.

```yaml
apiVersion: 2018-06-01
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

Execute o seguinte comando para implementar este grupo de contentor com a configuração de YAML acima:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Comando de início

A implementação define um comando de partida para ser executada quando o contentor é iniciado pela primeira vez em execução, definido pelo `command` propriedade que aceita uma matriz de cadeias de caracteres. Neste exemplo, irá iniciar uma sessão do bash e crie um ficheiro chamado `healthy` dentro do `/tmp` diretório ao transmitir este comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Irá, em seguida, em modo de suspensão durante 30 segundos antes de eliminar o ficheiro, em seguida, entra num modo de suspensão de 10 minutos.

### <a name="liveness-command"></a>Comando de liveness

Esta implementação define um `livenessProbe` que suporta um `exec` comando liveness que age como a verificação de liveness. Se este comando é encerrado com um valor diferente de zero, o contentor será terminado e reiniciado, sinalizando o `healthy` não foi possível encontrar o ficheiro. Se este comando é encerrado com êxito com o código de saída 0, não será conduzida nenhuma ação.

O `periodSeconds` propriedade designa o liveness comando deve ser executada a cada cinco segundos.

## <a name="verify-liveness-output"></a>Verificar a saída de liveness

Dentro os primeiros 30 segundos, o `healthy` existe ficheiro criado pelo comando de início. Quando o comando de liveness verifica a existência de `healthy` existência do arquivo, o código de estado retorna um zero, sinalizando com êxito, pelo que não reiniciar ocorre.

Após 30 segundos, o `cat /tmp/healthy` começarão a falhar, fazendo com que eventos de mau estado de funcionamento e matando para ocorrer.

Esses eventos podem ser visualizados a partir do portal do Azure ou da CLI do Azure.

![Portal evento mau estado de funcionamento][portal-unhealthy]

Ao visualizar os eventos no portal do Azure, eventos do tipo `Unhealthy` será acionado após a falha do comando de liveness. O evento subsequente será do tipo `Killing`, fazendo referência a eliminação de contentor, para que pode começar a um reinício. A contagem de reinício do contentor sofrerá um incremento sempre que isso ocorre.

Reinícios são concluídos no local, de modo a recursos, como endereços IP públicos e conteúdo de nó específico será mantido.

![Contador de reinício do portal][portal-restart]

Se o a sonda de atividade continuamente falha e aciona demasiados reinícios, o contentor irá introduzir um término exponencial atraso.

## <a name="liveness-probes-and-restart-policies"></a>Sondas de liveness e políticas de reinício

Políticas de reinício substituem o comportamento de reinício acionado por liveness sondas. Por exemplo, se definir um `restartPolicy = Never` *e* uma a sonda de atividade, o grupo de contentores não irá reiniciar em caso de uma verificação de liveness com falha. O grupo de contentores em vez disso, irão corresponder a política de reinício do grupo de contentores de `Never`.

## <a name="next-steps"></a>Passos Seguintes

Cenários de baseado em tarefas podem exigir uma a sonda de atividade para ativar reinícios automáticos, se uma função de pré-requisitos não está a funcionar corretamente. Para obter mais informações sobre a execução de contentores com base em tarefas, consulte [executar tarefas em contentores no Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
