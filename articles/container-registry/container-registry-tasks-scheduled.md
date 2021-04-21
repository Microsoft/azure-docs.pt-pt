---
title: Tutorial - Agendar uma tarefa ACR
description: Neste tutorial, aprenda a executar uma Tarefa de Registo de Contentores Azure num horário definido, definindo um ou mais gatilhos temporais
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780872"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Tutorial: Executar uma tarefa ACR em um horário definido

Este tutorial mostra-lhe como executar uma [tarefa ACR](container-registry-tasks-overview.md) em um horário. Agendar uma tarefa configurando um ou mais *gatilhos temporeiros*. Os gatilhos do temporizador podem ser utilizados sozinhos ou em combinação com outros gatilhos de tarefa.

Neste tutorial, conheça as tarefas de agendamento e:

> [!div class="checklist"]
> * Criar uma tarefa com um gatilho temporizador
> * Gerir gatilhos de temporizador

Agendar uma tarefa é útil para cenários como:

* Executar uma carga de trabalho de contentor para operações de manutenção programadas. Por exemplo, executar uma aplicação contentorizada para remover imagens não necessidade do seu registo.
* Faça um conjunto de testes numa imagem de produção durante o dia de trabalho como parte da monitorização do seu local de trabalho.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Sobre agendar uma tarefa

