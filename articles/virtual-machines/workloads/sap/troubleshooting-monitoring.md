---
title: Monitorização do SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Monitore o SAP HANA num Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1a8d6fc6acb33af0c36978aa79dc90aeac3cf596
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966756"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Como monitorizar o SAP HANA (grandes instâncias) em Azure

O SAP HANA on Azure (Grandes Instâncias) não é diferente de qualquer outra implantação do IaaS — é necessário monitorizar o que o SO e a aplicação estão a fazer e como as aplicações consomem os seguintes recursos:

- CPU
- Memória
- Largura de banda de rede
- Espaço em disco

Com as Máquinas Virtuais Azure, é necessário descobrir se as classes de recursos acima referidas são suficientes ou se ficam esgotadas. Aqui está mais detalhes sobre cada uma das diferentes classes:

**Consumo de recursos da CPU:** O rácio que o SAP definiu para determinada carga de trabalho contra a HANA é aplicado para garantir que deve haver recursos suficientes de CPU disponíveis para trabalhar através dos dados que são armazenados na memória. No entanto, pode haver casos em que a HANA consome muitas consultas de execução de CPUs devido à falta de índices ou problemas semelhantes. Isto significa que deve monitorizar o consumo de recursos da CPU da unidade de grande instância HANA, bem como os recursos da CPU consumidos pelos serviços específicos da HANA.

**Consumo de memória:** É importante monitorizar de dentro da HANA, bem como fora de HANA na unidade. Dentro da HANA, monitorize como os dados estão a consumir a memória alocada hana para se manter dentro das diretrizes de dimensionamento necessárias da SAP. Também pretende monitorizar o consumo de memória ao nível de Large Instance para se certificar de que o software adicional não-HANA instalado não consome muita memória e, portanto, competir com a HANA para memória.

**Largura de banda da rede:** O gateway Azure VNet é limitado na largura de banda dos dados que se deslocam para o Azure VNet, pelo que é útil monitorizar os dados recebidos por todos os VMs Azure dentro de um VNet para descobrir quão perto está dos limites do Gateway SKU Azure que selecionou. Na unidade HANA Large Instance, faz sentido monitorizar também o tráfego de rede de entrada e saída e acompanhar os volumes que são manuseados ao longo do tempo.

**Espaço em disco:** O consumo de espaço em disco geralmente aumenta com o tempo. As causas mais comuns são: aumento do volume de dados, execução de cópias de segurança de registo de transações, armazenamento de ficheiros de vestígios e realização de instantâneos de armazenamento. Por isso, é importante monitorizar a utilização do espaço do disco e gerir o espaço do disco associado à unidade HANA Large Instance.

Para os **SKUs tipo II** das Grandes Instâncias HANA, o servidor vem com as ferramentas de diagnóstico do sistema pré-carregadas. Pode utilizar estas ferramentas de diagnóstico para efetuar a verificação de saúde do sistema. Executar o seguinte comando para gerar o ficheiro de registo de verificação de saúde em /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Quando trabalha com a equipa de Suporte do Microsoft para resolver um problema, também pode ser solicitado que forneça os ficheiros de registo utilizando estas ferramentas de diagnóstico. Pode fechar o ficheiro utilizando o seguinte comando.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Próximos passos**

- Consulte [como monitorizar o SAP HANA (grandes instâncias) em Azure](./hana-monitor-troubleshoot.md).
