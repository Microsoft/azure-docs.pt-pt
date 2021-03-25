---
title: Gerir certificados de dispositivo - Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste, instale-os e gerencie-os num dispositivo Azure IoT Edge para preparar a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f3b6bd19d47658e5ad079f0b731cbafc866bb333
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045778"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gerir certificados num dispositivo IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Todos os dispositivos IoT Edge utilizam certificados para criar ligações seguras entre o runtime e quaisquer módulos em execução no dispositivo. Os dispositivos IoT Edge que funcionam como gateways usam estes mesmos certificados para se ligarem aos seus dispositivos a jusante, também.

## <a name="install-production-certificates"></a>Instalar certificados de produção

Quando instala pela primeira vez o IoT Edge e fornece o seu dispositivo, o dispositivo é configurado com certificados temporários para que possa testar o serviço.
Estes certificados temporários expiram em 90 dias, ou podem ser reiniciados reiniciando a sua máquina.
Uma vez que você se move em um cenário de produção, ou você quer criar um dispositivo gateway, você precisa fornecer seus próprios certificados.
Este artigo demonstra os passos para instalar certificados nos seus dispositivos IoT Edge.

Para saber mais sobre os diferentes tipos de certificados e suas funções, consulte [como a Azure IoT Edge utiliza certificados.](iot-edge-certs.md)

>[!NOTE]
>O termo "root CA" utilizado ao longo deste artigo refere-se ao certificado público de autoridade mais alta da cadeia de certificados para a sua solução IoT. Não precisa de utilizar a raiz de certificado de uma autoridade de certificado sindicado, nem a raiz da autoridade de certificados da sua organização. Em muitos casos, trata-se, na verdade, de um certificado público intermédio da AC.

### <a name="prerequisites"></a>Pré-requisitos

* Um dispositivo IoT Edge.

  Se não tiver um dispositivo IoT Edge configurado, pode criar um numa máquina virtual Azure. Siga os passos num dos artigos de arranque rápido para [criar um dispositivo Linux virtual](quickstart-linux.md) ou criar um dispositivo Virtual [Windows](quickstart.md).

