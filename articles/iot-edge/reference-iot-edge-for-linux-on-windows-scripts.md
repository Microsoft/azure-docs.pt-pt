---
title: Scripts PowerShell para Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Informações de referência para Azure IoT Edge para Linux nos scripts Windows PowerShell para implementar, provisão e status IoT Edge para Linux em máquinas virtuais Windows.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0881363a3f13f0ddf34157a1fffe6c26d0c0b692
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122855"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Scripts PowerShell para IoT Edge para Linux no Windows

Compreenda os scripts PowerShell que implementam, provisões e obtenha o estado do seu IoT Edge para Linux na máquina virtual do Windows.

Os comandos descritos neste artigo são do `AzureEFLOW.psm1` ficheiro, que pode ser encontrado no seu sistema no seu `WindowsPowerShell` diretório sob `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

O comando **Implementar-Fluxo** é o principal método de implantação. O comando de implementação cria a máquina virtual, fornece ficheiros e implanta o módulo de agente IoT Edge. Embora nenhum dos parâmetros seja necessário, podem ser utilizados para forrar o seu dispositivo IoT Edge durante a implementação e modificar as definições para a máquina virtual durante a criação. Para uma lista completa, utilize o comando `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>Para o tipo de provisionamento, **x509** refere-se atualmente exclusivamente ao fornecimento de X509 utilizando um [serviço de provisionamento de dispositivos Azure IoT Hub](../iot-dps/about-iot-dps.md). O método de provisionamento manual X509 não é suportado atualmente.

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| eflowVhdxDir | Caminho do diretório | Diretório onde a implantação armazena ficheiro VHDX para VM. |
| a provisionamentoType | **manual**, **TPM,** **X509,** ou **simétrico** |  Define o tipo de provisão que deseja utilizar para o seu dispositivo IoT Edge. |
| devConnString | A cadeia de ligação do dispositivo de um dispositivo IoT Edge existente | Cadeia de ligação do dispositivo para o fornecimento manual de um dispositivo IoT Edge **(manual**). |
| symmKey | A chave primária para uma inscrição dPS existente ou a chave primária de um dispositivo IoT Edge existente registado com teclas simétricas | Chave simétrica para o provisionamento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| scopeId | A identificação de âmbito para um caso dPS existente. | ID de âmbito para o fornecimento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| registrationId | O ID de registo de um dispositivo IoT Edge existente | ID de registo para o fornecimento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| identidadeCertLocVm | Percurso diretório; deve estar numa pasta que pode ser propriedade do `iotedge` serviço | Percurso de destino absoluto do certificado de identidade na sua máquina virtual para o fornecimento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| identidadeCertLocWin | Caminho do diretório | Percurso de origem absoluto do certificado de identidade no Windows para o fornecimento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| identidadePkLocVm |  | Percurso diretório; deve estar numa pasta que pode ser propriedade do `iotedge` serviço | Percurso de destino absoluto da chave privada de identidade na sua máquina virtual para o fornecimento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| identidadePkLocWin | Caminho do diretório | Caminho de origem absoluta da chave privada de identidade no Windows para o fornecimento de um dispositivo IoT Edge **(X509** ou **simétrico).** |
| vmSizeDefintion | Não mais de 30 caracteres | Definição do número de núcleos e RAM disponível para a máquina virtual. **Valor predefinido:** Standard_K8S_v1. |
| vmDiskSize | Entre 8 GB e 256 GB | Tamanho máximo do disco do disco rígido em expansão dinâmica. **Valor predefinido:** 16 GB. |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |
| vnetType | **Transparente** ou **ICS** | O tipo de interruptor virtual. **Valor predefinido**: Transparente. |
| vnetName | Não mais de 64 caracteres | O nome do interruptor virtual. **Valor predefinido**: Externo. |
| enableVtpm | Nenhum | **Parâmetro de comutação**. Crie a máquina virtual com TPM ativada ou desativada. |
| mobyPackageVersion | Não mais de 30 caracteres |  Versão do pacote Moby a ser verificado ou instalado na máquina virtual.  **Valor predefinido:** 19.03.11. |
| iotedgePackageVersion | Não mais de 30 caracteres | Versão do pacote IoT Edge a ser verificado ou instalado na máquina virtual. **Valor predefinido:** 1.1.0. |
| instalar Embalamentos | Nenhum | **Parâmetro de comutação**. Quando alternado, o script tentará instalar os pacotes Moby e IoT Edge em vez de apenas verificar se os pacotes estão presentes. |

## <a name="verify-eflowvm"></a>Verify-EflowVm

