---
title: Gerir certificados de dispositivo - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Crie certificados de teste, instale e gerencie-os num dispositivo Azure IoT Edge para se preparar para a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539211"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gerir certificados num dispositivo IoT Edge

Todos os dispositivos IoT Edge utilizam certificados para criar ligações seguras entre o tempo de funcionamento e quaisquer módulos em execução no dispositivo. Os dispositivos IoT Edge que funcionam como gateways utilizam estes mesmos certificados para se ligarem também aos seus dispositivos a jusante.

## <a name="install-production-certificates"></a>Instalar certificados de produção

Quando instala o IoT Edge pela primeira vez e aprovisiona o seu dispositivo, o dispositivo é configurado com certificados temporários para que possa testar o serviço.
Estes certificados temporários expiram em 90 dias, ou podem ser reiniciados reiniciando a sua máquina.
Uma vez que esteja pronto para mover os seus dispositivos para um cenário de produção, ou quer criar um cenário de gateway, precisa fornecer os seus próprios certificados.
Este artigo demonstra os passos para instalar certificados nos seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e as suas funções num cenário IoT Edge, veja [como o Azure IoT Edge utiliza certificados.](iot-edge-certs.md)

>[!NOTE]
>O termo "root CA" utilizado ao longo deste artigo refere-se ao certificado público mais alto da cadeia de certificados para a sua solução IoT. Não precisa de utilizar a raiz de certificado de uma autoridade sindical ou a raiz da autoridade de certificados da sua organização. Em muitos casos, trata-se, na verdade, de um certificado público intermédio da AC.

### <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo IoT Edge, que funciona no [Windows](how-to-install-iot-edge-windows.md) ou [no Linux.](how-to-install-iot-edge-linux.md)
* Dispre um certificado de certificado de raiz (CA), auto-assinado ou adquirido a uma autoridade de certificadocomercial confiável como Baltimore, Verisign, DigiCert ou GlobalSign.

Se ainda não tiver uma autoridade de certificado de raiz, mas queira experimentar funcionalidades IoT Edge que requerem certificados de produção (como cenários de gateway) pode [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Criar certificados de produção

Deve utilizar a sua própria autoridade de certificado para criar os seguintes ficheiros:

* AC de Raiz
* Certificado CA do dispositivo
* Chave privada ca dispositivo

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificados mais alta para uma organização. É a autoridade de certificado satismais alta para o cenário IoT Edge, que o módulo hub IoT Edge, módulos de utilizador e quaisquer dispositivos a jusante usam para estabelecer confiança entre si.

Para ver um exemplo destes certificados, reveja os scripts que criam certificados de demonstração na Gestão de [certificados ca de teste para amostras e tutoriais](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalar certificados no dispositivo

Instale a sua cadeia de certificados no dispositivo IoT Edge e configure o tempo de execução do IoT Edge para fazer referência aos novos certificados.

Por exemplo, se utilizou os scripts da amostra para [criar certificados de demonstração,](how-to-create-test-certificates.md)copie os seguintes ficheiros no seu dispositivo IoT-Edge:

* Certificado CA do dispositivo:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada do dispositivo CA:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Raiz CA:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copie os três ficheiros de certificado e chave no seu dispositivo IoT Edge.

   Pode utilizar um serviço como [o Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como o protocolo de cópia [Segura](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.  Se gerou os certificados no próprio dispositivo IoT Edge, pode saltar este passo e utilizar o caminho para o diretório de trabalho.

1. Abra o ficheiro de segurança da IoT Edge daemon config.

   * Janelas:`C:\ProgramData\iotedge\config.yaml`
   * Linux:`/etc/iotedge/config.yaml`

1. Detete as propriedades do **certificado** no ficheiro config.yaml no caminho completo para o certificado e ficheiros chave no dispositivo IoT Edge. Retire `#` o caracteres antes das propriedades do certificado para descomentar as quatro linhas. Certifique-se de que os **certificados:** a linha não tem espaço branco anterior e que os itens aninhados são retalhados por dois espaços. Por exemplo:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Nos dispositivos Linux, **certifique-se** de que o utilizador leu permissões para o diretório que detém os certificados.

1. Se já utilizou outros certificados para IoT Edge no dispositivo antes, elimine os ficheiros nos seguintes dois diretórios antes de iniciar ou reiniciar o IoT Edge:

   * Janelas: `C:\ProgramData\iotedge\hsm\certs` e`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Personalizar o tempo de vida do certificado

O IoT Edge gera automaticamente certificados no dispositivo em vários casos, incluindo:

* Se não fornecer os seus próprios certificados de produção quando instala e fornece IoT Edge, o gestor de segurança IoT Edge gera automaticamente um **certificado CA**do dispositivo . Este certificado auto-assinado destina-se apenas a cenários de desenvolvimento e de teste, não à produção. Este certificado expira após 90 dias.
* O gestor de segurança IoT Edge também gera um certificado CA de **carga de trabalho** assinado pelo certificado CA do dispositivo

Para obter mais informações sobre a função dos diferentes certificados num dispositivo IoT Edge, consulte [Entenda como o Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Para estes dois certificados gerados automaticamente, tem a opção de definir a bandeira **auto_generated_ca_lifetime_days** em config.yaml para configurar o número de dias para a vida útil dos certificados.

>[!NOTE]
>Há um terceiro certificado gerado automaticamente que o gestor de segurança IoT Edge cria, o certificado de servidor do **hub IoT Edge**. Este certificado tem sempre um 90 dias, mas é automaticamente renovado antes de expirar. O valor **auto_generated_ca_lifetime_days** não afeta este certificado.

Para configurar a expiração do certificado para algo diferente dos 90 dias predefinidos, adicione o valor em dias à secção de **certificados** do ficheiro config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Se forneceu os certificados CA do seu próprio dispositivo, então este valor ainda se aplica ao certificado CA de carga de trabalho, desde que o valor de vida definido seja mais curto do que o tempo de vida do certificado CA do dispositivo.

Depois de especificar a bandeira no ficheiro config.yaml, tome as seguintes medidas:

1. Apagar o conteúdo `hsm` da pasta.

   Janelas: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Reinicie o serviço IoT Edge.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confirme a definição vitalícia.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.

## <a name="next-steps"></a>Passos seguintes

Instalar certificados num dispositivo IoT Edge é um passo necessário antes de implementar a sua solução em produção. Saiba mais sobre como preparar a implementação da [sua solução IoT Edge em produção.](production-checklist.md)
