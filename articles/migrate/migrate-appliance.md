---
title: Arquitetura do dispositivo de migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do dispositivo de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 249cbea173afe1671118446e0714b721b8c7f72b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685088"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo descreve o dispositivo de migrações para Azure. Você implanta o dispositivo ao usar as ferramentas de avaliação e migração do Azure migrar para descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. 

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.



## <a name="appliance-overview"></a>Visão geral do dispositivo

Os tipos de dispositivo de migração do Azure e o uso são os seguintes.

**Implantado como** | **Usado para** | **Detalhes**
--- | --- |  ---
VM VMware | Avaliação de VM VMware com a ferramenta de avaliação de migrações para Azure.<br/><br/> Migração sem agente de VM do VMware com a ferramenta de migração de servidor de migrações para Azure | Baixe o modelo OVA e importe para vCenter Server para criar a VM do dispositivo.
VM do Hyper-V | Avaliação de VM do Hyper-V com a ferramenta de avaliação de migrações para Azure. | Baixe o VHD compactado e importe para o Hyper-V para criar a VM do dispositivo.

## <a name="appliance-access"></a>Acesso ao dispositivo

Depois de configurar o dispositivo, você pode acessar remotamente a VM do dispositivo por meio da porta TCP 3389. Você também pode acessar remotamente o aplicativo de gerenciamento da Web para o dispositivo, na porta 44368 com a URL: `https://<appliance-ip-or-name>:44368`.

## <a name="appliance-license"></a>Licença de dispositivo
O dispositivo vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.

## <a name="appliance-agents"></a>Agentes de dispositivo
O dispositivo tem esses agentes instalados.

**Agente** | **Detalhes**
--- | ---
Agente de descoberta | Coleta dados de configuração de máquinas virtuais locais
Agente de avaliação | Cria o perfil do ambiente local para coletar dados de desempenho da VM.
Adaptador de migração | Orquestra a replicação de VM e coordena a comunicação entre as VMs e o Azure.
Gateway de migração | Envia dados replicados da VM para o Azure.


## <a name="appliance-deployment-requirements"></a>Requisitos de implantação de dispositivo

