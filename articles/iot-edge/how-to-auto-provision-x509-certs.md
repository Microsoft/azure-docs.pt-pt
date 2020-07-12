---
title: Fornecimento automaticamente de dispositivos com DPS utilizando certificados X.509 - Azure IoT Edge / Microsoft Docs
description: Utilize certificados X.509 para testar o provisionamento automático do dispositivo para a borda IoT Azure com o Serviço de Provisionamento de Dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c54690645286a4fceb3fd786d85652b1cf77d7aa
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260037"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Criar e providenciar um dispositivo IoT Edge utilizando certificados X.509

Com o Serviço de Provisionamento de [Dispositivos Azure IoT Hub (DPS),](../iot-dps/index.yml)pode providenciar automaticamente dispositivos IoT Edge utilizando certificados X.509. Se não estiver familiarizado com o processo de provisão automática, reveja os [conceitos de provisionamento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra-lhe como criar uma inscrição do Serviço de Provisionamento de Dispositivos utilizando certificados X.509 num dispositivo IoT Edge com os seguintes passos:

* Gerar certificados e chaves.
* Crie uma inscrição individual para um dispositivo ou uma inscrição em grupo para um conjunto de dispositivos.
* Instale o tempo de funcionaamento do IoT Edge e registe o dispositivo com o IoT Hub.

A utilização de certificados X.509 como mecanismo de atestado é uma excelente forma de escalar a produção e simplificar o fornecimento de dispositivos. Normalmente, os certificados X.509 estão dispostos numa cadeia de certificados de confiança. Começando com um certificado de raiz auto-assinado ou fidedigno, cada certificado na cadeia assina o próximo certificado inferior. Este padrão cria uma cadeia de confiança delegada a partir do certificado raiz para baixo através de cada certificado intermédio para o certificado final "folha" instalado num dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT ativo.
* Um dispositivo físico ou virtual para ser o dispositivo IoT Edge.
* A versão mais recente do [Git](https://git-scm.com/download/) instalada.
* Um caso do serviço de provisionamento de dispositivos IoT Hub em Azure, ligado ao seu hub IoT.
  * Se não tiver uma instância de Serviço de Provisionamento de Dispositivos, siga as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).
  * Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** na página de visão geral. Utilize este valor quando configurar o tempo de execução IoT Edge.

## <a name="generate-device-identity-certificates"></a>Gerar certificados de identidade de dispositivo

O certificado de identidade do dispositivo é um certificado de folha que se liga através de uma cadeia de certificados de confiança ao certificado superior da Autoridade de Certificados X.509 (CA). O certificado de identidade do dispositivo deve ter o seu nome comum (CN) definido para o ID do dispositivo que pretende que o dispositivo tenha no seu hub IoT.

Os certificados de identidade do dispositivo são utilizados apenas para o fornecimento do dispositivo IoT Edge e para autenticar o dispositivo com o Azure IoT Hub. Não estão a assinar certificados, ao contrário dos certificados de CA que o dispositivo IoT Edge apresenta a módulos ou dispositivos de folha para verificação. Para obter mais informações, consulte [o certificado Azure IoT Edge .](iot-edge-certs.md)

Depois de criar o certificado de identidade do dispositivo, deverá ter dois ficheiros: um ficheiro .cer ou .pem que contenha a parte pública do certificado, e um ficheiro .cer ou .pem com a chave privada do certificado. Se planeia utilizar a inscrição em grupo em DPS, também precisa da parte pública de um certificado de CA intermédio ou raiz na mesma cadeia de certificados de confiança.

Precisa dos seguintes ficheiros para configurar o provisionamento automático com X.509:

* O certificado de identidade do dispositivo e o seu certificado chave privado. O certificado de identidade do dispositivo é enviado para DPS se criar uma inscrição individual. A chave privada é passada para o tempo de execução IoT Edge.
* Um certificado em cadeia completo, que deve ter, pelo menos, a identidade do dispositivo e os certificados intermédios nele. O certificado de cadeia completo é passado para o tempo de execução IoT Edge.
* Um certificado de CA intermédio ou raiz da cadeia de certificados de confiança. Este certificado é enviado para DPS se criar uma inscrição em grupo.

> [!NOTE]
> Atualmente, uma limitação no libiothsmo impede a utilização de certificados que expirem em ou após 1 de janeiro de 2050.

### <a name="use-test-certificates"></a>Utilizar certificados de teste

Se não tiver uma autoridade de certificado disponível para criar novos certificados de identidade e quiser experimentar este cenário, o repositório de git Azure IoT Edge contém scripts que pode usar para gerar certificados de teste. Estes certificados destinam-se apenas a ensaios de desenvolvimento e não devem ser utilizados na produção.

Para criar certificados de teste, siga os passos na [Criação de certificados de demonstração para testar as características do dispositivo IoT Edge](how-to-create-test-certificates.md). Preencha as duas secções necessárias para configurar os scripts de geração de certificados e criar um certificado de CA raiz. Em seguida, siga os passos para criar um certificado de identidade do dispositivo. Quando terminar, deve ter o seguinte cadeia de certificados e par de chaves:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Precisa destes dois certificados no dispositivo IoT Edge. Se vai utilizar a inscrição individual em DPS, então irá carregar o ficheiro .cert.pem. Se vai utilizar a inscrição em grupo em DPS, também precisa de um certificado de CA intermédio ou raiz na mesma cadeia de certificados de confiança para carregar. Se estiver a usar certificados de demonstração, utilize o `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certificado para inscrição em grupo.

## <a name="create-a-dps-individual-enrollment"></a>Criar uma inscrição individual DPS

Utilize os certificados e chaves gerados para criar uma inscrição individual em DPS para um único dispositivo IoT Edge. As inscrições individuais levam a parte pública do certificado de identidade de um dispositivo e correspondem ao certificado do dispositivo.

Se estiver à procura de vários dispositivos IoT Edge, siga os passos na secção seguinte, [Crie uma inscrição](#create-a-dps-group-enrollment)do grupo DPS .

Quando cria uma inscrição em DPS, tem a oportunidade de declarar um **Estado Gémeo de Dispositivo Inicial.** No twin do dispositivo, pode definir tags para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas.](how-to-deploy-at-scale.md)

Para obter mais informações sobre as inscrições no Serviço de Provisionamento de [Dispositivos, consulte Como gerir as matrículas do dispositivo.](../iot-dps/how-to-manage-enrollments.md)

   > [!TIP]
   > No CLI Azure, pode criar uma [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e utilizar a bandeira ativada por **arestas** para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Definições**, **selecione Gerir as inscrições**.

1. **Selecione Adicionar inscrição individual** e, em seguida, completar os seguintes passos para configurar a inscrição:  

   * **Mecanismo**: Selecione **X.509**.

   * **Certificado Primário .pem ou ficheiro .cer**: Faça o upload do ficheiro público a partir do certificado de identidade do dispositivo. Se utilizar os scripts para gerar um certificado de teste, escolha o seguinte ficheiro:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub Device ID**: Forneça uma identificação para o seu dispositivo, se quiser. Pode utilizar iDs do dispositivo para direcionar um dispositivo individual para a implementação do módulo. Se não fornecer um ID do dispositivo, é utilizado o nome comum (CN) no certificado X.509.

   * **Dispositivo IoT Edge**: Selecione **True** para declarar que a inscrição é para um dispositivo IoT Edge.

   * **Selecione os hubs IoT a que este dispositivo pode ser atribuído:** Escolha o hub IoT ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   * **Estado duplo do dispositivo inicial**: Adicione um valor de etiqueta a adicionar ao dispositivo gémeo, se quiser. Pode utilizar tags para direcionar grupos de dispositivos para a implementação automática. Por exemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selecione **Guardar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução IoT Edge pode automaticamente abastecer o dispositivo durante a instalação. Continue até à [secção de tempo de funcionaamento IoT Edge](#install-the-iot-edge-runtime) para configurar o seu dispositivo IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Criar uma inscrição no grupo DPS

Utilize os certificados e chaves gerados para criar uma inscrição em grupo em DPS para vários dispositivos IoT Edge. As inscrições em grupo utilizam um certificado de CA intermédio ou raiz da cadeia de certificados de confiança utilizada para gerar os certificados de identidade individuais do dispositivo.

Se pretender obter um único dispositivo IoT Edge, siga os passos na secção anterior, [Crie uma inscrição individual DPS](#create-a-dps-individual-enrollment).

Quando cria uma inscrição em DPS, tem a oportunidade de declarar um **Estado Gémeo de Dispositivo Inicial.** No twin do dispositivo, pode definir tags para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas.](how-to-deploy-at-scale.md)

### <a name="verify-your-root-certificate"></a>Verifique o seu certificado de raiz

Quando cria um grupo de inscrições, tem a opção de utilizar um certificado verificado. Pode verificar um certificado com DPS provando que tem a propriedade do certificado raiz. Para obter mais informações, consulte [Como fazer comprovativo de posse para certificados X.509 CA](../iot-dps/how-to-verify-certificates.md).

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Selecione **Certificados** do menu à esquerda.

1. **Selecione Adicionar** para adicionar um novo certificado.

1. Introduza um nome amigável para o seu certificado e, em seguida, navegue no ficheiro .cer ou .pem que representa a parte pública do seu certificado X.509.

   Se estiver a usar os certificados de demonstração, carrema o `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificado.

1. Selecione **Guardar**.

1. O seu certificado deve agora ser listado na página **de Certificados.** Selecione-o para abrir os detalhes do certificado.

1. Selecione **Código de Verificação de Geração** e, em seguida, copie o código gerado.

1. Quer tenha trazido o seu próprio certificado de AC ou esteja a utilizar os certificados de demonstração, pode utilizar a ferramenta de verificação fornecida no repositório IoT Edge para verificar a prova de posse. A ferramenta de verificação utiliza o seu certificado de CA para assinar um novo certificado que tem como nome o nome do assunto o código de verificação fornecido.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na página de detalhes do mesmo certificado no portal Azure, faça o upload do certificado de verificação recentemente gerado.

1. Selecione **Verificar**.

### <a name="create-enrollment-group"></a>Criar grupo de inscrições

Para obter mais informações sobre as inscrições no Serviço de Provisionamento de [Dispositivos, consulte Como gerir as matrículas do dispositivo.](../iot-dps/how-to-manage-enrollments.md)

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Definições**, **selecione Gerir as inscrições**.

1. **Selecione Adicionar grupo de inscrições** e, em seguida, completar os seguintes passos para configurar a inscrição:

   * **Nome do**grupo : Forneça um nome memorável para esta inscrição em grupo.

   * **Tipo de atestado**: Selecione **Certificado**.

   * **Dispositivo IoT Edge**: Selecione **True**. Para uma inscrição em grupo, todos os dispositivos devem ser dispositivos IoT Edge ou nenhum deles pode ser.

   * **Tipo de Certificado**: Selecione **o Certificado CA** se tiver um certificado de CA verificado armazenado com DPS ou Certificado **Intermédio** se pretender enviar um novo ficheiro apenas para esta inscrição.

   * **Certificado primário**: Se escolher o certificado de CA na última secção, escolha o seu certificado na lista de entrega. Se escolheu o certificado intermédio, faça o upload do ficheiro público a partir de um certificado de AC na cadeia de certificados de confiança que foi usado para gerar os certificados de identidade do dispositivo.

   * **Selecione os hubs IoT a que este dispositivo pode ser atribuído:** Escolha o hub IoT ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   * **Estado duplo do dispositivo inicial**: Adicione um valor de etiqueta a adicionar ao dispositivo gémeo, se quiser. Pode utilizar tags para direcionar grupos de dispositivos para a implementação automática. Por exemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selecione **Guardar**.

Agora que existe uma inscrição para este dispositivo, o tempo de execução IoT Edge pode automaticamente abastecer o dispositivo durante a instalação. Continue até à secção seguinte para configurar o seu dispositivo IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda.

O fornecimento de X.509 com DPS só é suportado na versão 1.0.9 ou mais recente do IoT Edge.

Necessitará das seguintes informações ao providenciar o seu dispositivo:

* O valor do **Âmbito de Identificação** DPS. Pode recuperar este valor a partir da página geral da sua instância DPS no portal Azure.
* O ficheiro da cadeia de certificados de identidade do dispositivo no dispositivo.
* O ficheiro da chave de identidade do dispositivo no dispositivo.
* Um ID de registo opcional (retirado do nome comum no certificado de identidade do dispositivo se não for fornecido).

### <a name="linux-device"></a>Dispositivo Linux

Utilize o seguinte link para instalar o tempo de funcionamento do Azure IoT Edge no seu dispositivo, utilizando os comandos adequados à arquitetura do seu dispositivo. Quando chegar à secção de configuração do daemon de segurança, configure o tempo de execução IoT Edge para x.509 automático, não manual, provisionamento. Deverá ter todas as informações e ficheiros de certificados de que necessita após o preenchimento das secções anteriores deste artigo.

[Instale o tempo de execução Azure IoT Edge no Linux](how-to-install-iot-edge-linux.md)

Quando adicionar o certificado X.509 e informações-chave ao ficheiro config.yaml, os caminhos devem ser fornecidos como URIs de ficheiro. Por exemplo:

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

A secção no ficheiro de configuração para o provisionamento automático X.509 é a seguinte:

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Substitua os valores do espaço reservado para `scope_id` , `identity_cert` com o `identity_pk` ID de âmbito da sua instância DPS, e os URIs para a cadeia cert e as localizações do ficheiro chave no seu dispositivo. Forneça um `registration_id` para o dispositivo se quiser, ou deixe esta linha comentada para registar o dispositivo com o nome CN do certificado de identidade.

Reinicie sempre o daemon de segurança depois de atualizar o ficheiro config.yaml.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Dispositivo Windows

Instale o tempo de funcionaamento do IoT Edge no dispositivo para o qual gerou a cadeia de certificados de identidade e a chave de identidade. Irá configurar o tempo de funcionamento do IoT Edge para o fornecimento automático, não manual.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerir contentores e atualizar o IoT Edge, consulte [instalar o tempo de execução do IoT Edge Azure no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela PowerShell no modo de administrador. Certifique-se de que utiliza uma sessão AMD64 de PowerShell ao instalar ioT Edge, não PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a sua máquina Do Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução IoT Edge. O comando não permite a utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos Windows 10 ou Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie já o seu dispositivo. Assim que o seu dispositivo estiver pronto, volte a executar o PowerShell como administrador.

1. O comando **Initialize-IoTEdge** configura o tempo de funcionamento do IoT Edge na sua máquina. O comando não permite o provisionamento manual, a menos que utilize a bandeira para utilizar o `-Dps` provisionamento automático.

   Substitua os valores do espaço reservado para `{scope_id}` `{identity cert chain path}` , e pelos `{identity key path}` valores adequados da sua instância DPS e dos caminhos de ficheiro no seu dispositivo. Se quiser especificar o ID de registo, inclua `-RegistrationId {registration_id}` também, substituindo o espaço reservado conforme apropriado.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >O ficheiro config.yaml armazena o seu certificado e informações chave como URIs de ficheiro. No entanto, o comando Initialize-IoTEdge lida com este passo de formatação para si, para que possa fornecer o caminho absoluto para o certificado e ficheiros chave no seu dispositivo.

## <a name="verify-successful-installation"></a>Verifique a instalação bem sucedida

Se o tempo de execução tiver começado com sucesso, pode entrar no seu Hub IoT e começar a implantar módulos IoT Edge no seu dispositivo.

Pode verificar se foi utilizada a inscrição individual que criou no Serviço de Provisionamento de Dispositivos. Navegue para a sua instância de Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes das inscrições para a inscrição individual que criou. Note que o estado da inscrição é **atribuído** e o ID do dispositivo está listado.

Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de funcionamento foi instalado e começou com sucesso.

### <a name="linux-device"></a>Dispositivo Linux

Verifique o estado do serviço IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os registos de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Listar módulos de execução.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verifique o estado do serviço IoT Edge.

```powershell
Get-Service iotedge
```

Examine os registos de serviço.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Listar módulos de execução.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir o ID do dispositivo e as etiquetas gémeas do dispositivo ao mesmo tempo que fornece o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos individuais ou grupos de dispositivos utilizando a gestão automática do dispositivo. Aprenda a [implementar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-at-scale.md) ou utilizando o [Azure CLI](how-to-deploy-cli-at-scale.md).
