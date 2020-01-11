---
title: Gerenciar e atualizar o cache HPC do Azure
description: Como gerenciar e atualizar o cache HPC do Azure usando o portal do Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: a166a904b2e63419efd5803fd54be1d1b59836fb
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867085"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Gerenciar seu cache do portal do Azure

A página Visão geral do cache na portal do Azure mostra detalhes do projeto, status do cache e estatísticas básicas para seu cache. Ele também tem controles para excluir o cache, liberar dados para o armazenamento de longo prazo ou atualizar o software.

Para abrir a página Visão geral, selecione o recurso de cache na portal do Azure. Por exemplo, carregue a página **todos os recursos** e clique no nome do cache.

![captura de tela de uma página de visão geral da instância do cache HPC do Azure](media/hpc-cache-overview.png)

Os botões na parte superior da página podem ajudá-lo a gerenciar o cache:

* [**Flush**](#flush-cached-data) -grava dados alterados para destinos de armazenamento
* [**Atualização**](#upgrade-cache-software) – atualiza o software de cache
* **Atualizar** – recarrega a página Visão geral
* [**Excluir**](#delete-the-cache) – destrói permanentemente o cache

Leia mais sobre essas opções abaixo.

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
> Para garantir que todos os dados no cache tenham sido gravados no armazenamento de longo prazo, siga este procedimento:
>
> 1. [Remova](hpc-cache-edit-storage.md#remove-a-storage-target) cada destino de armazenamento do cache HPC do Azure usando o botão excluir na página destinos de armazenamento. O sistema grava automaticamente todos os dados alterados do cache no sistema de armazenamento de back-end antes de remover o destino.
> 1. Aguarde até que o destino de armazenamento seja completamente removido. O processo pode levar uma hora ou mais se houver muitos dados para gravar do cache. Quando isso é feito, uma notificação do portal diz que a operação de exclusão foi bem-sucedida e o destino de armazenamento desaparece da lista.
> 1. Depois que todos os destinos de armazenamento afetados tiverem sido excluídos, será seguro excluir o cache.
>
> Como alternativa, você pode usar a opção de [liberação](#flush-cached-data) para salvar dados armazenados em cache, mas há um pequeno risco de perder trabalho se um cliente gravar uma alteração no cache após a liberação ser concluída, mas antes de a instância do cache ser destruída.

## <a name="cache-metrics-and-monitoring"></a>Métricas e monitoramento de cache

A página Visão geral mostra grafos para algumas estatísticas básicas de cache-taxa de transferência de cache, operações por segundo e latência.

![captura de tela de três gráficos de linha mostrando as estatísticas mencionadas acima para um cache de exemplo](media/hpc-cache-overview-stats.png)

Esses gráficos fazem parte das ferramentas internas de monitoramento e análise do Azure. Ferramentas e alertas adicionais estão disponíveis nas páginas no cabeçalho **monitoramento** na barra lateral do Portal. Saiba mais na seção portal da documentação de [monitoramento do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Passos seguintes

<!-- * Learn more about metrics and statistics for hpc cache -->
* Saiba mais sobre as [ferramentas de métricas e estatísticas do Azure](../azure-monitor/index.yml)
* Obtenha [ajuda com o cache do HPC do Azure](hpc-cache-support-ticket.md)
