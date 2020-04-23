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
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1398169c44dadcd11ad037e4e3a1cc0132e21f13
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024698"
---
# <a name="get-connected-to-azure-iot-central"></a>Fique ligado à Central Azure IoT

*Este artigo aplica-se aos operadores e desenvolvedores de dispositivos.*

Este artigo descreve as opções para ligar os seus dispositivos a uma aplicação Azure IoT Central.

Normalmente, tem de registar um dispositivo na sua aplicação antes de se ligar. No entanto, a IoT Central suporta cenários em que [os dispositivos podem ligar-se sem que os primeiros sejam registados](#connect-without-registering-devices).

A IoT Central utiliza o serviço de provisionamento de [dispositivos Hub Azure IoT (DPS)](../../iot-dps/about-iot-dps.md) para gerir o processo de ligação. Um dispositivo liga-se primeiro a um ponto final dPS para recuperar a informação que precisa para se ligar à sua aplicação. Internamente, a sua aplicação IoT Central utiliza um hub IoT para lidar com a conectividade do dispositivo. A utilização de DPS permite:

- IoT Central para apoiar dispositivos de embarque e ligação em escala.
- Você gera credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Você usa as suas próprias iDs de dispositivo para registar dispositivos na IoT Central. A utilização dos seus próprios IDs de dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Para garantir a comunicação entre um dispositivo e a sua aplicação, a IoT Central suporta tanto as assinaturas de acesso partilhado (SAS) como os certificados X.509. Os certificados X.509 são recomendados em ambientes de produção.

Este artigo descreve os seguintes casos de utilização:

- [Ligue um único dispositivo utilizando o SAS](#connect-a-single-device)
- [Ligar dispositivos à escala utilizando o SAS](#connect-devices-at-scale-using-sas)
- [Conecte os dispositivos em escala utilizando certificados X.509](#connect-devices-using-x509-certificates) - a abordagem recomendada para ambientes de produção.
- [Ligar dispositivos sem primeiro registrá-los](#connect-without-registering-devices)
- [Ligar dispositivos que utilizam matrículas individuais dPS](#individual-enrollment-based-device-connectivity)
- [Conecte dispositivos utilizando funcionalidades IoT Plug e Play (pré-visualização)](#connect-devices-with-iot-plug-and-play-preview)

## <a name="connect-a-single-device"></a>Ligar um único dispositivo

Esta abordagem é útil quando se está a experimentar com a IoT Central ou dispositivos de teste. Pode utilizar as teclas SAS de ligação ao dispositivo da sua aplicação IoT Central para ligar um dispositivo à sua aplicação IoT Central. Copiar a _chave SAS_ do dispositivo a partir das informações de ligação para um dispositivo registado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

Para saber mais, consulte a Create e ligue uma aplicação de [cliente Node.js ao seu tutorial de aplicação Azure IoT Central.](./tutorial-connect-device-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>Ligar dispositivos à escala utilizando o SAS

Para ligar os dispositivos à IoT Central à escala utilizando teclas SAS, é necessário registar-se e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registar dispositivos a granel

Para registar um grande número de dispositivos com a sua aplicação IoT Central, utilize um ficheiro CSV para [importar iDs de dispositivos e nomes](howto-manage-devices.md#import-devices)de dispositivos .

Para obter as informações de ligação para os dispositivos importados, [exporte um ficheiro CSV da sua aplicação IoT Central](howto-manage-devices.md#export-devices). O ficheiro CSV exportado inclui as iDs do dispositivo e as teclas SAS.

### <a name="set-up-your-devices"></a>Instale os seus dispositivos

Utilize as informações de ligação do ficheiro de exportação no código do seu dispositivo para permitir que os seus dispositivos conectem e enviem dados para IoT para a sua aplicação IoT Central. Também precisa da **margem** de identificação do DPS para a sua aplicação. Pode encontrar este valor na ligação de > Dispositivo da **Administração.**

> [!NOTE]
> Para saber como pode ligar os dispositivos sem antes registrá-los na IoT Central, consulte [Connect sem primeiro sintetizar dispositivos](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Ligar dispositivos utilizando certificados X.509

Em ambiente de produção, a utilização de certificados X.509 é o mecanismo recomendado de autenticação do dispositivo para a IoT Central. Para saber mais, consulte a [Autenticação do Dispositivo utilizando certificados X.509 CA](../../iot-hub/iot-hub-x509ca-overview.md).

Antes de ligar um dispositivo com um certificado X.509, adicione e verifique um certificado X.509 intermédio ou raiz na sua aplicação. Os dispositivos devem utilizar certificados de folha X.509 gerados a partir do certificado raiz ou intermédio.

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>Adicione e verifique um certificado de raiz ou intermédio

Navegue para **a Administração > Ligação** de Dispositivos > Gerir o certificado primário e adicione o certificado de raiz ou intermédio X.509 que está a usar para gerar os certificados do dispositivo.

![Definições de ligação](media/concepts-get-connected/manage-x509-certificate.png)

A verificação da propriedade do certificado garante que a pessoa que faz o upload do certificado tem a chave privada do certificado. Para verificar o certificado:

  1. Selecione o botão ao lado do Código de **Verificação** para gerar um código.
  1. Crie um certificado de verificação X.509 com o código de verificação gerado no passo anterior. Guarde o certificado como ficheiro .cer.
  1. Faça upload do certificado de verificação assinado e selecione **Verificar**. O certificado é marcado como **Verificado** quando a verificação é bem sucedida.

Se tiver uma falha de segurança ou se o seu certificado primário estiver definido para expirar, utilize o certificado secundário para reduzir o tempo de inatividade. Pode continuar a fornecer dispositivos utilizando o certificado secundário enquanto atualiza o certificado primário.

### <a name="register-and-connect-devices"></a>Registar e ligar dispositivos

Para ligar a granel os dispositivos utilizando certificados X.509, primeiro registe os dispositivos na sua aplicação, utilizando um ficheiro CSV para [importar as identificações do dispositivo e os nomes](howto-manage-devices.md#import-devices)do dispositivo . As identificações do dispositivo devem estar todas em minúsculas.

Gere certificados de folhaX.509 para os seus dispositivos utilizando o certificado raiz ou intermédio carregado. Utilize o **ID** `CNAME` do dispositivo como valor nos certificados de folha. O código do seu dispositivo necessita do valor de âmbito de **identificação** da sua aplicação, do ID do **dispositivo**e do certificado de dispositivo correspondente.

### <a name="for-testing-purposes-only"></a>Para efeitos de teste apenas

Apenas para testes, pode utilizar os seguintes utilitários para gerar certificados de raiz, intermédios e dispositivos:

- Ferramentas para o Dispositivo de Provisionamento de [Dispositivos Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md): uma coleção de ferramentas Node.js que pode utilizar para gerar e verificar certificados e chaves X.509.
- Se estiver a utilizar um dispositivo DevKit, esta [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gera um certificado CA que pode adicionar à sua aplicação IoT Central para verificar os certificados.
- [Gerir certificados ca de teste para amostras e tutoriais:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)uma coleção de scripts PowerShell e Bash para:
  - Criar uma cadeia de certificados.
  - Guarde os certificados como ficheiros .cer para fazer upload para a sua aplicação IoT Central.
  - Utilize o código de verificação da aplicação IoT Central para gerar o certificado de verificação.
  - Crie certificados de folhas para os seus dispositivos utilizando os seus IDs do dispositivo como parâmetro para a ferramenta.

### <a name="further-reference"></a>Outra referência

- [Implementação da amostra para RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)
- [Cliente de dispositivo de amostra em C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-registering-devices"></a>Ligar sem registar dispositivos

Os cenários previamente descritos exigem que registe os dispositivos na sua aplicação antes de se ligarem. A IoT Central também permite que os OEMs fabricem dispositivos de fabrico em massa que podem ligar-se sem que primeiro sejam registados. Um OEM gera credenciais de dispositivo adequadas e configura os dispositivos da fábrica. Quando um cliente liga um dispositivo pela primeira vez, liga-se ao DPS, que depois liga automaticamente o dispositivo à aplicação IoT Central correta. Um operador Central IoT deve aprovar o dispositivo antes de começar a enviar dados para a aplicação.

O fluxo é ligeiramente diferente dependendo se os dispositivos utilizam tokens SAS ou certificados X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Ligar dispositivos que usam fichas SAS sem registar

1. Copiar a chave primária do grupo ioT Central:

    ![Chave SAS primária do grupo de aplicação](media/concepts-get-connected/group-sas-keys.png)

1. Utilize a ferramenta [dps-keygen](https://www.npmjs.com/package/dps-keygen) para gerar as teclas SAS do dispositivo. Utilize a chave primária do grupo do passo anterior. As iDs do dispositivo devem ser minúsculas:

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. O OEM pisca cada dispositivo com um ID do dispositivo, uma chave SAS do dispositivo gerado e o valor de âmbito de **identificação** da aplicação.

1. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as suas informações de registo IoT Central.

    O dispositivo tem inicialmente um estado de dispositivo **não associado** na página **dispositivos** e não está atribuído a um modelo de dispositivo. Na página **dispositivos,** **emigra** o dispositivo para o modelo de dispositivo apropriado. O fornecimento do dispositivo está agora completo, o estado do dispositivo está agora **provisionado**e o dispositivo pode começar a enviar dados.

    Na página de **ligação do Dispositivo > Administração,** o **Auto aprova** os controlos de opção se precisa de aprovar manualmente o dispositivo antes de poder começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [o Connect devices com ioT Plug and Play (pré-visualização)](#connect-devices-with-iot-plug-and-play-preview).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Ligar dispositivos que usam certificados X.509 sem registar

1. [Adicione e verifique um certificado X.509 raiz ou intermédio](#connect-devices-using-x509-certificates) na sua aplicação IoT Central. (certificados #connect-dispositivos-utilização-x509)

1. Gere os certificados de folha sinuoso para os seus dispositivos utilizando o certificado raiz ou intermédio que adicionou à sua aplicação IoT Central. Utilize iDs de dispositivo `CNAME` minúsculos como os certificados de folha.

1. O OEM pisca cada dispositivo com um ID do dispositivo, um certificado x.509 esquerdo gerado e o valor de âmbito de **identificação** da aplicação.

1. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as suas informações de registo IoT Central.

    O dispositivo tem inicialmente um estado de dispositivo **não associado** na página **dispositivos** e não está atribuído a um modelo de dispositivo. Na página **dispositivos,** **emigra** o dispositivo para o modelo de dispositivo apropriado. O fornecimento do dispositivo está agora completo, o estado do dispositivo está agora **provisionado**e o dispositivo pode começar a enviar dados.

    Na página de **ligação do Dispositivo > Administração,** o **Auto aprova** os controlos de opção se precisa de aprovar manualmente o dispositivo antes de poder começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [o Connect devices com ioT Plug and Play (pré-visualização)](#connect-devices-with-iot-plug-and-play-preview).

## <a name="individual-enrollment-based-device-connectivity"></a>Conectividade individual do dispositivo baseado em inscrições

Para os clientes que conectam dispositivos que cada um tem as suas próprias credenciais de autenticação, utilize as matrículas individuais. Uma inscrição individual é uma entrada para um único dispositivo que é permitido ligar. As matrículas individuais podem utilizar certificados de folhax.509 ou tokens SAS (a partir de um módulo de plataforma de confiança física ou virtual) como mecanismos de atestado. O ID do dispositivo (também conhecido como ID de registo) numa matrícula individual é alfanumérico, minúsculo e pode conter hífenes. Para mais informações, consulte a [inscrição individual do DPS.](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)

> [!NOTE]
> Ao criar uma inscrição individual para um dispositivo, tem precedência sobre as opções de inscrição em grupo padrão na sua aplicação IoT Central.

### <a name="creating-individual-enrollments"></a>Criação de matrículas individuais

A IoT Central apoia os seguintes mecanismos de atestado para as matrículas individuais:

- Atestação de **chaves simétricas:** O atestado de chave simétrica é uma abordagem simples para autenticar um dispositivo com a instância DPS. Para criar uma inscrição individual que utilize teclas simétricas, abra a página de Ligação do **Dispositivo,** selecione **a inscrição individual** como método de ligação e a assinatura de acesso partilhado **(SAS)** como mecanismo. Introduza as teclas primárias e secundárias codificadas da base64 e guarde as alterações. Utilize o âmbito de **identificação,** **id do dispositivo,** e a chave primária ou secundária para ligar o seu dispositivo.

    > [!TIP]
    > Para testes, pode utilizar o **OpenSSL** para gerar teclas codificadas base64:`openssl rand -base64 64`

- **Certificados X.509:** Para criar uma inscrição individual com certificados X.509, abra a página **de Ligação** de Dispositivos, selecione **a inscrição individual** como método de ligação, e **certificados (X.509)** como mecanismo. Os certificados do dispositivo utilizados com uma inscrição individual têm a obrigação de que o emitente e o sujeito CN sejam definidos para o ID do dispositivo.

    > [!TIP]
    > Para testes, pode utilizar ferramentas para o Dispositivo de Provisionamento de [Dispositivos Azure IoT SDK para node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) para gerar um certificado auto-assinado:`node create_test_cert.js device "mytestdevice"`

- **Atestação de módulo de plataforma fidedigna (TPM):** Um [TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation) é um tipo de módulo de segurança de hardware. Usar um TPM é uma das formas mais seguras de ligar um dispositivo. Este artigo assume que está a usar um TPM discreto, firmware ou integrado. Os TPMs emulados pelo software são adequados para prototipagem ou teste, mas não fornecem o mesmo nível de segurança que os TPMs discretos, firmware ou integrados. Não use TPMs de software em produção. Para criar uma inscrição individual que utilize um TPM, abra a página **de Ligação** de Dispositivos, selecione **a inscrição individual** como método de ligação, e **tPM** como o mecanismo. Introduza a chave de averbamento TPM e guarde as informações de ligação do dispositivo.

## <a name="connect-devices-with-iot-plug-and-play-preview"></a>Ligar dispositivos com plug e reproduzir IoT (pré-visualização)

Uma das principais funcionalidades do IoT Plug and Play (pré-visualização) com o IoT Central é a capacidade de associar automaticamente os modelos do dispositivo na ligação do dispositivo. Juntamente com as credenciais do dispositivo, os dispositivos podem agora enviar o **CapabilityModelId** como parte da chamada de registo do dispositivo. Esta capacidade permite à IoT Central descobrir e associar o modelo do dispositivo ao dispositivo. O processo de descoberta funciona da seguinte forma:

1. Associa-se ao modelo do dispositivo se já estiver publicado na aplicação IoT Central.
1. Vai buscar ao repositório público de modelos de capacidade publicados e certificados.

Abaixo está o formato da carga útil adicional que o dispositivo enviaria durante a chamada de registo DPS

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> Note que o **Auto aprova** a opção na **administração > a ligação** do dispositivo deve ser ativada para que os dispositivos se conectem automaticamente, descubram o modelo do dispositivo e comecem a enviar dados.

## <a name="device-status-values"></a>Valores do estado do dispositivo

Quando um dispositivo real se liga à sua aplicação IoT Central, o seu estado do dispositivo muda da seguinte forma:

1. O estado do dispositivo está **registado**pela primeira vez . Este estado significa que o dispositivo é criado na IoT Central e tem um ID do dispositivo. Um dispositivo é registado quando:
    - Um novo dispositivo real é adicionado na página **dispositivos.**
    - Um conjunto de dispositivos é adicionado usando **import na** página **dispositivos.**

1. O estado do dispositivo muda para **OProvisionado** quando o dispositivo que ligou à sua aplicação IoT Central com credenciais válidas completa a etapa de provisionamento. Neste passo, o dispositivo utiliza DPS para recuperar automaticamente uma cadeia de ligação do Hub IoT utilizado pela sua aplicação IoT Central. O dispositivo pode agora ligar-se à IoT Central e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo está bloqueado, não pode enviar dados para a sua aplicação IoT Central. Os dispositivos bloqueados têm um estatuto de **Bloqueado**. Um operador deve redefinir o dispositivo antes de poder retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o estado retorna ao seu valor anterior, **registado** ou **provisionado**.

1. Se o estado do dispositivo estiver à espera de **aprovação,** significa que a opção **de aprovação automática** está desativada. Um operador deve aprovar explicitamente um dispositivo antes de começar a enviar dados. Os dispositivos não registados manualmente na página **dispositivos,** mas ligados com credenciais válidas terão o estado do dispositivo **à espera de aprovação**. Os operadores podem aprovar estes dispositivos a partir da página **dispositivos** utilizando o botão **'Aprovar'.**

1. Se o estado do dispositivo não estiver **associado,** significa que o dispositivo que liga à IoT Central não tem um modelo de dispositivo associado. Esta situação normalmente acontece nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando **import na** página **dispositivos** sem especificar o modelo do dispositivo.
    - Um dispositivo foi registado manualmente na página **dispositivos** sem especificar o modelo do dispositivo. O aparelho está então ligado a credenciais válidas.  

    O Operador pode associar um dispositivo a um modelo de dispositivo da página **Dispositivos** utilizando o botão **Migrate.**

## <a name="best-practices"></a>Melhores práticas

Não persista nem cache a cadeia de ligação do dispositivo que o DPS retorna quando ligar o dispositivo pela primeira vez. Para voltar a ligar um dispositivo, passe pelo fluxo de registo padrão do dispositivo para obter a cadeia de ligação correta do dispositivo. Se o dispositivo cachea a cadeia de ligação, o software do dispositivo corre o risco de ter uma cadeia de ligação velha se a IoT Central atualizar o hub Azure IoT subjacente que utiliza.

## <a name="sdk-support"></a>Suporte de SKDs

Os SDKs de Dispositivo SDKs Azure oferecem a forma mais fácil de implementar o seu código de dispositivo. Estão disponíveis os seguintes SDKs do dispositivo:

- [SDK do Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do Azure IoT para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do Azure IoT para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do Azure IoT para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Características sDK e conectividade IoT Hub

Toda a comunicação do dispositivo com o IoT Hub utiliza as seguintes opções de conectividade IoT Hub:

- [Mensagens dispositivo-a-nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Gémeos dispositivo](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela que se segue resume como o dispositivo Central Azure IoT apresenta o mapa nas funcionalidades do IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Mensagens dispositivo-a-nuvem |
| Propriedade | Propriedades reportadas twin dispositivo |
| Propriedade (rectilável) | Dispositivo twin propriedades desejadas e reportadas |
| Comando | Métodos diretos |

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

## <a name="next-steps"></a>Passos seguintes

Se é um desenvolvedor de dispositivos, alguns sugeriu que os próximos passos são para:

- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
- Saiba como definir um novo tipo de [dispositivo IoT na sua aplicação Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md)
