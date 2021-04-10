---
title: Configurar um aparelho Azure Migrate com um script
description: Saiba como configurar um aparelho Azure Migrate com um script
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: c78778f9152fd4c07fb9e550e562cfef858333c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786741"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurar um aparelho com um script

Siga este artigo para criar um [aparelho Azure Migrate](./migrate-appliance-architecture.md) para a avaliação/migração de servidores em VMware e no Hyper-V. Você executou um script para criar um aparelho, e verificar se ele pode ligar-se ao Azure. 

Pode implantar o aparelho para servidores em VMware e no Hyper-V utilizando um script, ou utilizando um modelo que descarrega a partir do portal Azure. A utilização de um script é útil se não conseguir criar um aparelho utilizando o modelo descarregado.

- Para utilizar um modelo, siga os tutoriais para [VMware](./tutorial-discover-vmware.md) ou [Hyper-V](./tutorial-discover-hyper-v.md).
- Para configurar um aparelho para servidores físicos, só pode utilizar um script. Siga [este artigo.](how-to-set-up-appliance-physical.md)
- Para instalar um aparelho numa nuvem do Governo Azure, siga [este artigo.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Pré-requisitos

O script configura o aparelho Azure Migrate num servidor existente.

- O servidor que funcionará como o aparelho deve satisfazer os seguintes requisitos de hardware e so:

Scenario | Requisitos
--- | ---
VMware | Windows Server 2016, com 32 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento de disco
Hyper-V | Windows Server 2016, com 16 GB de memória, oito vCPUs, cerca de 80 GB de armazenamento de disco

- O servidor também precisa de um interruptor virtual externo. Requer um endereço IP estático ou dinâmico e acesso à internet.
- Antes de implementar o aparelho, reveja os requisitos detalhados do aparelho para [servidores em VMware](migrate-appliance.md#appliance---vmware), [em Hyper-V](migrate-appliance.md#appliance---hyper-v).
- Não coloque o guião num aparelho Azure Migrate existente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurar o aparelho para VMware

Para configurar o aparelho para VMware, descarregue o ficheiro com fecho de correr com o nome AzureMigrateInstaller-Server-Public.zip a partir do portal ou a partir [daqui](https://go.microsoft.com/fwlink/?linkid=2140334)– e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto.

### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No servidor para o qual descarregou o ficheiro, abra uma janela de comando do administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Verifique a versão e o script mais recente do aparelho para a nuvem pública Azure:

    **Algoritmo** | **Transferência** | **SHA256**
    --- | --- | ---
    VMware (85.8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee434123861808d82147916330e66669f94c7969fe1b3d0fe72

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

1. Extraia o ficheiro com fecho para uma pasta no servidor que irá hospedar o aparelho. Certifique-se de que não executa o script num aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurar o aparelho para Hiper-V

Para configurar o aparelho para Hyper-V, descarregue o ficheiro com fecho de correr com o nome AzureMigrateInstaller-Server-Public.zip a partir do portal ou a partir [daqui](https://go.microsoft.com/fwlink/?linkid=2105112), e extraia o conteúdo. Você executou o script PowerShell para lançar a aplicação web do aparelho. Instale o aparelho e configuure-o pela primeira vez. Em seguida, registe o aparelho com o projeto.


### <a name="verify-file-security"></a>Verificar segurança de ficheiros

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No servidor para o qual descarregou o ficheiro, abra uma janela de comando do administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exemplo: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Verifique a versão e o script mais recente do aparelho para a nuvem pública Azure:

    **Cenário** | **Transferência** | **SHA256**
    --- | --- | ---
    Hiper-V (85,8 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Extraia o ficheiro com fecho para uma pasta no servidor que irá hospedar o aparelho. Certifique-se de que não executa o script num aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor, com privilégios de administrador (elevados).
3. Altere o diretório PowerShell para a pasta que contém o conteúdo extraído do ficheiro fechado descarregado.
4. Executar o script **AzureMigrateInstaller.ps1,** da seguinte forma:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Depois de o script ser executado com sucesso, lança a aplicação web do aparelho para que possa configurar o aparelho. Se encontrar algum problema, reveja os registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificar acesso

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para a nuvem [pública.](migrate-appliance.md#public-cloud-urls)

## <a name="next-steps"></a>Passos seguintes

Depois de colocar o aparelho, é necessário configurá-lo pela primeira vez e registá-lo com o projeto.

- Configurar o aparelho para [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Ativar o aparelho para [Hiper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).