* Possua um certificado de autoridade de certificados de raiz (CA), auto-assinado ou adquirido a uma autoridade de certificados comerciais fidedignas como Baltimore, Verisign, DigiCert ou GlobalSign.

  Se ainda não tiver uma autoridade de certificados de raiz, mas quiser experimentar funcionalidades IoT Edge que requerem certificados de produção (como cenários de gateway) pode [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Criar certificados de produção

Deve utilizar a sua própria autoridade de certificados para criar os seguintes ficheiros:

* AC de Raiz
* Certificado ca dispositivo
* Chave privada do dispositivo CA

Neste artigo, o que chamamos de *CA raiz* não é a autoridade de certificados mais alta para uma organização. É a autoridade de certificados mais alta para o cenário IoT Edge, que o módulo hub IoT Edge, módulos de utilizador e quaisquer dispositivos a jusante usam para estabelecer confiança entre si.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Atualmente, uma limitação no libiothsmo impede a utilização de certificados que expirem em ou após 1 de janeiro de 2038.

:::moniker-end

Para ver um exemplo destes certificados, reveja os scripts que criam certificados de demonstração em [Gestão de certificados ca para amostras e tutoriais.](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)

### <a name="install-certificates-on-the-device"></a>Instalar certificados no dispositivo

Instale a sua cadeia de certificados no dispositivo IoT Edge e configuure o tempo de execução IoT Edge para fazer referência aos novos certificados.

Copie os três certificados e ficheiros chave no seu dispositivo IoT Edge. Pode utilizar um serviço como [o Azure Key Vault](../key-vault/index.yml) ou uma função como o protocolo de cópia [Secure](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado. Se tiver gerado os certificados no próprio dispositivo IoT Edge, pode saltar este passo e utilizar o caminho para o diretório de trabalho.

Se estiver a utilizar o IoT Edge para o Linux no Windows, tem de utilizar a tecla SSH localizada no ficheiro Azure IoT Edge `id_rsa` para autenticar transferências de ficheiros entre o HOSPEDEIRO e a máquina virtual Linux. Pode fazer um SCP autenticado utilizando o seguinte comando:

   ```powershell-interactive
   C:\WINDOWS\System32\OpenSSH\scp.exe -i 'C:\Program Files\Azure IoT Edge\id_rsa' <PATH_TO_SOURCE_FILE> iotedge-user@<VM_IP>:<PATH_TO_FILE_DESTINATION>
   ```

   >[!NOTE]
   >O endereço IP da máquina virtual Linux pode ser consultado através do `Get-EflowVmAddr` comando.

Se utilizar os scripts de amostra para [criar certificados de demonstração,](how-to-create-test-certificates.md)copie os seguintes ficheiros no seu dispositivo IoT-Edge:

* Certificado ca do dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chave privada ca do dispositivo: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Raiz CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra o ficheiro IoT Edge security daemon config.

   * Linux e IoT Edge para Linux no Windows: `/etc/iotedge/config.yaml`

   * Janelas utilizando recipientes Windows: `C:\ProgramData\iotedge\config.yaml`

1. Desafie as propriedades do **certificado** em config.yaml para o caminho URI do ficheiro para o certificado e ficheiros chave no dispositivo IoT Edge. Retire o `#` carácter antes das propriedades do certificado para descomprometer as quatro linhas. Certifique-se de que os **certificados:** a linha não tem espaço em branco anterior e que os itens aninhados são recortados por dois espaços. Por exemplo:

   * Linux e IoT Edge para Linux no Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

   * Janelas utilizando recipientes Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

1. Nos dispositivos Linux, certifique-se de que o **iotedge** do utilizador leu permissões para o diretório que detém os certificados.

1. Se já utilizou outros certificados para ioT Edge no dispositivo antes, elimine os ficheiros nos dois diretórios seguintes antes de iniciar ou reiniciar o IoT Edge:

   * Linux e IoT Edge para Linux no Windows: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`

   * Janelas utilizando recipientes Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys`

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Abra o ficheiro IoT Edge security daemon config: `/etc/aziot/config.toml`

1. Encontre o `trust_bundle_cert` parâmetro no início do ficheiro. Descompromete esta linha e forneça o ficheiro URI ao certificado de CA raiz no seu dispositivo.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Encontre a `[edge_ca]` secção no ficheiro config.toml. Descompromete as linhas nesta secção e forneça os caminhos URI de ficheiros para o certificado e ficheiros chave no dispositivo IoT Edge.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Certifique-se de que o utilizador **iotedge** leu permissões para o diretório que detém os certificados.

1. Se já utilizou outros certificados para ioT Edge no dispositivo antes, elimine os ficheiros nos dois diretórios seguintes antes de iniciar ou reiniciar o IoT Edge:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Personalize o tempo de vida útil do certificado

O IoT Edge gera automaticamente certificados no dispositivo em vários casos, incluindo:

* Se não fornecer os seus próprios certificados de produção quando instalar e fornecer IoT Edge, o gestor de segurança IoT Edge gera automaticamente um **certificado de CA do dispositivo**. Este certificado auto-assinado destina-se apenas a cenários de desenvolvimento e teste, não de produção. Este certificado expira após 90 dias.
* O gestor de segurança IoT Edge também gera um **certificado de CA de carga de trabalho** assinado pelo certificado ca do dispositivo

Para obter mais informações sobre a função dos diferentes certificados num dispositivo IoT Edge, consulte [como a Azure IoT Edge utiliza certificados](iot-edge-certs.md).

Para estes dois certificados gerados automaticamente, tem a opção de definir a bandeira **auto_generated_ca_lifetime_days** no ficheiro config para configurar o número de dias para o tempo de vida dos certificados.

>[!NOTE]
>Existe um terceiro certificado autogerado que o gestor de segurança IoT Edge cria, o **certificado de servidor do hub IoT Edge**. Este certificado tem sempre uma vida útil de 90 dias, mas é automaticamente renovado antes de expirar. O **valor auto_generated_ca_lifetime_days** não afeta este certificado.

Após o termo após o número especificado de dias, o IoT Edge tem de ser reiniciado para regenerar o certificado de CA do dispositivo. O certificado de CA do dispositivo não será renovado automaticamente.

1. Para configurar a expiração do certificado para algo que não seja o padrão de 90 dias, adicione o valor em dias à secção de **certificados** do ficheiro config.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Atualmente, uma limitação no libiothsmo impede a utilização de certificados que expirem em ou após 1 de janeiro de 2038.

1. Elimine o conteúdo da `hsm` pasta para remover quaisquer certificados anteriormente gerados.

   * Linux e IoT Edge para Linux no Windows: `/var/lib/iotedge/hsm/certs` e `/var/lib/iotedge/hsm/cert_keys`

   * Janelas utilizando recipientes Windows: `C:\ProgramData\iotedge\hsm\certs` e `C:\ProgramData\iotedge\hsm\cert_keys`

1. Reinicie o serviço IoT Edge.

   * Linux e IoT Edge para Linux no Windows:

   ```bash
   sudo systemctl restart iotedge
   ```

   * Janelas utilizando recipientes Windows:

   ```powershell
   Restart-Service iotedge
   ```

1. Confirme a definição de vida.

   * Linux e IoT Edge para Linux no Windows:

   ```bash
   sudo iotedge check --verbose
   ```

   * Janelas utilizando recipientes Windows:

   ```powershell
   iotedge check --verbose
   ```

   Verifique a saída da prontidão de produção: verificação de **certificados,** que lista o número de dias até que os certificados ca do dispositivo gerado automaticamente expirem.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Passos seguintes

Instalar certificados num dispositivo IoT Edge é um passo necessário antes de implementar a sua solução na produção. Saiba mais sobre como [preparar-se para implementar a sua solução IoT Edge na produção.](production-checklist.md)
