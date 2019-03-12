---
title: Conectividade do dispositivo no Azure IoT Central | Documentos da Microsoft
description: Este artigo apresenta os principais conceitos relacionados com a conectividade do dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 02/28/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eb16778e0e7050dfda92fcc1b92afffe5860268d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767588"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividade do dispositivo no Azure IoT Central

Este artigo apresenta os principais conceitos relacionados com a conectividade do dispositivo no Microsoft Azure IoT Central.

O Azure IoT Central utiliza a [serviço aprovisionamento de dispositivos do Azure IoT Hub (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) para gerir todos os registo de dispositivos e a ligação.

A utilização de pontos de distribuição permite:

- Centro de IoT para suportar a integração e dispositivos de ligação à escala.
- A gerar o dispositivo de credenciais e configurar os dispositivos offline sem registar os dispositivos através do Centro de IoT da interface do Usuário.
- Os dispositivos estabeleçam ligação através do partilhados assinaturas de acesso (SAS).
- Dispositivos estabeleçam ligação através de certificados X.509 de norma da indústria.
- -O a utilizar o seu próprio identificações de dispositivo para registar dispositivos no IoT Central. Usar seus próprios identificações de dispositivo simplifica a integração com sistemas de back office existentes.
- Uma forma única e consistente para ligar dispositivos ao IoT Central.

Este artigo que mostra como descreve os casos de utilização de quatro seguintes:

1. [Ligue-se rapidamente um único dispositivo através da SAS](#connect-a-single-device)
1. [Ligar dispositivos à escala através da SAS](#connect-devices-at-scale-using-shared-access-signatures)
1. [Ligar dispositivos à escala utilizando certificados X.509](#connect-devices-using-x509-certificates) essa é a abordagem recomendada para ambientes de produção.
1. [Ligar-se sem o registo de dispositivos primeiro](#connect-without-first-registering-devices) 

## <a name="connect-a-single-device"></a>Ligar um dispositivo único

Essa abordagem é útil quando está experimentando o Centro de IoT ou dispositivos de teste.

Para ligar um único dispositivo ao IoT Central através da SAS, siga estes passos:

1. Para adicionar o dispositivo real, navegue para o **Device Explorer**, escolha um modelo de dispositivo e selecione **+ novo > Real**:
    - Introduza o seu próprio (minúsculas) **ID de dispositivo** ou utilizar o ID de sugerida.
    - Introduza um **nome do dispositivo** ou utilize o nome sugerido.

      ![Adicionar Dispositivo](media/concepts-connectivity/add-device.png)

1. Para obter as informações de ligação do dispositivo, selecione **Connect** na página do dispositivo. Terá do **ID de âmbito**, **ID do dispositivo**, e **chave primária** valores:
    - Cada aplicação IoT Central tem uma [ID de âmbito](../iot-dps/concepts-device.md#id-scope) que é gerado por pontos de distribuição.
    - [ID de dispositivo](../iot-dps/concepts-device.md#device-id) é o ID exclusivo do dispositivo. O dispositivo é armazenado na [registo de identidade](../iot-hub/iot-hub-devguide-identity-registry.md).
    - **Chave primária** é um token SAS gerado pelo centro de IoT para o dispositivo.

      ![Detalhes da ligação](media/concepts-connectivity/device-connect.png)

Utilize as informações de ligação no seu código de dispositivo para ativar o seu dispositivo para se ligar e enviar dados para a IoT para a sua aplicação IoT Central. Para obter mais informações sobre como ligar dispositivos, consulte [próximos passos](#next-steps).

## <a name="connect-devices-at-scale-using-sas"></a>Ligar dispositivos à escala através da SAS

Para ligar dispositivos ao IoT Central à escala através da SAS, precisa de registar e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registar dispositivos em massa

Para registar um grande número de dispositivos com a sua aplicação IoT Central, utilize um ficheiro CSV para [importar identificações de dispositivo e os nomes de dispositivo](howto-manage-devices.md#import-devices).

Para obter as informações de ligação para os dispositivos importados, [exportar um ficheiro CSV a partir da sua aplicação do Centro de IoT](howto-manage-devices.md#export-devices).

> [!NOTE]
> Para saber como pode ligar dispositivos sem Registro primeiro no Centro de IoT, veja [ligar sem o registo de dispositivos primeiro](#connect-without-first-registering-devices).

### <a name="set-up-your-devices"></a>Configurar seus dispositivos

Utilize as informações de ligação do ficheiro de exportação em seu código de dispositivo para permitir que os seus dispositivos para se ligar e enviar dados para a IoT para a sua aplicação IoT Central. Para obter mais informações sobre como ligar dispositivos, consulte [próximos passos](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Ligar dispositivos utilizando certificados X.509

Num ambiente de produção, utilizando certificados X.509 é o mecanismo de autenticação do dispositivo recomendada para o IoT Central. Para obter mais informações, consulte [autenticação de dispositivo usando certificados de AC X.509](../iot-hub/iot-hub-x509ca-overview.md).

Os passos seguintes descrevem como ligar dispositivos ao IoT Central, utilizando certificados X.509:

1. Em seu aplicativo de IoT Central _adicionar e verificar o intermediário ou uma raiz de certificado X.509_ estiver a utilizar para gerar certificados de dispositivo:

    - Navegue para **administração > ligação do dispositivo > certificados (X.509)** e adicione X.509 certificado raiz ou intermediário estiver a utilizar para gerar os certificados de dispositivos de folha.

      ![Definições de ligação](media/concepts-connectivity/connection-settings.png)

      Se ocorrer uma violação de segurança ou o certificado principal é definido para expirar, utilize o certificado secundário para reduzir o tempo de inatividade. Pode continuar a aprovisionar dispositivos com o certificado secundário enquanto atualizar o certificado primário.

    - Verificar a propriedade do certificado garante que o carregador do certificado tem a chave privada do certificado. Para verificar o certificado:
        - Selecione o botão junto a **código de verificação** para gerar um código.
        - Crie um certificado de verificação de X.509 com o código de verificação gerados no passo anterior. Guarde o certificado como um ficheiro. cer.
        - Carregue o certificado de verificação assinado e selecione **Verifique se**.

          ![Definições de ligação](media/concepts-connectivity/verify-cert.png)

1. Utilizar um ficheiro CSV para _importar e registar dispositivos_ na sua aplicação IoT Central.

1. _Configure seus dispositivos._ Gere os certificados de folha a utilizar o certificado de raiz carregado. Utilize o **ID de dispositivo** como o valor CNAME nos certificados de folha. O ID do dispositivo deve ser todo em minúsculas. Em seguida, os dispositivos com as informações de serviço de aprovisionamento do programa. Quando um dispositivo é ativado para o primeiro, ele recupera as informações de ligação para a sua aplicação IoT Central de pontos de distribuição.

### <a name="further-reference"></a>Referência adicional

- Implementação de exemplo para [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Cliente de dispositivo de exemplo em C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Para fins de teste apenas a fins

Apenas para teste, pode utilizar estes utilitários para gerar certificados de AC e certificados de dispositivo.

- Se estiver a utilizar um dispositivo de DevKit, isso [ferramenta da linha de comandos](https://aka.ms/iotcentral-docs-dicetool) gera um certificado de AC que pode adicionar à sua aplicação do Centro de IoT para verificar os certificados.

- Utilize esta opção [ferramenta da linha de comandos](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para:
  - Crie uma cadeia de certificados. Siga o passo 2 do artigo do GitHub.
  - Guarde os certificados como ficheiros. cer para carregar para o seu aplicativo de IoT Central.
  - Utilize o código de verificação da aplicação do Centro de IoT para gerar o certificado de verificação. Siga o passo 3 do artigo do GitHub.
  - Crie certificados de folha para os seus dispositivos com suas identificações de dispositivo como um parâmetro para a ferramenta. Siga o passo 4 do artigo do GitHub.

## <a name="connect-without-registering-devices"></a>Ligar-se sem registar dispositivos

Um cenário de chave permite a IoT Central é para OEMs para fabricação em massa de dispositivos que podem ligar a uma aplicação de centro de IoT sem primeiro a ser registado. Um fabricante tem de gerar as credenciais adequadas e configurar os dispositivos na fábrica de. Quando um dispositivo liga pela primeira vez, ele se conecta automaticamente a uma aplicação IoT Central. Um operador de IoT Central tem de aprovar o dispositivo antes de ser stat envio de dados.

O diagrama seguinte descreve este fluxo:

![Definições de ligação](media/concepts-connectivity/device-connection-flow.png)

Os passos seguintes descrevem este processo em mais detalhes. As etapas diferem ligeiramente dependendo se está a utilizar certificados X.509 ou de SAS para autenticação do dispositivo:

1. Configure as definições de ligação:

    - **Certificados X.509:** [Adicionar e verificar o certificado de raiz/intermédio](#connect-devices-using-x509-certificates) e utilizá-lo para gerar os certificados de dispositivo no passo seguinte.
    - **SAS:** Copie a chave primária. Esta chave é a chave SAS de grupo para a aplicação do Centro de IoT. Utilize a chave para gerar as chaves SAS do dispositivo no passo seguinte.
    ![Definições de ligação SAS](media/concepts-connectivity/connection-settings-sas.png)

1. Gerar as suas credenciais do dispositivo
    - **Certificados X.509:** Gere os certificados de folha para os seus dispositivos com o certificado raiz ou intermediário que adicionou à sua aplicação IoT Central. Certifique-se de que utiliza o minúsculas **ID de dispositivo** como o CNAME nos certificados de folha. Para fins de testes, apenas, utilizam isto [ferramenta da linha de comandos](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para gerar certificados de dispositivo.
    - **SAS:** Utilize esta opção [ferramenta de linha de comando](https://www.npmjs.com/package/dps-keygen) para gerar as chaves de SAS do dispositivo. Utilize o grupo de **chave primária** do passo anterior. O ID do dispositivo tem de estar em minúsculas.

      Para instalar o [utilitário do gerador de chaves](https://github.com/Azure/dps-keygen), execute o seguinte comando:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Para gerar uma chave de dispositivo a partir da chave primária do grupo SAS, execute o seguinte comando:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Para configurar seus dispositivos, flash cada dispositivo com o **ID de âmbito**, **ID do dispositivo**, e **certificado de dispositivo X.509** ou **chave SAS**.

1. Em seguida, ative o dispositivo para se ligar à sua aplicação IoT Central. Quando muda num dispositivo, liga-se primeiro para pontos de distribuição para obter as respetivas informações de registo do Centro de IoT.

1. No dispositivo ligado exibido inicialmente como um **dispositivos não associados** sobre o **Device Explorer** página. O estado de aprovisionamento de dispositivos é **registado**. **Associar** o dispositivo para o modelo de dispositivo apropriado e aprovar o dispositivo para ligar à sua aplicação IoT Central. O dispositivo, em seguida, pode obter uma cadeia de ligação do IoT Hub e começar a enviar dados. Aprovisionamento de dispositivos está agora concluído e o estado de aprovisionamento é agora **aprovisionado**.

## <a name="provisioning-status"></a>Estado de aprovisionamento

Quando um dispositivo real se liga a sua aplicação do Centro de IoT, suas alterações de estado de aprovisionamento da seguinte forma:

1. O estado de aprovisionamento de dispositivos é a primeiro **registado**. Este estado significa que o dispositivo é criado no Centro de IoT e tem um ID de dispositivo. Um dispositivo é registado quando:
    - Um dispositivo real novo for adicionado no **Device Explorer** página.
    - Um conjunto de dispositivos é adicionado utilizando **importação** sobre o **Device Explorer** página.
    - Não foi registado manualmente num dispositivo a **Device Explorer** página, mas conectados com credenciais válidas e é visível como um **Unassociated** dispositivo na **Device Explorer**página.

1. O estado de aprovisionamento de dispositivos é alterado para **aprovisionado** quando o dispositivo que ligados à sua aplicação do Centro de IoT com credenciais válidas é concluído o passo de aprovisionamento. Neste passo, o dispositivo obtém uma cadeia de ligação do IoT Hub. O dispositivo pode agora ligar ao IoT Hub e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo está bloqueado, ele não pode enviar dados à sua aplicação IoT Central. Bloqueado dispositivos têm um Estado de aprovisionamento de **bloqueado**. Pode retomar a enviar dados, um operador tem de repor o dispositivo. Quando um operador desbloqueia um dispositivo, o estado de aprovisionamento devolve para o valor anterior, **registada** ou **aprovisionado**.

## <a name="get-a-connection-string"></a>Obter uma cadeia de ligação

Os passos seguintes descrevem como pode obter uma cadeia de ligação para um dispositivo:

1. Selecione **Connect** sobre o **Device Explorer** página para obter os detalhes de ligação: **Definir âmbito de ID**, **ID do dispositivo**, e **chave primária do dispositivo**:

    ![Detalhes da ligação](media/concepts-connectivity/device-connect.png)

1. Utilize o `dps-keygen` utilitário de linha de comandos para gerar uma cadeia de ligação:  Para instalar o [utilitário do gerador de chaves](https://github.com/Azure/dps-keygen), execute o seguinte comando:

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Para gerar uma cadeia de ligação, execute o seguinte comando:

    ```cmd/sh
    dps-keygen -di:<device_id> -dk:<device_key> -si:<scope_id>
    ```

## <a name="sdk-support"></a>Suporte do SDK

A oferta de SDKs de dispositivo do Azure a forma mais fácil para implementar seu código de dispositivo. Os SDKs do dispositivo seguintes estão disponíveis:

- [SDK do Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para node. js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK para .NET](https://github.com/azure/azure-iot-sdk-csharp)

Cada dispositivo liga-se através de uma cadeia de ligação exclusivo que identifica o dispositivo. Só pode ligar um dispositivo ao hub IoT em que está registado. Quando cria um dispositivo real na sua aplicação do Azure IoT Central, o aplicativo gera as informações necessárias construir uma cadeia de ligação utilizando `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funcionalidades do SDK e conectividade do IoT Hub

Todas as comunicações de dispositivos com o IoT Hub utiliza as seguintes opções de conectividade do IoT Hub:

- [Mensagens do dispositivo para a cloud](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Dispositivos duplos](../iot-hub/iot-hub-devguide-device-twins.md)

A tabela seguinte resume como o mapeamento de funcionalidades do dispositivo Azure IoT Central, para funcionalidades do IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Medida: Telemetria | Mensagens do dispositivo para a cloud |
| Propriedades do dispositivo | Propriedades comunicadas do dispositivo duplo |
| Definições | Dispositivo duplo desejado e propriedades comunicadas |

Para saber mais sobre como utilizar os SDKs de dispositivo, veja um dos seguintes artigos, por exemplo de código:

- [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs.md)
- [Ligar um dispositivo de Raspberry Pi a sua aplicação do Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Ligar um dispositivo de kit de representante à sua aplicação do Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protocolos

Os SDKs de dispositivo suporta os seguintes protocolos de rede para ligar a um hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses protocolos de diferença e orientações sobre como escolher um, consulte [escolher um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md).

Se o seu dispositivo não é possível utilizar qualquer um dos protocolos suportados, pode utilizar o Azure IoT Edge para a conversão de protocolo. IoT Edge suporta outros cenários de inteligência no edge para a descarga de processamento na periferia da aplicação do Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são encriptados. IoT Hub autentica todos os pedidos de um dispositivo que se liga a qualquer um dos pontos finais com acesso de dispositivo IoT Hub. Para evitar a troca de credenciais durante a transmissão, um dispositivo utiliza tokens assinados para autenticar. Para obter mais informações, consulte, [controlar o acesso ao IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre a conectividade do dispositivo no Azure IoT Central, aqui estão os passos sugeridos seguintes:

- [Preparar e ligar um dispositivo de DevKit](howto-connect-devkit.md)
- [Preparar e ligar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Ligar um cliente Node.js genérico à aplicação do Azure IoT Central](howto-connect-nodejs.md)
- [SDK DE C: SDK de cliente de dispositivo de aprovisionamento](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
