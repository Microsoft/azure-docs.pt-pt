---
title: Módulo de procuração API configurar - Azure IoT Edge | Microsoft Docs
description: Saiba como personalizar o módulo de procuração API para as hierarquias de gateway IoT Edge.
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
ms.openlocfilehash: f55c3a1f699f8a087eb97eaba347a3f21c124cc9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307321"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Configurar o módulo de procuração API para o seu cenário de hierarquia gateway (Pré-visualização)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

O módulo de procuração API permite que os dispositivos IoT Edge enviem pedidos HTTP através de gateways em vez de fazer ligações diretas aos serviços na nuvem. Este artigo percorre as opções de configuração para que possa personalizar o módulo para suportar os requisitos da hierarquia do gateway.

>[!NOTE]
>Esta funcionalidade requer a versão 1.2 do IoT Edge, que se encontra em pré-visualização pública, a executar contentores Linux.

Em algumas arquiteturas de rede, os dispositivos IoT Edge por trás dos gateways não têm acesso direto à nuvem. Quaisquer módulos que tentem ligar-se aos serviços na nuvem falharão. O módulo de procuração API suporta dispositivos IoT Edge a jusante nesta configuração, reencaminhando as ligações do módulo para passar pelas camadas de uma hierarquia de gateway, em vez disso. Fornece uma forma segura para os clientes comunicarem a múltiplos serviços através de HTTPS sem fazer túneis, mas terminando as ligações em cada camada. O módulo de procuração API funciona como um módulo proxy entre os dispositivos IoT Edge numa hierarquia de gateway até chegarem ao dispositivo IoT Edge na camada superior. Nessa altura, os serviços que executam no dispositivo IoT Edge de camada superior lidam com estes pedidos, e o módulo de procuração API fornece todo o tráfego HTTP dos serviços locais para a nuvem através de uma única porta.

O módulo de procuração API pode permitir muitos cenários para as hierarquias de gateway, incluindo permitir que dispositivos de camada inferior puxem imagens de contentores ou empurrem bolhas para o armazenamento. A configuração das regras de procuração define como os dados são encaminhados. Este artigo discute várias opções de configuração comuns.

## <a name="deploy-the-proxy-module"></a>Implementar o módulo proxy

O módulo de procuração API está disponível no Microsoft Container Registry (MCR): `mcr.microsoft.com/azureiotedge-api-proxy:1.0` .

