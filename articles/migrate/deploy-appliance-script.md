---
title: Instale um aparelho Azure Migrate com um script
description: Aprenda a configurar um aparelho Azure Migrate com um script
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: bf8d7148f685d4ac6a5f33603020a0503b0c62e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337691"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um aparelho com um guião

Este artigo descreve como configurar o [aparelho Migratório Azure](deploy-appliance.md) utilizando um script de instalação PowerShell.

O script fornece:
- Uma alternativa à configuração do aparelho utilizando um modelo OVA, para avaliação e migração sem agente de VMware VMs.
- Uma alternativa à configuração do aparelho utilizando um modelo VHD, para avaliação e migração de VMs hiper-V.
- Para avaliação de servidores físicos (ou VMs que pretende migrar como servidores físicos), o script é o único método para configurar o aparelho.

## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate numa máquina física ou VM existente.

- A máquina que funcionará como aparelho deve estar a executar o Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento em disco e um interruptor virtual externo. Requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de utilizar o aparelho, reveja os requisitos detalhados do aparelho para [VMware VMs,](migrate-appliance.md#appliance---vmware) [Hiper-V VMs](migrate-appliance.md#appliance---hyper-v)e [servidores físicos](migrate-appliance.md#appliance---physical).
- Não faça o guião num aparelho azure migratório existente.


## <a name="download-the-script"></a>Descarregue o script

1. Localize a máquina/VM que funcionará como o aparelho Migratório Azure.
2. Na máquina, faça o seguinte:

    - Para utilizar o aparelho com VMs VMs ou VMs Hiper-V, [descarregue](https://go.microsoft.com/fwlink/?linkid=2105112) a pasta zipped contendo o script do instalador e as MSIs.
    - Para utilizar o aparelho com servidores físicos, descarregue o script do portal Azure Migrate, conforme descrito neste [tutorial](tutorial-assess-physical.md#set-up-the-appliance).

## <a name="verify-file-security"></a>Verificar a segurança dos ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3. Verifique se os valores de hash gerados correspondem a estas definições (para a versão mais recente do aparelho):

    **Algoritmo** | **Valor de hash**
      --- | ---
      MD5 | 1e92ede3e87c03bd148e56a708cd33f
      SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

## <a name="run-the-script"></a>Executar o script

Aqui está o que o guião faz:

- Instala agentes e uma aplicação web.
- Instala funções windows, incluindo O Serviço de Ativação do Windows, IIS e PowerShell ISE.
- Descarrega e instala um módulo iIS rewritável. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM), com configurações persistentes para o Azure Migrate.
- Cria ficheiros de registo e configuração da seguinte forma:
    - **Ficheiros Config**: %ProgramData%\Microsoft Azure\Config
    - **Ficheiros de Registo**: %ProgramData%\Microsoft Azure\Logs

Para executar o script:

1. Extraio o ficheiro com fecho numa pasta na máquina que irá alojar o aparelho.
2. Lance powerShell na máquina, com privilégios de administrador (elevado).
3. Altere o diretório PowerShell para a pasta que contém os conteúdos extraídos do ficheiro com fecho descarregado.
4. Executar o script **AzureMigrateInstaller.ps1** da seguinte forma:

    - Para VMware: 
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario VMware
        ```
    - Para hyper-V:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 -scenario Hyperv
        ```
    - Para servidores físicos:
        ```
        PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
        ```      
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, pode ver os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

## <a name="next-steps"></a>Passos seguintes

Depois de ter montado o aparelho utilizando o script, siga estas instruções:

- Instale o aparelho para [VMware](how-to-set-up-appliance-vmware.md#configure-the-appliance).
- Instale o aparelho para [hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Instale o aparelho para [servidores físicos](how-to-set-up-appliance-physical.md).