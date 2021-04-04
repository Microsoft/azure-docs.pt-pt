---
title: Visão geral do Azure IoT Hub X.509 CA | Microsoft Docs
description: Visão geral - como autenticar dispositivos no IoT Hub utilizando as Autoridades de Certificados X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92150704"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autenticação do Dispositivo com Certificados AC X.509

Este artigo descreve como utilizar certificados X.509 Certificate Authority (CA) para autenticar dispositivos que ligam o IoT Hub.  Neste artigo vai aprender:

* Como obter um certificado de CA X.509
* Como registar o certificado X.509 CA para ioT Hub
* Como assinar dispositivos utilizando certificados X.509 CA
* Como os dispositivos assinados com X.509 CA são autenticados

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Descrição Geral

A função X.509 CA permite a autenticação do dispositivo ao IoT Hub utilizando uma Autoridade de Certificados (CA). Simplifica consideravelmente o processo inicial de inscrição do dispositivo e a logística da cadeia de fornecimento durante o fabrico do dispositivo. [Saiba mais neste cenário sobre o valor da utilização de certificados X.509 CA](iot-hub-x509ca-concept.md) para autenticação de dispositivos.  Encorajamo-lo a ler este artigo de cenário antes de prosseguir, pois explica porque é que os passos que se seguem existem.

## <a name="prerequisite"></a>Pré-requisito

A utilização da função X.509 CA requer que tenha uma conta IoT Hub.  [Aprenda a criar uma instância IoT Hub](quickstart-send-telemetry-dotnet.md) se ainda não tiver uma.

## <a name="how-to-get-an-x509-ca-certificate"></a>Como obter um certificado de CA X.509

O certificado X.509 CA está no topo da cadeia de certificados para cada um dos seus dispositivos.  Pode comprar ou criar um dependendo da sua intenção de usá-lo.

Para o ambiente de produção, recomendamos que compre um certificado X.509 CA a uma autoridade pública de certificados de raiz. A compra de um certificado de CA tem o benefício da raiz ca agindo como um terceiro de confiança para atestar a legitimidade dos seus dispositivos. Considere esta opção se pretender que os seus dispositivos sejam parte de uma rede IoT aberta onde se espera que interajam com produtos ou serviços de terceiros.

Também pode criar um X.509 CA auto-assinado para experimentação ou para utilização em redes IoT fechadas.

Independentemente de como obtém o seu certificado X.509 CA, certifique-se de manter o seu segredo de chave privada correspondente e protegido em todos os momentos.  Isto é necessário para a confiança na confiança na autenticação da X.509 CA.

Saiba como [criar um certificado ca auto-assinado,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)que pode usar para experimentação ao longo desta descrição do recurso.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Assinar dispositivos na cadeia de certificados de confiança

O proprietário de um certificado de CA X.509 pode assinar criptograficamente um CA intermédio que pode, por sua vez, assinar outro CA intermédio, e assim por diante, até que o último CA intermédio encerre este processo assinando um dispositivo. O resultado é uma cadeia de certificados em cascata conhecida como uma cadeia de certificados de confiança. Na vida real, isto funciona como delegação de confiança para os dispositivos de assinatura. Esta delegação é importante porque estabelece uma cadeia de custódia criptograficamente variável e evita a partilha de chaves de assinatura.

![img-genérico-cert-cadeia-de-confiança](./media/generic-cert-chain-of-trust.png)

O certificado do dispositivo (também denominado certificado de folha) deve ter o *Nome do Sujeito* definido para o **ID** do dispositivo ( `CN=deviceId` ) que foi utilizado ao registar o dispositivo IoT no Azure IoT Hub. Esta definição é necessária para a autenticação.

Saiba aqui como [criar uma cadeia de certificados](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) como feito ao assinar dispositivos.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Como registar o certificado X.509 CA para ioT Hub

Registe o certificado X.509 CA no IoT Hub, onde será utilizado para autenticar os seus dispositivos durante o registo e ligação.  Registar o certificado X.509 CA é um processo em duas etapas que compreende o upload de ficheiros de certificado e o comprovativo de posse.

O processo de upload implica o upload de um ficheiro que contém o seu certificado.  Este ficheiro nunca deve conter chaves privadas.

A prova de passo de posse envolve um desafio criptográfico e processo de resposta entre si e o IoT Hub.  Dado que os conteúdos de certificados digitais são públicos e, portanto, suscetíveis a escutas, o IoT Hub gostaria de verificar se é realmente dono do certificado de CA.  Fá-lo-á gerando um desafio aleatório que deve assinar com a chave privada correspondente do certificado de A.  Se manteve o segredo privado e protegido como anteriormente aconselhado, só o seu conhecimento terá o conhecimento para completar este passo. O sigilo das chaves privadas é a fonte de confiança neste método.  Depois de assinar o desafio, complete este passo carregando um ficheiro contendo os resultados.

Saiba aqui como [registar o seu certificado de CA](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Como criar um dispositivo no IoT Hub

Para impedir a personificação do dispositivo, o IoT Hub requer que informe quais os dispositivos que esperar.  Fá-lo criando uma entrada de dispositivo no registo de dispositivos do IoT Hub.  Este processo é automatizado quando se utiliza o [Serviço de Provisionamento de Dispositivos](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)IoT Hub . 

Saiba aqui como [criar manualmente um dispositivo no IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Crie um dispositivo X.509 para o seu hub IoT

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Dispositivos de autenticação assinados com certificados X.509 CA

Com o certificado X.509 CA registado e os dispositivos assinados numa cadeia de certificados de confiança, o que resta é a autenticação do dispositivo quando o dispositivo se conecta, mesmo pela primeira vez.  Quando um dispositivo assinado por X.509 CA se conecta, carrega a sua cadeia de certificados para validação. A cadeia inclui todos os certificados intermédios de CA e dispositivos.  Com esta informação, o IoT Hub autentica o dispositivo num processo em duas etapas.  O IoT Hub valida criptograficamente a cadeia de certificados para consistência interna e, em seguida, emite um desafio de prova de posse para o dispositivo.  O IoT Hub declara o dispositivo autêntico numa resposta de prova de posse bem sucedida do dispositivo.  Esta declaração pressupõe que a chave privada do dispositivo está protegida e que apenas o dispositivo pode responder com sucesso a este desafio.  Recomendamos a utilização de chips seguros como Hardware Secure Modules (HSM) em dispositivos para proteger chaves privadas.

Uma ligação bem sucedida do dispositivo ao IoT Hub completa o processo de autenticação e também é indicativa de uma configuração adequada.

Saiba aqui como [completar este passo de ligação](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)do dispositivo .

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [o valor da autenticação X.509 CA](iot-hub-x509ca-concept.md) em IoT.

Começa com o [Serviço de Provisionamento de Dispositivos](../iot-dps/index.yml)IoT Hub .