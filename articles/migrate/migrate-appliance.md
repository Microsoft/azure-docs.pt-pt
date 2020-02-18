---
title: Aplicação do Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate utilizado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d02227747be4bc7d994e2ea84cd74e7f2fd2531f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425464"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo descreve o aparelho Azure Migrate. Implementa o aparelho quando utiliza a ferramenta [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) para descobrir e avaliar aplicações, infraestruturas e cargas de trabalho para migração para o Microsoft Azure. O aparelho também é utilizado quando migra VMware VMs para Azure utilizando [o Azure Migrate: Avaliação do servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) com [migração sem agente](server-migrate-overview.md).

## <a name="appliance-overview"></a>Visão geral do aparelho

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Usado para** 
--- | --- | ---
VMware VM | Azure Migrate: Avaliação do servidor<br/><br/> Migração Azure: Migração do servidor | Descubra VMware VMs<br/><br/> Descubra aplicações e dependências de máquinas<br/><br/> Recolher metadados de máquinas e metadados de desempenho para avaliações.<br/><br/> Replicar VMware VMs com migração sem agente.
VM hiper-V | Azure Migrate: Avaliação do servidor | Descubra VMs hiper-V<br/><br/> Recolher metadados de máquinas e metadados de desempenho para avaliações.
Máquina física |  Azure Migrate: Avaliação do servidor |  Descubra servidores físicos<br/><br/> Recolher metadados de máquinas e metadados de desempenho para avaliações.

## <a name="appliance---vmware"></a>Aparelho - VMware 

**Requisito** | **VMware** 
--- | ---
**Formato de descarregamento** | . ÓSIO 
**Link de descarregamento** | https://aka.ms/migrate/appliance/vmware 
**Tamanho de download** | 11,2 GB
**Licença** | O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
**Implementação** | Implanta o aparelho como VMware VM. Você precisa de recursos suficientes no vCenter Server para alocar um VM com RAM de 32 GB, 8 vCPUs, cerca de 80 GB de armazenamento de disco, e um interruptor virtual externo.<br/> O aparelho necessita de acesso à Internet, quer diretamente quer através de um representante.<br/> O aparelho pode ligar-se a um único vCenter Server.
**Hardware** | Recursos no vCenter para alocar um VM com 32-GB RAM 8 vCPUs, cerca de 80 GB de armazenamento de disco, e um interruptor virtual externo. 
**Valor de hash** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256: 4ce4faa3a78189a26bfa5b817c7afcf5b555eb4699c2fad9d2ebc808540c
**vCenter servidor/anfitrião** | O vM do aparelho deve ser implantado num hospedeiro ESXi que executa a versão 5.5 ou posterior.<br/><br/> vCenter Server executando 5.5, 6.0, 6.5 ou 6.7.
**Projeto Azure Migrate** | Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Descoberta** | Um aparelho pode descobrir até 10.000 VMware VMware num servidor vCenter.<br/> Um aparelho pode ligar-se a um único servidor vCenter.
**Componentes de aparelhos** | Aplicação de gestão: Aplicação web no aparelho para entrada do utilizador durante a implementação.<br/> Discovery agent: Recolhe dados de configuração da máquina.<br/> Agente de avaliação: Recolher dados de desempenho.<br/> Orquestra a replicação vm e coordena a comunicação entre máquinas/Azure.<br/> Gateway: Envia dados replicados para o Azure.<br/> Serviço de atualização automática: Componentes de atualização (funciona a cada 24 horas).
**VDDK (migração sem agente)** | Se estiver a realizar uma migração sem agente com a Migração do Servidor Migratório Migratório Migratório Migratório Migratório Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migrações, o VMware vSphere VDDK deve ser instalado no VM do aparelho.


## <a name="appliance---hyper-v"></a>Aparelho - Hiper-V

