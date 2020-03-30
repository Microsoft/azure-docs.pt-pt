---
title: Tutorial - Agendar uma tarefa ACR
description: Neste tutorial, aprenda a executar uma Tarefa de Registo de Contentores Azure num horário definido, definindo um ou mais gatilhos temporizadores
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402876"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>Executar uma tarefa ACR em um horário definido

Este tutorial mostra-lhe como executar uma [Tarefa ACR](container-registry-tasks-overview.md) num horário. Agende uma tarefa configurando um ou mais *gatilhos temporizadores*. Os gatilhos do temporizador podem ser utilizados sozinhos, ou em combinação com outros gatilhos de tarefa.

Neste tutorial, aprenda sobre tarefas de agendamento e:

> [!div class="checklist"]
> * Criar uma tarefa com um gatilho temporizador
> * Gerir os gatilhos do temporizador

Agendar uma tarefa é útil para cenários como o seguinte:

* Executar uma carga de trabalho de contentores para operações de manutenção programadas. Por exemplo, executar uma aplicação contentorizada para remover imagens desnecessárias do seu registo.
* Faça um conjunto de testes numa imagem de produção durante o dia de trabalho como parte da monitorização do seu site ao vivo.

Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI para executar os exemplos deste artigo. Se quiser usá-lo localmente, a versão 2.0.68 ou posterior é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].


## <a name="about-scheduling-a-task"></a>Sobre agendar uma tarefa

