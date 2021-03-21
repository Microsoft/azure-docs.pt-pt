---
title: Criar um aparelho Azure Migrate no Governo de Azure
description: Saiba como instalar um aparelho Azure Migrate no Governo de Azure
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 6778c8e5e1e4fa83d34141fd13ba21d483ab76e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041353"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Montar um aparelho no Governo de Azure 

Siga este artigo para implementar um [aparelho Azure Migrate](./migrate-appliance-architecture.md) para VMware VMs, VMs hiper-V e servidores físicos, numa nuvem do Governo Azure. Você executar um script para criar o aparelho, e verificar se ele pode ligar-se ao Azure. Se pretender configurar um aparelho na nuvem pública, siga [este artigo](deploy-appliance-script.md).


> [!NOTE]
> A opção de implantar um aparelho utilizando um modelo (para VMware VMs e VMs hiper-V) não é suportada no Governo de Azure.


## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate numa máquina física ou VM existente.

- A máquina que funcionará como o aparelho deve estar a executar o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento de disco e um interruptor virtual externo. Requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de colocar o aparelho, reveja os requisitos detalhados do aparelho para [VMware VMs,](migrate-appliance.md#appliance---vmware) [Hiper-VMs](migrate-appliance.md#appliance---hyper-v)e [servidores físicos](migrate-appliance.md#appliance---physical).
- Não coloque o guião num aparelho Azure Migrate existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurar o aparelho para VMware

Para configurar o aparelho para VMware, descarregue um ficheiro com fecho do portal Azure e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2.  In **Discover machines**  >  **Are your machines virtualized?** 
3.  Clique **em Baixar**, para descarregar o ficheiro com fecho de correr. 


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Verifique a versão mais recente do aparelho e o valor do haxixe:

    **Algoritmo** | **Transferência** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195d7d7c83527a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Executar o script

Eis o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarrega e instala um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com definições persistentes para Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Config Ficheiros**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de** registo : %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia a lima com fecho para uma pasta na máquina que irá hospedar o aparelho. Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma: 
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o aparelho para Hiper-V

Para configurar o aparelho para Hiper-V, descarregue um ficheiro com fecho do portal Azure e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2.  In **Discover machines**  >  **Are your machines virtualized?** 
3.  Clique **em Baixar**, para descarregar o ficheiro com fecho de correr. 


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Verifique a versão mais recente do aparelho e o valor do haxixe:

    **Cenário** | **Transferência** | **SHA256**
    --- | --- | ---
    Hiper-V (85,8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311ded1d4a1167183a94e8347456db9c5749c7332ff2ff2eb4b77777778765e48

          

### <a name="run-the-script"></a>Executar o script

Eis o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarrega e instala um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com definições persistentes para Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Config Ficheiros**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de** registo : %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia a lima com fecho para uma pasta na máquina que irá hospedar o aparelho. Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurar o aparelho para servidores físicos

Para configurar o aparelho para VMware, descarregue um ficheiro com fecho do portal Azure e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2.  In **Discover machines**  >  **Are your machines virtualized?** 
3.  Clique **em Baixar**, para descarregar o ficheiro com fecho de correr. 


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Verifique a versão mais recente do aparelho e o valor do haxixe:

    **Cenário** | **Download** _ | _ *Valor do haxixe**
    --- | --- | ---
    Físico (85 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc350716bae30b257f
          

### <a name="run-the-script"></a>Executar o script

Eis o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarrega e instala um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com definições persistentes para Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Config Ficheiros**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de** registo : %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia a lima com fecho para uma pasta na máquina que irá hospedar o aparelho. Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Passos seguintes

Depois de colocar o aparelho, é necessário configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

- Configurar o aparelho para [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Ativar o aparelho para [Hiper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configurar o aparelho para [servidores físicos.](how-to-set-up-appliance-physical.md)