**Requisito** | **Hyper-V** 
--- | ---
**Formato de descarregamento** | Pasta zipped (com VHD)
**Link de descarregamento** | https://aka.ms/migrate/appliance/hyperv 
**Tamanho de download** | 10 GB
**Licença** | O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
**Implantação de aparelhos**   |  Coloca o aparelho como um VM hiper-V.<br/> O vM do aparelho fornecido pela Azure Migrate é a versão 5.0 do Hyper-V VM.<br/> O hospedeiro Hyper-V deve estar a executar o Windows Server 2012 R2 ou mais tarde.<br/> O hospedeiro precisa de espaço suficiente para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento, e um interruptor externo para o VM do aparelho.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e acesso à Internet.
**Hardware** | Recursos no hospedeiro Hyper-V para alocar RAM de 16 GB, 8 vCPUs, cerca de 80 GB de espaço de armazenamento, e um interruptor externo para o vm do aparelho.
**Valor de hash** | MD5: 29a7531f32bcf69f32d964fa5ae950 a.c.<br/><br/> SHA256: 37b3f27bc4f475872e355f04fcb8f38606c8454c117d1609f2d1244569b31
**Anfitrião Hyper-V** | Executar o Windows Server 2012 R2 ou mais tarde.
**Projeto Azure Migrate** | Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Descoberta** | Um aparelho pode descobrir até 5000 VMs Hiper-V.<br/> Um aparelho pode ligar até 300 hospedeiros Hyper-V.
**Componentes de aparelhos** | Aplicação de gestão: Aplicação web no aparelho para entrada do utilizador durante a implementação.<br/> Discovery agent: Recolhe dados de configuração da máquina.<br/> Agente de avaliação: Recolher dados de desempenho.<br/>  Serviço de atualização automática: Componentes de atualização (funciona a cada 24 horas).


## <a name="appliance---physical"></a>Aparelho - Físico

