---
title: Criar dispositivo de gateway transparente - Azure IoT Edge | Documentos da Microsoft
description: Utilizar um dispositivo Azure IoT Edge como gateway transparente que pode processar informações a partir de dispositivos de downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e61ddd6cb51795fad564b6246fb24ea4ce48f028
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982968"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como gateway transparente

Este artigo fornece instruções detalhadas para configurar um IoT Edge dispositivo para funcionar como um gateway transparente para outros dispositivos se comunicarem com o Hub IoT. Neste artigo, o termo *gateway do IoT Edge* refere-se a um dispositivo IoT Edge utilizado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Atualmente:
> * Dispositivos com capacidade de borda não é possível ligar aos gateways de IoT Edge. 
> * Dispositivos jusante não é possível utilizar o carregamento de ficheiros.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a primeira etapa:

1. **O dispositivo de gateway precisa ser capaz de se conectar com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino adequado.**
2. O dispositivo downstream precisa ter uma identidade de dispositivo para ser capaz de autenticar com o Hub IoT e saber se comunicar por meio de seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. O dispositivo downstream precisa ser capaz de se conectar com segurança ao seu dispositivo de gateway. Para obter mais informações, consulte [ligar um dispositivo jusante a um gateway do Azure IoT Edge](how-to-connect-downstream-device.md).


Para que um dispositivo funcione como um gateway, ele precisa ser capaz de se conectar com segurança a seus dispositivos downstream. O Azure IoT Edge permite-lhe utilizar uma infraestrutura de chaves públicas (PKI) para configurar ligações seguras entre dispositivos. Neste caso, estamos está a permitir que um dispositivo downstream ligar a um dispositivo IoT Edge que atua como um gateway transparente. Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo de gateway. Essa verificação de identidade impede que os dispositivos se conectem a gateways potencialmente mal-intencionados.

