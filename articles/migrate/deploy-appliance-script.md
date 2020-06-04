---
title: Configurar um aparelho Azure Migrate com um script
description: Saiba como configurar um aparelho Azure Migrate com um script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 20dbe4ba3b1b4858cb7022bb72129ee419ea2540
ms.sourcegitcommit: 79508e58c1f5c58554378497150ffd757d183f30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84331989"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um aparelho com um script

Siga este artigo para criar um [aparelho Azure Migrate](deploy-appliance.md) para a avaliação/migração de VMware VMs e Hiper-VMs. Você executou um script para criar um aparelho, e verificar se ele pode ligar-se ao Azure. 

Pode implantar o aparelho para VMware e Hiper-VMs utilizando um script ou utilizando um modelo que descarrega a partir do portal Azure. A utilização de um script é útil se não conseguir criar um VM utilizando o modelo descarregado.

- Para utilizar um modelo, siga os tutoriais para [VMware](tutorial-prepare-vmware.md) ou [Hyper-V](tutorial-prepare-hyper-v.md).
- Para configurar um aparelho para servidores físicos, só pode utilizar um script. Siga [este artigo.](how-to-set-up-appliance-physical.md)
- Para instalar um aparelho numa nuvem do Governo Azure, siga [este artigo.](deploy-appliance-script-government.md)

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
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Verifique a versão e o script mais recente do aparelho para a nuvem pública Azure:

    **Algoritmo** | **Transferir** | **SHA256**
    --- | --- | ---
    VMware (10,9 GB) | [Versão mais recente](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6b6ce7402b79f234bc0fe69663d



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
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

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
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifique a versão e o script mais recente do aparelho para a nuvem pública Azure:

    **Cenário** | **Transferir** | **SHA256**
    --- | --- | ---
    Hiper-V (8.93 MB) | [Versão mais recente](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa8658c950bc319bdbeb931bb93b440577264500091c846a1

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
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

## <a name="next-steps"></a>Próximos passos

Depois de colocar o aparelho, é necessário configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

- Configurar o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Ativar o aparelho para [Hiper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
