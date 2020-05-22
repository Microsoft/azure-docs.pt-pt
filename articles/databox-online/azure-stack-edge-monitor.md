---
title: Monitorize o seu dispositivo Azure Stack Edge [ Microsoft Docs
description: Descreve como usar o portal Azure e a Web UI local para monitorizar o seu Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: ac762450b19f2d3f82b6859de23e07afe2d10629
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780347"
---
# <a name="monitor-your-azure-stack-edge"></a>Monitorize o seu Edge Azure Stack

Este artigo descreve como monitorizar o seu Azure Stack Edge. Para monitorizar o seu dispositivo, pode utilizar o portal Azure ou a UI web local. Utilize o portal Azure para visualizar eventos de dispositivos, configurar e gerir alertas e ver métricas. Utilize a UI web local no seu dispositivo físico para visualizar o estado do hardware dos vários componentes do dispositivo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Ver eventos de dispositivos e os alertas correspondentes
> * Ver o estado do hardware dos componentes do dispositivo
> * Ver métricas de capacidade e transação para o seu dispositivo
> * Configurar e gerir alertas

## <a name="view-device-events"></a>Ver eventos de dispositivos

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Ver estado de hardware

Tome os seguintes passos na UI web local para ver o estado do hardware dos componentes do seu dispositivo.

1. Ligue-se à Web UI local do seu dispositivo.
2. Vá para o **estado de hardware de manutenção >**. Pode ver a saúde dos vários componentes do dispositivo.

    ![Ver estado de hardware](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Ver métricas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Métricas no seu dispositivo

Esta secção descreve as métricas de monitorização no seu dispositivo. As métricas podem ser:

* Métricas de capacidade. As métricas de capacidade estão relacionadas com a capacidade do dispositivo.

* Métricas de transação. As métricas de transação estão relacionadas com as operações de leitura e escrita para o Armazenamento Azure.

* Métricas de computação de borda. As métricas de computação Edge estão relacionadas com o uso da computação Edge no seu dispositivo.

Uma lista completa das métricas é mostrada na tabela seguinte:

|Métricas de capacidade                     |Descrição  |
|-------------------------------------|-------------|
|**Capacidade disponível**               | Refere-se ao tamanho dos dados que podem ser escritos ao dispositivo. Por outras palavras, esta é a capacidade que pode ser disponibilizada no dispositivo. <br></br>Pode libertar a capacidade do dispositivo apagando a cópia local dos ficheiros que têm uma cópia tanto no dispositivo como na nuvem.        |
|**Capacidade total**                   | Refere-se aos bytes totais do dispositivo para escrever dados. Isto também é referido como o tamanho total da cache local. <br></br> Agora pode aumentar a capacidade de um dispositivo virtual existente adicionando um disco de dados. Adicione um disco de dados através da gestão do hipervisor para o VM e, em seguida, reinicie o seu VM. O conjunto de armazenamento local do dispositivo Gateway expandir-se-á para acomodar o disco de dados recém-adicionado. <br></br>Para mais informações, vá adicionar [um disco rígido para a máquina virtual Hyper-V](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Métricas de transação              | Descrição         |
|-------------------------------------|---------|
|**Bytes de nuvem carregados (dispositivo)**    | Soma de todos os bytes enviados em todas as ações do seu dispositivo        |
|**Bytes de nuvem carregados (partilhar)**     | Bytes carregadopor ação. Este pode ser: <br></br> Avg, que é a (Soma de todos os bytes carregados por ação/ Número de ações),  <br></br>Max, que é o número máximo de bytes carregados a partir de uma ação <br></br>Min, que é o número mínimo de bytes carregados a partir de uma ação      |
|**Entrada de download em nuvem (partilha)**| Bytes descarregados por ação. Este pode ser: <br></br> Avg, que é a (Soma de todos os bytes ler ou ser descarregado para uma parte / Número de ações) <br></br> Max, que é o número máximo de bytes descarregados de uma parte<br></br> e Min, que é o número mínimo de bytes descarregados de uma parte  |
|**Cloud ler a entrada**            | Soma de todos os bytes lidos da nuvem em todas as ações do seu dispositivo     |
|**Entrada de upload de nuvem**          | Soma de todos os bytes escritos à nuvem em todas as ações do seu dispositivo     |
|**Cloud upload de entrada (partilha)**  | Soma de todos os bytes escritos à nuvem a partir de uma parte / # de ações é média, máx e min por ação      |
|**Ler a entrada (rede)**           | Inclui a entrada da rede do sistema para todos os bytes lidos a partir da nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego sobre todos os adaptadores de rede no dispositivo. Isto inclui adaptadores que não estão ligados ou ativados.      |
|**Escrever a entrada (rede)**       | Inclui a entrada da rede do sistema para todos os bytes escritos na nuvem. Esta visão pode incluir dados que não se limitam a ações. <br></br>A divisão mostrará o tráfego sobre todos os adaptadores de rede no dispositivo. Isto inclui adaptadores que não estão ligados ou ativados.          |

| Métricas de computação de borda              | Descrição         |
|-------------------------------------|---------|
|**Computação de borda - uso da memória**      |           |
|**Computação de borda - CPU percentual**    |         |

## <a name="manage-alerts"></a>Gerir alertas

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba como [Gerir a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).
