---
title: Automatizar instalação de tempo de execução de integração auto-hospedada usando scripts locais powerShell
description: Para automatizar a instalação de Tempo de Integração Auto-hospedado em máquinas locais.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 068586a96ad3655cb70171266bd58f56ed320fc1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664383"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatizar instalação de tempo de execução de integração auto-hospedada usando scripts locais powerShell
Para automatizar a instalação de Tempo de Integração Auto-hospedado em máquinas locais (com a lém de VMs Azure onde podemos alavancar o modelo de Gestor de Recursos), pode utilizar scripts powerShell locais. Este artigo introduz dois scripts que pode usar.

## <a name="prerequisites"></a>Pré-requisitos

* Lance powerShell na sua máquina local. Para executar os scripts, tem de escolher **Executar como Administrador**.
* [Descarregue](https://www.microsoft.com/download/details.aspx?id=39717) o software de tempo de execução de integração auto-hospedado. Copie o caminho onde está o ficheiro descarregado. 
* Também precisa de uma chave de **autenticação** para registar o tempo de execução da integração auto-hospedada.
* Para automatizar atualizações manuais, é necessário ter um tempo de funcionamento de integração auto-configurado pré-configurado.

## <a name="scripts-introduction"></a>Introdução de scripts 

> [!NOTE]
> Estes scripts são criados usando o utilitário de linha de [comando documentado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#set-up-an-existing-self-hosted-ir-via-local-powershell) no tempo de execução de integração auto-hospedado. Se necessário, pode-se personalizar estes scripts de acordo com as suas necessidades de automação.
> Os scripts precisam de ser aplicados por nó, por isso certifique-se de executá-lo em todos os nós em caso de configuração de alta disponibilidade (2 ou mais nós).

* Para automatizar a configuração: Instale e registe um novo nó de tempo de funcionação de integração auto-hospedado utilizando **[a InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** - O script pode ser usado para instalar o nó de tempo de funcionação de integração auto-hospedado e registá-lo com uma chave de autenticação. O guião aceita dois argumentos, **primeiro** especificando a localização do tempo de execução de [integração auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717) num disco local, **em segundo lugar** especificando a chave de **autenticação** (para registar o nó de INFRAVERMELHOS auto-hospedado).

* Para automatizar atualizações manuais: Atualize o nó DE IR auto-hospedado com uma versão específica ou para a versão mais recente **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** - Isto também é suportado caso tenha desligado a atualização automática ou queira ter mais controlo sobre as atualizações. O script pode ser usado para atualizar o nó de tempo de funcionamento de integração auto-hospedado para a versão mais recente ou para uma versão mais alta especificada (downgrade não funciona). Aceita um argumento para especificar o número da versão (exemplo: -versão 3.13.6942.1). Quando nenhuma versão é especificada, atualiza sempre o IR auto-hospedado para a versão mais recente encontrada nos [downloads](https://www.microsoft.com/download/details.aspx?id=39717).
    > [!NOTE]
    > Só podem ser especificadas as últimas 3 versões. Idealmente, isto é usado para atualizar um nó existente para a versão mais recente. **Assume que tem um IR AUTO REGISTADO.** 

## <a name="usage-examples"></a>Exemplos de utilização

### <a name="for-automating-setup"></a>Para automatizar a configuração
1. Baixe o IR auto-hospedado [daqui.](https://www.microsoft.com/download/details.aspx?id=39717) 
1. Especifique o caminho onde está o SHIR MSI (ficheiro de instalação) acima descarregado. Por exemplo, se o caminho for *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi,* então pode utilizar abaixo o exemplo da linha de comando PowerShell para esta tarefa:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Substitua [chave] pela chave de autenticação para registar o seu IR.
    > Substitua o "nome de utilizador" pelo seu nome de utilizador.
    > Especifique a localização do ficheiro "InstalarGatewayOnLocalMachine.ps1" ao executar o script. Neste exemplo, armazenámo-lo no Desktop.

1. Se houver um IR auto-instalado pré-instalado na sua máquina, o script desinstala-o automaticamente e, em seguida, configura um novo. Verá a seguinte janela aberta: configurar o tempo de ![ execução da integração](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Quando a instalação e o registo da chave estiverem concluídos, verá *supor* que o Succeed instale o gateway e o Succeed para registar os resultados do *gateway* na sua PowerShell local.
        [![resultado de execução de script 1](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Para automatizar atualizações manuais
Este script é usado para atualizar/instalar + registar o mais recente tempo de execução de integração auto-hospedada. A execução do script executa os seguintes passos:
1. Verifique a versão ir auto-hospedada atual
2. Obtenha versão mais recente ou versão especificada a partir do argumento
3. Se houver versão mais recente do que a versão atual:
    * descarregar ir auto-hospedado msi
    * atualitá-lo

Pode seguir o exemplo abaixo da linha de comando para utilizar este script:
* Descarregue e instale o mais recente portal:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Descarregue e instale o portal da versão especificada:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Se a sua versão atual já for a mais recente, verá o resultado seguinte, sugerindo que não é necessária nenhuma atualização.   
    [![resultado de execução do script 2](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)
