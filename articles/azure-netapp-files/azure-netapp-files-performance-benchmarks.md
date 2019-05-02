---
title: Parâmetros de comparação de desempenho para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve os resultados dos testes de benchmark de desempenho para ficheiros de NetApp do Azure ao nível do volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870886"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Parâmetros de comparação de desempenho para os ficheiros do Azure NetApp

Este artigo descreve os resultados dos testes de benchmark de desempenho para ficheiros de NetApp do Azure ao nível do volume. 

## <a name="sample-application-used-for-the-tests"></a>Aplicação de exemplo utilizada para os testes

Testes de desempenho foram executados com uma aplicação de exemplo com ficheiros de NetApp do Azure. O aplicativo tem as seguintes características: 

* Um aplicativo baseado em Linux, criado para a cloud
* Pode aumentam linearmente com adicionadas máquinas virtuais (VMs) para aumentar a capacidade de computação, conforme necessário
* Requer a acessibilidade rápida do data lake
* Tem padrões de e/s que às vezes são aleatórios e, às vezes, sequenciais 
    * Um padrão aleatório necessita de baixa latência para grandes quantidades de e/s. 
    * Um padrão sequencial requer grandes quantidades de largura de banda. 

## <a name="about-the-workload-generator"></a>Sobre o gerador de carga de trabalho

Os resultados provenientes de arquivos de resumos de Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) é um utilitário de linha de comandos que gera cargas de trabalho de e/s de disco para validar o desempenho de armazenamento. A configuração de cliente-servidor utilizada é escalável.  Ele inclui um único mestre/cliente misto e o cliente dedicado 14 VMs.

## <a name="about-the-tests"></a>Sobre os testes

Os testes foram concebidos para identificar os limites que possa ter a aplicação de exemplo e o tempo de resposta que curvas até aos limites.  

Foram executados os seguintes testes: 

* Leitura de 8 KiB aleatória de 100%
* 100% 8 escrita aleatória de KiB
* Leitura de 64 KiB sequencial de 100%
* 100% 64 KiB sequenciais escrita
* 50% 64 KiB sequenciais leitura, as 64 escrita sequencial de KiB de 50%
* 50% 8 KiB aleatório de leitura, de 50% 8 escrita aleatória de KiB

## <a name="bandwidth"></a>Largura de Banda

Os ficheiros NetApp do Azure oferece várias [níveis de serviço](azure-netapp-files-service-levels.md). Cada nível de serviço oferece uma quantidade diferente de largura de banda por TiB de capacidade aprovisionada (quota de volume). O limite de largura de banda para um volume é aprovisionado com base na combinação de nível de serviço e a quota de volume. Observe que o limite de largura de banda é apenas um fator usado para determinar a quantidade real de débito que ocorrerá.  

Atualmente, o 4,500 MiB é o débito mais elevado que obteve por uma carga de trabalho em relação a um único volume no teste.  Com o nível de serviço Premium, uma quota de volume de 70.31 TiB irá aprovisionar a largura de banda suficiente para realizar essa taxa de transferência pelo cálculo abaixo: 

![Fórmula de largura de banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Nível de serviço e quota](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Cargas de trabalho intensivas de débito

O teste de débito utilizou Vdbench e uma combinação de VMs do armazenamento 12xD32s V3. O volume de exemplo no teste obtido os números de débito seguintes:

![Teste de débito](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Cargas de trabalho de e/S intensivas

O teste de e/s utilizado Vdbench e uma combinação de VMs do armazenamento 12xD32s V3. O volume de exemplo no teste obtido os números de e/s seguintes:

![Teste de e/s](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latência

A distância entre o teste de VMs e o volume de ficheiros do Azure NetApp tem um impacto sobre o desempenho de e/s.  A tabela abaixo compara o IOPS versus curvas de resposta de latência para dois conjuntos diferentes de VMs.  Um conjunto de VMs é quase NetApp serviço ficheiros do Azure e o outro conjunto é ainda mais longe.  Tenha em atenção que a latência maior para o conjunto adicional de VMs tem um impacto na quantidade de IOPS obtida num determinado nível de paralelismo.  Seja como for, leituras em relação a um volume podem exceder 300.000 IOPS, conforme ilustrado abaixo: 

![Estudo de latência](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Resumo

Cargas de trabalho sensíveis à latência (bases de dados) podem ter um tempo de resposta um milissegundo. O desempenho transacional pode ser as mais de 300k IOPS para um único volume.

Aplicações sensíveis a taxa de transferência (para transmissão em fluxo e geração de imagens) podem ter 4.5GiB / débito s.
