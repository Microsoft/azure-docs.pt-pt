---
title: Aplicação do Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate utilizado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: bd5686b30b07c0f7fb8961f8d1f71035cb2688a4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656423"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo resume os requisitos pré-requisitos e requisitos de suporte para o aparelho Migratório Azure. 

## <a name="deployment-scenarios"></a>Cenários de implementação

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Utilizado para** 
--- | --- | ---
**Avaliação vmware vm** | Azure Migrate:Avaliação do Servidor | Descubra VMware VMs<br/><br/> Descubra aplicações e dependências de máquinas<br/><br/> Recolher metadados de máquinas e metadados de desempenho para avaliações.
**Migração sem agente VMware VM** | Migração Azure:Migração do servidor | Descubra VMware VMs <br/><br/> Replicar VMware VMs com migração sem agente.
**Avaliação de VM hiper-V** | Azure Migrate:Avaliação do Servidor | Descubra VMs hiper-V<br/><br/> Recolher metadados de máquinas e metadados de desempenho para avaliações.
**Avaliação física da máquina** |  Azure Migrate:Avaliação do Servidor |  Descubra servidores físicos (ou VMs que trate como servidores físicos).<br/><br/> Recolher metadados de máquinas e metadados de desempenho para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O aparelho pode ser acionado com um par de métodos:

- O aparelho pode ser implantado utilizando um modelo para VMware VMs e VMs Hiper-V (modelo OVA para VMware ou VHD para Hyper-V).
- Se não quiser utilizar um modelo, pode utilizar o aparelho para VMware ou Hyper-V utilizando um script PowerShell.
- No Governo Azure, deve utilizar o aparelho utilizando um guião.
- Para servidores físicos, utilize sempre o aparelho utilizando um script.
- Os links de descarregamento estão disponíveis nas tabelas abaixo.


## <a name="appliance---vmware"></a>Aparelho - VMware 

A tabela que se segue resume os requisitos do aparelho Azure Migrate para vMware.

