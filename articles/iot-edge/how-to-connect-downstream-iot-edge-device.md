---
title: Ligar dispositivos IoT Edge a jusante - Azure IoT Edge / Microsoft Docs
description: Como configurar um dispositivo IoT Edge para ligar aos dispositivos de gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 37c237cdaf6c0d4f766d4b2e39c10e3e96215463
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187838"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Ligue um dispositivo IoT Edge a jusante a um gateway Azure IoT Edge (Pré-visualização)

Este artigo fornece instruções para estabelecer uma ligação fidedigna entre um gateway IoT Edge e um dispositivo IoT Edge a jusante.

>[!NOTE]
>Esta funcionalidade requer a versão 1.2 do IoT Edge, que se encontra em pré-visualização pública, a executar contentores Linux.

Num cenário de gateway, um dispositivo IoT Edge pode ser simultaneamente um gateway e um dispositivo a jusante. Vários gateways IoT Edge podem ser em camadas para criar uma hierarquia de dispositivos. Os dispositivos a jusante (ou criança) podem autenticar e enviar ou receber mensagens através do seu dispositivo gateway (ou pai).

Existem duas configurações diferentes para dispositivos IoT Edge numa hierarquia de gateway, e este artigo endereça ambos. O primeiro é o dispositivo IoT Edge **de camada superior.** Quando vários dispositivos IoT Edge estão a ligar-se entre si, qualquer dispositivo que não tenha um dispositivo principal mas que se conecte diretamente ao IoT Hub é considerado como estando na camada superior. Este dispositivo é responsável pelo manuseamento de pedidos de todos os dispositivos abaixo do mesmo. A outra configuração aplica-se a qualquer dispositivo IoT Edge numa **camada inferior** da hierarquia. Estes dispositivos podem ser uma porta de entrada para outros dispositivos IoT e IoT Edge a jusante, mas também precisam de encaminhar quaisquer comunicações através dos seus próprios dispositivos-mãe.

Algumas arquiteturas de rede requerem que apenas o dispositivo IoT Edge superior numa hierarquia possa ligar-se à nuvem. Nesta configuração, todos os dispositivos IoT Edge em camadas inferiores de uma hierarquia só podem comunicar com o seu dispositivo gateway (ou pai) e quaisquer dispositivos a jusante (ou criança).

Todos os passos deste artigo baseiam-se nos que estão em [Configure um dispositivo IoT Edge para funcionar como um gateway transparente](how-to-create-transparent-gateway.md), que configura um dispositivo IoT Edge para ser uma porta de entrada para dispositivos IoT a jusante. Os mesmos passos básicos aplicam-se a todos os cenários de gateway:

* **Autenticação**: Crie identidades IoT Hub para todos os dispositivos da hierarquia gateway.
* **Autorização**: Configurar a relação pai/filho no IoT Hub para autorizar os dispositivos infantis a ligarem-se ao dispositivo principal como se ligassem ao IoT Hub.
* **Descoberta gateway**: Certifique-se de que o dispositivo infantil pode encontrar o seu dispositivo-mãe na rede local.
* **Ligação segura**: Estabeleça uma ligação segura com certificados fidedignos que fazem parte da mesma cadeia.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT gratuito ou padrão.
* Pelo menos dois **dispositivos IoT Edge**, um para ser o dispositivo de camada superior e um ou mais dispositivos de camada inferior. Se não tiver dispositivos IoT Edge disponíveis, pode [executar Azure IoT Edge em máquinas virtuais Ubuntu](how-to-install-iot-edge-ubuntuvm.md).
* Se utilizar o CLI Azure para criar e gerir dispositivos, tenha o Azure CLI v2.3.1 com a extensão Azure IoT v0.10.6 ou superior instalada.

