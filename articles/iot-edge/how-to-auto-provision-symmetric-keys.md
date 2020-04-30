---
title: Dispositivo de fornecimento utilizando atestado de chave simétrica - Borda Azure IoT
description: Utilize atestado de chave simétrica para testar o fornecimento automático de dispositivos para o Azure IoT Edge com o Serviço de Provisionamento de Dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c6c2067526850ba972f002dc40bbd5d4cb24c9ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131020"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Criar e fornecer um dispositivo IoT Edge utilizando atestado de chave simétrica

Os dispositivos Azure IoT Edge podem ser aprovisionados automaticamente utilizando o Serviço de [Provisionamento](../iot-dps/index.yml) de Dispositivos, tal como dispositivos que não estão ativados por bordas. Se não estiver familiarizado com o processo de fornecimento automático, reveja os [conceitos de fornecimento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra-lhe como criar uma inscrição individual do Serviço de Provisionamento de Dispositivos utilizando a atestação de chaves simétricas num dispositivo IoT Edge com os seguintes passos:

* Crie uma instância do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).
* Crie uma inscrição individual para o dispositivo.
* Instale o tempo de execução do IoT Edge e ligue-se ao IoT Hub.

O atestado de chaves simétricas é uma abordagem simples para autenticar um dispositivo com uma instância do Serviço de Provisionamento de Dispositivos. Este método de atestado representa uma experiência "Hello world" para desenvolvedores que são novos no fornecimento de dispositivos, ou não têm requisitos de segurança rigorosos. O atestado do dispositivo utilizando um certificado [TPM](../iot-dps/concepts-tpm-attestation.md) ou [X.509](../iot-dps/concepts-security.md#x509-certificates) é mais seguro e deve ser utilizado para requisitos de segurança mais rigorosos.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub iot ativo
* Um dispositivo físico ou virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos IoT Hub

Crie uma nova instância do Serviço de Provisionamento de Dispositivos IoT Hub em Azure e ligue-o ao seu hub IoT. Pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** a partir da página de visão geral. Utiliza este valor quando configura o tempo de execução do IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolha um ID de registo único para o dispositivo

Deve ser definido um ID de registo único para identificar cada dispositivo. Pode utilizar o endereço MAC, número de série ou qualquer informação única do dispositivo.

Neste exemplo, utilizamos uma combinação de um endereço MAC e um `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`número de série formando a seguinte corda para um ID de registo: .

Crie um ID de registo único para o seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e traços ('-').

## <a name="create-a-dps-enrollment"></a>Criar uma inscrição dPS

Utilize o ID de registo do seu dispositivo para criar uma inscrição individual no DPS.

Ao criar uma inscrição em DPS, tem a oportunidade de declarar um **Dispositivo Inicial Twin State**. No dispositivo twin, pode definir etiquetas para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas](how-to-deploy-at-scale.md).

> [!TIP]
> As matrículas em grupo também são possíveis quando se utiliza o atestado de chave simétrica e envolvem as mesmas decisões que as matrículas individuais.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Definições,** **selecione Gerir as matrículas**.

