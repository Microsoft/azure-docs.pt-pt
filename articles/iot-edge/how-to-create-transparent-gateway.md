---
title: Criar dispositivo de gateway transparente - Azure IoT Edge | Documentos da Microsoft
description: Utilizar um dispositivo Azure IoT Edge como gateway transparente que pode processar informações a partir de dispositivos de downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058375"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como gateway transparente

Este artigo fornece instruções detalhadas para configurar um dispositivo IoT Edge para funcionar como gateway transparente para outros dispositivos comunicar com IoT Hub. Neste artigo, o termo *gateway do IoT Edge* refere-se a um dispositivo IoT Edge utilizado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo do IoT Edge pode ser utilizado como um gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Atualmente:
> * Dispositivos com capacidade de borda não é possível ligar aos gateways de IoT Edge. 
> * Dispositivos jusante não é possível utilizar o carregamento de ficheiros.

Existem três passos gerais para configurar uma ligação de gateway transparente com êxito. Este artigo abrange o primeiro passo:

1. **O dispositivo de gateway tem de ser capaz de ligar a dispositivos downstream, receber comunicações a partir de dispositivos de downstream, e encaminhar mensagens para o destino correto.**
2. O dispositivo de downstream tem de ter uma identidade de dispositivo para ser capaz de autenticar com o IoT Hub e saber para se comunicar por meio do seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md).
3. O dispositivo de downstream tem de conseguir ligar de forma segura para o seu dispositivo de gateway. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).


Para um dispositivo funcionar como um gateway, ele precisa ser capaz de ligar de forma segura para seus dispositivos de downstream. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para configurar ligações seguras entre dispositivos. Neste caso, estamos está a permitir que um dispositivo downstream ligar a um dispositivo IoT Edge que atua como um gateway transparente. Para manter a segurança razoável, o dispositivo de downstream deve confirmar a identidade do dispositivo de gateway. Esta verificação de identidade impede que os seus dispositivos ligam aos gateways potencialmente maliciosos.

Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo de downstream ainda pode ser uma aplicação em execução no dispositivo de gateway do IoT Edge em si. 

Pode criar qualquer infraestrutura de certificado que permite a confiança necessária para a sua topologia de gateway de dispositivo. Neste artigo, partimos do princípio a mesma configuração de certificado que pretende utilizar para ativar [segurança de AC X.509](../iot-hub/iot-hub-x509ca-overview.md) no IoT Hub, que envolve um certificado X.509 de AC associado a um específico IoT hub (o IoT hub AC de raiz), uma série de certificados assinados com esta AC e uma autoridade de certificação para o dispositivo do IoT Edge.

