---
title: Monitor Azure File Sync | de nivelamento de nuvem Microsoft Docs
description: Detalhes sobre as métricas a utilizar para monitorizar as suas políticas de nivelamento em nuvem.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93e9d9a552ab1bff16ca15d6bb11faeb5d47a503
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204453"
---
# <a name="monitor-cloud-tiering"></a>Monitorizar o nível da nuvem
Existem duas formas de monitorizar a sua política de nivelamento em nuvem: a lâmina de propriedades do ponto final do servidor e o Monitor Azure.

## <a name="monitoring-via-server-endpoint"></a>Monitorização através do ponto final do servidor

Faça o sôming no [portal Azure](https://portal.azure.com/)e, em seguida, navegue para a lâmina de propriedades do **ponto final** do servidor para o ponto final do servidor que pretende monitorizar e desloque-se para a secção de nivelamento da nuvem. 

![Uma imagem da secção de nivelamento da nuvem nas propriedades do ponto final do servidor.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**A poupança de** espaço é a quantidade de espaço guardada permitindo o tiering da nuvem. Se o seu tamanho de volume local for grande o suficiente para conter todos os seus dados, terá 0% de poupança de espaço. Se tiver 0% ou uma baixa percentagem de poupança de espaço, pode indicar que o tiering de nuvens não é benéfico com o tamanho atual da cache local. 

**A taxa de sucesso da cache** é a percentagem de ficheiros que abre na cache local. A taxa de certeing é calculada por ficheiros abertos diretamente a partir do número total de ficheiros abertos. Uma taxa de 100% de cache significa que todos os ficheiros que abriste na última hora já estavam na tua cache local. Se o seu objetivo é reduzir a saída, isto indica que a sua política atual está a funcionar bem.

> [!NOTE]
> Cargas de trabalho com padrões de acesso aleatórios normalmente têm taxas de acesso de cache mais baixas. 

**Tamanho total (nuvem)** é o tamanho dos seus ficheiros que foram sincronizados com a nuvem. 

**O tamanho em cache (servidor)** é o tamanho total dos ficheiros no seu servidor, tanto descarregados como tiered. Às vezes, o tamanho em cache pode ser maior do que o tamanho total dos seus ficheiros na nuvem. Variáveis como o tamanho do cluster do volume no servidor podem causar isto. Se o tamanho em cache for maior do que o deseja, considere aumentar a sua política de espaço livre de volume. 

**A política de volume eficaz** é utilizada pelo Azure File Sync para determinar quanto espaço livre para deixar no volume em que o ponto final do servidor está ligado. Quando existem vários pontos finais do servidor no mesmo volume, a política de espaço livre de volume mais elevada entre os pontos finais do servidor torna-se a política de volume eficaz para todos os pontos finais do servidor nesse volume. Por exemplo, se existirem dois pontos finais do servidor no mesmo volume, um com 30% de espaço livre de volume e outro com 50% de espaço livre de volume, a política de espaço livre de volume eficaz para ambos os pontos finais do servidor será de 50%.

**O espaço livre de volume atual** é o espaço livre de volume atualmente disponível no seu servidor no local. Se tiver uma saída alta mas mais espaço livre de volume disponível antes do início da sua política de espaço livre de volume, considere desativar a sua política de data. Outra questão pode ser que, fora dos ficheiros atualmente hierárquicos, o ficheiro mais recentemente acedido é maior do que o espaço livre de volume que resta antes do início da apólice. Neste caso, considere aumentar o seu volume local. 

## <a name="monitoring-via-azure-monitor"></a>Monitorização via Azure Monitor

Vá ao **Serviço de Sincronização de Armazenamento** e selecione **Métricas** na navegação lateral. 

Existem três métricas diferentes que pode usar para monitorizar a sua saída especificamente a partir do nível da nuvem:

- Tamanho de recuperação de camadas de nuvem
    - Esta é a dimensão total dos dados recolhidos nos bytes e pode ser usada para identificar a quantidade de dados que está a ser recolhido.
- Tamanho de recolha de camadas de nuvem por aplicação
    - Esta é a dimensão total dos dados recolhidos em bytes por uma aplicação e pode ser usado para identificar o que está a recordar os dados.
- Produção de recordação de camadas de nuvem
    - Isto mede a rapidez com que os dados estão a ser recolhidos em bytes e devem ser utilizados se tiver preocupações com o desempenho. 

Para ser mais específico sobre o que pretende que os seus gráficos sejam exibidos, considere utilizar **o Filtro de Adicionar** e Aplicar A **Divisão**.
 
Para obter detalhes sobre os diferentes tipos de métricas para Azure File Sync e como usá-las, consulte [Monitor Azure File Sync](storage-sync-files-monitoring.md).

Para mais detalhes sobre como usar métricas, consulte [Começar com o Azure Metrics Explorer.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

Se quiser alterar a sua política de nivelamento em nuvem, consulte [as políticas de tiering de nuvens](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Passos seguintes
* [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)