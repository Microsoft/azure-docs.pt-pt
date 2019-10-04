---
title: Provisionar automaticamente dispositivos com o DPS usando o atestado de chave simétrica-Azure IoT Edge | Microsoft Docs
description: Usar o atestado de chave simétrica para testar o provisionamento automático de dispositivos para Azure IoT Edge com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 53b1abca25119f4168aaf12a66c4347c53ed0a62
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828067"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Criar e provisionar um dispositivo IoT Edge usando o atestado de chave simétrica

Dispositivos do IoT Edge do Azure podem ser aprovisionado automaticamente com o [serviço aprovisionamento de dispositivos](../iot-dps/index.yml) tal como os dispositivos que não são habilitados no edge. Se não estiver familiarizado com o processo de aprovisionamento automático, reveja os [conceitos de aprovisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra como criar um registro individual de serviço de provisionamento de dispositivos usando o atestado de chave simétrica em um dispositivo IoT Edge com as seguintes etapas:

* Crie uma instância do IoT Hub dispositivo aprovisionamento DPS (serviço).
* Crie uma inscrição individual para o dispositivo.
* Instale o IoT Edge Runtime e conecte-se ao Hub IoT.

O atestado de chave simétrica é uma abordagem simples para autenticar um dispositivo com uma instância do serviço de provisionamento de dispositivos. Esse método de atestado representa uma experiência de "Olá, mundo" para desenvolvedores que são novos no provisionamento de dispositivos ou que não têm requisitos de segurança rígidos. O atestado de dispositivo usando um [TPM](../iot-dps/concepts-tpm-attestation.md) ou [certificados X. 509](../iot-dps/concepts-security.md#x509-certificates) é mais seguro e deve ser usado para requisitos de segurança mais rígidos.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT ativo
* Um dispositivo físico ou virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de aprovisionamento de dispositivos do IoT Hub

Criar uma nova instância do serviço de aprovisionamento de dispositivos do IoT Hub no Azure e ligá-lo ao seu hub IoT. Pode seguir as instruções em [configurar o Hub IoT DPS](../iot-dps/quick-setup-auto-provision.md).

Depois de ter a execução do serviço aprovisionamento de dispositivos, copie o valor da **âmbito do ID** da página de descrição geral. Utilize este valor quando configurar o runtime do IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolha uma ID de registro exclusiva para o dispositivo

Uma ID de registro exclusiva deve ser definida para identificar cada dispositivo. Você pode usar o endereço MAC, o número de série ou qualquer informação exclusiva do dispositivo.

Neste exemplo, usamos uma combinação de um endereço MAC e um número de série que formam a cadeia de caracteres a seguir para uma ID de registro.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Crie uma ID de registro exclusiva para seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e Dash ('-').

## <a name="create-a-dps-enrollment"></a>Criar uma inscrição de DPS

Use a ID de registro do dispositivo para criar um registro individual no DPS.

Quando criar uma inscrição em pontos de distribuição, terá a oportunidade para declarar uma **inicial estado do dispositivo duplo**. No dispositivo duplo, pode definir etiquetas para agrupar dispositivos com qualquer métrica que terá na sua solução, como o tipo de dispositivo, localização, ambiente ou região. Essas marcas são usadas para criar [implementações automáticas](how-to-deploy-monitor.md).

> [!TIP]
> Os registros de grupo também são possíveis ao usar o atestado de chave simétrica e envolvem as mesmas decisões que os registros individuais.

1. Na [portal do Azure](https://portal.azure.com), navegue até sua instância do serviço de provisionamento de dispositivos do Hub IOT.

1. Sob **configurações**, selecione **gerir inscrições**.

1. Selecione **adicionar inscrição individual** , em seguida, conclua os seguintes passos para configurar a inscrição:  

   1. Para **mecanismo**, selecione **chave simétrica**.

   1. Marque a caixa de seleção **gerar chaves automaticamente** .

   1. Forneça a **ID de registro** que você criou para seu dispositivo.

   1. Forneça uma **ID de dispositivo do Hub IOT** para seu dispositivo, se desejar. Pode usar as identificações de dispositivo para um dispositivo individual para implementação do módulo de destino. Se você não fornecer uma ID de dispositivo, a ID de registro será usada.

   1. Selecione **true** para declarar que o registro é para um dispositivo IOT Edge. Para um registro de grupo, todos os dispositivos devem ser IoT Edge dispositivos ou nenhum deles pode ser.

   1. Aceite o valor padrão da política de alocação do serviço de provisionamento de dispositivos para **saber como você deseja atribuir dispositivos a hubs** ou escolha um valor diferente que seja específico para esse registro.

   1. Escolha associada **IoT Hub** que pretende ligar o seu dispositivo. Você pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de alocação selecionada.

   1. Escolha **como você deseja que os dados do dispositivo sejam manipulados** ao reprovisionar quando os dispositivos solicitam o provisionamento após a primeira vez.

   1. Adicionar um valor de etiqueta para o **inicial estado do dispositivo duplo** se desejar. Pode utilizar etiquetas para grupos de dispositivos de destino para implementação do módulo. Por exemplo:

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

   1. Verifique se a **entrada habilitar** está definida como **habilitar**.

   1. Selecione **Guardar**.

Agora que um registro existe para esse dispositivo, o tempo de execução do IoT Edge pode provisionar automaticamente o dispositivo durante a instalação. Certifique-se de copiar o valor de **chave primária** do registro a ser usado ao instalar o IOT Edge Runtime ou se você pretende criar chaves de dispositivo para uso com um registro de grupo.

## <a name="derive-a-device-key"></a>Derivar uma chave de dispositivo

> [!NOTE]
> Esta seção é necessária somente se você estiver usando um registro de grupo.

Cada dispositivo usa sua chave de dispositivo derivada com sua ID de registro exclusiva para executar o atestado de chave simétrica com o registro durante o provisionamento. Para gerar a chave do dispositivo, use a chave que você copiou de seu registro de DPS para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) da ID de registro exclusiva para o dispositivo e converta o resultado no formato base64.

Não inclua a chave primária ou secundária do registro em seu código de dispositivo.

### <a name="linux-workstations"></a>Estações de trabalho do Linux

Se você estiver usando uma estação de trabalho do Linux, poderá usar o OpenSSL para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente.

Substitua o valor de **REG_ID** pela ID de registro do dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Estações de trabalho baseadas no Windows

Se você estiver usando uma estação de trabalho baseada no Windows, poderá usar o PowerShell para gerar a chave de dispositivo derivada, conforme mostrado no exemplo a seguir.

Substitua o valor da **chave** pela **chave primária** que você anotou anteriormente.

Substitua o valor de **REG_ID** pela ID de registro do dispositivo.

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

## <a name="install-the-iot-edge-runtime"></a>Instalar o runtime do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Seus componentes executadas em contentores e permitem-lhe implementar contentores adicionais no dispositivo para que pode executar o código na periferia.

Você precisará das seguintes informações ao provisionar seu dispositivo:

* O valor de **escopo da ID** de DPS
* A **ID de registro** do dispositivo que você criou
* A **chave primária** que você copiou do registro do DPS

> [!TIP]
> Para registros de grupo, você precisa da [chave derivada](#derive-a-device-key) de cada dispositivo em vez da chave de registro de DPS.

### <a name="linux-device"></a>Dispositivo Linux

Siga as instruções para a arquitetura do dispositivo. Certifique-se configurar o runtime do IoT Edge para aprovisionamento automático e não manual.

[Instalar o tempo de execução de Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

A seção no arquivo de configuração para provisionamento de chave simétrica tem esta aparência:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Substitua os valores de espaço `{scope_id}`reservado `{registration_id}`para, `{symmetric_key}` e pelos dados coletados anteriormente.

### <a name="windows-device"></a>Dispositivo Windows

Instale o IoT Edge tempo de execução no dispositivo para o qual você gerou uma chave de dispositivo derivada. Você configurará o tempo de execução de IoT Edge para o provisionamento automático, não manual.

Para obter informações mais detalhadas sobre como instalar o IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerenciar contêineres e atualizar IoT Edge, consulte [instalar o Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela do PowerShell no modo de administrador. Certifique-se de usar uma sessão AMD64 do PowerShell ao instalar o IoT Edge, não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se o computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução do Moby e o tempo de execução do IOT Edge. O padrão do comando é usar contêineres do Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem ser reiniciados automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar a reinicialização. Nesse caso, reinicie o dispositivo agora. Quando o dispositivo estiver pronto, execute o PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o tempo de execução de IOT Edge em seu computador. O comando usa como padrão o provisionamento manual com contêineres do Windows, a menos que você use o sinalizador `-Dps` para usar o provisionamento automático.

   Substitua os valores de espaço `{scope_id}`reservado `{registration_id}`para, `{symmetric_key}` e pelos dados coletados anteriormente.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Certifique-se a instalação com êxito

Se o tempo de execução foi iniciado com êxito, pode entrar no seu IoT Hub e iniciar a implementação de módulos do IoT Edge para o seu dispositivo. Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de execução instalado e iniciado com êxito.

### <a name="linux-device"></a>Dispositivo Linux

Verifique o estado do serviço IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os logs de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista de módulos em execução.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os logs de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista de módulos em execução.

```powershell
iotedge list
```

Você pode verificar se o registro individual criado no serviço de provisionamento de dispositivos foi usado. Navegue até a instância do serviço de provisionamento de dispositivos no portal do Azure. Abra os detalhes de registro para o registro individual que você criou. Observe que o status do registro é **atribuído** e a ID do dispositivo é listada.

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do serviço aprovisionamento de dispositivos permite-lhe definir o ID de dispositivo e etiquetas do dispositivo duplo ao mesmo tempo, como aprovisionar o novo dispositivo. Pode utilizar esses valores para dispositivos individuais ou grupos de dispositivos com a gestão de dispositivos automático de destino. Saiba como [implementar e monitorizar módulos em dimensionar no portal do Azure do IoT Edge](how-to-deploy-monitor.md) ou [com a CLI do Azure](how-to-deploy-monitor-cli.md).
