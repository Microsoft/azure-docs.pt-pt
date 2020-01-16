---
title: Suporte para avaliação do Hyper-V em migrações para Azure
description: Saiba mais sobre o suporte para a avaliação do Hyper-V com migrações para Azure.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 20bdbb16d2f0610f6519424141b09190eae3cc42
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028780"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Matriz de suporte para avaliação do Hyper-V

Este artigo resume as configurações de suporte e as limitações para avaliar as VMs do Hyper-V com as [migrações para Azure: avaliação do servidor](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Se você estiver procurando informações sobre como migrar VMs do Hyper-V para o Azure, examine a [matriz de suporte de migração](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Visão geral

Para avaliar as máquinas locais para migração para o Azure com este artigo, você adiciona a ferramenta migrações do Azure: Server Assessment a um projeto de migrações para Azure. Você implanta o [dispositivo migrações para Azure](migrate-appliance.md). O dispositivo descobre continuamente computadores locais e envia dados de configuração e desempenho para o Azure. Após a descoberta de máquina, você coleta computadores descobertos em grupos e executa uma avaliação de um grupo.


## <a name="limitations"></a>Limitações

**Suporte** | **Detalhes**
--- | ---
**Limites de avaliação**| Descubra e avalie até 35.000 VMs do Hyper-V em um único [projeto](migrate-support-matrix.md#azure-migrate-projects).
**Limites do projeto** | Você pode criar vários projetos em uma assinatura do Azure. Um projeto pode incluir VMs do VMware, VMs do Hyper-V e servidores físicos, até os limites de avaliação.
**Deteção** | O dispositivo de migrações para Azure pode descobrir até 5000 VMs do Hyper-V.<br/><br/> O dispositivo pode se conectar a até 300 hosts Hyper-V.
**Avaliação** | Você pode adicionar até 35.000 computadores em um único grupo.<br/><br/> Você pode avaliar até 35.000 VMs em uma única avaliação.

[Saiba mais](concepts-assessment-calculation.md) sobre as avaliações.



## <a name="hyper-v-host-requirements"></a>Requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de host**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. <br/> Como alternativa, se você não quiser atribuir permissões de administrador, crie uma conta de usuário local ou de domínio e adicione o usuário a esses grupos – usuários de gerenciamento remoto, administradores do Hyper-V e usuários de monitor de desempenho. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/> Não pode avaliar as VMs localizadas em sistemas anfitriões Hyper-V com o Windows Server 2012. |
| **Comunicação remota do PowerShell**   | Deve ser habilitado em cada host. |
| **Réplica do Hyper-V**       | Se você usar a réplica do Hyper-V (ou se tiver várias VMs com os mesmos identificadores de VM) e descobrir as VMs originais e replicadas usando as migrações para Azure, a avaliação gerada pelas migrações para Azure pode não ser precisa. |


## <a name="hyper-v-vm-requirements"></a>Requisitos de VM do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Integration Services**       | Os [Integration Services do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |


## <a name="azure-migrate-appliance-requirements"></a>Requisitos do dispositivo de migrações para Azure

As migrações para Azure usam o [dispositivo de migrações para Azure](migrate-appliance.md) para descoberta e avaliação. O dispositivo para o Hyper-V é executado em uma VM do Hyper-V e é implantado usando um VHD do Hyper-V compactado que você baixa do portal do Azure. 

- Saiba mais sobre [os requisitos de dispositivo](migrate-appliance.md#appliance---hyper-v) para o Hyper-V.
- Saiba mais sobre [URLs](migrate-appliance.md#url-access) que o dispositivo precisa acessar.

## <a name="port-access"></a>Acesso à porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **ligação**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Conexões de saída nas portas 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.
**Host/cluster do Hyper-V** | Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para efetuar pull da configuração e dos metadados de desempenho das VMs do Hyper-V usando uma sessão modelo CIM (CIM).



## <a name="next-steps"></a>Passos seguintes

[Preparar a avaliação de VM do Hyper-V](tutorial-prepare-hyper-v.md)
