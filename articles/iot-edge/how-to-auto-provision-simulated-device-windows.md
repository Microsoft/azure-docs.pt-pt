---
title: Disponibilização automática de dispositivos Windows com DPS - Azure IoT Edge [ Microsoft Docs
description: Utilize um dispositivo simulado na sua máquina Windows para testar o fornecimento automático de dispositivos para o Azure IoT Edge com o Serviço de Provisionamento de Dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8278d9f2129ab8b213cf1b561f4b82b56dffc8da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131044"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Crie e forre um dispositivo IoT Edge simulado com um TPM virtual no Windows

Os dispositivos Azure IoT Edge podem ser aprovisionados automaticamente utilizando o Serviço de [Provisionamento](../iot-dps/index.yml) de Dispositivos, tal como dispositivos que não estão ativados por bordas. Se não estiver familiarizado com o processo de fornecimento automático, reveja os [conceitos de fornecimento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

O DPS suporta o atestado de chave simétrica para dispositivos IoT Edge tanto na inscrição individual como na inscrição em grupo. Para a inscrição em grupo, se verificar a opção "is IoT Edge" para ser verdadeira no atestado de chave simétrica, todos os dispositivos que estão registados nesse grupo de matrículas serão marcados como dispositivos IoT Edge.

Este artigo mostra-lhe como testar o fornecimento automático num dispositivo IoT Edge simulado com os seguintes passos:

* Crie uma instância do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).
* Crie um dispositivo simulado na sua máquina Windows com um módulo de plataforma fidedigno simulado (TPM) para segurança de hardware.
* Crie uma inscrição individual para o dispositivo.
* Instale o tempo de execução do IoT Edge e ligue o dispositivo ao IoT Hub.

> [!TIP]
> Este artigo descreve o teste de fornecimento automático utilizando o atestado TPM em dispositivos virtuais, mas grande parte dele aplica-se também ao utilizar hardware TPM físico.

## <a name="prerequisites"></a>Pré-requisitos

* Uma máquina de desenvolvimento do Windows. Este artigo utiliza o Windows 10.
* Um hub ativo.

> [!NOTE]
> TPM 2.0 é necessário quando se utiliza atestado TPM com DPS e só pode ser usado para criar matrículas individuais, não em grupo.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos IoT Hub

Crie uma nova instância do Serviço de Provisionamento de Dispositivos IoT Hub em Azure e ligue-o ao seu hub IoT. Pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** a partir da página de visão geral. Utiliza este valor quando configura o tempo de execução do IoT Edge.

> [!TIP]
> Se estiver a utilizar um dispositivo TPM físico, tem de determinar a **chave De aval,** que é única em cada chip TPM e é obtida junto do fabricante de chips TPM associado ao mesmo. Pode obter um ID de **registo** único para o seu dispositivo TPM, por exemplo, criando um hash SHA-256 da chave de endosso.
>
> Siga as instruções do artigo Como gerir as matrículas do dispositivo com o [Portal Azure](../iot-dps/how-to-manage-enrollments.md) para criar a sua inscrição no DPS e, em seguida, prosseguir com a secção de tempo de [funcionação De Instalação da Borda IoT](#install-the-iot-edge-runtime) neste artigo para continuar.

## <a name="simulate-a-tpm-device"></a>Simular um dispositivo TPM

Crie um dispositivo TPM simulado na sua máquina de desenvolvimento windows. Recupere a chave **de identificação** e **averbamento** de registo para o seu dispositivo e utilize-os para criar uma entrada individual de inscrição no DPS.

Ao criar uma inscrição em DPS, tem a oportunidade de declarar um **Dispositivo Inicial Twin State**. No dispositivo twin pode configurar etiquetas para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas](how-to-deploy-at-scale.md).

Escolha a linguagem SDK que pretende utilizar para criar o dispositivo simulado e siga os passos até criar a inscrição individual.

Quando criar a inscrição individual, selecione **True** para declarar que o dispositivo TPM simulado na sua máquina de desenvolvimento Windows é um **dispositivo IoT Edge**.

> [!TIP]
> No Azure CLI, pode criar uma [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e utilizar a bandeira **ativada pela borda** para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

Dispositivo simulado e guias individuais de inscrição:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C #](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Depois de criar a inscrição individual, guarde o valor do ID de **registo**. Utiliza este valor quando configura o tempo de execução do IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda.

Necessitará das seguintes informações ao fornecer o seu dispositivo:

* O valor do âmbito de **identificação** dPS
* O **dispositivo Identificação de Registo** que criou

Instale o tempo de funcionamento do IoT Edge no dispositivo que está a executar o TPM simulado. Você vai configurar o tempo de funcionamento do IoT Edge para o fornecimento automático, não manual.

> [!TIP]
> Mantenha aberta a janela que está a funcionar o simulador TPM durante a sua instalação e teste.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerir contentores e atualizar o IoT Edge, consulte Instalar o tempo de execução do [Edge Azure IoT no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela PowerShell no modo administrador. Certifique-se de que utiliza uma sessão AMD64 de PowerShell ao instalar o IoT Edge, e não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução do IoT Edge. O comando não se aplica à utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos do Windows 10 ou Do Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie o seu dispositivo agora. Uma vez que o seu dispositivo esteja pronto, execute a PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando não se aplica ao fornecimento manual com recipientes Windows. Utilize `-Dps` a bandeira para utilizar o Serviço de Provisionamento de Dispositivos em vez de o provisionamento manual.

   Substitua os valores do espaço reservado para `{scope_id}` e `{registration_id}` com os dados recolhidos anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Se o tempo de funcionamento tiver começado com sucesso, pode entrar no seu Hub IoT e começar a implantar módulos IoT Edge para o seu dispositivo. Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de execução foi instalado e começou com sucesso.  

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registos de serviço dos últimos 5 minutos.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista de módulos de execução.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir as etiquetas duplas de identificação do dispositivo e dispositivo ao mesmo tempo que disponibiliza o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos ou grupos individuais de dispositivos utilizando a gestão automática do dispositivo. Saiba como [implantar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-at-scale.md) ou utilizando o [Azure CLI](how-to-deploy-cli-at-scale.md)
