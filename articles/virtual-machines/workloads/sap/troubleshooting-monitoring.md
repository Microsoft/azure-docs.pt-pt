---
title: Monitoramento de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Monitorar SAP HANA em um Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0aea4dddef65600fe30f36499d4ad2a4f461245
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70077940"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>Como monitorar SAP HANA (instâncias grandes) no Azure

SAP HANA no Azure (instâncias grandes) não é diferente de qualquer outra implantação de IaaS, você precisa monitorar o que o sistema operacional e o aplicativo estão fazendo e como os aplicativos consomem os seguintes recursos:

- CPU
- Memória
- Largura de banda da rede
- Espaço em disco

Com as máquinas virtuais do Azure, você precisa descobrir se as classes de recurso nomeadas acima são suficientes ou ficam esgotadas. Aqui está mais detalhes sobre cada uma das diferentes classes:

**Consumo de recursos de CPU:** A proporção que o SAP definiu para determinadas cargas de trabalho em relação ao HANA é imposta para garantir que deve haver recursos suficientes de CPU disponíveis para trabalhar com os dados armazenados na memória. No entanto, pode haver casos em que o HANA consome muitas CPUs executando consultas devido a índices ausentes ou problemas semelhantes. Isso significa que você deve monitorar o consumo de recursos de CPU da unidade de instância grande do HANA, bem como os recursos de CPU consumidos pelos serviços específicos do HANA.

**Consumo de memória:** É importante monitorar de dentro do HANA, bem como fora do HANA na unidade. No HANA, monitore como os dados estão consumindo a memória alocada HANA para permanecer dentro das diretrizes de dimensionamento necessárias do SAP. Você também deseja monitorar o consumo de memória no nível de instância grande para garantir que o software não HANA adicional instalado não consuma muita memória e, portanto, concorra com o HANA para memória.

**Largura de banda da rede:** O gateway de VNet do Azure é limitado na largura de banda dos dados que se movem para a VNet do Azure, portanto, é útil monitorar os dados recebidos por todas as VMs do Azure em uma VNet para descobrir o quão perto você é para os limites do SKU do gateway do Azure selecionado. Na unidade de instância grande do HANA, faz sentido monitorar o tráfego de rede de entrada e saída também e controlar os volumes que são manipulados ao longo do tempo.

**Espaço em disco:** O consumo de espaço em disco geralmente aumenta com o passar do tempo. As causas mais comuns são: o volume de dados aumenta, a execução de backups de log de transações, o armazenamento de arquivos de rastreamento e a execução de instantâneos de armazenamento. Portanto, é importante monitorar o uso do espaço em disco e gerenciar o espaço em disco associado à unidade de instância grande do HANA.

Para os **SKUs do tipo II** das instâncias grandes do Hana, o servidor vem com as ferramentas de diagnóstico de sistema pré-carregadas. Você pode utilizar essas ferramentas de diagnóstico para executar a verificação de integridade do sistema. Execute o comando a seguir para gerar o arquivo de log de verificação de integridade em/var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Ao trabalhar com a equipe de Suporte da Microsoft para solucionar um problema, você também pode ser solicitado a fornecer os arquivos de log usando essas ferramentas de diagnóstico. Você pode compactar o arquivo usando o comando a seguir.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Passos seguintes?**

- Consulte [como monitorar SAP Hana (instâncias grandes) no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot).
