---
title: Gerir e atualizar cache Azure HPC
description: Como gerir e atualizar a Cache Azure HPC utilizando o portal Azure ou Azure CLI
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/08/2021
ms.author: v-erkel
ms.openlocfilehash: b34beb65bb8c4136887651d8365c937b17718572
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471908"
---
# <a name="manage-your-cache"></a>Gerencie a sua cache

A página geral da cache no portal Azure mostra detalhes do projeto, estado de cache e estatísticas básicas para o seu cache. Também tem controlos para parar ou iniciar a cache, eliminar a cache, lavar dados para armazenamento a longo prazo e atualizar o software.

Este artigo também explica como fazer estas tarefas básicas com o Azure CLI.

Para abrir a página geral, selecione o seu recurso cache no portal Azure. Por exemplo, carregue a página **De Todos os recursos** e clique no nome cache.

![screenshot de uma página geral da cache de um Azure HPC Cache](media/hpc-cache-overview.png)

Os botões no topo da página podem ajudá-lo a gerir a cache:

* **Iniciar** e [**Parar**](#stop-the-cache) - Retoma ou suspende a operação de cache
* [**Flush**](#flush-cached-data) - Escreve dados alterados para alvos de armazenamento
* [**Upgrade**](#upgrade-cache-software) - Atualiza o software cache
* [**Recolher diagnósticos**](#collect-diagnostics) - Uploads de informação depuragem
* **Refresh** - Recarregue a página geral
* [**Excluir**](#delete-the-cache) - Destrói permanentemente a cache

Leia mais sobre estas opções abaixo.

Clique na imagem abaixo para ver um [vídeo](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) que demonstra tarefas de gestão de cache.

[![miniatura de vídeo: Azure HPC Cache: Gerir (clique para visitar a página de vídeo)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Pare a cache

Pode parar a cache para reduzir os custos durante um período inativo. Não é cobrado durante o tempo de paragem da cache, mas é cobrado pelo armazenamento do disco atribuído pelo cache. (Consulte [a](https://aka.ms/hpc-cache-pricing) página de preços para mais detalhes.)

Uma cache parada não responde aos pedidos dos clientes. Deve desmontar os clientes antes de parar a cache.

### <a name="portal"></a>[Portal](#tab/azure-portal)

O **botão Stop** suspende uma cache ativa. O botão **Stop** está disponível quando o estado de um cache é **saudável** ou **degradado**.

![screenshot dos botões superiores com Stop realçado e uma mensagem pop-up descrevendo a ação stop e perguntando 'você quer continuar?' com Sim (padrão) e Sem botões](media/stop-cache.png)

Depois de clicar em Sim para confirmar a paragem da cache, a cache coloca automaticamente o seu conteúdo nos alvos de armazenamento. Este processo pode demorar algum tempo, mas garante a consistência dos dados. Finalmente, o estado da cache muda para **Stop**.

Para reativar uma cache parada, clique no botão **Iniciar.** Não é necessária confirmação.

![screenshot dos botões superiores com Start realçado](media/start-cache.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

Suspenda temporariamente uma cache com o comando [de paragem az hpc-cache.](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) Esta ação só é válida quando o estado de uma cache é **saudável** ou **degradado.**

A cache coloca automaticamente o seu conteúdo nos alvos de armazenamento antes de parar. Este processo pode demorar algum tempo, mas garante a consistência dos dados.

Quando a ação estiver concluída, o estado da cache muda para **"Stop".**

Reativar uma cache parada com [arranque az hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start).

Quando emite o comando de arranque ou paragem, a linha de comando mostra uma mensagem de estado "Running" até que a operação esteja concluída.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

No final, a mensagem atualiza para "Terminado" e mostra códigos de devolução e outras informações.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Dados em cache de descarga

O botão **Flush** na página de visão geral diz à cache para escrever imediatamente todos os dados alterados que são armazenados na cache para os alvos de armazenamento de back-end. A cache guarda rotineiramente dados para os alvos de armazenamento, por isso não é necessário fazê-lo manualmente, a menos que queira certificar-se de que o sistema de armazenamento de back-end está atualizado. Por exemplo, pode utilizar **flush** antes de tirar uma foto de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de descarga, a cache não pode servir os pedidos do cliente. O acesso à cache é suspenso e retomado após o fim da operação.

Quando inicia a operação de descarga de cache, a cache deixa de aceitar pedidos do cliente e o estado da cache na página geral muda para **Flushing**.

Os dados na cache são guardados para os alvos de armazenamento apropriados. Dependendo da quantidade de dados necessários para ser lavado, o processo pode demorar alguns minutos ou mais de uma hora.

Depois de todos os dados são guardados para alvos de armazenamento, a cache começa automaticamente a receber os pedidos do cliente novamente. O estado da cache regressa ao **Healthy**.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para lavar a cache, clique no botão **Flush** e, em seguida, clique em **Sim** para confirmar a ação.

![screenshot dos botões superiores com Flush realçado e uma mensagem pop-up descrevendo a ação de descarga e perguntando 'você quer continuar?' com Sim (padrão) e Sem botões](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

Utilize [o flush az hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) para forçar a cache a escrever todos os dados alterados para os alvos de armazenamento.

Exemplo:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Quando a descarga terminar, uma mensagem de sucesso é devolvida.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Atualizar o software cache

Se estiver disponível uma nova versão de software, o botão **De atualização** torna-se ativo. Também deve ver uma mensagem no topo da página sobre a atualização do software.

![screenshot da linha superior de botões com o botão de upgrade ativado](media/hpc-cache-upgrade-button.png)

O acesso ao cliente não é interrompido durante uma atualização de software, mas o desempenho da cache abranda. Planeie atualizar o software durante as horas de utilização não-pico ou num período de manutenção planeado.

A atualização de software pode demorar várias horas. Caches configurados com maior produção demoram mais tempo a atualizar do que caches com valores de produção de pico mais pequenos.

Quando uma atualização de software estiver disponível, terá uma semana ou mais para aplicá-la manualmente. A data de fim está listada na mensagem de atualização. Se não atualizar durante esse tempo, o Azure aplica automaticamente a atualização ao seu cache. O tempo da atualização automática não é configurável. Se estiver preocupado com o impacto do desempenho da cache, deve atualizar o software antes do termo do período de tempo.

Se o seu cache for interrompido quando a data de fim passar, a cache atualizará automaticamente o software da próxima vez que for iniciado. (A atualização pode não começar imediatamente, mas começará na primeira hora.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Clique no botão **Deabos para** iniciar a atualização do software. O estado da cache muda para **atualização** até que a operação esteja concluída.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

No Azure CLI, novas informações de software são incluídas no final do relatório de estado da cache. (Use [az hpc-cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) para verificar.) Procure a cadeia "upgradeStatus" na mensagem.

Utilize [a az hpc-cache upgrade-firmware](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) para aplicar a atualização, se existir alguma.

Se não houver atualização disponível, esta operação não tem qualquer efeito.

Este exemplo mostra o estado da cache (não há atualização disponível) e os resultados do comando de upgrade-firmware.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Recolher diagnósticos

O botão **'Recolha' de diagnósticos** inicia manualmente o processo para recolher informações do sistema e carregá-la para o Microsoft Service and Support para resolução de problemas. O seu cache recolhe e carrega automaticamente as mesmas informações de diagnóstico se ocorrer um problema grave de cache.

Utilize este controlo se o Microsoft Service and Support o solicitar.

Depois de clicar no botão, clique em **Sim** para confirmar o upload.

![screenshot da mensagem de confirmação pop-up 'Start diagnostics collection'. O botão predefinido 'sim' é realçado.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Apagar a cache

O botão **Delete** destrói a cache. Quando se apaga uma cache, todos os seus recursos são destruídos e já não incorrem em encargos de conta.

Os volumes de armazenamento de back-end utilizados como alvos de armazenamento não são afetados quando se elimina a cache. Pode adicioná-los a uma cache futura mais tarde, ou desativá-los separadamente.

> [!NOTE]
> A Azure HPC Cache não escreve automaticamente dados alterados da cache para os sistemas de armazenamento de back-end antes de eliminar a cache.
>
> Para se certificar de que todos os dados da cache foram escritos para armazenamento a longo prazo, [pare a cache](#stop-the-cache) antes de eliminá-lo. Certifique-se de que mostra o estado **parado** antes de apagar.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Depois de parar a cache, clique no botão **Eliminar** para remover permanentemente a cache.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[Configurar o Azure CLI para a cache Azure HPC](./az-cli-prerequisites.md).

Utilize o comando Azure CLI [az hpc-cache para](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) remover permanentemente a cache.

Exemplo:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>Cache métricas e monitorização

A página geral mostra gráficos para algumas estatísticas básicas de cache - produção de cache, operações por segundo, e latência.

![screenshot de três gráficos de linha mostrando as estatísticas acima mencionadas para uma cache de amostra](media/hpc-cache-overview-stats.png)

Estes gráficos fazem parte das ferramentas de monitorização e análise incorporadas da Azure. Estão disponíveis ferramentas e alertas adicionais a partir das páginas sob o título **de Monitorização** na barra lateral do portal. Saiba mais na secção do portal da [documentação de Monitorização do Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="view-warnings"></a>Ver avisos

Se a cache entrar num estado pouco saudável, verifique a página **Avisos.** Esta página mostra notificações do software cache que podem ajudá-lo a entender o seu estado.

Estas notificações não aparecem no registo de atividades por não serem controladas pelo portal Azure. Estão frequentemente associados a configurações personalizadas que pode ter feito.

Os tipos de avisos que pode ver aqui incluem:

* A cache não pode alcançar o seu servidor NTP
* A cache falhou em descarregar informações de nome de utilizador de Grupos Estendidos
* As definições personalizadas de DNS mudaram num alvo de armazenamento

![screenshot da página monitoring > Warnings mostrando uma mensagem que os nomes de utilizadores de grupos alargados não podiam ser descarregados](media/warnings-page.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as métricas e ferramentas estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o seu Cache Azure HPC](hpc-cache-support-ticket.md)
