---
title: Instale um aparelho Azure Migrate com um script
description: Aprenda a configurar um aparelho Azure Migrate com um script
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: 0c4d85909bbfa623b5ad8590e973250474d9d95a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81676303"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um aparelho com um guião

Siga este artigo para criar um [aparelho Azure Migrate](deploy-appliance.md) para a avaliação/migração de VMware VMs e VMs Hiper-V. Executa um guião para criar um aparelho e verifique se pode ligar-se ao Azure. 

Pode implantar o aparelho para VMware e Hiper-V VMs utilizando um script ou utilizando um modelo que descarrega a partir do portal Azure. Usar um script é útil se não conseguir criar um VM usando o modelo descarregado.

- Para utilizar um modelo, siga os tutoriais para [VMware](tutorial-prepare-vmware.md) ou [Hyper-V](tutorial-prepare-hyper-v.md).
- Para configurar um aparelho para servidores físicos, só pode utilizar um script. Siga [este artigo.](how-to-set-up-appliance-physical.md)
- Para instalar um aparelho numa nuvem do Governo Azure, siga [este artigo.](deploy-appliance-script-government.md)

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
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```
3. Verifique o valor hash gerado. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c



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

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

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
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifique os valores de hash gerados. Para a versão mais recente do aparelho:

    **Algoritmo** | **Valor de hash**
    --- | ---
    MD5 | 1e92ede3e87c03bd148e56a708cd33f
    SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

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
4. Executar o script **AzureMigrateInstaller.ps1**, da seguinte forma:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos do script em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar o acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

## <a name="next-steps"></a>Passos seguintes

Depois de colocar o aparelho, é necessário configurá-lo pela primeira vez e registá-lo com o projeto Azure Migrate.

- Instale o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Instale o aparelho para [hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