Também pode implementar o módulo de procuração API diretamente do Azure Marketplace: [IoT Edge API Proxy](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Compreenda o módulo de procuração

O módulo de procuração API aproveita um proxy invertido nginx para encaminhar dados através de camadas de rede. Um proxy está incorporado no módulo, o que significa que a imagem do módulo precisa de suportar a configuração de procuração. Por exemplo, se o representante estiver a ouvir numa determinada porta, o módulo precisa de ter a porta aberta.

O proxy começa com um ficheiro de configuração padrão incorporado no módulo. Pode passar uma nova configuração para o módulo a partir da nuvem utilizando o seu [módulo twin](../iot-hub/iot-hub-devguide-module-twins.md). Além disso, pode utilizar variáveis ambientais para ligar ou desligar as definições de configuração no momento da implementação.

Este artigo foca-se primeiro no ficheiro de configuração predefinido e como utilizar variáveis ambientais para permitir as suas definições. Em seguida, discutimos a personalização do ficheiro de configuração no final.

### <a name="default-configuration"></a>Configuração predefinida

O módulo de procuração API vem com uma configuração padrão que suporta cenários comuns e permite a personalização. Pode controlar a configuração padrão através de variáveis ambientais do módulo.

Atualmente, as variáveis de ambiente padrão incluem:

| Variável de ambiente | Description |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Enumere todas as variáveis que pretende atualizar numa lista separada por vírgula. Este passo evita modificar acidentalmente as definições de configuração erradas.
| `NGINX_DEFAULT_PORT` | Muda a porta que o proxy nginx ouve. Se atualizar esta variável de ambiente, certifique-se de que a porta selecionada também está exposta no ficheiro de estiva do módulo e declarada como uma ligação de porta no manifesto de implantação.<br><br>O padrão é 443.<br><br>Quando implantado a partir do Azure Marketplace, a porta padrão é atualizada para 8000, para evitar conflitos com o módulo EdgeHub. Para mais informações, consulte [As portas abertas.](#minimize-open-ports) |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Endereço para encaminhar pedidos de docker. Modifique esta variável no dispositivo de camada superior para apontar para o módulo de registo.<br><br>O padrão é o nome de hospedeiro dos pais. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Endereço para encaminhar pedidos de registo de blob. Modifique esta variável no dispositivo de camada superior para apontar para o módulo de armazenamento de bolhas.<br><br>O padrão é o nome de hospedeiro dos pais. |

## <a name="minimize-open-ports"></a>Minimizar portas abertas

Para minimizar o número de portas abertas, o módulo de procuração API deve retransmitir todo o tráfego HTTPS (porta 443), incluindo o tráfego direcionado para o módulo edgeHub. O módulo de procuração API é configurado por padrão para redirecionar todo o tráfego edgeHub na porta 443.

Utilize os seguintes passos para configurar a sua implantação para minimizar as portas abertas:

1. Atualize as definições do módulo EdgeHub para não ligar na porta 443, caso contrário haverá conflitos de ligação à porta. Por predefinição, o módulo EdgeHub liga-se às portas 443, 5671 e 8883. Elimine a ligação da porta 443 e deixe as outras duas no lugar:

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

1. Configure o módulo de procuração API para ligar na porta 443.

   1. Valor definido da **variável ambiente NGINX_DEFAULT_PORT** para `443` .
   1. Atualize o recipiente criar opções para ligar na porta 443.

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

Se não precisar de minimizar as portas abertas, então pode deixar o módulo edgeHub utilizar a porta 443 e configurar o módulo de procuração API para ouvir noutra porta. Por exemplo, o módulo de procuração API pode ouvir na porta 8000, definindo o valor da variável **ambiente NGINX_DEFAULT_PORT** `8000` e criando uma ligação portuária para a porta 8000.

## <a name="enable-container-image-download"></a>Ativar o download de imagem de contentor

Um caso de utilização comum para o módulo de procuração API é permitir que os dispositivos IoT Edge em camadas inferiores puxem imagens de contentores. Este cenário utiliza o [módulo de registo Docker](https://hub.docker.com/_/registry) para recuperar imagens de contentores da nuvem e cache-las na camada superior. O proxy da API transmite todos os pedidos HTTPS para descarregar uma imagem de contentor das camadas inferiores para ser servido pelo módulo de registo na camada superior.

Este cenário requer que os dispositivos IoT Edge a jusante apitem para o nome de domínio `$upstream` seguido do número da porta do módulo API Proxy em vez do registo do contentor de uma imagem. Por exemplo: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Este caso de utilização é demonstrado no tutorial [Criar uma hierarquia de dispositivos IoT Edge utilizando gateways](tutorial-nested-iot-edge.md).

Configure os seguintes módulos na **camada superior:**

* Um módulo de registo Docker
  * Configurar o módulo com um nome memorável como *registo* e expor uma porta no módulo para receber pedidos.
  * Configure o módulo para mapear o registo do seu contentor.
* Um módulo de procuração API
  * Configure as seguintes variáveis ambientais:

    | Name | Valor |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | O nome do módulo de registo e a porta aberta. Por exemplo, `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy nginx ouve para pedidos de dispositivos a jusante. Por exemplo, `8000`. |

  * Configure as seguintes criaçõesOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configure o seguinte módulo em qualquer **camada inferior** para este cenário:

* Um módulo de procuração API
  * Configure as seguintes variáveis ambientais:

    | Name | Valor |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy nginx ouve para pedidos de dispositivos a jusante. Por exemplo, `8000`. |

  * Configure as seguintes criaçõesOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Ativar o upload do blob

Outro caso de utilização para o módulo de procuração API é permitir que os dispositivos IoT Edge em camadas inferiores carreguem bolhas. Este caso de utilização permite a funcionalidade de resolução de problemas em dispositivos de camada inferior, como carregar registos de módulos ou carregar o pacote de suporte.

Este cenário utiliza o [Azure Blob Storage no módulo IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) na camada superior para lidar com a criação de blob e carregar.

Configure os seguintes módulos na **camada superior:**

* Um Azure Blob Storage no módulo IoT Edge.
* Um módulo de procuração API
  * Configure as seguintes variáveis ambientais:

    | Name | Valor |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | O nome do módulo de armazenamento blob e porta aberta. Por exemplo, `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy nginx ouve para pedidos de dispositivos a jusante. Por exemplo, `8000`. |

  * Configure as seguintes criaçõesOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configure o seguinte módulo em qualquer **camada inferior** para este cenário:

* Um módulo de procuração API
  * Configure as seguintes variáveis ambientais:

    | Name | Valor |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | A porta que o proxy nginx ouve para pedidos de dispositivos a jusante. Por exemplo, `8000`. |

  * Configure as seguintes criaçõesOptions:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Utilize os seguintes passos para carregar o pacote de suporte ou o ficheiro de registo para o módulo de armazenamento de bolhas localizado na camada superior:

1. Crie um recipiente blob, utilizando o Azure Storage Explorer ou as APIs REST. Para obter mais informações, consulte [os dados da Loja no limite com o Azure Blob Storage no IoT Edge](how-to-store-data-blob.md).
1. Solicite um upload de registo ou suporte de acordo com os passos em [Registos de Recuperação a partir de implementações IoT Edge](how-to-retrieve-iot-edge-logs.md), mas use o nome de domínio `$upstream` e a porta de procuração aberta no lugar do endereço do módulo de armazenamento blob. Por exemplo:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Editar a configuração proxy

Um ficheiro de configuração predefinido está incorporado no módulo de procuração API, mas pode passar uma nova configuração para o módulo através da nuvem utilizando o módulo twin.

Quando escreve a sua própria configuração, ainda pode utilizar o ambiente para ajustar as definições por implementação. Utilize a seguinte sintaxe:

* Utilize `${MY_ENVIRONMENT_VARIABLE}` para recuperar o valor de uma variável ambiental.
* Utilize declarações condicionais para ligar ou desligar as definições com base no valor de uma variável ambiental:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Quando o módulo de procuração API analisa uma configuração de procuração, substitui primeiro todas as variáveis ambientais listadas nos `PROXY_CONFIG_ENV_VAR_LIST` seus valores fornecidos utilizando a substituição. Então, tudo entre um `#if_tag` par `#endif_tag` é substituído. Em seguida, o módulo fornece a configuração parsed para o proxy invertido nginx.

Para atualizar a configuração proxy de forma dinâmica, utilize os seguintes passos:

1. Escreva o seu ficheiro de configuração. Pode utilizar este modelo padrão como referência: [nginx_default_config.conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Copie o texto do ficheiro de configuração e converta-o na base64.
1. Cole o ficheiro de configuração codificado como o valor da `proxy_config` propriedade desejada no módulo twin.

   ![Pasta codificada ficheiro config como valor de proxy_config propriedade](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Passos seguintes

Utilize o módulo de procuração API para [ligar um dispositivo IoT Edge a jusante a um gateway Azure IoT Edge](how-to-connect-downstream-iot-edge-device.md).