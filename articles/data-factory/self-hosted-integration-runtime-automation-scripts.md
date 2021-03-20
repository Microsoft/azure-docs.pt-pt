---
title: Automatização da instalação de tempo de execução de integração auto-hospedada usando scripts locais do PowerShell
description: Para automatizar a instalação do tempo de execução de integração auto-hospedado em máquinas locais.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/09/2020
ms.openlocfilehash: 8cbe54a23cb1c8b55afd86a18b51c0e392c3f78a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376212"
---
# <a name="automating-self-hosted-integration-runtime-installation-using-local-powershell-scripts"></a>Automatização da instalação de tempo de execução de integração auto-hospedada usando scripts locais do PowerShell
Para automatizar a instalação do Tempo de Execução de Integração Auto-hospedado em máquinas locais (com além de VMs Azure onde podemos aproveitar o modelo de Gestor de Recursos), pode utilizar scripts locais do PowerShell. Este artigo introduz dois scripts que pode usar.

## <a name="prerequisites"></a>Pré-requisitos

* Lançar PowerShell na sua máquina local. Para executar os scripts, tem de escolher **Executar como Administrador**.
* [Descarregue](https://www.microsoft.com/download/details.aspx?id=39717) o software de execução de integração auto-hospedado. Copie o caminho onde está o ficheiro descarregado. 
* Também precisa de uma chave de **autenticação** para registar o tempo de integração auto-hospedado.
* Para automatizar atualizações manuais, é necessário ter um tempo de integração auto-organizado pré-configurado.

## <a name="scripts-introduction"></a>Introdução de scripts 

> [!NOTE]
> Estes scripts são criados usando a utilidade da linha de [comando documentada](./create-self-hosted-integration-runtime.md#set-up-an-existing-self-hosted-ir-via-local-powershell) no tempo de execução de integração auto-hospedado. Se necessário, pode-se personalizar estes scripts de acordo com as suas necessidades de automação.
> Os scripts precisam de ser aplicados por nó, por isso certifique-se de executá-lo em todos os nós em caso de configuração de alta disponibilidade (2 ou mais nós).

* Para automatizar a configuração: Instale e registe um novo nó de tempo de execução de integração auto-hospedado utilizando **[InstallGatewayOnLocalMachine.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/InstallGatewayOnLocalMachine.ps1)** - O script pode ser utilizado para instalar um nó de tempo de execução de integração auto-hospedado e registá-lo com uma chave de autenticação. O script aceita dois argumentos, **especificando primeiro** a localização do tempo de [integração auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717) num disco local, **em segundo lugar** especificando a chave de **autenticação** (para registar o nó IV auto-hospedado).

* Para automatizar atualizações manuais: Atualize o nó IR auto-hospedado com uma versão específica ou para a versão mais recente **[script-update-gateway.ps1](https://github.com/nabhishek/SelfHosted-IntegrationRuntime_AutomationScripts/blob/master/script-update-gateway.ps1)** - Isto também é suportado no caso de ter desligado a atualização automática ou pretender ter mais controlo sobre as atualizações. O script pode ser usado para atualizar o nó de tempo de execução de integração auto-hospedado para a versão mais recente ou para uma versão mais alta especificada (downgrade não funciona). Aceita um argumento para especificar o número da versão (exemplo: -versão 3.13.6942.1). Quando nenhuma versão é especificada, atualiza sempre o IR auto-hospedado para a versão mais recente encontrada nos [downloads](https://www.microsoft.com/download/details.aspx?id=39717).
    > [!NOTE]
    > Apenas as últimas 3 versões podem ser especificadas. Idealmente isto é usado para atualizar um nó existente para a versão mais recente. **ASSUME QUE TEM UM IR AUTO-HOSPEDADO REGISTADO.** 

## <a name="usage-examples"></a>Exemplos de utilização

### <a name="for-automating-setup"></a>Para automatizar a configuração
1. Descarregue o IR auto-hospedado a partir [daqui.](https://www.microsoft.com/download/details.aspx?id=39717) 
1. Especifique o caminho onde está o SHIR MSI (ficheiro de instalação) já descarregado. Por exemplo, se o caminho for *C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi*, então pode utilizar abaixo o exemplo da linha de comando PowerShell para esta tarefa:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\InstallGatewayOnLocalMachine.ps1 -path "C:\Users\username\Downloads\IntegrationRuntime_4.7.7368.1.msi" -authKey "[key]"
   ```

    > [!NOTE]
    > Substitua [a chave] pela chave de autenticação para registar o seu IR.
    > Substitua o "nome de utilizador" pelo seu nome de utilizador.
    > Especifique a localização do ficheiro "InstallGatewayOnLocalMachine.ps1" ao executar o script. Neste exemplo, armazenámo-lo no Desktop.

1. Se houver um IR auto-instalado pré-instalado na sua máquina, o script desinstala-o automaticamente e, em seguida, configura um novo. Você verá a seguinte janela surgiu: ![ configurar o tempo de integração](media/self-hosted-integration-runtime-automation-scripts/integration-runtime-configure.png)

1. Quando a instalação e o registo das chaves estiverem concluídos, *verás Que Conseguir instalar gateway* e conseguir registar os resultados do *gateway* no powershell local.
        [![resultado de execução do script 1](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-1-run-result.png#lightbox)

### <a name="for-automating-manual-updates"></a>Para automatizar atualizações manuais
Este script é utilizado para atualizar/instalar + registar o tempo de execução de integração auto-hospedado mais recente. A execução do script executa os seguintes passos:
1. Verifique a versão atual do IR auto-hospedado
2. Obtenha a versão mais recente ou especificada a partir do argumento
3. Se houver uma versão mais recente do que a versão atual:
    * baixar imi IR auto-hospedado
    * atualizá-lo

Pode seguir abaixo o exemplo da linha de comando para utilizar este script:
* Descarregue e instale o mais recente gateway:

   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1
   ```    
* Descarregue e instale porta de entrada de versão especificada:
   ```powershell
   PS C:\windows\system32> C:\Users\username\Desktop\script-update-gateway.ps1 -version 3.13.6942.1
   ``` 
   Se a sua versão atual já for a mais recente, verá o seguinte resultado, sugerindo que não é necessária nenhuma atualização.   
    [![resultado de execução do script 2](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png)](media/self-hosted-integration-runtime-automation-scripts/script-2-run-result.png#lightbox)