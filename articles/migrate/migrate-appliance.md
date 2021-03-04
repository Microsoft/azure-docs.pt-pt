---
title: Aplicação do Azure Migrate
description: Fornece um resumo do suporte para o aparelho Azure Migrate.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 08cd0e9d33dd88b9bdc418f3d1bbd382b2d80632
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038769"
---
# <a name="azure-migrate-appliance"></a>Aplicação do Azure Migrate

Este artigo resume os pré-requisitos e requisitos de suporte para o aparelho Azure Migrate.

## <a name="deployment-scenarios"></a>Cenários de implementação

O aparelho Azure Migrate é utilizado nos seguintes cenários.

**Cenário** | **Ferramenta** | **Costumava**
--- | --- | ---
**Descoberta e avaliação de servidores em execução em ambiente VMware** | Azure Migrate:Avaliação do servidor | Descubra servidores em execução no seu ambiente VMware<br/><br/> Realize a descoberta de aplicações instaladas, análise de dependência sem agentes e descubra instâncias e bases de dados do SQL Server.<br/><br/> Colete a configuração do servidor e metadados de desempenho para avaliações.
**Migração sem agente de servidores em execução em ambiente VMware** | Azure Migrate:Migração de servidores | Descubra os servidores em execução no seu ambiente VMware. <br/><br/> Replicar servidores sem instalar quaisquer agentes neles.
**Descoberta e avaliação de servidores em execução em ambiente Hiper-V** | Azure Migrate:Avaliação do servidor | Descubra servidores em execução no seu ambiente Hiper-V.<br/><br/> Colete a configuração do servidor e metadados de desempenho para avaliações.
**Descoberta e avaliação de servidores físicos ou virtualizados no local** |  Azure Migrate:Avaliação do servidor |  Descubra servidores físicos ou virtualizados no local.<br/><br/> Colete a configuração do servidor e metadados de desempenho para avaliações.

## <a name="deployment-methods"></a>Métodos de implantação

O aparelho pode ser implantado utilizando um par de métodos:

- O aparelho pode ser implantado utilizando um modelo para servidores em execução em ambiente VMware ou Hiper-V[(modelo OVA para VMware](how-to-set-up-appliance-vmware.md) ou [VHD para Hiper-V](how-to-set-up-appliance-hyper-v.md)).
- Se não quiser utilizar um modelo, pode implantar o aparelho para ambiente VMware ou Hiper-V utilizando um [script instalador PowerShell](deploy-appliance-script.md).
- No Governo Azure, deve utilizar o aparelho utilizando um script instalador PowerShell. Consulte os passos de implantação [aqui.](deploy-appliance-script-government.md)
- Para servidores físicos ou virtualizados no local ou em qualquer outra nuvem, utilize sempre o aparelho utilizando um script instalador PowerShell. Consulte os passos de implantação [aqui.](how-to-set-up-appliance-physical.md)
- Os links de descarregamento estão disponíveis nas tabelas abaixo.


## <a name="appliance---vmware"></a>Aparelho - VMware

A tabela que se segue resume os requisitos do aparelho Azure Migrate para o VMware.

> [!Note]
> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **leste da Austrália.** Se já tem um projeto na Austrália East e quer experimentar esta funcionalidade, certifique-se de que completou estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

