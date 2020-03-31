---
title: Gerir e atualizar o Cache Azure HPC
description: Como gerir e atualizar o Azure HPC Cache utilizando o portal Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: da260074fc69fac9e98d3698bb2d40fdf80d7118
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252047"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerencie a sua cache a partir do portal Azure

A página de visão geral do portal Azure mostra detalhes do projeto, estado de cache e estatísticas básicas para a sua cache. Também tem controlos para parar ou iniciar a cache, eliminar a cache, lavar os dados para armazenamento a longo prazo e atualizar o software.

Para abrir a página de visão geral, selecione o seu recurso cache no portal Azure. Por exemplo, carregue a página **De todos os recursos** e clique no nome cache.

![screenshot de uma página de visão geral da instância de Cache HPC Azure](media/hpc-cache-overview.png)

Os botões na parte superior da página podem ajudá-lo a gerir a cache:

* **Iniciar** e [**Parar**](#stop-the-cache) - Suspende operação de cache
* [**Flush**](#flush-cached-data) - Escreve dados alterados para alvos de armazenamento
* [**Upgrade**](#upgrade-cache-software) - Atualiza o software cache
* **Refresh** - Recargas a página de visão geral
* [**Delete**](#delete-the-cache) - Destrói permanentemente a cache

Leia mais sobre estas opções abaixo.

## <a name="stop-the-cache"></a>Pare a cache

Pode parar a cache para reduzir custos durante um período inativo. Não é cobrado para o tempo de espera enquanto a cache é parada, mas é cobrado pelo armazenamento de disco atribuído à cache. (Consulte a página [de preços](https://aka.ms/hpc-cache-pricing) para mais detalhes.)

Uma cache parada não responde aos pedidos dos clientes. Devia desmontar clientes antes de parar a cache.

O botão **Stop** suspende uma cache ativa. O botão **Stop** está disponível quando o estado de uma cache estiver **saudável** ou **degradado**.

![screenshot dos botões superiores com Stop destacado e uma mensagem pop-up descrevendo a ação stop e perguntando 'quer continuar?' com Sim (padrão) e Sem botões](media/stop-cache.png)

Depois de clicar em Sim para confirmar a paragem da cache, a cache automaticamente descarrega o seu conteúdo para os alvos de armazenamento. Este processo pode demorar algum tempo, mas garante a consistência dos dados. Finalmente, o estado da cache muda para **Stop**.

Para reativar uma cache parada, clique no botão **Iniciar.** Não é necessária nenhuma confirmação.

![screenshot dos botões superiores com Início destacado](media/start-cache.png)

## <a name="flush-cached-data"></a>Lavar dados em cache

O botão **Flush** na página de visão geral diz à cache para escrever imediatamente todos os dados alterados que são armazenados na cache para os alvos de armazenamento traseiro. A cache guarda rotineiramente dados para os alvos de armazenamento, por isso não é necessário fazê-lo manualmente, a menos que queira certificar-se de que o sistema de armazenamento de back-end está atualizado. Por exemplo, pode utilizar **o Flush** antes de tirar uma fotografia de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de descarga, a cache não pode servir os pedidos dos clientes. O acesso à cache é suspenso e retoma após o fim da operação.

![screenshot dos botões superiores com Flush realçado e uma mensagem pop-up descrevendo a ação de flush e perguntando 'você quer continuar?' com Sim (padrão) e Sem botões](media/hpc-cache-flush.png)

Quando inicia a operação de cache flush, a cache deixa de aceitar pedidos de clientes e o estado de cache na página geral altera-se em **Flushing**.

Os dados na cache são guardados para os alvos de armazenamento apropriados. Dependendo da quantidade de dados necessários para ser lavado, o processo pode demorar alguns minutos ou mais de uma hora.

Depois de todos os dados ser guardados para alvos de armazenamento, a cache começa automaticamente a receber pedidos de clientes novamente. O estado da cache volta a **Healthy**.

## <a name="upgrade-cache-software"></a>Atualizar software cache

Se estiver disponível uma nova versão de software, o botão **Upgrade** torna-se ativo. Também deve ver uma mensagem no topo da página sobre a atualização do software.

![screenshot da linha superior dos botões com o botão upgrade ativado](media/hpc-cache-upgrade-button.png)

O acesso ao cliente não é interrompido durante uma atualização de software, mas o desempenho da cache abranda. Planeie atualizar o software durante as horas de utilização não-máximas ou num período de manutenção planeado.

A atualização do software pode demorar várias horas. Caches configurados com uma entrada mais alta demoram mais tempo a atualizar do que caches com valores de pico mais pequenos.

Quando estiver disponível uma atualização de software, terá uma semana ou mais para aplicá-la manualmente. A data final está listada na mensagem de atualização. Se não atualizar durante esse período, o Azure aplica automaticamente a atualização à sua cache. O tempo da atualização automática não é configurável. Se estiver preocupado com o impacto do desempenho da cache, deverá atualizar o software antes do termo do prazo.

Se a sua cache for interrompida quando a data final passar, a cache irá automaticamente atualizar o software da próxima vez que for iniciado. (A atualização pode não começar imediatamente, mas começará na primeira hora.)

Clique no botão **Upgrade** para iniciar a atualização do software. O estado da cache muda para **atualizar** até que a operação esteja concluída.

## <a name="delete-the-cache"></a>Apagar a cache

O botão **Delete** destrói a cache. Quando se elimina uma cache, todos os seus recursos são destruídos e já não incorrem em encargos de conta.

Os volumes de armazenamento traseiroutilizados como alvos de armazenamento não são afetados quando elimina a cache. Pode adicioná-los a uma cache futura mais tarde, ou desencomendá-los separadamente.

> [!NOTE]
> A Cache Azure HPC não escreve automaticamente dados alterados da cache para os sistemas de armazenamento traseiros antes de apagar a cache.
>
> Para se certificar de que todos os dados da cache foram escritos para armazenamento a longo prazo, [pare a cache](#stop-the-cache) antes de o apagar. Certifique-se de que mostra o estado **parado** antes de clicar no botão de apagar.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Métricas de cache e monitorização

A página geral mostra gráficos para algumas estatísticas básicas de cache - entrada em cache, operações por segundo, e latência.

![screenshot de gráficos de três linhas mostrando as estatísticas acima mencionadas para uma cache de amostra](media/hpc-cache-overview-stats.png)

Estes gráficos fazem parte das ferramentas de monitorização e análise incorporadas do Azure. Ferramentas e alertas adicionais estão disponíveis a partir das páginas sob a rubrica **monitora** na barra lateral do portal. Saiba mais na secção portal da documentação de [Monitorização Do Azure.](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)

## <a name="next-steps"></a>Passos seguintes

<!-- * Learn more about metrics and statistics for hpc cache -->
* Saiba mais sobre [métricas azure e ferramentas estatísticas](../azure-monitor/index.yml)
* Obtenha [ajuda com o seu Cache Azure HPC](hpc-cache-support-ticket.md)
