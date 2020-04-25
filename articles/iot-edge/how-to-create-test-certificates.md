---
title: Criar certificados de teste - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Crie certificados de teste e aprenda a instalá-los num dispositivo Azure IoT Edge para se preparar para a implantação da produção.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9540913cd86b74fd51e96aa9d1d1dd34c5d60631
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82129800"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Criar certificados de demonstração para testar funcionalidades do dispositivo IoT Edge

Os dispositivos IoT Edge requerem certificados para uma comunicação segura entre o tempo de execução, os módulos e quaisquer dispositivos a jusante.
Se não tiver autoridade de certificado para criar os certificados necessários, pode utilizar certificados de demonstração para experimentar as funcionalidades IoT Edge no seu ambiente de teste.
Este artigo descreve a funcionalidade dos scripts de geração de certificados que o IoT Edge fornece para testes.

Estes certificados expiram em 30 dias e não devem ser utilizados em qualquer cenário de produção.

Pode criar certificados em qualquer máquina e, em seguida, copiá-los para o seu dispositivo IoT Edge.
É mais fácil usar a sua máquina primária para criar os certificados em vez de os gerar no próprio dispositivo IoT Edge.
Ao utilizar a sua máquina principal, pode configurar os scripts uma vez e, em seguida, repetir o processo para criar certificados para vários dispositivos.

Siga estes passos para criar certificados de demonstração para testar o seu cenário IoT Edge:

