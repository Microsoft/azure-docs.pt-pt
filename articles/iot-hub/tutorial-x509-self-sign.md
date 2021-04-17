---
title: Tutorial - Use OpenSSL para criar certificados auto-assinados para Azure IoT Hub | Microsoft Docs
description: Tutorial - Use OpenSSL para criar certificados X.509 auto-assinados para Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378198"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Tutorial: Utilização do OpenSSL para criar certificados auto-assinados

Pode autenticar um dispositivo no seu Hub IoT utilizando dois certificados de dispositivo auto-assinados. Isto às vezes é chamado de autenticação de impressão digital porque os certificados contêm impressões digitais (valores de haxixe) que submete ao hub IoT. Os seguintes passos dizem-lhe como criar dois certificados auto-assinados.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Passo 1 - Criar uma chave para o primeiro certificado

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Passo 2 - Criar uma RSE para o primeiro certificado

Certifique-se de que especifica o ID do dispositivo quando solicitado.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Passo 3 - Verifique a RSE

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Passo 4 - Certificado de auto-sinalização 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Passo 5 - Criar uma chave para o certificado 2

Quando solicitado, especifique o mesmo ID do dispositivo que utilizou para o certificado 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Passo 6 - Certificado de auto-sinalização 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Passo 7 - Recuperar a impressão digital para o certificado 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Passo 8 - Recuperar a impressão digital para o certificado 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Passo 9 - Criar um novo dispositivo IoT

Navegue até ao seu Hub IoT no portal Azure e crie uma nova identidade do dispositivo IoT com as seguintes características:

* Forneça o **ID** do dispositivo que corresponda ao nome do sujeito dos seus dois certificados.
* Selecione o tipo de autenticação **auto-assinada X.509.**
* Cole as impressões digitais das cordas hexaxadas que copiou dos certificados primários e secundários do seu dispositivo. Certifique-se de que as cordas do hexáxe não têm delimiters de cólon.

## <a name="next-steps"></a>Passos Seguintes

Vá à [Autenticação do Certificado de Teste](tutorial-x509-test-certificate.md) para determinar se o seu certificado pode autenticar o seu dispositivo no seu Hub IoT.
