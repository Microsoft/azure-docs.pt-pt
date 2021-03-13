---
title: Conectividade do dispositivo em Azure IoT Central | Microsoft Docs
description: Este artigo introduz conceitos-chave relativos à conectividade do dispositivo na Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 1/15/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: dc0655aba424d29a4055f0d50a20057f22d084ed
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015460"
---
# <a name="get-connected-to-azure-iot-central"></a>Ligar-se ao Azure IoT Central

*Este artigo aplica-se aos operadores e desenvolvedores de dispositivos.*

Este artigo descreve como os dispositivos se ligam a uma aplicação Azure IoT Central. Antes que um dispositivo possa trocar dados com a IoT Central, deve:

- *Autenticar*. A autenticação com a aplicação IoT Central utiliza um _token de assinatura de acesso partilhado (SAS)_ ou um _certificado X.509_. Os certificados X.509 são recomendados em ambientes de produção.
- *Registe-se*. Os dispositivos devem ser registados com a aplicação IoT Central. Pode ver os dispositivos registados na página **dispositivos** na aplicação.
- *Associe-se a um modelo de dispositivo.* Numa aplicação IoT Central, os modelos de dispositivo definem o UI que os operadores usam para visualizar e gerir dispositivos conectados.

A IoT Central suporta os seguintes dois cenários de registo do dispositivo:

- *Inscrição automática.* O aparelho é registado automaticamente quando se liga pela primeira vez. Este cenário permite que os OEMs fabricem dispositivos de fabrico em massa que se possam ligar sem primeiro serem registados. Um OEM gera credenciais adequadas para dispositivos e configura os dispositivos na fábrica. Opcionalmente, pode exigir que um operador aprove o dispositivo antes de começar a enviar dados. Este cenário requer que você confirmige uma _inscrição_ do grupo X.509 ou SAS na sua aplicação.
- *Registo manual*. Os operadores registam dispositivos individuais na página **dispositivos** ou [importam um ficheiro CSV](howto-manage-devices.md#import-devices) para dispositivos de registo a granel. Neste cenário pode utilizar _a inscrição_ do grupo X.509 ou SAS, ou _inscrição individual_ de X.509 ou SAS.

Os dispositivos que se ligam à IoT Central devem seguir as *convenções IoT Plug and Play*. Uma destas convenções é que um dispositivo deve enviar o _ID_ do modelo do dispositivo que implementa quando se conecta. O ID do modelo permite que a aplicação IoT Central associe o dispositivo ao modelo de dispositivo correto.

A IoT Central utiliza o [serviço de provisionamento de dispositivos Azure IoT Hub (DPS)](../../iot-dps/about-iot-dps.md) para gerir o processo de ligação. Um dispositivo liga-se primeiro a um ponto final DPS para recuperar a informação de que necessita para se ligar à sua aplicação. Internamente, a sua aplicação IoT Central utiliza um hub IoT para lidar com a conectividade do dispositivo. A utilização de DPS permite:

- IoT Central para suportar dispositivos de embarque e ligação à escala.
- Você deve gerar credenciais de dispositivo e configurar os dispositivos offline sem registar os dispositivos através da IoT Central UI.
- Você deve usar os seus próprios IDs de dispositivo para registar dispositivos na IoT Central. A utilização dos iDs do seu próprio dispositivo simplifica a integração com os sistemas de back-office existentes.
- Uma forma única e consistente de ligar os dispositivos à IoT Central.

Este artigo descreve os seguintes passos de ligação do dispositivo:

- [Inscrição em grupo X.509](#x509-group-enrollment)
- [Inscrição no grupo SAS](#sas-group-enrollment)
- [Inscrição individual](#individual-enrollment)
- [Registo de dispositivo](#device-registration)
- [Associe um dispositivo a um modelo de dispositivo](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>Inscrição em grupo X.509

Num ambiente de produção, a utilização de certificados X.509 é o mecanismo de autenticação recomendado para a IoT Central. Para saber mais, consulte [a autenticação do dispositivo utilizando certificados X.509 CA](../../iot-hub/iot-hub-x509ca-overview.md).

Para ligar um dispositivo com um certificado X.509 à sua aplicação:

1. Crie um *grupo de inscrições* que utilize o tipo **de atestado certificados (X.509).**
1. Adicione e verifique um certificado X.509 intermédio ou raiz no grupo de inscrição.
1. Gere um certificado de folha a partir da raiz ou certificado intermédio no grupo de inscrição. Envie o certificado de folha do dispositivo quando este se ligar à sua aplicação.

Para saber mais, consulte [Como conectar dispositivos com certificados X.509](how-to-connect-devices-x509.md)

### <a name="for-testing-purposes-only"></a>Apenas para fins de teste

Apenas para testes, pode utilizar os seguintes utilitários para gerar certificados de raiz, intermédios e dispositivos:

- [Ferramentas para o Dispositivo de Provisionamento de DispositivoS Azure IoT SDK:](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)uma coleção de ferramentas Node.js que pode utilizar para gerar e verificar certificados e chaves X.509.
- Se estiver a utilizar um dispositivo DevKit, esta [ferramenta de linha de comando](https://aka.ms/iotcentral-docs-dicetool) gera um certificado de CA que pode adicionar à sua aplicação IoT Central para verificar os certificados.
- [Gerir certificados de CA de teste para amostras e tutoriais:](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)uma coleção de scripts PowerShell e Bash para:
  - Criar uma cadeia de certificados.
  - Guarde os certificados como .cer ficheiros para fazer o upload para a sua aplicação IoT Central.
  - Utilize o código de verificação a partir da aplicação IoT Central para gerar o certificado de verificação.
  - Crie certificados de folha para os seus dispositivos utilizando os IDs do seu dispositivo como parâmetro para a ferramenta.

## <a name="sas-group-enrollment"></a>Inscrição no grupo SAS

Para ligar um dispositivo com a chave SAS do dispositivo à sua aplicação:

1. Crie um *grupo de inscrições* que utilize o tipo de atestado de **Assinatura de Acesso Partilhado (SAS).**
1. Copie a chave primária ou secundária do grupo de inscrição.
1. Utilize o CLI Azure para gerar uma chave de dispositivo a partir da chave de grupo:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Utilize a chave do dispositivo gerada quando o dispositivo se ligar à sua aplicação IoT Central.

## <a name="individual-enrollment"></a>Inscrição individual

Os clientes que conectam dispositivos que cada um tem as suas próprias credenciais de autenticação, usam as matrículas individuais. Uma inscrição individual é uma entrada para um único dispositivo que é permitido ligar. As inscrições individuais podem usar certificados de folha X.509 ou fichas SAS (a partir de um módulo de plataforma de confiança física ou virtual) como mecanismos de atestação. Um ID do dispositivo pode conter letras, números e `-` o caráter. Para mais informações, consulte [a inscrição individual do DPS.](../../iot-dps/concepts-service.md#individual-enrollment)

> [!NOTE]
> Quando cria uma inscrição individual para um dispositivo, tem precedência sobre as opções de inscrição em grupo predefinido na sua aplicação IoT Central.

### <a name="create-individual-enrollments"></a>Criar inscrições individuais

A IoT Central apoia os seguintes mecanismos de atestação para as matrículas individuais:

- **Atestado de chave simétrica:** A tecla de teclas simétricas é uma abordagem simples para autenticar um dispositivo com a instância DPS. Para criar uma inscrição individual que utilize chaves simétricas, abra a página **de ligação do Dispositivo** para o dispositivo, selecione a **inscrição individual** como método de ligação e assinatura de acesso partilhado **(SAS)** como mecanismo. Introduza as teclas primárias e secundárias codificadas da base64 e guarde as suas alterações. Utilize o **âmbito de identificação,** **o ID do dispositivo** e a chave primária ou secundária para ligar o dispositivo.

    > [!TIP]
    > Para testes, pode utilizar **o OpenSSL** para gerar chaves codificadas base64: `openssl rand -base64 64`

- **Certificados X.509:** Para criar uma inscrição individual com certificados X.509, abra a página **de Ligação** do Dispositivo, selecione **a inscrição individual** como método de ligação e **Certificados (X.509)** como mecanismo. Os certificados do dispositivo utilizados com uma entrada individual de inscrição têm a obrigação de que o emitente e o assunto CN estejam definidos para o ID do dispositivo.

    > [!TIP]
    > Para testes, pode utilizar [ferramentas para o dispositivo de provisionamento do dispositivo Azure IoT SDK para Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools) gerar um certificado auto-assinado: `node create_test_cert.js device "mytestdevice"`

- **Atestado de Módulo de Plataforma Fidedigna (TPM):** Um [TPM](../../iot-dps/concepts-tpm-attestation.md) é um tipo de módulo de segurança de hardware. A utilização de um TPM é uma das formas mais seguras de ligar um dispositivo. Este artigo pressupõe que está a usar um TPM discreto, firmware ou integrado. Os TPMs emulados por software são adequados para prototipagem ou teste, mas não fornecem o mesmo nível de segurança que tPMs discretos, firmware ou TPMs integrados. Não utilize TPMs de software em produção. Para criar uma inscrição individual que utilize um TPM, abra a página **de Ligação** do Dispositivo, selecione **a inscrição individual** como método de ligação e **TPM** como mecanismo. Introduza a chave de endosso TPM e guarde as informações de ligação do dispositivo.

## <a name="device-registration"></a>Registo de dispositivo

Antes de um dispositivo poder ligar-se a uma aplicação IoT Central, deve estar registado na aplicação:

- Os dispositivos podem registar-se automaticamente quando se ligam pela primeira vez. Para utilizar esta opção, deve utilizar a inscrição do [grupo X.509](#x509-group-enrollment) ou [a inscrição no grupo SAS.](#sas-group-enrollment)
- Um operador pode importar um ficheiro CSV para registar em massa uma lista de dispositivos na aplicação.
- Um operador pode registar manualmente um dispositivo individual na página **dispositivos** na aplicação.

A IoT Central permite que os OEMs fabricem dispositivos de fabrico em massa que se possam registar automaticamente. Um OEM gera credenciais adequadas para dispositivos e configura os dispositivos na fábrica. Quando um cliente liga um dispositivo pela primeira vez, liga-se ao DPS, que liga automaticamente o dispositivo à aplicação IoT Central correta. Opcionalmente, pode exigir que um operador aprove o dispositivo antes de começar a enviar dados para a aplicação.

> [!TIP]
> Na página de ligação > **dispositivo da Administração,** a opção **Auto aprova** os controlos se um operador deve aprovar manualmente o dispositivo antes de poder começar a enviar dados.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>Registar automaticamente dispositivos que utilizam certificados X.509

1. Gere os certificados de folha para os seus dispositivos utilizando o certificado raiz ou intermédio que adicionou ao seu [grupo de matrículas X.509](#x509-group-enrollment). Utilize as identificações do dispositivo como `CNAME` certificados de folha. Um ID do dispositivo pode conter letras, números e `-` o caráter.

1. Como um OEM, flash cada dispositivo com um ID do dispositivo, um certificado de folha X.509 gerado e o valor de **alcance de identificação** da aplicação. O código do dispositivo também deve enviar o ID do modelo do dispositivo que implementa.

1. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as informações de ligação IoT Central.

1. O dispositivo utiliza as informações do DPS para ligar e registar-se com a sua aplicação IoT Central.

A aplicação IoT Central utiliza o ID modelo enviado pelo dispositivo para [associar o dispositivo registado a um modelo de dispositivo](#associate-a-device-with-a-device-template).

### <a name="automatically-register-devices-that-use-sas-tokens"></a>Registar automaticamente dispositivos que utilizam fichas SAS

1. Copie a chave primária do grupo do grupo de inscrição **SAS-IoT-Devices:**

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="Chave primária do grupo SAS-IoT-Devices":::

1. Utilize o `az iot central device compute-device-key` comando para gerar as teclas SAS do dispositivo. Utilize a chave primária do grupo a partir do passo anterior. O ID do dispositivo pode conter letras, números e `-` o carácter:

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. Como um OEM, flash cada dispositivo com o ID do dispositivo, a chave SAS do dispositivo gerado e o valor de **alcance de identificação** da aplicação. O código do dispositivo também deve enviar o ID do modelo do dispositivo que implementa.

1. Quando liga um dispositivo, liga-se primeiro ao DPS para recuperar as informações de registo da IoT Central.

1. O dispositivo utiliza as informações do DPS para ligar e registar-se com a sua aplicação IoT Central.

A aplicação IoT Central utiliza o ID modelo enviado pelo dispositivo para [associar o dispositivo registado a um modelo de dispositivo](#associate-a-device-with-a-device-template).

### <a name="bulk-register-devices-in-advance"></a>Dispositivos de registo a granel com antecedência

Para registar um grande número de dispositivos com a sua aplicação IoT Central, utilize um ficheiro CSV para [importar IDs e nomes de dispositivos](howto-manage-devices.md#import-devices).

Se os seus dispositivos utilizarem fichas SAS para autenticar, [exporte um ficheiro CSV da sua aplicação IoT Central](howto-manage-devices.md#export-devices). O ficheiro CSV exportado inclui os IDs do dispositivo e as teclas SAS.

Se os seus dispositivos utilizarem certificados X.509 para autenticar, gere certificados de folha X.509 para os seus dispositivos utilizando o certificado raiz ou intermédio no seu grupo de matrículas X.509. Utilize as identificações do dispositivo que importou como `CNAME` valor nos certificados de folha.

Os dispositivos devem utilizar o valor **de ID Scope** para a sua aplicação e enviar um ID de modelo quando se ligarem.

> [!TIP]
> Pode encontrar o valor **do ID Scope** na **ligação > dispositivo da administração**.

### <a name="register-a-single-device-in-advance"></a>Registar um único dispositivo com antecedência

Esta abordagem é útil quando se está a experimentar com ioT Central ou dispositivos de teste. Selecione **+ Novo** na página **dispositivos** para registar um dispositivo individual. Pode utilizar as teclas SAS de ligação ao dispositivo para ligar o dispositivo à sua aplicação IoT Central. Copie a _chave SAS_ do dispositivo a partir das informações de ligação de um dispositivo registado:

![Chaves SAS para um dispositivo individual](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>Associe um dispositivo a um modelo de dispositivo

O IoT Central associa automaticamente um dispositivo a um modelo de dispositivo quando o dispositivo se liga. Um dispositivo envia um [ID do modelo](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id) quando se conecta. A IoT Central utiliza o ID do modelo para identificar o modelo do dispositivo para esse modelo específico do dispositivo. O processo de descoberta funciona da seguinte forma:

1. Se o modelo do dispositivo já estiver publicado na aplicação IoT Central, o dispositivo está associado ao modelo do dispositivo.
1. Se o modelo do dispositivo ainda não estiver publicado na aplicação IoT Central, a IoT Central procura o modelo do dispositivo no [repositório de modelos públicos](https://github.com/Azure/iot-plugandplay-models). Se a IoT Central encontrar o modelo, utiliza-o para gerar um modelo de dispositivo básico.
1. Se a IoT Central não encontrar o modelo no repositório de modelos públicos, o dispositivo está marcado como **Não Associado**. Um operador pode criar um modelo de dispositivo para o dispositivo e, em seguida, migrar o dispositivo não associado para o novo modelo do dispositivo.

A imagem que se segue mostra-lhe como visualizar o ID do modelo de um modelo de dispositivo na IoT Central. Num modelo de dispositivo, selecione um componente e, em seguida, **selecione Ver identidade**:

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="Screenshot mostrando o ID do modelo no modelo do dispositivo do termóstato.":::

Pode ver [o modelo do termóstato](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json) no repositório de modelos públicos. A definição de ID do modelo parece:

```json
"@id": "dtmi:com:example:Thermostat;1"
```

## <a name="device-status-values"></a>Valores de estado do dispositivo

Quando um dispositivo real se liga à sua aplicação IoT Central, o estado do dispositivo muda da seguinte forma:

1. O estado do dispositivo é **registado** pela primeira vez. Este estado significa que o dispositivo foi criado na IoT Central e tem um ID do dispositivo. Um dispositivo é registado quando:
    - Um novo dispositivo real é adicionado na página **dispositivos.**
    - Um conjunto de dispositivos é adicionado usando **import na** página **dispositivos.**

1. O estado do dispositivo muda para **Provisionado** quando o dispositivo que ligou à sua aplicação IoT Central com credenciais válidas completa o passo de provisionamento. Neste passo, o dispositivo utiliza DPS para recuperar automaticamente uma cadeia de ligação do Hub IoT utilizado pela sua aplicação IoT Central. O dispositivo pode agora ligar-se à IoT Central e começar a enviar dados.

1. Um operador pode bloquear um dispositivo. Quando um dispositivo está bloqueado, não pode enviar dados para a sua aplicação IoT Central. Os dispositivos bloqueados têm um estado de **bloqueio.** Um operador deve reiniciar o dispositivo antes de poder retomar o envio de dados. Quando um operador desbloqueia um dispositivo, o estado retorna ao seu valor anterior, **Registado** ou **Provisionado**.

1. Se o estado do dispositivo estiver **à espera de aprovação,** significa que a opção **auto aprovação** está desativada. Um operador deve aprovar explicitamente um dispositivo antes de começar a enviar dados. Os dispositivos não registados manualmente na página **dispositivos,** mas ligados a credenciais válidas terão o estado do dispositivo **à espera de aprovação**. Os operadores podem aprovar estes dispositivos a partir da página **dispositivos** utilizando o botão **Aprovar.**

1. Se o estado do dispositivo não estiver **associado,** significa que o dispositivo que se liga à IoT Central não tem um modelo de dispositivo associado. Esta situação acontece tipicamente nos seguintes cenários:

    - Um conjunto de dispositivos é adicionado usando **importe** na página **dispositivos** sem especificar o modelo do dispositivo.
    - Um dispositivo foi registado manualmente na página **dispositivos** sem especificar o modelo do dispositivo. O dispositivo então ligado com credenciais válidas.  

    O Operador pode associar um dispositivo a um modelo de dispositivo a partir da página **dispositivos** utilizando o botão **Migrar.**

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
- [Mensagens nuvem-para-dispositivo](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [Gémeos do dispositivo](../../iot-hub/iot-hub-devguide-device-twins.md)

A tabela a seguir resume como o dispositivo Azure IoT Central apresenta o mapa nas características do IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Telemetria | Mensagens dispositivo-a-nuvem |
| Comandos offline | Mensagens nuvem-para-dispositivo |
| Propriedade | Propriedades reportadas de twin dispositivo |
| Propriedade (depreciada) | Propriedades duplas do dispositivo desejadas e reportadas |
| Comando | Métodos diretos |

### <a name="protocols"></a>Protocolos

Os SDKs do dispositivo suportam os seguintes protocolos de rede para a ligação a um hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre estes protocolos de diferença e orientação sobre a escolha de um, consulte [Escolha um protocolo de comunicação.](../../iot-hub/iot-hub-devguide-protocols.md)

Se o seu dispositivo não puder utilizar nenhum dos protocolos suportados, utilize o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge suporta outros cenários de inteligência no limite para descarregar o processamento a partir da aplicação Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o seu Azure IoT Central estão encriptados. O IoT Hub autentica todos os pedidos de um dispositivo que se conecta a qualquer um dos pontos finais do IoT Hub virado para o dispositivo. Para evitar a troca de credenciais por cima do fio, um dispositivo utiliza fichas assinadas para autenticar. Para mais informações, consulte, [Controle o acesso ao IoT Hub](../../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Passos seguintes

Se você é um desenvolvedor de dispositivos, alguns passos sugeridos seguintes são:

- Reveja as [melhores práticas](concepts-best-practices.md) para o desenvolvimento de dispositivos.
- Reveja algum código de amostra que mostre como usar fichas SAS em [Tutorial: Criar e ligar uma aplicação de cliente à sua aplicação Azure IoT Central](tutorial-connect-device.md)
- Saiba como [conectar dispositivos com certificados X.509 utilizando Node.js dispositivo SDK para aplicação central IoT](how-to-connect-devices-x509.md)
- Saiba como monitorizar a [conectividade do dispositivo utilizando o Azure CLI](./howto-monitor-devices-azure-cli.md)
- Leia sobre [dispositivos Azure IoT Edge e Azure IoT Central](./concepts-iot-edge.md)