**Requisito** | **VMware** 
--- | ---
**Permissões** | Para aceder à aplicação web do aparelho local ou remotamente, é necessário ser administrador de domínio ou administração local na máquina do aparelho.
**Componentes de aparelhos** | O aparelho tem os seguintes componentes:<br/><br/> - **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).<br/>- **Agente DRA**: Orquestra a replicação vm e coordena a comunicação entre máquinas replicadas e Azure. Usado apenas quando replica VMware VMs para Azure usando migração sem agente.<br/>- **Gateway**: Envia dados replicados para o Azure. Usado apenas quando replica VMware VMs para Azure usando migração sem agente.
**Implantação apoiada** | Implemente como VMware VM utilizando o modelo OVA.<br/><br/> Implemente como VMware VM ou máquina física utilizando o script de instalação PowerShell.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 10.000 VMware VMware num servidor vCenter.<br/> Um aparelho pode ligar-se a um único servidor vCenter.
**Modelo DE OVA** | Baixar a partir do portal ou de https://aka.ms/migrate/appliance/vmware .<br/><br/> O tamanho do download é de 11,2 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
**Script do PowerShell** | Download [de](https://go.microsoft.com/fwlink/?linkid=2105112)script .<br/><br/> 
**Software/hardware** |  O aparelho deve funcionar na máquina com o Windows Server 2016, 32-GB RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de acesso à Internet, quer diretamente quer através de um representante.<br/><br/> Se executar o aparelho num VMware VM, necessita de recursos suficientes no VCenter Server para alocar um VM que satisfaça os requisitos.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e cumpre os requisitos de hardware. 
**Requisitos vMware** | Se implantar o aparelho como VMware VM, este deve ser implantado num hospedeiro ESXi que executa a versão 5.5 ou posterior.<br/><br/> vCenter Server executando 5.5, 6.0, 6.5 ou 6.7.
**VDDK (migração sem agente)** | Se implantar o aparelho como VMware VM e estiver a fazer uma migração sem agente, o VMware vSphere VDDK deve ser instalado no VM do aparelho.
**Valor hash-OVA** | [Verifique](tutorial-assess-vmware.md#verify-security) os valores de hash do modelo OVA.
**Script hash value-PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.




## <a name="appliance---hyper-v"></a>Aparelho - Hiper-V

**Requisito** | **Hyper-V** 
--- | ---
**Permissões** | Para aceder à aplicação web do aparelho local ou remotamente, é necessário ser administrador de domínio ou administração local na máquina do aparelho.
**Componentes de aparelhos** | O aparelho tem os seguintes componentes:<br/><br/>- **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação apoiada** | Desdobre como Hyper-V VM utilizando um modelo VHD.<br/><br/> Desloque-se como um VM hiper-V ou uma máquina física utilizando um script de instalação PowerShell.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 5000 VMs Hiper-V.<br/> Um aparelho pode ligar até 300 hospedeiros Hyper-V.
**Modelo VHD** | Pasta com fecho, incluindo VHD. Baixar a partir do portal ou de https://aka.ms/migrate/appliance/hyperv .<br/><br/> O tamanho do download é de 10 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
**Script do PowerShell** | Download [de](https://go.microsoft.com/fwlink/?linkid=2105112)script .<br/><br/> 
**Software/hardware***   |  O aparelho deve funcionar na máquina com o Windows Server 2016, 32-GB RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e necessita de acesso à Internet, quer diretamente quer através de um representante.<br/><br/> Se executar o aparelho como um VM Hiper-V, precisa de recursos suficientes no hospedeiro Hyper-V para alocar RAM de 16 GB, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um interruptor externo para o VM do aparelho.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e cumpre os requisitos de hardware. 
**Requisitos de hiper-V** | Se colocar o aparelho com o modelo VHD, o vM do aparelho fornecido pela Azure Migrate é a versão 5.0 do Hyper-V VM.<br/><br/> O hospedeiro Hyper-V deve estar a executar o Windows Server 2012 R2 ou mais tarde. 
**Valor hash-VHD** | [Verifique](tutorial-assess-hyper-v.md#verify-security) os valores de hash do modelo VHD.
**Script hash value-PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.


## <a name="appliance---physical"></a>Aparelho - Físico

**Requisito** | **Físico** 
--- | ---
**Permissões** | Para aceder à aplicação web do aparelho local ou remotamente, é necessário ser administrador de domínio ou administração local na máquina do aparelho.
**Componentes de aparelhos** | O aparelho tem os seguintes componentes: <br/><br/> - **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação apoiada** | Desloque-se como uma máquina física dedicada, ou um VM, utilizando um script de instalação PowerShell. O script está disponível para download a partir do portal.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 250 servidores físicos.
**Script do PowerShell** | Descarregue o script (AzureMigrateInstaller.ps1) numa pasta com fecho a partir do portal. [Saiba mais](tutorial-assess-physical.md#set-up-the-appliance). Em alternativa, [baixe diretamente](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> O tamanho do download é de 59,7 MB.
**Software/hardware** |  O aparelho deve funcionar na máquina com o Windows Server 2016, 32-GB RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e necessita de acesso à Internet, quer diretamente quer através de um representante.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e cumpre os requisitos de hardware.<br/> Não é suportado o funcionamento do aparelho numa máquina com o Windows Server 2019.
**Valor de hash** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.

## <a name="url-access"></a>Acesso url

O aparelho Azure Migrate precisa de conectividade com a internet.

- Quando implanta o aparelho, a Azure Migrate faz uma verificação de conectividade aos URLs necessários.
- Tem de permitir o acesso a todos os URLs da lista. Se estiver a fazer apenas avaliação, pode saltar os URLs que estão marcados como necessários apenas para a migração sem agente VMware.
-  Se estiver a utilizar um representante baseado em URL para se ligar à internet, certifique-se de que o representante resolve quaisquer registos CNAME recebidos enquanto procura os URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Inscreva-se na sua assinatura Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie aplicativos azure Ative Directory (AD) para que o aparelho se comunique com a Azure Migrate.
management.azure.com | Crie aplicativos Azure AD para o aparelho comunicar com o serviço Azure Migrate.
*.services.visualstudio.com | Faça upload dos registos de aplicativos utilizados para monitorização interna.
*.vault.azure.net | Gerencie segredos no Cofre de Chaves Azure.
aka.ms/* | Permitir o acesso a links aka. Utilizado para atualizações de aparelhos Azure Migrate.
download.microsoft.com/download | Permitir transferências a partir do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Ligue-se aos URLs de serviço Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente VMware**<br/><br/> Ligue-se aos URLs de serviço Azure Migrate.
*.blob.core.windows.net |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.

### <a name="government-cloud-urls"></a>URLs de nuvem do governo

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.us  | Navegue para o portal do Azure.
graph.windows.net | Inscreva-se na sua assinatura Azure.
login.microsoftonline.us  | Crie aplicativos azure Ative Directory (AD) para que o aparelho se comunique com a Azure Migrate.
management.usgovcloudapi.net | Crie aplicativos Azure AD para o aparelho comunicar com o serviço Azure Migrate.
dc.services.visualstudio.com | Faça upload dos registos de aplicativos utilizados para monitorização interna.
*.vault.usgovcloudapi.net | Gerencie segredos no Cofre de Chaves Azure.
aka.ms/* | Permitir o acesso a links aka. Utilizado para atualizações de aparelhos Azure Migrate.
download.microsoft.com/download | Permitir transferências a partir do download da Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Ligue-se aos URLs de serviço Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Usado para migração sem agente VMware**<br/><br/> Ligue-se aos URLs de serviço Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.
*.applicationinsights.us | Faça upload dos registos de aplicativos utilizados para monitorização interna.





## <a name="collected-data---vmware"></a>Dados recolhidos - VMware

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise](concepts-dependency-visualization.md) de dependência sem agente).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para o Azure, máquinas e aplicações de tamanho direito, planos de custos e análise de dependências de aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa dos metadados VMware VM que o aparelho recolhe e envia para o Azure.

**DADOS** | **CONTADOR**
--- | --- 
**Detalhes da máquina** | 
ID da VM | vm. Config.InstanceUid 
o nome da VM | vm. Config.Name
id do servidor vCenter | VMwareClient.Instance.Uuid
Descrição vm | vm. Resumo.Config.Anotação
Nome do produto de licença | vm. Cliente.ServiceContent.About.LicenseProductName
Tipo de sistema operativo | vm. ResumoConfig.GuestFullName
Tipo de arranque | vm. Config.Firmware
Número de núcleos | vm. Config.Hardware.NumCPU
Memória (MB) | vm. Config.Hardware.MemoryMB
Número de discos | vm. Config.Hardware.Device.ToList(). FindAll (x => é VirtualDisk).count
Lista de tamanho seleções | vm. Config.Hardware.Device.ToList(). FindAll (x => é VirtualDisk)
Lista de adaptadores de rede | vm. Config.Hardware.Device.ToList(). FindAll (x => é virtualEthernet).contagem
Utilização da CPU | cpu.usage.média
Utilização da memória |mem.usage.average
**Por detalhes do disco** | 
Valor da chave do disco | disco. Chave
Número dikunit | disco. Número unitário
Valor da chave do controlador de disco | disco. ControllerKey.Value
Gigabytes provisionados | virtualDisk.DeviceInfo.Sumário
Nome do disco | Valor gerado usando disco. UnitNúmero, disco. Chave, disco. ControllerKey.VAlue
Ler operações por segundo | virtualDisk.numberReadAveraged.average
Escrever operações por segundo | virtualDisk.numberWriteAveraged.average
Ler a entrada (MB por segundo) | virtualDisk.read.average
Escreva a entrada (MB por segundo) | virtualDisk.write.average
**Por detalhes nic** | 
Nome adaptador de rede | nic. Chave
Endereço MAC | ((VirtualEthernetCard)nic). MacAddress
Endereços IPv4 | vm. Guest.Net
Endereços IPv6 | vm. Guest.Net
Ler a entrada (MB por segundo) | net.received.average
Escreva a entrada (MB por segundo) | net.transmitted.average
**Detalhes do caminho do inventário** | 
Name | recipiente. GetType(). Nome
Tipo de objeto infantil | recipiente. Tipo infantil
Detalhes de referência | recipiente. MoRef
Detalhes dos pais | Contentor.Pai
Detalhes da pasta por VM | ((Pasta)recipiente). ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)container). VmFolder
Detalhes do datacenter por pasta anfitriã | ((Datacenter)container). Pasta anfitriã
Detalhes do cluster por anfitrião | ((ClusterComputeResource)recipiente). Anfitrião
Detalhes do anfitrião por VM | (contentor do Sistema de Acolhimento). VM

### <a name="performance-data"></a>Dados de desempenho


Aqui estão os dados de desempenho vMware VM que o aparelho recolhe e envia para o Azure.

**Dados** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.média | Tamanho/custo recomendado de VM
Utilização da memória | mem.usage.average | Tamanho/custo recomendado de VM
Entrada de leitura de disco (MB por segundo) | virtualDisk.read.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
O disco escreve a entrada (MB por segundo) | virtualDisk.write.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Operações de leitura de disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Disco escreve operações por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Nic ler a entrada (MB por segundo) | net.received.average | Cálculo para o tamanho vm
NIC escreve a entrada (MB por segundo) | net.transmitted.average  |Cálculo para o tamanho vm

### <a name="app-dependencies-metadata"></a>Metadados de dependências de aplicações

A análise de dependência sem agente recolhe dados de conexão e de processo.

#### <a name="connection-data"></a>Dados de ligação

Aqui estão os dados de ligação que o aparelho recolhe de cada VM habilitado para análise de dependência sem agente. Estes dados são enviados para o Azure.

**Dados** | **Comando utilizado** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado de ligação TCP | netstat
ID de Processo | netstat
Número de ligações ativas | netstat

#### <a name="process-data"></a>Processar dados
Aqui estão os dados do processo que o aparelho recolhe de cada VM habilitado para análise de dependência sem agente. Estes dados são enviados para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | Caminho Executável
Argumentos de processo | Win32_Process | Linha de Comando
Nome da aplicação | Win32_Process | VersionInfo.ProductName parâmetro da propriedade ExecutablePath

#### <a name="linux-vm-data"></a>Dados de VM Linux

Aqui estão os dados de ligação e tratamento que o aparelho recolhe de cada VM Linux habilitado para análise de dependência sem agente. Estes dados são enviados para o Azure.

**Dados** | **Comando utilizado** 
--- | ---
Porta local | netstat 
Endereço IP local | netstat 
Porta remota | netstat 
Endereço IP remoto | netstat 
Estado de ligação TCP | netstat 
Número de ligações ativas | netstat
ID de Processo  | netstat 
Nome do processo | ps
Argumentos de processo | ps
Nome da aplicação | dpkg ou rpm



## <a name="collected-data---hyper-v"></a>Dados recolhidos - Hiper-V

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise](concepts-dependency-visualization.md) de dependência sem agente).

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para o Azure, máquinas e aplicações de tamanho direito, planos de custos e análise de dependências de aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa dos metadados Hyper-V VM que o aparelho recolhe e envia para o Azure.

**DADOS** | **Classe WMI** | **PROPRIEDADE CLASSE WMI**
--- | --- | ---
**Detalhes da máquina** | 
Número de série de BIOS _ Msvm_BIOSElement | BIOSSerialNumber
Tipo VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | Subtipo virtual do sistema
Nome de exibição VM | Msvm_VirtualSystemSettingData | ElementName
Versão VM | Msvm_ProcessorSettingData | Quantidade Virtual
Memória (bytes) | Msvm_MemorySettingData | Quantidade Virtual
Memória máxima que pode ser consumida pela VM | Msvm_MemorySettingData | Limite
Memória dinâmica ativada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão do sistema operativo/FQDN | Msvm_KvpExchangeComponent | Dados de nome de TrocadeS De Hóspedes
Estado de potência VM | Msvm_ComputerSystem | EnabledState
**Por detalhes do disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | Virtualdiskid
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Tipo
Tamanho virtual do disco rígido | Msvm_VirtualHardDiskSettingData | Tamanho MaxInternal
Pai de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Caminho-de-semana
**Por detalhes nic** | 
Endereços IP (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP habilitado (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID NIC (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço NIC MAC (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | Endereço
ID NIC (NICs legados) | MsvmEmulatedEthernetDados deDefinição de Portas | InstanceID
NIC MAC ID (NICs legados) | MsvmEmulatedEthernetDados deDefinição de Portas | Endereço

### <a name="performance-data"></a>Dados de desempenho

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


## <a name="collected-data---physical"></a>Dados recolhidos - Físico

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise](concepts-dependency-visualization.md) de dependência sem agente).

### <a name="windows-metadata"></a>Metadados do Windows

Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para o Azure, máquinas e aplicações de tamanho direito, planos de custos e análise de dependências de aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa dos metadados do servidor do Windows que o aparelho recolhe e envia para o Azure.

**DADOS** | **Classe WMI** | **PROPRIEDADE CLASSE WMI**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domínio, Nome, PartofDomain
Contagem do núcleo do processador | Win32_PRocessor | NumberOfCores
Memória atribuída | Win32_ComputerSystem | Memória Total Física
Número de série bios | Win32_ComputerSystemProduct | Número de identificação
BIOS GUID | Win32_ComputerSystemProduct | UUID
Tipo de arranque | Win32_DiskPartition | Verifique a partilha com o Tipo = **GPT:Sistema** de EFI/BIOS
Nome os | Win32_OperatingSystem | Legenda
Versão do SO |Win32_OperatingSystem | Versão
Arquitetura osso | Win32_OperatingSystem | ARQUITETURA OS
Contagem de discos | Win32_DiskDrive | Modelo, Tamanho, DispositivoID, MediaType, Nome
Tamanho do disco | Win32_DiskDrive | Tamanho
Lista NIC | Win32_NetworkAdapterConfiguration | Descrição, Índice
Endereço IP NIC | Win32_NetworkAdapterConfiguration | IPAddress
Endereço NIC MAC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Metadados Linux

Aqui está a lista completa dos metadados do servidor Linux que o aparelho recolhe e envia para o Azure.

**DADOS** | **LINUX** 
--- | --- 
FQDN | gato /proc/sys/kernel/hostname, nome de anfitrião -f
Contagem do núcleo do processador |  /proc/cpuinfo \| awk '/^processador/{print $3}' \| wc -l
Memória atribuída | gato /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Número de série bios | Lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2="; print}'
BIOS GUID | gato /sys/class/dmi/id/product_uuid
Tipo de arranque | [ -d /sys/firmware/efi ] && eco EFI \| \| echo BIOS
Nome/versão osso | Acedemos a estes ficheiros para a versão e nome sinuosos:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/lançamento da empresa <br/> /etc/redhat-release<br/> /etc/oráculo-libertação<br/>  /etc/Libertação suSE<br/>  /etc/lsb-release  <br/> /etc/debian_version
Arquitetura osso | Uname -m
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Disco de arranque | df /boot \| sed -n 2p \| awk '{print $1}'
Tamanho do disco | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Lista NIC | ip -o -4 addr mostrar \| awk '{print $2}'
Endereço IP NIC | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1-d "/" 
Endereço NIC MAC | ip addr mostrar $nic \| ether grep \| awk '{print $2}'

### <a name="windows-performance-data"></a>Dados de desempenho do Windows

Aqui estão os dados de desempenho do servidor do Windows que o aparelho recolhe e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
Utilização da CPU | Win32_PerfFormattedData_PerfOS_Processor | PercentidleTime
Uso da memória | Win32_PerfFormattedData_PerfOS_Memory | MBytes disponíveis
Contagem nic | Win32_PerfFormattedData_Tcpip_NetworkInterface | Faça a contagem do dispositivo de rede.
Dados recebidos por NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dados transmitidos por NIC | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Contagem de discos | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Contagem de discos
Detalhes do disco | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dados de desempenho do Linux

Aqui estão os dados de desempenho do servidor Linux que o aparelho recolhe e envia para o Azure.

**Dados** | **Linux** 
--- | --- 
Utilização da CPU | gato /proc/stat/| grep 'cpu' /proc/stat
Uso da memória | grep free \| Mem \| awk '{print $3/$2 * 100.0}'
Contagem nic | Lshw -classe \| rede grep eth[0-60] \| wc -l
Dados recebidos por NIC | gato /sys/class/net/eth$nic/statistics/rx_bytes
Dados transmitidos por NIC | gato /sys/class/net/eth$nic/statistics/tx_bytes
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Detalhes do disco | gato /proc/diskstats


## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes da Migração Azure que estão a funcionar no aparelho são atualizados. Isto acontece automaticamente, uma vez que a atualização automática está ativada no aparelho por defeito. Pode alterar esta definição predefinida, para atualizar manualmente os serviços de aparelhos.

### <a name="turn-off-auto-update"></a>Desligue a atualização automática

1. Na máquina que executa o aparelho, abra o Editor de Registo.
2. Navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Para desativar a atualização automática, crie uma chave de registo **AutoUpdate** com o valor DWORD de 0.

    ![Definir chave de registo](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Ativar a tualização automática

Pode ativar a atualização automática utilizando qualquer um destes métodos:

- Ao apagar a chave de registo AutoUpdate de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Após a descoberta estar concluída, no Gestor de Configuração do Aparelho.

Para eliminar a chave de registo:

1. Na máquina que executa o aparelho, abra o Editor de Registo.
2. Navegue para **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Elimine a chave de registo **AutoUpdate,** que foi previamente criada para desligar a atualização automática.

Para ligar a partir do Gestor de Configuração do Aparelho, após a descoberta estar completa:

1. Na máquina do aparelho, abra o Gestor de Configuração do Aparelho.
2. Nos **serviços de eletrodomésticos**A  >  **atualização automática dos componentes do Azure Migrate é desligada,** clique para ligar a atualização automática.

    ![Ativar atualizações automáticas](./media/migrate-appliance/turn-on.png)

### <a name="check-the-appliance-services-version"></a>Verifique a versão dos serviços de eletrodomésticos

Pode verificar a versão dos serviços de eletrodomésticos utilizando qualquer um destes métodos:

- No Gestor de Configuração do Aparelho, após a descoberta estar concluída.
- Na máquina do aparelho, nos **Control Panel**  >  **Programas e Características**do Painel de Controlo .

Para verificar o Gestor de Configuração do Aparelho:

1. Após a descoberta estar concluída, abra o Gestor de Configuração do Aparelho (na aplicação web do aparelho).
2. Nos **serviços de eletrodomésticos,** verifique as versões dos serviços de eletrodomésticos.

    ![Ver versão](./media/migrate-appliance/version.png)

Para verificar no Painel de Controlo:

1. No aparelho, clique em **Programas**e  >  **Control Panel**  >  **Funcionalidades** do Painel de Controlo de Início
2. Verifique as versões dos serviços de eletrodomésticos na lista.

    ![Ver versão no Painel de Controlo](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Atualizar manualmente uma versão mais antiga

Se estiver a executar uma versão mais antiga para qualquer um dos componentes, tem de desinstalar o serviço e atualizar manualmente para a versão mais recente.

1. Para verificar as versões mais recentes do serviço de eletrodomésticos, [descarregue](https://aka.ms/latestapplianceservices) o ficheiro LatestComponents.json.
2.  Depois de descarregar, abra o ficheiro LatestComponents.json no Bloco de Notas.
3. Encontre a versão de serviço mais recente no ficheiro e o link de descarregamento para o mesmo. Por exemplo:

    "Nome": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi " versão": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.  Descarregue a versão mais recente de um serviço desatualizado, utilizando o link de descarregamento no ficheiro.
5. Depois de descarregar, faça o seguinte comando numa janela de comando de administrador, para verificar a integridade do MSI descarregado.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```Por exemplo: C: \> CertUtil -HashFile C:\Utilizadores\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Verifique se a saída de comando corresponde à entrada do valor hash para o serviço no ficheiro (por exemplo, o valor hash MD5 acima).
6. Agora, execute o MSI para instalar o serviço. É uma instalação silenciosa, e a janela de instalação fecha depois de ser feita.
7. Após a instalação estar concluída, **Control panel**verifique a versão do serviço em  >  **Programas e Funcionalidades**do painel de controlo . A versão de serviço deve agora ser atualizada para as mais recentes mostradas no ficheiro json.



## <a name="next-steps"></a>Passos seguintes

- [Aprenda](how-to-set-up-appliance-vmware.md) a configurar o aparelho para VMware.
- [Aprenda](how-to-set-up-appliance-hyper-v.md) a configurar o aparelho para hyper-V.
- [Aprenda](how-to-set-up-appliance-physical.md) a configurar o aparelho para servidores físicos.

