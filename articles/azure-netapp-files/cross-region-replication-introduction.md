---
title: Replicação transversal de volumes de ficheiros Azure NetApp | Microsoft Docs
description: Descreve o que a replicação transversal do Azure NetApp Files faz, pares de regiões suportados, objetivos de nível de serviço, durabilidade de dados e modelo de custos.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: ac0f9e6e5e1a1988386cc85c2d7576719acbd6e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590964"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replicação transversal dos volumes dos Ficheiros Azure NetApp

A funcionalidade de replicação dos Ficheiros Azure NetApp fornece proteção de dados através da replicação de volumes entre regiões. Pode replicar assincroticamente dados de um volume de Ficheiros Azure NetApp (fonte) numa região para outro volume de Ficheiros Azure NetApp (destino) noutra região.  Esta capacidade permite-lhe falhar a sua aplicação crítica em caso de interrupção ou desastre em toda a região.

> [!IMPORTANT]
> A funcionalidade de replicação entre regiões está atualmente em visualização pública. Tem de submeter um pedido de lista de espera para aceder à funcionalidade através da [página de submissão da lista de espera de replicação da Azure NetApp Files](https://aka.ms/anfcrrpreviewsignup). Aguarde um e-mail oficial de confirmação da equipa do Azure NetApp Files antes de utilizar a funcionalidade de replicação entre regiões.

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>Pares de replicação inter-região apoiados

A replicação do volume Azure NetApp Files é suportada entre [vários pares regionais Azure](../best-practices-availability-paired-regions.md#azure-regional-pairs) e não-pares. A replicação do volume Azure NetApp Files está atualmente disponível entre as seguintes regiões:  

### <a name="azure-regional-pairs"></a>Pares regionais azure

* Leste dos EUA e Oeste dos EUA
* Leste dos EUA 2 e Central EUA
* Austrália Leste e Austrália Sudeste
* Canadá Central e Canadá Leste
* Índia do Sul e Índia Central 
* Alemanha Centro Ocidental e Alemanha Norte
* Japão Leste e Japão Oeste
* Europa do Norte e Europa Ocidental
* Reino Unido Sul e Reino Unido Oeste

### <a name="azure-regional-non-pairs"></a>Não pares regionais do Azure

*   Eua Ocidental 2 e Leste dos EUA
*   Centro-Sul dos EUA e Central EUA
*   Centro-Sul dos EUA e Leste dos EUA
*   Centro-Sul dos EUA e Leste dos EUA 2
*   Leste dos EUA e Leste dos EUA 2
*   Leste dos EUA 2 e Oeste dos EUA 2
*   Austrália Leste e Sudeste Asiático 
*   Alemanha West Central e Reino Unido Sul

## <a name="service-level-objectives"></a>Objetivos ao nível do serviço

Os Objetivos do Ponto de Recuperação (RPO), ou a perda máxima de dados toleráveis, é definido como o dobro do calendário de replicação.  O RPO atual observado pode variar em função de fatores como o tamanho total do conjunto de dados, juntamente com a taxa de alteração, a percentagem de sobreposições de dados e a largura de banda de replicação disponível para transferência.   

* Para o horário de replicação de 10 minutos, o RPO máximo é de 20 minutos.  
* Para o horário de replicação horária, o RPO máximo é de duas horas.  
* Para o horário diário de replicação, o RPO máximo é de dois dias.  

O Objetivo do Tempo de Recuperação (RTO), ou o tempo máximo de inatividade da aplicação comercial tolerável, é determinado por fatores na elevação da aplicação e no acesso aos dados no segundo local. Espera-se que a parte de armazenamento do RTO para quebrar a relação de observação para ativar o volume de destino e fornecer acesso de dados de leitura e escrita no segundo site esteja concluída dentro de um minuto.

## <a name="cost-model-for-cross-region-replication"></a>Modelo de custos para a replicação entre regiões  

Com a replicação transversal do Azure NetApp Files, paga apenas pela quantidade de dados que replica. Não existem taxas de configuração ou taxa mínima de utilização. O preço de replicação baseia-se na frequência de replicação e na região do volume de *destino* que escolher durante a configuração inicial da replicação. Consulte a página [de preços dos ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/netapp/) para obter mais informações.  

A taxa regular de capacidade de armazenamento de Ficheiros Azure NetApp aplica-se ao volume de destino de replicação (também chamado de volume *de proteção de dados).* 

### <a name="pricing-examples"></a>Exemplos de preços

O valor de replicação entre regiões faturado num mês baseia-se na quantidade de dados replicados através da funcionalidade de replicação entre regiões durante esse mês. A quantidade de dados replicados é medida em GiB. Representa a soma dos dados replicados em duas regiões durante todas as replicações regulares dos volumes de origem até aos volumes de destino e durante todas as replicações de resync dos volumes de destino aos volumes de origem.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Exemplo 1: Mês 1 de replicação de base e replicações incrementais

Assumir as seguintes situações:

* O seu volume *de origem* é do nível de serviço Azure NetApp Files *Premium.* Tem um volume de quota de 1000 GiB e um volume consumido tamanho de 500 GiB no início do primeiro dia de um mês. O volume está na região *centro-sul dos EUA.*
* O seu volume de *destino* é do nível de serviço Azure NetApp Files *Standard.* É na região *leste dos EUA 2.*
* Configuraste uma replicação de uma região de hora em *hora* entre os dois volumes acima. Portanto, o preço da replicação é $0,12 por GiB.
* Para simplificar, assuma que o seu volume de origem tem uma constante alteração de dados de 0,5 GiB a cada hora, mas o tamanho total consumido não cresce (permanece em 500 GiB). 

Após a configuração inicial, a replicação da linha de base ocorre imediatamente.  

* Quantidade de dados replicada durante a replicação da linha de base: `500 GiB`
* Taxas de replicação de base: `500 GiB * $0.12 = $60`

Após a replicação da linha de base, apenas os blocos alterados são replicados. Portanto, apenas 0,5 GiB de dados serão replicados a cada hora nas replicações incrementais subsequentes.

* Soma de dados replicados através de replicações incrementais durante um mês de 30 dias: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Taxas de replicação incrementais: `360 GiB * $0.12 = $43.2`

Até ao final do mês 1, a taxa total de replicação entre regiões é a seguinte:  

*  Taxa total de replicação entre regiões a partir do 1.92.009: `$60 + $43.2 = $103.2`

A taxa regular de capacidade de armazenamento dos Ficheiros Azure NetApp aplica-se ao volume de destino. No entanto, o volume de destino pode utilizar um nível de armazenamento diferente (e mais barato que) o nível de volume de origem.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Exemplo 2: 27 0018 replicações incrementais e replicações resync  

Assuma que tem um volume de origem, um volume de destino, e uma relação de replicação entre os dois configurado como descrito no Exemplo 1. Durante 29 dias do segundo mês (um mês de 30 dias), as replicações horárias ocorreram como esperado.

* Soma de dados replicados através de replicações incrementais durante 29 dias: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Assuma que no último dia do mês, ocorreu uma paralisação não planeada na região de origem e que falhou no volume de destino. Após 2 horas, a região de origem recuperou e realizou uma réplica ressínc do volume de destino para o volume de origem. Durante as 2 horas, 0,8 GiB de alteração de dados ocorreu no volume de destino e precisava de ser ressívado à fonte.

* Soma de dados replicados em replicações regulares durante 22 horas no último dia: `0.5 GiB * 22 hours = 11 GiB`
* Quantidade de dados replicada durante uma replicação de resync: `0.8 GiB`

Por conseguinte, até ao final do Mês 2, a taxa total de replicação entre regiões é a seguinte:  

* Taxa total de replicação entre regiões a partir do 2.009: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

A taxa regular de capacidade de armazenamento dos Ficheiros Azure NetApp para o Mês 2 aplica-se ao volume de destino.

## <a name="next-steps"></a>Passos seguintes
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Criar replicação de volume](cross-region-replication-create-peering.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Gerir a recuperação após desastre](cross-region-replication-manage-disaster-recovery.md)
* [Redimensionar um volume de destino de replicação transversal](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Eliminar volumes ou replicações de volume](cross-region-replication-delete.md)
* [Resolver problemas da replicação entre regiões](troubleshoot-cross-region-replication.md)