Este artigo fornece passos e opções detalhados para ajudá-lo a criar a hierarquia de gateway certa para o seu cenário. Para obter um tutorial guiado, consulte [Criar uma hierarquia de dispositivos IoT Edge utilizando gateways](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Criar uma hierarquia de gateway

Você cria uma hierarquia de gateway IoT Edge definindo relações pai/filho para os dispositivos IoT Edge no cenário. Pode configurar um dispositivo-mãe quando criar uma nova identidade do dispositivo, ou pode gerir os pais e os filhos de uma identidade de dispositivo existente.

O passo da criação de relações entre pais e filhos autoriza os dispositivos infantis a ligarem-se ao dispositivo principal, tal como se ligariam ao IoT Hub.

Apenas os dispositivos IoT Edge podem ser dispositivos-mãe, mas tanto os dispositivos IoT Edge como os dispositivos IoT podem ser crianças. Um pai pode ter muitos filhos, mas uma criança só pode ter um pai. Uma hierarquia de gateway é criada acorrentando os conjuntos entre pais e filhos para que o filho de um dispositivo seja o pai de outro.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal Azure, pode gerir a relação pai/filho quando criar novas identidades do dispositivo, ou editando dispositivos existentes.

Quando cria um novo dispositivo IoT Edge, tem a opção de escolher dispositivos de pais e crianças da lista de dispositivos IoT Edge existentes naquele centro.

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu hub IoT.
1. Selecione **IoT Edge** do menu de navegação.
1. **Selecione Adicione um dispositivo IoT Edge**.
1. Juntamente com a definição do ID do dispositivo e as definições de autenticação, pode **definir um dispositivo-mãe** ou **escolher dispositivos para crianças**.
1. Escolha o dispositivo ou dispositivos que deseja como pai ou criança.

Também pode criar ou gerir relações entre pais e filhos para dispositivos existentes.

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu hub IoT.
1. Selecione **IoT Edge** do menu de navegação.
1. Selecione o dispositivo que pretende gerir a partir da lista de **dispositivos IoT Edge**.
1. Selecione **Definir um dispositivo-mãe** ou **gerir dispositivos para crianças**.
1. Adicione ou remova qualquer dispositivo de pai ou criança.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A extensão [azure-iot](/cli/azure/ext/azure-iot) para o CLI Azure fornece comandos para gerir os seus recursos IoT. Pode gerir a relação pai/filho dos dispositivos IoT e IoT Edge quando criar novas identidades do dispositivo ou editando dispositivos existentes.

O conjunto de comandos de identidade do [dispositivo az iot hub](/cli/azure/ext/azure-iot/iot/hub/device-identity) permite-lhe gerir as relações pai/filho para um determinado dispositivo.

O `create` comando inclui parâmetros para adicionar dispositivos infantis e definir um dispositivo principal no momento da criação do dispositivo.

Comandos adicionais de identidade do dispositivo, incluindo `add-children` , e ou e , `list-children` `remove-children` `get-parent` `set-parent` permitem-lhe gerir as relações pai/filho para dispositivos existentes.

---

## <a name="prepare-certificates"></a>Preparar certificados

Uma cadeia consistente de certificados deve ser instalada através de dispositivos na mesma hierarquia de gateway para estabelecer uma comunicação segura entre si. Todos os dispositivos da hierarquia, seja um dispositivo IoT Edge ou um dispositivo de folha IoT, precisam de uma cópia do mesmo certificado de CA raiz. Cada dispositivo IoT Edge na hierarquia utiliza então esse certificado de CA raiz como raiz para o seu certificado ca dispositivo.

Com esta configuração, cada dispositivo IoT Edge a jusante ou dispositivo de folha IoT pode verificar a identidade do seu progenitor, verificando se o edgeHub a que se ligam tem um certificado de servidor assinado pelo certificado de CA de raiz partilhada.

<!-- TODO: certificate graphic -->

Criar os seguintes certificados:

* Um **certificado de CA de raiz,** que é o certificado partilhado mais alto para todos os dispositivos numa determinada hierarquia de gateway. Este certificado está instalado em todos os dispositivos.
* Quaisquer **certificados intermédios** que pretenda incluir na cadeia de certificados de raiz.
* Um **certificado ca dispositivo** e a sua chave **privada,** gerado pelos certificados raiz e intermédio. Precisa de um certificado CA de dispositivo único para cada dispositivo IoT Edge na hierarquia do gateway.

>[!NOTE]
>Atualmente, uma limitação no libiothsmo impede a utilização de certificados que expirem em ou após 1 de janeiro de 2038.

Você pode usar uma autoridade de certificado auto-assinado ou comprar um de uma autoridade de certificado comercial de confiança como Baltimore, Verisign, Digicert ou GlobalSign.

Se não tiver os seus próprios certificados para utilizar, pode [criar certificados de demonstração para testar as funcionalidades do dispositivo IoT Edge](how-to-create-test-certificates.md). Siga os passos nesse artigo para criar um conjunto de certificados de raiz e intermédio, em seguida, para criar certificados CA do dispositivo IoT Edge para cada um dos seus dispositivos.

## <a name="configure-iot-edge-on-devices"></a>Configure ioT Edge em dispositivos

Os passos para configurar o IoT Edge como um gateway são muito semelhantes aos passos para configurar o IoT Edge como um dispositivo a jusante.

Para permitir a descoberta do gateway, todos os dispositivos de gateway IoT Edge precisam de ser configurados com um **nome de hospedeiro** que os seus dispositivos infantis utilizarão para o encontrar na rede local. Todos os dispositivos IoT Edge a jusante precisam de ser configurados com um **parent_hostname** a que se possa ligar. Se um único dispositivo IoT Edge for simultaneamente um pai e um dispositivo infantil, precisa de ambos os parâmetros.

Para permitir ligações seguras, todos os dispositivos IoT Edge num cenário de gateway precisam de ser configurados com um certificado CA de dispositivo único e uma cópia do certificado de CA raiz partilhado por todos os dispositivos da hierarquia gateway.

Já deve ter o IoT Edge instalado no seu dispositivo. Caso contrário, siga os passos para [instalar o tempo de funcionamento do Azure IoT Edge](how-to-install-iot-edge.md) e, em seguida, forneça o seu dispositivo com [autenticação simétrica de teclas](how-to-manual-provision-symmetric-key.md) ou [autenticação de certificado X.509](how-to-manual-provision-x509.md).

Os passos nesta secção referem o **certificado de CA raiz** e o certificado de CA do dispositivo e a chave **privada** que foram discutidos anteriormente neste artigo. Se criou esses certificados num dispositivo diferente, disponibilize-os neste dispositivo. Pode transferir os ficheiros fisicamente, como com uma unidade USB, com um serviço como [o Azure Key Vault,](../key-vault/general/overview.md)ou com uma função como [cópia de ficheiro Secure](https://www.ssh.com/ssh/scp/).

Utilize os seguintes passos para configurar o IoT Edge no seu dispositivo.

No Linux, certifique-se de que o **iotedge** do utilizador leu permissões para o diretório que detém os certificados e chaves.

1. Instale o **certificado de CA raiz** neste dispositivo IoT Edge.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Atualize a loja de certificados.

   ```bash
   sudo update-ca-certificates
   ```

   Este comando deve ser descoduado que um certificado foi adicionado a /etc/ssl/certs.

1. Abra o ficheiro de configuração do daemon de segurança IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Encontre a secção **de certificados** no ficheiro config.yaml. Atualize os três campos de certificados para indicar os seus certificados. Forneça os caminhos URI de ficheiro, que tomam o formato `file:///<path>/<filename>` .

   * **device_ca_cert**: Arquivar caminho URI para o certificado CA do dispositivo exclusivo deste dispositivo.
   * **device_ca_pk**: Arquivar caminho URI para a chave privada CA do dispositivo única neste dispositivo.
   * **trusted_ca_certs**: Arquivar o caminho URI para o certificado de CA raiz partilhado por todos os dispositivos da hierarquia gateway.

1. Encontre o parâmetro **do nome hospedeiro** no ficheiro config.yaml. Atualize o nome de anfitrião para ser o nome de domínio totalmente qualificado (FQDN) ou o endereço IP do dispositivo IoT Edge.

   O valor deste parâmetro é o que os dispositivos a jusante usarão para se ligarem a este gateway. O nome de anfitrião toma o nome da máquina por padrão, mas o endereço FQDN ou IP é necessário para ligar dispositivos a jusante.

   Use um nome de anfitrião com menos de 64 caracteres, que é o limite de caracteres para um nome comum de certificado de servidor.

   Seja consistente com o padrão de nome hospedeiro através de uma hierarquia de gateway. Utilize fQDNs ou endereços IP, mas não ambos.

1. **Se este dispositivo for um dispositivo para crianças,** encontre o **parâmetro parent_hostname.** Atualize o campo **parent_hostname** para ser o endereço FQDN ou IP do dispositivo principal, correspondendo o que foi fornecido como nome de anfitrião no ficheiro config.yaml do progenitor.

1. Enquanto esta funcionalidade estiver em pré-visualização pública, é necessário configurar o seu dispositivo IoT Edge para utilizar a versão de pré-visualização pública do agente IoT Edge quando este começar a trabalhar.

   Encontre a secção yaml do **agente** e atualize o valor da imagem para a imagem de pré-visualização pública:

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Guarde `Ctrl+O` () e feche `Ctrl+X` () o ficheiro config.yaml.

1. Se já utilizou outros certificados para ioT Edge antes, elimine os ficheiros nos dois seguintes diretórios para se certificar de que os seus novos certificados são aplicados:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Reinicie o serviço IoT Edge para aplicar as suas alterações.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Verifique se há erros na configuração.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >A ferramenta de verificação IoT Edge utiliza um recipiente para efetuar algumas das verificações de diagnóstico. Se pretender utilizar esta ferramenta em dispositivos IoT Edge a jusante, certifique-se de que podem aceder `mcr.microsoft.com/azureiotedge-diagnostics:latest` ou ter a imagem do recipiente no registo privado do seu contentor.

## <a name="configure-runtime-modules-for-public-preview"></a>Configurar módulos de tempo de execução para pré-visualização pública

Enquanto esta funcionalidade se encontra em pré-visualização pública, é necessário configurar o seu dispositivo IoT Edge para utilizar as versões de pré-visualização públicas dos módulos de tempo de funcionamento IoT Edge. A secção anterior fornece passos para configurar o edgeAgent no arranque. Também é necessário configurar os módulos de tempo de execução nas implementações do seu dispositivo.

1. Configure o módulo edgeHub para utilizar a imagem de pré-visualização do público: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

1. Configure as seguintes variáveis ambientais para o módulo edgeHub:

   | Name | Valor |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Configure o módulo edgeAgent para utilizar a imagem de pré-visualização do público: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` .

## <a name="network-isolate-downstream-devices"></a>Rede isola dispositivos a jusante

Os passos até agora neste artigo configuram dispositivos IoT Edge como um gateway ou um dispositivo a jusante, e criam uma ligação confiável entre eles. O dispositivo gateway lida com interações entre o dispositivo a jusante e o IoT Hub, incluindo autenticação e encaminhamento de mensagens. Os módulos implantados para dispositivos IoT Edge a jusante ainda podem criar as suas próprias ligações aos serviços na nuvem.

Algumas arquiteturas de rede, como as que seguem a norma ISA-95, procuram minimizar o número de ligações à Internet. Nesses cenários, pode configurar dispositivos IoT Edge a jusante sem conectividade direta na Internet. Para além de encaminhar as comunicações do IoT Hub através do seu dispositivo gateway, os dispositivos IoT Edge a jusante podem contar com o dispositivo gateway para todas as ligações na nuvem.

Esta configuração de rede requer que apenas o dispositivo IoT Edge na camada superior de uma hierarquia gateway tenha ligações diretas à nuvem. Os dispositivos IoT Edge nas camadas inferiores só podem comunicar com o seu dispositivo principal ou com quaisquer dispositivos infantis. Os módulos especiais nos dispositivos gateway permitem este cenário, incluindo:

* O **módulo de procuração API** é necessário em qualquer gateway IoT Edge que tenha outro dispositivo IoT Edge abaixo. Isso significa que deve estar em *todas as camadas* de uma hierarquia de gateway, exceto na camada inferior. Este módulo utiliza um proxy invertido [nginx](https://nginx.org) para encaminhar dados HTTP através de camadas de rede sobre uma única porta. É altamente configurável através das suas variáveis de módulo twin e ambiente, por isso pode ser ajustado de acordo com os requisitos do seu cenário gateway.

* O **módulo de registo Docker** pode ser implantado na porta de entrada IoT Edge na camada *superior* de uma hierarquia de gateway. Este módulo é responsável pela recuperação e caching de imagens de contentores em nome de todos os dispositivos IoT Edge em camadas inferiores. A alternativa para implantar este módulo na camada superior é utilizar um registo local, ou carregar manualmente imagens de contentores nos dispositivos e definir a política de puxar o módulo para **nunca**.

* O **Azure Blob Storage on IoT Edge** pode ser implantado no gateway IoT Edge na *camada superior* de uma hierarquia de gateway. Este módulo é responsável pelo upload de bolhas em nome de todos os dispositivos IoT Edge em camadas mais baixas. A capacidade de carregar bolhas também permite uma funcionalidade útil de resolução de problemas para dispositivos IoT Edge em camadas mais baixas, como o upload de registo de módulos e o upload do pacote de suporte.

### <a name="network-configuration"></a>Configuração de rede

Para cada dispositivo de gateway na camada superior, os operadores de rede devem:

* Fornecer um endereço IP estático ou nome de domínio totalmente qualificado (FQDN).
* Autorizar comunicações de saída deste endereço IP para o seu nome de anfitrião Azure IoT Hub sobre as portas 443 (HTTPS) e 5671 (AMQP).
* Autorizar comunicações de saída deste endereço IP para o seu nome de anfitrião do Registo do Contentor Azure sobre a porta 443 (HTTPS).

  O módulo de procuração API só pode manusear ligações a um registo de contentores de cada vez. Recomendamos ter todas as imagens do contentor, incluindo as imagens públicas fornecidas pelo Microsoft Container Registry (mcr.microsoft.com), armazenadas no seu registo privado de contentores.

Para cada dispositivo de gateway numa camada inferior, os operadores de rede devem:

* Forneça um endereço IP estático.
* Autorizar comunicações de saída deste endereço IP para o endereço IP do portal principal sobre as portas 443 (HTTPS) e 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Implementar módulos para dispositivos de camada superior

O dispositivo IoT Edge na camada superior de uma hierarquia gateway tem um conjunto de módulos necessários que devem ser implantados no mesmo, além de quaisquer módulos de carga de trabalho que possa utilizar no dispositivo.

O módulo de procuração API foi projetado para ser personalizado para lidar com os cenários de gateway mais comuns. Este artigo fornece e exemplo para configurar os módulos numa configuração básica. Consulte o [módulo de procuração API para o seu cenário de hierarquia gateway](how-to-configure-api-proxy-module.md) para obter informações e exemplos mais detalhados.

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu hub IoT.
1. Selecione **IoT Edge** do menu de navegação.
1. Selecione o dispositivo de camada superior que está a configurar a partir da lista de **dispositivos IoT Edge**.
1. Selecione **Definir módulos**.
1. Na secção de **módulos IoT Edge,** selecione **Adicionar** e escolha o **módulo Marketplace.**
1. Procure e selecione o módulo **Proxy IoT Edge API.**
1. Selecione o nome do módulo de procuração API da lista de módulos implantados e atualize as seguintes definições do módulo:
   1. No separador **De variáveis Ambiente,** atualize o valor da **NGINX_DEFAULT_PORT** para `443` .
   1. No **separador De opções** de Criação de Recipientes, atualize as ligações portuárias à porta de referência 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Estas alterações configuram o módulo de procuração API para ouvir na porta 443. Para evitar colisões de encadernação portuária, é necessário configurar o módulo edgeHub para não ouvir na porta 443. Em vez disso, o módulo de procuração API irá encaminhar qualquer tráfego edgeHub na porta 443.

1. Selecione **Definições de tempo de execução** e encontre o módulo edgeHub criar opções. Elimine a ligação portuária para a porta 443, deixando as ligações para as portas 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. **Selecione Guardar** para guardar as alterações nas definições de tempo de execução.
1. **Selecione Adicionar** novamente e, em seguida, escolher **o módulo IoT Edge**.
1. Forneça os seguintes valores para adicionar o módulo de registo Docker à sua implantação:
   1. **Nome do módulo IoT Edge**: `registry`
   1. No **separador definições** do Módulo, **Image URI**: `registry:latest`
   1. No separador **de variáveis ambiente,** adicione as seguintes variáveis ambientais:

      * **Nome**: `REGISTRY_PROXY_REMOTEURL` **Valor**: O URL para o registo do contentor para o mesmo, pretende que este módulo de registo mapeie. Por exemplo, `https://myregistry.azurecr`.

        O módulo de registo só pode mapear um registo de contentores, pelo que recomendamos ter todas as imagens de contentores num único registo privado de contentores.

      * **Nome**: `REGISTRY_PROXY_USERNAME` **Valor**: Nome de utilizador para autenticar no registo do contentor.

      * **Nome**: `REGISTRY_PROXY_PASSWORD` **Valor**: Palavra-passe para autenticar no registo do contentor.

   1. No **separador De opções do Recipiente,** cole:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. **Selecione Adicionar** para adicionar o módulo à implementação.
1. Selecione **Seguinte: Rotas** para ir para o próximo passo.
1. Para permitir que mensagens de dispositivos em nuvem de dispositivos a jusante cheguem ao IoT Hub, inclua uma rota que transmite todas as mensagens para o IoT Hub. Por exemplo:
    1. **Nome:**`Route`
    1. **Valor:**`FROM /messages/* INTO $upstream`
1. Selecione **Review + criar** para ir ao passo final.
1. Selecione **Criar** para implementar no seu dispositivo.

### <a name="deploy-modules-to-lower-layer-devices"></a>Implementar módulos para dispositivos de camada inferior

Os dispositivos IoT Edge em camadas inferiores de uma hierarquia gateway têm um módulo necessário que deve ser implantado para eles, além de quaisquer módulos de carga de trabalho que possa executar no dispositivo.

#### <a name="route-container-image-pulls"></a>A imagem do contentor da rota puxa

Antes de discutir o módulo proxy necessário para dispositivos IoT Edge em hierarquias gateway, é importante entender como os dispositivos IoT Edge em camadas inferiores obtêm as suas imagens de módulos.

Se os dispositivos de camada inferior não conseguirem ligar-se à nuvem, mas pretende que retirem as imagens do módulo como de costume, então o dispositivo de camada superior da hierarquia gateway deve estar configurado para lidar com estes pedidos. O dispositivo de camada superior precisa de executar um módulo **de registo** Docker que está mapeado para o registo do seu contentor. Em seguida, configurar o módulo de procuração API para encaminhar os pedidos de contentores para o mesmo. Estes detalhes são discutidos nas secções anteriores deste artigo. Nesta configuração, os dispositivos de camada inferior não devem apontar para os registos de contentores em nuvem, mas para o registo que funciona na camada superior.

Por exemplo, em vez de `mcr.microsoft.com/azureiotedge-api-proxy:latest` chamar, os dispositivos de camada inferior devem ligar `$upstream:443/azureiotedge-api-proxy:latest` .

O **parâmetro $upstream** aponta para o progenitor de um dispositivo de camada inferior, pelo que o pedido irá percorrer todas as camadas até chegar à camada superior que tem um recipiente de encaminhamento ambiental proxy para o módulo de registo. A `:443` porta deste exemplo deve ser substituída por qualquer porta que o módulo de procuração API no dispositivo-mãe esteja a ouvir.

O módulo de procuração API só pode encaminhar-se para um módulo de registo, e cada módulo de registo só pode mapear para um registo de contentores. Por isso, todas as imagens que os dispositivos de camada inferior precisam de puxar devem ser armazenadas num único registo de contentores.

Se não quiser que dispositivos de camada inferior faça pedidos de puxar módulos através de uma hierarquia de gateway, outra opção é gerir uma solução de registo local. Ou, empurre as imagens do módulo para os dispositivos antes de criar implementações e, em seguida, coloque a **imagemPullPolicy** para **nunca**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Bootstrap o agente IoT Edge

O agente IoT Edge é o primeiro componente de tempo de execução a iniciar-se em qualquer dispositivo IoT Edge. Você precisa ter certeza de que qualquer dispositivo IoT Edge a jusante pode aceder à imagem do módulo edgeAgent quando eles iniciarem, e então eles podem aceder a implementações e iniciar as restantes imagens do módulo.

Quando você entrar no ficheiro config.yaml em um dispositivo IoT Edge para fornecer as suas informações de autenticação, certificados e nome de anfitrião, também atualize a imagem do recipiente edgeAgent.

Se o dispositivo de gateway de nível superior estiver configurado para lidar com os pedidos de imagem do contentor, `mcr.microsoft.com` substitua-o pelo nome de hospedeiro dos pais e pela porta de escuta de procuração API. No manifesto de implantação, pode ser utilizado `$upstream` como atalho, mas isso requer o módulo edgeHub para manusear o encaminhamento e esse módulo ainda não começou. Por exemplo:

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc2"
    auth: {}
```

Se estiver a utilizar um registo de contentores local ou a fornecer manualmente as imagens do recipiente no dispositivo, atualize o ficheiro config.yaml em conformidade.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Configurar o tempo de execução e implementar o módulo de procuração

O **módulo de procuração API** é necessário para encaminhar todas as comunicações entre a nuvem e quaisquer dispositivos IoT Edge a jusante. Um dispositivo IoT Edge na camada inferior da hierarquia, sem dispositivos IoT Edge a jusante, não necessita deste módulo.

O módulo de procuração API foi projetado para ser personalizado para lidar com os cenários de gateway mais comuns. Este artigo toca brevemente nos passos para configurar os módulos numa configuração básica. Consulte o [módulo de procuração API para o seu cenário de hierarquia gateway](how-to-configure-api-proxy-module.md) para obter informações e exemplos mais detalhados.

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu hub IoT.
1. Selecione **IoT Edge** do menu de navegação.
1. Selecione o dispositivo de camada inferior que está a configurar a partir da lista de **dispositivos IoT Edge**.
1. Selecione **Definir módulos**.
1. Na secção de **módulos IoT Edge,** selecione **Adicionar** e escolha o **módulo Marketplace.**
1. Procure e selecione o módulo **Proxy IoT Edge API.**
1. Selecione o nome do módulo de procuração API da lista de módulos implantados e atualize as seguintes definições do módulo:
   1. No **separador definições** do Módulo, atualize o valor do **Image URI**. Substitua `mcr.microsoft.com` por `$upstream:443`.
   1. No separador **De variáveis Ambiente,** atualize o valor da **NGINX_DEFAULT_PORT** para `443` .
   1. No **separador De opções** de Criação de Recipientes, atualize as ligações portuárias à porta de referência 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Estas alterações configuram o módulo de procuração API para ouvir na porta 443. Para evitar colisões de encadernação portuária, é necessário configurar o módulo edgeHub para não ouvir na porta 443. Em vez disso, o módulo de procuração API irá encaminhar qualquer tráfego edgeHub na porta 443.

1. Selecione **definições de tempo de execução**.
1. Atualize as definições do módulo EdgeHub:

   1. No campo **Imagem,** `mcr.microsoft.com` substitua-o por `$upstream:443` .
   1. No campo **de opções Criar,** elimine a ligação portuária para a porta 443, deixando as ligações para as portas 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Atualizar as definições do módulo edgeAgent:
   1. No campo **Imagem,** `mcr.microsoft.com` substitua-o por `$upstream:443` .

1. **Selecione Guardar** para guardar as alterações nas definições de tempo de execução.
1. Selecione **Seguinte: Rotas** para ir para o próximo passo.
1. Para permitir que mensagens de dispositivos em nuvem de dispositivos a jusante cheguem ao IoT Hub, inclua uma rota que transmite todas as mensagens para `$upstream` . O parâmetro a montante aponta para o dispositivo-mãe no caso de dispositivos de camada inferior. Por exemplo:
    1. **Nome:**`Route`
    1. **Valor:**`FROM /messages/* INTO $upstream`
1. Selecione **Review + criar** para ir ao passo final.
1. Selecione **Criar** para implementar no seu dispositivo.

## <a name="next-steps"></a>Passos seguintes

[De que forma um dispositivo IoT Edge pode ser utilizado como gateway](iot-edge-as-gateway.md)

[Configurar o módulo de procuração da API para o seu cenário de hierarquia gateway](how-to-configure-api-proxy-module.md)