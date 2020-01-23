---
title: Instalar certificados no dispositivo-Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste e saiba como instalá-los em um dispositivo Azure IoT Edge para preparar a implantação de produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf073572cd5b371ec484c99f14cbefb4cba75ce7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509908"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Instalar certificados de produção em um dispositivo IoT Edge

Todos os dispositivos IoT Edge usam certificados para criar conexões seguras entre o tempo de execução e os módulos em execução no dispositivo.
IoT Edge dispositivos funcionando como gateways usam esses mesmos certificados para se conectar aos dispositivos downstream também.

Quando você instala o IoT Edge e provisiona o dispositivo pela primeira vez, o dispositivo é configurado com certificados temporários para que você possa testar o serviço.
Esses certificados temporários expiram em 90 dias ou podem ser redefinidos reiniciando o computador.
Quando estiver pronto para mover seus dispositivos para um cenário de produção ou se desejar criar um cenário de gateway, você precisará fornecer seus próprios certificados.
Este artigo demonstra as etapas para instalar certificados em seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e suas funções em um cenário de IoT Edge, consulte [entender como o Azure IOT Edge usa certificados](iot-edge-certs.md).

>[!NOTE]
>O termo "autoridade de certificação raiz" usado em todo este artigo refere-se ao certificado público de autoridade superior da cadeia de certificados para sua solução de IoT. Você não precisa usar a raiz do certificado de uma autoridade de certificação agregada ou a raiz da autoridade de certificação de sua organização. Em muitos casos, é, na verdade, um certificado público de CA intermediário.

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo IoT Edge, em execução no [Windows](how-to-install-iot-edge-windows.md) ou no [Linux](how-to-install-iot-edge-linux.md).
* Ter um certificado de AC (autoridade de certificação) raiz, autoassinado ou comprado de uma autoridade de certificação comercial confiável, como Baltimore, Verisign, DigiCert ou GlobalSign.

Se você ainda não tiver uma autoridade de certificação raiz, mas quiser experimentar IoT Edge recursos que exigem certificados de produção (como cenários de gateway), poderá [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Criar certificados de produção

Você deve usar sua própria autoridade de certificação para criar os seguintes arquivos:

* AC de Raiz
* Certificado de acesso condicional de dispositivo
* Chave privada da AC do dispositivo

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificação mais alta para uma organização. É a mais alta autoridade de certificação para o cenário de IoT Edge, que o IoT Edge módulo de Hub, os módulos de usuário e os dispositivos downstream usam para estabelecer a confiança entre si.

Para ver um exemplo desses certificados, examine os scripts que criam certificados de demonstração em [gerenciando certificados de AC de teste para exemplos e tutoriais](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Instalar certificados no dispositivo

Instale a cadeia de certificados no dispositivo IoT Edge e configure o tempo de execução IoT Edge para fazer referência aos novos certificados.

Por exemplo, se você usou os scripts de exemplo para [criar certificados de demonstração](how-to-create-test-certificates.md), os três arquivos que você precisa copiar para o dispositivo IOT Edge são os seguintes:

* Certificado de autoridade de certificação do dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada da AC do dispositivo: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* AC raiz: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copie os três arquivos de certificado e de chave em seu dispositivo IoT Edge.

   Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.  Se você tiver gerado os certificados no próprio dispositivo IoT Edge, poderá ignorar esta etapa e usar o caminho para o diretório de trabalho.

2. Abra o ficheiro de configuração do daemon de segurança do IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Defina as propriedades do **certificado** no arquivo config. YAML como o URI de arquivo do certificado e dos arquivos de chave no dispositivo IOT Edge. Remova o caractere de `#` antes das propriedades do certificado para remover os comentários das quatro linhas. Verifique se a linha **certificados:** não tem espaço em branco precedente e se os itens aninhados são recuados em dois espaços. Por exemplo:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Em dispositivos Linux, verifique se o usuário **iotedge** tem permissões de leitura para o diretório que contém os certificados.

## <a name="next-steps"></a>Passos seguintes

A instalação de certificados em um dispositivo IoT Edge é uma etapa necessária antes de implantar sua solução em produção. Saiba mais sobre como [se preparar para implantar sua solução de IOT Edge em produção](production-checklist.md).
