---
title: Arquitetura de aplicação do Azure Migrate | Documentos da Microsoft
description: Fornece uma descrição geral da aplicação do Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: c2c9ca3082aa9c2067a63f8d6304e8a229dac14a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811456"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo descreve a aplicação do Azure Migrate. Implementar a aplicação ao utilizar ferramentas de migração e de avaliação de migração do Azure para detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. 

[O Azure Migrate](migrate-services-overview.md) fornece um concentrador central para controlar a deteção, avaliação e migração de aplicações no local e cargas de trabalho e pública/privada na cloud VMs, para o Azure. O hub fornece ferramentas do Azure Migrate para avaliação e migração, bem como ofertas de (ISV fornecedor) de terceiros independentes de software.



## <a name="appliance-overview"></a>Descrição geral da aplicação

O Azure Migrate tipos de aplicação e o uso são os seguintes.

**Implementada como** | **Utilizado para** | **Detalhes**
--- | --- |  ---
VM de VMware | Avaliação da VM de VMware com a ferramenta de avaliação de migração do Azure.<br/><br/> Migração sem agente de VM do VMware com a ferramenta de migração do servidor de migrar do Azure | Transferir modelo OVA e importe-se ao vCenter Server para criar a aplicação da VM.
VM de Hyper-V | Avaliação da VM de Hyper-V com a ferramenta de avaliação de migração do Azure. | Baixe o VHD zipado e importar para o Hyper-V para criar a aplicação da VM.

## <a name="appliance-access"></a>Acesso da aplicação

Depois de configurar a aplicação, pode aceder remotamente a aplicação da VM através da porta TCP 3389. Pode aceder a aplicação de gestão para a aplicação, na porta 44368 com o URL da web também remotamente: ``` https://<appliance-ip-or-name>:44368 ```.

## <a name="appliance-license"></a>Licença de aplicação
A aplicação vem com uma licença de avaliação do Windows Server 2016, o que é válida por 180 dias. Se o período de avaliação está próximo de expiração, recomendamos que transfira e implemente um novo dispositivo ou que ativa a licença de sistema operativo da aplicação da VM.

## <a name="appliance-agents"></a>Agentes de aplicação
A aplicação tem estes agentes instalados.

**Agente** | **Detalhes**
--- | ---
Agente de deteção | Recolhe dados de configuração de VMs no local.
Agente de avaliação | Os perfis do ambiente no local para recolher dados de desempenho da VM.
Adaptador de migração | Orquestra a replicação de VM e coordena a comunicação entre VMs e o Azure.
Gateway de migração | Envia os dados VM de replicados para o Azure.


## <a name="appliance-deployment-requirements"></a>Requisitos de implementação da aplicação

