---
title: Gerenciar e atualizar o cache HPC do Azure
description: Como gerenciar e atualizar o cache HPC do Azure usando o portal do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 62b54bfe120acdde1fd22c4a0d04165ea7243b50
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582205"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerenciar seu cache do portal do Azure

A página Visão geral do cache na portal do Azure mostra detalhes do projeto, status do cache e estatísticas básicas para seu cache. Ele também tem controles para excluir o cache, liberar dados para o armazenamento de longo prazo ou atualizar o software.

Para abrir a página Visão geral, selecione o recurso de cache na portal do Azure. Por exemplo, carregue a página **todos os recursos** e clique no nome do cache.

![captura de tela de uma página de visão geral da instância do cache HPC do Azure](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

Os botões na parte superior da página podem ajudá-lo a gerenciar o cache:

* [**Flush**](#flush-cached-data) – grava todos os dados armazenados em cache para destinos de armazenamento
* [**Atualização**](#upgrade-cache-software) – atualiza o software de cache
* **Atualizar** – recarrega a página Visão geral
* [**Excluir**](#delete-the-cache) – destrói permanentemente o cache

Leia mais sobre essas opções abaixo.

## <a name="flush-cached-data"></a>Liberar dados armazenados em cache

O botão **liberar** na página Visão geral informa o cache para gravar imediatamente todos os dados alterados armazenados no cache para os destinos de armazenamento de back-end. O cache salva rotineiramente os dados nos destinos de armazenamento, portanto, não é necessário fazer isso manualmente, a menos que você queira verificar se o sistema de armazenamento de back-end está atualizado. Por exemplo, você pode usar **flush** antes de tirar um instantâneo de armazenamento ou verificar o tamanho do conjunto de dados.

> [!NOTE]
> Durante o processo de liberação, o cache não pode atender às solicitações do cliente. O acesso ao cache é suspenso e continua após a conclusão da operação.

Quando você inicia a operação de liberação de cache, o cache para de aceitar solicitações de cliente e o status do cache na página de visão geral muda para a **liberação**.

Os dados no cache são salvos nos destinos de armazenamento apropriados. O processo pode levar alguns minutos ou pode levar uma hora ou mais, dependendo da quantidade de dados gravados no cache recentemente.

Depois que todos os dados são salvos em destinos de armazenamento, o cache começa automaticamente a executar solicitações de cliente novamente. O status do cache retorna para **íntegro**.

## <a name="upgrade-cache-software"></a>Atualizar o software de cache

Se uma nova versão de software estiver disponível, o botão **Atualizar** se tornará ativo. Você também poderá ver uma mensagem na parte superior da página sobre como atualizar o software.

![captura de tela da linha superior de botões com o botão de atualização habilitado](media/hpc-cache-upgrade-button.png)

O acesso do cliente não é interrompido durante uma atualização de software, mas o desempenho do cache fica mais lento. Planeje a atualização de software durante horas de uso fora do pico ou em um período de manutenção planejada.

A atualização de software pode levar várias horas. Os caches configurados com maior taxa de transferência levam mais tempo para atualizar que os caches com valores menores de taxa de transferência de pico.

Quando uma atualização de software estiver disponível, você terá vários dias para aplicá-la manualmente. A data de término é listada na mensagem de atualização. Se você não atualizar durante esse tempo, o Azure aplicará automaticamente a atualização ao seu cache. O tempo de atualização automática não é configurável. Se você estiver preocupado em afetar o desempenho do cache, você deve atualizar o software por conta própria antes que o período de tempo expire.

Clique no botão **Atualizar** para iniciar a atualização de software. O status do cache muda para **atualizando** até que a operação seja concluída.

## <a name="delete-the-cache"></a>Excluir o cache

O botão **excluir** destrói o cache. Quando você exclui um cache, todos os seus recursos são destruídos e não incorrem mais em encargos de conta.

Os destinos de armazenamento não são afetados quando você exclui o cache. Você pode adicioná-los a um cache futuro posteriormente ou descomissiona-los separadamente.

O cache libera automaticamente todos os dados não salvos para destinos de armazenamento como parte de seu desligamento final.

## <a name="cache-metrics-and-monitoring"></a>Métricas e monitoramento de cache

A página Visão geral mostra grafos para algumas estatísticas básicas de cache-taxa de transferência de cache, operações por segundo e latência.

![captura de tela de três gráficos de linha mostrando as estatísticas mencionadas acima para um cache de exemplo](media/hpc-cache-overview-stats.png)

Esses gráficos fazem parte das ferramentas internas de monitoramento e análise do Azure. Ferramentas e alertas adicionais estão disponíveis nas páginas no cabeçalho **monitoramento** na barra lateral do Portal. Saiba mais na seção portal da documentação de [monitoramento do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Passos seguintes

<!-- * Learn more about metrics and statistics for hpc cache -->
* Saiba mais sobre as [ferramentas de métricas e estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o cache do HPC do Azure](hpc-cache-support-ticket.md)
