---
title: Aplicação do Azure Migrate
description: Fornece uma visão geral do dispositivo de migrações para Azure usado em avaliação e migração de servidor.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 652fe9d379d6e2ba50e9e282f384905e154368d8
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031668"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo descreve o dispositivo de migrações para Azure. Implementa o aparelho quando utiliza a ferramenta [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) para descobrir e avaliar aplicações, infraestruturas e cargas de trabalho para migração para o Microsoft Azure. O aparelho também é utilizado quando migra VMware VMs para Azure utilizando [o Azure Migrate: Avaliação do servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) com [migração sem agente](server-migrate-overview.md).

## <a name="appliance-overview"></a>Visão geral do dispositivo

O dispositivo migrações para Azure é usado nos cenários a seguir.

**Cenário** | **Ferramenta** | **Usado para** 
--- | --- | ---
VM VMware | Migrações para Azure: avaliação do servidor<br/><br/> Migrações para Azure: migração de servidor | Descobrir VMs VMware<br/><br/> Descobrir aplicativos e dependências de computador<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.<br/><br/> Replique VMs VMware com migração sem agente.
VM hiper-V | Migrações para Azure: avaliação do servidor | Descobrir VMs do Hyper-V<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.
Computador físico |  Migrações para Azure: avaliação do servidor |  Descobrir servidores físicos<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.

## <a name="appliance---vmware"></a>Dispositivo – VMware 

**Requisito** | **VMware** 
--- | ---
**Formato de descarregamento** | . OVA 
**Link de descarregamento** | https://aka.ms/migrate/appliance/vmware 
**Tamanho de download** | 11,2 GB
**Licença** | O modelo de dispositivo baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Implementação** | Você implanta o dispositivo como uma VM VMware. Você precisa de recursos suficientes no vCenter Server para alocar uma VM com 32 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo.<br/> O dispositivo requer acesso à Internet, seja diretamente ou por meio de um proxy.<br/> O dispositivo pode se conectar a um único vCenter Server.
**Hardware** | Recursos no vCenter para alocar uma VM com 32 GB de RAM 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo. 
**Valor de hash** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter servidor/anfitrião** | A VM do dispositivo deve ser implantada em um host ESXi executando a versão 5,5 ou posterior.<br/><br/> vCenter Server executando 5,5, 6,0, 6,5 ou 6,7.
**Projeto Azure Migrate** | Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Descoberta** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um único vCenter Server.
**Componentes de aparelhos** | Aplicativo de gerenciamento: aplicativo Web no dispositivo para entrada do usuário durante a implantação.<br/> Agente de descoberta: coleta dados de configuração do computador.<br/> Agente de avaliação: coletar dados de desempenho.<br/> DRA: Orquestra a replicação de VM e coordena a comunicação entre computadores/Azure.<br/> Gateway: envia dados replicados para o Azure.<br/> Serviço de atualização automática: atualizar componentes (é executado a cada 24 horas).
**VDDK (migração sem agente)** | Se você estiver executando uma migração sem agente com a migração de servidor de migrações para Azure, o VMware vSphere VDDK deverá ser instalado na VM do dispositivo.


## <a name="appliance---hyper-v"></a>Dispositivo – Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Formato de descarregamento** | Pasta compactada (com VHD)
**Link de descarregamento** | https://aka.ms/migrate/appliance/hyperv 
**Tamanho de download** | 10 GB
**Licença** | O modelo de dispositivo baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Implantação de aparelhos**   |  Você implanta o dispositivo como uma VM do Hyper-V.<br/> A VM do dispositivo fornecida pela migração do Azure é a VM Hyper-V versão 5,0.<br/> O host Hyper-V deve estar executando o Windows Server 2012 R2 ou posterior.<br/> O host precisa de espaço suficiente para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo para a VM do dispositivo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e acesso à Internet.
**Hardware** | Recursos no host Hyper-V para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo para a VM do dispositivo.
**Valor de hash** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Anfitrião Hyper-V** | Executando o Windows Server 2012 R2 ou posterior.
**Projeto Azure Migrate** | Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Descoberta** | Um dispositivo pode descobrir até 5000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a até 300 hosts Hyper-V.
**Componentes de aparelhos** | Aplicativo de gerenciamento: aplicativo Web no dispositivo para entrada do usuário durante a implantação.<br/> Agente de descoberta: coleta dados de configuração do computador.<br/> Agente de avaliação: coletar dados de desempenho.<br/>  Serviço de atualização automática: atualizar componentes (é executado a cada 24 horas).


