---
title: Gerenciar e atualizar o cache HPC do Azure
description: Como gerenciar e atualizar o cache HPC do Azure usando o portal do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 9ad6348e15c8a25f721a89be7eab3e17c58ae17c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988891"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerenciar seu cache do portal do Azure

A página Visão geral do cache na portal do Azure mostra detalhes do projeto, status do cache e estatísticas básicas para seu cache. Também tem controlos para parar ou iniciar a cache, eliminar a cache, lavar os dados para armazenamento a longo prazo e atualizar o software.

Para abrir a página Visão geral, selecione o recurso de cache na portal do Azure. Por exemplo, carregue a página **todos os recursos** e clique no nome do cache.

![captura de tela de uma página de visão geral da instância do cache HPC do Azure](media/hpc-cache-overview.png)

Os botões na parte superior da página podem ajudá-lo a gerenciar o cache:

* **Iniciar** e [**Parar**](#stop-the-cache) - Suspende operação de cache
* [**Flush**](#flush-cached-data) -grava dados alterados para destinos de armazenamento
* [**Atualização**](#upgrade-cache-software) – atualiza o software de cache
* **Atualizar** – recarrega a página Visão geral
* [**Excluir**](#delete-the-cache) – destrói permanentemente o cache

Leia mais sobre essas opções abaixo.

## <a name="stop-the-cache"></a>Pare a cache

Pode parar a cache para reduzir custos durante um período inativo. Não é cobrado para o tempo de espera enquanto a cache é parada, mas é cobrado pelo armazenamento de disco atribuído à cache. (Consulte a página [de preços](https://aka.ms/hpc-cache-pricing) para mais detalhes.)

Uma cache parada não responde aos pedidos dos clientes. Devia desmontar clientes antes de parar a cache.

O botão **Stop** suspende uma cache ativa. O botão **Stop** está disponível quando o estado de uma cache estiver **saudável** ou **degradado**.

![screenshot dos botões superiores com Stop destacado e uma mensagem pop-up descrevendo a ação stop e perguntando 'quer continuar?' com Sim (padrão) e nenhum botão](media/stop-cache.png)

Depois de clicar em Sim para confirmar a paragem da cache, a cache automaticamente descarrega o seu conteúdo para os alvos de armazenamento. Este processo pode demorar algum tempo, mas garante a consistência dos dados. Finalmente, o estado da cache muda para **Stop**.

Para reativar uma cache parada, clique no botão **Iniciar.** Não é necessária nenhuma confirmação.

![screenshot dos botões superiores com Início destacado](media/start-cache.png)

## <a name="flush-cached-data"></a>Liberar dados armazenados em cache

O botão **liberar** na página Visão geral informa o cache para gravar imediatamente todos os dados alterados armazenados no cache para os destinos de armazenamento de back-end. O cache salva rotineiramente os dados nos destinos de armazenamento, portanto, não é necessário fazer isso manualmente, a menos que você queira verificar se o sistema de armazenamento de back-end está atualizado. Por exemplo, você pode usar **flush** antes de tirar um instantâneo de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de liberação, o cache não pode atender às solicitações do cliente. O acesso ao cache é suspenso e continua após a conclusão da operação.

![captura de tela dos botões superior com liberação realçada e uma mensagem pop-up descrevendo a ação de liberação e perguntando "deseja continuar?" com Sim (padrão) e nenhum botão](media/hpc-cache-flush.png)

Quando você inicia a operação de liberação de cache, o cache para de aceitar solicitações de cliente e o status do cache na página de visão geral muda para a **liberação**.

Os dados no cache são salvos nos destinos de armazenamento apropriados. Dependendo da quantidade de dados que precisa ser liberada, o processo pode levar alguns minutos ou mais de uma hora.

Depois que todos os dados são salvos em destinos de armazenamento, o cache começa automaticamente a executar solicitações de cliente novamente. O status do cache retorna para **íntegro**.

## <a name="upgrade-cache-software"></a>Atualizar o software de cache

Se uma nova versão de software estiver disponível, o botão **Atualizar** se tornará ativo. Você também deverá ver uma mensagem na parte superior da página sobre como atualizar o software.

![captura de tela da linha superior de botões com o botão de atualização habilitado](media/hpc-cache-upgrade-button.png)

O acesso do cliente não é interrompido durante uma atualização de software, mas o desempenho do cache fica mais lento. Planeje a atualização de software durante horas de uso fora do pico ou em um período de manutenção planejada.

A atualização de software pode levar várias horas. Os caches configurados com maior taxa de transferência levam mais tempo para atualizar que os caches com valores menores de taxa de transferência de pico.

Quando uma atualização de software estiver disponível, você terá uma semana ou mais para aplicá-la manualmente. A data de término é listada na mensagem de atualização. Se você não atualizar durante esse tempo, o Azure aplicará automaticamente a atualização ao seu cache. O tempo de atualização automática não é configurável. Se você estiver preocupado com o impacto no desempenho do cache, atualize o software por conta própria antes que o período de tempo expire.

Clique no botão **Atualizar** para iniciar a atualização de software. O status do cache muda para **atualizando** até que a operação seja concluída.

## <a name="delete-the-cache"></a>Excluir o cache

O botão **excluir** destrói o cache. Quando você exclui um cache, todos os seus recursos são destruídos e não incorrem mais em encargos de conta.

Os volumes de armazenamento de back-end usados como destinos de armazenamento não são afetados quando você exclui o cache. Você pode adicioná-los a um cache futuro posteriormente ou descomissiona-los separadamente.

> [!NOTE]
> O cache HPC do Azure não grava automaticamente os dados alterados do cache nos sistemas de armazenamento de back-end antes de excluir o cache.
>
> Para se certificar de que todos os dados da cache foram escritos para armazenamento a longo prazo, [pare a cache](#stop-the-cache) antes de o apagar. Certifique-se de que mostra o estado **parado** antes de clicar no botão de apagar.
<!--... written to long-term storage, follow this procedure:
>
> 1. [Remove](hpc-cache-edit-storage.md#remove-a-storage-target) each storage target from the Azure HPC Cache by using the delete button on the Storage targets page. The system automatically writes any changed data from the cache to the back-end storage system before removing the target.
> 1. Wait for the storage target to be completely removed. The process can take an hour or longer if there is a lot of data to write from the cache. When it is done, a portal notification says that the delete operation was successful, and the storage target disappears from the list.
> 1. After all affected storage targets have been deleted, it is safe to delete the cache.
>
> Alternatively, you can use the [flush](#flush-cached-data) option to save cached data, but there is a small risk of losing work if a client writes a change to the cache after the flush completes but before the cache instance is destroyed.-->

## <a name="cache-metrics-and-monitoring"></a>Métricas e monitoramento de cache

A página Visão geral mostra grafos para algumas estatísticas básicas de cache-taxa de transferência de cache, operações por segundo e latência.

![captura de tela de três gráficos de linha mostrando as estatísticas mencionadas acima para um cache de exemplo](media/hpc-cache-overview-stats.png)

Esses gráficos fazem parte das ferramentas internas de monitoramento e análise do Azure. Ferramentas e alertas adicionais estão disponíveis nas páginas no cabeçalho **monitoramento** na barra lateral do Portal. Saiba mais na seção portal da documentação de [monitoramento do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Passos seguintes

<!-- * Learn more about metrics and statistics for hpc cache -->
* Saiba mais sobre as [ferramentas de métricas e estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o cache do HPC do Azure](hpc-cache-support-ticket.md)
