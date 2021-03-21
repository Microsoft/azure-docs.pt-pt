---
title: Monitorize o seu dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Descreve como usar o portal Azure e a Web UI local para monitorizar o seu Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438796"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Monitorize o seu Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

Este artigo descreve como monitorizar o seu Azure Stack Edge Pro. Para monitorizar o seu dispositivo, pode utilizar o portal Azure ou a UI web local. Utilize o portal Azure para visualizar eventos do dispositivo, configurar e gerir alertas e ver métricas. Utilize a UI web local no seu dispositivo físico para visualizar o estado de hardware dos vários componentes do dispositivo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Ver eventos do dispositivo e os alertas correspondentes
> * Ver estado de hardware dos componentes do dispositivo
> * Ver métricas de capacidade e transação para o seu dispositivo

## <a name="view-device-events"></a>Ver eventos do dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Ver estado de hardware

Execute os passos seguintes na IU da Web local para ver o estado de hardware dos componentes do dispositivo.

1. Ligue-se à IU da Web local do dispositivo.
2. Vá para **o estado de hardware > manutenção**. Pode ver o estado de funcionamento dos vários componentes do dispositivo.

    ![Ver estado de hardware](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Ver métricas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Métricas no seu dispositivo

Esta secção descreve as métricas de monitorização do seu dispositivo. As métricas podem ser:

* Métricas de capacidade. As métricas de capacidade estão relacionadas com a capacidade do dispositivo.

* Métricas de transação. As métricas de transação estão relacionadas com as operações de leitura e escrita para o Azure Storage.

* Métricas de computação de borda. As métricas de cálculo Edge estão relacionadas com a utilização do cálculo Edge no seu dispositivo.

Uma lista completa das métricas é mostrada na tabela seguinte:

|Métricas de capacidade                     |Description  |
|-------------------------------------|-------------|
|**Capacidade disponível**               | Refere-se ao tamanho dos dados que podem ser escritos ao dispositivo. Por outras palavras, esta métrica é a capacidade que pode ser disponibilizada no dispositivo. <br></br>Pode libertar a capacidade do dispositivo eliminando a cópia local de ficheiros que têm uma cópia tanto no dispositivo como na nuvem.        |
|**Capacidade total**                   | Refere-se ao total de bytes no dispositivo para escrever dados, que também é referido como o tamanho total da cache local. <br></br> Agora pode aumentar a capacidade de um dispositivo virtual existente adicionando um disco de dados. Adicione um disco de dados através da gestão do hipervisor para o VM e, em seguida, reinicie o seu VM. O conjunto de armazenamento local do dispositivo Gateway expandir-se-á para acomodar o novo disco de dados adicionado. <br></br>Para obter mais informações, aceda a [Adicionar um disco rígido para máquina virtual Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Métricas de transação              | Description         |
|-------------------------------------|---------|
|**Bytes de nuvem carregados (dispositivo)**    | Soma de todos os bytes carregados em todas as ações do seu dispositivo        |
|**Bytes de nuvem carregados (partilhar)**     | Bytes carregados por ação. Esta métrica pode ser: <br></br> Avg, que é a (Soma de todos os bytes carregados por ação / Número de ações),  <br></br>Max, que é o número máximo de bytes carregados a partir de uma ação <br></br>Min, que é o número mínimo de bytes carregados a partir de uma parte      |
|**Produção de download em nuvem (partilhar)**| Bytes descarregados por ação. Esta métrica pode ser: <br></br> Avg, que é a (Soma de todos os bytes lidos ou descarregados para uma partilha / Número de ações) <br></br> Max, que é o número máximo de bytes descarregados de uma parte<br></br> e Min, que é o número mínimo de bytes descarregados de uma parte  |
|**Produção de leitura de nuvem**            | Soma de todos os bytes lidos da nuvem em todas as ações do seu dispositivo     |
|**Saída de upload de nuvem**          | Soma de todos os bytes escritos para a nuvem em todas as ações do seu dispositivo     |
|**Produção de upload de nuvem (partilhar)**  | Soma de todos os bytes escritos para a nuvem a partir de uma parte / # de ações é média, máx e min por ação      |
|**Ler a produção (rede)**           | Inclui a produção da rede de sistema para todos os bytes lidos a partir da nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego em todos os adaptadores de rede do dispositivo, incluindo adaptadores que não estão ligados ou ativados.      |
|**Escrever produção (rede)**       | Inclui a produção da rede de sistema para todos os bytes escritos na nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego em todos os adaptadores de rede do dispositivo, incluindo adaptadores que não estão ligados ou ativados.          |

| Métricas de computação de borda              | Description         |
|-------------------------------------|---------|
|**Edge compute - utilização da memória**      |           |
|**Edge compute - cpu percentual**    |         |

## <a name="next-steps"></a>Passos seguintes

Saiba como [Gerir a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).
Saiba como gerir notificações de [alerta de eventos do dispositivo](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
