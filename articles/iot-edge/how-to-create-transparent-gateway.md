---
title: Criar dispositivo de gateway transparente-Azure IoT Edge | Microsoft Docs
description: Usar um dispositivo Azure IoT Edge como um gateway transparente que pode processar informações de dispositivos downstream
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792331"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Configurar um dispositivo IoT Edge para atuar como um gateway transparente

Este artigo fornece instruções detalhadas para configurar um IoT Edge dispositivo para funcionar como um gateway transparente para outros dispositivos se comunicarem com o Hub IoT. Neste artigo, o termo *IOT Edge gateway* refere-se a um dispositivo IOT Edge usado como um gateway transparente. Para obter mais informações, consulte [como um dispositivo de IOT Edge pode ser usado como um gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Existentes
> * Os dispositivos habilitados para borda não podem se conectar a IoT Edge gateways. 
> * Os dispositivos downstream não podem usar o carregamento de arquivo.

Há três etapas gerais para configurar uma conexão de gateway transparente bem-sucedida. Este artigo aborda a primeira etapa:

1. **O dispositivo de gateway precisa ser capaz de se conectar com segurança a dispositivos downstream, receber comunicações de dispositivos downstream e rotear mensagens para o destino adequado.**
2. O dispositivo downstream precisa ter uma identidade de dispositivo para ser capaz de autenticar com o Hub IoT e saber se comunicar por meio de seu dispositivo de gateway. Para obter mais informações, consulte [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).
3. O dispositivo downstream precisa ser capaz de se conectar com segurança ao seu dispositivo de gateway. Para obter mais informações, consulte [conectar um dispositivo downstream a um gateway de Azure IOT Edge](how-to-connect-downstream-device.md).


Para que um dispositivo funcione como um gateway, ele precisa ser capaz de se conectar com segurança a seus dispositivos downstream. Azure IoT Edge permite que você use uma PKI (infraestrutura de chave pública) para configurar conexões seguras entre dispositivos. Nesse caso, estamos permitindo que um dispositivo downstream se conecte a um dispositivo IoT Edge atuando como um gateway transparente. Para manter a segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo de gateway. Essa verificação de identidade impede que os dispositivos se conectem a gateways potencialmente mal-intencionados.

Um dispositivo downstream em um cenário de gateway transparente pode ser qualquer aplicativo ou plataforma que tenha uma identidade criada com o serviço de nuvem do [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub) . Em muitos casos, esses aplicativos usam o [SDK do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md). Para todas as finalidades práticas, um dispositivo downstream poderia até ser um aplicativo em execução no próprio dispositivo de gateway de IoT Edge. No entanto, um dispositivo de IoT Edge não pode ser downstream de um gateway de IoT Edge. 

Você pode criar qualquer infraestrutura de certificado que habilite a confiança necessária para sua topologia de gateway de dispositivo. Neste artigo, assumimos a mesma configuração de certificado que você usaria para habilitar a [segurança de autoridade de certificação x. 509](../iot-hub/iot-hub-x509ca-overview.md) no Hub IOT, que envolve um certificado de autoridade de certificação x. 509 associado a um hub IOT específico (a AC raiz do Hub IOT), uma série de certificados assinados com essa AC e uma AC para o dispositivo IOT Edge.

![Configuração do certificado do gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>O termo "CA raiz" usado em todo este artigo refere-se ao certificado público mais alto da autoridade da cadeia de certificados PKI e não necessariamente à raiz do certificado de uma autoridade de certificação agregada. Em muitos casos, é, na verdade, um certificado público de CA intermediário. 

O gateway apresenta seu IoT Edge certificado de autoridade de certificação do dispositivo ao dispositivo downstream durante o início da conexão. O dispositivo downstream verifica se o certificado de autoridade de certificação do dispositivo IoT Edge está assinado pelo certificado de autoridade de certificação raiz. Esse processo permite que o dispositivo downstream confirme se o gateway vem de uma fonte confiável.

As etapas a seguir orientam você pelo processo de criação dos certificados e sua instalação nos locais certos no gateway. Você pode usar qualquer computador para gerar os certificados e, em seguida, copiá-los para o dispositivo IoT Edge. 

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de desenvolvimento para criar certificados. 
* Um dispositivo Azure IoT Edge para configurar como um gateway. Use as etapas de instalação do IoT Edge para um dos seguintes sistemas operacionais:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Gerar certificados com o Windows

Use as etapas nesta seção para gerar certificados de teste no Windows. Você pode usar um computador Windows para gerar os certificados e, em seguida, copiá-los para qualquer IoT Edge dispositivo em execução em qualquer sistema operacional com suporte. 

Os certificados gerados nesta seção destinam-se apenas a fins de teste. 

### <a name="install-openssl"></a>Instalar o OpenSSL

Instale o OpenSSL for Windows no computador que você está usando para gerar os certificados. Se você já tiver o OpenSSL instalado em seu dispositivo Windows, poderá ignorar esta etapa, mas verifique se o OpenSSL. exe está disponível em sua variável de ambiente PATH. 

Há várias maneiras de instalar o OpenSSL, incluindo:

* **Mais fácil:** Baixe e instale quaisquer [binários do OpenSSL](https://wiki.openssl.org/index.php/Binaries)de terceiros, por exemplo, do [OpenSSL no SourceForge](https://sourceforge.net/projects/openssl/). Adicione o caminho completo para OpenSSL. exe à variável de ambiente PATH. 
   
* **Recomendado:** Baixe o código-fonte OpenSSL e crie os binários em seu computador por conta própria ou por meio de [vcpkg](https://github.com/Microsoft/vcpkg). As instruções listadas abaixo usam o vcpkg para baixar o código-fonte, compilar e instalar o OpenSSL em seu computador Windows com etapas fáceis.

   1. Navegue até um diretório onde você deseja instalar o vcpkg. Vamos nos referir a esse diretório como *\<VCPKGDIR >* . Siga as instruções para baixar e instalar o [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. Quando o vcpkg estiver instalado, execute o seguinte comando em um prompt do PowerShell para instalar o pacote OpenSSL para Windows x64. A instalação normalmente leva cerca de 5 minutos para ser concluída.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Adicione `<VCPKGDIR>\installed\x64-windows\tools\openssl` à variável de ambiente PATH para que o arquivo OpenSSL. exe esteja disponível para invocação.

### <a name="prepare-creation-scripts"></a>Preparar scripts de criação

O repositório Git do Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste. Nesta seção, você clonará o repositório de IoT Edge e executará os scripts. 

1. Abra uma janela do PowerShell no modo de administrador. 

2. Clone o repositório git que contém scripts para gerar certificados de não produção. Esses scripts ajudam a criar os certificados necessários para configurar um gateway transparente. Use o comando `git clone` ou [Baixe o zip](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navegue até o diretório no qual você deseja trabalhar. Ao longo deste artigo, vamos chamar esse diretório *\<WRKDIR >* . Todos os certificados e chaves serão criados nesse diretório de trabalho.

4. Copie a configuração e os arquivos de script do repositório clonado para seu diretório de trabalho. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Se você baixou o repositório como um ZIP, o nome da pasta será `iotedge-master` e o restante do caminho será o mesmo. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Habilite o PowerShell para executar os scripts.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Traga as funções usadas pelos scripts no namespace global do PowerShell.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   A janela do PowerShell exibirá um aviso de que os certificados gerados por esse script são apenas para fins de teste e não devem ser usados em cenários de produção.

8. Verifique se o OpenSSL foi instalado corretamente e certifique-se de que não haverá colisões de nome com os certificados existentes. Se houver problemas, o script deverá descrever como corrigi-los em seu sistema.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Criar certificados

Nesta seção, você cria três certificados e os conecta em uma cadeia. Colocar os certificados em um arquivo de cadeia permite que você os Instale facilmente em seu dispositivo de gateway IoT Edge e em todos os dispositivos downstream.  

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

Os certificados gerados nesta seção destinam-se apenas a fins de teste. 

### <a name="prepare-creation-scripts"></a>Preparar scripts de criação

O repositório Git do Azure IoT Edge contém scripts que você pode usar para gerar certificados de teste. Nesta seção, você clonará o repositório de IoT Edge e executará os scripts. 

1. Clone o repositório git que contém scripts para gerar certificados de não produção. Esses scripts ajudam a criar os certificados necessários para configurar um gateway transparente. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navegue até o diretório no qual você deseja trabalhar. Vamos nos referir a esse diretório em todo o artigo como *\<WRKDIR >* . Todos os arquivos de certificado e de chave serão criados nesse diretório.
  
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

Nesta seção, você cria três certificados e os conecta em uma cadeia. Colocar os certificados em um arquivo de cadeia permite instalá-los facilmente em seu dispositivo de gateway IoT Edge e em todos os dispositivos downstream.  

1. Crie o certificado de autoridade de certificação raiz e um certificado intermediário. Esses certificados são colocados em *\<WRKDIR >* .

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

Agora que você já fez uma cadeia de certificados, precisará instalá-la no dispositivo IoT Edge gateway e configurar o IoT Edge Runtime para fazer referência aos novos certificados. 

1. Copie os seguintes arquivos de *\<WRKDIR >* . Salve-os em qualquer lugar no dispositivo IoT Edge. Vamos nos referir ao diretório de destino em seu dispositivo IoT Edge como *\<CERTDIR >* . 

   * Certificado de autoridade de certificação do dispositivo-`<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Chave privada da AC do dispositivo-`<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * AC raiz-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Você pode usar um serviço como [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) ou uma função como [protocolo de cópia segura](https://www.ssh.com/ssh/scp/) para mover os arquivos de certificado.  Se você tiver gerado os certificados no próprio dispositivo IoT Edge, poderá ignorar esta etapa e usar o caminho para o diretório de trabalho.

2. Abra o arquivo de configuração do daemon de segurança IoT Edge. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Defina as propriedades do **certificado** no arquivo config. YAML como o caminho completo para o certificado e os arquivos de chave no dispositivo IOT Edge. Remova o caractere de `#` antes das propriedades do certificado para remover os comentários das quatro linhas. Lembre-se de que os recuos em YAML são dois espaços.

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

## <a name="deploy-edgehub-to-the-gateway"></a>Implantar EdgeHub no gateway

Quando você instala o IoT Edge pela primeira vez em um dispositivo, somente um módulo do sistema é iniciado automaticamente: o agente do IoT Edge. Para que seu dispositivo funcione como um gateway, você precisa dos dois módulos do sistema. Se você ainda não tiver implantado nenhum módulo para seu dispositivo de gateway, crie uma implantação inicial para seu dispositivo para iniciar o segundo módulo do sistema, o Hub de IoT Edge. A implantação ficará vazia porque você não adiciona nenhum módulo no assistente, mas verificará se ambos os módulos do sistema estão em execução. 

Você pode verificar quais módulos estão sendo executados em um dispositivo com o comando `iotedge list`. Se a lista retornar apenas o módulo **edgeAgent** sem **edgeHub**, use as seguintes etapas:

1. No portal do Azure, navegue para o seu hub IoT.

2. Vá para **IOT Edge** e selecione o dispositivo IOT Edge que você deseja usar como um gateway.

3. Selecione **Definir Módulos**.

4. Selecione **Seguinte**.

5. Na página **especificar rotas** , você deve ter uma rota padrão que envia todas as mensagens de todos os módulos para o Hub IOT. Caso contrário, adicione o seguinte código e, em seguida, selecione **Seguinte**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. Na página **modelo de revisão** , selecione **Enviar**.

## <a name="open-ports-on-gateway-device"></a>Abrir portas no dispositivo de gateway

Os dispositivos IoT Edge padrão não precisam de nenhuma conectividade de entrada para funcionar, pois toda a comunicação com o Hub IoT é feita por meio de conexões de saída. Os dispositivos de gateway são diferentes porque precisam receber mensagens de seus dispositivos downstream. Se um firewall estiver entre os dispositivos downstream e o dispositivo de gateway, a comunicação também precisará ser possível por meio do firewall.

Para que um cenário de gateway funcione, pelo menos um dos protocolos com suporte do hub de IoT Edge deve estar aberto para o tráfego de entrada de dispositivos downstream. Os protocolos com suporte são MQTT, AMQP, HTTPS, MQTT sobre WebSockets e AMQP sobre WebSockets. 

| Porta | Protocolo |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Rotear mensagens de dispositivos downstream
O tempo de execução de IoT Edge pode rotear mensagens enviadas de dispositivos downstream, assim como mensagens enviadas por módulos. Esse recurso permite que você execute análises em um módulo em execução no gateway antes de enviar qualquer dado para a nuvem. 

Atualmente, a maneira de rotear mensagens enviadas por dispositivos downstream é diferenciando-as das mensagens enviadas por módulos. Todas as mensagens enviadas por módulos contêm uma propriedade do sistema chamada **connectionModuleId** , mas as mensagens enviadas por dispositivos downstream não. Você pode usar a cláusula WHERE da rota para excluir todas as mensagens que contêm essa propriedade do sistema. 

A rota abaixo é um exemplo que enviaria mensagens de qualquer dispositivo downstream para um módulo chamado `ai_insights`e, em seguida, de `ai_insights` para o Hub IoT.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Para obter mais informações sobre o roteamento de mensagens, consulte [implantar módulos e estabelecer rotas](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Habilitar operação offline estendida

A partir da [versão v 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do tempo de execução do IOT Edge, o dispositivo de gateway e os dispositivos downstream que se conectam a ele podem ser configurados para operação estendida offline. 

Com esse recurso, os módulos locais ou dispositivos downstream podem se autenticar novamente com o dispositivo IoT Edge conforme necessário e se comunicarem entre si usando mensagens e métodos mesmo quando estiverem desconectados do Hub IoT. Para obter mais informações, consulte [noções básicas sobre recursos offline estendidos para dispositivos IOT Edge, módulos e dispositivos filho](offline-capabilities.md).

Para habilitar recursos offline estendidos, você estabelece uma relação pai-filho entre um dispositivo IoT Edge gateway e dispositivos downstream que se conectarão a ele. Essas etapas são explicadas em mais detalhes em [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Passos seguintes

Agora que você tem um dispositivo IoT Edge funcionando como um gateway transparente, você precisa configurar seus dispositivos downstream para confiar no gateway e enviar mensagens para ele. Continue em para [autenticar um dispositivo downstream no Hub IOT do Azure](how-to-authenticate-downstream-device.md) para as próximas etapas em Configurando seu cenário de gateway transparente. 
