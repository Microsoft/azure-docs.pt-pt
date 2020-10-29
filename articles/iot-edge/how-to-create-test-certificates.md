---
title: Criar certificados de teste - Azure IoT Edge / Microsoft Docs
description: Crie certificados de teste e aprenda a instalá-los num dispositivo Azure IoT Edge para preparar a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c5af77da0ed2c579a478c8ebaaa924882d9a15c6
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927707"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Criar certificados de demonstração para testar as funcionalidades dos dispositivos IoT Edge

Os dispositivos IoT Edge requerem certificados para uma comunicação segura entre o tempo de funcionaamento, os módulos e quaisquer dispositivos a jusante.
Se não tiver uma autoridade de certificados para criar os certificados necessários, pode utilizar certificados de demonstração para experimentar as funcionalidades IoT Edge no seu ambiente de teste.
Este artigo descreve a funcionalidade dos scripts de geração de certificados que o IoT Edge fornece para testes.

Estes certificados expiram em 30 dias e não devem ser utilizados em qualquer cenário de produção.

Pode criar certificados em qualquer máquina e copiá-los para o seu dispositivo IoT Edge.
É mais fácil usar a sua máquina primária para criar os certificados em vez de os gerar no seu próprio dispositivo IoT Edge.
Ao utilizar a sua máquina primária, pode configurar os scripts uma vez e depois usá-los para criar certificados para vários dispositivos.

Siga estes passos para criar certificados de demonstração para testar o seu cenário IoT Edge:

