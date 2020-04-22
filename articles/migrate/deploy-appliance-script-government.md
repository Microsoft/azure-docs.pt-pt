---
title: Criação de um aparelho Azure Migrate no Governo de Azure
description: Saiba como montar um aparelho Azure Migrate no Governo de Azure
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: fd97161ffa075a6165ea963ef80bfabf8904576e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726738"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Instale um aparelho no Governo de Azure 

Siga este artigo para implantar um [aparelho Azure Migrate](deploy-appliance.md) para VMware VMs, VMs Hiper-V e servidores físicos, numa nuvem do Governo Azure. Executa um guião para criar o aparelho e verifique se pode ligar-se ao Azure. Se quiser instalar um aparelho na nuvem pública, siga [este artigo](deploy-appliance-script.md).


> [!NOTE]
> A opção de implantar um aparelho utilizando um modelo (para VMware VMs e VMs Hiper-V) não é suportada no Governo Azure.


## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate numa máquina física ou VM existente.

- A máquina que funcionará como aparelho deve estar a executar o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um interruptor virtual externo. Requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de utilizar o aparelho, reveja os requisitos detalhados do aparelho para [VMware VMs,](migrate-appliance.md#appliance---vmware) [Hiper-V VMs](migrate-appliance.md#appliance---hyper-v)e [servidores físicos](migrate-appliance.md#appliance---physical).
- Não faça o guião num aparelho azure migratório existente.

## <a name="set-up-the-appliance-for-vmware"></a>Instale o aparelho para VMware

Para configurar o aparelho para VMware, faça o download de um ficheiro com fecho de correr do portal Azure e extrai o conteúdo. Executa o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2.  Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Yes, with VMWare vSphere hypervisor**
3.  Clique em **Baixar,** para descarregar o ficheiro zipped. 


### <a name="verify-file-security"></a>Verificar a segurança dos ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip MD5```

3. Verifique os valores de hash gerados. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 6316bcc8bc93204295bfe3f4be39449
          

### <a name="run-the-script"></a>Executar o script

Aqui está o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções windows, incluindo O Serviço de Ativação do Windows, IIS e PowerShell ISE.
- Descarrega e instala um módulo iIS rewritável. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com configurações persistentes para o Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Ficheiros Config**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de Registo**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraio o ficheiro com fecho numa pasta na máquina que irá alojar o aparelho. Certifique-se de que não executa o guião numa máquina num aparelho de migração Azure existente.
2. Lance powerShell na máquina, com privilégios de administrador (elevado).
3. Altere o diretório PowerShell para a pasta que contém os conteúdos extraídos do ficheiro com fecho descarregado.
4. Executar o script **AzureMigrateInstaller.ps1**, da seguinte forma:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se a URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Instale o aparelho para Hyper-V

Para configurar o aparelho para Hyper-V, faça o download de um ficheiro com fecho do portal Azure e extrai o conteúdo. Executa o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2.  Em **Discover machines** > **As suas máquinas são virtualizadas?** **Yes, with Hyper-V**
3.  Clique em **Baixar,** para descarregar o ficheiro zipped. 


### <a name="verify-file-security"></a>Verificar a segurança dos ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verifique os valores de hash gerados. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 717f8b9185f565006b5aff0215ecadac
          

### <a name="run-the-script"></a>Executar o script

Aqui está o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções windows, incluindo O Serviço de Ativação do Windows, IIS e PowerShell ISE.
- Descarrega e instala um módulo iIS rewritável. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com configurações persistentes para o Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Ficheiros Config**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de Registo**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraio o ficheiro com fecho numa pasta na máquina que irá alojar o aparelho. Certifique-se de que não executa o guião numa máquina num aparelho de migração Azure existente.
2. Lance powerShell na máquina, com privilégios de administrador (elevado).
3. Altere o diretório PowerShell para a pasta que contém os conteúdos extraídos do ficheiro com fecho descarregado.
4. Executar o script **AzureMigrateInstaller.ps1**, da seguinte forma:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ``` 
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se a URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurar o aparelho para servidores físicos

Para configurar o aparelho para VMware, faça o download de um ficheiro com fecho de correr do portal Azure e extrai o conteúdo. Executa o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configure-o pela primeira vez. Em seguida, registe o aparelho com o projeto Azure Migrate.

### <a name="download-the-script"></a>Descarregue o script

1.  Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2.  Em **Discover machines** > **As suas máquinas são virtualizadas?** **Not virtualized/Other**
3.  Clique em **Baixar,** para descarregar o ficheiro zipped. 


### <a name="verify-file-security"></a>Verificar a segurança dos ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5```

3. Verifique os valores de hash gerados. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | f81c155fc4a1409901caea948713913f
          

### <a name="run-the-script"></a>Executar o script

Aqui está o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções windows, incluindo O Serviço de Ativação do Windows, IIS e PowerShell ISE.
- Descarrega e instala um módulo iIS rewritável. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com configurações persistentes para o Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Ficheiros Config**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de Registo**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraio o ficheiro com fecho numa pasta na máquina que irá alojar o aparelho. Certifique-se de que não executa o guião numa máquina num aparelho de migração Azure existente.
2. Lance powerShell na máquina, com privilégios de administrador (elevado).
3. Altere o diretório PowerShell para a pasta que contém os conteúdos extraídos do ficheiro com fecho descarregado.
4. Executar o script **AzureMigrateInstaller.ps1**, da seguinte forma:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se a URLs Azure para [nuvens governamentais](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Passos seguintes

Depois de colocar o aparelho, é necessário configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

- Instale o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Instale o aparelho para [hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Instale o aparelho para [servidores físicos](how-to-set-up-appliance-physical.md).