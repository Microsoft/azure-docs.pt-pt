---
title: Criar certificados de teste-Azure IoT Edge | Microsoft Docs
description: Crie certificados de teste e saiba como instalá-los em um dispositivo Azure IoT Edge para preparar a implantação de produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6f64a6714b9d795a1e809c555394be6f7671c63
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510673"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Criar certificados de demonstração para testar IoT Edge recursos do dispositivo

IoT Edge dispositivos exigem certificados para comunicação segura entre o tempo de execução, os módulos e os dispositivos downstream.
Se você não tiver uma autoridade de certificação para criar os certificados necessários, poderá usar os certificados de demonstração para experimentar IoT Edge recursos em seu ambiente de teste.
Este artigo descreve a funcionalidade dos scripts de geração de certificado que IoT Edge fornece para teste.

Esses certificados expiram em 30 dias e não devem ser usados em nenhum cenário de produção.

Você pode criar certificados em qualquer computador e, em seguida, copiá-los para o dispositivo IoT Edge.
É mais fácil usar o computador primário para criar os certificados em vez de gerá-los no próprio dispositivo IoT Edge.
Usando seu computador primário, você pode configurar os scripts uma vez e, em seguida, repetir o processo para criar certificados para vários dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Um computador de desenvolvimento com o Git instalado.

## <a name="set-up-scripts"></a>Configurar scripts

