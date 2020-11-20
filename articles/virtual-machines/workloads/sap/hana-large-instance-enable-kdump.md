---
title: Script para ativar Kdump em SAP HANA (Grandes Instâncias)/ Microsoft Docs
description: Script para permitir Kdump em SAP HANA (Grandes Instâncias) HLI Tipo I, HLI Tipo II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6afcfe7684102b451860d9916d849842489cace
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967708"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump para SAP HANA em Azure Large Instances (HLI)

Configurar e ativar o kdump é um passo que é necessário para resolver falhas no sistema que não têm uma causa clara.
Há momentos em que um sistema irá inesperadamente falhar que não pode ser explicado por um problema de hardware ou infraestrutura.
Nestes casos pode ser um sistema operativo ou problema de aplicação e a KDump permitirá à SUSE determinar por que um sistema se despenhou.

## <a name="enable-kdump-service"></a>Ativar o serviço Kdump

Este documento descreve os detalhes sobre como ativar o serviço Kdump em Azure HANA Large Instance **(Tipo I e Tipo II).**

## <a name="supported-skus"></a>SKUs apoiados

|  Tipo Hana Large Instance   |  Fornecedor de OS   |  Versão pacote de SO   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S224m      |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP4         |  S224m      |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S72        |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S96        |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S192       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S192       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP4         |  S192       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S192m      |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S192m      |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP4         |  S192m      |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S144       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S144       |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP2         |  S144m      |
|   Tipo I                    |  Rio Suse        |   SLES 12 SP3         |  S144m      |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP2         |  S384       |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP4         |  S384       |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP2         |  S384xm     |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP4         |  S384xm     |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP2         |  S576m      |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  Rio Suse        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Pré-requisitos

- O serviço Kdump usa `/var/crash` diretório para escrever despejos, certifique-se de que a partição corresponde a este diretório tem espaço suficiente para acomodar despejos.

## <a name="setup-details"></a>Detalhes da configuração

- O script para ativar kdump pode ser encontrado [aqui](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)
> [!NOTE]
> este script é feito com base na nossa configuração de laboratório e espera-se que o Cliente contacte o fornecedor de OS para qualquer afinação posterior.
> A LUN separada será a provisionada para os novos servidores existentes para guardar as lixeiras e o script cuidará de configurar o sistema de ficheiros fora do LUN.
> A Microsoft não será responsável pela análise da lixeira. O cliente tem de abrir um bilhete com o fornecedor de SO para o analisar.

- Execute este script em HANA Large Instance usando o comando abaixo

    > [!NOTE]
    > privilégio sudo necessário para executar este comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Se as saídas de comando Kdump estiverem ativadas com sucesso, certifique-se de reiniciar o sistema para aplicar as alterações com sucesso.

- Se a saída do comando não for conseguida para fazer determinada operação, sair!!!!, então o serviço Kdump não está ativado. Consulte a [questão de suporte da](#support-issue)secção .

## <a name="test-kdump"></a>Teste Kdump

> [!NOTE]
>  Abaixo da operação irá desencadear uma falha no núcleo e reiniciar o sistema.

- Desencadear um acidente de núcleo

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Depois de o sistema reiniciar com sucesso, verifique se o `/var/crash` diretório se verifica se há registos de colisão com o kernel.

- Se o `/var/crash` diretório tiver diretório com a data atual, então o Kdump está habilitado com sucesso.

## <a name="support-issue"></a>Questão de apoio

Se o script falhar com um erro ou o Kdump não estiver ativado, aumente o pedido de serviço com a equipa de suporte da Microsoft com os seguintes detalhes.

* ID de assinatura HLI

* Nome do servidor

* Fornecedor de OS

* Versão do SO

* Versão de kernel

## <a name="related-documents"></a>Documentos Relacionados
- Saber mais sobre [a configuração do kdump](https://www.suse.com/support/kb/doc/?id=3374462)
