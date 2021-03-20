---
title: Monitorize o seu dispositivo Azure Data Box Gateway | Microsoft Docs
description: Descreve como usar o portal Azure e a UI web local para monitorizar o seu Gateway de Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582484"
---
# <a name="monitor-your-azure-data-box-gateway"></a>Monitorize o seu Gateway de Caixa de Dados Azure

Este artigo descreve como monitorizar o seu Gateway de caixa de dados Azure. Para monitorizar o seu dispositivo, pode utilizar o portal Azure ou a UI web local. Utilize o portal Azure para visualizar eventos do dispositivo, configurar e gerir alertas e ver métricas.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Ver eventos do dispositivo e os alertas correspondentes
> * Ver métricas de capacidade e transação para o seu dispositivo
> * Configurar e gerir alertas

## <a name="view-device-events"></a>Ver eventos do dispositivo

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>Ver métricas

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Métricas no seu dispositivo

Esta secção descreve as métricas de monitorização do seu dispositivo. As métricas podem ser:

* Métricas de capacidade. As métricas de capacidade estão relacionadas com a capacidade do dispositivo.

* Métricas de transação. As métricas de transação estão relacionadas com as operações de leitura e escrita para o Azure Storage.

Uma lista completa das métricas é mostrada na tabela seguinte:

|Métricas de capacidade                     |Description  |
|-------------------------------------|-------------|
|**Capacidade disponível**               | Refere-se ao tamanho dos dados que podem ser escritos ao dispositivo. Por outras palavras, esta é a capacidade que pode ser disponibilizada no dispositivo. <br></br>Pode libertar a capacidade do dispositivo eliminando a cópia local de ficheiros que têm uma cópia tanto no dispositivo como na nuvem.        |
|**Capacidade total**                   | Refere-se aos bytes totais do dispositivo para escrever dados. Isto também é referido como o tamanho total da cache local. <br></br> Agora pode aumentar a capacidade de um dispositivo virtual existente adicionando um disco de dados. Adicione um disco de dados através da gestão do hipervisor para o VM e, em seguida, reinicie o seu VM. O conjunto de armazenamento local do dispositivo Gateway expandir-se-á para acomodar o novo disco de dados adicionado. <br></br>Para obter mais informações, aceda a [Adicionar um disco rígido para máquina virtual Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Métricas de transação              | Description         |
|-------------------------------------|---------|
|**Bytes de nuvem carregados (dispositivo)**    | Soma de todos os bytes carregados em todas as ações do seu dispositivo        |
|**Bytes de nuvem carregados (partilhar)**     | Bytes carregados por ação. Este pode ser: <br></br> Avg, que é a (Soma de todos os bytes carregados por ação / Número de ações),  <br></br>Max, que é o número máximo de bytes carregados a partir de uma ação <br></br>Min, que é o número mínimo de bytes carregados a partir de uma parte      |
|**Produção de download em nuvem (partilhar)**| Bytes descarregados por ação. Este pode ser: <br></br> Avg, que é a (Soma de todos os bytes lidos ou descarregados para uma partilha / Número de ações) <br></br> Max, que é o número máximo de bytes descarregados de uma parte<br></br> e Min, que é o número mínimo de bytes descarregados de uma parte  |
|**Produção de leitura de nuvem**            | Soma de todos os bytes lidos da nuvem em todas as ações do seu dispositivo     |
|**Saída de upload de nuvem**          | Soma de todos os bytes escritos para a nuvem em todas as ações do seu dispositivo     |
|**Produção de upload de nuvem (partilhar)**  | Soma de todos os bytes escritos para a nuvem a partir de uma parte / # de ações é média, máx e min por ação      |
|**Ler a produção (rede)**           | Inclui a produção da rede de sistema para todos os bytes lidos a partir da nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego em todos os adaptadores de rede do dispositivo. Isto inclui adaptadores que não estão ligados ou ativados.      |
|**Escrever produção (rede)**       | Inclui a produção da rede de sistema para todos os bytes escritos na nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego em todos os adaptadores de rede do dispositivo. Isto inclui adaptadores que não estão ligados ou ativados.          |

## <a name="manage-alerts"></a>Gerir alertas

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba como [Gerir a largura de banda](data-box-gateway-manage-bandwidth-schedules.md).
