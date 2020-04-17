---
title: Instale um aparelho Azure Migrate com um script
description: Aprenda a configurar um aparelho Azure Migrate com um script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: faed7f96ea8c1850af5523d35f9f891011a48df8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537717"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um aparelho com um guião

Este artigo descreve como configurar o [aparelho Azure Migrate](deploy-appliance.md) utilizando um script de instalação PowerShell, para VMware VMs e VMs Hyper-V. Se quiser configurar o aparelho para servidores físicos, [reveja este artigo](how-to-set-up-appliance-physical.md).


Pode utilizar o aparelho utilizando um par de métodos:


- Utilizando um modelo para VMware VMs (OVA) ou VMs Hiper-V (VHD).
- Usando um guião. Este é o método descrito neste artigo. O script fornece:
    - Uma alternativa à configuração do aparelho utilizando um modelo OVA, para avaliação e migração sem agente de VMware VMs.
    - Uma alternativa à configuração do aparelho utilizando um modelo VHD, para avaliação e migração de VMs hiper-V.
    - Para avaliação de servidores físicos (ou VMs que pretende migrar como servidores físicos), o script é o único método para configurar o aparelho.
    - Uma forma de implantar o aparelho no Governo de Azure.


Depois de criar o aparelho, verifique se pode ligar-se ao Azure Migrate. Em seguida, configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.


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
    - Exemplo de utilização para nuvem pública:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Exemplo de utilização para a nuvem do governo:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip```

3. Verifique os valores de hash gerados:

    - Para a nuvem pública (para a versão mais recente do aparelho):

        **Algoritmo** | **Valor de hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Para o governo Azure (para a versão mais recente do aparelho):

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
4. Executar o script **AzureMigrateInstaller.ps1**, da seguinte forma:
    - Para a nuvem pública:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
    - Para o Governo azure:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>AzureMigrateInstaller.ps1 ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se a URLs Azure para [publicas](migrate-appliance.md#public-cloud-urls) e [nuvens governamentais](migrar-aparelhos.md#government-cloud-urls.


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
    - Exemplo de utilização para nuvem pública:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
    - Exemplo de utilização para a nuvem do governo:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip MD5```

3. Verifique os valores de hash gerados:

    - Para a nuvem pública (para a versão mais recente do aparelho):

        **Algoritmo** | **Valor de hash**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Para o governo Azure (para a versão mais recente do aparelho):

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
4. Executar o script **AzureMigrateInstaller.ps1**, da seguinte forma:
    - Para a nuvem pública:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
    - Para o Governo azure:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>AzureMigrateInstaller.ps1 ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se a URLs Azure para [publicas](migrate-appliance.md#public-cloud-urls) e [nuvens governamentais](migrar-aparelhos.md#government-cloud-urls.



## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a configuração do aparelho com um modelo, ou para servidores físicos, reveja estes artigos:

- Instale o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Instale o aparelho para [hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Instale o aparelho para [servidores físicos](how-to-set-up-appliance-physical.md).