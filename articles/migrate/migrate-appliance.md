---
title: Aplicação do Azure Migrate
description: Fornece uma visão geral do dispositivo de migrações para Azure usado em avaliação e migração de servidor.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c3ac39759cc096bb27535877084e14f4ed50cea9
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719584"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo descreve o dispositivo de migrações para Azure. Você implanta o dispositivo ao usar as ferramentas de avaliação e migração do Azure migrar para descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. 

As [migrações para Azure](migrate-services-overview.md) fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem privada/pública, para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros.



## <a name="appliance-overview"></a>Visão geral do dispositivo

Os tipos de dispositivo de migração do Azure e o uso são os seguintes.

**Cenário** | **Ferramenta** | **Utilizado para** 
--- | --- 
VM VMware | Migrações para Azure: avaliação do servidor; Migrações para Azure: migração de servidor | Descobrir VMs VMware<br/><br/> Descobrir aplicativos e dependências<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.<br/><br/> Replique VMs VMware com migração sem agente.
VM do Hyper-V | Migrações para Azure: avaliação do servidor | Descobrir VMs do Hyper-V<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.
Computador físico |  Migrações para Azure: ferramenta de avaliação |  Descobrir servidores físicos<br/><br/> Coletar metadados de computador e metadados de desempenho para avaliações.

## <a name="appliance---vmware"></a>Dispositivo – VMware 

**Requisito** | **VMware** 
--- | ---
**Formato de download** | . OVA 
**Ligação para transferência** | https://aka.ms/migrate/appliance/vmware 
**Tamanho do download** | 11,2 GB
**Licença** | O modelo de dispositivo baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Hardware** | Recursos no vCenter para alocar uma VM com 32 GB de RAM 8 vCPUs, cerca de 80 GB de armazenamento em disco e um comutador virtual externo. 
**Valor de hash** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256:4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**servidor/host do vCenter** | A VM do dispositivo deve ser implantada em um host ESXi executando a versão 5,5 ou posterior.<br/><br/> vCenter Server executando 5,5, 6,0, 6,5 ou 6,7.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Deteção** | Um dispositivo pode descobrir até 10.000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a um único vCenter Server.
**Componentes do dispositivo** | Aplicativo de gerenciamento: aplicativo Web no dispositivo para entrada do usuário durante a implantação.<br/> Agente de descoberta: coleta dados de configuração do computador.<br/> Agente de avaliação: coletar dados de desempenho.<br/> DRA: Orquestra a replicação de VM e coordena a comunicação entre computadores/Azure.<br/> Gateway: envia dados replicados para o Azure.<br/> Serviço de atualização automática: atualizar componentes (é executado a cada 24 horas).


## <a name="appliance---hyper-v"></a>Dispositivo – Hyper-V

**Requisito** | **Hyper-V** 
--- | ---
**Formato de download** | Pasta compactada (com VHD)
**Ligação para transferência** | https://aka.ms/migrate/appliance/hyperv 
**Tamanho do download** | 10 GB
**Licença** | O modelo de dispositivo baixado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
**Hardware** | Recursos no host Hyper-V para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo para a VM do dispositivo.
**Valor de hash** | MD5:29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256:37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Anfitrião Hyper-V** | Executando o Windows Server 2012 R2 ou posterior.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Deteção** | Um dispositivo pode descobrir até 5000 VMs VMware em um vCenter Server.<br/> Um dispositivo pode se conectar a até 300 hosts Hyper-V.
**Componentes do dispositivo** | Aplicativo de gerenciamento: aplicativo Web no dispositivo para entrada do usuário durante a implantação.<br/> Agente de descoberta: coleta dados de configuração do computador.<br/> Agente de avaliação: coletar dados de desempenho.<br/>  Serviço de atualização automática: atualizar componentes (é executado a cada 24 horas)


## <a name="appliance---physical"></a>Dispositivo-físico