O comando **Check-EflowVm** é uma função exposta para verificar se foi criado o IoT Edge for Linux na máquina virtual Windows. Só são necessários parâmetros comuns, e voltará **a ser verdadeiro** se a máquina virtual for criada e **falsa** se não for. Para obter informações adicionais, utilize o comando `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

O comando **Provision-EflowVm** adiciona as informações de provisionamento do seu dispositivo IoT Edge ao ficheiro IoT Edge da máquina `config.yaml` virtual. O provisionamento também pode ser feito durante a fase de implantação, definindo parâmetros no comando **Deploy-Eflow.** Para obter informações adicionais, utilize o comando `Get-Help Provision-EflowVm -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |
| a provisionamentoType | **manual**, **TPM,** **X509,** ou **simétrico** |  Define o tipo de provisão que deseja utilizar para o seu dispositivo IoT Edge. |
| devConnString | A cadeia de ligação do dispositivo de um dispositivo IoT Edge existente | Cadeia de ligação do dispositivo para o fornecimento manual de um dispositivo IoT Edge **(manual**). |
| symmKey | A chave primária para uma inscrição dPS existente ou a chave primária de um dispositivo IoT Edge existente registado com teclas simétricas | Chave simétrica para o provisionamento de um dispositivo IoT Edge **(DPS,** **simétrica**). |
| scopeId | A identificação de âmbito para um caso dPS existente. | ID de âmbito para o fornecimento de um dispositivo IoT Edge **(DPS**). |
| registrationId | O ID de registo de um dispositivo IoT Edge existente | ID de registo para o fornecimento de um dispositivo IoT Edge **(DPS**). |
| identidadeCertLocVm | Percurso diretório; deve estar numa pasta que pode ser propriedade do `iotedge` serviço | Percurso de destino absoluto do certificado de identidade na sua máquina virtual para o fornecimento de um dispositivo IoT Edge **(DPS,** **X509**). |
| identidadeCertLocWin | Caminho do diretório | Caminho de origem absoluta do certificado de identidade no Windows para o fornecimento de um dispositivo IoT Edge **(dps**, **X509**). |
| identidadePkLocVm |  | Percurso diretório; deve estar numa pasta que pode ser propriedade do `iotedge` serviço | Percurso de destino absoluto da chave privada de identidade na sua máquina virtual para o fornecimento de um dispositivo IoT Edge **(DPS,** **X509**). |
| identidadePkLocWin | Caminho do diretório | Caminho de origem absoluta da chave privada de identidade no Windows para o fornecimento de um dispositivo IoT Edge **(dps**, **X509**). |

## <a name="get-eflowvmname"></a>Get-EflowVmName

O comando **Get-EflowVmName** é utilizado para consultar o nome de anfitrião atual da máquina virtual. Este comando existe para explicar o facto de que o nome de hospedeiro do Windows pode mudar com o tempo. Só são necessários parâmetros comuns e devolverá o nome de hospedeiro atual da máquina virtual. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

O comando **Get-EflowLogs** é utilizado para recolher e embalar registos do IoT Edge para Linux na implementação do Windows. Produz os troncos agregados sob a forma de uma `.zip` pasta. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowLogs -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

O comando **Get-EflowVmTpmProvisioningInfo** é utilizado para recolher e exibir as informações de fornecimento de vTPM da máquina virtual. Se a máquina virtual foi criada sem vTPM, o comando retornará que não foi capaz de encontrar informações de fornecimento de TPM. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

O comando **Get-EflowVmAddr** é utilizado para encontrar e exibir os endereços IP e MAC da máquina virtual. Só são precisos parâmetros comuns. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

O comando **Get-EflowVmSystemInformation** é utilizado para recolher e visualizar informações do sistema a partir da máquina virtual, como a utilização da memória e do armazenamento. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmSystemInformation -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

O comando **Get-EflowVmEdgeInformation** é utilizado para recolher e exibir informações do IoT Edge a partir da máquina virtual, como a versão do IoT Edge que a máquina virtual está a executar. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmEdgeInformation -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

O comando **Get-EflowVmEdgeModuleList** é utilizado para consultar e exibir a lista de módulos IoT Edge em funcionamento na máquina virtual. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

O comando **Get-EflowVmEdgeStatus** é utilizado para consultar e exibir o estado de funcionamento do IoT Edge na máquina virtual. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmEdgeStatus -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

O comando **Get-EflowVmUserName** é utilizado para consultar e exibir o nome de utilizador da máquina virtual que foi utilizado para criar a máquina virtual a partir do registo. Só são precisos parâmetros comuns. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

O comando **Get-EflowVmSshKey** é utilizado para consultar e exibir a chave SSH utilizada pela máquina virtual. Só são precisos parâmetros comuns. Para obter informações adicionais, utilize o comando `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

O comando **Ssh-EflowVm** é utilizado para sSH na máquina virtual. Para obter informações adicionais, utilize o comando `Get-Help Ssh-EflowVm -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| vmUser | Não mais de 30 caracteres | Nome de utilizador para iniciar sessão na máquina virtual. |

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar estes comandos no seguinte artigo:

* [Instale a borda Azure IoT para Linux no Windows](how-to-install-iot-edge-windows.md)

* Consulte [a referência de IoT Edge para Linux no Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) para todos os comandos disponíveis através do PowerShell.
