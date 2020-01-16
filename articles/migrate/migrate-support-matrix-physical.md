---
title: Suporte para avaliação de servidor físico com migrações para Azure
description: Saiba mais sobre o suporte para a avaliação de servidor físico com as migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 32080605217cde78bd648ca6192f73d1025dea4c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028773"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Matriz de suporte para avaliação do servidor físico 

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte e as limitações para avaliar e migrar servidores físicos locais.


## <a name="overview"></a>Visão geral

Para avaliar as máquinas locais para migração para o Azure com este artigo, você adiciona a ferramenta migrações do Azure: Server Assessment a um projeto de migrações para Azure. Você implanta o [dispositivo migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia dados de configuração e desempenho para o Azure. Após a descoberta de máquina, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo

## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 servidores físicos em um único [projeto](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Um projeto pode incluir VMs do VMware, VMs do Hyper-V e servidores físicos, até os limites de avaliação.
**Deteção** | O dispositivo de migrações para Azure pode descobrir até 250 servidores físicos.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 computadores em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.




## <a name="physical-server-requirements"></a>Requisitos do servidor físico

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de servidor físico**       | O servidor físico pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | **Windows:** Configure uma conta de usuário local em todos os servidores Windows que você deseja incluir na descoberta. A conta de usuário precisa ser adicionada a esses grupos-Área de Trabalho Remota usuários, usuários do monitor de desempenho e usuários de log de desempenho. <br/> **Linux:** Você precisa de uma conta raiz nos servidores Linux que deseja descobrir. |
| **Sistema operativo** | Há suporte para todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , exceto o seguinte:<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="azure-migrate-appliance-requirements"></a>Requisitos do dispositivo de migrações para Azure

As migrações para Azure usam o [dispositivo de migrações para Azure](migrate-appliance.md) para descoberta e avaliação. O dispositivo para servidores físicos pode ser executado em uma VM ou em um computador físico. Você o configura usando um script do PowerShell que você baixa do portal do Azure.

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---physical) para servidores físicos.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o dispositivo precisa acessar.

## <a name="port-access"></a>Acesso à porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **ligação**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Conexões de saída nas portas 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.
**Servidores físicos** | **Windows:** Conexões de entrada na porta 443, as portas WinRM 5985 (HTTP) e 5986 (HTTPS) para efetuar pull de metadados de configuração e desempenho de servidores Windows. <br/> **Linux:**  Conexões de entrada na porta 22 (UDP) para efetuar pull de metadados de configuração e desempenho de servidores Linux. |


## <a name="next-steps"></a>Passos seguintes

[Prepare-se para a avaliação do servidor físico](tutorial-prepare-physical.md).