Um dispositivo de downstream pode ser qualquer aplicação ou a plataforma que têm uma identidade criada com o [IoT Hub do Azure](https://docs.microsoft.com/azure/iot-hub) serviço em nuvem. Em muitos casos, estas aplicações se utilizar o [do Azure IoT device SDK](../iot-hub/iot-hub-devguide-sdks.md). Para todos os fins práticos, um dispositivo de downstream ainda pode ser uma aplicação em execução no dispositivo de gateway do IoT Edge em si. 

Pode criar qualquer infraestrutura de certificado que permite a confiança necessária para a sua topologia de gateway de dispositivo. Neste artigo, assumimos a mesma configuração de certificado que você usaria para habilitar a [segurança de autoridade de certificação x. 509](../iot-hub/iot-hub-x509ca-overview.md) no Hub IOT, que envolve um certificado de autoridade de certificação x. 509 associado a um hub IOT específico (a AC raiz do Hub IOT), uma série de certificados assinados com essa autoridade de certificação e uma CA para o dispositivo IoT Edge.

![Configurar o certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>O termo "CA raiz" usado em todo este artigo refere-se ao certificado público mais alto da autoridade da cadeia de certificados PKI e não necessariamente à raiz do certificado de uma autoridade de certificação agregada. Em muitos casos, é, na verdade, um certificado público de CA intermediário. 

O gateway apresenta seu IoT Edge certificado de autoridade de certificação do dispositivo ao dispositivo downstream durante o início da conexão. O dispositivo downstream verifica se o certificado de autoridade de certificação do dispositivo IoT Edge está assinado pelo certificado de autoridade de certificação raiz. Esse processo permite que o dispositivo downstream confirme se o gateway vem de uma fonte confiável.

As etapas a seguir orientam você pelo processo de criação dos certificados e sua instalação nos locais certos no gateway. Pode utilizar qualquer máquina para gerar os certificados e, em seguida, copie os mesmos para o seu dispositivo IoT Edge. 

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo Azure IoT Edge para configurar como um gateway. Use as etapas de instalação do IoT Edge para um dos seguintes sistemas operacionais:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Gerar certificados com o Windows

Use as etapas nesta seção para gerar certificados de teste no Windows. Você pode usar um computador Windows para gerar os certificados e, em seguida, copiá-los para qualquer IoT Edge dispositivo em execução em qualquer sistema operacional com suporte. 

Os certificados gerados nesta secção destinam-se para finalidades de teste. 

### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL para o Windows na máquina que está a utilizar para gerar os certificados. Se você já tiver o OpenSSL instalado em seu dispositivo Windows, poderá ignorar esta etapa, mas verifique se o OpenSSL. exe está disponível em sua variável de ambiente PATH. 

Existem várias formas, pode instalar o OpenSSL:

* **Fáceis** Baixe e instale quaisquer [binários do OpenSSL](https://wiki.openssl.org/index.php/Binaries)de terceiros, por exemplo, do [OpenSSL no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para openssl.exe a variável de ambiente PATH. 
   
* **Aconselhável** Baixe o código-fonte OpenSSL e crie os binários em seu computador por conta própria ou por meio de [vcpkg](https://github.com/Microsoft/vcpkg). As instruções indicadas abaixo utilizam vcpkg para transferir o código-fonte, compilação e instalar o OpenSSL no seu computador Windows com passos simples.

   1. Navegue para um diretório onde pretende instalar vcpkg. Faremos referência a este diretório como  *\<VCPKGDIR >* . Siga as instruções para transferir e instalar [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Quando o vcpkg estiver instalado, execute o seguinte comando em um prompt do PowerShell para instalar o pacote OpenSSL para Windows x64. A instalação normalmente demora cerca de 5 minutos a ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adicionar `<VCPKGDIR>\installed\x64-windows\tools\openssl` a variável de ambiente PATH para que o arquivo de openssl.exe está disponível para invocação.

### <a name="prepare-creation-scripts"></a>Preparar os scripts de criação

O repositório Git do Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste. Nesta seção, você clonará o repositório de IoT Edge e executará os scripts. 

1. Abra uma janela do PowerShell no modo de administrador. 

2. Clone o repositório de git que contém os scripts para gerar certificados de não produção. Estes scripts ajudar a criar os certificados necessários para configurar um gateway transparente. Utilize o `git clone` comando ou [download ZIP](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue para o diretório no qual pretende trabalhar. Ao longo deste artigo, vamos chamar esse diretório  *\<de WRKDIR >* . Todos os certificados e chaves serão criados nesse diretório de trabalho.

4. Copie a configuração e os arquivos de script do repositório clonado para seu diretório de trabalho. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se você baixou o repositório como um zip, o nome da pasta `iotedge-master` será e o restante do caminho será o mesmo. 
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

7. Traga as funções usadas pelos scripts no namespace global do PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   A janela do PowerShell exibirá um aviso de que os certificados gerados por esse script são apenas para fins de teste e não devem ser usados em cenários de produção.

8. Verifique se o OpenSSL foi instalado corretamente e certifique-se de que não haverá colisões de nome com os certificados existentes. Se existirem problemas, o script deve descrever como corrigi-los no seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Criar certificados

Nesta secção, crie três certificados e, em seguida, ligá-los numa cadeia. Colocar os certificados num arquivo de cadeia permite-lhe para instalá-los facilmente no seu dispositivo de gateway do IoT Edge e todos os dispositivos downstream.  

1. Crie o certificado de autoridade de certificação raiz e faça com que ele assine um certificado intermediário. Os certificados são colocados em seu diretório de trabalho.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Esse comando de script cria vários arquivos de certificado e de chave, mas vamos nos referir a um em particular mais adiante neste artigo:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Crie o certificado de autoridade de certificação do dispositivo IoT Edge e a chave privada com o comando a seguir. Forneça um nome para o certificado de autoridade de certificação, por exemplo, **MyEdgeDeviceCA**. O nome é usado para nomear os arquivos e durante a geração do certificado. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Esse comando de script cria vários arquivos de certificado e chave, incluindo dois que vamos nos referir mais adiante neste artigo:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Se você fornecer um nome diferente de **MyEdgeDeviceCA**, os certificados e as chaves criados por esse comando refletirão esse nome. 

Agora que você tem os certificados, pule para [instalar certificados no gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Gerar certificados com o Linux

Use as etapas nesta seção para gerar certificados de teste no Linux. Você pode usar um computador Linux para gerar os certificados e, em seguida, copiá-los para qualquer IoT Edge dispositivo em execução em qualquer sistema operacional com suporte. 

Os certificados gerados nesta secção destinam-se para finalidades de teste. 

### <a name="prepare-creation-scripts"></a>Preparar os scripts de criação

O repositório Git do Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste. Nesta seção, você clonará o repositório de IoT Edge e executará os scripts. 

1. Clone o repositório de git que contém os scripts para gerar certificados de não produção. Estes scripts ajudar a criar os certificados necessários para configurar um gateway transparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue para o diretório no qual pretende trabalhar. Vamos nos referir a esse diretório em todo o artigo como  *\<WRKDIR >* . Todos os arquivos de certificado e de chave serão criados nesse diretório.
  
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

### <a name="create-certificates"></a>Criar certificados

Nesta secção, crie três certificados e, em seguida, ligá-los numa cadeia. Permite que colocar os certificados num arquivo de cadeia para instalá-los facilmente no seu dispositivo de gateway do IoT Edge e todos os dispositivos downstream.  

1. Crie o certificado de autoridade de certificação raiz e um certificado intermediário. Estes certificados são colocados num  *\<WRKDIR >* .

   Se você já tiver criado certificados raiz e intermediários nesse diretório de trabalho, não execute esse script novamente. A reexecução desse script substituirá os certificados existentes. Em vez disso, vá para a próxima etapa. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   O script cria vários certificados e chaves. Anote uma, à qual iremos nos referir na próxima seção:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Crie o certificado de autoridade de certificação do dispositivo IoT Edge e a chave privada com o comando a seguir. Forneça um nome para o certificado de autoridade de certificação, por exemplo, **MyEdgeDeviceCA**. O nome é usado para nomear os arquivos e durante a geração do certificado. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   O script cria vários certificados e chaves. Anote dois, aos quais iremos nos referir na próxima seção: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Se você fornecer um nome diferente de **MyEdgeDeviceCA**, os certificados e as chaves criados por esse comando refletirão esse nome. 

## <a name="install-certificates-on-the-gateway"></a>Instalar certificados no gateway

Agora que fez uma cadeia de certificados, terá de instalá-lo no dispositivo de gateway do IoT Edge e configurar o runtime do IoT Edge para referenciar os novos certificados. 

1. Copie os seguintes ficheiros da  *\<WRKDIR >* . Salvá-los em qualquer lugar no seu dispositivo IoT Edge. Faremos referência para o diretório de destino no seu dispositivo IoT Edge como  *\<CERTDIR >* . 

   * Certificado de acesso condicional de dispositivo-  `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Chave privada de AC de dispositivo- `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * AC raiz-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.  Se você tiver gerado os certificados no próprio dispositivo IoT Edge, poderá ignorar esta etapa e usar o caminho para o diretório de trabalho.

2. Abra o ficheiro de configuração do daemon de segurança do IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Defina as propriedades do **certificado** no arquivo config. YAML como o caminho completo para o certificado e os arquivos de chave no dispositivo IOT Edge. Remova o `#` caractere antes das propriedades do certificado para remover os comentários das quatro linhas. Lembre-se de que os recuos em YAML são dois espaços.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Em dispositivos Linux, verifique se o usuário **iotedge** tem permissões de leitura para o diretório que contém os certificados. 

## <a name="deploy-edgehub-to-the-gateway"></a>Implementar EdgeHub para o gateway

Quando você instala o IoT Edge pela primeira vez em um dispositivo, somente um módulo do sistema é iniciado automaticamente: o agente do IoT Edge. Para o seu dispositivo funcionar como um gateway, terá de ambos os módulos do sistema. Se você ainda não tiver implantado nenhum módulo para seu dispositivo de gateway, crie uma implantação inicial para seu dispositivo para iniciar o segundo módulo do sistema, o Hub de IoT Edge. A implantação ficará vazia porque você não adiciona nenhum módulo no assistente, mas verificará se ambos os módulos do sistema estão em execução. 

Pode verificar quais os módulos estão em execução num dispositivo com o comando `iotedge list`. Se a lista retornar apenas o módulo **edgeAgent** sem **edgeHub**, use as seguintes etapas:

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

Os dispositivos IoT Edge padrão não precisam de nenhuma conectividade de entrada para funcionar, pois toda a comunicação com o Hub IoT é feita por meio de conexões de saída. Os dispositivos de gateway são diferentes porque precisam receber mensagens de seus dispositivos downstream. Se um firewall estiver entre os dispositivos downstream e o dispositivo de gateway, a comunicação também precisará ser possível por meio do firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos com suporte do hub de IoT Edge deve estar aberto para o tráfego de entrada de dispositivos downstream. Os protocolos com suporte são MQTT, AMQP e HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Encaminhar mensagens a partir de dispositivos de downstream
O runtime do IoT Edge pode encaminhar mensagens enviadas a partir de dispositivos downstream, assim como as mensagens enviadas por módulos. Esse recurso permite que você execute análises em um módulo em execução no gateway antes de enviar qualquer dado para a nuvem. 

Atualmente, a maneira que rotear as mensagens enviadas pelos dispositivos downstream é diferenciá-los de mensagens enviadas por módulos. As mensagens enviadas por todos os módulos de contenham uma propriedade de sistema chamada **connectionModuleId** mas não as mensagens enviadas pelos dispositivos downstream. Pode utilizar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade de sistema. 

A rota abaixo é um exemplo que enviaria mensagens de qualquer dispositivo downstream para um módulo chamado `ai_insights`e, em seguida `ai_insights` , do Hub IOT.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Para obter mais informações sobre o roteamento de mensagens, consulte [implementar módulos e rotas](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Habilitar operação offline estendida

A partir da [versão v 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de execução do IOT Edge, o dispositivo de gateway e os dispositivos downstream que se conectam a ele podem ser configurados para operação estendida offline. 

Com esse recurso, os módulos locais ou dispositivos downstream podem se autenticar novamente com o dispositivo IoT Edge conforme necessário e se comunicarem entre si usando mensagens e métodos mesmo quando estiverem desconectados do Hub IoT. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge, dispositivos, módulos e dispositivos de subordinados](offline-capabilities.md).

Para habilitar recursos offline estendidos, você estabelece uma relação pai-filho entre um dispositivo IoT Edge gateway e dispositivos downstream que se conectarão a ele. Essas etapas são explicadas em mais detalhes em [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passos Seguintes

Agora que tem um dispositivo IoT Edge como gateway transparente a funcionar, terá de configurar os seus dispositivos jusante para o gateway de confiança e enviar mensagens ao mesmo. Para obter mais informações, consulte [conectar um dispositivo downstream a um Azure IOT Edge gateway](how-to-connect-downstream-device.md) e [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