1. [Crie scripts](#set-up-scripts) para a geração de certificados no seu dispositivo.
2. [Crie o certificado ca raiz](#create-root-ca-certificate) que usa para assinar todos os outros certificados para o seu cenário.
3. Gere os certificados de que necessita para o cenário que pretende testar:
   * [Crie certificados](#create-iot-edge-device-identity-certificates) de identidade do dispositivo IoT Edge para testar o fornecimento automático com o Serviço de Provisionamento de Dispositivos IoT Hub.
   * [Crie certificados CA do dispositivo IoT Edge](#create-iot-edge-device-ca-certificates) para testar cenários de produção ou cenários de gateway.
   * [Crie certificados](#create-downstream-device-certificates) de dispositivos a jusante para testar a autenticação de dispositivos a jusante para o IoT Hub num cenário de gateway.

## <a name="prerequisites"></a>Pré-requisitos

Uma máquina de desenvolvimento com Git instalado.

## <a name="set-up-scripts"></a>Configurar scripts

O repositório IoT Edge no GitHub inclui scripts de geração de certificados que pode usar para criar certificados de demonstração.
Esta secção fornece instruções para preparar as scripts para ser executada no seu computador, quer no Windows quer no Linux.
Se estiver numa máquina linux, salte para a frente para [configurar o Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Configurar no Windows

Para criar certificados de demonstração num dispositivo Windows, é necessário instalar o OpenSSL e, em seguida, clonar os scripts de geração e configurá-los para funcionar localmente no PowerShell.

#### <a name="install-openssl"></a>Instalar openssl

Instale o OpenSSL para Windows na máquina que está a utilizar para gerar os certificados.
Se já tiver o OpenSSL instalado no seu dispositivo Windows, poderá saltar este passo, mas certifique-se de que o openssl.exe está disponível na variável ambiente PATH.

Existem várias formas de instalar o OpenSSL, incluindo as seguintes opções:

* **Mais fácil:** Descarregue e instale quaisquer [binários OpenSSL de terceiros,](https://wiki.openssl.org/index.php/Binaries)por exemplo, a partir de [OpenSSL no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para abrir.exe à variável ambiente PATH.

* **Recomendado:** Descarregue o código fonte OpenSSL e construa os binários na sua máquina por si mesmo ou via [vcpkg](https://github.com/Microsoft/vcpkg). As instruções listadas abaixo utilizam o vcpkg para descarregar código fonte, compilar e instalar o OpenSSL na sua máquina Windows com passos fáceis.

   1. Navegue para um diretório onde pretenda instalar vcpkg. Siga as instruções para descarregar e instalar [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Uma vez instalado o vcpkg, execute o seguinte comando a partir de uma solicitação PowerShell para instalar o pacote OpenSSL para Windows x64. A instalação normalmente demora cerca de 5 minutos a ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Adicione `<vcpkg path>\installed\x64-windows\tools\openssl` à sua variável ambiente PATH de modo que o ficheiro openssl.exe esteja disponível para invocação.

#### <a name="prepare-scripts-in-powershell"></a>Prepare scripts no PowerShell

O repositório de git Azure IoT Edge contém scripts que pode utilizar para gerar certificados de teste.
Nesta secção, clona o repo IoT Edge e executa os scripts.

1. Abra uma janela PowerShell no modo administrador.

2. Clone o IoT Edge git repo, que contém scripts para gerar certificados de demonstração. Utilize `git clone` o comando ou [descarregue o ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue para o diretório em que quer trabalhar. Ao longo deste artigo, chamaremos a este diretório * \<WRKDIR>*. Todos os certificados e chaves serão criados neste diretório de trabalho.

4. Copie a configuração e os ficheiros de script do repo clonado para o seu diretório de trabalho.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se descarregou o repo como ZIP, então o nome da pasta é `iotedge-master` e o resto do caminho é o mesmo.

5. Ative a PowerShell a executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Leve as funções usadas pelos scripts para o espaço de nome global da PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   A janela PowerShell apresentará um aviso de que os certificados gerados por este script são apenas para fins de teste, e não devem ser utilizados em cenários de produção.

7. Verifique se o OpenSSL foi instalado corretamente e certifique-se de que não haverá colisões de nomecom certificados existentes. Se houver problemas, a saída do script deve descrever como corrigi-los no seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Configurar em Linux

Para criar certificados de demonstração num dispositivo Windows, precisa de clonar os scripts de geração e configurá-los para funcionar localmente em bash.

1. Clone o IoT Edge git repo, que contém scripts para gerar certificados de demonstração.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue para o diretório em que quer trabalhar. Vamos referir-nos a este diretório ao longo do artigo como * \<WRKDIR>*. Todos os certificados e ficheiros chave serão criados neste diretório.
  
3. Copie os ficheiros de config e script do repo clonado IoT Edge para o seu diretório de trabalho.

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

## <a name="create-root-ca-certificate"></a>Criar certificado ca raiz

O certificado ca raiz é usado para fazer todos os outros certificados de demonstração para testar um cenário IoT Edge.
Pode continuar a utilizar o mesmo certificado CA raiz para fazer certificados de demonstração para vários dispositivos IoT Edge ou a jusante.

Se já tiver um certificado CA raiz na sua pasta de trabalho, não crie um novo.
O novo certificado de ca raiz irá sobrepor o antigo, e quaisquer certificados a jusante feitos do antigo deixarão de funcionar.
Se pretender vários certificados ca raiz, certifique-se de que os gere em pastas separadas.

Antes de prosseguir com os passos nesta secção, siga os passos na secção [de scripts configurar](#set-up-scripts) para preparar um diretório de trabalho com os scripts de geração de certificados de demonstração.

### <a name="windows"></a>Windows

1. Navegue para o diretório de trabalho onde colocou os scripts de geração de certificados.

1. Crie o certificado de raiz ca e o mande assinar um certificado intermédio. Os certificados estão todos colocados no seu diretório de trabalho.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Este comando de script cria vários ficheiros de certificados e chave, mas quando os artigos pedirem o **certificado CA raiz,** utilize o seguinte ficheiro:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navegue para o diretório de trabalho onde colocou os scripts de geração de certificados.

1. Crie o certificado de raiz CA e um certificado intermédio.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Este comando de script cria vários ficheiros de certificados e chave, mas quando os artigos pedirem o **certificado CA raiz,** utilize o seguinte ficheiro:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Crie certificados CA do dispositivo IoT Edge

Todos os dispositivos IoT Edge que vão para a produção precisam de um certificado CA do dispositivo que seja referenciado a partir do ficheiro config.yaml.
O certificado CA do dispositivo é responsável pela criação de certificados para módulos em execução no dispositivo.
É também como o dispositivo IoT Edge verifica a sua identidade ao ligar-se a dispositivos a jusante.

Os certificados CA do dispositivo entram na secção **de certificado** do ficheiro config.yaml no dispositivo IoT Edge.

Antes de prosseguir com os passos nesta secção, siga os passos nas [scripts de configuração](#set-up-scripts) e crie as secções de [certificado saca raiz.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificados e certificado ca raiz.

2. Crie o certificado CA do dispositivo IoT Edge e a chave privada com o seguinte comando. Forneça um nome para o certificado CA, por **exemplo, MyEdgeDeviceCA,** que é usado para nomear os ficheiros de saída.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Este comando de script cria vários ficheiros de certificadoe chave. O seguinte certificado e par de chaves deve ser copiado para um dispositivo IoT Edge e referenciado no ficheiro config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

O nome do dispositivo gateway passado para esses scripts não deve ser o mesmo que o parâmetro "nome de anfitrião" em config.yaml, ou o ID do dispositivo no IoT Hub.
Os scripts ajudam-no a evitar quaisquer problemas, afixando uma corda ".ca" ao nome do dispositivo gateway para evitar a colisão do nome no caso de um utilizador configurar o IoT Edge usando o mesmo nome em ambos os locais.
No entanto, é uma boa prática evitar usar o mesmo nome.

### <a name="linux"></a>Linux

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificados e certificado ca raiz.

2. Crie o certificado CA do dispositivo IoT Edge e a chave privada com o seguinte comando. Forneça um nome para o certificado CA, por **exemplo, MyEdgeDeviceCA,** que é usado para nomear os ficheiros de saída.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Este comando de script cria vários ficheiros de certificadoe chave. O seguinte certificado e par de chaves deve ser copiado para um dispositivo IoT Edge e referenciado no ficheiro config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

O nome do dispositivo gateway passado para esses scripts não deve ser o mesmo que o parâmetro "nome de anfitrião" em config.yaml, ou o ID do dispositivo no IoT Hub.
Os scripts ajudam-no a evitar quaisquer problemas, afixando uma corda ".ca" ao nome do dispositivo gateway para evitar a colisão do nome no caso de um utilizador configurar o IoT Edge usando o mesmo nome em ambos os locais.
No entanto, é uma boa prática evitar usar o mesmo nome.

## <a name="create-iot-edge-device-identity-certificates"></a>Criar certificados de identidade do dispositivo IoT Edge

Os certificados de identidade do dispositivo são utilizados para fornecer dispositivos IoT Edge através do Serviço de Provisionamento de [Dispositivos Hub Azure IoT (DPS)](../iot-dps/index.yml).

Os certificados de identidade do dispositivo vão na secção de **provisionamento** do ficheiro config.yaml no dispositivo IoT Edge.

Antes de prosseguir com os passos nesta secção, siga os passos nas [scripts de configuração](#set-up-scripts) e crie as secções de [certificado saca raiz.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

Crie o certificado de identidade do dispositivo IoT Edge e a chave privada com o seguinte comando:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

O nome que passar para este comando será o ID do dispositivo IoT Edge no IoT Hub.

O novo comando de identidade do dispositivo cria vários ficheiros de certificados e chave, incluindo três que utilizará ao criar uma inscrição individual no DPS e instalar o tempo de execução ioT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Crie o certificado de identidade do dispositivo IoT Edge e a chave privada com o seguinte comando:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

O nome que passar para este comando será o ID do dispositivo IoT Edge no IoT Hub.

O script cria vários ficheiros de certificados e chave, incluindo três que utilizará ao criar uma inscrição individual no DPS e instalar o tempo de execução do IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Criar certificados de dispositivoa jusante

Se estiver a configurar um dispositivo IoT a jusante para um cenário de gateway, pode gerar certificados de demonstração para a autenticação X.509.
Existem duas formas de autenticar um dispositivo IoT utilizando certificados X.509: utilizar certs auto-assinados ou usar certs assinados pela autoridade de certificados (CA).
Para a autenticação auto-assinada X.509, por vezes referida como autenticação de impressão digital, é necessário criar novos certificados para colocar no seu dispositivo IoT.
Estes certificados têm uma impressão digital que partilha com o IoT Hub para autenticação.
Para a autenticação assinada pela X.509 (CA), é necessário um certificado CA raiz registado no IoT Hub que utiliza para assinar certificados para o seu dispositivo IoT.
Qualquer dispositivo que utilize um certificado emitido pelo certificado de raiz CA ou qualquer um dos seus certificados intermédios será autorizado a autenticar.

Os scripts de geração de certificados podem ajudá-lo a fazer certificados de demonstração para testar qualquer um destes cenários de autenticação.

Antes de prosseguir com os passos nesta secção, siga os passos nas [scripts de configuração](#set-up-scripts) e crie as secções de [certificado saca raiz.](#create-root-ca-certificate)

### <a name="self-signed-certificates"></a>Certificados auto-assinados

Ao autenticar um dispositivo IoT com certificados auto-assinados, tem de criar certificados de dispositivo com base no certificado CA raiz para a sua solução.
Depois, recupera-se uma "impressão digital" hexadecimal dos certificados para fornecer ao IoT Hub.
O seu dispositivo IoT também precisa de uma cópia dos seus certificados de dispositivo para que possa autenticar com o IoT Hub.

#### <a name="windows"></a>Windows

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificados e certificado ca raiz.

2. Crie dois certificados (primários e secundários) para o dispositivo a jusante. Uma convenção de nomeação fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, o rótulo primário ou secundário. Por exemplo:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Este comando de script cria vários ficheiros de certificadoe chave. Os seguintes pares de certificados e chaves devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que ligam ao IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada de impressão digital em contextos IoT Hub) de cada certificado. A impressão digital é uma corda de 40 caracteres hexadecimais. Utilize o seguinte comando aberto para visualizar o certificado e encontrar a impressão digital:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Execute este comando duas vezes, uma para o certificado primário e outra para o certificado secundário. Fornece impressões digitais para ambos os certificados quando regista um novo dispositivo IoT utilizando certificados X.509 auto-assinados.

#### <a name="linux"></a>Linux

1. Navegue para o diretório de trabalho que tem os scripts de geração de certificados e certificado ca raiz.

2. Crie dois certificados (primários e secundários) para o dispositivo a jusante. Uma convenção de nomeação fácil de usar é criar os certificados com o nome do dispositivo IoT e, em seguida, o rótulo primário ou secundário. Por exemplo:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Este comando de script cria vários ficheiros de certificadoe chave. Os seguintes pares de certificados e chaves devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que ligam ao IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Recupere a impressão digital SHA1 (chamada de impressão digital em contextos IoT Hub) de cada certificado. A impressão digital é uma corda de 40 caracteres hexadecimais. Utilize o seguinte comando aberto para visualizar o certificado e encontrar a impressão digital:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Fornece a impressão digital primária e secundária quando regista um novo dispositivo IoT utilizando certificados X.509 auto-assinados.

### <a name="ca-signed-certificates"></a>Certificados assinados pela CA

Quando autentica rumit um dispositivo IoT com certificados auto-assinados, precisa de carregar o certificado de CA raiz para a sua solução para o IoT Hub.
Em seguida, você executa uma verificação para provar ao IoT Hub que você possui o certificado de CA raiz.
Por fim, utiliza o mesmo certificado CA raiz para criar certificados de dispositivo para colocar no seu dispositivo IoT para que possa autenticar com o IoT Hub.

Os certificados nesta secção são para os passos na configuração de [segurança X.509 no seu hub Azure IoT](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Faça upload do ficheiro de certificado `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`ca raiz do seu diretório de trabalho, para o seu centro ioT.

2. Utilize o código fornecido no portal Azure para verificar se possui esse certificado de CA raiz.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Crie uma cadeia de certificados para o seu dispositivo a jusante. Utilize o mesmo ID do dispositivo com o que o dispositivo está registado no IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Este comando de script cria vários ficheiros de certificadoe chave. Os seguintes pares de certificados e chaves devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que ligam ao IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Faça upload do ficheiro de certificado `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`ca raiz do seu diretório de trabalho, para o seu centro ioT.

2. Utilize o código fornecido no portal Azure para verificar se possui esse certificado de CA raiz.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Crie uma cadeia de certificados para o seu dispositivo a jusante. Utilize o mesmo ID do dispositivo com o que o dispositivo está registado no IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Este comando de script cria vários ficheiros de certificadoe chave. Os seguintes pares de certificados e chaves devem ser copiados para o dispositivo IoT a jusante e referenciados nas aplicações que ligam ao IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