- [Examine](migrate-support-matrix-vmware.md#assessment-appliance-requirements) os requisitos de implantação de um dispositivo VMware e as URLs que o dispositivo precisa acessar.
- [Examine](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) os requisitos de implantação de um dispositivo Hyper-V e as URLs que o dispositivo precisa acessar.


## <a name="collected-performance-data-vmware"></a>Coletados de dados de desempenho-VMware

Aqui estão os dados de desempenho da VM VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Neutraliza** | **Impacto da avaliação**
--- | --- | ---
Utilização da CPU | CPU. Usage. Average | Tamanho/custo da VM recomendado
Utilização de memória | mem. Usage. Average | Tamanho/custo da VM recomendado
Taxa de transferência de leitura de disco (MB por segundo) | virtualDisk. Read. Average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de gravação do disco (MB por segundo) | virtualDisk. Write. Average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Operações de leitura de disco por segundo | virtualDisk. numberReadAveraged. Average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Operações de gravação de disco por segundo | virtualDisk. numberWriteAveraged. Average  | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de leitura de NIC (MB por segundo) | NET. Received. média | Cálculo para o tamanho da VM
Taxa de transferência de gravação da NIC (MB por segundo) | NET. transmitido. Average  |Cálculo para o tamanho da VM


## <a name="collected-metadata-vmware"></a>Metadados coletados-VMware

> [!NOTE]
> Os metadados descobertos pelo dispositivo de migrações para Azure são usados para ajudá-lo a dimensionar seus aplicativos conforme você os migra para o Azure, executar análise de adequação do Azure, análise de dependência de aplicativos e planejamento de custos. A Microsoft não usa esses dados em relação a nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados de VM do VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Neutraliza**
--- | --- 
**Detalhes do computador** | 
ID DA VM | VM. Config. InstanceUuid 
o nome da VM | VM. Config.Name
ID de vCenter Server | VMwareClient.Instance.Uuid
Descrição da VM | VM. Resumo. config. Annotation
Nome do produto de licença | VM. Client. messagecontent. about. LicenseProductName
Tipo de sistema operacional | vm.SummaryConfig.GuestFullName
Tipo de inicialização | VM. Config. firmware
Número de núcleos | VM. Config. hardware. NumCPU
Memória (MB) | VM. Config. hardware. MemoryMB
Número de discos | VM. Config. hardware. Device. ToList (). FindAll (x = > é VirtualDisk). Count
Lista tamanho do disco | VM. Config. hardware. Device. ToList (). FindAll (x = > é VirtualDisk)
Lista de adaptadores de rede | VM. Config. hardware. Device. ToList (). FindAll (x = > é VirtualEthernet). Count
Utilização da CPU | CPU. Usage. Average
Utilização de memória |mem. Usage. Average
**Detalhes de por disco** | 
Valor de chave de disco | disco. Chaves
Número de Dikunit | disco. UnitNumber
Valor da chave do controlador de disco | disco. ControllerKey. Value
Gigabytes provisionados | virtualDisk. DeviceInfo. Summary
Nome do disco | Valor gerado usando disco. UnitNumber, disco. Chave, disco. ControllerKey. VAlue
Operações de leitura por segundo | virtualDisk. numberReadAveraged. Average
Operações de gravação por segundo | virtualDisk. numberWriteAveraged. Average
Taxa de transferência de leitura (MB por segundo) | virtualDisk. Read. Average
Taxa de transferência de gravação (MB por segundo) | virtualDisk. Write. Average
**Por detalhes da NIC** | 
Nome do adaptador de rede | NIC. Chaves
Endereço MAC | ((VirtualEthernetCard) NIC). MacAddress
Endereços IPv4 | VM. Guest.Net
Endereços IPv6 | VM. Guest.Net
Taxa de transferência de leitura (MB por segundo) | NET. Received. média
Taxa de transferência de gravação (MB por segundo) | NET. transmitido. Average
**Detalhes do caminho de inventário** | 
Nome | Container. GetType (). Nomes
Tipo de objeto filho | Container. ChildType
Detalhes da referência | Container. MoRef
Detalhes do pai | Contêiner. pai
Detalhes da pasta por VM | (Contêiner (pasta)). ChildEntity. Type
Detalhes do datacenter por VM | ((Datacenter) contêiner). VmFolder
Detalhes do datacenter por pasta de host | ((Datacenter) contêiner). HostFolder
Detalhes do cluster por host | ((ClusterComputeResource)container).Host
Detalhes do host por VM | ((HostSystem)container).VM



## <a name="collected-performance-data-hyper-v"></a>Coletados de dados de desempenho-Hyper-V

> [!NOTE]
> Os metadados descobertos pelo dispositivo de migrações para Azure são usados para ajudá-lo a dimensionar seus aplicativos conforme você os migra para o Azure, executar análise de adequação do Azure, análise de dependência de aplicativos e planejamento de custos. A Microsoft não usa esses dados em relação a nenhuma auditoria de conformidade de licença.

Aqui estão os dados de desempenho do Hyper VM que o dispositivo coleta e envia para o Azure.

**Classe de contador de desempenho** | **Neutraliza** | **Impacto da avaliação**
--- | --- | ---
Processador virtual do hipervisor do Hyper-V | % De tempo de execução de convidado | Tamanho/custo da VM recomendado
VM Memória Dinâmica Hyper-V | Pressão atual (%)<br/> Memória física visível de convidado (MB) | Tamanho/custo da VM recomendado
Dispositivo de armazenamento virtual do Hyper-V | Bytes de leitura/segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Dispositivo de armazenamento virtual do Hyper-V | Bytes de gravação/segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Adaptador de rede virtual do Hyper-V | Bytes recebidos/segundo | Cálculo para o tamanho da VM
Adaptador de rede virtual do Hyper-V | Bytes enviados/segundo | Cálculo para o tamanho da VM

- A utilização da CPU é a soma de todo o uso, para todos os processadores virtuais anexados a uma VM.
- A utilização de memória é (pressão atual * memória física visível de convidado)/100.
- Os valores de utilização de disco e rede são coletados dos contadores de desempenho do Hyper-V listados.

## <a name="collected-metadata-hyper-v"></a>Metadados coletados-Hyper-V

Aqui está a lista completa de metadados de VM do Hyper-V que o dispositivo coleta e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade de classe WMI**
--- | --- | ---
**Detalhes do computador** | 
Número de série de BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo de VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição da VM | Msvm_VirtualSystemSettingData | ElementName
Versão da VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica habilitada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão/FQDN do sistema operacional | Msvm_KvpExchangeComponent | Dados do nome do GuestIntrinsicExchangeItems
Status de energia da VM | Msvm_ComputerSystem | Habilitadostate
**Detalhes de por disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Tipo
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Pai do disco rígido virtual | Msvm_VirtualHardDiskSettingData | ParentPath
**Por detalhes da NIC** | 
Endereços IP (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP habilitado (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID da NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço MAC NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID da NIC (NICs herdadas) | Dados do MsvmEmulatedEthernetPortSetting | InstanceID
ID de NIC MAC (NICs herdadas) | Dados do MsvmEmulatedEthernetPortSetting | Endereço




## <a name="discovery-and-collection-process"></a>Processo de descoberta e coleta

O dispositivo se comunica com servidores vCenter e hosts/cluster do Hyper-V usando o processo a seguir.


1. **Iniciar descoberta**:
    - Quando você inicia a descoberta no dispositivo Hyper-V, ele se comunica com os hosts Hyper-V nas portas WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando você inicia a descoberta no dispositivo VMware, ela se comunica com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter escuta em uma porta diferente, você pode configurá-lo no aplicativo Web do dispositivo.
2. **Coletar metadados e dados de desempenho**:
    - O dispositivo usa uma sessão de modelo CIM (CIM) para coletar dados de VM do Hyper-V do host Hyper-V nas portas 5985 e 5986.
    - O dispositivo se comunica com a porta 443 por padrão, para coletar dados de VM do VMware do vCenter Server.
3. **Enviar dados**: o dispositivo envia os dados coletados para a avaliação de servidor de migrações para Azure e migração de servidor de migrações para Azure por meio da porta SSL 443.
    - Para dados de desempenho, o dispositivo coleta dados de utilização em tempo real.
        - Os dados de desempenho são coletados a cada 20 segundos para VMware e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados coletados são acumulados para criar um único ponto de dados por dez minutos.
        - O valor de pico de utilização é selecionado de todos os 20/30 segundo pontos de dados e enviado ao Azure para cálculo da avaliação.
        - Com base no valor de percentil especificado nas propriedades de avaliação (50 º/90 º/95 º/99 º), os pontos de dez minutos são classificados em ordem crescente e o valor de percentil apropriado é usado para calcular a avaliação
    - Para a migração de servidor, o dispositivo inicia a coleta de dados da VM e o Replica no Azure.
4. **Avaliar e migrar**: agora você pode criar avaliações dos metadados coletados pelo dispositivo usando a avaliação do servidor de migrações para Azure. Além disso, você também pode começar a migrar VMs VMware usando a migração de servidor de migração do Azure para orquestrar a replicação de VM sem agente.


![Arquitetura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado conforme os agentes de migração do Azure em execução no dispositivo são atualizados.

- Isso ocorre automaticamente porque a atualização automática está habilitada no dispositivo por padrão.
- Você pode alterar essa configuração padrão para atualizar os agentes manualmente.
- Para desabilitar a atualização automática, vá para o editor do registro > HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\AzureAppliance e defina a chave do registro-"AutoUpdate" como 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Definir atualizações do agente para manual

Para atualizações manuais, certifique-se de atualizar todos os agentes no dispositivo ao mesmo tempo, usando o botão **Atualizar** para cada agente desatualizado no dispositivo. Você pode alternar a configuração de atualização de volta para atualizações automáticas a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

[Saiba como](tutorial-assess-vmware.md#set-up-the-appliance-vm) configurar o dispositivo para VMware.
[Saiba como](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) configurar o dispositivo para o Hyper-V.

