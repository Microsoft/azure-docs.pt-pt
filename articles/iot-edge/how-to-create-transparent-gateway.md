---
title: Criar dispositivo de gateway transparente - Azure IoT Edge | Documentos da Microsoft
description: Utilizar um dispositivo Azure IoT Edge como gateway transparente que pode processar informações a partir de dispositivos de downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c769ae8e684a94e60f6a2e31ba404a0593f7aa78
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096712"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como gateway transparente

Este artigo fornece instruções detalhadas para a configuração de dispositivos do IoT Edge para funcionar como gateway transparente para outros dispositivos comunicar com IoT Hub. Neste artigo, o termo *gateway do IoT Edge* refere-se a um dispositivo IoT Edge utilizado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo do IoT Edge pode ser utilizado como um gateway](./iot-edge-as-gateway.md), que fornece uma descrição geral conceptual.

>[!NOTE]
>Atualmente:
> * Se o gateway estiver desconectado do IoT Hub, os dispositivos jusante não é possível autenticar com o gateway.
> * Dispositivos com capacidade de borda não é possível ligar aos gateways de IoT Edge. 
> * Dispositivos jusante não é possível utilizar o carregamento de ficheiros.

Para um dispositivo funcionar como um gateway, ele precisa ser capaz de se liguem a dispositivos de downstream. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para configurar ligações seguras entre dispositivos. Neste caso, estamos está a permitir que um dispositivo downstream ligar a um dispositivo IoT Edge que atua como um gateway transparente. Para manter a segurança razoável, o dispositivo de downstream deve confirmar a identidade do dispositivo de limite, uma vez que apenas pretende que os dispositivos se liguem ao seus gateways e não um gateway potencialmente malicioso.

Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo de downstream ainda pode ser uma aplicação em execução no dispositivo de gateway do IoT Edge em si. 

Pode criar qualquer infraestrutura de certificado que permite a confiança necessária para a sua topologia de gateway de dispositivo. Neste artigo, partimos do princípio a mesma configuração de certificado que pretende utilizar para ativar [segurança de AC X.509](../iot-hub/iot-hub-x509ca-overview.md) no IoT Hub, que envolve um certificado X.509 de AC associado a um hub de IoT específico (o IoT hub proprietário AC) e uma série de certificados, sessão iniciada com esta AC e uma autoridade de certificação para o dispositivo de limite.