![Configurar o certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>O termo "AC de raiz" utilizado ao longo deste artigo refere-se para o certificado público da autoridade superior de cadeia de certificados PKI e não necessariamente a raiz de certificado de uma autoridade de certificação agregado. Em muitos casos, é realmente um público certificado da AC intermediária. 

O gateway apresenta o IoT Edge certificado de AC de dispositivo para o dispositivo jusante durante a inicialização da ligação. O dispositivo de downstream verifica para se certificar de que o certificado de AC de dispositivo do IoT Edge está assinado pelo certificado de AC de raiz. Este processo permite ao dispositivo downstream confirmar que o gateway é proveniente de uma origem fidedigna.

Os seguintes passos guiá-lo pelo processo de criação dos certificados e instalá-los nos locais corretos no gateway. Pode utilizar qualquer máquina para gerar os certificados e, em seguida, copie os mesmos para o seu dispositivo IoT Edge. 

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge para configurar como um gateway. Utilize os passos de instalação do IoT Edge para um dos seguintes sistemas operativos:
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [ARM32 do Linux](./how-to-install-iot-edge-linux-arm.md)

Este artigo refere-se para o *nome de anfitrião de gateway* em vários pontos. O nome de anfitrião de gateway é declarada no **hostname** parâmetro do ficheiro config.yaml no dispositivo de gateway do IoT Edge. Ele é usado para criar os certificados neste artigo e é referido na cadeia de ligação dos dispositivos downstream. O nome de anfitrião de gateway tem de ser resolvido para um endereço IP, usando DNS ou uma entrada de ficheiro host.

## <a name="generate-certificates-with-windows"></a>Gerar certificados com o Windows

Utilize os passos nesta secção para gerar certificados de teste no Windows. Pode utilizar uma máquina do Windows para gerar os certificados e, em seguida, copie os mesmos para qualquer dispositivo IoT Edge em execução em qualquer sistema operativo suportado. 

Os certificados gerados nesta secção destinam-se para finalidades de teste. 

### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL para o Windows na máquina que está a utilizar para gerar os certificados. Se já tiver OpenSSL instalada no seu dispositivo Windows, pode ignorar este passo, mas certifique-se de que openssl.exe está disponível na variável de ambiente do caminho. 

Existem várias formas, pode instalar o OpenSSL:

* **Mais fácil:** Transfira e instale qualquer [binários de OpenSSL de terceiros](https://wiki.openssl.org/index.php/Binaries), por exemplo, do [OpenSSL no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe a variável de ambiente PATH. 
   
* **Recomendado:** Transferir o código-fonte OpenSSL e criar os binários no seu computador, por si próprio ou por meio [vcpkg](https://github.com/Microsoft/vcpkg). As instruções indicadas abaixo utilizam vcpkg para transferir o código-fonte, compilação e instalar o OpenSSL no seu computador Windows com passos simples.

   1. Navegue para um diretório onde pretende instalar vcpkg. Faremos referência a este diretório como  *\<VCPKGDIR >* . Siga as instruções para transferir e instalar [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Uma vez vcpkg estiver instalado, execute o seguinte comando a partir de uma linha de comandos do powershell para instalar o pacote de OpenSSL para o Windows x64. A instalação normalmente demora cerca de 5 minutos a ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adicionar `<VCPKGDIR>\installed\x64-windows\tools\openssl` a variável de ambiente PATH para que o arquivo de openssl.exe está disponível para invocação.

### <a name="prepare-creation-scripts"></a>Preparar os scripts de criação

O repositório de git do Azure IoT Edge contém os scripts que pode utilizar para gerar certificados de teste. Nesta secção, clone o repositório do IoT Edge e executar os scripts. 

1. Abra uma janela do PowerShell no modo de administrador. 

2. Clone o repositório de git que contém os scripts para gerar certificados de não produção. Estes scripts ajudar a criar os certificados necessários para configurar um gateway transparente. Utilize o `git clone` comando ou [download ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue para o diretório no qual pretende trabalhar. Ao longo deste artigo, vamos chamar este diretório  *\<WRKDIR >* . Todos os certificados e chaves serão criadas neste diretório de trabalho.

4. Copie os ficheiros de configuração e o script do repositório clonado no seu diretório de trabalho. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se transferiu o repositório como um ZIP, então é o nome da pasta `iotedge-master` e o resto do caminho é o mesmo. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Ative o PowerShell para executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Colocar as funções usadas pelos scripts para o espaço de nomes global do PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   A janela do PowerShell irá apresentar um aviso de que os certificados gerados por este script são apenas para fins de teste e não devem ser utilizados em cenários de produção.

8. Certifique-se de que OpenSSL foi instalado corretamente e certifique-se de que não haja conflitos de nomes com certificados existentes. Se existirem problemas, o script deve descrever como corrigi-los no seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Criar certificados

Nesta secção, crie três certificados e, em seguida, ligá-los numa cadeia. Colocar os certificados num arquivo de cadeia permite-lhe para instalá-los facilmente no seu dispositivo de gateway do IoT Edge e todos os dispositivos downstream.  

1. Criar o certificado de AC de raiz e tê-lo a assinar um certificado intermédio. Os certificados são colocados no diretório de trabalho.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Este comando de script cria vários certificado e ficheiros de chave, mas vamos referir-se a uma em particular neste artigo:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Crie o IoT Edge e chave privada de certificado de AC de dispositivo com o seguinte comando. Forneça o nome de anfitrião de gateway, que pode ser encontrado no arquivo iotedge\config.yaml o dispositivo de gateway. O nome de anfitrião de gateway é usado para nomear os arquivos e durante a geração do certificado. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   Este comando de script cria vários certificado e ficheiros de chave, incluindo duas que vamos fazer referência a este artigo:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Agora que tem os certificados, avançar diretamente para [instalar certificados no gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Gerar certificados com o Linux

Utilize os passos nesta secção para gerar certificados de teste no Linux. Pode utilizar uma máquina Linux para gerar os certificados e, em seguida, copie os mesmos para qualquer dispositivo IoT Edge em execução em qualquer sistema operativo suportado. 

Os certificados gerados nesta secção destinam-se para finalidades de teste. 

### <a name="prepare-creation-scripts"></a>Preparar os scripts de criação

O repositório de git do Azure IoT Edge contém os scripts que pode utilizar para gerar certificados de teste. Nesta secção, clone o repositório do IoT Edge e executar os scripts. 

1. Clone o repositório de git que contém os scripts para gerar certificados de não produção. Estes scripts ajudar a criar os certificados necessários para configurar um gateway transparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue para o diretório no qual pretende trabalhar. Faremos referência a este diretório em todo o artigo como  *\<WRKDIR >* . Todos os ficheiros de certificado e chave serão criados neste diretório.
  
3. Copie os ficheiros de configuração e de script do repositório clonado do IoT Edge no seu diretório de trabalho.

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

### <a name="create-certificates"></a>Criar certificados

Nesta secção, crie três certificados e, em seguida, ligá-los numa cadeia. Permite que colocar os certificados num arquivo de cadeia para instalá-los facilmente no seu dispositivo de gateway do IoT Edge e todos os dispositivos downstream.  

1. Crie o certificado de AC de raiz e um certificado intermédio. Estes certificados são colocados num  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   O script cria vários certificados e chaves. Tome nota de um, o que faremos referência a na secção seguinte:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Crie o IoT Edge e chave privada de certificado de AC de dispositivo com o seguinte comando. Forneça o nome de anfitrião de gateway, que pode ser encontrado no arquivo iotedge/config.yaml o dispositivo de gateway. O nome de anfitrião de gateway é usado para nomear os arquivos e durante a geração do certificado. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   O script cria vários certificados e chaves. Tome nota dos dois, o que faremos referência a na secção seguinte: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Instalar certificados no gateway

Agora que fez uma cadeia de certificados, terá de instalá-lo no dispositivo de gateway do IoT Edge e configurar o runtime do IoT Edge para referenciar os novos certificados. 

1. Copie os seguintes ficheiros da  *\<WRKDIR >* . Salvá-los em qualquer lugar no seu dispositivo IoT Edge. Faremos referência para o diretório de destino no seu dispositivo IoT Edge como  *\<CERTDIR >* . 

   * Certificado de acesso condicional de dispositivo-  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Chave privada de AC de dispositivo- `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * AC de raiz- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Pode utilizar um serviço como [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [cópia seguro protocolo](https://www.ssh.com/ssh/scp/) para mover os ficheiros de certificado.  Se gerar os certificados no próprio dispositivo IoT Edge, pode ignorar este passo e utilizar o caminho para o diretório de trabalho.

2. Abra o ficheiro de configuração do daemon de segurança do IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Definir o **certificado** propriedades no ficheiro config.yaml para o caminho completo para os ficheiros de certificado e chave no dispositivo IoT Edge. Remover o `#` caráter antes das propriedades do certificado para retirar os comentários as quatro linhas. Lembre-se de que recua no yaml é dois espaços.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Em dispositivos de Linux, certifique-se de que o usuário **iotedge** tem permissões de leitura para o diretório que contém os certificados. 

## <a name="deploy-edgehub-to-the-gateway"></a>Implementar EdgeHub para o gateway

Ao instalar o IoT Edge num dispositivo, o módulo de apenas um sistema é iniciado automaticamente: o agente do IoT Edge. Para o seu dispositivo funcionar como um gateway, terá de ambos os módulos do sistema. Se não tiver implementado quaisquer módulos para o seu dispositivo de gateway antes, crie uma implementação inicial para o seu dispositivo iniciar o módulo de sistema segundo, o hub IoT Edge. A implementação terá um aspeto vazia porque não adicione quaisquer módulos no assistente, mas ele será Certifique-se de que ambos os módulos do sistema estão em execução. 

Pode verificar quais os módulos estão em execução num dispositivo com o comando `iotedge list`. Se a lista devolve apenas o módulo **edgeAgent** sem **edgeHub**, utilize os seguintes passos:

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

## <a name="open-ports-on-gateway-device"></a>Abrir portas no dispositivo de gateway

Dispositivos do IoT Edge padrão não tem qualquer conectividade de entrada para a função, porque todas as comunicações com o IoT Hub é feita por meio de conexões de saída. Dispositivos de gateway são diferentes porque precisam para receber mensagens a partir dos seus dispositivos de downstream. Se uma firewall entre os dispositivos a jusante e o dispositivo de gateway, comunicação tem de ser possível através da firewall também.

Para um cenário de gateway funcione, pelo menos um dos protocolos com suporte do hub IoT Edge tem de estar aberto para tráfego de entrada dos dispositivos de downstream. Os protocolos suportados são MQTT, AMQP e HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Encaminhar mensagens a partir de dispositivos de downstream
O runtime do IoT Edge pode encaminhar mensagens enviadas a partir de dispositivos downstream, assim como as mensagens enviadas por módulos. Esta funcionalidade permite-lhe efetuar análises num módulo em execução no gateway antes de enviar quaisquer dados para a cloud. 

Atualmente, a maneira que rotear as mensagens enviadas pelos dispositivos downstream é diferenciá-los de mensagens enviadas por módulos. As mensagens enviadas por todos os módulos de contenham uma propriedade de sistema chamada **connectionModuleId** mas não as mensagens enviadas pelos dispositivos downstream. Pode utilizar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade de sistema. 

A rota segue um exemplo que seria enviar mensagens de qualquer dispositivo jusante a um módulo com o nome `ai_insights`e, em seguida, a partir da `ai_insights` ao IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Para obter mais informações sobre o roteamento de mensagens, consulte [implementar módulos e rotas](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Ativar a operação offline expandida

Começando com o [a v1.0.4 versão](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do runtime do IoT Edge, o dispositivo de gateway e a jusante dispositivos que se ligam à mesma podem ser configurados para a operação offline expandida. 

Com esta capacidade, os módulos locais ou dispositivos downstream podem novamente à autenticação do dispositivo IoT Edge conforme necessário e comunicar entre si através de mensagens e métodos, mesmo quando estiverem desconectados do IoT hub. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge, dispositivos, módulos e dispositivos de subordinados](offline-capabilities.md).

Para habilitar recursos offline expandidos, estabelece uma relação principal-subordinado entre um dispositivo de gateway do IoT Edge e downstream dispositivos que irão ligar à mesma. Esses passos são explicados de forma mais detalhada [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que tem um dispositivo IoT Edge como gateway transparente a funcionar, terá de configurar os seus dispositivos jusante para o gateway de confiança e enviar mensagens ao mesmo. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md) e [autenticar um dispositivo hub IoT do Azure de downstream](how-to-authenticate-downstream-device.md).