O repositório IoT Edge no GitHub inclui scripts de geração de certificado que você pode usar para criar certificados de demonstração.
Esta seção fornece instruções para preparar os scripts para execução no computador, seja no Windows ou no Linux.
Se você estiver em um computador Linux, pule para [configurar no Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Configurar no Windows

Para criar certificados de demonstração em um dispositivo Windows, você precisa instalar o OpenSSL e, em seguida, clonar os scripts de geração e configurá-los para serem executados localmente no PowerShell.

#### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL para o Windows na máquina que está a utilizar para gerar os certificados.
Se você já tiver o OpenSSL instalado em seu dispositivo Windows, poderá ignorar esta etapa, mas verifique se o OpenSSL. exe está disponível em sua variável de ambiente PATH.

Há várias maneiras de instalar o OpenSSL, incluindo as seguintes opções:

* **Mais fácil:** Baixe e instale quaisquer [binários do OpenSSL](https://wiki.openssl.org/index.php/Binaries)de terceiros, por exemplo, do [OpenSSL no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe a variável de ambiente PATH.

* **Recomendado:** transfira o código-fonte OpenSSL e crie os binários no seu computador, por si próprio ou por meio de [vcpkg](https://github.com/Microsoft/vcpkg). As instruções indicadas abaixo utilizam vcpkg para transferir o código-fonte, compilação e instalar o OpenSSL no seu computador Windows com passos simples.

   1. Navegue para um diretório onde pretende instalar vcpkg. Siga as instruções para transferir e instalar [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Quando o vcpkg estiver instalado, execute o seguinte comando em um prompt do PowerShell para instalar o pacote OpenSSL para Windows x64. A instalação normalmente leva cerca de 5 minutos para ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Adicionar `<vcpkg path>\installed\x64-windows\tools\openssl` a variável de ambiente PATH para que o arquivo de openssl.exe está disponível para invocação.

#### <a name="prepare-scripts-in-powershell"></a>Preparar scripts no PowerShell

O repositório Git do Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste.
Nesta seção, você clonará o repositório de IoT Edge e executará os scripts.

1. Abra uma janela do PowerShell no modo de administrador.

2. Clone o repositório git IoT Edge, que contém scripts para gerar certificados de demonstração. Utilize o `git clone` comando ou [download ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue para o diretório no qual pretende trabalhar. Ao longo deste artigo, vamos chamar esse diretório *\<WRKDIR >* . Todos os certificados e chaves serão criados nesse diretório de trabalho.

4. Copie a configuração e os arquivos de script do repositório clonado para seu diretório de trabalho.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se você baixou o repositório como um ZIP, o nome da pasta será `iotedge-master` e o restante do caminho será o mesmo.

5. Ative o PowerShell para executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Traga as funções usadas pelos scripts no namespace global do PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   A janela do PowerShell exibirá um aviso de que os certificados gerados por esse script são apenas para fins de teste e não devem ser usados em cenários de produção.

7. Verifique se o OpenSSL foi instalado corretamente e certifique-se de que não haverá colisões de nome com os certificados existentes. Se houver problemas, a saída do script deverá descrever como corrigi-los em seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Configurar no Linux

Para criar certificados de demonstração em um dispositivo Windows, você precisa clonar os scripts de geração e configurá-los para serem executados localmente no bash.

1. Clone o repositório git IoT Edge, que contém scripts para gerar certificados de demonstração.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue para o diretório no qual pretende trabalhar. Vamos nos referir a esse diretório em todo o artigo como *\<WRKDIR >* . Todos os arquivos de certificado e de chave serão criados nesse diretório.
  
3. Copie os arquivos de configuração e script do repositório de IoT Edge clonado para seu diretório de trabalho.

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

## <a name="create-root-ca-certificate"></a>Criar certificado de AC raiz

O certificado de autoridade de certificação raiz é usado para fazer todos os outros certificados de demonstração para testar um cenário de IoT Edge.
Você pode continuar usando o mesmo certificado de autoridade de certificação raiz para criar certificados de demonstração para vários dispositivos IoT Edge ou downstream.

Se você já tiver um certificado de autoridade de certificação raiz em sua pasta de trabalho, não crie um novo.
O novo certificado de autoridade de certificação raiz substituirá o antigo e todos os certificados downstream criados a partir do antigo deixarão de funcionar.
Se você quiser vários certificados de AC raiz, certifique-se de gerenciá-los em pastas separadas.

Antes de prosseguir com as etapas nesta seção, siga as etapas na seção [Configurar scripts](#set-up-scripts) para preparar um diretório de trabalho com os scripts de geração de certificado de demonstração.

### <a name="windows"></a>Windows

1. Navegue até o diretório de trabalho em que você colocou os scripts de geração de certificado.

1. Crie o certificado de autoridade de certificação raiz e faça com que ele assine um certificado intermediário. Os certificados são colocados em seu diretório de trabalho.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Esse comando de script cria vários arquivos de certificado e de chave, mas quando os artigos solicitam o **certificado de autoridade de certificação raiz**, use o seguinte arquivo:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navegue até o diretório de trabalho em que você colocou os scripts de geração de certificado.

1. Crie o certificado de autoridade de certificação raiz e um certificado intermediário.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Esse comando de script cria vários arquivos de certificado e de chave, mas quando os artigos solicitam o **certificado de autoridade de certificação raiz**, use o seguinte arquivo:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Criar IoT Edge certificados de AC do dispositivo

Cada dispositivo de IoT Edge que vai para a produção precisa de um certificado de autoridade de certificação de dispositivo referenciado no arquivo config. YAML. O certificado de autoridade de certificação do dispositivo é responsável por criar certificados para módulos em execução no dispositivo. Também é assim que o dispositivo IoT Edge verifica sua identidade ao se conectar a dispositivos downstream.

Antes de prosseguir com as etapas nesta seção, siga as etapas nas seções [Configurar scripts](#set-up-scripts) e [criar certificado de autoridade de certificação raiz](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

1. Navegue até o diretório de trabalho que tem os scripts de geração de certificado e o certificado de autoridade de certificação raiz.

2. Crie o certificado de autoridade de certificação do dispositivo IoT Edge e a chave privada com o comando a seguir. Forneça um nome para o certificado de autoridade de certificação, por exemplo, **MyEdgeDeviceCA**, que é usado para nomear os arquivos de saída.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Esse comando de script cria vários arquivos de certificado e chave. O seguinte certificado e par de chaves precisam ser copiados para um dispositivo IoT Edge e referenciados no arquivo config. YAML:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

O nome do dispositivo de gateway passado para esses scripts não deve ser o mesmo que o parâmetro "hostname" em config. YAML. Os scripts ajudam a evitar problemas acrescentando uma cadeia de caracteres ". ca" ao nome do dispositivo de gateway para evitar a colisão de nomes, caso um usuário configure IoT Edge usando o mesmo nome em ambos os locais. No entanto, é uma boa prática evitar o uso do mesmo nome.

### <a name="linux"></a>Linux

1. Navegue até o diretório de trabalho que tem os scripts de geração de certificado e o certificado de autoridade de certificação raiz.

2. Crie o certificado de autoridade de certificação do dispositivo IoT Edge e a chave privada com o comando a seguir. Forneça um nome para o certificado de autoridade de certificação, por exemplo, **MyEdgeDeviceCA**, que é usado para nomear os arquivos de saída.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Esse comando de script cria vários arquivos de certificado e chave. O seguinte certificado e par de chaves precisam ser copiados para um dispositivo IoT Edge e referenciados no arquivo config. YAML:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

O nome do dispositivo de gateway passado para esses scripts não deve ser o mesmo que o parâmetro "hostname" em config. YAML. Os scripts ajudam a evitar problemas acrescentando uma cadeia de caracteres ". ca" ao nome do dispositivo de gateway para evitar a colisão de nomes, caso um usuário configure IoT Edge usando o mesmo nome em ambos os locais. No entanto, é uma boa prática evitar o uso do mesmo nome.

## <a name="create-x509-certs-for-downstream-devices"></a>Criar certificados X. 509 para dispositivos downstream

Se você estiver configurando um dispositivo IoT downstream para um cenário de gateway, poderá gerar certificados de demonstração para a autenticação X. 509.
Há duas maneiras de autenticar um dispositivo IoT usando certificados X. 509: usando CERT. autoassinados ou usando certificados assinados por AC (autoridade de certificação).
Para a autenticação autoassinada X. 509, às vezes conhecida como autenticação de impressão digital, você precisa criar novos certificados para serem colocados em seu dispositivo IoT.
Esses certificados têm uma impressão digital neles que você compartilha com o Hub IoT para autenticação.
Para autenticação assinada da AC (autoridade de certificação) X. 509, você precisa de um certificado de autoridade de certificação raiz registrado no Hub IoT que você usa para assinar certificados para seu dispositivo IoT.
Qualquer dispositivo que use um certificado emitido pelo certificado de autoridade de certificação raiz ou qualquer um de seus certificados intermediários terá permissão para autenticar.

Os scripts de geração de certificado podem ajudá-lo a fazer certificados de demonstração para testar qualquer um desses cenários de autenticação.

Antes de prosseguir com as etapas nesta seção, siga as etapas nas seções [Configurar scripts](#set-up-scripts) e [criar certificado de autoridade de certificação raiz](#create-root-ca-certificate) .

### <a name="self-signed-certificates"></a>Certificados autoassinados

Ao autenticar um dispositivo IoT com certificados autoassinados, você precisa criar certificados de dispositivo com base no certificado de autoridade de certificação raiz para sua solução.
Em seguida, você recupera uma "impressão digital" hexadecimal dos certificados para fornecer ao Hub IoT.
O dispositivo IoT também precisa de uma cópia de seus certificados de dispositivo para que ele possa ser autenticado com o Hub IoT.

#### <a name="windows"></a>Windows

1. Navegue até o diretório de trabalho que tem os scripts de geração de certificado e o certificado de autoridade de certificação raiz.

2. Crie dois certificados (primário e secundário) para o dispositivo downstream. Uma Convenção de nomenclatura fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, o rótulo primário ou secundário. Por exemplo:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Esse comando de script cria vários arquivos de certificado e chave. Os seguintes certificados e pares de chaves precisam ser copiados para o dispositivo IoT downstream e referenciados nos aplicativos que se conectam ao Hub IoT:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada de impressão digital nos contextos do Hub IoT) de cada certificado. A impressão digital é uma cadeia de caracteres hexadecimal 40. Use o comando openssl a seguir para exibir o certificado e encontrar a impressão digital:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Execute este comando duas vezes, uma vez para o certificado primário e uma vez para o certificado secundário. Você fornece impressões digitais para ambos os certificados ao registrar um novo dispositivo IoT usando certificados X. 509 autoassinados.

#### <a name="linux"></a>Linux

1. Navegue até o diretório de trabalho que tem os scripts de geração de certificado e o certificado de autoridade de certificação raiz.

2. Crie dois certificados (primário e secundário) para o dispositivo downstream. Uma Convenção de nomenclatura fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, o rótulo primário ou secundário. Por exemplo:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Esse comando de script cria vários arquivos de certificado e chave. Os seguintes certificados e pares de chaves precisam ser copiados para o dispositivo IoT downstream e referenciados nos aplicativos que se conectam ao Hub IoT:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada de impressão digital nos contextos do Hub IoT) de cada certificado. A impressão digital é uma cadeia de caracteres hexadecimal 40. Use o comando openssl a seguir para exibir o certificado e encontrar a impressão digital:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Você fornece a impressão digital primária e secundária ao registrar um novo dispositivo IoT usando certificados X. 509 autoassinados.

### <a name="ca-signed-certificates"></a>Certificados assinados por AC

Ao autenticar um dispositivo IoT com certificados autoassinados, você precisa carregar o certificado de autoridade de certificação raiz para sua solução para o Hub IoT.
Em seguida, você executa uma verificação para provar ao Hub IoT que você possui o certificado de autoridade de certificação raiz.
Por fim, use o mesmo certificado de autoridade de certificação raiz para criar certificados de dispositivo a serem colocados em seu dispositivo IoT para que ele possa ser autenticado com o Hub IoT.

Os certificados nesta seção são para as etapas em [Configurar a segurança X. 509 no Hub IOT do Azure](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Carregue o arquivo de certificado de autoridade de certificação raiz do seu diretório de trabalho, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`, para o Hub IoT.

2. Use o código fornecido no portal do Azure para verificar se você possui esse certificado de autoridade de certificação raiz.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Crie uma cadeia de certificados para seu dispositivo downstream. Use a mesma ID de dispositivo com a qual o dispositivo está registrado no Hub IoT.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Esse comando de script cria vários arquivos de certificado e chave. Os seguintes certificados e pares de chaves precisam ser copiados para o dispositivo IoT downstream e referenciados nos aplicativos que se conectam ao Hub IoT:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Carregue o arquivo de certificado de autoridade de certificação raiz do seu diretório de trabalho, `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`, para o Hub IoT.

2. Use o código fornecido no portal do Azure para verificar se você possui esse certificado de autoridade de certificação raiz.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Crie uma cadeia de certificados para seu dispositivo downstream. Use a mesma ID de dispositivo com a qual o dispositivo está registrado no Hub IoT.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Esse comando de script cria vários arquivos de certificado e chave. Os seguintes certificados e pares de chaves precisam ser copiados para o dispositivo IoT downstream e referenciados nos aplicativos que se conectam ao Hub IoT:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
