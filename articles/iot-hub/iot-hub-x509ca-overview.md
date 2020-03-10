---
title: Visão geral da segurança do Azure IoT Hub X.509 CA Microsoft Docs
description: Visão geral - como autenticar dispositivos para IoT Hub utilizando as Autoridades de Certificados X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381987"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Autenticação do dispositivo utilizando certificados X.509 CA

Este artigo descreve como utilizar certificados x.509 da Autoridade de Certificados (CA) para autenticar dispositivos que ligam o IoT Hub.  Neste artigo aprenderá:

* Como obter um certificado De Ca X.509
* Como registar o certificado X.509 CA ao IoT Hub
* Como assinar dispositivos usando certificados X.509 CA
* Como os dispositivos assinados com X.509 CA são autenticados

## <a name="overview"></a>Descrição geral

A função X.509 CA permite a autenticação do dispositivo ao IoT Hub utilizando uma Autoridade de Certificados (CA). Simplifica consideravelmente o processo inicial de inscrição do dispositivo e a logística da cadeia de fornecimento durante o fabrico de dispositivos. [Saiba mais neste artigo sobre o valor da utilização de certificados X.509 CA](iot-hub-x509ca-concept.md) para autenticação de dispositivos.  Encorajamo-lo a ler este artigo de cenário antes de prosseguir, pois explica por que razão existem os passos que se seguem.

## <a name="prerequisite"></a>Pré-requisito

A utilização da função X.509 CA requer que tenha uma conta IoT Hub.  [Aprenda a criar um caso IoT Hub](quickstart-send-telemetry-dotnet.md) se ainda não tiver um.

## <a name="how-to-get-an-x509-ca-certificate"></a>Como obter um certificado De Ca X.509

O certificado X.509 CA está no topo da cadeia de certificados para cada um dos seus dispositivos.  Pode comprar ou criar um dependendo da forma como pretende utilizá-lo.

Para o ambiente de produção, recomendamos que adquira um certificado De Ca X.509 a uma autoridade pública de certificados de raiz. A aquisição de um certificado ca tem o benefício da raiz da AC agindo como um terceiro de confiança para garantir a legitimidade dos seus dispositivos. Considere esta opção se pretender que os seus dispositivos façam parte de uma rede IoT aberta onde se espera que interajam com produtos ou serviços de terceiros.

Também pode criar um X.509 CA auto-assinado para experimentação ou para utilização em redes IoT fechadas.

Independentemente de como obtém o seu certificado X.509 CA, certifique-se de manter a sua chave privada correspondente em segredo e protegida em todos os momentos.  Isto é necessário para a confiança na autenticação X.509 CA.

Saiba como [criar um certificado CA auto-assinado,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)que pode utilizar para experimentação ao longo desta descrição da funcionalidade.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Assine os dispositivos na cadeia de confiança de certificados

O proprietário de um certificado De Ca X.509 pode assinar criptograficamente um CA intermédio que por sua vez pode assinar outro CA intermédio, e assim por diante, até que o último CA intermédio encerre este processo assinando um dispositivo. O resultado é uma cadeia de certificados em cascata conhecida como cadeia de fidedignidade de certificados. Na vida real, isto funciona como uma delegação de confiança para a assinatura de dispositivos. Esta delegação é importante porque estabelece uma cadeia de custódia criptograficamente variável e evita a partilha de chaves de assinatura.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

O certificado do dispositivo (também chamado de certificado de folha) deve ter o nome de *assunto* definido para o ID do **dispositivo** que foi utilizado ao registar o dispositivo IoT no Hub Azure IoT. Esta definição é necessária para autenticação.

Aprenda aqui como criar uma cadeia de [certificados](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) como feito ao assinar dispositivos.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Como registar o certificado X.509 CA ao IoT Hub

Registe o seu certificado X.509 CA no IoT Hub, onde será utilizado para autenticar os seus dispositivos durante o registo e ligação.  Registar o certificado X.509 CA é um processo em duas etapas que inclui o upload de ficheirode certificado e o comprovativo de posse.

O processo de upload implica o upload de um ficheiro que contém o seu certificado.  Este ficheiro nunca deve conter chaves privadas.

A prova de passo de posse envolve um desafio criptográfico e processo de resposta entre si e o IoT Hub.  Dado que os conteúdos de certificados digitais são públicos e, portanto, suscetíveis a escutas, o IoT Hub gostaria de verificar se realmente possui o certificado CA.  Fá-lo-á gerando um desafio aleatório que deve assinar com a chave privada correspondente do certificado CA.  Se manteve a chave privada em segredo e protegida como anteriormente aconselhado, então só possuirá o conhecimento para completar este passo. O sigilo das chaves privadas é a fonte de confiança neste método.  Depois de assinar o desafio, complete este passo carregando um ficheiro contendo os resultados.

Saiba aqui como [registar o seu certificado CA](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Como criar um dispositivo no IoT Hub

Para impedir a personificação do dispositivo, o IoT Hub exige que informe quais os dispositivos a esperar.  Faça-o criando uma entrada de dispositivo no registo de dispositivos do IoT Hub.  Este processo é automatizado ao utilizar o Serviço de [Provisionamento de Dispositivos](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)IoT Hub . 

Aprenda aqui como [criar manualmente um dispositivo no IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Crie um dispositivo X.509 para o seu hub IoT

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Dispositivos de autenticação assinados com certificados X.509 CA

Com o certificado X.509 CA registado e os dispositivos assinados numa cadeia de confiança de certificado, o que resta é a autenticação do dispositivo quando o dispositivo se conecta, mesmo pela primeira vez.  Quando um dispositivo x.509 ca assinado se conecta, ele carrega a sua cadeia de certificados para validação. A cadeia inclui todos os certificados intermédios de CA e dispositivo.  Com esta informação, o IoT Hub autentica o dispositivo num processo em duas etapas.  O IoT Hub valida criptograficamente a cadeia de certificados para consistência interna e, em seguida, emite um desafio de prova de posse para o dispositivo.  O IoT Hub declara o dispositivo autêntico numa resposta de prova de posse bem sucedida do dispositivo.  Esta declaração pressupõe que a chave privada do dispositivo está protegida e que apenas o dispositivo pode responder com sucesso a este desafio.  Recomendamos a utilização de chips seguros como módulos de segurança de hardware (HSM) em dispositivos para proteger as chaves privadas.

Uma ligação bem sucedida do dispositivo ao IoT Hub completa o processo de autenticação e é também indicativo de uma configuração adequada.

Saiba aqui como [completar este passo de ligação](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)do dispositivo .

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre [o valor da autenticação X.509 CA](iot-hub-x509ca-concept.md) em IoT.

Inicie-se com o Serviço de Provisionamento de [Dispositivos](https://docs.microsoft.com/azure/iot-dps/)IoT Hub .
