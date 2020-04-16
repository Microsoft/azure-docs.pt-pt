---
title: Dispositivos de fornecimento automática com DPS utilizando certificados X.509 - Borda Azure IoT [ Microsoft Docs
description: Utilize certificados X.509 para testar o fornecimento automático de dispositivos para o Azure IoT Edge com o Serviço de Provisionamento de Dispositivos
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5e968e578428a16a0005149a409986015a1fc5c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393759"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Criar e fornecer um dispositivo IoT Edge utilizando certificados X.509

Com o Serviço de Provisionamento de [Dispositivos Hub Azure IoT (DPS),](../iot-dps/index.yml)pode fornecer automaticamente dispositivos IoT Edge utilizando certificados X.509. Se não estiver familiarizado com o processo de fornecimento automático, reveja os [conceitos de fornecimento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

Este artigo mostra-lhe como criar uma inscrição no Serviço de Provisionamento de Dispositivos utilizando certificados X.509 num dispositivo IoT Edge com os seguintes passos:

* Gerar certificados e chaves.
* Crie uma inscrição individual para um dispositivo ou uma inscrição em grupo para um conjunto de dispositivos.
* Instale o tempo de execução do IoT Edge e registe o dispositivo com o IoT Hub.

A utilização de certificados X.509 como mecanismo de atestado é uma excelente forma de escalar a produção e simplificar o fornecimento de dispositivos. Normalmente, os certificados X.509 são organizados numa cadeia de fidedignidade de certificados. Começando com um certificado de raiz auto-assinado ou de confiança, cada certificado da cadeia assina o próximo certificado inferior. Este padrão cria uma cadeia de confiança delegada desde o certificado de raiz até cada certificado intermédio até ao certificado final de "folha" instalado num dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub ativo.
* Um dispositivo físico ou virtual para ser o dispositivo IoT Edge.
* A versão mais recente de [Git](https://git-scm.com/download/) instalada.
* Uma instância do Serviço de Provisionamento de Dispositivos IoT Hub em Azure, ligado ao seu hub IoT.
  * Se não tiver uma instância de Serviço de Provisionamento de Dispositivos, siga as instruções em [Configurar o DPS do Hub IoT](../iot-dps/quick-setup-auto-provision.md).
  * Depois de ter o Serviço de Provisionamento de Dispositivos em funcionamento, copie o valor do **ID Scope** a partir da página de visão geral. Utiliza este valor quando configura o tempo de execução do IoT Edge.

## <a name="generate-device-identity-certificates"></a>Gerar certificados de identidade do dispositivo

O certificado de identidade do dispositivo é um certificado de folha que se liga através de uma cadeia de fidedignidade de certificado ao certificado de autoridade de certificados X.509 (CA). O certificado de identidade do dispositivo deve ter o nome comum (CN) definido no ID do dispositivo que pretende que o dispositivo tenha no seu hub IoT.

Os certificados de identidade do dispositivo são utilizados apenas para o fornecimento do dispositivo IoT Edge e autenticação do dispositivo com hub Azure IoT. Não estão a assinar certificados, ao contrário dos certificados CA que o dispositivo IoT Edge apresenta a módulos ou dispositivos de folhas para verificação. Para mais informações, consulte o detalhe de utilização do [certificado Azure IoT Edge](iot-edge-certs.md).

Depois de criar o certificado de identidade do dispositivo, deve ter dois ficheiros: um ficheiro .cer ou .pem que contenha a parte pública do certificado e um ficheiro .cer ou .pem com a chave privada do certificado. Se planeia utilizar a inscrição em grupo em DPS, também precisa da parte pública de um certificado ca intermédio ou raiz na mesma cadeia de fidedignidade de certificado.

Precisa dos seguintes ficheiros para configurar o fornecimento automático com X.509:

* O certificado de identidade do dispositivo e o seu certificado de chave privada. O certificado de identidade do dispositivo é enviado para DPS se criar uma matrícula individual. A chave privada é passada para o tempo de execução de IoT Edge.
* Um certificado em cadeia completo, que deve ter, pelo menos, a identidade do dispositivo e os certificados intermédios nele. O certificado de corrente completo é passado para o tempo de funcionação do IoT Edge.
* Um certificado de AC intermédio ou raiz da cadeia de certificados de confiança. Este certificado é enviado para dPS se criar uma inscrição em grupo.

### <a name="use-test-certificates"></a>Utilizar certificados de ensaio

Se não tiver uma autoridade de certificado disponível para criar novos certificados de identidade e quiser experimentar este cenário, o repositório de git Azure IoT Edge contém scripts que pode usar para gerar certificados de teste. Estes certificados destinam-se apenas a testes de desenvolvimento e não devem ser utilizados na produção.

Para criar certificados de teste, siga os passos em [Create demo certificates para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md). Complete as duas secções necessárias para configurar os scripts de geração de certificados e criar um certificado ca raiz. Em seguida, siga os passos para criar um certificado de identidade do dispositivo. Quando terminar, deve ter o seguinte par de certificados e chave:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Precisa de ambos os certificados no dispositivo IoT Edge. Se vai utilizar a inscrição individual no DPS, então irá carregar o ficheiro .cert.pem. Se vai utilizar a inscrição em grupo em DPS, então também precisa de um certificado ca intermédio ou raiz na mesma cadeia de fidedignidade para carregar. Se estiver a usar certs de `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` demonstração, use o certificado para inscrição em grupo.

## <a name="create-a-dps-individual-enrollment"></a>Criar uma inscrição individual dPS

Utilize os seus certificados e chaves gerados para criar uma inscrição individual em DPS para um único dispositivo IoT Edge. As matrículas individuais pegam na parte pública do certificado de identidade de um dispositivo e correspondem ao certificado do dispositivo.

Se procura fornecer vários dispositivos IoT Edge, siga os passos na secção seguinte, [Crie uma inscrição](#create-a-dps-group-enrollment)em grupo DPS .

Ao criar uma inscrição em DPS, tem a oportunidade de declarar um **Dispositivo Inicial Twin State**. No dispositivo twin, pode definir etiquetas para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas](how-to-deploy-monitor.md).

Para obter mais informações sobre as inscrições no Serviço de Provisionamento de Dispositivos, consulte [como gerir as matrículas](../iot-dps/how-to-manage-enrollments.md)do dispositivo.

   > [!TIP]
   > No Azure CLI, pode criar uma [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) ou um grupo de [inscrição](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e utilizar a bandeira **ativada pela borda** para especificar que um dispositivo, ou grupo de dispositivos, é um dispositivo IoT Edge.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Definições,** **selecione Gerir as matrículas**.

1. **Selecione Adicionar inscrição individual** e, em seguida, completar os seguintes passos para configurar a inscrição:  

   * **Mecanismo**: Selecione **X.509**.

   * **Certificado Primário .pem ou ficheiro .cer**: Faça o upload do ficheiro público a partir do certificado de identidade do dispositivo. Se usou os scripts para gerar um certificado de teste, escolha o seguinte ficheiro:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub Device ID**: Forneça uma identificação para o seu dispositivo, se quiser. Pode utilizar iDs do dispositivo para direcionar um dispositivo individual para a implementação do módulo. Se não fornecer um ID do dispositivo, o nome comum (CN) no certificado X.509 é utilizado.

   * **Dispositivo IoT Edge**: Selecione **True** para declarar que a inscrição é para um dispositivo IoT Edge.

   * **Selecione os hubs IoT**a que este dispositivo pode ser atribuído : Escolha o hub IoT ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   * **Dispositivo Inicial Twin State**: Adicione um valor de etiqueta a ser adicionado ao dispositivo twin, se quiser. Pode utilizar etiquetas para grupos-alvo de dispositivos para a implementação automática. Por exemplo:

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

Agora que existe uma inscrição para este dispositivo, o tempo de funcionamento do IoT Edge pode fornecer automaticamente o dispositivo durante a instalação. Continue a instalar a secção de tempo de [execução IoT Edge](#install-the-iot-edge-runtime) para configurar o seu dispositivo IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Criar uma inscrição em grupo DPS

Utilize os seus certificados e chaves gerados para criar uma inscrição em grupo em DPS para vários dispositivos IoT Edge. As inscrições em grupo utilizam um certificado ca intermédio ou raiz da cadeia de fidedignidade utilizada para gerar os certificados de identidade individuais do dispositivo.

Se pretende fornecer um único dispositivo IoT Edge, siga os passos na secção anterior, [Crie uma inscrição individual DPS](#create-a-dps-individual-enrollment).

Ao criar uma inscrição em DPS, tem a oportunidade de declarar um **Dispositivo Inicial Twin State**. No dispositivo twin, pode definir etiquetas para agrupar dispositivos por qualquer métrica que necessite na sua solução, como região, ambiente, localização ou tipo de dispositivo. Estas etiquetas são utilizadas para criar [implementações automáticas](how-to-deploy-monitor.md).

### <a name="verify-your-root-certificate"></a>Verifique o seu certificado de raiz

Ao criar um grupo de inscrições, tem a opção de utilizar um certificado verificado. Pode verificar um certificado com DPS provando que tem a propriedade do certificado raiz. Para mais informações, consulte [Como fazer a prova de posse dos certificados X.509 CA](../iot-dps/how-to-verify-certificates.md).

1. No [portal Azure,](https://portal.azure.com)navegue para a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Selecione **Certificados** do menu à esquerda.

1. Selecione **Adicionar** para adicionar um novo certificado.

1. Introduza um nome amigável para o seu certificado e, em seguida, navegue para o ficheiro .cer ou .pem que representa a parte pública do seu certificado X.509.

   Se estiver a usar os certificados `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` de demonstração, faça o certificado.

1. Selecione **Guardar**.

1. O seu certificado deve agora estar listado na página de **Certificados.** Selecione-o para abrir os dados do certificado.

1. Selecione **Generate Verification Code** e, em seguida, copie o código gerado.

1. Quer tenha trazido o seu próprio certificado CA ou esteja a utilizar os certificados de demonstração, pode utilizar a ferramenta de verificação fornecida no repositório IoT Edge para verificar o comprovativo de posse. A ferramenta de verificação utiliza o seu certificado CA para assinar um novo certificado que tem o código de verificação fornecido como nome do assunto.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na mesma página de dados do certificado no portal Azure, carregue o certificado de verificação recentemente gerado.

1. Selecione **Verificar**.

### <a name="create-enrollment-group"></a>Criar grupo de inscrições

Para obter mais informações sobre as inscrições no Serviço de Provisionamento de Dispositivos, consulte [como gerir as matrículas](../iot-dps/how-to-manage-enrollments.md)do dispositivo.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua instância de Serviço de Provisionamento de Dispositivos IoT Hub.

1. Em **Definições,** **selecione Gerir as matrículas**.

1. Selecione Adicionar o grupo de **inscrição** e, em seguida, completar os seguintes passos para configurar a inscrição:

   * **Nome do grupo**: Forneça um nome memorável para esta inscrição em grupo.

   * **Tipo de atestado**: Selecione **Certificado**.

   * **Dispositivo IoT Edge**: Selecione **True**. Para uma inscrição em grupo, todos os dispositivos devem ser dispositivos IoT Edge ou nenhum deles pode ser.

   * **Tipo de Certificado**: Selecione **Certificado CA** se tiver um certificado CA verificado armazenado com DPS, ou **Certificado Intermédio** se pretender fazer o upload de um novo ficheiro apenas para esta inscrição.

   * **Certificado primário**: Se escolher o certificado CA na última secção, escolha o seu certificado na lista de abandono. Se escolheu um certificado intermédio, faça o upload do ficheiro público de um certificado de AC na cadeia de fidedignidade que foi usada para gerar os certificados de identidade do dispositivo.

   * **Selecione os hubs IoT**a que este dispositivo pode ser atribuído : Escolha o hub IoT ligado ao qual pretende ligar o seu dispositivo. Pode escolher vários hubs e o dispositivo será atribuído a um deles de acordo com a política de atribuição selecionada.

   * **Dispositivo Inicial Twin State**: Adicione um valor de etiqueta a ser adicionado ao dispositivo twin, se quiser. Pode utilizar etiquetas para grupos-alvo de dispositivos para a implementação automática. Por exemplo:

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

Agora que existe uma inscrição para este dispositivo, o tempo de funcionamento do IoT Edge pode fornecer automaticamente o dispositivo durante a instalação. Continue na secção seguinte para configurar o seu dispositivo IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instale o tempo de execução do IoT Edge

O runtime do IoT Edge é implementado em todos os dispositivos do IoT Edge. Os seus componentes funcionam em contentores e permitem-lhe colocar recipientes adicionais no dispositivo para que possa executar código na borda.

O fornecimento x.509 com DPS só é suportado na versão 1.0.9 do IoT Edge ou mais recente.

Necessitará das seguintes informações ao fornecer o seu dispositivo:

* O valor do Âmbito de **identificação** dPS. Pode recuperar este valor a partir da página geral da sua instância DPS no portal Azure.
* O ficheiro da cadeia de certificadode identidade do dispositivo no dispositivo.
* O ficheiro de chave de identidade do dispositivo no dispositivo.
* Um ID de registo opcional (retirado do nome comum no certificado de identidade do dispositivo se não for fornecido).

### <a name="linux-device"></a>Dispositivo Linux

Utilize o seguinte link para instalar o tempo de execução do Azure IoT Edge no seu dispositivo, utilizando os comandos adequados para a arquitetura do seu dispositivo. Quando chegar à secção de configuração do daemon de segurança, configure o tempo de funcionamento do IoT Edge para o fornecimento automático x.509, não manual. Deverá ter todas as informações e ficheiros de certificados de que necessita depois de concluir as secções anteriores deste artigo.

[Instale o tempo de execução do Azure IoT Edge em Linux](how-to-install-iot-edge-linux.md)

Quando adicionar o certificado X.509 e as informações chave ao ficheiro config.yaml, os caminhos devem ser fornecidos como URIs de ficheiro. Por exemplo:

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

A secção do ficheiro de configuração para o fornecimento automático X.509 é a seguinte:

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

Substitua os valores `identity_cert` `identity_pk` do espaço reservado para, `scope_id`com o ID de âmbito da sua instância DPS, e as URIs para a cadeia cert e localizações de ficheiros chave no seu dispositivo. Forneça `registration_id` um para o dispositivo, se quiser, ou deixe esta linha comentada para registar o dispositivo com o nome NC do certificado de identidade.

Reinicie sempre o daemon de segurança depois de atualizar o ficheiro config.yaml.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Dispositivo Windows

Instale o tempo de execução do IoT Edge no dispositivo para o qual gerou a cadeia de certificados de identidade e a chave de identidade. Você vai configurar o tempo de funcionamento do IoT Edge para o fornecimento automático, não manual.

Para obter informações mais detalhadas sobre a instalação do IoT Edge no Windows, incluindo pré-requisitos e instruções para tarefas como gerir contentores e atualizar o IoT Edge, consulte Instalar o tempo de execução do [Edge Azure IoT no Windows](how-to-install-iot-edge-windows.md).

1. Abra uma janela PowerShell no modo administrador. Certifique-se de que utiliza uma sessão AMD64 de PowerShell ao instalar o IoT Edge, e não o PowerShell (x86).

1. O comando **Deploy-IoTEdge** verifica se a sua máquina Windows está numa versão suportada, liga a funcionalidade de contentores e, em seguida, descarrega o tempo de execução moby e o tempo de execução do IoT Edge. O comando não se aplica à utilização de recipientes Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. Neste ponto, os dispositivos IoT Core podem reiniciar automaticamente. Outros dispositivos do Windows 10 ou Do Windows Server podem pedir-lhe para reiniciar. Em caso afirmativo, reinicie o seu dispositivo agora. Uma vez que o seu dispositivo esteja pronto, execute a PowerShell como administrador novamente.

1. O comando **Initialize-IoTEdge** configura o tempo de execução do IoT Edge na sua máquina. O comando não se aplica ao `-Dps` fornecimento manual, a menos que utilize a bandeira para utilizar o fornecimento automático.

   Substitua os valores `{identity cert chain path}`do `{identity key path}` espaço reservado para, `{scope_id}`e pelos valores apropriados da sua instância DPS e dos caminhos de ficheiro no seu dispositivo. Se quiser especificar o ID `-RegistrationId {registration_id}` de registo, inclua também, substituindo o espaço reservado conforme apropriado.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >O ficheiro config.yaml armazena o seu certificado e informações chave como URIs de ficheiro. No entanto, o comando Initialize-IoTEdge trata deste passo de formatação para si, para que possa fornecer o caminho absoluto para o certificado e ficheiros chave no seu dispositivo.

## <a name="verify-successful-installation"></a>Verificar instalação bem sucedida

Se o tempo de funcionamento tiver começado com sucesso, pode entrar no seu Hub IoT e começar a implantar módulos IoT Edge para o seu dispositivo.

Pode verificar se foi utilizada a inscrição individual que criou no Serviço de Provisionamento de Dispositivos. Navegue para a sua instância de Serviço de Provisionamento de Dispositivos no portal Azure. Abra os detalhes das inscrições para a inscrição individual que criou. Note que o estado da inscrição está **atribuído** e o ID do dispositivo está listado.

Utilize os seguintes comandos no seu dispositivo para verificar se o tempo de execução foi instalado e começou com sucesso.

### <a name="linux-device"></a>Dispositivo Linux

Verifique o estado do serviço IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine os registos de serviço.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista de módulos de execução.

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

Lista de módulos de execução.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passos seguintes

O processo de inscrição do Serviço de Provisionamento de Dispositivos permite-lhe definir as etiquetas duplas de identificação do dispositivo e dispositivo ao mesmo tempo que disponibiliza o novo dispositivo. Pode utilizar esses valores para direcionar dispositivos ou grupos individuais de dispositivos utilizando a gestão automática do dispositivo. Aprenda a [implantar e monitorizar os módulos IoT Edge em escala utilizando o portal Azure](how-to-deploy-monitor.md) ou utilizando o [Azure CLI](how-to-deploy-monitor-cli.md).
