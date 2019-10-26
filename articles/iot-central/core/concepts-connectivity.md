---
title: Conectividade do dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade do dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 99efad08819ffb28ebcbd8462a1085b8e69ba003
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951786"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividade do dispositivo no Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo apresenta os principais conceitos relacionados à conectividade do dispositivo no Microsoft Azure IoT Central.

O Azure IoT Central usa o [DPS (serviço de provisionamento de dispositivos) do Hub IOT do Azure](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) para gerenciar todo o registro e a conexão do dispositivo.

O uso do DPS permite:

- IoT Central para dar suporte à integração e à conexão de dispositivos em escala.
- Você deve gerar credenciais de dispositivo e configurar os dispositivos offline sem registrar os dispositivos por meio da interface do usuário do IoT Central.
- Dispositivos para se conectar usando SAS (assinaturas de acesso compartilhado).
- Dispositivos para se conectar usando certificados X. 509 padrão do setor.
- Você usa suas próprias IDs de dispositivo para registrar dispositivos no IoT Central. O uso de suas próprias IDs de dispositivo simplifica a integração com sistemas de Back-Office existentes.
- Uma maneira única e consistente de conectar dispositivos a IoT Central.

Este artigo descreve os quatro casos de uso a seguir:

- [Conectar rapidamente um único dispositivo usando SAS](#connect-a-single-device)
- [Conectar dispositivos em escala usando SAS](#connect-devices-at-scale-using-sas)
- [Conectar dispositivos em escala usando certificados X. 509](#connect-devices-using-x509-certificates) esta é a abordagem recomendada para ambientes de produção.
- [Conectar-se sem primeiro registrar os dispositivos](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Conectar um único dispositivo

Essa abordagem é útil quando você está experimentando IoT Central ou dispositivos de teste. Você pode usar as informações de conexão do dispositivo do seu aplicativo IoT Central para conectar um dispositivo ao seu aplicativo IoT Central usando o DPS (serviço de provisionamento de dispositivos). Você pode encontrar o código do cliente do dispositivo DPS de exemplo para os seguintes idiomas:

- [C\#](./howto-connect-raspberry-pi-csharp.md)
- [Node.js](./howto-connect-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>Conectar dispositivos em escala usando SAS

Para conectar dispositivos a IoT Central em escala usando SAS, você precisa registrar e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registrar dispositivos em massa

Para registrar um grande número de dispositivos com seu aplicativo IoT Central, use um arquivo CSV para [importar IDs de dispositivo e nomes de dispositivo](howto-manage-devices.md#import-devices).

Para recuperar as informações de conexão dos dispositivos importados, [exporte um arquivo CSV de seu aplicativo IOT central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Para saber como você pode conectar dispositivos sem primeiro registrá-los no IoT Central, consulte [conectar sem primeiro registrar os dispositivos](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Configurar seus dispositivos

Use as informações de conexão do arquivo de exportação no código do dispositivo para permitir que seus dispositivos se conectem e enviem dados para o IoT para seu aplicativo IoT Central. Para obter mais informações sobre como conectar dispositivos, consulte [próximas etapas](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Conectar dispositivos usando certificados X. 509

Em um ambiente de produção, o uso de certificados X. 509 é o mecanismo de autenticação de dispositivo recomendado para IoT Central. Para saber mais, confira [autenticação de dispositivo usando certificados de autoridade de certificação X. 509](../../iot-hub/iot-hub-x509ca-overview.md).

As etapas a seguir descrevem como conectar dispositivos a IoT Central usando certificados X. 509:

1. No aplicativo IoT Central, _adicione e verifique o certificado X. 509 intermediário ou raiz_ que você está usando para gerar certificados de dispositivo:

    - Navegue até **administração > conexão de dispositivo > certificados (X. 509)** e adicione a raiz ou o certificado intermediário X. 509 que você está usando para gerar os certificados de dispositivo de folha.

      ![Definições de ligação](media/concepts-connectivity/connection-settings.png)

      Se você tiver uma violação de segurança ou se o certificado principal estiver definido como expirar, use o certificado secundário para reduzir o tempo de inatividade. Você pode continuar a provisionar dispositivos usando o certificado secundário enquanto atualiza o certificado primário.

    - Verificar a propriedade do certificado garante que o carregador do certificado tenha a chave privada do certificado. Para verificar o certificado:
        - Selecione o botão ao lado do **código de verificação** para gerar um código.
        - Crie um certificado de verificação X. 509 com o código de verificação gerado na etapa anterior. Salve o certificado como um arquivo. cer.
        - Carregue o certificado de verificação assinado e selecione **verificar**.

          ![Definições de ligação](media/concepts-connectivity/verify-cert.png)

1. Use um arquivo CSV para _importar e registrar dispositivos_ no seu aplicativo IOT central.

1. _Configure seus dispositivos._ Gere os certificados de folha usando o certificado raiz carregado. Use a **ID do dispositivo** como o valor CNAME nos certificados folha. A ID do dispositivo deve ter todas as letras minúsculas. Em seguida, programe seus dispositivos com as informações do serviço de provisionamento. Quando um dispositivo é ativado pela primeira, ele recupera suas informações de conexão para seu aplicativo IoT Central do DPS.

### <a name="further-reference"></a>Referência adicional

- Exemplo de implementação para [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Cliente de dispositivo de exemplo em C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Somente para fins de teste

Somente para teste, você pode usar esses utilitários para gerar certificados de autoridade de certificação e certificados de dispositivo.

- Se você estiver usando um dispositivo DevKit, essa [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gerará um certificado de autoridade de certificação que você pode adicionar ao seu aplicativo IOT central para verificar os certificados.

- Use essa [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para:
  - Crie uma cadeia de certificados. Siga a etapa 2 no artigo do GitHub.
  - Salve os certificados como arquivos. cer para carregar em seu aplicativo IoT Central.
  - Use o código de verificação do aplicativo IoT Central para gerar o certificado de verificação. Siga a etapa 3 no artigo do GitHub.
  - Crie certificados folha para seus dispositivos usando suas IDs de dispositivo como um parâmetro para a ferramenta. Siga a etapa 4 no artigo do GitHub.

## <a name="connect-without-registering-devices"></a>Conectar sem registrar dispositivos

Um cenário-chave IoT Central permite que os OEMs para fabricar dispositivos em massa que podem se conectar a um aplicativo IoT Central sem ser registrado primeiro. Um fabricante deve gerar credenciais adequadas e configurar os dispositivos na fábrica. Quando um dispositivo é ativado pela primeira vez, ele se conecta automaticamente a um aplicativo IoT Central. Um operador de IoT Central deve aprovar o dispositivo antes de poder enviar dados.

O diagrama a seguir descreve esse fluxo:

![Definições de ligação](media/concepts-connectivity/device-connection-flow1.png)

As etapas a seguir descrevem esse processo mais detalhadamente. As etapas diferem um pouco dependendo se você estiver usando certificados SAS ou X. 509 para autenticação de dispositivo:

1. Defina as configurações de conexão:

    - **Certificados X. 509:** [adicione e verifique o certificado raiz/intermediário](#connect-devices-using-x509-certificates) e use-o para gerar os certificados de dispositivo na etapa a seguir.
    - **SAS:** Copie a chave primária. Essa é a chave de SAS do grupo para o aplicativo IoT Central. Use a chave para gerar as chaves SAS do dispositivo na etapa a seguir.
    ![](media/concepts-connectivity/connection-settings-sas.png) SAS de configurações de conexão

1. Gerar suas credenciais de dispositivo
    - **Certificados X. 509:** Gere os certificados de folha para seus dispositivos usando o certificado raiz ou intermediário que você adicionou ao seu aplicativo IoT Central. Certifique-se de usar a ID do **dispositivo** em minúsculas como o CNAME nos certificados folha. Somente para fins de teste, use essa [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para gerar certificados de dispositivo.
    - **SAS:** Use esta [ferramenta de linha de comando](https://www.npmjs.com/package/dps-keygen) para gerar chaves SAS do dispositivo. Use a **chave primária** do grupo da etapa anterior. A ID do dispositivo deve estar em letras minúsculas.

      Para instalar o [utilitário gerador de chaves](https://github.com/Azure/dps-keygen), execute o seguinte comando:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Para gerar uma chave de dispositivo a partir da chave primária de SAS do grupo, execute o seguinte comando:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Para configurar seus dispositivos, atualize cada dispositivo com a **ID do escopo**, a **ID do dispositivo**e o certificado de **dispositivo X. 509** ou a **chave SAS**.

1. Em seguida, ligue o dispositivo para que ele se conecte ao seu aplicativo IoT Central. Quando você alterna um dispositivo, ele se conecta primeiro ao DPS para recuperar suas informações de registro IoT Central.

1. Inicialmente, o dispositivo conectado aparece como um **dispositivo não associado** na página **Device Explorer** . O status de provisionamento do dispositivo é **registrado**. **Associe** o dispositivo ao modelo de dispositivo apropriado e aprove o dispositivo para se conectar ao seu aplicativo IOT central. O dispositivo pode então recuperar uma cadeia de conexão do Hub IoT e começar a enviar dados. O provisionamento do dispositivo agora está concluído e o status de provisionamento agora está **provisionado**.

## <a name="provisioning-status"></a>Status de provisionamento

Quando um dispositivo real se conecta ao seu aplicativo IoT Central, seu status de provisionamento é alterado da seguinte maneira:

1. O status de provisionamento do dispositivo é **registrado**primeiro. Esse status significa que o dispositivo foi criado em IoT Central e tem uma ID de dispositivo. Um dispositivo é registrado quando:
    - Um novo dispositivo real é adicionado na página de **Device Explorer** .
    - Um conjunto de dispositivos é adicionado usando a **importação** na página **Device Explorer** .
    - Um dispositivo não foi registrado manualmente na página **Device Explorer** , mas conectado com credenciais válidas e está visível como um dispositivo não **associado** na página **Device Explorer** .

1. O status de provisionamento do dispositivo muda para **provisionado** quando o dispositivo conectado ao seu aplicativo IOT central com credenciais válidas conclui a etapa de provisionamento. Nesta etapa, o dispositivo recupera uma cadeia de conexão do Hub IoT. Agora, o dispositivo pode se conectar ao Hub IoT e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo é bloqueado, ele não pode enviar dados para seu aplicativo IoT Central. Os dispositivos bloqueados têm um status de provisionamento de **bloqueado**. Um operador deve redefinir o dispositivo antes que ele possa retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o status de provisionamento retorna ao seu valor anterior, **registrado** ou **provisionado**.

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs de dispositivo do Azure oferecem a maneira mais fácil de implementar o código do dispositivo. Os seguintes SDKs de dispositivo estão disponíveis:

- [SDK do IoT do Azure para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do IoT do Azure para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para node. js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do Azure IoT para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos do SDK e conectividade do Hub IoT

Todas as comunicações de dispositivo com o Hub IoT usam as seguintes opções de conectividade do Hub IoT:

- [Mensagens do dispositivo para a nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivo gêmeos](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como o Azure IoT Central recursos do dispositivo são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Medida: telemetria | Mensagens do dispositivo para a nuvem |
| Propriedades do dispositivo | Propriedades relatadas do dispositivo. |
| Definições | Propriedades relatadas e desejadas do dispositivo. |

Para saber mais sobre como usar os SDKs do dispositivo, consulte um dos artigos a seguir para obter um exemplo de código:

- [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs.md)
- [Conectar um dispositivo Raspberry Pi ao aplicativo IoT Central do Azure](howto-connect-raspberry-pi-python.md)
- [Conecte um dispositivo do kit do representante ao seu aplicativo IOT central do Azure](howto-connect-devkit.md).

### <a name="protocols"></a>Protocolos

Os SDKs de dispositivo dão suporte aos seguintes protocolos de rede para se conectar a um hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses protocolos de diferença e orientação sobre como escolher um, consulte [escolher um protocolo de comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, você poderá usar Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para descarregar o processamento para a borda do aplicativo de IoT Central do Azure.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e sua IoT Central do Azure são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que se conecta a qualquer um dos pontos de extremidade do Hub IoT voltados para o dispositivo. Para evitar a troca de credenciais pela conexão, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [controlar o acesso ao Hub IOT](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre a conectividade do dispositivo no Azure IoT Central, aqui estão as próximas etapas sugeridas:

- [Preparar e conectar um dispositivo DevKit](howto-connect-devkit.md)
- [Preparar e ligar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs.md)
- [SDK do C: Provisionando o SDK do cliente do dispositivo](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