* **Gatilho com expressão cron** - O gatilho temporizador para uma tarefa usa uma *expressão crono.* A expressão é uma corda com cinco campos especificando o minuto, hora, dia, mês e dia da semana para desencadear a tarefa. São suportadas frequências até uma vez por minuto.

  Por exemplo, `"0 12 * * Mon-Fri"` a expressão desencadeia uma tarefa ao meio-dia UTC em cada dia da semana. Veja mais tarde neste artigo [os detalhes.](#cron-expressions)
* **Múltiplos gatilhos temporizadores** - A adição de vários temporizadores a uma tarefa é permitida, desde que os horários sejam diferentes.
    * Especifique múltiplos gatilhos do temporizador quando criar a tarefa, ou adicione-os mais tarde.
    * Nomeie opcionalmente os gatilhos para uma gestão mais fácil, ou as Tarefas ACR fornecerão nomes de gatilho padrão.
    * Se os horários do temporizador se sobrepuserem de cada vez, as Tarefas ACR desencadeiam a tarefa na hora programada para cada temporizador.
* **Outros gatilhos de tarefas** - Numa tarefa acionada pelo temporizador, também pode ativar os gatilhos com base no compromisso do [código fonte](container-registry-tutorial-build-task.md) ou [nas atualizações](container-registry-tutorial-base-image-update.md)de imagem base . Tal como outras tarefas ACR, também pode [desencadear manualmente][az-acr-task-run] uma tarefa programada.

## <a name="create-a-task-with-a-timer-trigger"></a>Criar uma tarefa com um gatilho temporizador

Quando cria uma tarefa com a [tarefa az acr criar][az-acr-task-create] comando, pode adicionar opcionalmente um gatilho temporizador. Adicione `--schedule` o parâmetro e passe uma expressão cron para o temporizador.

Como um exemplo simples, o seguinte `hello-world` comando dispara a imagem do Docker Hub todos os dias às 21:00 UTC. A tarefa funciona sem um contexto de código fonte.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Executar o comando de [show de tarefas az acr][az-acr-task-show] para ver se o gatilho do temporizador está configurado. Por defeito, o gatilho de atualização de imagem base também está ativado.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

Desencadear a tarefa manualmente com [a tarefa az acr][az-acr-task-run] executar para garantir que está corretamente configurada:

```azurecli
az acr task run --name mytask --registry myregistry
```

Se o recipiente correr com sucesso, a saída é semelhante à seguinte:

```output
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

Após o tempo programado, executar o comando de [execução da lista de tarefas az acr][az-acr-task-list-runs] para verificar se o temporizador desencadeou a tarefa como esperado:

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

Quando o temporizador é bem sucedido, a saída é semelhante à seguinte:

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Gerir os gatilhos do temporizador

Utilize os comandos [temporizadores az acr][az-acr-task-timer] para gerir os gatilhos do temporizador para uma tarefa ACR.

### <a name="add-or-update-a-timer-trigger"></a>Adicione ou atualize um gatilho temporizador

Depois de criada uma tarefa, adicione opcionalmente um gatilho temporizador utilizando o [temporizador de tarefa az acr adicionar][az-acr-task-timer-add] comando. O exemplo seguinte adiciona um temporizador de marcação *temporizador2* à *minha tarefa* criada anteriormente. Este temporizador despoleta a tarefa todos os dias às 10:30 UTC.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Atualize o calendário de um gatilho existente, ou altere o seu estado, utilizando o comando de atualização de atualização de tempo de [tarefa az acr.][az-acr-task-timer-update] Por exemplo, atualize o *temporizador* nomeado para o gatilho 2 para desencadear a tarefa às 11:30 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Gatilhos do temporizador da lista

O comando da [lista de tarefas az acr][az-acr-task-timer-list] mostra os gatilhos do temporizador configurados para uma tarefa:

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

### <a name="remove-a-timer-trigger"></a>Remover um gatilho temporizador

Utilize o [temporizador de tarefa az acr remover][az-acr-task-timer-remove] o comando para remover um gatilho temporizador de uma tarefa. O exemplo que se segue remove o gatilho *temporizador2* da *minha tarefa:*

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Expressões cronosas

A Cr Tasks utiliza a biblioteca [NCronTab](https://github.com/atifaziz/NCrontab) para interpretar expressões de comum. Expressões suportadas em Tarefas ACR têm cinco campos necessários separados pelo espaço branco:

`{minute} {hour} {day} {month} {day-of-week}`

O fuso horário utilizado com as expressões cronéis é o Tempo Universal Coordenado (UTC). As horas estão em formato 24 horas.

> [!NOTE]
> As Tarefas ACR `{second}` não `{year}` suportam o campo ou o campo em expressões cron. Se copiar uma expressão cronológica utilizada noutro sistema, certifique-se de que remove esses campos, se forem utilizados.

Cada campo pode ter um dos seguintes tipos de valores:

|Tipo  |Exemplo  |Quando desencadeado  |
|---------|---------|---------|
|Um valor específico |<nobr>`"5 * * * *"`</nobr>|a cada hora a 5 minutos depois da hora|
|Todos os`*`valores ()|<nobr>`"* 5 * * *"`</nobr>|cada minuto da hora a partir das 5:00 UTC (60 vezes por dia)|
|Uma gama`-` (operador)|<nobr>`"0 1-3 * * *"`</nobr>|3 vezes por dia, às 13:00, 2:00 e 3:00 UTC|
|Um conjunto de`,` valores (operador)|<nobr>`"20,30,40 * * * *"`</nobr>|3 vezes por hora, a 20 minutos, 30 minutos e 40 minutos depois da hora.|
|Um valor`/` de intervalo (operador)|<nobr>`"*/10 * * * *"`</nobr>|6 vezes por hora, a 10 minutos, 20 minutos, e assim por diante, depois da hora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Exemplos de cron

|Exemplo|Quando desencadeado  |
|---------|---------|
|`"*/5 * * * *"`|uma vez a cada cinco minutos|
|`"0 * * * *"`|uma vez no topo de cada hora|
|`"0 */2 * * *"`|uma vez a cada duas horas|
|`"0 9-17 * * *"`|uma vez a cada hora das 9:00 às 17:00 UTC|
|`"30 9 * * *"`|às 9:30 UTC todos os dias|
|`"30 9 * * 1-5"`|às 9:30 UTC todos os dias da semana|
|`"30 9 * Jan Mon"`|às 9:30 UTC todas as segundas-feiras em janeiro|

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que criou nesta série tutorial, incluindo o registo de contentores ou registos, instância de contentores, cofre chave e diretor de serviço, emita os seguintes comandos:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar tarefas de Registo de Contentores Azure que são automaticamente desencadeadas por um temporizador. 

Por exemplo, utilizando uma tarefa programada para limpar repositórios num registo, consulte automaticamente a purga de imagens de um registo de [contentores Azure](container-registry-auto-purge.md).

Por exemplo, de tarefas desencadeadas por código seletiva ou atualizações de imagem base, consulte outros artigos na [série tutorial ACR Tasks](container-registry-tutorial-quick-task.md).



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
