---
title: Suporte azure IoT Hub para redes virtuais
description: Como usar o padrão de conectividade de redes virtuais com o IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: jlian
ms.openlocfilehash: c0d01ae6507864373a79282476846d6f96adf83b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231446"
---
# <a name="iot-hub-support-for-virtual-networks"></a>Suporte do IoT Hub para redes virtuais

Este artigo introduz o padrão de conectividade VNET e elabora sobre como configurar uma experiência de conectividade privada para um hub IoT através de um Azure VNET propriedade do cliente.

> [!NOTE]
> As características do IoT Hub descritas neste artigo estão atualmente disponíveis para centros IoT [criados com identidade](#create-an-iot-hub-with-managed-service-identity) de serviço gerida nas seguintes regiões: Leste dos EUA, Centro-Sul dos EUA e Us 2 Ocidental.


## <a name="introduction"></a>Introdução

Por padrão, os nomes de anfitriões do IoT Hub mapeiam para um ponto final público com um endereço IP publicamente repreensível através da Internet. Como mostra a ilustração abaixo, este ponto final do IoT Hub é partilhado entre os centros de propriedade de diferentes clientes e pode ser acedido por dispositivos IoT através de redes de grande área, bem como redes no local.

Várias funcionalidades do IoT Hub, incluindo [o encaminhamento de mensagens,](./iot-hub-devguide-messages-d2c.md)o upload de [ficheiros,](./iot-hub-devguide-file-upload.md)e a [importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel requerem igualmente conectividade do IoT Hub para um recurso Azure propriedade do cliente sobre o seu ponto final público. Como ilustrado abaixo, estes caminhos de conectividade constituem coletivamente o tráfego de saída do IoT Hub para os recursos do cliente.
![Ponto final do IoT Hub](./media/virtual-network-support/public-endpoint.png)


Por várias razões, os clientes podem querer restringir a conectividade aos seus recursos Azure (incluindo o IoT Hub) através de um VNET que possuem e operam. Estas razões incluem:

* Introduzindo camadas adicionais de segurança através do isolamento de nível de rede para o seu hub IoT, evitando a exposição à conectividade ao seu hub através da Internet pública.

* Possibilitando uma experiência de conectividade privada a partir dos seus ativos de rede no local, garantindo que os seus dados e tráfego são transmitidos diretamente para a rede de espinha dorsal Azure.

* Prevenção de ataques de exfiltração de redes sensíveis no local. 

* Seguindo padrões de conectividade estabelecidos em toda a Azure utilizando [pontos finais privados](../private-link/private-endpoint-overview.md).


Este artigo descreve como alcançar estes objetivos usando [pontos finais privados](../private-link/private-endpoint-overview.md) para a conectividade ingresso ao IoT Hub, como usando azure confiável primeiro serviços de serviços exceção para a conectividade de egress de IoT Hub para outros recursos Azure.


## <a name="ingress-connectivity-to-iot-hub-using-private-endpoints"></a>Conectividade ingressa ao IoT Hub usando pontos finais privados

Um ponto final privado é um endereço IP privado atribuído dentro de um VNET propriedade do cliente através do qual um recurso Azure é acessível. Ao ter um ponto final privado para o seu hub IoT, poderá permitir que os serviços que operam dentro do seu VNET cheguem ao IoT Hub sem exigir que o tráfego seja enviado para o ponto final público do IoT Hub. Da mesma forma, os dispositivos que operam no seu local podem utilizar a [Rede Privada Virtual (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou o [ExpressRoute](https://azure.microsoft.com/services/expressroute/) Private Peering para obter conectividade com o seu VNET em Azure e, posteriormente, para o seu Hub IoT (através do seu ponto final privado). Como resultado, os clientes que desejem restringir a conectividade aos pontos finais públicos do seu hub IoT (ou possivelmente bloqueá-lo completamente) podem atingir este objetivo usando as regras de [firewall Do IoT Hub,](./iot-hub-ip-filtering.md) mantendo a conectividade com o seu Hub usando o ponto final privado.

> [!NOTE]
> O principal foco desta configuração é para dispositivos dentro de uma rede no local. Esta configuração não é aconselhada para dispositivos implantados numa rede de grande área.

![Ponto final do IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Antes de prosseguir, certifique-se de que os seguintes pré-requisitos são cumpridos:

* O seu hub IoT deve ser aprovisionado com identidade de [serviço gerida.](#create-an-iot-hub-with-managed-service-identity)

* O seu centro ioT deve ser aprovisionado numa das [regiões apoiadas.](#regional-availability-private-endpoints)

* Você disponibilizou um Azure VNET com uma subnet na qual o ponto final privado será criado. Consulte a criação de [uma rede virtual utilizando o Azure CLI](../virtual-network/quick-create-cli.md) para obter mais detalhes.

* Para dispositivos que operam dentro das redes no local, instale a [Rede Privada Virtual (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) ou a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privada no seu Azure VNET.


### <a name="regional-availability-private-endpoints"></a>Disponibilidade regional (pontos finais privados)

Pontos finais privados apoiados no IoT Hub criado nas seguintes regiões:

* E.U.A. Leste

* E.U.A. Centro-Sul

* E.U.A.Oeste 2


### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Criar um ponto final privado para ioT hub ingress

Para criar um ponto final privado, siga estes passos:

1. Execute o seguinte comando Azure CLI para reregistar o fornecedor Azure IoT Hub com a sua subscrição:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Devices --wait --subscription  <subscription-name>
    ```

2. Navegue para o separador de **ligações de ponto final Privado** no seu portal IoT Hub (este separador só está disponível para em Hubs IoT nas [regiões suportadas),](#regional-availability-private-endpoints)e clique no **+** sinal para adicionar um novo ponto final privado.

3. Forneça a subscrição, o grupo de recursos, o nome e a região para criar o novo ponto final privado (idealmente, o ponto final privado deve ser criado na mesma região que o seu hub; consulte a secção de [disponibilidade regional](#regional-availability-private-endpoints) para mais detalhes).

4. Clique em **Seguinte: Recurso**, e forneça a subscrição para o seu recurso IoT Hub, e selecione **"Microsoft.Devices/IotHubs"** como tipo de recurso, o seu nome IoT Hub como **recurso,** e **iotHub** como subrecurso alvo.

5. Clique em **Seguinte: Configuração** e forneça a sua rede virtual e subnet para criar o ponto final privado. Selecione a opção de integração com a zona Privada DNS do Azure, se desejar.

6. Clique em **Seguinte: Tags,** e opcionalmente forneça quaisquer etiquetas para o seu recurso.

7. Clique em **Rever + crie** para criar o seu recurso de ponto final privado.


### <a name="pricing-private-endpoints"></a>Preços (pontos finais privados)

Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure](https://azure.microsoft.com/pricing/details/private-link).


## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Conectividade egress do IoT Hub para outros recursos Azure

O IoT Hub precisa de acesso ao seu armazenamento de blob Azure, centros de eventos, recursos de ônibus de serviço para [encaminhamento de mensagens,](./iot-hub-devguide-messages-d2c.md)upload de [ficheiros](./iot-hub-devguide-file-upload.md)e [importação/exportação](./iot-hub-bulk-identity-mgmt.md)de dispositivos a granel, que normalmente ocorre sobre o ponto final público dos recursos. Caso ligue a sua conta de armazenamento, centros de eventos ou recursos de ônibus de serviço a um VNET, a configuração aconselhada bloqueará a conectividade com o recurso por defeito. Consequentemente, isto irá impedir a funcionalidade do IoT Hub que requer acesso a esses recursos.

Para aliviar esta situação, você precisa permitir a conectividade desde o seu recurso IoT Hub até a sua conta de armazenamento, centros de eventos ou recursos de ônibus de serviço através da opção de **serviços fidedignos da primeira parte do Azure.**

Os pré-requisitos são os seguintes:

* O seu centro ioT deve ser aprovisionado numa das [regiões apoiadas.](#regional-availability-trusted-microsoft-first-party-services)

* O seu Hub IoT deve ser atribuído a uma identidade de serviço gerida no tempo de fornecimento do hub. Siga as instruções sobre como [criar um hub com identidade](#create-an-iot-hub-with-managed-service-identity)de serviço gerida .


### <a name="regional-availability-trusted-microsoft-first-party-services"></a>Disponibilidade regional (serviços de primeira parte confiáveis da Microsoft)

A Azure confiou na exceção dos serviços de primeira parte para contornar as restrições de firewall ao armazenamento do Azure, os centros de eventos e os recursos de autocarros de serviço só são suportados para os Hubs IoT nas seguintes regiões:

* E.U.A. Leste

* E.U.A. Centro-Sul

* E.U.A.Oeste 2


### <a name="pricing-trusted-microsoft-first-party-services"></a>Preços (serviços de primeira parte confiáveis da Microsoft)

A funcionalidade de exceção de serviços de primeira parte da Microsoft fidedigna é gratuita nos Hubs IoT nas [regiões apoiadas.](#regional-availability-trusted-microsoft-first-party-services) Os encargos com as contas de armazenamento, centros de eventos ou recursos de autocarros de serviço aplicam-se separadamente.


### <a name="create-an-iot-hub-with-managed-service-identity"></a>Criar um hub IoT com identidade de serviço gerida

Uma identidade de serviço gerida pode ser atribuída ao seu hub no tempo de fornecimento de recursos (esta funcionalidade não é atualmente suportada para os centros existentes), o que requer que o hub IoT utilize o TLS 1.2 como versão mínima. Para o efeito, tem de utilizar o modelo de recurso ARM abaixo:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "minTlsVersion": "1.2"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "minTlsVersion": "1.2"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Depois de substituir os `name`valores pelo seu recurso, `location` `SKU.name` e `SKU.tier`pode utilizar o Azure CLI para implantar o recurso num grupo de recursos existente utilizando:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Após a criação do recurso, pode recuperar a identidade de serviço gerida atribuída ao seu hub utilizando o Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

Assim que o IoT Hub com uma identidade de serviço gerida for provisionada, siga a secção correspondente para configurar pontos finais de encaminhamento para contas de [armazenamento,](#egress-connectivity-to-storage-account-endpoints-for-routing)centros de [eventos](#egress-connectivity-to-event-hubs-endpoints-for-routing)e recursos de autocarros de [serviço,](#egress-connectivity-to-service-bus-endpoints-for-routing) ou para configurar o upload de [ficheiros](#egress-connectivity-to-storage-accounts-for-file-upload) e a [importação/exportação](#egress-connectivity-to-storage-accounts-for-bulk-device-importexport)de dispositivos a granel.


### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Conectividade egress para pontos finais da conta de armazenamento para encaminhamento

O IoT Hub pode ser configurado para encaminhar mensagens para uma conta de armazenamento propriedade do cliente. Para permitir que a funcionalidade de encaminhamento aceda a uma conta de armazenamento enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade de serviço gerida (ver como criar um hub com identidade de [serviço gerida).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder à sua conta de armazenamento.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** sob a secção de atribuição de **funções.**

2. Selecione **Storage Blob Data Contributor** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como Atribuir acesso e selecione o nome de recursos do seu IoT Hub na lista de drop-down. **Assigning access to** Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**. Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de Mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **Armazenamento** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o recipiente que pretende utilizar no seu armazenamento blob, forneça codificação e formato de nome de ficheiro. Selecione **Sistema Atribuído** como o **tipo de Autenticação** ao seu ponto final de armazenamento. Clique no botão **Criar**.

Agora o seu ponto final de armazenamento personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de armazenamento, apesar das restrições de firewall. Agora pode usar este ponto final para estabelecer uma regra de encaminhamento.


### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Conectividade egress para os pontos finais dos hubs de eventos para o encaminhamento

O IoT Hub pode ser configurado para direcionar mensagens para um espaço de nome de centros de eventos do cliente. Para permitir que a funcionalidade de encaminhamento aceda a um recurso de hubs de eventos enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade de serviço gerida (ver como criar um hub com identidade de [serviço gerida).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder aos seus centros de eventos.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** dos seus hubs de eventos e clique em **Adicionar** sob a secção **de atribuição de funções.**

2. Selecione **Event Hubs Data Sender** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** nos seus hubs de eventos e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam aos centros de eventos**. Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de Mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **os cubos de evento seleção** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço de nome e instância dos seus centros de eventos e clique no botão **Criar.**

Agora, o seu ponto final de evento personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao recurso dos hubs do seu evento, apesar das restrições de firewall. Agora pode usar este ponto final para estabelecer uma regra de encaminhamento.


### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Conectividade egress para servir pontos finais de autocarro para encaminhamento

O IoT Hub pode ser configurado para encaminhar mensagens para um espaço de nome de autocarro de serviço do cliente. Para permitir que a funcionalidade de encaminhamento aceda a um recurso de ônibus de serviço enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade de serviço gerida (ver como criar um hub com identidade de [serviço gerida).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder ao seu ônibus de serviço.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** do seu autocarro de serviço e clique em **Adicionar** sob a secção **de atribuição de funções.**

2. Selecione **Service bus Data Sender** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** no seu autocarro de serviço e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir serviços fidedignos**da Microsoft para aceder a este autocarro de serviço . Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de Mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione a fila de **ônibus de serviço** ou o tópico do **ônibus de serviço** (conforme aplicável) como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço de nome do seu autocarro de serviço e a fila ou tópico (conforme aplicável). Clique no botão **Criar**.

Agora, o seu ponto final de ônibus de serviço personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de ônibus de serviço, apesar das restrições de firewall. Agora pode usar este ponto final para estabelecer uma regra de encaminhamento.


### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Conectividade egress para contas de armazenamento para upload de ficheiros

A funcionalidade de upload de ficheiros do IoT Hub permite que os dispositivos carreguem ficheiros para uma conta de armazenamento do cliente. Para permitir que o upload do ficheiro funcione, tanto os dispositivos como o IoT Hub precisam de ter conectividade com a conta de armazenamento. Se houver restrições de firewall na conta de armazenamento, os seus dispositivos precisam de utilizar qualquer um dos mecanismos da conta de armazenamento suportado (incluindo [pontos finais privados,](../private-link/create-private-endpoint-storage-portal.md) [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço ou [configuração de firewall direta)](../storage/common/storage-network-security.md)para obter conectividade. Da mesma forma, se houver restrições de firewall na conta de armazenamento, o IoT Hub precisa de ser configurado para aceder ao recurso de armazenamento através da exceção de serviços confiáveis da Microsoft. Para o efeito, o seu IoT Hub deve ter uma identidade de serviço gerida (ver como criar um hub com identidade de [serviço gerida).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder à sua conta de armazenamento.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** sob a secção de atribuição de **funções.**

2. Selecione **Storage Blob Data Contributor** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como Atribuir acesso e selecione o nome de recursos do seu IoT Hub na lista de drop-down. **Assigning access to** Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**. Clique no botão **Guardar**.

4. Na página de recursos do ioT Hub, navegue para **o separador de upload do Ficheiro.**

5. Na página que aparece, selecione o recipiente que pretende utilizar no seu armazenamento de blob, configure as definições de notificação de **Ficheiros**, **SAS TTL,** **Default TTL** e Contagem máxima de **entrega** conforme desejado. Selecione **Sistema Atribuído** como o **tipo de Autenticação** ao seu ponto final de armazenamento. Clique no botão **Criar**.

Agora, o seu ponto final de armazenamento para upload de ficheiros está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de armazenamento, apesar das restrições de firewall.


### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Conectividade egress com contas de armazenamento para importação/exportação de dispositivos a granel

O IoT Hub suporta a funcionalidade de informação sobre [a importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel de/para uma bolha de armazenamento fornecida pelo cliente. Para permitir que a função de importação/exportação a granel funcione, tanto os dispositivos como o IoT Hub precisam de ter conectividade com a conta de armazenamento.

Esta funcionalidade requer conectividade do IoT Hub para a conta de armazenamento. Para aceder a um recurso de ônibus de serviço enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade de serviço gerida (ver como criar um hub com identidade de [serviço gerida).](#create-an-iot-hub-with-managed-service-identity) Uma vez que uma identidade de serviço gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder ao seu ônibus de serviço.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** sob a secção de atribuição de **funções.**

2. Selecione **Storage Blob Data Contributor** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como Atribuir acesso e selecione o nome de recursos do seu IoT Hub na lista de drop-down. **Assigning access to** Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**. Clique no botão **Guardar**.

Pode agora utilizar os API's Do API's Azure IoT REST para [criar postos](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) de trabalho de importação para informações sobre a utilização da funcionalidade de importação/exportação a granel. Tenha em anote que `storageAuthenticationType="identityBased"` terá de fornecer `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` o corpo e utilização do seu pedido e como url's de entrada e saída da sua conta de armazenamento, respectivamente.


O Azure IoT Hub SDK também suporta esta funcionalidade no gestor de registo do cliente de serviço. O seguinte código de snippet mostra como iniciar um trabalho de importação ou exportação na utilização do C# SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```


Para utilizar esta versão limitada da região dos SDKs Azure IoT com suporte de rede virtual para C#, Java e Node.js:

1. Crie uma `EnableStorageIdentity` variável ambiental `1`nomeada e detetete o seu valor para .

2. Baixe o SDK: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Para Python, descarregue a nossa versão limitada do GitHub.

1. Navegue para a página de lançamento do [GitHub](https://aka.ms/vnetpythonsdk).

2. Faça o download do seguinte ficheiro, que encontrará na parte inferior da página de lançamento sob o cabeçalho nomeado **assets**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Abra um terminal e navegue para a pasta com o ficheiro descarregado.

4. Executar o seguinte comando para instalar o Serviço Python SDK com suporte para redes virtuais:
    > pip instalar ./azure_iot_hub-2.2.0_limited-py2.py3-none-any-whl


## <a name="next-steps"></a>Passos seguintes

Utilize os links abaixo para saber mais sobre as funcionalidades do IoT Hub:

* [Encaminhamento de mensagens](./iot-hub-devguide-messages-d2c.md)
* [Upload de ficheiros](./iot-hub-devguide-file-upload.md)
* [Importação/exportação de dispositivos a granel](./iot-hub-bulk-identity-mgmt.md) 
