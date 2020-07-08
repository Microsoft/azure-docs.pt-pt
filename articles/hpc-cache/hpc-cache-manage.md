---
title: Gerir e atualizar cache Azure HPC
description: Como gerir e atualizar a Cache Azure HPC utilizando o portal Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 825b8a34e130286a5772363107311fe4170e8743
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515555"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerencie a sua cache a partir do portal Azure

A página geral da cache no portal Azure mostra detalhes do projeto, estado de cache e estatísticas básicas para o seu cache. Também tem controlos para parar ou iniciar a cache, eliminar a cache, lavar dados para armazenamento a longo prazo e atualizar o software.

Para abrir a página geral, selecione o seu recurso cache no portal Azure. Por exemplo, carregue a página **De Todos os recursos** e clique no nome cache.

![screenshot de uma página geral da cache de um Azure HPC Cache](media/hpc-cache-overview.png)

Os botões no topo da página podem ajudá-lo a gerir a cache:

* **Iniciar** e [**Parar**](#stop-the-cache) - Suspende a operação de cache
* [**Flush**](#flush-cached-data) - Escreve dados alterados para alvos de armazenamento
* [**Upgrade**](#upgrade-cache-software) - Atualiza o software cache
* **Refresh** - Recarregue a página geral
* [**Excluir**](#delete-the-cache) - Destrói permanentemente a cache

Leia mais sobre estas opções abaixo.

Clique na imagem abaixo para ver um [vídeo](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) que demonstra tarefas de gestão de cache.

[![miniatura de vídeo: Azure HPC Cache: Gerir (clique para visitar a página de vídeo)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Pare a cache

Pode parar a cache para reduzir os custos durante um período inativo. Não é cobrado durante o tempo de paragem da cache, mas é cobrado pelo armazenamento do disco atribuído pelo cache. (Consulte [a](https://aka.ms/hpc-cache-pricing) página de preços para mais detalhes.)

Uma cache parada não responde aos pedidos dos clientes. Deve desmontar os clientes antes de parar a cache.

O **botão Stop** suspende uma cache ativa. O botão **Stop** está disponível quando o estado de um cache é **saudável** ou **degradado**.

![screenshot dos botões superiores com Stop realçado e uma mensagem pop-up descrevendo a ação stop e perguntando 'você quer continuar?' com Sim (padrão) e Sem botões](media/stop-cache.png)

Depois de clicar em Sim para confirmar a paragem da cache, a cache coloca automaticamente o seu conteúdo nos alvos de armazenamento. Este processo pode demorar algum tempo, mas garante a consistência dos dados. Finalmente, o estado da cache muda para **Stop**.

Para reativar uma cache parada, clique no botão **Iniciar.** Não é necessária confirmação.

![screenshot dos botões superiores com Start realçado](media/start-cache.png)

## <a name="flush-cached-data"></a>Dados em cache de descarga

O botão **Flush** na página de visão geral diz à cache para escrever imediatamente todos os dados alterados que são armazenados na cache para os alvos de armazenamento de back-end. A cache guarda rotineiramente dados para os alvos de armazenamento, por isso não é necessário fazê-lo manualmente, a menos que queira certificar-se de que o sistema de armazenamento de back-end está atualizado. Por exemplo, pode utilizar **flush** antes de tirar uma foto de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de descarga, a cache não pode servir os pedidos do cliente. O acesso à cache é suspenso e retomado após o fim da operação.

![screenshot dos botões superiores com Flush realçado e uma mensagem pop-up descrevendo a ação de descarga e perguntando 'você quer continuar?' com Sim (padrão) e Sem botões](media/hpc-cache-flush.png)

Quando inicia a operação de descarga de cache, a cache deixa de aceitar pedidos do cliente e o estado da cache na página geral muda para **Flushing**.

Os dados na cache são guardados para os alvos de armazenamento apropriados. Dependendo da quantidade de dados necessários para ser lavado, o processo pode demorar alguns minutos ou mais de uma hora.

Depois de todos os dados são guardados para alvos de armazenamento, a cache começa automaticamente a receber os pedidos do cliente novamente. O estado da cache regressa ao **Healthy**.

## <a name="upgrade-cache-software"></a>Atualizar o software cache

Se estiver disponível uma nova versão de software, o botão **De atualização** torna-se ativo. Também deve ver uma mensagem no topo da página sobre a atualização do software.

![screenshot da linha superior de botões com o botão de upgrade ativado](media/hpc-cache-upgrade-button.png)

O acesso ao cliente não é interrompido durante uma atualização de software, mas o desempenho da cache abranda. Planeie atualizar o software durante as horas de utilização não-pico ou num período de manutenção planeado.

A atualização de software pode demorar várias horas. Caches configurados com maior produção demoram mais tempo a atualizar do que caches com valores de produção de pico mais pequenos.

Quando uma atualização de software estiver disponível, terá uma semana ou mais para aplicá-la manualmente. A data de fim está listada na mensagem de atualização. Se não atualizar durante esse tempo, o Azure aplica automaticamente a atualização ao seu cache. O tempo da atualização automática não é configurável. Se estiver preocupado com o impacto do desempenho da cache, deve atualizar o software antes do termo do período de tempo.

Se o seu cache for interrompido quando a data de fim passar, a cache atualizará automaticamente o software da próxima vez que for iniciado. (A atualização pode não começar imediatamente, mas começará na primeira hora.)

Clique no botão **Deabos para** iniciar a atualização do software. O estado da cache muda para **atualização** até que a operação esteja concluída.

## <a name="delete-the-cache"></a>Apagar a cache

O botão **Delete** destrói a cache. Quando se apaga uma cache, todos os seus recursos são destruídos e já não incorrem em encargos de conta.

Os volumes de armazenamento de back-end utilizados como alvos de armazenamento não são afetados quando se elimina a cache. Pode adicioná-los a uma cache futura mais tarde, ou desativá-los separadamente.

> [!NOTE]
> A Azure HPC Cache não escreve automaticamente dados alterados da cache para os sistemas de armazenamento de back-end antes de eliminar a cache.
>
> Para se certificar de que todos os dados da cache foram escritos para armazenamento a longo prazo, [pare a cache](#stop-the-cache) antes de eliminá-lo. Certifique-se de que mostra o estado **parado** antes de clicar no botão eliminar.

## <a name="cache-metrics-and-monitoring"></a>Cache métricas e monitorização

A página geral mostra gráficos para algumas estatísticas básicas de cache - produção de cache, operações por segundo, e latência.

![screenshot de três gráficos de linha mostrando as estatísticas acima mencionadas para uma cache de amostra](media/hpc-cache-overview-stats.png)

Estes gráficos fazem parte das ferramentas de monitorização e análise incorporadas da Azure. Estão disponíveis ferramentas e alertas adicionais a partir das páginas sob o título **de Monitorização** na barra lateral do portal. Saiba mais na secção do portal da [documentação de Monitorização do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [as métricas e ferramentas estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o seu Cache Azure HPC](hpc-cache-support-ticket.md)