* **Gatilho com expressão de cron** - O gatilho do temporizador para uma tarefa utiliza uma expressão de *cron*. A expressão é uma cadeia com cinco campos especificando o minuto, hora, dia, mês e dia da semana para desencadear a tarefa. São suportadas frequências de até uma vez por minuto.

  Por exemplo, a expressão `"0 12 * * Mon-Fri"` desencadeia uma tarefa ao meio-dia UTC em cada dia da semana. Veja [mais](#cron-expressions) detalhes neste artigo.
* **Disparadores de temporizador múltiplos** - É permitido adicionar vários temporizadores a uma tarefa, desde que os horários diferam.
    * Especifique vários gatilhos do temporizador quando criar a tarefa, ou adicione-os mais tarde.
    * Opcionalmente nomeie os gatilhos para uma gestão mais fácil, ou as tarefas ACR fornecerão nomes de gatilhos predefinidos.
    * Se os horários se sobrepõem de cada vez, as tarefas ACR desencadeiam a tarefa na hora programada para cada temporizador.
* **Outros gatilhos** de tarefas - Numa tarefa desencadeada pelo temporizador, também pode ativar os gatilhos baseados em [código fonte](container-registry-tutorial-build-task.md) ou [atualizações de imagem base](container-registry-tutorial-base-image-update.md). Tal como outras tarefas ACR, também pode [executar manualmente][az-acr-task-run] uma tarefa programada.

## <a name="create-a-task-with-a-timer-trigger"></a>Criar uma tarefa com um gatilho temporizador

### <a name="task-command"></a>Comando de tarefa

Em primeiro lugar, povoe a seguinte variável ambiente de concha com um valor adequado para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se não preencher a variável ambiente, deve substituir manualmente cada valor onde quer que apareça nos comandos de exemplo.

[![Lançamento incorporado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Quando cria uma tarefa com o comando [az acr,][az-acr-task-create] pode adicionar opcionalmente um gatilho do temporizador. Adicione o `--schedule` parâmetro e passe uma expressão de cron para o temporizador.

Como exemplo simples, a seguinte tarefa desencadeia a execução `hello-world` da imagem do Registo de Contentores da Microsoft todos os dias às 21:00 UTC. A tarefa é executado sem um contexto de código fonte.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Executar o comando [de programa de tarefas az acr][az-acr-task-show] para ver se o gatilho do temporizador está configurado. Por predefinição, o gatilho da atualização de imagem base também está ativado.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Desencadear a tarefa

Desaccione manualmente a tarefa [az acr][az-acr-task-run] para garantir que está configurada corretamente:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Se o recipiente funciona com sucesso, a saída é semelhante à seguinte. A saída é condensada para mostrar passos-chave

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Após a hora programada, executar o comando [de lista de tarefas az acr][az-acr-task-list-runs] para verificar se o temporizador desencadeou a tarefa como esperado:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Quando o temporizador é bem sucedido, a saída é semelhante à seguinte:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Gerir gatilhos de temporizador

Utilize os comandos [do temporizador de tarefas az acr][az-acr-task-timer] para gerir os gatilhos do temporizador para uma tarefa ACR.

### <a name="add-or-update-a-timer-trigger"></a>Adicione ou atualize um gatilho do temporizador

Depois de uma tarefa ser criada, adicione opcionalmente um gatilho do temporizador utilizando o [temporizador de tarefa az acr adicionar][az-acr-task-timer-add] comando. O exemplo a seguir adiciona um *temporizador do temporizador2* ao *temporizador* criado anteriormente. Este temporizador despoleta a tarefa todos os dias às 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Atualize a programação de um gatilho existente, ou altere o seu estado, utilizando o comando de atualização do [temporizador de tarefas az acr.][az-acr-task-timer-update] Por exemplo, atualize o *temporizador* nomeado para ativar a tarefa às 11:30 UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Gatilhos do temporizador da lista

O comando [da lista de temporizadores de tarefas az acr][az-acr-task-timer-list] mostra os gatilhos do temporizador configurado para uma tarefa:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
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

### <a name="remove-a-timer-trigger"></a>Remover um gatilho do temporizador

Utilize o [temporizador de tarefas az acr remover][az-acr-task-timer-remove] o comando para remover um gatilho do temporizador de uma tarefa. O exemplo a seguir remove o gatilho *do temporizador do* *timertask:*

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Expressões de cron

ACR Tasks utiliza a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões de cron. Expressões apoiadas em Tarefas ACR têm cinco campos necessários separados pelo espaço branco:

`{minute} {hour} {day} {month} {day-of-week}`

O fuso horário utilizado com as expressões cron é o Tempo Universal Coordenado (UTC). As horas são em formato 24 horas.

> [!NOTE]
> As Tarefas ACR não suportam o `{second}` `{year}` ou campo em expressões de cron. Se copiar uma expressão de cron utilizada noutro sistema, certifique-se de remover esses campos, se forem utilizados.

Cada campo pode ter um dos seguintes tipos de valores:

|Tipo  |Exemplo  |Quando acionado  |
|---------|---------|---------|
|Um valor específico |<nobr>`"5 * * * *"`</nobr>|a cada hora a 5 minutos depois da hora|
|Todos os valores `*` ()|<nobr>`"* 5 * * *"`</nobr>|cada minuto da hora a partir das 5:00 UTC (60 vezes por dia)|
|Uma gama `-` (operador)|<nobr>`"0 1-3 * * *"`</nobr>|3 vezes por dia, às 13:00, 2:00 e 3:00 UTC|
|Um conjunto de valores `,` (operador)|<nobr>`"20,30,40 * * * *"`</nobr>|3 vezes por hora, a 20 minutos, 30 minutos e 40 minutos depois da hora|
|Um valor de intervalo `/` (operador)|<nobr>`"*/10 * * * *"`</nobr>|6 vezes por hora, a 10 minutos, 20 minutos, e assim por diante, depois da hora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Exemplos de Cron

|Exemplo|Quando acionado  |
|---------|---------|
|`"*/5 * * * *"`|uma vez a cada cinco minutos|
|`"0 * * * *"`|uma vez no topo de cada hora|
|`"0 */2 * * *"`|uma vez a cada duas horas|
|`"0 9-17 * * *"`|uma vez a cada hora das 9:00 às 17:00 UTC|
|`"30 9 * * *"`|às 9:30 UTC todos os dias|
|`"30 9 * * 1-5"`|às 9:30 UTC todos os dias da semana|
|`"30 9 * Jan Mon"`|às 9:30 UTC todas as segundas-feiras de janeiro|

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover todos os recursos que criou nesta série tutorial, incluindo o registo de contentores ou registos, instância de contentor, cofre chave e principal de serviço, emite os seguintes comandos:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar tarefas de Registo de Contentores Azure que são automaticamente desencadeadas por um temporizador. 

Para um exemplo de utilização de uma tarefa programada para limpar repositórios num registo, consulte [automaticamente as imagens de um registo de contentores Azure](container-registry-auto-purge.md).

Por exemplo, as tarefas desencadeadas pelo código fonte comprometem-se ou atualizações de imagem base, consulte outros artigos na [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