1. **Selecione Adicionar inscrição individual** e, em seguida, completar os seguintes passos para configurar a inscrição:  

   1. Para **mecanismo,** selecione **Chave Simétrica**.

   1. Selecione a caixa de verificação **de chaves geradas automaticamente.**

   1. Forneça o ID de **Registo** que criou para o seu dispositivo.

   1. Forneça um ID do **IoT Hub Device** para o seu dispositivo, se quiser. Pode utilizar iDs do dispositivo para direcionar um dispositivo individual para a implementação do módulo. Se não fornecer uma identificação do dispositivo, o ID de registo é utilizado.

   1. Selecione **True** para declarar que a inscrição é para um dispositivo IoT Edge. Para uma inscrição em grupo, todos os dispositivos devem ser dispositivos IoT Edge ou nenhum deles pode ser.

   > [!TIP]
   > No Azure CLI, pode criar uma [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e utilizar a bandeira **ativada pela borda** para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

   1. Aceite o valor predefinido da política de atribuição do Serviço de Fornecimento de Dispositivos para **a forma como pretende atribuir dispositivos a hubs** ou escolher um valor diferente específico para esta inscrição.

   1. Escolha o **IoT Hub** ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   1. Escolha como pretende que os dados do **dispositivo sejam tratados no reprovisionamento** quando os dispositivos solicitarem o fornecimento após a primeira vez.

   1. Adicione um valor de etiqueta ao **Dispositivo Inicial Twin State,** se quiser. Pode utilizar etiquetas para grupos-alvo de dispositivos para a implementação de módulos. Por exemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Certifique-se de que **a entrada ativa** está definida para **ativar**.

   1. Selecione **Guardar**.

Agora que existe uma inscrição para este dispositivo, o tempo de funcionamento do IoT Edge pode fornecer automaticamente o dispositivo durante a instalação. Certifique-se de copiar o valor **principal** da sua matrícula para usar ao instalar o tempo de execução do IoT Edge, ou se vai estar a criar chaves de dispositivo para utilização com uma inscrição em grupo.

## <a name="derive-a-device-key"></a>Derivar uma chave do dispositivo

> [!NOTE]
> Esta secção só é necessária se utilizar uma inscrição em grupo.

Cada dispositivo utiliza a sua chave de dispositivo derivada com o seu ID de registo único para realizar o atestado da chave simétrica com a inscrição durante o fornecimento. Para gerar a chave do dispositivo, utilize a chave que copiou da sua inscrição em DPS para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave primária ou secundária da sua inscrição no código do dispositivo.

### <a name="linux-workstations"></a>Estações de trabalho linux

Se estiver a utilizar uma estação de trabalho Linux, pode utilizar o openssl para gerar a sua chave de dispositivo derivada, como mostra o seguinte exemplo.

Substitua o valor da **KEY** pela **Chave Primária** que observou anteriormente.

Substitua o valor do **REG_ID** pelo ID de registo do seu dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas em janelas

Se estiver a utilizar uma estação de trabalho baseada no Windows, pode utilizar o PowerShell para gerar a sua chave de dispositivo derivada, como mostra o seguinte exemplo.

Substitua o valor da **KEY** pela **Chave Primária** que observou anteriormente.

Substitua o valor do **REG_ID** pelo ID de registo do seu dispositivo.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda.

Necessitará das seguintes informações ao fornecer o seu dispositivo:

* O valor do âmbito de **identificação** dPS
* O **dispositivo Identificação de Registo** que criou
* A **Chave Primária** que copiou da inscrição do DPS

> [!TIP]
> Para as inscrições em grupo, precisa da [chave derivada de](#derive-a-device-key) cada dispositivo em vez da chave de inscrição do DPS.

### <a name="linux-device"></a>Dispositivo Linux

Siga as instruções para a arquitetura do seu dispositivo. Certifique-se de configurar o tempo de funcionamento do IoT Edge para o fornecimento automático, não manual.

[Instale o tempo de execução do Azure IoT Edge em Linux](how-to-install-iot-edge-linux.md)

A secção do ficheiro de configuração para o fornecimento de chaves simétricas é a seguinte:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Substitua os valores `<REGISTRATION_ID>`do `<SYMMETRIC_KEY>` espaço reservado para, `<SCOPE_ID>`e pelos dados recolhidos anteriormente. Certifique-se de que o **fornecimento:** a linha não tem espaço branco anterior e que os itens aninhados são retalhados por dois espaços.

### <a name="windows-device"></a>Dispositivo Windows

Instale o tempo de execução do IoT Edge no dispositivo para o qual gerou uma chave derivada do dispositivo. Você vai configurar o tempo de funcionamento do IoT Edge para o fornecimento automático, não manual.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerir contentores e atualizar o IoT Edge, consulte Instalar o tempo de execução do [Edge Azure IoT no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela PowerShell no modo administrador. Certifique-se de que utiliza uma sessão AMD64 de PowerShell ao instalar o IoT Edge, e não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução do IoT Edge. O comando não se aplica à utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos do Windows 10 ou Do Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie o seu dispositivo agora. Uma vez que o seu dispositivo esteja pronto, execute a PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando não se aplica ao fornecimento manual `-Dps` com recipientes Windows, a menos que utilize a bandeira para utilizar o fornecimento automático.

   Substitua os valores `{registration_id}`do `{symmetric_key}` espaço reservado para, `{scope_id}`e pelos dados recolhidos anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Se o tempo de funcionamento tiver começado com sucesso, pode entrar no seu Hub IoT e começar a implantar módulos IoT Edge para o seu dispositivo. Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de execução foi instalado e começou com sucesso.

### <a name="linux-device"></a>Dispositivo Linux

Verifique o estado do serviço IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os registos de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista de módulos de execução.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registos de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista de módulos de execução.

```powershell
iotedge list
```

Pode verificar se foi utilizada a inscrição individual que criou no Serviço de Provisionamento de Dispositivos. Navegue para a sua instância de Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes das inscrições para a inscrição individual que criou. Note que o estado da inscrição está **atribuído** e o ID do dispositivo está listado.

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir as etiquetas duplas de identificação do dispositivo e dispositivo ao mesmo tempo que disponibiliza o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos ou grupos individuais de dispositivos utilizando a gestão automática do dispositivo. Aprenda a [implantar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-at-scale.md) ou utilizando o [Azure CLI](how-to-deploy-cli-at-scale.md).
