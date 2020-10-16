---
title: Conectividade do dispositivo no Azure IoT Central Microsoft Docs
description: Este artigo introduz conceitos-chave relativos à conectividade do dispositivo na Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 5f9f8be81c5b90ff5e7172b2aba41a108afc64bd
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126846"
---
# <a name="get-connected-to-azure-iot-central"></a>Ligar-se ao Azure IoT Central

*Este artigo aplica-se aos operadores e desenvolvedores de dispositivos.*

Este artigo descreve as opções de ligação dos seus dispositivos a uma aplicação Azure IoT Central.

Normalmente, deve registar um dispositivo na sua aplicação antes de poder ligar. No entanto, a IoT Central faz cenários de suporte onde [os dispositivos podem ligar-se sem primeiro serem registados.](#connect-without-registering-devices)

A IoT Central utiliza o [serviço de provisionamento de dispositivos Azure IoT Hub (DPS)](../../iot-dps/about-iot-dps.md) para gerir o processo de ligação. Um dispositivo liga-se primeiro a um ponto final DPS para recuperar a informação de que necessita para se ligar à sua aplicação. Internamente, a sua aplicação IoT Central utiliza um hub IoT para lidar com a conectividade do dispositivo. A utilização de DPS permite:

- IoT Central para suportar dispositivos de embarque e ligação à escala.
- Você deve gerar credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Você deve usar os seus próprios IDs de dispositivo para registar dispositivos na IoT Central. A utilização dos iDs do seu próprio dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Para garantir a comunicação entre um dispositivo e a sua aplicação, a IoT Central suporta tanto as assinaturas de acesso partilhado (SAS) como os certificados X.509. Os certificados X.509 são recomendados em ambientes de produção.

Este artigo descreve os seguintes casos de utilização:

- [Ligue um único dispositivo utilizando SAS](#connect-a-single-device)
- [Ligar dispositivos à escala utilizando SAS](#connect-devices-at-scale-using-sas)
- [Ligar dispositivos à escala utilizando certificados X.509](#connect-devices-using-x509-certificates) - a abordagem recomendada para ambientes de produção.
- [Conecte os dispositivos sem primeiro os registar](#connect-without-registering-devices)
- [Ligar dispositivos que utilizam inscrições individuais de DPS](#individual-enrollment-based-device-connectivity)
- [Associe automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>Ligar um único dispositivo

Esta abordagem é útil quando se está a experimentar com ioT Central ou dispositivos de teste. Pode utilizar as teclas SAS de ligação do dispositivo da sua aplicação IoT Central para ligar um dispositivo à sua aplicação IoT Central. Copie a _chave SAS_ do dispositivo a partir das informações de ligação de um dispositivo registado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

Para saber mais, consulte o Create e ligue uma aplicação Node.js ao seu tutorial [de aplicação Azure IoT Central.](./tutorial-connect-device-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>Ligar dispositivos à escala utilizando SAS

Para ligar os dispositivos à IoT Central em escala utilizando as teclas SAS, é necessário registar-se e, em seguida, configurar os dispositivos:

### <a name="register-devices-in-bulk"></a>Registar dispositivos a granel

Para registar um grande número de dispositivos com a sua aplicação IoT Central, utilize um ficheiro CSV para [importar IDs e nomes de dispositivos](howto-manage-devices.md#import-devices).

Para obter as informações de ligação dos dispositivos importados, [exporte um ficheiro CSV a partir da sua aplicação IoT Central](howto-manage-devices.md#export-devices). O ficheiro CSV exportado inclui os IDs do dispositivo e as teclas SAS.

### <a name="set-up-your-devices"></a>Configurar os seus dispositivos

Utilize as informações de ligação do ficheiro de exportação no código do seu dispositivo para permitir que os seus dispositivos conectem e enviem dados para ioT para a sua aplicação IoT Central. Também precisa do âmbito de identificação do **DPS** para a sua aplicação. Pode encontrar este valor na **ligação > dispositivo da Administração.**

> [!NOTE]
> Para saber como pode ligar dispositivos sem primeiro os registar no IoT Central, consulte [Connect sem primeiro registar os dispositivos](#connect-without-registering-devices).

## <a name="connect-devices-using-x509-certificates"></a>Ligar dispositivos utilizando certificados X.509

Num ambiente de produção, a utilização de certificados X.509 é o mecanismo de autenticação recomendado para a IoT Central. Para saber mais, consulte [a autenticação do dispositivo utilizando certificados X.509 CA](../../iot-hub/iot-hub-x509ca-overview.md).

Para ligar um dispositivo com um certificado X.509 à sua aplicação:

1. Crie um *grupo de inscrições* que utilize o tipo **de atestado certificados (X.509).**
2. Adicione e verifique um certificado X.509 intermédio ou raiz no grupo de inscrição.
3. Registar e ligar dispositivos que utilizem certificados de folha X.509 gerados a partir do certificado raiz ou intermédio no grupo de inscrição.

### <a name="create-an-enrollment-group"></a>Criar um grupo de inscrições

Um grupo de [inscrições](../../iot-dps/concepts-service.md#enrollment) é um grupo de dispositivos que partilham o mesmo tipo de atestado. Os dois tipos de atestado suportados são certificados X.509 e SAS:

- Num grupo de matrículas X.509, todos os dispositivos que se ligam ao IoT Central utilizam certificados X.509 da raiz ou do certificado intermédio no grupo de inscrição.
- Num grupo de inscrições SAS, todos os dispositivos que se ligam à IoT Central utilizam um token SAS gerado a partir do token SAS no grupo de inscrição.

Os dois grupos de inscrição padrão em cada aplicação IoT Central são grupos de inscrição SAS - um para dispositivos IoT e um para dispositivos Azure IoT Edge. Para criar um grupo de inscrição X.509, navegue na página **de ligação** do Dispositivo e selecione **+ Adicione o grupo de inscrição**:

:::image type="content" source="media/concepts-get-connected/add-enrollment-group.png" alt-text="Adicione uma imagem de grupo de inscrição X.509":::

### <a name="add-and-verify-a-root-or-intermediate-x509-certificate"></a>Adicione e verifique um certificado X.509 raiz ou intermédio

Para adicionar e verificar um certificado de raiz ou intermédio ao seu grupo de inscrição:

1. Navegue para o grupo de inscrições X.509 que acabou de criar. Tem a opção de adicionar certificados X.509 primários e secundários. **Selecione + Gerir o primário**.

1. Na página do **certificado Primário,** faça o upload do seu certificado X.509 primário. Esta é a sua raiz ou certificado intermédio:

    :::image type="content" source="media/concepts-get-connected/upload-primary-certificate.png" alt-text="Adicione uma imagem de grupo de inscrição X.509":::

1. Clique **em Gerar código de verificação** para gerar um código para a ferramenta que está a usar para gerar o certificado de verificação. Em seguida, **selecione Verifique** para carregar o certificado de verificação.

1. Quando a verificação for bem sucedida, vê a seguinte confirmação:

    :::image type="content" source="media/concepts-get-connected/verified-primary-certificate.png" alt-text="Adicione uma imagem de grupo de inscrição X.509":::

Verificar a propriedade do certificado garante que a pessoa que faz o upload do certificado tem a chave privada do certificado.

Se tiver uma falha de segurança ou o seu certificado primário estiver definido para expirar, utilize o certificado secundário para reduzir o tempo de inatividade. Pode continuar a providenciar dispositivos utilizando o certificado secundário enquanto atualiza o certificado primário.

### <a name="register-and-connect-devices"></a>Registar e ligar dispositivos

Para ligar a granel os dispositivos que utilizam certificados X.509, primeiro registe os dispositivos na sua aplicação utilizando um ficheiro CSV para [importar os IDs do dispositivo e os nomes do dispositivo](howto-manage-devices.md#import-devices). As identificações do dispositivo devem estar todas em minúsculas.

Gere certificados de folha X.509 para os seus dispositivos utilizando o certificado raiz ou intermédio que carregou para o seu grupo de matrículas X.509. Utilize o **ID** do dispositivo como `CNAME` o valor nos certificados de folha. O código do dispositivo necessita do valor de **alcance de ID** para a sua aplicação, o **ID**do dispositivo e o certificado do dispositivo correspondente.

#### <a name="sample-device-code"></a>Código do dispositivo de amostra

A seguinte amostra do [Azure IoT Node.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js) mostra como um cliente Node.js dispositivo usa um certificado de folha X.509 e DPS para se registar com uma aplicação IoT Central:

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

Para obter uma amostra C equivalente, consulte [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c) no [Azure IoT C Provisioning Client SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md).

### <a name="for-testing-purposes-only"></a>Apenas para fins de teste

Apenas para testes, pode utilizar os seguintes utilitários para gerar certificados de raiz, intermédios e dispositivos:

- [Ferramentas para o Dispositivo de Provisionamento de DispositivoS Azure IoT SDK:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)uma coleção de ferramentas Node.js que pode utilizar para gerar e verificar certificados e chaves X.509.
- Se estiver a utilizar um dispositivo DevKit, esta [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gera um certificado de CA que pode adicionar à sua aplicação IoT Central para verificar os certificados.
- [Gerir certificados de CA de teste para amostras e tutoriais:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)uma coleção de scripts PowerShell e Bash para:
  - Criar uma cadeia de certificados.
  - Guarde os certificados como ficheiros .cer para fazer o upload para a sua aplicação IoT Central.
  - Utilize o código de verificação a partir da aplicação IoT Central para gerar o certificado de verificação.
  - Crie certificados de folha para os seus dispositivos utilizando os IDs do seu dispositivo como parâmetro para a ferramenta.

## <a name="connect-without-registering-devices"></a>Conecte-se sem registar dispositivos

Os cenários anteriormente descritos exigem que registe os dispositivos na sua aplicação antes de se ligarem. A IoT Central também permite que os OEMs fabricem dispositivos de fabrico em massa que podem ligar-se sem primeiro serem registados. Um OEM gera credenciais adequadas para dispositivos e configura os dispositivos na fábrica. Quando um cliente liga um dispositivo pela primeira vez, liga-se ao DPS, que liga automaticamente o dispositivo à aplicação IoT Central correta. Um operador central da IoT deve aprovar o dispositivo antes de começar a enviar dados para a aplicação.

O fluxo é ligeiramente diferente dependendo se os dispositivos usam fichas SAS ou certificados X.509:

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>Ligar dispositivos que usam fichas SAS sem registar

1. Copie a chave primária do grupo do grupo de inscrição **SAS-IoT-Devices:**

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Adicione uma imagem de grupo de inscrição X.509":::

1. Utilize o `az iot central device compute-device-key` comando para gerar as teclas SAS do dispositivo. Utilize a chave primária do grupo a partir do passo anterior. As iDs do dispositivo devem ser minúsculas:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. O OEM pisca cada dispositivo com um ID do dispositivo, uma chave SAS do dispositivo gerado e o valor de **alcance de identificação** da aplicação.

1. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as informações de registo da IoT Central.

    O dispositivo tem inicialmente um estado de dispositivo **não associado** na página **dispositivos** e não está atribuído a um modelo de dispositivo. Na página **Dispositivos,** **emigre** o dispositivo para o modelo do dispositivo apropriado. O fornecimento de dispositivos está agora completo, o estado do dispositivo **encontra-se**agora a ser previsto , e o dispositivo pode começar a enviar dados.

    Na página de ligação > **dispositivo da Administração,** a opção **Auto aprova** os controlos se precisa de aprovar manualmente o dispositivo antes de poder começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template).

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>Ligar dispositivos que usam certificados X.509 sem registar

1. [Crie um grupo de inscrições](#create-an-enrollment-group) e, em seguida, [adicione e verifique um certificado X.509 raiz ou intermédio](#add-and-verify-a-root-or-intermediate-x509-certificate) na sua aplicação IoT Central.

1. Gere os certificados de folha para os seus dispositivos utilizando o certificado raiz ou intermédio que adicionou à sua aplicação IoT Central. Utilize iDs de dispositivo de caixa inferior como os `CNAME` certificados de folha.

1. O OEM pisca cada dispositivo com um ID do dispositivo, um certificado X.509 de folha gerada e o valor de **alcance de identificação** da aplicação.

1. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as informações de registo da IoT Central.

    O dispositivo tem inicialmente um estado de dispositivo **não associado** na página **dispositivos** e não está atribuído a um modelo de dispositivo. Na página **Dispositivos,** **emigre** o dispositivo para o modelo do dispositivo apropriado. O fornecimento de dispositivos está agora completo, o estado do dispositivo **encontra-se**agora a ser previsto , e o dispositivo pode começar a enviar dados.

    Na página de ligação > **dispositivo da Administração,** a opção **Auto aprova** os controlos se precisa de aprovar manualmente o dispositivo antes de poder começar a enviar dados.

    > [!NOTE]
    > Para saber como associar automaticamente um dispositivo a um modelo de dispositivo, consulte [associar automaticamente um dispositivo a um modelo de dispositivo](#automatically-associate-with-a-device-template).

## <a name="individual-enrollment-based-device-connectivity"></a>Conectividade individual do dispositivo baseado em matrículas

Para os clientes que ligam dispositivos que cada um tem as suas próprias credenciais de autenticação, utilize matriculas individuais. Uma inscrição individual é uma entrada para um único dispositivo que é permitido ligar. As inscrições individuais podem usar certificados de folha X.509 ou fichas SAS (a partir de um módulo de plataforma de confiança física ou virtual) como mecanismos de atestação. O ID do dispositivo (também conhecido como ID de registo) numa matrícula individual é alfanumérico, minúsculo, e pode conter hífenes. Para mais informações, consulte [a inscrição individual do DPS.](../../iot-dps/concepts-service.md#individual-enrollment)

> [!NOTE]
> Quando cria uma inscrição individual para um dispositivo, tem precedência sobre as opções de inscrição em grupo predefinido na sua aplicação IoT Central.

### <a name="create-individual-enrollments"></a>Criar inscrições individuais

A IoT Central apoia os seguintes mecanismos de atestação para as matrículas individuais:

- **Atestado de chave simétrica:** A tecla de teclas simétricas é uma abordagem simples para autenticar um dispositivo com a instância DPS. Para criar uma inscrição individual que utilize chaves simétricas, abra a página **de Ligação** do Dispositivo, selecione **a inscrição individual** como método de ligação e assinatura de acesso partilhado **(SAS)** como mecanismo. Introduza as teclas primárias e secundárias codificadas da base64 e guarde as suas alterações. Utilize o **âmbito de identificação,** **o ID do dispositivo**e a chave primária ou secundária para ligar o dispositivo.

    > [!TIP]
    > Para testes, pode utilizar **o OpenSSL** para gerar chaves codificadas base64: `openssl rand -base64 64`

- **Certificados X.509:** Para criar uma inscrição individual com certificados X.509, abra a página **de Ligação** do Dispositivo, selecione **a inscrição individual** como método de ligação e **Certificados (X.509)** como mecanismo. Os certificados do dispositivo utilizados com uma entrada individual de inscrição têm a obrigação de que o emitente e o assunto CN estejam definidos para o ID do dispositivo.

    > [!TIP]
    > Para testes, pode utilizar [ferramentas para o dispositivo de provisionamento do dispositivo Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) gerar um certificado auto-assinado: `node create_test_cert.js device "mytestdevice"`

- **Atestado de Módulo de Plataforma Fidedigna (TPM):** Um [TPM](../../iot-dps/concepts-tpm-attestation.md) é um tipo de módulo de segurança de hardware. A utilização de um TPM é uma das formas mais seguras de ligar um dispositivo. Este artigo pressupõe que está a usar um TPM discreto, firmware ou integrado. Os TPMs emulados por software são adequados para prototipagem ou teste, mas não fornecem o mesmo nível de segurança que tPMs discretos, firmware ou TPMs integrados. Não utilize TPMs de software em produção. Para criar uma inscrição individual que utilize um TPM, abra a página **de Ligação** do Dispositivo, selecione **a inscrição individual** como método de ligação e **TPM** como mecanismo. Introduza a chave de endosso TPM e guarde as informações de ligação do dispositivo.

## <a name="automatically-associate-with-a-device-template"></a>Associar automaticamente a um modelo de dispositivo

Uma das principais características da IoT Central é a capacidade de associar automaticamente os modelos do dispositivo na ligação do dispositivo. Juntamente com as credenciais do dispositivo, os dispositivos podem enviar um **CapabilityModelId** como parte da chamada de registo do dispositivo. O **CapabilityModelID** é uma URN que identifica o modelo de capacidade que o dispositivo implementa. A aplicação IoT Central pode usar o **CapabilityModelID** para identificar o modelo do dispositivo para utilizar e, em seguida, associar automaticamente o dispositivo ao modelo do dispositivo. O processo de descoberta funciona da seguinte forma:

1. Se o modelo do dispositivo já estiver publicado na aplicação IoT Central, o dispositivo está associado ao modelo do dispositivo.
1. Para dispositivos IoT Plug e Play pré-certificados, se o modelo do dispositivo ainda não estiver publicado na aplicação IoT Central, o modelo do dispositivo é recolhido do repositório público.

Os seguintes snippets mostram o formato da carga útil adicional que o dispositivo deve enviar durante a chamada de registo DPS para que a associação automática funcione.

Este é o formato para dispositivos que utilizam o dispositivo geralmente disponível SDK:

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

Este é o formato para dispositivos que utilizam o dispositivo público de pré-visualização SDK:

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> A opção **de aprovação automática** na **ligação > dispositivo** deve ser ativada para que os dispositivos se conectem automaticamente, descubram o modelo do dispositivo e comecem a enviar dados.

## <a name="device-status-values"></a>Valores de estado do dispositivo

Quando um dispositivo real se liga à sua aplicação IoT Central, o estado do dispositivo muda da seguinte forma:

1. O estado do dispositivo é **registado**pela primeira vez. Este estado significa que o dispositivo foi criado na IoT Central e tem um ID do dispositivo. Um dispositivo é registado quando:
    - Um novo dispositivo real é adicionado na página **dispositivos.**
    - Um conjunto de dispositivos é adicionado usando **import na** página **dispositivos.**

1. O estado do dispositivo muda para **Provisionado** quando o dispositivo que ligou à sua aplicação IoT Central com credenciais válidas completa o passo de provisionamento. Neste passo, o dispositivo utiliza DPS para recuperar automaticamente uma cadeia de ligação do Hub IoT utilizado pela sua aplicação IoT Central. O dispositivo pode agora ligar-se à IoT Central e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo está bloqueado, não pode enviar dados para a sua aplicação IoT Central. Os dispositivos bloqueados têm um estado de **bloqueio.** Um operador deve reiniciar o dispositivo antes de poder retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o estado retorna ao seu valor anterior, **Registado** ou **Provisionado**.

1. Se o estado do dispositivo estiver **à espera de aprovação,** significa que a opção **auto aprovação** está desativada. Um operador deve aprovar explicitamente um dispositivo antes de começar a enviar dados. Os dispositivos não registados manualmente na página **dispositivos,** mas ligados a credenciais válidas terão o estado do dispositivo **à espera de aprovação**. Os operadores podem aprovar estes dispositivos a partir da página **dispositivos** utilizando o botão **Aprovar.**

1. Se o estado do dispositivo não estiver **associado,** significa que o dispositivo que se liga à IoT Central não tem um modelo de dispositivo associado. Esta situação acontece tipicamente nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando **importe** na página **dispositivos** sem especificar o modelo do dispositivo.
    - Um dispositivo foi registado manualmente na página **dispositivos** sem especificar o modelo do dispositivo. O dispositivo então ligado com credenciais válidas.  

    O Operador pode associar um dispositivo a um modelo de dispositivo a partir da página **dispositivos** utilizando o botão **Migrar.**

## <a name="best-practices"></a>Melhores práticas

Não persista ou cache a cadeia de ligação do dispositivo que o DPS retorna quando ligar o dispositivo pela primeira vez. Para voltar a ligar um dispositivo, passe pelo fluxo de registo padrão do dispositivo para obter a cadeia de ligação correta do dispositivo. Se o dispositivo cache a cadeia de ligação, o software do dispositivo corre o risco de ter uma cadeia de ligação velha se o IoT Central atualizar o hub Azure IoT subjacente que utiliza.

## <a name="sdk-support"></a>Suporte de SKDs

Os SDKs do dispositivo Azure oferecem a forma mais fácil de implementar o código do dispositivo. Estão disponíveis os seguintes SDKs do dispositivo:

- [SDK do Azure IoT para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do Azure IoT para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do Azure IoT para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do Azure IoT para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do Azure IoT para .NET](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funcionalidades SDK e conectividade IoT Hub

Toda a comunicação do dispositivo com o IoT Hub utiliza as seguintes opções de conectividade IoT Hub:

- [Mensagens dispositivo-a-nuvem](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Gémeos do dispositivo](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como o dispositivo Azure IoT Central apresenta o mapa nas características do IoT Hub:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Telemetria | Mensagens dispositivo-a-nuvem |
| Propriedade | Propriedades reportadas de twin dispositivo |
| Propriedade (depreciada) | Propriedades duplas do dispositivo desejadas e reportadas |
| Comando | Métodos diretos |

### <a name="protocols"></a>Protocolos

Os SDKs do dispositivo suportam os seguintes protocolos de rede para a ligação a um hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre estes protocolos de diferença e orientação sobre a escolha de um, consulte [Escolha um protocolo de comunicação.](../../iot-hub/iot-hub-devguide-protocols.md)

Se o seu dispositivo não puder utilizar nenhum dos protocolos suportados, pode utilizar o Azure IoT Edge para fazer a conversão de protocolos. O IoT Edge suporta outros cenários de inteligência no limite para descarregar o processamento para a borda da aplicação Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o seu Azure IoT Central estão encriptados. O IoT Hub autentica todos os pedidos de um dispositivo que se conecta a qualquer um dos pontos finais do IoT Hub virado para o dispositivo. Para evitar a troca de credenciais por cima do fio, um dispositivo utiliza fichas assinadas para autenticar. Para mais informações, consulte, [Controle o acesso ao IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
- Saiba como definir um novo tipo de [dispositivo IoT na sua aplicação Azure IoT Central](./howto-set-up-template.md)
- Leia sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md)