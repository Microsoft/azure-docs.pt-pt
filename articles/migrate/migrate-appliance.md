---
title: Aplicação do Azure Migrate
description: Fornece um resumo do suporte para o aparelho Azure Migrate.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: ac3c90f1c09d290d5112a0e0d7abc5218788caf7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450050"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo resume os pré-requisitos e requisitos de suporte para o aparelho Azure Migrate. 

## <a name="deployment-scenarios"></a>Cenários de implementação

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Utilizado para** 
--- | --- | ---
**Avaliação VMware VM** | Azure Migrate:Avaliação do servidor | Detetar VMs VMware<br/><br/> Descubra aplicativos e dependências de máquinas<br/><br/> Recolha metadados de máquina e metadados de desempenho para avaliações.
**VMware VM migração sem agente** | Azure Migrate:Migração de servidores | Detetar VMs VMware <br/><br/> Replicar VMware VMs com migração sem agente.
**Avaliação de VM hiper-V** | Azure Migrate:Avaliação do servidor | Detetar VMs Hyper-V<br/><br/> Recolha metadados de máquina e metadados de desempenho para avaliações.
**Avaliação física da máquina** |  Azure Migrate:Avaliação do servidor |  Descubra servidores físicos (ou VMs que trata como servidores físicos).<br/><br/> Recolha metadados de máquina e metadados de desempenho para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O aparelho pode ser implantado utilizando um par de métodos:

- O aparelho pode ser implantado utilizando um modelo para VMware VMs e VMs Hiper-V (modelo OVA para VMware ou VHD para Hiper-V).
- Se não quiser utilizar um modelo, pode utilizar o aparelho para VMware ou Hyper-V utilizando um script PowerShell.
- No Governo Azure, deve utilizar o aparelho utilizando um script.
- Para servidores físicos, utilize sempre o aparelho utilizando um script.
- Os links de descarregamento estão disponíveis nas tabelas abaixo.


## <a name="appliance---vmware"></a>Aparelho - VMware 

A tabela que se segue resume os requisitos do aparelho Azure Migrate para o VMware.

