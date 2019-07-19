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
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325872"
---
# <a name="configure-liveness-probes"></a>Configurar as pesquisas liveness

Os aplicativos em contêineres podem ser executados por longos períodos de tempo, resultando em Estados desfeitos que talvez precisem ser reparados reiniciando o contêiner. As instâncias de contêiner do Azure dão suporte a investigações de vida para incluir configurações para que seu contêiner possa ser reiniciado se a funcionalidade crítica não estiver funcionando.

Este artigo explica como implantar um grupo de contêineres que inclui uma investigação de tempo de vida, demonstrando a reinicialização automática de um contêiner não íntegro simulado.

## <a name="yaml-deployment"></a>Implantação do YAML

Crie um `liveness-probe.yaml` arquivo com o trecho a seguir. Esse arquivo define um grupo de contêineres que consiste em um contêiner NGNIX que eventualmente se torna não íntegro.

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

Execute o seguinte comando para implantar esse grupo de contêineres com a configuração YAML acima:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Comando iniciar

A implantação define um comando inicial a ser executado quando o contêiner começa a ser executado pela primeira vez `command` , definido pela propriedade que aceita uma matriz de cadeias de caracteres. Neste exemplo, ele iniciará uma sessão de bash e criará um arquivo `healthy` chamado dentro `/tmp` do diretório passando este comando:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Em seguida, ele será suspenso por 30 segundos antes de excluir o arquivo e, em seguida, entrará em uma suspensão de 10 minutos.

### <a name="liveness-command"></a>Comando de vida

Essa implantação define um `livenessProbe` que dá suporte `exec` a um comando de vida que atua como a verificação de tempo de vida. Se esse comando for encerrado com um valor diferente de zero, o contêiner será encerrado e reiniciado, o sinalização `healthy` do arquivo não poderá ser encontrado. Se esse comando for encerrado com êxito com o código de saída 0, nenhuma ação será executada.

A `periodSeconds` Propriedade designa que o comando de vida deve ser executado a cada 5 segundos.

## <a name="verify-liveness-output"></a>Verificar a saída de vida

Nos primeiros 30 segundos, o `healthy` arquivo criado pelo comando Iniciar existe. Quando o comando de tempo de vida verifica `healthy` a existência do arquivo, o código de status retorna um zero, sinalizando com êxito, portanto, não ocorrerá nenhuma reinicialização.

Após 30 segundos, o `cat /tmp/healthy` começará a falhar, causando a ocorrência de eventos não íntegros e de eliminação.

Esses eventos podem ser exibidos no portal do Azure ou CLI do Azure.

![Evento de não integridade do portal][portal-unhealthy]

Ao exibir os eventos no portal do Azure, os eventos do tipo `Unhealthy` serão disparados na falha do comando de vida. O evento subsequente será do tipo `Killing`, significando uma exclusão de contêiner para que uma reinicialização possa começar. A contagem de reinicialização do contêiner aumentará cada vez que isso ocorrer.

As reinicializações são concluídas no local para que os recursos como endereços IP públicos e conteúdos específicos do nó sejam preservados.

![Contador de reinicialização do portal][portal-restart]

Se a investigação de vida falhar continuamente e disparar muitas reinicializações, o contêiner entrará em um atraso de retirada exponencial.

## <a name="liveness-probes-and-restart-policies"></a>Testes de vida e políticas de reinicialização

As políticas de reinicialização substituem o comportamento de reinicialização acionado por investigações de vida. Por exemplo, se você definir um `restartPolicy = Never` *e* uma investigação de tempo de vida, o grupo de contêineres não será reiniciado no caso de uma verificação de falha de vida. Em vez disso, o grupo de contêineres aderirá à política de reinicialização do grupo de contêineres do `Never`.

## <a name="next-steps"></a>Passos Seguintes

Cenários baseados em tarefas podem exigir uma investigação de vida para habilitar reinicializações automáticas se uma função de pré-requisito não estiver funcionando corretamente. Para obter mais informações sobre como executar contêineres baseados em tarefas, consulte [executar tarefas em contêineres em instâncias de contêiner do Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
