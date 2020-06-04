---
title: Criar um aparelho Azure Migrate no Governo de Azure
description: Saiba como instalar um aparelho Azure Migrate no Governo de Azure
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: a7a2e90b718c50cb86fdca9911e7535d434e7afa
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84332006"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Montar um aparelho no Governo de Azure 

Siga este artigo para implementar um [aparelho Azure Migrate](deploy-appliance.md) para VMware VMs, VMs hiper-V e servidores físicos, numa nuvem do Governo Azure. Você executar um script para criar o aparelho, e verificar se ele pode ligar-se ao Azure. Se pretender configurar um aparelho na nuvem pública, siga [este artigo](deploy-appliance-script.md).


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
2.  In **Discover machines**  >  **Are your machines virtualized?** **Yes, with VMWare vSphere hypervisor**
3.  Clique **em Baixar**, para descarregar o ficheiro com fecho de correr. 


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Verifique a versão mais recente do aparelho e o valor do haxixe:

    **Algoritmo** | **Transferir** | **SHA256**
    --- | --- | ---
    VMware (63.1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d582038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="run-the-script"></a>Executar o script

Eis o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarrega e instala um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com definições persistentes para Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Config Ficheiros**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de**registo : %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia a lima com fecho para uma pasta na máquina que irá hospedar o aparelho. Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o aparelho para Hiper-V

Para configurar o aparelho para Hiper-V, descarregue um ficheiro com fecho do portal Azure e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2.  In **Discover machines**  >  **Are your machines virtualized?** **Yes, with Hyper-V**
3.  Clique **em Baixar**, para descarregar o ficheiro com fecho de correr. 


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verifique a versão mais recente do aparelho e o valor do haxixe:

    **Cenário** | **Transferir** | **SHA256**
    --- | --- | ---
    Hiper-V (63,1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae4689408e43ab55ff397b77200b92121972e683f9aa3

          

### <a name="run-the-script"></a>Executar o script

Eis o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarrega e instala um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com definições persistentes para Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Config Ficheiros**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de**registo : %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia a lima com fecho para uma pasta na máquina que irá hospedar o aparelho. Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurar o aparelho para servidores físicos

Para configurar o aparelho para VMware, descarregue um ficheiro com fecho do portal Azure e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2.  In **Discover machines**  >  **Are your machines virtualized?** **Not virtualized/Other**
3.  Clique **em Baixar**, para descarregar o ficheiro com fecho de correr. 


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Verifique a versão mais recente do aparelho e o valor do haxixe:

    **Cenário** | **Baixar*** | **Valor de hash**
    --- | --- | ---
    Físico (63,1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cf3f9e1e61c9b37e1
          

### <a name="run-the-script"></a>Executar o script

Eis o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções Windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarrega e instala um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com definições persistentes para Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Config Ficheiros**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de**registo : %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraia a lima com fecho para uma pasta na máquina que irá hospedar o aparelho. Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell na máquina, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Próximos passos

Depois de colocar o aparelho, é necessário configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

- Configurar o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Ativar o aparelho para [Hiper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configurar o aparelho para [servidores físicos.](how-to-set-up-appliance-physical.md)
