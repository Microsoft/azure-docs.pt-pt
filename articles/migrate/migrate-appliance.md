---
title: Aplicação do Azure Migrate
description: Fornece uma visão geral do aparelho Azure Migrate utilizado na avaliação e migração do servidor.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 1c21f06e674871aefde1ae952a459db16feeb717
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676344"
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
**Componentes de aparelhos** | O aparelho tem os seguintes componentes:<br/><br/> - **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).<br/>- **Agente DRA**: Orquestra a replicação vm e coordena a comunicação entre máquinas replicadas e Azure. Usado apenas quando replica VMware VMs para Azure usando migração sem agente.<br/>- **Gateway**: Envia dados replicados para o Azure. Usado apenas quando replica VMware VMs para Azure usando migração sem agente.
**Implantação apoiada** | Implemente como VMware VM utilizando o modelo OVA.<br/><br/> Implemente como VMware VM ou máquina física utilizando o script de instalação PowerShell.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 10.000 VMware VMware num servidor vCenter.<br/> Um aparelho pode ligar-se a um único servidor vCenter.
**Modelo DE OVA** | Baixar a partir https://aka.ms/migrate/appliance/vmwaredo portal ou de .<br/><br/> O tamanho do download é de 11,2 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
**Script do PowerShell** | Download [de](https://go.microsoft.com/fwlink/?linkid=2105112)script .<br/><br/> 
**Software/hardware** |  O aparelho deve funcionar na máquina com o Windows Server 2016, 32-GB RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de acesso à Internet, quer diretamente quer através de um representante.<br/><br/> Se executar o aparelho num VMware VM, necessita de recursos suficientes no VCenter Server para alocar um VM que satisfaça os requisitos.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e cumpre os requisitos de hardware. 
**Requisitos vMware** | Se implantar o aparelho como VMware VM, este deve ser implantado num hospedeiro ESXi que executa a versão 5.5 ou posterior.<br/><br/> vCenter Server executando 5.5, 6.0, 6.5 ou 6.7.
**VDDK (migração sem agente)** | Se implantar o aparelho como VMware VM e estiver a fazer uma migração sem agente, o VMware vSphere VDDK deve ser instalado no VM do aparelho.
**Valor hash-OVA** | [Verifique](tutorial-assess-vmware.md#verify-security) os valores de hash do modelo OVA.
**Script hash value-PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.




## <a name="appliance---hyper-v"></a>Aparelho - Hiper-V

**Requisito** | **Hyper-V** 
--- | ---
**Componentes de aparelhos** | O aparelho tem os seguintes componentes:<br/><br/>- **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação apoiada** | Desdobre como Hyper-V VM utilizando um modelo VHD.<br/><br/> Desloque-se como um VM hiper-V ou uma máquina física utilizando um script de instalação PowerShell.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 5000 VMs Hiper-V.<br/> Um aparelho pode ligar até 300 hospedeiros Hyper-V.
**Modelo VHD** | Pasta com fecho, incluindo VHD. Baixar a partir https://aka.ms/migrate/appliance/hypervdo portal ou de .<br/><br/> O tamanho do download é de 10 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
**Script do PowerShell** | Download [de](https://go.microsoft.com/fwlink/?linkid=2105112)script .<br/><br/> 
**Software/hardware***   |  O aparelho deve funcionar na máquina com o Windows Server 2016, 32-GB RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e necessita de acesso à Internet, quer diretamente quer através de um representante.<br/><br/> Se executar o aparelho como um VM Hiper-V, precisa de recursos suficientes no hospedeiro Hyper-V para alocar RAM de 16 GB, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um interruptor externo para o VM do aparelho.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e cumpre os requisitos de hardware. 
**Requisitos de hiper-V** | Se colocar o aparelho com o modelo VHD, o vM do aparelho fornecido pela Azure Migrate é a versão 5.0 do Hyper-V VM.<br/><br/> O hospedeiro Hyper-V deve estar a executar o Windows Server 2012 R2 ou mais tarde. 
**Valor hash-VHD** | [Verifique](tutorial-assess-hyper-v.md#verify-security) os valores de hash do modelo VHD.
**Script hash value-PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.


## <a name="appliance---physical"></a>Aparelho - Físico

**Requisito** | **Físico** 
--- | ---
**Componentes de aparelhos** | O aparelho tem os seguintes componentes: <br/><br/> - **Aplicativo de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Discovery agent**: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de avaliação**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação apoiada** | Desloque-se como uma máquina física dedicada, ou um VM, utilizando um script de instalação PowerShell. O script está disponível para download a partir do portal.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 250 servidores físicos.
**Script do PowerShell** | Descarregue o script (AzureMigrateInstaller.ps1) numa pasta com fecho a partir do portal. [Saiba mais](tutorial-assess-physical.md#set-up-the-appliance). Em alternativa, [baixe diretamente](https://go.microsoft.com/fwlink/?linkid=2105112).<br/><br/> O tamanho do download é de 59,7 MB.
**Software/hardware** |  O aparelho deve funcionar na máquina com o Windows Server 2016, 32-GB RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e necessita de acesso à Internet, quer diretamente quer através de um representante.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e cumpre os requisitos de hardware. 
**Valor de hash** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores de hash do script PowerShell.

## <a name="url-access"></a>Acesso url

O aparelho Azure Migrate precisa de conectividade com a internet.

- Quando implanta o aparelho, a Azure Migrate faz uma verificação de conectividade aos URLs necessários.
- Se estiver a usar um proxy baseado em URL para se ligar à internet, tem de permitir o acesso a estes URLs, certificando-se de que o proxy resolve quaisquer registos CNAME recebidos enquanto procura os URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal do Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Inscreva-se na sua assinatura Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie aplicativos azure Ative Directory (AD) para que o aparelho se comunique com a Azure Migrate.
management.azure.com | Crie aplicativos Azure AD para o aparelho comunicar com o serviço Azure Migrate.
dc.services.visualstudio.com | Faça upload dos registos de aplicativos utilizados para monitorização interna.
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
Nome | recipiente. GetType(). Nome
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
Não. de conexões ativas | netstat

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
Não. de conexões ativas | netstat
ID de Processo  | netstat 
Nome do processo | ps
Argumentos de processo | ps
Nome da aplicação | dpkg ou rpm



## <a name="collected-data---hyper-v"></a>Dados recolhidos - Hiper-V

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise](concepts-dependency-visualization.md) de dependência sem agente).

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para o Azure, máquinas e aplicações de tamanho direito, planos de custos e análise de dependências de aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa dos metadados Hyper-V VM que o aparelho recolhe e envia para o Azure.

**DADOS* | **Classe WMI** | **PROPRIEDADE CLASSE WMI**
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

## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes da Migração Azure que estão a funcionar no aparelho são atualizados. Isto acontece automaticamente porque a atualização automática está ativada no aparelho por defeito. Pode alterar esta definição predefinida para atualizar manualmente os agentes.

- Desligue a **atualização automática**: Desliga a atualização automática no registo, definindo HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" para 0 (DWORD). Se decidir utilizar atualizações manuais, é importante atualizar todos os agentes do aparelho ao mesmo tempo, utilizando o botão **Atualizar** para cada agente desatualizado do aparelho.
- **Atualização manual**: Para atualizações manuais, certifique-se de que atualiza todos os agentes do aparelho, utilizando o botão **Atualizar** para cada agente desatualizado do aparelho. Pode mudar a definição da atualização para atualizações automáticas a qualquer momento.

![Atualizar automaticamente o aparelho](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>Passos seguintes

- [Aprenda](how-to-set-up-appliance-vmware.md) a configurar o aparelho para VMware.
- [Aprenda](how-to-set-up-appliance-hyper-v.md) a configurar o aparelho para hyper-V.
- [Aprenda](how-to-set-up-appliance-physical.md) a configurar o aparelho para servidores físicos.