**Requisito** | **Físico** 
--- | ---
**Formato de descarregamento** | Pasta zipped (com script de instalação baseado powerShell)
**Link de descarregamento** | [Link de descarregamento](https://go.microsoft.com/fwlink/?linkid=2105112)
**Tamanho de download** | 59,7 MB
**Hardware** | Máquina física dedicada, ou utilize uma Máquina Virtual. O aparelho de funcionamento da máquina necessita de RAM de 16 GB, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um interruptor externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e acesso à Internet.
**Valor de hash** | MD5: 1e92ede3e87c03bd148e56a708cd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e666de7b9f475b6542beef114b20bfdac3c
**Sistema Operativo** | A máquina de aparelhos deve estar a funcionar no Windows Server 2016. 
**Implantação de aparelhos**   |  O script do instalador do aparelho é descarregado do portal (numa pasta com fecho). <br/> Desaperta a pasta e executa o script PowerShell (AzureMigrateInstaller.ps1).
**Descoberta** | Um aparelho pode descobrir até 250 servidores físicos.
**Componentes de aparelhos** | Aplicação de gestão: Aplicação web no aparelho para entrada do utilizador durante a implementação.<br/> Discovery agent: Recolhe dados de configuração da máquina.<br/> Agente de avaliação: Recolher dados de desempenho.<br/>  Serviço de atualização automática: Componentes de atualização (funciona a cada 24 horas).


## <a name="url-access"></a>Acesso url

O aparelho Azure Migrate precisa de conectividade com a internet.

- Quando implanta o aparelho, a Azure Migrate faz uma verificação de conectividade aos URLs resumidos na tabela abaixo.
- Se estiver a utilizar um proxy baseado em URL para se ligar à internet, permita o acesso a estes URLs, certificando-se de que o representante resolve quaisquer registos CNAME recebidos enquanto procura os URLs.

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Inscreva-se na sua assinatura Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie aplicativos de Diretório Ativo para o aparelho comunicar com a Azure Migrate.
management.azure.com | Crie aplicativos ative diretório para o aparelho comunicar com o serviço Azure Migrate.
dc.services.visualstudio.com | Faça upload dos registos de aplicativos utilizados para monitorização interna.
*.vault.azure.net | Gerencie segredos no Cofre de Chaves Azure.
aka.ms/* | Permitir o acesso a links aka. Utilizado para atualizações de aparelhos Azure Migrate.
download.microsoft.com/download | Permitir transferências a partir do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Ligue-se aos URLs de serviço Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente VMware**<br/><br/> Ligue-se aos URLs de serviço Azure Migrate.
*.blob.core.windows.net |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.




## <a name="collected-data---vmware"></a>Dados recolhidos - VMware

### <a name="collected-performance-data-vmware"></a>Recolhidos dados-VMware

Aqui estão os dados de desempenho vMware VM que o aparelho recolhe e envia para o Azure.

**Dados** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo recomendado de VM
Utilização da memória | mem.usage.average | Tamanho/custo recomendado de VM
Entrada de leitura de disco (MB por segundo) | virtualDisk.read.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
O disco escreve a entrada (MB por segundo) | virtualDisk.write.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Operações de leitura de disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Disco escreve operações por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Nic ler a entrada (MB por segundo) | net.received.average | Cálculo para o tamanho vm
NIC escreve a entrada (MB por segundo) | net.transmitted.average  |Cálculo para o tamanho vm


### <a name="collected-metadata-vmware"></a>Metadados-VMware recolhidos

> [!NOTE]
> Os metadados descobertos pelo aparelho Azure Migrate são utilizados para o ajudar a dimensionar as suas aplicações à medida que as migra para o Azure, realizar análises de adequação do Azure, análise da dependência da aplicação e planeamento de custos. A Microsoft não utiliza estes dados em relação a qualquer auditoria de conformidade com a licença.

Aqui está a lista completa dos metadados VMware VM que o aparelho recolhe e envia para o Azure.

**Dados** | **Contador**
--- | --- 
**Detalhes da máquina** | 
ID DA VM | vm.Config.InstanceUuid 
o nome da VM | vm.Config.Name
id do servidor vCenter | VMwareClient.Instance.Uuid
Descrição vm | vm.Summary.Config.Annotation
Nome do produto de licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operativo | vm.SummaryConfig.GuestFullName
Tipo de arranque | vm.Config.Firmware
Número de núcleos | vm.Config.Hardware.NumCPU
Memória (MB) | vm.Config.Hardware.MemoryMB
Número de discos | vm. Config.Hardware.Device.ToList(). FindAll (x => é VirtualDisk).count
Lista de tamanho seleções | vm. Config.Hardware.Device.ToList(). FindAll (x => é VirtualDisk)
Lista de adaptadores de rede | vm. Config.Hardware.Device.ToList(). FindAll (x => é VirtualEthernet).contagem
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.average
**Por detalhes do disco** | 
Valor da chave do disco | disco. Chave
Número dikunit | disk.UnitNumber
Valor da chave do controlador de disco | disk.ControllerKey.Value
Gigabytes provisionados | virtualDisk.DeviceInfo.Sumário
Nome do disco | Valor gerado usando disco. UnitNúmero, disco. Chave, disco. ControllerKey.VAlue
Ler operações por segundo | virtualDisk.numberReadAveraged.average
Escrever operações por segundo | virtualDisk.numberWriteAveraged.average
Ler a entrada (MB por segundo) | virtualDisk.read.average
Escreva a entrada (MB por segundo) | virtualDisk.write.average
**Por detalhes nic** | 
Nome adaptador de rede | nic. Chave
Endereço MAC | ((VirtualEthernetCard)nic). MacAddress
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Ler a entrada (MB por segundo) | net.received.average
Escreva a entrada (MB por segundo) | net.transmitted.average
**Detalhes do caminho do inventário** | 
Nome | container.GetType().Name
Tipo de objeto infantil | recipiente. Tipo infantil
Detalhes de referência | container.MoRef
Detalhes dos pais | Container.Parent
Detalhes da pasta por VM | ((Folder)container).ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)container).VmFolder
Detalhes do datacenter por pasta anfitriã | ((Datacenter)container).HostFolder
Detalhes do cluster por anfitrião | ((ClusterComputeResource)container).Host
Detalhes do anfitrião por VM | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>Dados recolhidos - Hiper-V

### <a name="collected-performance-data-hyper-v"></a>Dados de desempenho recolhidos-Hyper-V

> [!NOTE]
> Os metadados descobertos pelo aparelho Azure Migrate são utilizados para o ajudar a dimensionar as suas aplicações à medida que as migra para o Azure, realizar análises de adequação do Azure, análise da dependência da aplicação e planeamento de custos. A Microsoft não utiliza estes dados em relação a qualquer auditoria de conformidade com a licença.

Aqui estão os dados de desempenho da Hyper VM que o aparelho recolhe e envia para o Azure.

**Classe de contra-desempenho** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Processador Virtual Hyper-V Hypervisor | % Tempo de execução de hóspedes | Tamanho/custo recomendado de VM
VM de memória dinâmica hiper-V | Pressão Atual (%)<br/> Memória Física Visível do Hóspede (MB) | Tamanho/custo recomendado de VM
Dispositivo de armazenamento virtual hiper-V | Ler Bytes/Segundo | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Dispositivo de armazenamento virtual hiper-V | Escrever Bytes/Segundo | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Adaptador de rede virtual Hiper-V | Bytes Recebidos/Segundo | Cálculo para o tamanho vm
Adaptador de rede virtual Hiper-V | Bytes Enviados/Segundo | Cálculo para o tamanho vm

- A utilização do CPU é a soma de toda a utilização, para todos os processadores virtuais ligados a um VM.
- A utilização da memória é (Pressão atual * Memória Física Visível do Hóspede) / 100.
- Os valores de utilização do disco e da rede são recolhidos nos contadores de desempenho hiper-V listados.

### <a name="collected-metadata-hyper-v"></a>Metadados recolhidos-Hiper-V

Aqui está a lista completa dos metadados Hyper-V VM que o aparelho recolhe e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
**Detalhes da máquina** | 
Número de série de BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição VM | Msvm_VirtualSystemSettingData | Nome do elemento
Versão VM | Msvm_ProcessorSettingData | Quantidade Virtual
Memória (bytes) | Msvm_MemorySettingData | Quantidade Virtual
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica ativada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão do sistema operativo/FQDN | Msvm_KvpExchangeComponent | Dados de nome de TrocadeS De Hóspedes
Estado de potência VM | Msvm_ComputerSystem | Estado habilitado
**Por detalhes do disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Tipo
Tamanho virtual do disco rígido | Msvm_VirtualHardDiskSettingData | Tamanho MaxInternal
Pai de disco rígido virtual | Msvm_VirtualHardDiskSettingData | ParentPath
**Por detalhes nic** | 
Endereços IP (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | Endereços IP
DHCP habilitado (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID NIC (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | Instância
Endereço NIC MAC (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID NIC (NICs legados) | MsvmEmulatedEthernetDados deDefinição de Portas | Instância
NIC MAC ID (NICs legados) | MsvmEmulatedEthernetDados deDefinição de Portas | Endereço




## <a name="discovery-and-collection-process"></a>Processo de descoberta e recolha

O aparelho comunica com os vCenter Servers e os anfitriões/cluster hyper-V utilizando o seguinte processo.

1. **Começar a descoberta:**
    - Quando inicia a descoberta no aparelho Hyper-V, comunica com os anfitriões Hyper-V nas portas WinRM 5985 (HTTP) e 5986 (HTTPS).
    - Quando começa a ser descoberto no aparelho VMware, comunica-se com o servidor vCenter na porta TCP 443 por padrão. Se o servidor vCenter ouvir uma porta diferente, pode configurá-lo na aplicação web do aparelho.
2. **Recolher metadados e dados de desempenho:**
    - O aparelho utiliza uma sessão do Common Information Model (CIM) para recolher dados de VM Hiper-V do hospedeiro Hyper-V nas portas 5985 e 5986.
    - O aparelho comunica com a porta 443 por defeito, para recolher dados VMware VM do vCenter Server.
3. **Enviar dados**: O aparelho envia os dados recolhidos para a Avaliação do Servidor Migratório De Migração Azure e migração do servidor migratório Azure através da porta SSL 443. O aparelho pode ligar-se ao Azure através da internet, ou pode utilizar o ExpressRoute com o público/microsoft a espreitar.
    - Para obter dados de desempenho, o aparelho recolhe dados de utilização em tempo real.
        - Os dados de desempenho são recolhidos a cada 20 segundos para vMware, e a cada 30 segundos para hyper-V, para cada métrica de desempenho.
        - Os dados recolhidos são enrolados para criar um único ponto de dados durante 10 minutos.
        - O valor máximo de utilização é selecionado de todos os pontos de dados de 20/30 segundos, e enviado para o Azure para cálculo de avaliação.
        - Com base no valor percentil especificado nas propriedades de avaliação (50/90/95/99), os pontos de dez minutos são classificados em ordem ascendente, e o valor de percentil apropriado é usado para calcular a avaliação
    - Para a Migração do Servidor, o aparelho começa a recolher dados vM e replica-os para o Azure.
4. **Avaliar e migrar:** Pode agora criar avaliações a partir dos metadados recolhidos pelo aparelho utilizando a Avaliação do Servidor Migratório Azure. Além disso, também pode começar a migrar VMware VMs usando a Migração do Servidor De Migração de Servidores Migradores Azure migrate para orquestrar a replicação vm sem agente.


![Arquitetura](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes da Migração Azure que estão a funcionar no aparelho são atualizados.

- Isto acontece automaticamente porque a atualização automática está ativada no aparelho por defeito.
- Pode alterar esta definição predefinida para atualizar manualmente os agentes.
- Para desativar a atualização automática, vá ao Editor de Registo>HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance e defina a chave de registo - "AutoUpdate" para 0 (DWORD).
 
### <a name="set-agent-updates-to-manual"></a>Definir atualizações de agente para manual

Para atualizações manuais, certifique-se de que atualiza todos os agentes do aparelho ao mesmo tempo, utilizando o botão **Atualizar** para cada agente desatualizado do aparelho. Pode mudar a definição da atualização para atualizações automáticas a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

[Aprenda](tutorial-assess-vmware.md#set-up-the-appliance-vm) a configurar o aparelho para VMware.
[Aprenda](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) a configurar o aparelho para hyper-V.