**Requisito** | **VMware** 
--- | ---
**Permissões** | Para aceder ao gestor de configuração do aparelho local ou remotamente, é necessário ter uma conta de utilizador local ou de domínio com privilégios administrativos no servidor do aparelho.
**Serviços de eletrodomés** | O aparelho dispõe dos seguintes serviços:<br/><br/> - **Gestor de configuração do aparelho**: Esta é uma aplicação web que pode ser configurada com detalhes de origem para iniciar a descoberta e avaliação dos servidores.<br/> - **VMware discovery agent**: O agente recolhe metadados de configuração do servidor que podem ser usados para criar como avaliações no local.<br/>- **Agente de avaliação VMware**: O agente recolhe metadados de desempenho do servidor que podem ser utilizados para criar avaliações baseadas no desempenho.<br/>- **Serviço de atualização automática**: O serviço mantém todos os agentes a trabalhar no aparelho atualizados. Funciona automaticamente uma vez a cada 24 horas.<br/>- **Agente DRA**: Orquestra a replicação do servidor e coordena a comunicação entre servidores replicados e Azure. Usado apenas para replicar servidores para Azure usando migração sem agente.<br/>- **Gateway**: Envia dados replicados para Azure. Usado apenas para replicar servidores para Azure usando migração sem agente.<br/>- **SQL discovery and assessment agent**: envia os metadados de configuração e desempenho de instâncias e bases de dados do SQL Server para O Azure.
**Limites do projeto** |  Um aparelho só pode ser registado com um único projeto.<br/> Um único projeto pode ter vários aparelhos registados.
**Limites de descoberta** | Um aparelho pode descobrir até 10.000 servidores em execução num servidor vCenter.<br/> Um aparelho pode ligar-se a um único servidor vCenter.
**Implantação suportada** | Implemente como novo servidor em execução no vCenter Server utilizando o modelo OVA.<br/><br/> Implemente num servidor existente que executa o Windows Server 2016 utilizando o script do instalador PowerShell.
**Modelo de OVA** | Download do projeto ou a partir [daqui](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> O tamanho do download é de 11,9 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias.<br/>Se o período de avaliação estiver próximo de expirar, recomendamos que descarregue e implemente um novo aparelho utilizando o modelo OVA, ou que ative a licença do sistema operativo do servidor do aparelho.
**Verificação do OVA** | [Verifique](tutorial-discover-vmware.md#verify-security) o modelo OVA descarregado do projeto verificando os valores do haxixe.
**Script do PowerShell** | Consulte este [artigo](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) sobre como implantar um aparelho utilizando o script do instalador PowerShell.<br/><br/> 
**Requisitos de hardware e rede** |  O aparelho deve funcionar no servidor com o Windows Server 2016, 32 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho requer acesso à Internet, diretamente ou através de um representante.<br/><br/> Se implementar o aparelho utilizando o modelo OVA, necessita de recursos suficientes no servidor vCenter para criar um servidor que satisfaça os requisitos de hardware.<br/><br/> Se executar o aparelho num servidor existente, certifique-se de que está a executar o Windows Server 2016 e que cumpre os requisitos de hardware.<br/>_(Atualmente, a implementação do aparelho só é suportada no Windows Server 2016.)_
**Requisitos de VMware** | Se implementar o aparelho como servidor no vCenter Server, este deve ser implantado num servidor vCenter que funciona 5.5, 6.0, 6.5 ou 6.7 e um anfitrião ESXi a correr versão 5.5 ou posterior.<br/><br/> 
**VDDK (migração sem agente)** | Para alavancar o aparelho para a migração sem agentes dos servidores, o VMware vSphere VDDK deve ser instalado no servidor do aparelho.

## <a name="appliance---hyper-v"></a>Aparelho - Hiper-V

**Requisito** | **Hyper-V** 
--- | ---
**Permissões** | Para aceder ao gestor de configuração do aparelho local ou remotamente, é necessário ter uma conta de utilizador local ou de domínio com privilégios administrativos no servidor do aparelho.
**Serviços de eletrodomés** | O aparelho dispõe dos seguintes serviços:<br/><br/> - **Gestor de configuração do aparelho**: Esta é uma aplicação web que pode ser configurada com detalhes de origem para iniciar a descoberta e avaliação dos servidores.<br/> - **Agente de** descoberta : O agente recolhe metadados de configuração do servidor que podem ser usados para criar como avaliações no local.<br/>- **Agente de avaliação**: O agente recolhe metadados de desempenho do servidor que podem ser utilizados para criar avaliações baseadas no desempenho.<br/>- **Serviço de atualização automática**: O serviço mantém todos os agentes a trabalhar no aparelho atualizados. Funciona automaticamente uma vez a cada 24 horas.
**Limites do projeto** |  Um aparelho só pode ser registado com um único projeto.<br/> Um único projeto pode ter vários aparelhos registados.
**Limites de descoberta** | Um aparelho pode descobrir até 5000 servidores em funcionamento em ambiente Hiper-V.<br/> Um aparelho pode ligar até 300 anfitriões Hiper-V.
**Implantação suportada** | Implemente como servidor em execução num anfitrião Hiper-V usando um modelo VHD.<br/><br/> Implemente num servidor existente que executa o Windows Server 2016 utilizando o script do instalador PowerShell.
**Modelo VHD** | Ficheiro zip que inclui um VHD. Descarregue a partir de projeto ou a partir [daqui.](https://go.microsoft.com/fwlink/?linkid=2140422)<br/><br/> O tamanho do download é de 8.91 GB.<br/><br/> O modelo de aparelho descarregado vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias.<br/> Se o período de avaliação estiver próximo de expirar, recomendamos que descarregue e implemente um novo aparelho, ou que ative a licença do sistema operativo do servidor do aparelho.
**Verificação VHD** | [Verifique](tutorial-discover-hyper-v.md#verify-security) o modelo VHD descarregado do projeto verificando os valores do haxixe.
**Script do PowerShell** | Consulte este [artigo](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) sobre como implantar um aparelho utilizando o script do instalador PowerShell.<br/>
**Requisitos de hardware e rede**  |  O aparelho deve funcionar no servidor com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou através de um representante.<br/><br/> Se executar o aparelho como um servidor em execução num anfitrião Hiper-V, precisa de recursos suficientes no hospedeiro para criar um servidor que satisfaça os requisitos de hardware.<br/><br/> Se executar o aparelho num servidor existente, certifique-se de que está a executar o Windows Server 2016 e que cumpre os requisitos de hardware.<br/>_(Atualmente, a implementação do aparelho só é suportada no Windows Server 2016.)_
**Requisitos de hiper-V** | Se colocar o aparelho com o modelo VHD, o aparelho fornecido pela Azure Migrate é a versão 5.0 do Hiper-VM.<br/><br/> O anfitrião Hyper-V deve estar a executar o Windows Server 2012 R2 ou mais tarde.

## <a name="appliance---physical"></a>Aparelho - Físico

**Requisito** | **Físico** 
--- | ---
**Permissões** | Para aceder ao gestor de configuração do aparelho local ou remotamente, é necessário ter uma conta de utilizador local ou de domínio com privilégios administrativos no servidor do aparelho.
**Serviços de eletrodomés** | O aparelho dispõe dos seguintes serviços:<br/><br/> - **Gestor de configuração do aparelho**: Esta é uma aplicação web que pode ser configurada com detalhes de origem para iniciar a descoberta e avaliação dos servidores.<br/> - **Agente de** descoberta : O agente recolhe metadados de configuração do servidor que podem ser usados para criar como avaliações no local.<br/>- **Agente de avaliação**: O agente recolhe metadados de desempenho do servidor que podem ser utilizados para criar avaliações baseadas no desempenho.<br/>- **Serviço de atualização automática**: O serviço mantém todos os agentes a trabalhar no aparelho atualizados. Funciona automaticamente uma vez a cada 24 horas.
**Limites do projeto** |  Um aparelho só pode ser registado com um único projeto.<br/> Um único projeto pode ter vários aparelhos registados.<br/> 
**Limites de descoberta** | Um aparelho pode descobrir até 1000 servidores físicos.
**Implantação suportada** | Implemente num servidor existente que executa o Windows Server 2016 utilizando o script do instalador PowerShell.
**Script do PowerShell** | Descarregue o script (AzureMigrateInstaller.ps1) num ficheiro zip do projeto ou a partir [daqui.](https://go.microsoft.com/fwlink/?linkid=2140334) [Saiba mais](tutorial-discover-physical.md).<br/><br/> O tamanho do download é de 85,8 MB.
**Verificação do script** | [Verifique](tutorial-discover-physical.md#verify-security) o script do instalador PowerShell descarregado do projeto verificando os valores do haxixe.
**Requisitos de hardware e rede** |  O aparelho deve funcionar no servidor com o Windows Server 2016, 16 GB de RAM, 8 vCPUs, cerca de 80 GB de armazenamento de disco.<br/> O aparelho necessita de um endereço IP estático ou dinâmico e requer acesso à Internet, diretamente ou através de um representante.<br/><br/> Se executar o aparelho num servidor existente, certifique-se de que está a executar o Windows Server 2016 e que cumpre os requisitos de hardware.<br/>_(Atualmente, a implementação do aparelho só é suportada no Windows Server 2016.)_


## <a name="url-access"></a>Acesso a URL

O aparelho Azure Migrate necessita de conectividade com a internet.

- Quando coloca o aparelho, a Azure Migrate faz uma verificação de conectividade para os URLs necessários.
- Tem de permitir o acesso a todos os URLs da lista. Se estiver a fazer apenas avaliações, pode saltar os URLs que estão marcados como necessário para a migração sem agente VMware.
-  Se estiver a utilizar um representante baseado em URL para se ligar à internet, certifique-se de que o representante resolve quaisquer registos CNAME recebidos enquanto procura os URLs.

### <a name="public-cloud-urls"></a>URLs de nuvem pública

**URL** | **Detalhes**  
--- | --- |
*.portal.azure.com  | Navegue para o portal do Azure.
\*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Inscreva-se na sua assinatura Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Crie aplicativos Azure Ative Directory (AD) para o aparelho comunicar com a Azure Migrate.
management.azure.com | Crie aplicativos AD AZure para o aparelho comunicar com o Azure Migrate.
*.services.visualstudio.com | Faça o upload dos registos do aparelho utilizados para a monitorização interna.
*.vault.azure.net | Gerir segredos no Cofre da Chave Azure.<br/> Nota: Certifique-se de que os servidores para replicar têm acesso a isto.
aka.ms/* | Permitir o acesso a links aka; usado para descarregar e instalar as últimas atualizações para serviços de aparelhos.
download.microsoft.com/download | Permitir downloads a partir do centro de descarregamento da Microsoft.
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
*.services.visualstudio.com | Faça o upload dos registos do aparelho utilizados para a monitorização interna.
*.vault.usgovcloudapi.net | Gerir segredos no Cofre da Chave Azure.
aka.ms/* | Permitir o acesso a links aka; usado para descarregar e instalar as últimas atualizações para serviços de aparelhos.
download.microsoft.com/download | Permitir downloads a partir do centro de descarregamento da Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicação entre o aparelho e o serviço Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Ligue-se aos URLs de serviço Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Usado para migração sem agente VMware**<br/><br/> Ligue-se aos URLs de serviço Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Usado para migração sem agente VMware**<br/><br/>Faça upload de dados para armazenamento para migração.
*.applicationinsights.us | Faça o upload dos registos do aparelho utilizados para a monitorização interna.


## <a name="collected-data---vmware"></a>Dados recolhidos - VMware

O aparelho recolhe metadados de configuração, metadados de desempenho e dados de dependências do servidor (se for utilizada [uma análise de dependência](concepts-dependency-visualization.md) sem agente).

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se os servidores estão prontos para a migração para a Azure, servidores de tamanho certo, custos de planos e análise de dependências de aplicações. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa de metadados do servidor que o aparelho recolhe e envia para o Azure.

**DADOS** | **CONTADOR**
--- | --- 
**Detalhes do servidor** | 
ID do servidor | vm.Config. InstânciaUida 
Nome do servidor | vm.Config. Nome
VCenter Server ID | VMwareClient.Instance.Uuid
Descrição do VM | vm.Summary.Config. Anotação
Nome do produto da licença | vm.Client.ServiceContent.About.LicenseProductName
Tipo de sistema operativo | vm.SummaryConfig.GuestFullName
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
Endereços IPv4 | vm.Guest.Net
Endereços IPv6 | vm.Guest.Net
Ler a produção (MB por segundo) | net.received.average
Produção de escrita (MB por segundo) | net.transmitted.average
**Detalhes do caminho do inventário** | 
Name | contentor. GetType(). Nome
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


### <a name="installed-applications-data"></a>Dados de aplicações instalados

O aparelho recolhe dados sobre aplicações, funções e funcionalidades instaladas nos servidores.

#### <a name="windows-server-application-data"></a>Dados da aplicação do servidor do Windows

Aqui estão os dados das aplicações que o aparelho recolhe de cada servidor Windows descoberto no seu ambiente VMware.

**Dados** | **Localização do registo** | **Chave**
--- | --- | ---
Nome da Aplicação  | HKLM:\Software\Microsoft\Microsoft\Windows\CurrentVersion\Desinstalar\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Unnstall\*  | DisplayName
Versão  | HKLM:\Software\Microsoft\Microsoft\Windows\CurrentVersion\Desinstalar\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Unnstall\*  | ExibiçãoVersão 
Fornecedor  | HKLM:\Software\Microsoft\Microsoft\Windows\CurrentVersion\Desinstalar\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Unnstall\*  | Publisher

#### <a name="windows-server-features-data"></a>O servidor do Windows apresenta dados

Aqui estão os dados de funcionalidades que o aparelho recolhe de cada servidor windows descoberto no seu ambiente VMware.

**Dados**  | **Cmdlet PowerShell** | **Propriedade**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Tipo de Recurso | Get-WindowsFeature  | CaracterísticaType
Principal  | Get-WindowsFeature  | Principal

#### <a name="sql-server-metadata"></a>Metadados do Servidor SQL

Aqui estão os dados do SQL Server que o aparelho recolhe de cada servidor Windows descoberto no seu ambiente VMware.

**Dados**  | **Localização do registo**  | **Chave**
--- | --- | ---
Name  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server\Instance Names\SQL  | instaladoInstance
Edição  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Edição 
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | SP
Versão  | HKLM:\SOFTWARE\Microsoft\Microsoft\Microsoft SQL Server \\ \<InstanceName> \Setup  | Versão 

#### <a name="windows-server-operating-system-data"></a>Dados do sistema operativo do servidor do Windows

Aqui estão os dados do sistema operativo que o aparelho recolhe de cada servidor Windows descoberto no seu ambiente VMware.

**Dados**  | **Classe WMI**  | **Propriedade classe WMI**
--- | --- | ---
Name  | Win32_operatingsystem  | Legenda
Versão  | Win32_operatingsystem  | Versão
Arquitetura  | Win32_operatingsystem  | OsArchitecture

#### <a name="linux-server-application-data"></a>Dados da aplicação do servidor Linux

Aqui estão os dados das aplicações que o aparelho recolhe de cada servidor Linux descoberto no seu ambiente VMware. Com base no sistema operativo do servidor, um ou mais dos comandos são executados.

**Dados**  | **Comandos**
--- | --- 
Name | rpm, dpkg-consulta, estalar
Versão | rpm, dpkg-consulta, estalar
Fornecedor | rpm, dpkg-consulta, estalar

#### <a name="linux-server-operating-system-data"></a>Dados do sistema operativo do servidor Linux

Aqui estão os dados do sistema operativo que o aparelho recolhe de cada servidor Linux descoberto no seu ambiente VMware.

**Dados**  | **Comandos**
--- | --- 
Name <br/> versão | Recolhidos a partir de um ou mais dos seguintes ficheiros:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oráculo-liberação  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Arquitetura | uname

### <a name="sql-server-instances-and-databases-data"></a>SqL Server casos e dados de bases de dados

O aparelho recolhe dados sobre as instâncias e bases de dados do SQL Server.

> [!Note]
> A descoberta e avaliação de instâncias e bases de dados do SQL Server em execução no seu ambiente VMware está agora em pré-visualização. Para experimentar esta funcionalidade, utilize [**este link**](https://aka.ms/AzureMigrate/SQL) para criar um projeto na região **leste da Austrália.** Se já tem um projeto na Austrália East e quer experimentar esta funcionalidade, certifique-se de que completou estes [**pré-requisitos**](how-to-discover-sql-existing-project.md) no portal.

#### <a name="sql-database-metadata"></a>Metadados de base de dados SQL

**Metadados de base de dados** | **Vistas/ Propriedades do Servidor SQL**
--- | ---
Identificador único da base de dados | sys.databases
ID de base de dados definido pelo servidor | sys.databases
Nome da base de dados | sys.databases
Nível de compatibilidade da base de dados | sys.databases
Nome de colagem da base de dados | sys.databases
Estado da base de dados | sys.databases
Tamanho da base de dados (em MBs) | sys.master_files
Carta de unidade de localização contendo ficheiros de dados | SERVERPROPERTY e Software\Microsoft\MICROSOFT\MSSQLServer\MSSQLServer
Lista de ficheiros de bases de dados | sys.databases, sys.master_files
O corretor de serviços está ativado ou não | sys.databases
A base de dados está ativada para alterar a captura de dados ou não | sys.databases

#### <a name="sql-server-metadata"></a>Metadados do Servidor SQL

**Metadados do servidor** | **Vistas/ Propriedades do servidor SQL**
--- | ---
Nome do servidor |SERVERPROPERTY 
FQDN | Cadeia de ligação derivada da descoberta de aplicações instaladas
Instalar ID | sys.dm_server_registry
Versão do servidor | SERVERPROPERTY
Edição do servidor | SERVERPROPERTY
Plataforma de anfitrião do servidor (Windows/Linux) | SERVERPROPERTY
Nível de produto do servidor (RTM SP CTP) | SERVERPROPERTY 
Caminho de backup predefinido | SERVERPROPERTY
Caminho padrão dos ficheiros de dados | SERVERPROPERTY e Software\Microsoft\MICROSOFT\MSSQLServer\MSSQLServer
Caminho predefinido dos ficheiros de registo | SERVERPROPERTY e Software\Microsoft\MICROSOFT\MSSQLServer\MSSQLServer
N.º de núcleos no servidor | sys.dm_os_schedulers, sys.dm_os_sys_info
Nome de colagem de servidor | SERVERPROPERTY
N.º de núcleos no servidor com estado ONLINE VISÍVEL | sys.dm_os_schedulers
ID de servidor único | sys.dm_server_registry
HA ativado ou não | SERVERPROPERTY
Extensão do pool tampão ativada ou não | sys.dm_os_buffer_pool_extension_configuration
Aglomerado de failover configurado ou não | SERVERPROPERTY
Servidor apenas utilizando o modo de autenticação do Windows | SERVERPROPERTY 
Servidor instala PolyBase | SERVERPROPERTY 
N.º de CPUs lógicos no sistema | sys.dm_server_registry, sys.dm_os_sys_info
Relação do não dos núcleos lógicos ou físicos expostos por um pacote de processador físico | sys.dm_os_schedulers, sys.dm_os_sys_info
Não de CPUs físicos no sistema | sys.dm_os_schedulers, sys.dm_os_sys_info
O servidor de data e hora começou pela última vez | sys.dm_server_registry
Utilização da memória do servidor max (em MBs) | sys.dm_os_process_memory
Total não. de utilizadores em todas as bases de dados | sys.databases, sys.logins
Tamanho total de todas as bases de dados dos utilizadores | sys.databases
Tamanho da base de dados temporária | sys.master_files, sys.configurations, sys.dm_os_sys_info
N.º de logins | sys.logins
Lista de servidores ligados | sys.servidores
Lista de trabalho de agente | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. [syscategories]

### <a name="performance-metadata"></a>Metadados de desempenho

**Desempenho** | **Vistas/ Propriedades do servidor SQL** | **Impacto da Avaliação**
--- | --- | ---
Utilização do CPU do servidor SQL| sys.dm_os_ring_buffers| Tamanho SKU recomendado (dimensão CPU)
Contagem de CPU lógica SQL| sys.dm_os_sys_info| Tamanho SKU recomendado (dimensão CPU)
SQL memória física em uso| sys.dm_os_process_memory| Não é bem-de-suso
Percentagem de utilização da memória SQL| sys.dm_os_process_memory | Não é bem-de-suso
Utilização do CPU de base de dados| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Tamanho SKU recomendado (dimensão CPU)
Memória de base de dados em uso (piscina tampão)| sys.dm_os_buffer_descriptors| Tamanho SKU recomendado (dimensão da memória)
Leitura de ficheiros/IO de escrita| sys.dm_io_virtual_file_stats, sys.master_files| Tamanho SKU recomendado (dimensão IO)
Arquivar num de leituras/escritos| sys.dm_io_virtual_file_stats, sys.master_files| Tamanho SKU recomendado (dimensão de produção)
File IO stall read/write (ms)| sys.dm_io_virtual_file_stats, sys.master_files| Tamanho SKU recomendado (dimensão de latência IO)
Tamanho dos ficheiros| sys.master_files| Tamanho SKU recomendado (dimensão de armazenamento)


### <a name="application-dependency-data"></a>Dados de dependência da aplicação

A análise da dependência de agentes recolhe os dados de ligação e processo.

#### <a name="windows-server-dependencies-data"></a>Dados de dependências de servidores do Windows

Aqui estão os dados de ligação que o aparelho recolhe de cada servidor do Windows, habilitado para análise de dependência sem agentes.

**Dados** | **Comandos** 
--- | --- 
Porta local | netstat
Endereço IP local | netstat
Porta remota | netstat
Endereço IP remoto | netstat
Estado de ligação TCP | netstat
ID de Processo | netstat
Número de ligações ativas | netstat

Aqui estão os dados de ligação que o aparelho recolhe de cada servidor do Windows, habilitado para análise de dependência sem agentes.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
Nome do processo | Win32_Process | Caminho executável
Argumentos de processo | Win32_Process | Linha de Comando
Nome da aplicação | Win32_Process | VersionInfo.ProductName parâmetro da propriedade ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Dados de dependências de servidores Linux

Aqui estão os dados de ligação que o aparelho recolhe de cada servidor Linux, habilitado para análise de dependência sem agentes.

**Dados** | **Comandos** 
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

O aparelho recolhe metadados de configuração e desempenho de servidores em funcionamento em ambiente Hiper-V.

### <a name="metadata"></a>Metadados
Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se os servidores estão prontos para a migração para a Azure, servidores de tamanho certo e custos de planos. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

Aqui está a lista completa de metadados do servidor que o aparelho recolhe e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
--- | --- | ---
**Detalhes do servidor** | 
Número de série de BIOS | Msvm_BIOSElement | BiosserialNumber
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

Aqui estão os dados de desempenho do servidor que o aparelho recolhe e envia para o Azure.

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

O aparelho recolhe metadados de configuração e desempenho de servidores físicos ou virtuais que estão a funcionar no local.

### <a name="metadata"></a>Metadados

Os metadados descobertos pelo aparelho Azure Migrate ajudam-no a descobrir se os servidores estão prontos para a migração para a Azure, servidores de tamanho certo e custos de planos. A Microsoft não utiliza estes dados em nenhuma auditoria de conformidade com a licença.

### <a name="windows-server-metadata"></a>Metadados do servidor windows

Aqui está a lista completa dos metadados do servidor do Windows que o aparelho recolhe e envia para o Azure.

**Dados** | **Classe WMI** | **Propriedade classe WMI**
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

### <a name="linux-server-metadata"></a>Metadados de servidor Linux

Aqui está a lista completa dos metadados do servidor Linux que o aparelho recolhe e envia para a Azure.

**Dados** | **Comandos** 
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

**Dados** | **Comandos** 
--- | --- 
Utilização da CPU | gato /proc/stat/| grep 'cpu' /proc/stat
Utilização de memória | grep mem grátis \| \| awk '{print $3/$2 * 100,0}'
Contagem de NIC | Lshw -classe rede \| grep eth[0-60] \| wc -l
Dados recebidos por NIC | gato /sys/class/net/eth$nic/statistics/rx_bytes
Dados transmitidos por NIC | gato /sys/class/net/eth$nic/statistics/tx_bytes
Contagem de discos | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| corte -f1 -d ':'
Detalhes do disco | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado à medida que os serviços Azure Migrate que estão a funcionar no aparelho são atualizados. Isto acontece automaticamente, porque a atualização automática é ativada no aparelho por predefinição. Pode alterar esta definição predefinida para atualizar manualmente os serviços do aparelho.

### <a name="turn-off-auto-update"></a>Desativar a atualização automática

1. No servidor que executa o aparelho, abra o Editor de Registos.
2. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.**
3. Para desativar a atualização automática, crie uma chave de registo **AutoUpdate** com valor DWORD de 0.

    ![Definir chave de registo](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Ligue a atualização automática

Pode ligar a atualização automática utilizando qualquer um destes métodos:

- Eliminando a chave de registo AutoUpdate de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Clique em **Ver os serviços** de aparelhos a partir das últimas verificações de atualização no painel **de pré-requisitos configurar** para ligar automaticamente.

Para eliminar a chave de registo:

1. Na máquina que funciona o aparelho, abra o Editor de Registos.
2. Navegue até **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.**
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

1. No aparelho, clique em **Iniciar** Programas e  >    >  **Funcionalidades** do Painel de Controlo
2. Verifique as versões de serviços do aparelho na lista.

    ![Ver versão no Painel de Controlo](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Atualizar manualmente uma versão mais antiga

Se estiver a executar uma versão mais antiga para qualquer um dos serviços, tem de desinstalar o serviço e atualizar manualmente para a versão mais recente.

1. Para verificar as versões mais recentes do serviço de aparelhos, [descarregue](https://aka.ms/latestapplianceservices) o LatestComponents.jsno ficheiro.
2.    Depois de descarregar, abra a LatestComponents.jsno ficheiro no Bloco de Notas.
3. Encontre a versão de serviço mais recente no ficheiro e o link de descarregamento para o mesmo. Por exemplo:

    "Nome": "ASRMigrationWebApp", "DownloadLink": " https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi " Versão": "6.0.211.2", "Md5Hash": "e00a742acc35e78a6a6a81e75469b84"

4.    Descarregue a versão mais recente de um serviço desatualizado, utilizando o link de descarregamento no ficheiro.
5. Após o download, execute o seguinte comando numa janela de comando do administrador, para verificar a integridade do MSI descarregado.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Por exemplo: C: \> CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Verifique se a saída do comando corresponde à entrada de valor hash para o serviço no ficheiro (por exemplo, o valor do hash MD5 acima).
6. Agora, executar o MSI para instalar o serviço. É uma instalação silenciosa, e a janela de instalação fecha depois de ter terminado.
7. Após a instalação estar concluída, verifique a versão do serviço em  >  **Programas e Funcionalidades** do Painel de Controlo . A versão de serviço deve agora ser atualizada para as mais recentes mostradas no ficheiro JSON.



## <a name="next-steps"></a>Passos seguintes

- [Saiba como](how-to-set-up-appliance-vmware.md) configurar o aparelho para VMware.
- [Saiba como](how-to-set-up-appliance-hyper-v.md) configurar o aparelho para o Hyper-V.
- [Saiba como](how-to-set-up-appliance-physical.md) configurar o aparelho para servidores físicos.