1. [Configurar scripts](#set-up-scripts) para geração de certificados no seu dispositivo.
2. [Crie o certificado de CA raiz](#create-root-ca-certificate) que utiliza para assinar todos os outros certificados para o seu cenário.
3. Gere os certificados de que precisa para o cenário que pretende testar:
   * [Crie certificados de identidade de dispositivo IoT Edge](#create-iot-edge-device-identity-certificates) para fornecimento automático com o Serviço de Provisionamento de Dispositivos IoT Hub.
   * [Crie certificados CA de dispositivo IoT Edge](#create-iot-edge-device-ca-certificates) para dispositivos IoT Edge em cenários de gateway.
   * [Crie certificados de dispositivo a jusante](#create-downstream-device-certificates) para autenticar dispositivos a jusante num cenário de gateway.

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento com Git instalada.

## <a name="set-up-scripts"></a>Configurar scripts

O repositório IoT Edge no GitHub inclui scripts de geração de certificados que podes usar para criar certificados de demonstração.
Esta secção fornece instruções para preparar os scripts para executar no seu computador, seja no Windows ou linux.
Se estiver numa máquina Linux, avance para [configurar o Linux.](#set-up-on-linux)

### <a name="set-up-on-windows"></a>Configurar no Windows

Para criar certificados de demonstração num dispositivo Windows, é necessário instalar o OpenSSL e, em seguida, clonar os scripts de geração e confiá-los para serem executados localmente no PowerShell.

#### <a name="install-openssl"></a>Instalar OpenSSL

Instale o OpenSSL para windows na máquina que está a utilizar para gerar os certificados.
Se já tiver o OpenSSL instalado no seu dispositivo Windows, certifique-se de que openssl.exe está disponível na variável ambiente PATH.

Existem várias formas de instalar o OpenSSL, incluindo as seguintes opções:

* **Mais fácil:** Faça o download e instale quaisquer [binários OpenSSL de terceiros,](https://wiki.openssl.org/index.php/Binaries)por exemplo, a partir do [OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe à variável ambiente PATH.

* **Recomendado:** Faça o download do código fonte OpenSSL e construa os binários na sua máquina por si ou através de [vcpkg](https://github.com/Microsoft/vcpkg). As instruções listadas abaixo utilizam vcpkg para descarregar código fonte, compilar e instalar o OpenSSL na sua máquina Windows com passos fáceis.

   1. Navegue para um diretório onde pretende instalar vcpkg. Siga as instruções para descarregar e instalar [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Uma vez instalado o vcpkg, executar o seguinte comando a partir de um pedido PowerShell para instalar o pacote OpenSSL para o Windows x64. A instalação normalmente demora cerca de 5 minutos a ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Adicione `<vcpkg path>\installed\x64-windows\tools\openssl` à variável ambiente PATH para que o ficheiro openssl.exe esteja disponível para invocação.

#### <a name="prepare-scripts-in-powershell"></a>Preparar scripts em PowerShell

O repositório Azure IoT Edge git contém scripts que pode usar para gerar certificados de teste.
Nesta secção, clona-se o repo IoT Edge e executa os scripts.

1. Abra uma janela PowerShell no modo de administrador.

2. Clone o repo ioT Edge git, que contém scripts para gerar certificados de demonstração. Utilize o `git clone` comando ou [descarregue o ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue para o diretório em que quer trabalhar. Ao longo deste artigo, chamaremos este *\<WRKDIR>* diretório. Todos os certificados e chaves serão criados neste diretório de trabalho.

4. Copie os ficheiros de configuração e script do repo clonado para o seu diretório de trabalho.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se descarregou o repo como zip, então o nome da pasta é `iotedge-master` e o resto do caminho é o mesmo.

5. Ativar o PowerShell para executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Traga as funções usadas pelos scripts para o espaço de nome global da PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   A janela PowerShell apresentará um aviso de que os certificados gerados por este script são apenas para fins de teste, e não devem ser utilizados em cenários de produção.

7. Verifique se o OpenSSL foi instalado corretamente e certifique-se de que não haverá colisões de nomes com certificados existentes. Se houver problemas, a saída do script deve descrever como corrigi-los no seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Instale-se no Linux

Para criar certificados de demonstração num dispositivo Windows, precisa de clonar os scripts de geração e confiá-los para serem executados localmente em bash.

1. Clone o repo ioT Edge git, que contém scripts para gerar certificados de demonstração.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue para o diretório em que quer trabalhar. Vamos referir-nos a este diretório ao longo do artigo como *\<WRKDIR>* . Todos os certificados e ficheiros-chave serão criados neste diretório.
  
3. Copie os ficheiros config e scripts do repo IoT Edge clonado para o seu diretório de trabalho.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Criar certificado de CA raiz

O certificado de CA raiz é utilizado para fazer todos os outros certificados de demonstração para testar um cenário IoT Edge.
Pode continuar a utilizar o mesmo certificado de CA raiz para fazer certificados de demonstração para vários dispositivos IoT Edge ou a jusante.

Se já tiver um certificado de CA de raiz na sua pasta de trabalho, não crie um novo.
O novo certificado de CA de raiz substituirá o antigo, e quaisquer certificados a jusante feitos a partir do antigo deixarão de funcionar.
Se quiser vários certificados de CA de raiz, certifique-se de que os gere em pastas separadas.

Antes de prosseguir com os passos nesta secção, siga os passos na secção [de scripts configurar](#set-up-scripts) para preparar um diretório de trabalho com os scripts de geração de certificados de demonstração.

### <a name="windows"></a>Windows

1. Navegue para o diretório de trabalho onde colocou os scripts de geração de certificados.

1. Crie o certificado de CA raiz e o tenha que assinar um certificado intermédio. Os certificados são todos colocados no seu diretório de trabalho.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Este comando de script cria vários certificados e ficheiros chave, mas quando os artigos pedem o **certificado de CA raiz,** use o seguinte ficheiro:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navegue para o diretório de trabalho onde colocou os scripts de geração de certificados.

1. Crie o certificado de CA raiz e um certificado intermédio.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Este comando de script cria vários certificados e ficheiros chave, mas quando os artigos pedem o **certificado de CA raiz,** use o seguinte ficheiro:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Criar certificados de identidade de dispositivo IoT Edge

Os certificados de identidade do dispositivo são utilizados para o fornecimento de dispositivos IoT Edge através do Serviço de Provisionamento de Dispositivos IoT Hub (DPS).

Os certificados de identidade do dispositivo vão na secção de **Provisionamento** do ficheiro config.yaml no dispositivo IoT Edge.

Antes de prosseguir com os passos nesta secção, siga os passos nas [definições](#set-up-scripts) de scripts e crie secções [de certificados de CA de raiz.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

Crie o certificado de identidade do dispositivo IoT Edge e a chave privada com o seguinte comando:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

O nome que passar para este comando será o ID do dispositivo IoT Edge no IoT Hub.

O novo comando de identidade do dispositivo cria vários certificados e ficheiros-chave, incluindo três que utilizará ao criar uma inscrição individual em DPS e instalar o tempo de execução IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Crie o certificado de identidade do dispositivo IoT Edge e a chave privada com o seguinte comando:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

O nome que passar para este comando será o ID do dispositivo IoT Edge no IoT Hub.

O script cria vários certificados e ficheiros chave, incluindo três que você usará ao criar uma inscrição individual em DPS e instalar o tempo de execução IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>Criar certificados CA de dispositivo IoT Edge

Todos os dispositivos IoT Edge que vão para a produção precisam de um certificado de CA do dispositivo que é referenciado a partir do ficheiro config.yaml.
O certificado CA do dispositivo é responsável pela criação de certificados para módulos em execução no dispositivo.
Também é necessário para cenários de gateway, porque o certificado de CA do dispositivo é como o dispositivo IoT Edge verifica a sua identidade para dispositivos a jusante.

Os certificados de CA do dispositivo vão na secção **certificado** do ficheiro config.yaml no dispositivo IoT Edge.

Antes de prosseguir com os passos nesta secção, siga os passos nas [definições](#set-up-scripts) de scripts e crie secções [de certificados de CA de raiz.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificado e certificado de CA raiz.

2. Crie o certificado CA do dispositivo IoT Edge e a chave privada com o seguinte comando. Forneça um nome para o certificado de AC.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   Este comando cria vários certificados e ficheiros chave. O seguinte certificado e par de chaves precisa de ser copiado para um dispositivo IoT Edge e referenciado no ficheiro config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

O nome passado para o comando **New-CACertsEdgeDevice** não deve ser o mesmo que o parâmetro do nome hospedeiro em config.yaml, ou o ID do dispositivo no IoT Hub.

### <a name="linux"></a>Linux

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificado e certificado de CA raiz.

2. Crie o certificado CA do dispositivo IoT Edge e a chave privada com o seguinte comando. Forneça um nome para o certificado de AC.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   Este comando de script cria vários certificados e ficheiros chave. O seguinte certificado e par de chaves precisa de ser copiado para um dispositivo IoT Edge e referenciado no ficheiro config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

O nome passado para o comando **create_edge_device_ca_certificate** não deve ser o mesmo que o parâmetro do nome hospedeiro em config.yaml, ou o ID do dispositivo no IoT Hub.

## <a name="create-downstream-device-certificates"></a>Criar certificados de dispositivo a jusante

Se estiver a configurar um dispositivo IoT a jusante para um cenário de gateway e quiser utilizar a autenticação X.509, pode gerar certificados de demonstração para o dispositivo a jusante.
Se pretender utilizar a autenticação de chaves simétricas, não precisa de criar certificados adicionais para o dispositivo a jusante.
Existem duas formas de autenticar um dispositivo IoT utilizando certificados X.509: utilizar certificados auto-assinados ou utilizar certificados assinados pela Autoridade de Certificados (CA).
Para a autenticação auto-assinada X.509, por vezes referida como autenticação por impressão digital, é necessário criar novos certificados para colocar no seu dispositivo IoT.
Estes certificados têm uma impressão digital que partilha com o IoT Hub para autenticação.
Para a autenticação assinada pela Autoridade de Certificados X.509 (CA), necessita de um certificado de CA raiz registado no IoT Hub que utilize para assinar certificados para o seu dispositivo IoT.
Qualquer dispositivo que utilize um certificado emitido pelo certificado de CA raiz ou qualquer um dos seus certificados intermédios será autorizado a autenticar.

Os scripts de geração de certificados podem ajudá-lo a fazer certificados de demonstração para testar qualquer um destes cenários de autenticação.

Antes de prosseguir com os passos nesta secção, siga os passos nas [definições](#set-up-scripts) de scripts e crie secções [de certificados de CA de raiz.](#create-root-ca-certificate)

### <a name="self-signed-certificates"></a>Certificados auto-assinados

Quando autenticar um dispositivo IoT com certificados auto-assinados, tem de criar certificados de dispositivo com base no certificado de CA raiz para a sua solução.
Em seguida, você recupera uma "impressão digital" hexadómica dos certificados para fornecer ao IoT Hub.
O seu dispositivo IoT também necessita de uma cópia dos certificados do dispositivo para que possa autenticar com o IoT Hub.

#### <a name="windows"></a>Windows

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificado e certificado de CA raiz.

2. Criar dois certificados (primários e secundários) para o dispositivo a jusante. Uma convenção de nomeação fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, a etiqueta primária ou secundária. Por exemplo:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Este comando de script cria vários certificados e ficheiros chave. O seguinte certificado e pares-chave devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que se ligam ao IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada impressão digital em contextos IoT Hub) a partir de cada certificado. A impressão digital é uma corda de caracteres hexadécimais de 40. Utilize o seguinte comando de abertura para visualizar o certificado e encontrar a impressão digital:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Executar este comando duas vezes, uma para o certificado primário e uma para o certificado secundário. Fornece impressões digitais para ambos os certificados quando regista um novo dispositivo IoT utilizando certificados X.509 auto-assinados.

#### <a name="linux"></a>Linux

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificado e certificado de CA raiz.

2. Criar dois certificados (primários e secundários) para o dispositivo a jusante. Uma convenção de nomeação fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, a etiqueta primária ou secundária. Por exemplo:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Este comando de script cria vários certificados e ficheiros chave. O seguinte certificado e pares-chave devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que se ligam ao IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada impressão digital em contextos IoT Hub) a partir de cada certificado. A impressão digital é uma corda de caracteres hexadécimais de 40. Utilize o seguinte comando de abertura para visualizar o certificado e encontrar a impressão digital:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Fornece a impressão digital primária e secundária quando regista um novo dispositivo IoT utilizando certificados X.509 auto-assinados.

### <a name="ca-signed-certificates"></a>Certificados assinados pela CA

Quando autenticar um dispositivo IoT com certificados auto-assinados, tem de carregar o certificado de CA raiz para a sua solução para o IoT Hub.
Em seguida, efetue uma verificação para provar ao IoT Hub que é dono do certificado de CA raiz.
Por fim, utiliza o mesmo certificado de CA raiz para criar certificados de dispositivo para colocar no seu dispositivo IoT para que possa autenticar com o IoT Hub.

Os certificados desta secção são para os passos da [configuração da segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Faça o upload do ficheiro de certificado de CA raiz do seu diretório de `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` trabalho, para o seu hub IoT.

2. Utilize o código fornecido no portal Azure para verificar se possui o certificado de CA raiz.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Crie uma cadeia de certificados para o seu dispositivo a jusante. Utilize o mesmo ID do dispositivo com o qual o dispositivo está registado no IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Este comando de script cria vários certificados e ficheiros chave. O seguinte certificado e pares-chave devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que se ligam ao IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Faça o upload do ficheiro de certificado de CA raiz do seu diretório de `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` trabalho, para o seu hub IoT.

2. Utilize o código fornecido no portal Azure para verificar se possui o certificado de CA raiz.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Crie uma cadeia de certificados para o seu dispositivo a jusante. Utilize o mesmo ID do dispositivo com o qual o dispositivo está registado no IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Este comando de script cria vários certificados e ficheiros chave. O seguinte certificado e pares-chave devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que se ligam ao IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