**Requisito** | **VMware** 
--- | ---
**Permissões** | Para aceder à aplicação web do aparelho local ou remotamente, é necessário ser administrador de domínio ou administrador local na máquina do aparelho.
**Componentes do aparelho** | O aparelho tem os seguintes componentes:<br/><br/> - **Aplicação de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Agente de**descoberta: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de recolha**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).<br/>- **Agente DRA**: Orquestra a replicação VM e coordena a comunicação entre máquinas replicadas e Azure. Utilizado apenas na replicação de VMware VMs para Azure usando migração sem agente.<br/>- **Gateway**: Envia dados replicados para Azure. Utilizado apenas na replicação de VMware VMs para Azure usando migração sem agente.
**Implantação suportada** | Implemente como VMware VM utilizando o modelo OVA.<br/><br/> Implemente como VMware VM ou máquina física utilizando o script de instalação PowerShell.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 10.000 VMware VMs num servidor vCenter.<br/> Um aparelho pode ligar-se a um único servidor vCenter.
**Modelo de OVA** | Descarregue a partir do portal ou a partir [daqui](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> O tamanho do download é de 11,9 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo de expirar, recomendamos que descarregue e implemente um novo aparelho, ou que ative a licença do sistema operativo do aparelho VM.
**Script do PowerShell** | Consulte este [artigo.](./deploy-appliance-script.md#set-up-the-appliance-for-vmware)<br/><br/> 
**Software/hardware** |  O aparelho deve funcionar em máquina com o Windows Server 2016, 32 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho requer acesso à Internet, diretamente ou através de um representante.<br/><br/> Se executar o aparelho num VMware VMware, precisa de recursos suficientes no servidor vCenter para alocar um VM que satisfaça os requisitos.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e que cumpre os requisitos de hardware.
**Requisitos de VMware** | Se colocar o aparelho como VMware VM, deve ser implantado numa versão de execução do anfitrião ESXi 5.5 ou posterior.<br/><br/> vCenter Server com execução de 5.5, 6.0, 6.5 ou 6.7.
**VDDK (migração sem agente)** | Se colocar o aparelho como VMware VMware e estiver a executar uma migração sem agentes, o VMware vSphere VDDK deve ser instalado no VM do aparelho.
**Valor de haxixe-OVA** | [Verifique](tutorial-discover-vmware.md#verify-security) os valores do hash do modelo OVA.
**Roteiro hash value-PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores do hash do script PowerShell.




## <a name="appliance---hyper-v"></a>Aparelho - Hiper-V

**Requisito** | **Hyper-V** 
--- | ---
**Permissões** | Para aceder à aplicação web do aparelho local ou remotamente, é necessário ser administrador de domínio ou administrador local na máquina do aparelho.
**Componentes do aparelho** | O aparelho tem os seguintes componentes:<br/><br/>- **Aplicação de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Agente de**descoberta: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de recolha**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação suportada** | Implemente como Hyper-V VM utilizando um modelo VHD.<br/><br/> Implemente como um VM Hiper-V ou uma máquina física utilizando um script de instalação PowerShell.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 5000 Hiper-VMs.<br/> Um aparelho pode ligar até 300 anfitriões Hiper-V.
**Modelo VHD** | Pasta com fecho, incluindo VHD. Descarregue a partir do portal ou a partir [daqui.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> O tamanho do download é de 8.91 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo de expirar, recomendamos que descarregue e implemente um novo aparelho, ou que ative a licença do sistema operativo do aparelho VM.
**Script do PowerShell** | Consulte este [artigo.](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v)<br/><br/> 
**Software/hardware***   |  O aparelho deve funcionar em máquina com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou através de um representante.<br/><br/> Se executar o aparelho como Um VM Hiper-V, precisa de recursos suficientes no hospedeiro Hyper-V para alocar os requisitos de hardware.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e que cumpre os requisitos de hardware. 
**Requisitos de hiper-V** | Se colocar o aparelho com o modelo VHD, o aparelho VM fornecido pela Azure Migrate é a versão 5.0 do Hiper-VM.<br/><br/> O anfitrião Hyper-V deve estar a executar o Windows Server 2012 R2 ou mais tarde. 
**Valor de haxixe VHD** | [Verificar](tutorial-discover-hyper-v.md#verify-security) Valores de hash do modelo VHD.
**Roteiro hash value-PowerShell** | [Verifique](deploy-appliance-script.md#verify-file-security) os valores do hash do script PowerShell.


## <a name="appliance---physical"></a>Aparelho - Físico

**Requisito** | **Físico** 
--- | ---
**Permissões** | Para aceder à aplicação web do aparelho local ou remotamente, é necessário ser administrador de domínio ou administrador local na máquina do aparelho.
**Componentes do aparelho** | O aparelho tem os seguintes componentes: <br/><br/> - **Aplicação de gestão**: Esta é uma aplicação web para a entrada do utilizador durante a implementação do aparelho. Usado na avaliação de máquinas para migração para Azure.<br/> - **Agente de**descoberta: O agente recolhe dados de configuração da máquina. Usado na avaliação de máquinas para migração para Azure.<br/>- **Agente de recolha**: O agente recolhe dados de desempenho. Usado na avaliação de máquinas para migração para Azure.<br/>- **Serviço de atualização automática**: Atualiza os componentes do aparelho (funciona a cada 24 horas).
**Implantação suportada** | Implemente como uma máquina física dedicada, ou um VM, utilizando um script de instalação PowerShell. O script está disponível para download a partir do portal.
**Apoio ao projeto** |  Um aparelho pode ser associado a um único projeto. <br/> Qualquer número de aparelhos pode ser associado a um único projeto.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 1000 servidores físicos.
**Script do PowerShell** | Descarregue o script (AzureMigrateInstaller.ps1) numa pasta com fecho de correr a partir do portal ou a partir [daqui](https://go.microsoft.com/fwlink/?linkid=2140334). [Saiba mais](tutorial-discover-physical.md).<br/><br/> O tamanho do download é de 85,8 MB.
**Software/hardware** |  O aparelho deve funcionar em máquina com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou através de um representante.<br/><br/> Se executar o aparelho numa máquina física, certifique-se de que está a executar o Windows Server 2016 e que cumpre os requisitos de hardware.<br/>_(Atualmente, a implementação do aparelho só é suportada no Windows Server 2016.)_
**Valor de hash** | [Verifique](tutorial-discover-physical.md#verify-security) os valores do hash do script PowerShell.

## <a name="url-access"></a>Acesso a URL

O aparelho Azure Migrate necessita de conectividade com a internet.

- Quando coloca o aparelho, a Azure Migrate faz uma verificação de conectividade para os URLs necessários.
- Tem de permitir o acesso a todos os URLs da lista. Se estiver a fazer apenas avaliações, pode saltar os URLs que são marcados como necessário apenas para a migração sem agente VMware.
-  Se estiver a utilizar um representante baseado em URL para se ligar à internet, certifique-se de que o representante resolve quaisquer registos CNAME recebidos enquanto procura os URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal do Azure.
\*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Inscreva-se na sua assinatura Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie aplicativos Azure Ative Directory (AD) para o aparelho comunicar com a Azure Migrate.
management.azure.com | Crie aplicativos AD AZure para o aparelho comunicar com o serviço Azure Migrate.
*.services.visualstudio.com | Faça upload de registos de aplicativos utilizados para monitorização interna.
*.vault.azure.net | Gerir segredos no Cofre da Chave Azure. Nota: Certifique-se de que as máquinas para replicar têm acesso a isto.
aka.ms/* | Permitir o acesso a links aka. Utilizado para atualizações de aparelhos Azure Migrate.
download.microsoft.com/download | Permita downloads a partir do download da Microsoft.
*.servicebus.windows.net | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Ligue-se aos URLs de serviço Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usado para migração sem agente VMware**<br/><br/> Ligue-se aos URLs de serviço Azure Migrate.
*.blob.core.windows.net |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.

### <a name="government-cloud-urls"></a>URLs de nuvem do governo

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.us  | Navegue para o portal do Azure.
graph.windows.net | Inscreva-se na sua assinatura Azure.
login.microsoftonline.us  | Crie aplicativos Azure Ative Directory (AD) para o aparelho comunicar com a Azure Migrate.
management.usgovcloudapi.net | Crie aplicativos AD AZure para o aparelho comunicar com o serviço Azure Migrate.
*.services.visualstudio.com | Faça upload de registos de aplicativos utilizados para monitorização interna.
*.vault.usgovcloudapi.net | Gerir segredos no Cofre da Chave Azure.
aka.ms/* | Permitir o acesso a links aka. Utilizado para atualizações de aparelhos Azure Migrate.
download.microsoft.com/download | Permita downloads a partir do download da Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Ligue-se aos URLs de serviço Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Usado para migração sem agente VMware**<br/><br/> Ligue-se aos URLs de serviço Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.
*.applicationinsights.us | Faça upload de registos de aplicativos utilizados para monitorização interna.





## <a name="collected-data---vmware"></a>Dados recolhidos - VMware

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise de dependência](concepts-dependency-visualization.md) sem agente).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para a Azure, máquinas e aplicações de tamanho certo, planos de custos e analisar as dependências das aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa de metadados VMware VM que o aparelho recolhe e envia para a Azure.

**DADOS** | **CONTADOR**
--- | --- 
**Detalhes da máquina** | 
ID da VM | vm.Config. InstânciaUida 
o nome da VM | vm.Config. Nome
VCenter Server ID | VMwareClient.Instance.Uuid
Descrição do VM | vm.Summary.Config. Anotação
Nome do produto da licença | vm. Cliente.ServiceContent.About.LicenseProductName
Tipo de sistema operativo | vm. ResumoConfig.GuestFullName
Tipo de bota | vm.Config. Firmware
Número de núcleos | vm.Config. Hardware.NumCPU
Memória (MB) | vm.Config. Hardware.MemoryMB
Número de discos | vm.Config. Hardware.Device.ToList(). FindAll(x => é VirtualDisk).contagem
Lista de tamanhos de disco | vm.Config. Hardware.Device.ToList(). FindAll(x => é VirtualDisk)
Lista de adaptadores de rede | vm.Config. Hardware.Device.ToList(). FindAll(x => é VirtualEthernet).contagem
Utilização da CPU | cpu.usage.average
Utilização da memória |mem.usage.média
**Por detalhes de disco** | 
Valor chave do disco | disco. Chave
Número dikunit | disco. UnitNumber
Valor chave do controlador de disco | disco. ControladorKey.Value
Gigabytes a provisionados | virtualDisk.DeviceInfo.Resumo
Nome do disco | Valor gerado através do disco. UnitNumber, disco. Chave, disco. ControladorKey.VAlue
Ler operações por segundo | virtualDisk.numberReadAveraged.average
Escrever operações por segundo | virtualDisk.numberWriteAveraged.average
Ler a produção (MB por segundo) | virtualDisk.read.average
Produção de escrita (MB por segundo) | virtualDisk.write.average
**Detalhes de NIC** | 
Nome do adaptador de rede | nic. Chave
Endereço MAC | (VirtualEthernetCard)nic). MacAddress
Endereços IPv4 | vm. Guest.Net
Endereços IPv6 | vm. Guest.Net
Ler a produção (MB por segundo) | net.received.average
Produção de escrita (MB por segundo) | net.transmitted.average
**Detalhes do caminho do inventário** | 
Nome | contentor. GetType(). Nome
Tipo de objeto de criança | contentor. ChildType
Detalhes de referência | contentor. Rio MoRef
Detalhes dos pais | Contentor.Pai
Detalhes da pasta por VM | ((Pasta)recipiente). ChildEntity.Type
Detalhes do datacenter por VM | ((Datacenter)recipiente). VmFolder
Detalhes do Datacenter por pasta de anfitrião | ((Datacenter)recipiente). HostFolder
Detalhes do cluster por anfitrião | ((ClusterComputeResource)container). Anfitrião
Detalhes do anfitrião por VM | ((HostSystem)container). VM

### <a name="performance-data"></a>Dados de desempenho


Aqui estão os dados de desempenho vMware VM que o aparelho recolhe e envia para Azure.

**Dados** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Utilização da CPU | cpu.usage.average | Tamanho/custo VM recomendado
Utilização da memória | mem.usage.média | Tamanho/custo VM recomendado
Produção de leitura de disco (MB por segundo) | virtualDisk.read.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Disco escreve produção (MB por segundo) | virtualDisk.write.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Operações de leitura de disco por segundo | virtualDisk.numberReadAveraged.average | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Disco escreve operações por segundo | virtualDisk.numberWriteAveraged.average  | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Produção de leitura de NIC (MB por segundo) | net.received.average | Cálculo para o tamanho de VM
NIC escreve produção (MB por segundo) | net.transmitted.average  |Cálculo para o tamanho de VM


### <a name="installed-apps-metadata"></a>Metadados de aplicações instalados

A descoberta de aplicações recolhe aplicações instaladas e dados do sistema operativo.

#### <a name="windows-vm-apps-data"></a>Dados de aplicações do Windows VM

Aqui estão os dados da aplicação instalados que o aparelho recolhe de cada VM habilitado para a descoberta da aplicação. Estes dados são enviados para a Azure.

**Dados** | **Localização do registo** | **Chave**
--- | --- | ---
Nome da Aplicação  | HKLM:\Software\Microsoft\Microsoft\Windows\CurrentVersion\Desinstalar\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Unnstall\*  | DisplayName
Versão  | HKLM:\Software\Microsoft\Microsoft\Windows\CurrentVersion\Desinstalar\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Unnstall\*  | ExibiçãoVersão 
Fornecedor  | HKLM:\Software\Microsoft\Microsoft\Windows\CurrentVersion\Desinstalar\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Unnstall\*  | Publisher

#### <a name="windows-vm-features-data"></a>Windows VM apresenta dados

Aqui estão os dados das funcionalidades que o aparelho recolhe de cada VM habilitado para a descoberta da aplicação. Estes dados são enviados para a Azure.

**Dados**  | **Cmdlet PowerShell** | **Propriedade**
--- | --- | ---
Nome  | Get-WindowsFeature  | Nome
Tipo de Recurso | Get-WindowsFeature  | CaracterísticaType
Principal  | Get-WindowsFeature  | Principal

#### <a name="windows-vm-sql-server-metadata"></a>Metadados do Servidor SQL do Windows VM

Aqui está os metadados do servidor SQL que o aparelho recolhe a partir de VMs que executam o servidor Microsoft SQL habilitado para a descoberta de aplicações. Estes dados são enviados para a Azure.

**Dados**  | **Localização do registo**  | **Chave**
--- | --- | ---
Nome  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server\Instance Names\SQL  | instaladoInstance
Edição  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Edição 
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | SP
Versão  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Versão 

#### <a name="windows-vm-operating-system-data"></a>Dados do sistema operativo Windows VM

Aqui estão os dados do sistema operativo que o aparelho recolhe cada VM habilitado para a descoberta da aplicação. Estes dados são enviados para a Azure.

Dados  | Classe WMI  | Propriedade classe WMI
--- | --- | ---
Nome  | Win32_operatingsystem  | Legenda
Versão  | Win32_operatingsystem  | Versão
Arquitetura  | Win32_operatingsystem  | OsArchitecture

#### <a name="linux-vm-apps-data"></a>Dados de aplicações Linux VM

Aqui estão os dados da aplicação instalados que o aparelho recolhe de cada VM habilitado para a descoberta da aplicação. Com base no sistema operativo do VM, um ou mais dos comandos são executados . Estes dados são enviados para a Azure.

Dados  | Comando
--- | --- 
Nome | rpm, dpkg-consulta, estalar
Versão | rpm, dpkg-consulta, estalar
Fornecedor | rpm, dpkg-consulta, estalar

#### <a name="linux-vm-operating-system-data"></a>Dados do sistema operativo Linux VM

Aqui estão os dados do sistema operativo que o aparelho recolhe cada VM habilitado para a descoberta da aplicação. Estes dados são enviados para a Azure.

**Dados**  | **Comando** 
--- | --- | ---
Nome <br/> versão | Recolhidos a partir de um ou mais dos seguintes ficheiros:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oráculo-liberação  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Arquitetura | uname


### <a name="app-dependencies-metadata"></a>Metadados de dependências de aplicativos

A análise da dependência de agentes recolhe dados de conexão e processo.

#### <a name="windows-vm-app-dependencies-data"></a>Dados de dependências de aplicações do Windows VM

Aqui estão os dados de ligação que o aparelho recolhe de cada VM habilitado para análise de dependência sem agentes. Estes dados são enviados para a Azure.

**Dados** | **Comando utilizado** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado de ligação TCP | netstat
ID de Processo | netstat
Número de ligações ativas | netstat


Aqui estão os dados do processo que o aparelho recolhe de cada VM habilitado para análise de dependência sem agentes. Estes dados são enviados para a Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | Caminho executável
Argumentos de processo | Win32_Process | Linha de Comando
Nome da aplicação | Win32_Process | VersionInfo.ProductName parâmetro da propriedade ExecutablePath

#### <a name="linux-vm-app-dependencies-data"></a>Dados de dependências de aplicativos Linux VM

Aqui estão os dados de ligação e processo que o aparelho recolhe de cada VM Linux habilitado para análise de dependência sem agente. Estes dados são enviados para a Azure.

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

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise de dependência](concepts-dependency-visualization.md) sem agente).

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para a Azure, máquinas e aplicações de tamanho certo, planos de custos e analisar as dependências das aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa de metadados Hiper-V VM que o aparelho recolhe e envia para Azure.

**DADOS** | **CLASSE WMI** | **PROPRIEDADE CLASSE WMI**
--- | --- | ---
**Detalhes da máquina** | 
Número de série de BIOS _ Msvm_BIOSElement | BiosserialNumber
Tipo VM (Gen 1 ou 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome de exibição VM | Msvm_VirtualSystemSettingData | ElementName
Versão VM | Msvm_ProcessorSettingData | VirtualQuantity
Memória (bytes) | Msvm_MemorySettingData | VirtualQuantity
Memória máxima que pode ser consumida por VM | Msvm_MemorySettingData | Limite
Memória dinâmica ativada | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versão/FQDN do sistema operativo | Msvm_KvpExchangeComponent | Dados de nomes de GuestIntrinsicExchangeItems
Estado de potência VM | Msvm_ComputerSystem | EnabledState
**Por detalhes de disco** | 
Identificador de disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo de disco rígido virtual | Msvm_VirtualHardDiskSettingData | Tipo
Tamanho do disco rígido virtual | Msvm_VirtualHardDiskSettingData | MaxInternalsize
Pai de disco rígido virtual | Msvm_VirtualHardDiskSettingData | ParentPath
**Detalhes de NIC** | 
Endereços IP (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP ativado (NICs sintéticos) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Endereço NIC MAC (NICs sintéticos) | Msvm_SyntheticEthernetPortSettingData | Endereço
NIC ID (NICs legados) | Dados msvmEmuladosEthernetPortSetting | InstanceID
NIC MAC ID (NICs legados) | Dados msvmEmuladosEthernetPortSetting | Endereço

### <a name="performance-data"></a>Dados de desempenho

Aqui estão os dados de desempenho do Hyper VM que o aparelho recolhe e envia para a Azure.

**Classe de contador de desempenho** | **Contador** | **Impacto da avaliação**
--- | --- | ---
Processador virtual hiper-V do hiperconselhante | % Tempo de execução do hóspede | Tamanho/custo VM recomendado
Memória Dinâmica Hiper-V V | Pressão corrente (%)<br/> Memória Física Visível do Hóspede (MB) | Tamanho/custo VM recomendado
Dispositivo de armazenamento virtual hiper-V | Ler Bytes/Segundo | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Dispositivo de armazenamento virtual hiper-V | Escrever Bytes/Segundo | Cálculo para o tamanho do disco, custo de armazenamento, tamanho VM
Adaptador de rede virtual Hiper-V | Bytes Recebidos/Segundo | Cálculo para o tamanho de VM
Adaptador de rede virtual Hiper-V | Bytes Enviados/Segundo | Cálculo para o tamanho de VM

- A utilização do CPU é a soma de toda a utilização, para todos os processadores virtuais ligados a um VM.
- A utilização da memória é (Pressão atual * Memória Física Visível do Hóspede) / 100.
- Os valores de utilização do disco e da rede são recolhidos nos contadores de desempenho do Hyper-V listados.


## <a name="collected-data---physical"></a>Dados recolhidos - Físico

O aparelho recolhe metadados, dados de desempenho e dados de análise de dependência (se for utilizada [uma análise de dependência](concepts-dependency-visualization.md) sem agente).

### <a name="windows-metadata"></a>Metadados do Windows

Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se as máquinas e aplicações estão prontas para a migração para a Azure, máquinas e aplicações de tamanho certo, planos de custos e analisar as dependências das aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa dos metadados do servidor do Windows que o aparelho recolhe e envia para o Azure.

**DADOS** | **CLASSE WMI** | **PROPRIEDADE CLASSE WMI**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domínio, Nome, PartOfDomain
Contagem de núcleos do processador | Win32_PRocessor | NúmeroOfCores
Memória alocada | Win32_ComputerSystem | TotalPhysicalMemory
Número de série BIOS | Win32_ComputerSystemProduct | Identificação Número
GUIA DE BIOS | Win32_ComputerSystemProduct | UUID
Tipo de bota | Win32_DiskPartition | Verifique a partição com o Tipo = **GPT:Sistema** de EFI/BIOS
Nome do SO | Win32_OperatingSystem | Legenda
Versão do SO |Win32_OperatingSystem | Versão
Arquitetura de SO | Win32_OperatingSystem | OsArchitecture
Contagem de discos | Win32_DiskDrive | Modelo, Tamanho, DeviceID, MediaType, Nome
Tamanho do disco | Win32_DiskDrive | Tamanho
Lista DE NIC | Win32_NetworkAdapterConfiguration | Descrição, Índice
Endereço IP NIC | Win32_NetworkAdapterConfiguration | IPAddress
Endereço NIC MAC | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Metadados Linux

Aqui está a lista completa dos metadados do servidor Linux que o aparelho recolhe e envia para a Azure.

**DADOS** | **LINUX** 
--- | --- 
FQDN | gato /proc/sys/kernel/hostname, nome de anfitrião -f
Contagem de núcleos do processador |  /proc/cpuinfo \| awk '/^processador/{print $3}' \| wc -l
Memória alocada | gato /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Número de série BIOS | Lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="; $2="; impressão}'
GUIA DE BIOS | gato /sys/class/dmi/id/product_uuid
Tipo de bota | [ d /sys/firmware/efi ] && eco EFI \| \| echo BIOS
Nome/versão do SO | Acedemos a estes ficheiros para a versão e nome do SO:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oráculo-liberação<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Arquitetura de SO | Uname-m
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| corte -f1 -d ':'
Disco de arranque | df /boot \| sed -n 2p \| awk '{print $1}'
Tamanho do disco | fdisk -l \| egrep 'Disk.*bytes' \| egreps $disk: \| awk '{print $5}'
Lista DE NIC | ip -o -4 addr show \| awk '{print $2}'
Endereço IP NIC | ip addr show $nic \| grep inet \| awk '{print $2}' \| corte -f1 -d "/" 
Endereço NIC MAC | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Dados de desempenho do Windows

Aqui estão os dados de desempenho do servidor do Windows que o aparelho recolhe e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
Utilização da CPU | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Utilização de memória | Win32_PerfFormattedData_PerfOS_Memory | DisponívelMBytes
Contagem de NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface | Faça a contagem do dispositivo de rede.
Dados recebidos por NIC | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dados transmitidos por NIC | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Contagem de discos | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Contagem de discos
Detalhes do disco | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dados de desempenho do Linux

Aqui estão os dados de desempenho do servidor Linux que o aparelho recolhe e envia para a Azure.

**Dados** | **Linux** 
--- | --- 
Utilização da CPU | gato /proc/stat/| grep 'cpu' /proc/stat
Utilização de memória | grep mem grátis \| \| awk '{print $3/$2 * 100,0}'
Contagem de NIC | Lshw -classe rede \| grep eth[0-60] \| wc -l
Dados recebidos por NIC | gato /sys/class/net/eth$nic/statistics/rx_bytes
Dados transmitidos por NIC | gato /sys/class/net/eth$nic/statistics/tx_bytes
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| corte -f1 -d ':'
Detalhes do disco | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os agentes Azure Migrate que estão a trabalhar no aparelho são atualizados. Isto acontece automaticamente, porque a atualização automática é ativada no aparelho por predefinição. Pode alterar esta definição predefinida para atualizar manualmente os serviços do aparelho.

### <a name="turn-off-auto-update"></a>Desativar a atualização automática

1. Na máquina que funciona o aparelho, abra o Editor de Registos.
2. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance. **
3. Para desativar a atualização automática, crie uma chave de registo **AutoUpdate** com valor DWORD de 0.

    ![Definir chave de registo](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Ligue a atualização automática

Pode ligar a atualização automática utilizando qualquer um destes métodos:

- Eliminando a chave de registo AutoUpdate de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Clique em **Ver os serviços** de aparelhos a partir das últimas verificações de atualização no painel **de pré-requisitos configurar** para ligar automaticamente.

Para eliminar a chave de registo:

1. Na máquina que funciona o aparelho, abra o Editor de Registos.
2. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance. **
3. Elimine a chave de registo **AutoUpdate,** que foi previamente criada para desligar a atualização automática.

Para ligar a partir do Gestor de Configuração do Aparelho, após a descoberta estar concluída:

1. No gestor de configuração do aparelho, vá ao Configurar painel **pré-requisitos**
2. Na verificação das atualizações mais recentes, clique nos **serviços do aparelho ver** e clique no link para ligar a atualização automática.

    ![Ligue as atualizações automáticas](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Verifique a versão do serviço do aparelho

Pode consultar a versão do serviço do aparelho utilizando qualquer um destes métodos:

- No gestor de configuração do aparelho, vá ao **painel de pré-requisitos de configuração.**
- Na máquina do aparelho, nos Programas e Funcionalidades **do Painel de**  >  **Controlo**.

Para verificar no gestor de configuração do aparelho:

1. No gestor de configuração do aparelho, vá ao Configurar painel **pré-requisitos**
2. Nas últimas atualizações, clique nos **serviços de aparelhos Ver**.

    ![Ver versão](./media/migrate-appliance/versions.png)

Para verificar no Painel de Controlo:

1. No aparelho, clique em **Iniciar**Programas e  >  **Control Panel**  >  **Funcionalidades** do Painel de Controlo
2. Verifique as versões de serviços do aparelho na lista.

    ![Ver versão no Painel de Controlo](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Atualizar manualmente uma versão mais antiga

Se estiver a executar uma versão mais antiga para qualquer um dos componentes, tem de desinstalar o serviço e atualizar manualmente para a versão mais recente.

1. Para verificar as versões mais recentes do serviço de aparelhos, [descarregue](https://aka.ms/latestapplianceservices) o LatestComponents.jsno ficheiro.
2.    Depois de descarregar, abra a LatestComponents.jsno ficheiro no Bloco de Notas.
3. Encontre a versão de serviço mais recente no ficheiro e o link de descarregamento para o mesmo. Por exemplo:

    "Nome": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi " Versão": "6.0.211.2", "Md5Hash": "e00a742acc35e78a6a6a81e75469b84"

4.    Descarregue a versão mais recente de um serviço desatualizado, utilizando o link de descarregamento no ficheiro.
5. Após o download, execute o seguinte comando numa janela de comando do administrador, para verificar a integridade do MSI descarregado.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Por exemplo: C: \> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Verifique se a saída do comando corresponde à entrada de valor hash para o serviço no ficheiro (por exemplo, o valor do hash MD5 acima).
6. Agora, executar o MSI para instalar o serviço. É uma instalação silenciosa, e a janela de instalação fecha depois de ter terminado.
7. Após a instalação estar concluída, **Control panel**verifique a versão do serviço em  >  **Programas e Funcionalidades**do Painel de Controlo . A versão de serviço deve agora ser atualizada para as mais recentes mostradas no ficheiro JSON.



## <a name="next-steps"></a>Passos seguintes

- [Saiba como](how-to-set-up-appliance-vmware.md) configurar o aparelho para VMware.
- [Saiba como](how-to-set-up-appliance-hyper-v.md) configurar o aparelho para o Hyper-V.
- [Saiba como](how-to-set-up-appliance-physical.md) configurar o aparelho para servidores físicos.

