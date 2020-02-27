---
title: Monitorização do SAP HANA em Azure (Grandes Instâncias)  Microsoft Docs
description: Monitor SAP HANA em um Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50a6b4f15a7de02533e3bb51e5659f7b4c078b40
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617296"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Como monitorizar o SAP HANA (grandes instâncias) no Azure

O SAP HANA on Azure (Grandes Instâncias) não é diferente de qualquer outra implantação do IaaS — é necessário monitorizar o que o SISTEMA e a aplicação estão a fazer e como as aplicações consomem os seguintes recursos:

- CPU
- Memória
- Largura de banda da rede
- Espaço em disco

Com as Máquinas Virtuais Azure, você precisa descobrir se as classes de recursos acima referidas são suficientes ou se ficam esgotadas. Aqui está mais detalhes sobre cada uma das diferentes classes:

**Consumo de recursos cpu:** O rácio que o SAP definiu para determinada carga de trabalho contra a HANA é aplicado para garantir que devem existir recursos cpu suficientes disponíveis para trabalhar através dos dados armazenados na memória. No entanto, pode haver casos em que a HANA consome muitas CPUs executando consultas devido a índices em falta ou questões semelhantes. Isto significa que deve monitorizar o consumo de recursos da CPU da unidade de grandes instâncias HANA, bem como os recursos cpu consumidos pelos serviços específicos da HANA.

**Consumo de memória:** É importante monitorizar de dentro da HANA, bem como fora de HANA na unidade. Dentro da HANA, monitorize como os dados estão a consumir a memória atribuída à HANA para se manter dentro das diretrizes de dimensionamento exigidas do SAP. Também pretende monitorizar o consumo de memória no nível de Instância Grande para se certificar de que o software adicional não HANA instalado não consome muita memória e, portanto, competir com hana para memória.

**Largura de banda da rede:** O gateway Azure VNet é limitado na largura de banda dos dados que se deslocam para o Azure VNet, pelo que é útil monitorizar os dados recebidos por todos os VMs Azure dentro de um VNet para descobrir a sua proximidade com os limites do portal Azure SKU selecionado. Na unidade HANA Large Instance, faz sentido monitorizar também o tráfego de rede de entrada e saída e acompanhar os volumes que são tratados ao longo do tempo.

**Espaço em disco:** O consumo de espaço em disco geralmente aumenta com o tempo. As causas mais comuns são: aumento do volume de dados, execução de cópias de segurança de registo de transações, armazenamento de ficheiros de vestígios e realização de instantâneos de armazenamento. Por isso, é importante monitorizar o uso do espaço do disco e gerir o espaço do disco associado à unidade HANA Large Instance.

Para as **SKUs tipo II** das Grandes Instâncias HANA, o servidor vem com as ferramentas de diagnóstico do sistema pré-carregada. Pode utilizar estas ferramentas de diagnóstico para efetuar a verificação de saúde do sistema. Executar o seguinte comando para gerar o ficheiro de registo de verificação de saúde em /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Quando trabalha com a equipa de Suporte do Microsoft para resolver um problema, também pode ser solicitado que forneça os ficheiros de registo utilizando estas ferramentas de diagnóstico. Pode fechar o ficheiro utilizando o seguinte comando.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Passos seguintes?**

- Consulte [como monitorizar o SAP HANA (grandes instâncias) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