![Configurar o certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

O gateway apresenta o respetivo certificado de AC de dispositivo do Edge no dispositivo jusante durante a inicialização da ligação. O dispositivo de downstream verifica para se certificar de que o certificado de AC de dispositivo do Edge está assinado pelo certificado de AC de proprietário. Este processo permite ao dispositivo downstream confirmar se que o gateway é proveniente de uma origem fidedigna.

Os seguintes passos guiá-lo pelo processo de criação dos certificados e instalá-los nos locais corretos.

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge para configurar como um gateway. Pode utilizar o seu computador de desenvolvimento ou uma máquina virtual como um dispositivo IoT Edge com os passos para os seguintes sistemas operativos:
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [ARM32 do Linux](./how-to-install-iot-edge-linux-arm.md)

Pode utilizar qualquer máquina para gerar os certificados e, em seguida, copie os mesmos para o seu dispositivo IoT Edge.

>[!NOTE]
>O "nome de gateway" utilizado para criar os certificados nessa instrução, tem de ser o mesmo nome que é utilizado como nome de anfitrião em seu arquivo de config.yaml do IoT Edge e como GatewayHostName na cadeia de ligação do dispositivo downstream. O "nome de gateway" tem de ser resolvido para um endereço IP, usando DNS ou uma entrada de ficheiro host. Comunicação com base no protocolo utilizado (MQTTS:8883 / AMQPS:5671 / HTTPS:433) tem de ser possível entre o dispositivo jusante e o transparant do IoT Edge. Se uma firewall entre, a respetiva porta tem de ser aberto.

## <a name="generate-certificates-with-windows"></a>Gerar certificados com o Windows

Utilize os passos nesta secção para gerar certificados de teste num dispositivo Windows. Pode gerar os certificados no seu dispositivo IoT Edge em si, ou utilizar uma máquina separada e copie os certificados finais para qualquer dispositivo IoT Edge com qualquer sistema operativo suportado. 

Os certificados gerados nesta secção destinam-se para finalidades de teste. 

### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL para o Windows na máquina que está a utilizar para gerar os certificados. Existem várias formas, pode instalar o OpenSSL:

   >[!NOTE]
   >Se já tiver OpenSSL instalada no seu dispositivo Windows, pode ignorar este passo, mas certifique-se de que openssl.exe está disponível na variável de ambiente do caminho.

* **Mais fácil:** Transfira e instale qualquer [binários de OpenSSL de terceiros](https://wiki.openssl.org/index.php/Binaries), por exemplo, do [neste projeto no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe a variável de ambiente PATH. 
   
* **Recomendado:** Transferir o código-fonte OpenSSL e criar os binários no seu computador, por si próprio ou por meio [vcpkg](https://github.com/Microsoft/vcpkg). As instruções indicadas abaixo utilizam vcpkg para transferir o código-fonte, compilação e instalar o OpenSSL no seu computador Windows com passos simples.

   1. Navegue para um diretório onde pretende instalar vcpkg. Faremos referência a este diretório como  *\<VCPKGDIR >*. Siga as instruções para transferir e instalar [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Depois de vcpkg é instalado, a partir de uma linha de comandos do powershell, execute o seguinte comando para instalar o pacote de OpenSSL para o Windows x64. A instalação normalmente demora cerca de 5 minutos a ser concluída.

      ```PowerShell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adicionar `<VCPKGDIR>\installed\x64-windows\tools\openssl` a variável de ambiente PATH para que o arquivo de openssl.exe está disponível para invocação.

### <a name="prepare-creation-scripts"></a>Preparar os scripts de criação

Azure IoT device SDK para C contém os scripts que pode utilizar para gerar certificados de teste. Nesta secção, clone o SDK e configurar o PowerShell.

1. Abra uma janela do PowerShell no modo de administrador. 

2. Clone o repositório de git que contém os scripts para gerar certificados de não produção. Estes scripts ajudar a criar os certificados necessários para configurar um gateway transparente. Utilize o `git clone` comando ou [download ZIP](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Navegue para o diretório no qual pretende trabalhar. Faremos referência a este diretório como  *\<WRKDIR >*.  Todos os ficheiros serão criados neste diretório.

4. Copie os ficheiros de configuração e script no seu diretório de trabalho. 

   ```PowerShell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Se transferiu o SDK como um ZIP, então é o nome da pasta `azure-iot-sdk-c-master` e o resto do caminho é o mesmo. 

5. Defina a variável de ambiente OPENSSL_CONF para usar o arquivo de configuração openssl_root_ca.cnf.

    ```PowerShell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Ative o PowerShell para executar os scripts.

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Colocar as funções, usadas pelos scripts, no espaço de nomes global do PowerShell.
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

8. Certifique-se de que OpenSSL foi instalado corretamente e certifique-se de que não haja conflitos de nomes com certificados existentes. Se existirem problemas, o script deve descrever como corrigi-los no seu sistema.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Criar certificados

Nesta secção, crie três certificados e, em seguida, ligá-los numa cadeia. Colocar os certificados num arquivo de cadeia permite-lhe para instalá-los facilmente no seu dispositivo de gateway do IoT Edge e todos os dispositivos downstream.  

1. Criar o certificado de AC de proprietário e tê-lo a assinar um certificado intermédio. Os certificados são colocados num  *\<WRKDIR >*.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

2. Crie o certificado de AC de dispositivo do Edge e a chave privada com o seguinte comando. Forneça um nome para o dispositivo de gateway, que será utilizado para nomes aos ficheiros e durante a geração do certificado. 

   ```PowerShell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Crie uma cadeia de certificados a partir do certificado de AC de proprietário, certificado intermédio e certificado de AC de dispositivo de periferia com o seguinte comando. 

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   O script cria os seguintes certificados e chaves:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Gerar certificados com o Linux

Utilize os passos nesta secção para gerar certificados de teste num dispositivo de Linux. Pode gerar os certificados no seu dispositivo IoT Edge em si, ou utilizar uma máquina separada e copie os certificados finais para qualquer dispositivo IoT Edge com qualquer sistema operativo suportado. 

### <a name="prepare-creation-scripts"></a>Preparar os scripts de criação

1. Clone o repositório de git que contém os scripts para gerar certificados de não produção. Estes scripts ajudar a criar os certificados necessários para configurar um gateway transparente. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Navegue para o diretório no qual pretende trabalhar. Faremos referência a este diretório como  *\<WRKDIR >*.  Todos os ficheiros serão criados neste diretório.
  
3. Copie os ficheiros de configuração e script no seu diretório de trabalho.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Configure OpenSSL para gerar certificados usando o script fornecido. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Criar certificados

Nesta secção, crie três certificados e, em seguida, ligá-los numa cadeia. Permite que colocar os certificados num arquivo de cadeia para instalá-los facilmente no seu dispositivo de gateway do IoT Edge e todos os dispositivos downstream.  

1. Crie o certificado de AC de proprietário e um certificado intermédio. Estes certificados são colocados num  *\<WRKDIR >*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   O script cria as seguintes chaves e certificados:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. Crie o certificado de AC de dispositivo do Edge e a chave privada com o seguinte comando. Forneça um nome para o dispositivo de gateway, que será utilizado para nomes aos ficheiros e durante a geração do certificado. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   O script cria os seguintes certificados e chaves:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Criar uma cadeia de certificados chamada **novo-edge-dispositivo-full-chain.cert.pem** do certificado de AC de proprietário, o certificado intermédio e o certificado de AC do dispositivo do Edge.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Instalar certificados no gateway

Agora que fez uma cadeia de certificados, terá de instalá-lo no dispositivo de gateway do IoT Edge e configurar o runtime do IoT Edge para referenciar os novos certificados. 

1. Copie os seguintes ficheiros da  *\<WRKDIR >*. Salvá-los em qualquer lugar no seu dispositivo IoT Edge. Faremos referência para o diretório de destino no seu dispositivo IoT Edge como  *\<CERTDIR >*. 

   Se gerar os certificados no próprio dispositivo Edge, pode ignorar este passo e utilizar o caminho para o diretório de trabalho.

   * Certificado de acesso condicional de dispositivo-  `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Chave privada de AC de dispositivo- `<WRKDIR>\private\new-edge-device.key.pem`
   * Proprietário da Califórnia- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Abra o ficheiro de configuração do daemon de segurança do IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Definir o **certificado** propriedades no ficheiro config.yaml para o caminho onde colocou os ficheiros de certificado e chave no dispositivo IoT Edge.

```yaml
certificates:
  device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Implementar EdgeHub para o gateway

Ao instalar o IoT Edge num dispositivo, o módulo de apenas um sistema é iniciado automaticamente: o agente do Edge. Para o seu dispositivo funcionar como um gateway, terá de ambos os módulos do sistema. Se não tiver implementado quaisquer módulos para o seu dispositivo de gateway antes, crie uma implementação para o seu dispositivo iniciar o módulo de sistema segundo, o hub do Edge. A implementação terá um aspeto vazia porque não adicionar quaisquer módulos no assistente, mas irá implementar ambos os módulos do sistema. 

Pode verificar quais os módulos estão em execução num dispositivo com o comando `iotedge list`.

1. No portal do Azure, navegue para o seu hub IoT.

2. Aceda a **IoT Edge** e selecione o seu dispositivo IoT Edge que pretende utilizar como um gateway.

3. Selecione **Definir Módulos**.

4. Selecione **Seguinte**.

5. Na **especificar rotas** página, deve ter uma rota envia todas as mensagens de todos os módulos para o IoT Hub. Caso contrário, adicione o seguinte código e, em seguida, selecione **Seguinte**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Na **rever modelo** página, selecione **submeter**.

## <a name="route-messages-from-downstream-devices"></a>Encaminhar mensagens a partir de dispositivos de downstream
O runtime do IoT Edge pode encaminhar mensagens enviadas a partir de dispositivos downstream, assim como as mensagens enviadas por módulos. Isto permite-lhe efetuar análises num módulo em execução no gateway antes de enviar quaisquer dados para a cloud. 

Atualmente, a maneira que rotear as mensagens enviadas pelos dispositivos downstream é diferenciá-los de mensagens enviadas por módulos. As mensagens enviadas por todos os módulos de contenham uma propriedade de sistema chamada **connectionModuleId** mas não as mensagens enviadas pelos dispositivos downstream. Pode utilizar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade de sistema. 

A seguir a rota seria usada para enviar mensagens a partir de qualquer dispositivo downstream para um nome de módulo `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Para obter mais informações sobre o roteamento de mensagens, consulte [implementar módulos e rotas](./module-composition.md#declare-routes).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem um dispositivo IoT Edge como gateway transparente a funcionar, terá de configurar os seus dispositivos jusante para o gateway de confiança e enviar mensagens ao mesmo. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).