**Requisito** | **Físico** 
--- | ---
**Formato de download** | Pasta compactada (com o script do instalador do PowerShell)
**Ligação para transferência** | [Ligação para transferência](https://go.microsoft.com/fwlink/?linkid=2105112)
**Tamanho do download** | 59,7 MB
**Hardware** | A máquina que executa o dispositivo precisa de 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento.
**Valor de hash** | MD5:96fd99581072c400aa605ab036a0a7c0<br/><br/> SHA256: f5454beef510c0aa38ac1c6be6346207c351d5361afa0c9cea4772d566fcdc36
**Software** | A máquina do dispositivo deve executar o Windows Server 2016. O servidor deve ser um servidor físico dedicado ou uma VM.
**Projeto de migrações para Azure** | Um dispositivo pode ser associado a um único projeto. <br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> 
**Deteção** | Um dispositivo pode descobrir até 250 servidores físicos.
**Componentes do dispositivo** | Aplicativo de gerenciamento: aplicativo Web no dispositivo para entrada do usuário durante a implantação.<br/> Agente de descoberta: coleta dados de configuração do computador.<br/> Agente de avaliação: coletar dados de desempenho.<br/>  Serviço de atualização automática: atualizar componentes (é executado a cada 24 horas).
**Acesso/portas** | Depois de configurar o dispositivo, as conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/><br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ' https://< dispositivo-IP-ou-Name >: 44368.<br/><br/> Conexões de saída na porta 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.

## <a name="url-access"></a>Acesso à URL

O dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um proxy baseado em URL para se conectar à Internet, permita o acesso a essas URLs, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *. live.com | Entre em sua assinatura do Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie Active Directory aplicativos para o dispositivo se comunicar com as migrações para Azure.
management.azure.com | Crie Active Directory aplicativos para o dispositivo se comunicar com o serviço migrações para Azure.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
*.vault.azure.net | Gerenciar segredos no Azure Key Vault.
aka.ms/* | Permitir acesso a links conhecidos.
download.microsoft.com/download | Permitir downloads do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o dispositivo e o serviço de migrações para Azure.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com <br/> *.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.


## <a name="collected-data---vmware"></a>Dados coletados-VMware

### <a name="collected-performance-data-vmware"></a>Coletados de dados de desempenho-VMware

Aqui estão os dados de desempenho da VM VMware que o dispositivo coleta e envia para o Azure.

**Dados** | **Neutraliza** | **Impacto da avaliação**
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

**Dados** | **Neutraliza**
--- | --- 
**Detalhes do computador** | 
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
**Detalhes de por disco** | 
Valor de chave de disco | disco. Chaves
Número de Dikunit | disk.UnitNumber
Valor da chave do controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk. DeviceInfo. Summary
Nome do disco | Valor gerado usando disco. UnitNumber, disco. Chave, disco. ControllerKey. VAlue
Operações de leitura por segundo | virtualDisk.numberReadAveraged.average
Operações de gravação por segundo | virtualDisk.numberWriteAveraged.average
Taxa de transferência de leitura (MB por segundo) | virtualDisk.read.average
Taxa de transferência de gravação (MB por segundo) | virtualDisk.write.average
**Por detalhes da NIC** | 
Nome do adaptador de rede | NIC. Chaves
Endereço MAC | ((VirtualEthernetCard) NIC). MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Taxa de transferência de leitura (MB por segundo) | NET. Received. média
Taxa de transferência de gravação (MB por segundo) | net.transmitted.average
**Detalhes do caminho de inventário** | 
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

### <a name="collected-metadata-hyper-v"></a>Metadados coletados-Hyper-V

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
Status de energia da VM | Msvm_ComputerSystem | EnabledState
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
3. **Enviar dados**: o dispositivo envia os dados coletados para a avaliação de servidor de migrações para Azure e migração de servidor de migrações para Azure por meio da porta SSL 443. O dispositivo pode se conectar ao Azure pela Internet ou você pode usar o ExpressRoute com emparelhamento público/Microsoft.
    - Para dados de desempenho, o dispositivo coleta dados de utilização em tempo real.
        - Os dados de desempenho são coletados a cada 20 segundos para VMware e a cada 30 segundos para o Hyper-V, para cada métrica de desempenho.
        - Os dados coletados são acumulados para criar um único ponto de dados por 10 minutos.
        - O valor de pico de utilização é selecionado de todos os pontos de dados de 20/30 segundos e enviado ao Azure para cálculo da avaliação.
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

