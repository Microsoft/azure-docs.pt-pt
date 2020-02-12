---
title: Instale certificados no dispositivo - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Crie certificados de teste e aprenda a instalá-los num dispositivo Azure IoT Edge para se preparar para a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149903"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Instale certificados de produção num dispositivo IoT Edge

Todos os dispositivos IoT Edge utilizam certificados para criar ligações seguras entre o tempo de funcionamento e quaisquer módulos em execução no dispositivo.
Os dispositivos IoT Edge que funcionam como gateways utilizam estes mesmos certificados para se ligarem também aos seus dispositivos a jusante.

Quando instala o IoT Edge pela primeira vez e aprovisiona o seu dispositivo, o dispositivo é configurado com certificados temporários para que possa testar o serviço.
Estes certificados temporários expiram em 90 dias, ou podem ser reiniciados reiniciando a sua máquina.
Uma vez que esteja pronto para mover os seus dispositivos para um cenário de produção, ou quer criar um cenário de gateway, precisa fornecer os seus próprios certificados.
Este artigo demonstra os passos para instalar certificados nos seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e as suas funções num cenário IoT Edge, veja [como o Azure IoT Edge utiliza certificados.](iot-edge-certs.md)

>[!NOTE]
>O termo "root CA" utilizado ao longo deste artigo refere-se ao certificado público mais alto da cadeia de certificados para a sua solução IoT. Não precisa de utilizar a raiz de certificado de uma autoridade sindical ou a raiz da autoridade de certificados da sua organização. Em muitos casos, trata-se, na verdade, de um certificado público intermédio da AC.

## <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo IoT Edge, que funciona no [Windows](how-to-install-iot-edge-windows.md) ou [no Linux.](how-to-install-iot-edge-linux.md)
* Dispre um certificado de certificado de raiz (CA), auto-assinado ou adquirido a uma autoridade de certificadocomercial confiável como Baltimore, Verisign, DigiCert ou GlobalSign.

Se ainda não tiver uma autoridade de certificado de raiz, mas queira experimentar funcionalidades IoT Edge que requerem certificados de produção (como cenários de gateway) pode [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Criar certificados de produção

Deve utilizar a sua própria autoridade de certificado para criar os seguintes ficheiros:

* AC de Raiz
* Certificado de acesso condicional de dispositivo
* Chave privada ca dispositivo

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificados mais alta para uma organização. É a autoridade de certificado satismais alta para o cenário IoT Edge, que o módulo hub IoT Edge, módulos de utilizador e quaisquer dispositivos a jusante usam para estabelecer confiança entre si.

Para ver um exemplo destes certificados, reveja os scripts que criam certificados de demonstração na Gestão de [certificados ca de teste para amostras e tutoriais](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Instalar certificados no dispositivo

Instale a sua cadeia de certificados no dispositivo IoT Edge e configure o tempo de execução do IoT Edge para fazer referência aos novos certificados.

Por exemplo, se utilizou os scripts da amostra para [criar certificados de demonstração,](how-to-create-test-certificates.md)os três ficheiros que precisa copiar para o seu dispositivo IoT Edge são os seguintes:

* Certificado CA do dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada do dispositivo CA: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Raiz CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copie os três ficheiros de certificado e chave no seu dispositivo IoT Edge.

   Pode utilizar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo de cópia [Segura](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.  Se gerou os certificados no próprio dispositivo IoT Edge, pode saltar este passo e utilizar o caminho para o diretório de trabalho.

2. Abra o ficheiro de configuração do daemon de segurança do IoT Edge.

   * Janelas: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Detete as propriedades do **certificado** no ficheiro config.yaml para o ficheiro URI do certificado e ficheiros chave no dispositivo IoT Edge. Remova o carácter `#` antes das propriedades do certificado para descomentar as quatro linhas. Certifique-se de que os **certificados:** a linha não tem espaço branco anterior e que os itens aninhados são retalhados por dois espaços. Por exemplo:

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

4. Nos dispositivos Linux, **certifique-se** de que o utilizador leu permissões para o diretório que detém os certificados.

5. Se já utilizou outros certificados para IoT Edge no dispositivo antes, elimine os ficheiros nos seguintes dois diretórios antes de iniciar ou reiniciar o IoT Edge:

   * Janelas: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>Passos seguintes

Instalar certificados num dispositivo IoT Edge é um passo necessário antes de implementar a sua solução em produção. Saiba mais sobre como preparar a implementação da [sua solução IoT Edge em produção.](production-checklist.md)
