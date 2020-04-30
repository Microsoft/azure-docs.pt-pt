---
title: Script para permitir Kdump em SAP HANA (Grandes Instâncias); Microsoft Docs
description: Script para permitir kdump em SAP HANA (Grandes Instâncias) HLI Tipo I, HLI Tipo II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204939"
---
# <a name="enable-kdump-service"></a>Ativar o serviço Kdump

Este documento descreve os detalhes sobre como ativar o serviço Kdump em Azure HANA Large Instance **(Tipo I e Tipo II)**

## <a name="supported-skus"></a>SKUs suportados

|  Tipo de caso grande hana   |  Fornecedor de OS   |  Versão do pacote sO   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>Pré-requisitos

- O serviço `/var/crash` Kdump usa diretório para escrever lixeiras, certifique-se de que a partição corresponde a este diretório tem espaço suficiente para acomodar despejos.

## <a name="setup-details"></a>Detalhes da configuração

- Script para ativar Kdump pode ser encontrado [aqui](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)

- Execute este script em HANA Large Instance usando o comando abaixo

    > [!NOTE]
    > privilégio sudo necessário para dirigir este comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Se as saídas de comando Kdump estiverem ativadas com sucesso, por favor reinicie o sistema para aplicar a alteração, então o Kdump está ativado com sucesso. Reiniciar o sistema para aplicar alterações.

- Se a saída de comando não for concluída, a saída!!!!, então o serviço Kdump não está ativado. Consulte a [questão](#support-issue)do suporte da secção .

## <a name="test-kdump"></a>Teste Kdump

> [!NOTE]
>  A operação abaixo irá desencadear uma falha no núcleo e reiniciar o sistema.

- Desencadear uma queda de miolo

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- Depois de o sistema reiniciar `/var/crash` com sucesso, verifique se o diretório está a ver se há troncos de colisão com o kernel.

- Se `/var/crash` o diretório tem a data atual, então o Kdump está ativado com sucesso.

## <a name="support-issue"></a>Questão de apoio

Se o script falhar com um erro ou o Kdump não estiver ativado, levante o pedido de serviço com a equipa de suporte da Microsoft com os seguintes detalhes.

* ID de subscrição hli

* Nome do servidor

* Fornecedor de OS

* Versão do SO

* Versão de kernel
