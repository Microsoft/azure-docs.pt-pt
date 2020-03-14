---
title: Conectividade do dispositivo em Azure IoT Central [ Central De IoT] Microsoft Docs
description: Este artigo introduz conceitos-chave relativos à conectividade do dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e67a8f6b9cc175932b09e6f576148656dd9da9ba
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298823"
---
# <a name="get-connected-to-azure-iot-central"></a>Fique ligado à Central Azure IoT

Este artigo introduz conceitos-chave relacionados com a conectividade do dispositivo na Microsoft Azure IoT Central.

A Azure IoT Central utiliza o serviço de provisionamento de [dispositivos Hub Azure IoT (DPS)](../../iot-dps/about-iot-dps.md) para gerir todo o registo e ligação do dispositivo.

A utilização de DPS permite:

- IoT Central para apoiar dispositivos de embarque e ligação em escala.
- Você gera credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Dispositivos para se conectarem utilizando assinaturas de acesso partilhado (SAS).
- Dispositivos para se conectarem utilizando certificados X.509 padrão da indústria.
- Você usa as suas próprias iDs de dispositivo para registar dispositivos na IoT Central. A utilização dos seus próprios IDs de dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Este artigo descreve os seguintes casos de utilização:

- [Ligue rapidamente um único dispositivo utilizando o SAS](#connect-a-single-device)
- [Ligar dispositivos à escala utilizando o SAS](#connect-devices-at-scale-using-sas)
- [Ligue os dispositivos em escala utilizando certificados X.509,](#connect-devices-using-x509-certificates) esta é a abordagem recomendada para ambientes de produção.
- [Ligar sem primeiro sintetar dispositivos](#connect-without-registering-devices)
- [Conecte dispositivos utilizando funcionalidades IoT Plug e Play (pré-visualização)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Ligar um único dispositivo

Esta abordagem é útil quando se está a experimentar com a IoT Central ou dispositivos de teste. Pode utilizar as informações de ligação do dispositivo a partir da sua aplicação IoT Central para ligar um dispositivo à sua aplicação IoT Central utilizando o Serviço de Provisionamento de Dispositivos (DPS). Pode encontrar o código cliente do dispositivo DPS para os seguintes idiomas:

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

## <a name="connect-devices-at-scale-using-sas"></a>Ligar dispositivos à escala utilizando o SAS

Para ligar os dispositivos à IoT Central à escala utilizando o SAS, é necessário registar-se e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registar dispositivos a granel

Para registar um grande número de dispositivos com a sua aplicação IoT Central, utilize um ficheiro CSV para [importar iDs de dispositivos e nomes](howto-manage-devices.md#import-devices)de dispositivos .

Para obter as informações de ligação para os dispositivos importados, [exporte um ficheiro CSV da sua aplicação IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Para saber como pode ligar os dispositivos sem antes registrá-los na IoT Central, consulte [Connect sem primeiro sintetizar dispositivos](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Instale os seus dispositivos

Utilize as informações de ligação do ficheiro de exportação no código do seu dispositivo para permitir que os seus dispositivos conectem e enviem dados para IoT para a sua aplicação IoT Central. Para obter mais informações sobre dispositivos de ligação, consulte [Os próximos passos](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Ligar dispositivos utilizando certificados X.509

Em ambiente de produção, a utilização de certificados X.509 é o mecanismo recomendado de autenticação do dispositivo para a IoT Central. Para saber mais, consulte a [Autenticação do Dispositivo utilizando certificados X.509 CA](../../iot-hub/iot-hub-x509ca-overview.md).

As seguintes etapas descrevem como ligar os dispositivos à IoT Central utilizando certificados X.509:

1. Na sua aplicação IoT Central, _adicione e verifique o certificado intermédio ou raiz X.509_ que está a usar para gerar certificados de dispositivo:

    - Navegue para **Administration > Device Connection > Certificates (X.509)** e adicione x.509 root ou certificado intermédio que está a usar para gerar os certificados do dispositivo de folha.

      ![Definições de ligação](media/concepts-get-connected/connection-settings.png)

      Se tiver uma falha de segurança ou se o seu certificado primário estiver definido para expirar, utilize o certificado secundário para reduzir o tempo de inatividade. Pode continuar a fornecer dispositivos utilizando o certificado secundário enquanto atualiza o certificado primário.

    - A verificação da propriedade do certificado garante que o carregador do certificado tem a chave privada do certificado. Para verificar o certificado:
        - Selecione o botão ao lado do Código de **Verificação** para gerar um código.
        - Crie um certificado de verificação X.509 com o código de verificação gerado no passo anterior. Guarde o certificado como ficheiro .cer.
        - Faça upload do certificado de verificação assinado e selecione **Verificar**.

          ![Definições de ligação](media/concepts-get-connected/verify-cert.png)

1. Utilize um ficheiro CSV para _importar e registar dispositivos_ na sua aplicação IoT Central.

1. _Instale os seus dispositivos._ Gere os certificados de folha utilizando o certificado de raiz carregado. Utilize o ID do **dispositivo** como valor CNAME nos certificados de folha. O ID do dispositivo deve ser todo minúsculo. Em seguida, programe os seus dispositivos com informações de serviço de fornecimento. Quando um dispositivo é ligado pela primeira vez, ele recupera as suas informações de ligação para a sua aplicação IoT Central a partir de DPS.

### <a name="further-reference"></a>Outra referência

- Implementação da amostra para [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Cliente de dispositivo de amostra em C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Para efeitos de teste apenas

Apenas para testes, pode utilizar estes utilitários para gerar certificados ca e certificados de dispositivo.

- Se estiver a utilizar um dispositivo DevKit, esta [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gera um certificado CA que pode adicionar à sua aplicação IoT Central para verificar os certificados.

- Utilize esta [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para:
  - Criar uma cadeia de certificados. Siga o passo 2 no artigo do GitHub.
  - Guarde os certificados como ficheiros .cer para fazer upload para a sua aplicação IoT Central.
  - Utilize o código de verificação da aplicação IoT Central para gerar o certificado de verificação. Siga o passo 3 no artigo do GitHub.
  - Crie certificados de folhas para os seus dispositivos utilizando os seus IDs do dispositivo como parâmetro para a ferramenta. Siga o passo 4 no artigo do GitHub.

## <a name="connect-without-registering-devices"></a>Ligar sem registar dispositivos

Um cenário-chave que a IoT Central permite é que os OEMs fabricem dispositivos de fabrico em massa que possam ligar-se a uma aplicação IoT Central sem serem registados primeiro. Um fabricante deve gerar credenciais adequadas e configurar os dispositivos na fábrica. Quando um dispositivo se liga pela primeira vez, liga-se automaticamente a uma aplicação IoT Central. Um operador Central IoT deve aprovar o dispositivo antes de poder apresentar dados de envio.

O diagrama seguinte descreve este fluxo:

![Definições de ligação](media/concepts-get-connected/device-connection-flow1.png)

Os seguintes passos descrevem este processo com mais detalhes. Os passos diferem ligeiramente dependendo se está a utilizar certificados SAS ou X.509 para autenticação do dispositivo:

1. Configure as definições de ligação:

    - **X.509 Certificados:** [Adicione e verifique o certificado raiz/intermédio](#connect-devices-using-x509-certificates) e use-o para gerar os certificados do dispositivo no passo seguinte.
    - **SAS:** Copie a chave principal. Esta chave é a chave SAS do grupo para a aplicação IoT Central. Utilize a tecla para gerar as teclas SAS do dispositivo no passo seguinte.
    definições de ligação ![SAS](media/concepts-get-connected/connection-settings-sas.png)

1. Gere as credenciais do seu dispositivo
    - **Certificados X.509:** Gere os certificados de folha sinuoso para os seus dispositivos utilizando o certificado raiz ou intermédio que adicionou à sua aplicação IoT Central. Certifique-se de que utiliza o ID do **dispositivo** inferior como CNAME nos certificados de folha. Apenas para efeitos de teste, utilize esta [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para gerar certificados de dispositivo.
    - **SAS:** Utilize esta ferramenta de linha de [comando](https://www.npmjs.com/package/dps-keygen) para gerar teclas SAS do dispositivo. Utilize a **Chave Primária** do grupo do passo anterior. O ID do dispositivo deve ser minúsculo.

      Para instalar o utilitário gerador de [chaves,](https://github.com/Azure/dps-keygen)executar o seguinte comando:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Para gerar uma chave de dispositivo a partir da chave primária do grupo SAS, executar o seguinte comando:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Para configurar os seus dispositivos, mostre cada dispositivo com o ID de **alcance,** **id do dispositivo**e o certificado de dispositivo **X.509** ou **a tecla SAS**.

1. Em seguida, ligue o dispositivo para que este se ligue à sua aplicação IoT Central. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as suas informações de registo IoT Central.

1. O dispositivo ligado aparece inicialmente como **Não Associado** na página **dispositivos.** O estado de fornecimento do dispositivo está **registado**. **Emigra** o dispositivo para o modelo de dispositivo apropriado e aprove o dispositivo para se ligar à sua aplicação IoT Central. O dispositivo pode então recuperar uma cadeia de ligação do IoT Hub e começar a enviar dados. O fornecimento de dispositivos está agora completo e o estado de provisionamento está agora **provisionado.**

## <a name="individual-enrollment-based-device-connectivity"></a>Conectividade individual do dispositivo baseado em inscrições

Para os clientes que conectam dispositivos que tenham credenciais de autenticação que são por dispositivo individual é a opção. Uma inscrição individual é uma entrada para um único dispositivo que pode ligar. As matrículas individuais podem utilizar certificados de folhax.509 ou tokens SAS (de um TPM físico ou virtual) como mecanismos de atestado. O ID do dispositivo (também conhecido como ID de registo) numa matrícula individual é alfanumérico, minúsculo e pode conter hífenes. Saiba mais sobre as matrículas individuais [aqui.](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)

> [!NOTE]
> Quando cria uma inscrição individual para um dispositivo, tem precedência sobre as inscrições padrão do Grupo (SAS, X509) na sua aplicação.

### <a name="creating-individual-enrollments"></a>Criação de matrículas individuais
IoT Central apoia os seguintes mecanismos de atestado

1. Atestação de **chaves simétricas:** O atestado de chaves simétricas é uma abordagem simples para autenticar um dispositivo com uma instância do Serviço de Provisionamento de Dispositivos. Para criar uma inscrição individual com teclas simétricas; abrir o diálogo Connect, selecionar Individual Registration and Mechanism "SAS" e inserir as teclas Primária e Secundária. As teclas SAS devem estar codificadas na base64. Aqui está o [link](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) para amostras de código para ajudar a escrever o seu código de dispositivo para dispositivos de provisionamento usando chaves simétricas e inscrições individuais.
1. **Certificados X.509:** Os certificados X.509, como o título sugere, é um mecanismo de atestado baseado em certificados, uma excelente forma de escalar a produção. Para criar uma inscrição individual com teclas simétricas selecione Individual Registration and Mechanism "X.509" e carregue os certificados primários e secundários e poupe para criar a inscrição. Aqui está o [link](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) para amostras de código para ajudar a escrever o seu código de dispositivo para dispositivos de provisionamento usando X509. Os certificados do dispositivo utilizados com uma [inscrição individual](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment) têm a obrigação de que o Nome do Assunto deve ser definido para o ID do dispositivo (também conhecido como ID de registo) da entrada individual de inscrição.
1. **Atestado TPM:** TPM significa Módulo de Plataforma Fidedigna e é um tipo de módulo de segurança de hardware (HSM) e é uma das formas mais seguras de ligar os seus dispositivos.  Este artigo assume que está a usar um TPM discreto, firmware ou integrado. Os TPMs emulados pelo software são adequados para prototipagem ou teste, mas não fornecem o mesmo nível de segurança que os TPMs discretos, firmware ou integrados. Não recomendamos a utilização de TPMs de software em produção. Para criar uma inscrição individual com teclas simétricas selecione Individual Registration and Mechanism "TPM" e insere as teclas de endosso para criar a inscrição. Para mais informações sobre tipos de TPMs, pode saber mais sobre o atestado TPM [aqui](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation). Aqui está o [link](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample) para amostras de código para ajudar a escrever o seu código de dispositivo para dispositivos de provisionamento usando TPM. Para criar um atestado baseado em TPM, digite a chave de apoio e poupe.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Ligar dispositivos com plug e reproduzir IoT (pré-visualização)

Uma das principais funcionalidades do IoT Plug and Play (pré-visualização) com o IoT Central é a capacidade de associar automaticamente os modelos do dispositivo na ligação do dispositivo. Juntamente com as credenciais do dispositivo, os dispositivos podem agora enviar o **CapabilityModelId** como parte da chamada de registo do dispositivo e a IoT Central descobrirá e associará o modelo do dispositivo. O processo de descoberta segue a seguinte ordem:

1. Associa-se ao modelo do dispositivo se já estiver publicado na aplicação IoT Central.
1. Vai buscar ao repositório público de modelos de capacidade publicados e certificados.

Abaixo está o formato da carga útil adicional que o dispositivo enviaria durante a chamada de registo DPS

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> Note que a opção De Amissão Automática deve ser ativada para que os dispositivos se conectem automaticamente, descubram o modelo e comecem a enviar dados.

## <a name="device-status"></a>Estado do dispositivo

Quando um dispositivo real se liga à sua aplicação IoT Central, o seu estado do dispositivo muda da seguinte forma:

1. O estado do dispositivo está **registado**pela primeira vez . Este estado significa que o dispositivo é criado na IoT Central e tem um ID do dispositivo. Um dispositivo é registado quando:
    - Um novo dispositivo real é adicionado na página **dispositivos.**
    - Um conjunto de dispositivos é adicionado usando **import na** página **dispositivos.**

1. O estado do dispositivo muda para **OProvisionado** quando o dispositivo que ligou à sua aplicação IoT Central com credenciais válidas completa a etapa de provisionamento. Neste passo, o dispositivo recupera uma cadeia de ligação do IoT Hub. O dispositivo pode agora ligar-se ao IoT Hub e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo está bloqueado, não pode enviar dados para a sua aplicação IoT Central. Os dispositivos bloqueados têm um estatuto de **Bloqueado**. Um operador deve redefinir o dispositivo antes de poder retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o estado retorna ao seu valor anterior, **registado** ou **provisionado**.

1. O estado do dispositivo está à espera de **aprovação,** o que significa que a opção **De aprovação automática** está desativada e exige que todos os dispositivos ligados à IoT Central sejam explicitamente aprovados por um operador. Os dispositivos não registados manualmente na página **dispositivos,** mas ligados com credenciais válidas terão o estado do dispositivo **à espera de aprovação**. Os operadores podem aprovar estes dispositivos a partir da página **dispositivos** utilizando o botão **'Aprovar'.**

1. O estado do dispositivo não está **associado,** o que significa que os dispositivos que ligam à IoT Central não têm um Modelo de Dispositivo associado a eles. Isto normalmente acontece nos seguintes cenários:
    - Um conjunto de dispositivos é adicionado usando **importna** página **de Dispositivos** sem especificar o modelo de dispositivo
    - Dispositivos não registados manualmente na página **dispositivos ligados** com credenciais válidas, mas sem especificar o ID do modelo durante o registo.  
O Operador pode associar um dispositivo a um modelo da página **dispositivos** utilizando o botão **Migrate.**

## <a name="best-practices"></a>Melhores práticas 
1.  Ao utilizar o DPS para ligar os dispositivos à IoT Central, certifique-se de que a cadeia de ligação do dispositivo (IoT Hub) não é persistiu ou está em cache. Para religar os dispositivos, passe pelo fluxo regular de registo do dispositivo DPS para obter a cadeia de ligação correta do dispositivo. Se a cadeia de ligação estiver em cache, o software do dispositivo corre o risco de ter uma cadeia de ligação velha nos cenários em que a IoT Central atualizou o Hub Azure IoT subjacente. 

## <a name="sdk-support"></a>Suporte SDK

Os SDKs de Dispositivo SDKs Azure oferecem a forma mais fácil de implementar o seu código de dispositivo. Estão disponíveis os seguintes SDKs do dispositivo:

- [Azure IoT SDK para C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK para Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK para Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Características sDK e conectividade IoT Hub

Toda a comunicação do dispositivo com o IoT Hub utiliza as seguintes opções de conectividade IoT Hub:

- [Mensagens dispositivo-a-nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Gémeos dispositivo](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela que se segue resume como o dispositivo Central Azure IoT apresenta o mapa nas funcionalidades do IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Medição: Telemetria | Mensagens dispositivo-a-nuvem |
| Propriedades do dispositivo | Propriedades reportadas twin dispositivo |
| Definições | Dispositivo twin propriedades desejadas e reportadas |

Para saber mais sobre a utilização dos SDKs de Dispositivo, consulte [Connect a DevDiv kit device à sua aplicação Azure IoT Central,](howto-connect-devkit.md) por exemplo, código.

### <a name="protocols"></a>Protocolos

Os SDKs do dispositivo suportam os seguintes protocolos de rede para a ligação a um hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre estes protocolos de diferença e orientação sobre a escolha de um, consulte Escolha um protocolo de [comunicação](../../iot-hub/iot-hub-devguide-protocols.md).

Se o seu dispositivo não puder utilizar nenhum dos protocolos suportados, pode utilizar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge suporta outros cenários de inteligência no limite para descarregar o processamento para a borda da aplicação Central Azure IoT.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e a central do Azure IoT estão encriptados. O IoT Hub autentica todos os pedidos de um dispositivo que se conecta a qualquer um dos pontos finais do IoT Hub virado para o dispositivo. Para evitar trocar credenciais por cima do fio, um dispositivo utiliza fichas assinadas para autenticar. Para mais informações, consulte, [Controle o acesso ao IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu sobre a conectividade do dispositivo na Central Azure IoT, aqui estão os próximos passos sugeridos:

- [Preparar e ligar um dispositivo DevKit](howto-connect-devkit.md)
- [C SDK: Provisioning Device Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