## <a name="appliance---physical"></a>Dispositivo-físico

**Requisito** | **Físico** 
--- | ---
**Formato de descarregamento** | Pasta zipped (com script de instalação baseado powerShell)
**Link de descarregamento** | [Link de descarregamento](https://go.microsoft.com/fwlink/?linkid=2105112)
**Tamanho de download** | 59,7 MB
**Hardware** | Máquina física dedicada, ou utilize uma Máquina Virtual. A máquina que executa o dispositivo precisa de 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e acesso à Internet.
**Valor de hash** | MD5: 1e92ede3e87c03bd148e56a708cd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e666de7b9f475b6542beef114b20bfdac3c
**Sistema Operativo** | A máquina de aparelhos deve estar a funcionar no Windows Server 2016. 
**Implantação de aparelhos**   |  O script do instalador do dispositivo é baixado do portal (em uma pasta compactada). <br/> Descompacte a pasta e execute o script do PowerShell (AzureMigrateInstaller. ps1).
**Descoberta** | Um dispositivo pode descobrir até 250 servidores físicos.
**Componentes de aparelhos** | Aplicativo de gerenciamento: aplicativo Web no dispositivo para entrada do usuário durante a implantação.<br/> Agente de descoberta: coleta dados de configuração do computador.<br/> Agente de avaliação: coletar dados de desempenho.<br/>  Serviço de atualização automática: atualizar componentes (é executado a cada 24 horas).


## <a name="url-access"></a>Acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um proxy baseado em URL para se conectar à Internet, permita o acesso a essas URLs, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal Azure.
\*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com | Entre em sua assinatura do Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie Active Directory aplicativos para o dispositivo se comunicar com as migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
aka.ms/* | Permitir acesso a links conhecidos. Usado para atualizações do dispositivo de migração do Azure.
download.microsoft.com/download | Permitir downloads do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente VMware**<br/><br/> Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.




## <a name="collected-data---vmware"></a>Dados coletados-VMware

### <a name="collected-performance-data-vmware"></a>Coletados de dados de desempenho-VMware

Aqui estão os dados de desempenho da VM VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo da VM recomendado
Utilização de memória | mem.usage.average | Tamanho/custo da VM recomendado
Taxa de transferência de leitura de disco (MB por segundo) | virtualDisk.read.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de gravações de disco (MB por segundo) | virtualDisk.write.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Operações de leitura de disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Operações de gravações de disco por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Taxa de transferência de leitura de NIC (MB por segundo) | NET. Received. média | Cálculo para o tamanho da VM
Taxa de transferência de gravações da NIC (MB por segundo) | net.transmitted.average  |Cálculo para o tamanho da VM


### <a name="collected-metadata-vmware"></a>Metadados coletados-VMware

> [!NOTE]
> Os metadados descobertos pelo dispositivo de migrações para Azure são usados para ajudá-lo a dimensionar seus aplicativos conforme você os migra para o Azure, executar análise de adequação do Azure, análise de dependência de aplicativos e planejamento de custos. A Microsoft não usa esses dados em relação a nenhuma auditoria de conformidade de licença.

Aqui está a lista completa de metadados de VM do VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Contador**
--- | --- 
**Detalhes da máquina** | 
ID DA VM | vm.Config.InstanceUuid 
o nome da VM | vm.Config.Name
ID de vCenter Server | VMwareClient.Instance.Uuid
Descrição da VM | vm.Summary.Config.Annotation
Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operativo | vm.SummaryConfig.GuestFullName
Tipo de inicialização | vm.Config.Firmware
Número de núcleos | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Número de discos | VM. Config. hardware. Device. ToList (). FindAll (x = > é VirtualDisk). Count
Lista tamanho do disco | VM. Config. hardware. Device. ToList (). FindAll (x = > é VirtualDisk)
Lista de adaptadores de rede | VM. Config. hardware. Device. ToList (). FindAll (x = > é VirtualEthernet). Count
Utilização da CPU | cpu.usage.average
Utilização de memória |mem.usage.average
**Por detalhes do disco** | 
Valor de chave de disco | disco. Chaves
Número de Dikunit | disk.UnitNumber
Valor da chave do controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk. DeviceInfo. Summary
Nome do disco | Valor gerado usando disco. UnitNumber, disco. Chave, disco. ControllerKey. VAlue
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Taxa de transferência de leitura (MB por segundo) | virtualDisk.read.average
Taxa de transferência de gravação (MB por segundo) | virtualDisk.write.average
**Por detalhes nic** | 
Nome do adaptador de rede | NIC. Chaves
Endereço MAC | ((VirtualEthernetCard) NIC). MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Taxa de transferência de leitura (MB por segundo) | NET. Received. média
Taxa de transferência de gravação (MB por segundo) | net.transmitted.average
**Detalhes do caminho do inventário** | 
Nome | container.GetType().Name
Tipo de objeto filho | Container. ChildType
Detalhes da referência | container.MoRef
Detalhes do pai | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)container).VmFolder
Detalhes do datacenter por pasta de host | ((Datacenter)container).HostFolder
Detalhes do cluster por host | ((ClusterComputeResource)container).Host
Detalhes do host por VM | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>Dados coletados-Hyper-V

### <a name="collected-performance-data-hyper-v"></a>Coletados de dados de desempenho-Hyper-V

> [!NOTE]
> Os metadados descobertos pelo dispositivo de migrações para Azure são usados para ajudá-lo a dimensionar seus aplicativos conforme você os migra para o Azure, executar análise de adequação do Azure, análise de dependência de aplicativos e planejamento de custos. A Microsoft não usa esses dados em relação a nenhuma auditoria de conformidade de licença.

Aqui estão os dados de desempenho do Hyper VM que o dispositivo coleta e envia para o Azure.

**Classe de contra-desempenho** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Processador virtual do hipervisor do Hyper-V | % Tempo de execução de hóspedes | Tamanho/custo da VM recomendado
VM Memória Dinâmica Hyper-V | Pressão Atual (%)<br/> Memória física visível de convidado (MB) | Tamanho/custo da VM recomendado
Dispositivo de armazenamento virtual do Hyper-V | Bytes de leitura/segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Dispositivo de armazenamento virtual do Hyper-V | Bytes de gravação/segundo | Cálculo para tamanho do disco, custo de armazenamento, tamanho da VM
Adaptador de rede virtual do Hyper-V | Bytes recebidos/segundo | Cálculo para o tamanho da VM
Adaptador de rede virtual do Hyper-V | Bytes enviados/segundo | Cálculo para o tamanho da VM

- A utilização da CPU é a soma de todo o uso, para todos os processadores virtuais anexados a uma VM.
- A utilização de memória é (pressão atual * memória física visível de convidado)/100.
- Os valores de utilização de disco e rede são coletados dos contadores de desempenho do Hyper-V listados.

### <a name="collected-metadata-hyper-v"></a>Metadados coletados-Hyper-V

Aqui está a lista completa de metadados de VM do Hyper-V que o dispositivo coleta e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
**Detalhes da máquina** | 
Número de série de BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo de VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição da VM | Msvm_VirtualSystemSettingData | Nome do elemento
Versão da VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica habilitada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão/FQDN do sistema operacional | Msvm_KvpExchangeComponent | Dados do nome do GuestIntrinsicExchangeItems
Status de energia da VM | Msvm_ComputerSystem | Estado habilitado
**Por detalhes do disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Tipo
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Pai do disco rígido virtual | Msvm_VirtualHardDiskSettingData | ParentPath
**Por detalhes nic** | 
Endereços IP (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP habilitado (NICs sintéticas) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID da NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | Instância
Endereço MAC NIC (NICs sintéticas) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID da NIC (NICs herdadas) | Dados do MsvmEmulatedEthernetPortSetting | Instância
ID de NIC MAC (NICs herdadas) | Dados do MsvmEmulatedEthernetPortSetting | Endereço




## <a name="discovery-and-collection-process"></a>Processo de descoberta e coleta

O dispositivo se comunica com servidores vCenter e hosts/cluster do Hyper-V usando o processo a seguir.

1. **Começar a descoberta:**
    - Quando você inicia a descoberta no dispositivo Hyper-V, ele se comunica com os hosts Hyper-V nas portas WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando você inicia a descoberta no dispositivo VMware, ela se comunica com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter escuta em uma porta diferente, você pode configurá-lo no aplicativo Web do dispositivo.
2. **Recolher metadados e dados de desempenho:**
    - O dispositivo usa uma sessão de modelo CIM (CIM) para coletar dados de VM do Hyper-V do host Hyper-V nas portas 5985 e 5986.
    - O dispositivo se comunica com a porta 443 por padrão, para coletar dados de VM do VMware do vCenter Server.
3. **Enviar dados**: O aparelho envia os dados recolhidos para a Avaliação do Servidor Migratório De Migração Azure e migração do servidor migratório Azure através da porta SSL 443. O dispositivo pode se conectar ao Azure pela Internet ou você pode usar o ExpressRoute com emparelhamento público/Microsoft.
    - Para dados de desempenho, o dispositivo coleta dados de utilização em tempo real.
        - Os dados de desempenho são coletados a cada 20 segundos para VMware e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados coletados são acumulados para criar um único ponto de dados por 10 minutos.
        - O valor de pico de utilização é selecionado de todos os pontos de dados de 20/30 segundos e enviado ao Azure para cálculo da avaliação.
        - Com base no valor de percentil especificado nas propriedades de avaliação (50 º/90 º/95 º/99 º), os pontos de dez minutos são classificados em ordem crescente e o valor de percentil apropriado é usado para calcular a avaliação
    - Para a migração de servidor, o dispositivo inicia a coleta de dados da VM e o Replica no Azure.
4. **Avaliar e migrar:** Pode agora criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a Avaliação do Servidor Migratório Azure. Além disso, você também pode começar a migrar VMs VMware usando a migração de servidor de migração do Azure para orquestrar a replicação de VM sem agente.


![Arquitetura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado conforme os agentes de migração do Azure em execução no dispositivo são atualizados.

- Isso ocorre automaticamente porque a atualização automática está habilitada no dispositivo por padrão.
- Você pode alterar essa configuração padrão para atualizar os agentes manualmente.
- Para desativar a atualização automática, vá ao Editor de Registo>HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance e defina a chave de registo - "AutoUpdate" para 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Definir atualizações do agente para manual

Para atualizações manuais, certifique-se de que atualiza todos os agentes do aparelho ao mesmo tempo, utilizando o botão **Atualizar** para cada agente desatualizado do aparelho. Você pode alternar a configuração de atualização de volta para atualizações automáticas a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

[Aprenda](tutorial-assess-vmware.md#set-up-the-appliance-vm) a configurar o aparelho para VMware.
[Aprenda](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) a configurar o aparelho para hyper-V.