- [Revisão](migrate-support-matrix-vmware.md#assessment-appliance-requirements) os requisitos de implementação para uma aplicação de VMware e os URLs que a aplicação precisa de acesso.
- [Revisão](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) os requisitos de implementação para uma aplicação de Hyper-V e os URLs que a aplicação precisa de acesso.


## <a name="collected-performance-data-vmware"></a>Dados de desempenho coletados-VMware

Segue-se os dados de desempenho da VM de VMware que a aplicação recolhe e envia para o Azure.

**Dados** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Recomendado tamanho/custo VM
Utilização da memória | mem.usage.average | Recomendado tamanho/custo VM
Débito (MB por segundo) de leitura de disco | virtualDisk.read.average | Cálculo para o tamanho do disco, o custo de armazenamento, o tamanho da VM
Débito de escrita de disco (MB por segundo) | virtualDisk.write.average | Cálculo para o tamanho do disco, o custo de armazenamento, o tamanho da VM
Operações por segundo de leitura de disco | virtualDisk.numberReadAveraged.average | Cálculo para o tamanho do disco, o custo de armazenamento, o tamanho da VM
Operações por segundo de escrita de disco | virtualDisk.numberWriteAveraged.average  | Cálculo para o tamanho do disco, o custo de armazenamento, o tamanho da VM
Débito (MB por segundo) de leitura de NIC | net.received.average | Cálculo para o tamanho VM
Débito de NIC de escrita (MB por segundo) | net.transmitted.average  |Cálculo para o tamanho VM


## <a name="collected-metadata-vmware"></a>Metadados recolhido-VMware

Eis a lista completa de metadados de VM do VMware que a aplicação recolhe e envia para o Azure.

**Dados** | **Contador**
--- | --- 
**Detalhes da máquina** | 
ID DA VM | vm.Config.InstanceUuid 
o nome da VM | vm.Config.Name
ID do servidor do vCenter | VMwareClient.Instance.Uuid
Descrição da VM | vm.Summary.Config.Annotation
Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operativo | vm.SummaryConfig.GuestFullName
Tipo de arranque | vm.Config.Firmware
Número de núcleos | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Número de discos | VM. Config.Hardware.Device.ToList(). FindAll(x => is VirtualDisk).count
Lista de tamanho de disco | VM. Config.Hardware.Device.ToList(). FindAll (x = > é VirtualDisk)
Lista de adaptadores de rede | VM. Config.Hardware.Device.ToList(). FindAll(x => is VirtualEthernet).count
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.average
**Por detalhes do disco** | 
Valor de chave de disco | disco. Chave
Número de Dikunit | disk.UnitNumber
Valor de chave do controlador de disco | disk.ControllerKey.Value
Gigabytes aprovisionados | virtualDisk.DeviceInfo.Summary
Nome do disco | Valor gerado com o disco. UnitNumber, o disco. Disco da chave. ControllerKey.VAlue
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de escrita por segundo | virtualDisk.numberWriteAveraged.average
Débito de leitura (MB por segundo) | virtualDisk.read.average
Escrever o débito (MB por segundo) | virtualDisk.write.average
**Por detalhes NIC** | 
Nome do adaptador de rede | nic.Key
Endereço MAC | ((VirtualEthernetCard)nic).MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Débito de leitura (MB por segundo) | net.received.average
Escrever o débito (MB por segundo) | net.transmitted.average
**Detalhes do caminho de inventário** | 
Nome | container.GetType().Name
Tipo de objeto subordinado | container.ChildType
Detalhes de referência | container.MoRef
Detalhes de principal | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do Centro de dados por VM | ((Datacenter)container).VmFolder
Detalhes do Centro de dados por pasta de anfitrião | ((Datacenter)container).HostFolder
Detalhes do cluster por anfitrião | ((ClusterComputeResource)container).Host
Detalhes de anfitrião por VM | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Desempenho coletados dados de Hyper-V

Segue-se os dados de desempenho da VM de VMware que a aplicação recolhe e envia para o Azure.

**Classe de contador de desempenho** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Processador de Virtual de hipervisor Hyper-V | % Tempo de execução de convidado | Recomendado tamanho/custo VM
Memória dinâmica do Hyper-V VM | Pressão atual (%)<br/> Memória física visível convidado (MB) | Recomendado tamanho/custo VM
Dispositivo de armazenamento Virtual do Hyper-V | Ler Bytes por segundo | Cálculo para o tamanho do disco, o custo de armazenamento, o tamanho da VM
Dispositivo de armazenamento Virtual do Hyper-V | Escrever Bytes/segundo | Cálculo para o tamanho do disco, o custo de armazenamento, o tamanho da VM
Adaptador de rede Virtual do Hyper-V | Bytes recebidos/segundo | Cálculo para o tamanho VM
Adaptador de rede Virtual do Hyper-V | Bytes enviados por segundo | Cálculo para o tamanho VM

- Utilização da CPU é a soma de toda a utilização, para todos os processadores virtuais anexados a uma VM.
- Utilização da memória é (pressão atual * convidado de memória física visível) / 100.
- Os valores de utilização do disco e rede são recolhidos a partir os contadores de desempenho do Hyper-V listados.

## <a name="collected-metadata-hyper-v"></a>Recolhidos metadados Hyper-V

Eis a lista completa de metadados de VM de Hyper-V que a aplicação recolhe e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade de classe do WMI**
--- | --- | ---
**Detalhes da máquina** | 
Número de série do BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo de VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome a apresentar da VM | Msvm_VirtualSystemSettingData | ElementName
Versão VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida por VM | Msvm_MemorySettingData | Limite
Memória dinâmica ativada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão/FQDN do sistema operativo | Msvm_KvpExchangeComponent | Dados de nome de GuestIntrinsciExchangeItems
Estado de energia VM | Msvm_ComputerSystem | EnabledState
**Por detalhes do disco** | 
Identificador do disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Type
Tamanho de disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Disco rígido virtual principal | Msvm_VirtualHardDiskSettingData | ParentPath
**Por detalhes NIC** | 
Endereços IP (sintéticas NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP ativado (sintéticas NICs) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID da NIC (NIC sintético) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço de MAC da NIC (NIC sintético) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID da NIC (NIC herdado) | Dados de MsvmEmulatedEthernetPortSetting | InstanceID
ID de MAC da NIC (NIC herdado) | Dados de MsvmEmulatedEthernetPortSetting | Endereço




## <a name="discovery-and-collection-process"></a>Processo de deteção e coleção

A aplicação comunica com servidores vCenter e anfitriões/cluster de Hyper-V com o processo seguinte.


1. **Iniciar a deteção de**:
    - Quando iniciar a deteção num dispositivo de Hyper-V, ele se comunica com os anfitriões de Hyper-V nas portas de WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando iniciar a deteção na aplicação da VMware, ele se comunica com o servidor vCenter na porta TCP 443 por predefinição. Se o vCenter server escuta numa porta diferente, pode configurá-lo na aplicação web da aplicação.
2. **Recolher dados de metadados e o desempenho**:
    - A aplicação utiliza uma sessão do modelo CIM (Common Information Model) para recolher dados de VM de Hyper-V a partir do anfitrião de Hyper-V nas portas 5985 e 5986.
    - A aplicação comunica com a porta 443 por predefinição, para recolher dados de VM do VMware vCenter Server.
3. **Enviar dados**: A aplicação envia os dados recolhidos para avaliação do servidor de migrar do Azure e migração do servidor de migrar do Azure através da porta SSL 443.
    - Para os dados de desempenho, a aplicação recolhe os dados de utilização em tempo real.
        - Dados de desempenho são recolhidos a cada 20 segundos para VMware e cada 30 segundos para Hyper-V, para cada métrica de desempenho.
        - Os dados recolhidos são agregados para criar um ponto de dados individual para dez minutos.
        - O valor de utilização de pico está selecionado a partir de todos os 20/30 segundo pontos de dados e enviados para o Azure para o cálculo de avaliação.
        - Com base no valor de percentil especificado nas propriedades de avaliação (percentis 50 º / 90/95/99th), os pontos de dez minutos são ordenados por ordem ascendente e o valor de percentil apropriado é utilizado para calcular a avaliação
    - Para a migração de servidor, a aplicação inicia a recolha de dados da VM e replica-a para o Azure.
4. **Avaliar e migrar**: Agora, pode criar avaliações dos metadados recolhidos pela aplicação da avaliação do servidor de migrar do Azure a utilizar. Além disso, também pode começar a migrar VMs do VMware com a migração do servidor de migrar do Azure para orquestrar a replicação de VM sem agente.


![Arquitetura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Atualizações da aplicação

A aplicação é atualizada à medida que os agentes do Azure Migrate em execução na aplicação da são atualizados.

- Isto ocorre automaticamente uma vez que a atualização automática está ativada na aplicação por predefinição.
- Pode alterar esta predefinição atualizar manualmente os agentes.
- Para desativar as atualizações automáticas, defina a chave de registo da aplicação a atualização automática, na HKLM\SOFTWAREMicrosoft\Azure.

### <a name="set-agent-updates-to-manual"></a>Atualizações de agente do conjunto para manual

Para as atualizações manuais, certifique-se de que Atualize todos os agentes a aplicação ao mesmo tempo, com o **atualizar** botão para cada agente desatualizado sobre a aplicação. Pode voltar a mudar a definição de atualização para as atualizações automáticas em qualquer altura.

## <a name="next-steps"></a>Passos Seguintes

[Saiba como](tutorial-assess-vmware.md#set-up-the-appliance-vm) para configurar a aplicação para VMware.
[Saiba como](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) para configurar a aplicação para o Hyper-V.

