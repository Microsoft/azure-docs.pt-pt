---
title: Agendar tarefas de Azure Container Registry
description: Definir temporizadores, para executar uma tarefa de registo de contentor do Azure com base numa agenda definida.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509706"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Executar uma tarefa ACR com base numa agenda definida

Este artigo mostra como executar uma [tarefas ACR](container-registry-tasks-overview.md) com base numa agenda. Agendar uma tarefa ao configurar um ou mais *acionadores de temporizadores*. 

Agendar uma tarefa é útil para cenários como o seguinte:

* Execute uma carga de trabalho de contentor para operações de manutenção agendada. Por exemplo, execute uma aplicação em contentores para remover as imagens desnecessárias do seu registo.
* Execute um conjunto de testes numa imagem de produção durante o dia de trabalho como parte da sua monitorização de sites ativos.

Pode utilizar o Azure Cloud Shell ou uma instalação local da CLI do Azure para executar os exemplos neste artigo. Se gostaria de usá-lo localmente, versão 2.0.68 ou posterior é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Sobre o agendamento de uma tarefa

* **Acionador com a expressão cron** -o acionador de temporizador para uma tarefa utiliza um *expressão cron*. A expressão é uma cadeia de caracteres com cinco campos especificando o minuto, hora, dia, mês e dia da semana para acionar a tarefa. São suportadas as frequências de até uma vez por minuto. 

  Por exemplo, a expressão `"0 12 * * Mon-Fri"` aciona uma tarefa ao meio-dia UTC em cada dia da semana. Ver [detalhes](#cron-expressions) mais adiante neste artigo.
* **Múltiplos acionadores de temporizador** -múltiplos timers para uma tarefa é permitido adicionar, desde que as agendas são diferentes. 
    * Especifica múltiplos acionadores de temporizador quando criar a tarefa ou adicioná-los mais tarde.
    * Opcionalmente, atribua um nome os disparadores para facilitar a gestão ou tarefas de ACR irá fornecer nomes de Acionador de padrão.
    * Se existir sobreposição de agendas de temporizador ao mesmo tempo, as tarefas de ACR aciona a tarefa à hora agendada para cada temporizador. 
* **Outros acionadores de tarefas** -uma tarefa de acionada por temporizador de mensagens em fila, também pode ativar a acionadores com base na [consolidação de código de origem](container-registry-tutorial-build-task.md) ou [basear as atualizações da imagem](container-registry-tutorial-base-image-update.md). Como outras tarefas ACR, também pode [acionar manualmente][az-acr-task-run] uma tarefa agendada.

## <a name="create-a-task-with-a-timer-trigger"></a>Crie uma tarefa com um acionador de temporizador

Quando cria uma tarefa com o [az acr tarefa criar][az-acr-task-create] de comando, opcionalmente, pode adicionar um acionador de temporizador. Adicionar o `--schedule` parâmetro e passar uma expressão cron para o temporizador. 

Como um exemplo simples, o seguinte comando a executar os acionadores a `hello-world` imagem do Docker Hub todos os dias às 21:00 UTC. A tarefa é executada sem um contexto de código de origem.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

Executar o [show de tarefa az acr][az-acr-task-show] comando para ver que o acionador de temporizador está configurado. Por predefinição, o acionador de atualização da imagem base também está ativado.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Acionar a tarefa com o manualmente [execução da tarefa az acr][az-acr-task-run] para se certificar de que este está configurado corretamente:

```azurecli
az acr task run --name mytask --registry myregistry
```

Se o contentor é executado com êxito, o resultado é semelhante ao seguinte:

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Depois da hora agendada, execute o [az acr tarefas lista-runs][az-acr-task-list-runs] comando para verificar se o timer acionado a tarefa, conforme esperado:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

Quando o temporizador é bem-sucedida, o resultado é semelhante ao seguinte:

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>Gerir os acionadores de temporizadores

Utilize o [temporizador de tarefa az acr][az-acr-task-timer] comandos para gerir os acionadores de temporizador para uma tarefa do ACR.

### <a name="add-or-update-a-timer-trigger"></a>Adicionar ou atualizar um acionador de temporizador

Depois de uma tarefa é criada, opcionalmente, adicione um acionador de temporizador, utilizando o [temporizador de tarefa az acr adicionar][az-acr-task-timer-add] comando. O exemplo seguinte adiciona um nome de Acionador de temporizador *timer2* ao *mytask* criado anteriormente. Este temporizador aciona a tarefa de todos os dias às 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Atualizar a agenda de um acionador existente ou alterar o respetivo estado, utilizando o [atualização do az acr tarefa temporizador][az-acr-task-timer-update] comando. Por exemplo, atualizar o acionador com o nome *timer2* para acionar a tarefa em 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Lista os acionadores de temporizadores

O [lista de temporizador de tarefas do az acr][az-acr-task-timer-list] comando mostra os acionadores de temporizadores definida para uma tarefa:

```azurecli
az acr task timer list --name mytask --registry myregistry
```

Exemplo de saída:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Remover um acionador de temporizador 

Utilize o [remover de temporizador de tarefa do az acr][az-acr-task-timer-remove] comando para remover um acionador de temporizador a partir de uma tarefa. O exemplo seguinte remove o *timer2* acionar partir *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Expressões cron

Tarefas de ACR utiliza a [NCronTab](https://github.com/atifaziz/NCrontab) biblioteca interpretar expressões cron. As expressões suportadas no ACR tarefas têm cinco campos obrigatórios, separados por espaços em branco:

`{minute} {hour} {day} {month} {day-of-week}`

O fuso horário utilizado com as expressões cron é a hora Universal Coordenada (UTC). As horas estão no formato de 24 horas.

> [!NOTE]
> Tarefas de ACR não suporta o `{second}` ou `{year}` campo em expressões cron. Se copiar uma expressão cron utilizada em outro sistema, certifique-se de que remover esses campos, se forem usados.

Cada campo pode ter um dos seguintes tipos de valores:

|Type  |Exemplo  |Quando acionado  |
|---------|---------|---------|
|Um valor específico |<nobr>"5 * * * *"</nobr>|cada hora em 5 minutos decorridos desde a hora|
|Todos os valores (`*`)|<nobr>"* 5 * * *"</nobr>|todos os minutos do UTC de 5:00 início a hora (60 vezes por dia)|
|Um intervalo (`-` operador)|<nobr>"0 1-3 * * *"</nobr>|3 vezes por dia, às 1:00, 2:00 e 3:00 UTC|  
|Um conjunto de valores (`,` operador)|<nobr>"20,30,40 * * * *"</nobr>|3 vezes por hora, em 20 minutos, 30 minutos e 40 minutos decorridos desde a hora|
|Um valor de intervalo (`/` operador)|<nobr>"*/10 * * * *"</nobr>|6 vezes por hora, em 10 minutos, 20 minutos e assim por diante, decorridos desde a hora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Exemplos de cron

|Exemplo|Quando acionado  |
|---------|---------|
|`"*/5 * * * *"`|uma vez a cada cinco minutos|
|`"0 * * * *"`|uma vez na parte superior de cada hora|
|`"0 */2 * * *"`|uma vez a cada duas horas|
|`"0 9-17 * * *"`|uma vez por hora de 9:00 17:00 UTC|
|`"30 9 * * *"`|em 9:30 UTC todos os dias|
|`"30 9 * * 1-5"`|em 9:30 UTC cada dia da semana|
|`"30 9 * Jan Mon"`|em 9:30 UTC sempre de segunda em Janeiro|


## <a name="next-steps"></a>Passos Seguintes

Para os exemplos de tarefas acionado por consolidações de código de origem ou uma imagem base, atualizações, consulte a [série de tutoriais de tarefas de ACR](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli