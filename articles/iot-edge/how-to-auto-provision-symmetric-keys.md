---
title: Dispositivo de provisão utilizando atestado de chave simétrica - Azure IoT Edge
description: Utilize atestado de chave simétrica para testar o fornecimento automático de dispositivos para a borda IoT Azure com o serviço de provisionamento de dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb5cbc2f2db0ba9f92a637c7e9a905d2f746880a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200828"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Criar e providenciar um dispositivo IoT Edge utilizando atestado de chave simétrica

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Os dispositivos Azure IoT Edge podem ser provisões automáticas utilizando o [Serviço de Provisionamento de Dispositivos,](../iot-dps/index.yml) tal como dispositivos que não estão ativados por arestas. Se não estiver familiarizado com o processo de provisão automática, reveja a visão geral do [provisionamento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Este artigo mostra-lhe como criar uma inscrição individual ou em grupo do Serviço de Provisionamento de Dispositivos utilizando o atestado de chave simétrica num dispositivo IoT Edge com os seguintes passos:

* Criar uma instância do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).
* Crie uma inscrição para o dispositivo.
* Instale o tempo de funcionação do IoT Edge e ligue-o ao Hub IoT.

A tecla de teclas simétricas é uma abordagem simples para autenticar um dispositivo com uma instância de Serviço de Provisionamento de Dispositivos. Este método de atestado representa uma experiência "Hello world" para desenvolvedores que são novos no fornecimento de dispositivos, ou não têm requisitos de segurança rigorosos. O atestado do dispositivo utilizando certificados [TPM](../iot-dps/concepts-tpm-attestation.md) ou [X.509](../iot-dps/concepts-x509-attestation.md) é mais seguro e deve ser utilizado para requisitos de segurança mais rigorosos.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT ativo
* Um dispositivo físico ou virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar o serviço de provisionamento de dispositivos IoT Hub

Crie uma nova instância do serviço de provisionamento de dispositivos IoT Hub em Azure e ligue-o ao seu hub IoT. Pode seguir as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).

Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** na página de visão geral. Utilize este valor quando configurar o tempo de execução IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Escolha um ID de registo único para o dispositivo

Deve ser definido um ID de registo único para identificar cada dispositivo. Pode utilizar o endereço MAC, número de série ou qualquer informação única do dispositivo.

Neste exemplo, utilizamos uma combinação de um endereço MAC e número de série formando a seguinte cadeia para um ID de registo: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Crie um ID de registo único para o seu dispositivo. Os caracteres válidos são alfanuméricos minúsculos e traços ('-').

## <a name="create-a-dps-enrollment"></a>Criar uma inscrição de DPS

Utilize o ID de registo do seu dispositivo para criar uma inscrição individual em DPS.

Quando cria uma inscrição em DPS, tem a oportunidade de declarar um **Estado Gémeo de Dispositivo Inicial.** No twin do dispositivo, pode definir tags para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas.](how-to-deploy-at-scale.md)

> [!TIP]
> As inscrições em grupo também são possíveis quando se utilizam o atestado de chave simétrica e envolvem as mesmas decisões que as matrículas individuais.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Definições**, **selecione Gerir as inscrições**.

1. **Selecione Adicionar inscrição individual** e, em seguida, completar os seguintes passos para configurar a inscrição:  

   1. Para **mecanismo**, selecione **Chave Simétrica**.

   1. Selecione a caixa **de verificação das teclas de geração automática.**

   1. Forneça o **ID de registo** que criou para o seu dispositivo.

   1. Forneça um **IoT Hub Device Device ID** para o seu dispositivo, se quiser. Pode utilizar iDs do dispositivo para direcionar um dispositivo individual para a implementação do módulo. Se não fornecer uma identificação do dispositivo, o ID de registo é usado.

   1. Selecione **True** para declarar que a inscrição é para um dispositivo IoT Edge. Para uma inscrição em grupo, todos os dispositivos devem ser dispositivos IoT Edge ou nenhum deles pode ser.

      > [!TIP]
      > No CLI Azure, pode criar uma [inscrição](/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e utilizar a bandeira ativada por **arestas** para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

   1. Aceite o valor padrão da política de atribuição do Serviço de Provisionamento de Dispositivos para **a forma como pretende atribuir dispositivos a hubs** ou escolher um valor diferente que seja específico desta inscrição.

   1. Escolha o **Hub IoT** ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   1. Escolha **como pretende que os dados do dispositivo sejam tratados no reeserção** quando os dispositivos solicitarem o provisionamento após a primeira vez.

   1. Adicione um valor de etiqueta ao **Estado Gémeo do Dispositivo Inicial,** se quiser. Pode utilizar tags para direcionar grupos de dispositivos para a implementação do módulo. Por exemplo:

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

   1. **Certifique-se de que a entrada** ativa está definida para **ativar**.

   1. Selecione **Guardar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução IoT Edge pode automaticamente abastecer o dispositivo durante a instalação. Certifique-se de copiar o valor da **chave primária** da sua inscrição para usar ao instalar o tempo de execução IoT Edge, ou se vai criar chaves do dispositivo para utilização com uma inscrição em grupo.

## <a name="derive-a-device-key"></a>Derivar uma chave de dispositivo

> [!NOTE]
> Esta secção só é necessária se utilizar uma inscrição em grupo.

Cada dispositivo utiliza a sua chave de dispositivo derivada com o seu ID de registo único para executar o atestado de chave simétrica com a inscrição durante o provisionamento. Para gerar a chave do dispositivo, utilize a chave que copiou da sua inscrição DPS para calcular um [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) do ID de registo único para o dispositivo e converter o resultado em formato Base64.

Não inclua a chave primária ou secundária da sua inscrição no código do dispositivo.

### <a name="linux-workstations"></a>Estações de trabalho linux

Se estiver a utilizar uma estação de trabalho Linux, pode utilizar o openssl para gerar a chave do dispositivo derivado, como mostra o exemplo seguinte.

Substitua o valor da **CHAVE** pela **Chave Primária** que observou anteriormente.

Substitua o valor da **REG_ID** pelo ID de registo do seu dispositivo.

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

Se estiver a utilizar uma estação de trabalho baseada no Windows, pode utilizar o PowerShell para gerar a chave do dispositivo derivado, como mostra o exemplo a seguir.

Substitua o valor da **CHAVE** pela **Chave Primária** que observou anteriormente.

Substitua o valor da **REG_ID** pelo ID de registo do seu dispositivo.

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

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda.

Siga os passos no [Instale o tempo de execução Azure IoT Edge](how-to-install-iot-edge.md)e, em seguida, volte a este artigo para disposir o dispositivo.

## <a name="configure-the-device-with-provisioning-information"></a>Configure o dispositivo com informações de provisionamento

Uma vez instalado o tempo de funcionamento no seu dispositivo, configuure o dispositivo com as informações que utiliza para ligar ao Serviço de Provisionamento de Dispositivos e ao IoT Hub.

Tenha as seguintes informações prontas:

* O valor do **ID Scope** DPS
* O **ID de registo do** dispositivo que criou
* A **Chave Primária** que copiou da inscrição do DPS

> [!TIP]
> Para as inscrições em grupo, precisa da [chave derivada de](#derive-a-device-key) cada dispositivo em vez da chave primária de inscrição DPS.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Abra o ficheiro de configuração no dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Encontre a secção de configurações de provisionamento do ficheiro. Descompromecer as linhas para o provisionamento de chaves simétricas DPS e certificar-se de que quaisquer outras linhas de provisionamento são comentadas.

   A `provisioning:` linha não deve ter espaço branco anterior, e os itens aninhados devem ser recortados por dois espaços.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Atualize os valores de `scope_id` , e com as `registration_id` `symmetric_key` informações do seu DPS e dispositivo.

1. Opcionalmente, utilize as `always_reprovision_on_startup` linhas ou `dynamic_reprovisioning` linhas para configurar o comportamento de reprovisionamento do seu dispositivo. Se um dispositivo estiver definido para reprovisionar no arranque, tentará sempre fornecer primeiro com DPS e depois voltar para a cópia de segurança de provisionamento se isso falhar. Se um dispositivo estiver definido para reprovisionar-se dinamicamente, o IoT Edge reiniciará e reprovisionará se for detetado um evento de reprovisionamento. Para obter mais informações, consulte [conceitos de reprovisionamento do dispositivo IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Reinicie o tempo de funcionamento do IoT Edge de modo a que detete todas as alterações de configuração que fez no dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Crie um ficheiro de configuração para o seu dispositivo com base num ficheiro de modelo que é fornecido como parte da instalação IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra o ficheiro de configuração no dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Encontre a secção **de Provisionamento** do ficheiro. Descompromecer as linhas de provisionamento de DPS com chave simétrica, e certificar-se de que quaisquer outras linhas de provisionamento são comentadas.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. Atualize os valores de `id_scope` , e com as `registration_id` `symmetric_key` informações do seu DPS e dispositivo.

   O parâmetro chave simétrico pode aceitar um valor de uma chave inline, um URI de ficheiro ou um PKCS#11 URI. Descomprometar apenas uma linha de chave simétrica, com base no formato que está a usar.

   Se utilizar algum PKCS#11 URIs, encontre a secção **PKCS#11** no ficheiro config e forneça informações sobre a sua configuração PKCS#11.

1. Guarde e feche o ficheiro config.toml.

1. Aplique as alterações de configuração que fez no IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Dispositivo Windows

1. Abra uma janela PowerShell no modo de administrador. Certifique-se de que utiliza uma sessão AMD64 de PowerShell ao instalar ioT Edge, não PowerShell (x86).

1. O comando **Initialize-IoTEdge** configura o tempo de funcionamento do IoT Edge na sua máquina. O comando predefine o fornecimento manual com recipientes Windows, por isso utilize a bandeira para utilizar o `-DpsSymmetricKey` provisionamento automático com autenticação de chave simétrica.

   Substitua os valores de espaço reservado para `{scope_id}` `{registration_id}` , e pelos `{symmetric_key}` dados recolhidos anteriormente.

   Adicione o `-ContainerOs Linux` parâmetro se estiver a utilizar recipientes Linux no Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Se o tempo de execução tiver começado com sucesso, pode entrar no seu Hub IoT e começar a implantar módulos IoT Edge no seu dispositivo. Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de funcionamento foi instalado e começou com sucesso.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Verifique o estado do serviço IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os registos de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Listar módulos de execução.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Verifique o estado do serviço IoT Edge.

```cmd/sh
sudo iotedge system status
```

Examine os registos de serviço.

```cmd/sh
sudo iotedge system logs
```

Listar módulos de execução.

```cmd/sh
sudo iotedge list
```

:::moniker-end

### <a name="windows-device"></a>Dispositivo Windows

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registos de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Listar módulos de execução.

```powershell
iotedge list
```

Pode verificar se foi utilizada a inscrição individual que criou no Serviço de Provisionamento de Dispositivos. Navegue para a sua instância de Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes das inscrições para a inscrição individual que criou. Note que o estado da inscrição é **atribuído** e o ID do dispositivo está listado.

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir o ID do dispositivo e as etiquetas gémeas do dispositivo ao mesmo tempo que fornece o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos individuais ou grupos de dispositivos utilizando a gestão automática do dispositivo. Aprenda a [implementar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-at-scale.md) ou utilizando o [Azure CLI](how-to-deploy-cli-at-scale.md).
