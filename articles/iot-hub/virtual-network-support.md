---
title: Suporte Azure IoT Hub para redes virtuais
description: Como usar o padrão de conectividade das redes virtuais com o IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: jlian
ms.openlocfilehash: bf193859c140001def83a18ca7965d9cbd312b02
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907538"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Suporte ioT Hub para redes virtuais com Ligação Privada e Identidade Gerida

Por padrão, os nomes hostnames do IoT Hub mapeiam para um ponto final público com um endereço IP publicamente roteado através da internet. Diferentes clientes partilham este ponto final público do IoT Hub, e os dispositivos IoT em redes de ampla área e redes no local podem aceder-lhe.

![Ponto final público do IoT Hub](./media/virtual-network-support/public-endpoint.png)

As funcionalidades do IoT Hub, [incluindo o encaminhamento de mensagens,](./iot-hub-devguide-messages-d2c.md)o carregamento de [ficheiros](./iot-hub-devguide-file-upload.md)e [a importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel também requerem conectividade do IoT Hub para um recurso Azure propriedade do cliente sobre o seu ponto final público. Estes caminhos de conectividade constituem coletivamente o tráfego de saída do IoT Hub para os recursos do cliente.

É melhor restringir a conectividade aos seus recursos Azure (incluindo o IoT Hub) através de um VNet que possui e opera. Estas razões incluem:

* Introduzindo o isolamento da rede para o seu hub IoT, evitando a exposição à conectividade à internet pública.

* Permitir uma experiência de conectividade privada a partir dos seus ativos de rede no local, garantindo que os seus dados e tráfego são transmitidos diretamente para a rede de espinha dorsal Azure.

* Prevenir ataques de exfiltração de redes sensíveis no local. 

* Seguindo padrões de conectividade estabelecidos a nível Azure utilizando [pontos finais privados](../private-link/private-endpoint-overview.md).

Este artigo descreve como alcançar estes objetivos usando [o Azure Private Link](../private-link/private-link-overview.md) para a conectividade ingressa ao IoT Hub e usando a exceção de serviços confiáveis da Microsoft para a conectividade de Egress de IoT Hub para outros recursos Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Ingress conectividade ao IoT Hub usando Azure Private Link

Um ponto final privado é um endereço IP privado atribuído dentro de um VNet propriedade do cliente através do qual um recurso Azure é acessível. Através do Azure Private Link, pode configurar um ponto final privado para o seu hub IoT para permitir que os serviços dentro do seu VNet cheguem ao IoT Hub sem exigir que o tráfego seja enviado para o ponto final público do IoT Hub. Da mesma forma, os seus dispositivos no local podem utilizar [a Rede Privada Virtual (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para obter conectividade com o seu VNet e o seu IoT Hub (através do seu ponto final privado). Como resultado, pode restringir ou bloquear completamente a conectividade aos pontos finais públicos do seu hub IoT, utilizando [o filtro IP do Hub IoT](./iot-hub-ip-filtering.md) e [configurando o encaminhamento para não enviar quaisquer dados para o ponto final incorporado.](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint) Esta abordagem mantém a conectividade com o seu Hub utilizando o ponto final privado para dispositivos. O principal foco desta configuração é para dispositivos dentro de uma rede no local. Esta configuração não é aconselhada para dispositivos implantados numa rede de vasta área.

![Ponto final público do IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Antes de proceder, assegure-se de que sejam cumpridos os seguintes requisitos:

* [Criou um Azure VNet](../virtual-network/quick-create-portal.md) com uma sub-rede na qual o ponto final privado será criado.

* Para dispositivos que operam em redes no local, crie [rede privada virtual (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](https://azure.microsoft.com/services/expressroute/) private espreitando no seu Azure VNet.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Criar um ponto final privado para a entrada do IoT Hub

1. No portal Azure, **selecione Networking**, **Private endpoint connections**, e clique no **ponto final + Private**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Screenshot mostrando onde adicionar ponto final privado para IoT Hub":::

1. Forneça a subscrição, grupo de recursos, nome e região para criar o novo ponto final privado em. Idealmente, o ponto final privado deve ser criado na mesma região que o seu hub.

1. Clique **em seguida: Recurso**, e forneça a subscrição do seu recurso IoT Hub e selecione **"Microsoft.Devices/IotHubs"** como tipo de recurso, o seu nome IoT Hub como **recurso**, e **iotHub** como subresource alvo.

1. Clique **em seguida: Configurar** e fornecer a sua rede virtual e sub-rede para criar o ponto final privado dentro Selecione a opção de integração com a zona de DNS privada Azure, se desejar.

1. Clique **em Seguida: Tags**e, opcionalmente, forneça todas as etiquetas para o seu recurso.

1. Clique **em Rever + criar** para criar o seu recurso de ligação privada.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>Ponto final compatível com o Event Hub incorporado não suporta acesso sobre o ponto final privado

O [ponto final compatível com o Event Hub incorporado](iot-hub-devguide-messages-read-builtin.md) não suporta o acesso sobre o ponto final privado. Quando configurado, o ponto final privado de um hub IoT é apenas para a conectividade ingressa. O consumo de dados a partir de um ponto final compatível com o Event Hub só pode ser feito através da internet pública. 

O [filtro IP](iot-hub-ip-filtering.md) do IoT Hub também não controla o acesso do público ao ponto final incorporado. Para bloquear completamente o acesso da rede pública ao seu hub IoT, deve: 

1. Configure o acesso ao ponto final privado para o IoT Hub
1. Desligue o acesso à rede pública utilizando o filtro IP para bloquear todo o IP
1. Desligue o ponto final do Event Hub [incorporado, configurando o encaminhamento para não enviar dados para o mesmo](iot-hub-devguide-messages-d2c.md)
1. Desligue a rota de [retorno](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Configure a saída para outros recursos da Azure usando [o serviço de confiança da Microsoft](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Preços para Ligação Privada

Para obter detalhes sobre os preços, consulte [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Conectividade da Egress do IoT Hub para outros recursos Azure

O IoT Hub pode ligar-se ao seu armazenamento de blob Azure, centro de eventos, recursos de autocarro de serviço para [encaminhamento de mensagens,](./iot-hub-devguide-messages-d2c.md) [carregamento de ficheiros](./iot-hub-devguide-file-upload.md)e [importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel sobre o ponto final público dos recursos. Ligar o seu recurso a um VNet bloqueia a conectividade ao recurso por predefinição. Como resultado, esta configuração impede que o IoT Hub's trabalhe enviando dados para os seus recursos. Para corrigir este problema, ative a conectividade do seu recurso IoT Hub para a sua conta de armazenamento, centro de eventos ou recursos de ônibus de serviço através da opção **de serviço confiável** da Microsoft.

### <a name="turn-on-managed-identity-for-iot-hub"></a>Ligue a identidade gerida para ioT Hub

Para permitir que outros serviços encontrem o seu hub IoT como um serviço microsoft de confiança, deve ter uma identidade gerida atribuída ao sistema.

1. Navegue para a **identidade** no seu portal IoT Hub

1. Em **Status**, selecione **On**e, em seguida, clique em **Guardar**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Screenshot mostrando como ligar identidade gerida para IoT Hub":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Atribua identidade gerida ao seu IoT Hub no momento da criação usando o modelo ARM

Para atribuir a identidade gerida ao seu hub IoT no tempo de fornecimento de recursos, utilize o modelo ARM abaixo:

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

Depois de substituir os valores do seu `name` `location` recurso, `SKU.name` pode utilizar o `SKU.tier` Azure CLI para implantar o recurso num grupo de recursos existente utilizando:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Após a criação do recurso, pode recuperar a identidade de serviço gerida atribuída ao seu hub utilizando o Azure CLI:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Preços para identidade gerida

A funcionalidade de exceção de serviços de primeira festa fidedigna da Microsoft é gratuita. Os encargos com as contas de armazenamento, centros de eventos ou recursos de autocarros de serviço aplicam-se separadamente.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Conectividade Egressa para pontos finais da conta de armazenamento para encaminhamento

O IoT Hub pode encaminhar mensagens para uma conta de armazenamento do cliente. Para permitir que a funcionalidade de encaminhamento aceda a uma conta de armazenamento enquanto as restrições de firewall estão em vigor, o seu IoT Hub precisa de ter uma [identidade gerida.](#turn-on-managed-identity-for-iot-hub) Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para aceder à sua conta de armazenamento.

1. No portal Azure, navegue no separador Controlo de **Acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** na secção **Adicionar uma função.**

2. Selecione **o Colaborador de Dados blob de armazenamento** ([*não* contribuinte ou contribuinte de conta de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)) como **função**, **utilizador, grupo ou principal de serviço do Azure** como **atribuindo acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para o separador **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção **de redes selecionadas.** Na lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft tenham acesso a esta conta de armazenamento.** Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **o Armazenamento** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o recipiente que pretende utilizar no seu armazenamento de bolhas, forneça codificação e formato de nome de ficheiro. Selecione **Sistema Atribuído** como tipo **de autenticação** ao seu ponto final de armazenamento. Clique no botão **Criar**.

Agora, o seu ponto final de armazenamento personalizado está configurado para usar a identidade atribuída ao sistema do seu hub, e tem permissão para aceder ao seu recurso de armazenamento, apesar das restrições de firewall. Agora pode utilizar este ponto final para definir uma regra de encaminhamento.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Conectividade Egressa para centros de eventos pontos finais para encaminhamento

O IoT Hub pode ser configurado para encaminhar mensagens para um espaço de nomes de centros de eventos pertencentes ao cliente. Para permitir que a funcionalidade de encaminhamento aceda a um recurso de centros de eventos enquanto as restrições de firewall estão em vigor, o seu IoT Hub precisa de ter uma identidade gerida. Assim que uma identidade gerida for criada, siga os passos abaixo para dar permissão ao RBAC para aceder aos seus centros de eventos.

1. No portal Azure, navegue no separador **Access control (IAM)** dos seus centros de eventos e clique em **Adicionar** na secção De atribuição **de funções** Adicionar.

2. Selecione **O Remetente de Dados de Centros de Eventos** como **função**, utilizador, grupo ou principal de serviço do **Azure** como **atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para o separador **Firewalls e redes virtuais** nos seus centros de eventos e permita o acesso a partir da opção **de redes selecionadas.** Na lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam aos centros de eventos.** Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **os centros de eventos** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço e o exemplo dos centros de eventos e clique no botão **Criar.**

Agora, o ponto final dos seus centros de eventos personalizados está configurado para usar a identidade atribuída ao sistema do seu hub, e tem permissão para aceder aos seus centros de eventos, apesar das restrições de firewall. Agora pode utilizar este ponto final para definir uma regra de encaminhamento.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Conectividade Egress para serviço de pontos finais de autocarro para encaminhamento

O IoT Hub pode ser configurado para encaminhar mensagens para um espaço de nome de ônibus de serviço de propriedade do cliente. Para permitir que a funcionalidade de encaminhamento aceda a um recurso de ônibus de serviço enquanto as restrições de firewall estão em vigor, o seu IoT Hub precisa de ter uma identidade gerida. Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para aceder ao seu ônibus de serviço.

1. No portal Azure, navegue no separador **access control (IAM)** do seu autocarro de serviço e clique em **Adicionar** na secção De atribuição de **funções** Adicionar.

2. Selecione **O Remetente de dados do ônibus** de serviço como **função**, **utilizador, grupo ou principal de serviço Azure** como **atribuindo acesso** e selecione o nome de recurso do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para o separador **Firewalls e redes virtuais** no seu autocarro de serviço e permita o acesso a partir da opção **de redes selecionadas.** Na lista **de Exceções,** verifique a caixa para permitir que **os serviços fidedignos da Microsoft acedam a este autocarro de serviço.** Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **a fila de autocarros** de serviço ou **o tópico do Service Bus** (conforme aplicável) como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço de nome do seu autocarro de serviço e a fila ou tópico (conforme aplicável). Clique no botão **Criar**.

Agora, o seu ponto final de serviço personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de ônibus de serviço, apesar das restrições de firewall. Agora pode utilizar este ponto final para definir uma regra de encaminhamento.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Conectividade Egressa para contas de armazenamento para upload de ficheiros

A funcionalidade de upload de ficheiros do IoT Hub permite que os dispositivos carreguem ficheiros para uma conta de armazenamento do cliente. Para permitir o funcionamento do upload do ficheiro, ambos os dispositivos e ioT Hub precisam de ter conectividade com a conta de armazenamento. Se as restrições de firewall estiverem em vigor na conta de armazenamento, os seus dispositivos precisam de utilizar qualquer mecanismo da conta de armazenamento suportada (incluindo [pontos finais privados,](../private-link/create-private-endpoint-storage-portal.md) [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço ou [configuração de firewall direta](../storage/common/storage-network-security.md)) para ganhar conectividade. Da mesma forma, se as restrições de firewall estiverem em vigor na conta de armazenamento, o IoT Hub precisa de ser configurado para aceder ao recurso de armazenamento através da exceção de serviços fidedignos da Microsoft. Para isso, o seu Hub IoT deve ter uma identidade gerida. Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para aceder à sua conta de armazenamento.

1. No portal Azure, navegue no separador Controlo de **Acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** na secção **Adicionar uma função.**

2. Selecione **o Colaborador de Dados blob de armazenamento** ([*não* contribuinte ou contribuinte de conta de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)) como **função**, **utilizador, grupo ou principal de serviço do Azure** como **atribuindo acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para o separador **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção **de redes selecionadas.** Na lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft tenham acesso a esta conta de armazenamento.** Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para **o separador de upload de ficheiros.**

5. Na página que aparece, selecione o recipiente que pretende utilizar no seu armazenamento de bolhas, configufique as **definições de notificação de ficheiros,** **SAS TTL,** **TTL Padrão**e **contagem máxima de entrega** conforme desejado. Selecione **Sistema Atribuído** como tipo **de autenticação** ao seu ponto final de armazenamento. Clique no botão **Criar**.

Agora, o seu ponto final de armazenamento para upload de ficheiros está configurado para usar a identidade atribuída ao sistema do seu hub, e tem permissão para aceder ao seu recurso de armazenamento, apesar das restrições de firewall.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>A conectividade da Egress às contas de armazenamento para a importação/exportação de dispositivos a granel

O IoT Hub suporta a funcionalidade de [importação/exportação](./iot-hub-bulk-identity-mgmt.md) de informações de dispositivos a granel a partir de/para uma bolha de armazenamento fornecida pelo cliente. Para permitir o funcionamento da função de importação/exportação a granel, tanto os dispositivos como o IoT Hub precisam de ter conectividade com a conta de armazenamento.

Esta funcionalidade requer conectividade desde o IoT Hub até à conta de armazenamento. Para aceder a um recurso de ônibus de serviço enquanto as restrições de firewall estão em vigor, o seu IoT Hub precisa de ter uma identidade gerida. Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão ao RBAC para a identidade de recursos do seu hub para aceder ao seu ônibus de serviço.

1. No portal Azure, navegue no separador Controlo de **Acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** na secção **Adicionar uma função.**

2. Selecione **o Colaborador de Dados blob de armazenamento** ([*não* contribuinte ou contribuinte de conta de armazenamento](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)) como **função**, **utilizador, grupo ou principal de serviço do Azure** como **atribuindo acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para o separador **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção **de redes selecionadas.** Na lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft tenham acesso a esta conta de armazenamento.** Clique no botão **Guardar**.

Pode agora utilizar as APIs Azure IoT REST para criar postos de trabalho de [exportação](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) de importação para obter informações sobre como utilizar a funcionalidade de importação/exportação a granel. Terá de fornecer o `storageAuthenticationType="identityBased"` corpo e a utilização no seu corpo de pedido `inputBlobContainerUri="https://..."` e como `outputBlobContainerUri="https://..."` URLs de entrada e saída da sua conta de armazenamento, respectivamente.

O Azure IoT Hub SDKs também suporta esta funcionalidade no gestor de registo do cliente de serviço. O seguinte código snippet mostra como iniciar uma tarefa de importação ou de exportação na utilização do C# SDK.

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

Para utilizar esta versão dos Azure IoT SDKs com suporte de rede virtual para C#, Java e Node.js:

1. Crie uma variável ambiental nomeada `EnableStorageIdentity` e desaprova o seu valor `1` para .

2. Descarregue o SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
Para Python, baixe a nossa versão limitada do GitHub.

1. Navegue para a página de lançamento do [GitHub](https://aka.ms/vnetpythonsdk).

2. Descarregue o seguinte ficheiro, que encontrará na parte inferior da página de lançamento sob o título de **título**.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Abra um terminal e navegue para a pasta com o ficheiro descarregado.

4. Executar o seguinte comando para instalar o Python Service SDK com suporte para redes virtuais:
    > pip instalar ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Passos seguintes

Use os links abaixo para saber mais sobre as funcionalidades do IoT Hub:

* [Encaminhamento de mensagens](./iot-hub-devguide-messages-d2c.md)
* [Upload de arquivo](./iot-hub-devguide-file-upload.md)
* [Importação/exportação de dispositivos a granel](./iot-hub-bulk-identity-mgmt.md) 
