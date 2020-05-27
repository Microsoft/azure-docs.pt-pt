---
title: Suporte azure IoT Hub para redes virtuais
description: Como usar o padrão de conectividade de redes virtuais com o IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: jlian
ms.openlocfilehash: 7d7e04c526f7327a000ac26e255d2c8363c01f5c
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871243"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Suporte do IoT Hub para redes virtuais com Link Privado e Identidade Gerida

Por padrão, os nomes de anfitriões do IoT Hub mapeiam para um ponto final público com um endereço IP publicamente resaída através da internet. Diferentes clientes partilham este ponto final do IoT Hub, e os dispositivos IoT em redes de grande área e redes no local podem todos acessá-lo.

![Ponto final do IoT Hub](./media/virtual-network-support/public-endpoint.png)

As funcionalidades do IoT Hub, incluindo [o encaminhamento de mensagens,](./iot-hub-devguide-messages-d2c.md) [o upload de ficheiros,](./iot-hub-devguide-file-upload.md)e a [importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel também requerem conectividade do IoT Hub para um recurso Azure propriedade do cliente sobre o seu ponto final público. Estes caminhos de conectividade compõem coletivamente o tráfego de saída do IoT Hub para os recursos dos clientes.

Você pode querer restringir a conectividade aos seus recursos Azure (incluindo IoT Hub) através de um VNet que possui e opera. Estas razões incluem:

* Introduzindo o isolamento da rede para o seu hub IoT, evitando a exposição à conectividade à internet pública.

* Possibilitando uma experiência de conectividade privada a partir dos seus ativos de rede no local, garantindo que os seus dados e tráfego são transmitidos diretamente para a rede de espinha dorsal Azure.

* Prevenção de ataques de exfiltração de redes sensíveis no local. 

* Seguindo padrões de conectividade estabelecidos em toda a Azure utilizando [pontos finais privados](../private-link/private-endpoint-overview.md).

Este artigo descreve como alcançar estes objetivos usando [o Azure Private Link](../private-link/private-link-overview.md) para a conectividade ingresso com o IoT Hub e usando a exceção de serviços confiáveis da Microsoft para a conectividade de egress do IoT Hub para outros recursos Azure.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Conectividade ingressa ao IoT Hub usando ligação privada Azure

Um ponto final privado é um endereço IP privado atribuído dentro de um VNet propriedade do cliente através do qual um recurso Azure é acessível. Através do Azure Private Link, você pode configurar um ponto final privado para o seu hub IoT para permitir que os serviços dentro do seu VNet cheguem ao IoT Hub sem exigir que o tráfego seja enviado para o ponto final público do IoT Hub. Da mesma forma, os seus dispositivos no local podem utilizar a [Rede Privada Virtual (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](https://azure.microsoft.com/services/expressroute/) a espreitar para obter conectividade com o seu VNet e o seu IoT Hub (através do seu ponto final privado). Como resultado, pode restringir ou bloquear completamente a conectividade com os pontos finais públicos do seu hub IoT utilizando [o filtro IP IoT Hub](./iot-hub-ip-filtering.md) e configurando o [encaminhamento para não enviar quaisquer dados para o ponto final incorporado](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Esta abordagem mantém a conectividade com o seu Hub utilizando o ponto final privado para dispositivos. O principal foco desta configuração é para dispositivos dentro de uma rede no local. Esta configuração não é aconselhada para dispositivos implantados numa rede de área ampla.

![Ponto final do IoT Hub](./media/virtual-network-support/virtual-network-ingress.png)

Antes de prosseguir, certifique-se de que os seguintes pré-requisitos são cumpridos:

* [Criou um Azure VNet](../virtual-network/quick-create-portal.md) com uma subrede na qual será criado o ponto final privado.

* Para dispositivos que operam em redes no local, instale a [Rede Privada Virtual (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou a [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privada no seu Azure VNet.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Criar um ponto final privado para ioT hub ingress

1. No portal Azure, selecione **Networking**, **Ligações de ponto final privados,** e clique no **ponto final + Private**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Screenshot mostrando onde adicionar ponto final privado para IoT Hub":::

1. Forneça a subscrição, o grupo de recursos, o nome e a região para criar o novo ponto final privado. Idealmente, o ponto final privado deve ser criado na mesma região que o seu hub.

1. Clique em **Seguinte: Recurso**, e forneça a subscrição para o seu recurso IoT Hub, e selecione **"Microsoft.Devices/IotHubs"** como tipo de recurso, o seu nome IoT Hub como **recurso,** e **iotHub** como subrecurso alvo.

1. Clique em **Seguinte: Configuração** e forneça a sua rede virtual e subnet para criar o ponto final privado. Selecione a opção de integração com a zona Privada DNS do Azure, se desejar.

1. Clique em **Seguinte: Tags,** e opcionalmente forneça quaisquer etiquetas para o seu recurso.

1. Clique em **Rever + crie** para criar o seu recurso de ligação privada.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>O endpoint compatível com o Event Hub incorporado não suporta o acesso sobre o ponto final privado

O ponto final compatível com o [Event Hub incorporado](iot-hub-devguide-messages-read-builtin.md) não suporta o acesso sobre o ponto final privado. Quando configurado, o ponto final privado de um hub IoT é apenas para conectividade de ingresso. Consumir dados do Event Hub integrado só pode ser feito através da internet pública. 

O [filtro IP](iot-hub-ip-filtering.md) do IoT Hub também não controla o acesso público ao ponto final incorporado. Para bloquear completamente o acesso da rede pública ao seu hub IoT, deve: 

1. Configure acesso de ponto final privado para IoT Hub
1. Desligue o acesso à rede pública utilizando filtro IP para bloquear todos os IP
1. Desligue o ponto final do Event Hub [incorporado, configurando o encaminhamento para não enviar dados para ele](iot-hub-devguide-messages-d2c.md)
1. Desligue a rota de [recuo](iot-hub-devguide-messages-d2c.md#fallback-route)
1. Configure a saída para outros recursos do Azure utilizando [o serviço confiável da Microsoft](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Preços para Private Link

Para obter detalhes sobre preços, consulte o preço do [Link Privado Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Conectividade egress do IoT Hub para outros recursos Azure

O IoT Hub pode ligar-se ao seu armazenamento de blob Azure, hub de eventos, recursos de ônibus de serviço para [encaminhamento de mensagens,](./iot-hub-devguide-messages-d2c.md)upload de [ficheiros](./iot-hub-devguide-file-upload.md)e [importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel sobre o ponto final público dos recursos. Ligar o seu recurso a um VNet bloqueia a conectividade com o recurso por padrão. Como resultado, esta configuração impede que o IoT Hub's trabalhe no envio de dados para os seus recursos. Para corrigir este problema, ative a conectividade desde o seu recurso IoT Hub até à sua conta de armazenamento, hub de eventos ou recursos de ônibus de serviço através da opção **de serviço confiável** da Microsoft.

### <a name="turn-on-managed-identity-for-iot-hub"></a>Ligue a identidade gerida para o IoT Hub

Para permitir que outros serviços encontrem o seu hub IoT como um serviço microsoft confiável, deve ter uma identidade gerida atribuída pelo sistema.

1. Navegue para **a Identidade** no seu portal IoT Hub

1. Em **'Estado',** selecione **On**e, em seguida, clique em **Guardar**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Screenshot mostrando como ativar a identidade gerida para IoT Hub":::

### <a name="pricing-for-managed-identity"></a>Preços para identidade gerida

A funcionalidade de exceção de serviços de primeira parte da Microsoft fidedigna é gratuita. Os encargos com as contas de armazenamento, centros de eventos ou recursos de autocarros de serviço aplicam-se separadamente.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Conectividade egress para pontos finais da conta de armazenamento para encaminhamento

O IoT Hub pode encaminhar mensagens para uma conta de armazenamento propriedade do cliente. Para permitir que a funcionalidade de encaminhamento aceda a uma conta de armazenamento enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma [identidade gerida](#turn-on-managed-identity-for-iot-hub). Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder à sua conta de armazenamento.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** sob a secção de atribuição de **funções.**

2. Selecione O Colaborador de **Dados blob** de armazenamento[*(não* Colaborador ou Contribuinte](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)de Conta de Armazenamento ) como **função,** utilizador, grupo ou principal de **serviço azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**. Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de Mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **Armazenamento** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o recipiente que pretende utilizar no seu armazenamento blob, forneça codificação e formato de nome de ficheiro. Selecione **Sistema Atribuído** como o **tipo de Autenticação** ao seu ponto final de armazenamento. Clique no botão **Criar**.

Agora o seu ponto final de armazenamento personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de armazenamento, apesar das restrições de firewall. Agora pode usar este ponto final para estabelecer uma regra de encaminhamento.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Conectividade egress para os pontos finais dos hubs de eventos para o encaminhamento

O IoT Hub pode ser configurado para direcionar mensagens para um espaço de nome de centros de eventos do cliente. Para permitir que a funcionalidade de encaminhamento aceda a um recurso de hubs de eventos enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade gerida. Assim que for criada uma identidade gerida, siga os passos abaixo para dar permissão ao RBAC para aceder à identidade de recursos do seu hub para aceder aos seus centros de eventos.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** dos seus hubs de eventos e clique em **Adicionar** sob a secção **de atribuição de funções.**

2. Selecione **Event Hubs Data Sender** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** nos seus hubs de eventos e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam aos centros de eventos**. Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de Mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione **os cubos de evento seleção** como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço de nome e instância dos seus centros de eventos e clique no botão **Criar.**

Agora, o seu ponto final de evento personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao recurso dos hubs do seu evento, apesar das restrições de firewall. Agora pode usar este ponto final para estabelecer uma regra de encaminhamento.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Conectividade egress para servir pontos finais de autocarro para encaminhamento

O IoT Hub pode ser configurado para encaminhar mensagens para um espaço de nome de autocarro de serviço do cliente. Para permitir que a funcionalidade de encaminhamento aceda a um recurso de ônibus de serviço enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade gerida. Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder ao seu ônibus de serviço.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** do seu autocarro de serviço e clique em **Adicionar** sob a secção **de atribuição de funções.**

2. Selecione **Service bus Data Sender** como **função**, utilizador, grupo ou principal de **serviço Azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** no seu autocarro de serviço e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir serviços fidedignos**da Microsoft para aceder a este autocarro de serviço . Clique no botão **Guardar**.

4. Na página de recursos do seu IoT Hub, navegue para o **separador de encaminhamento de Mensagens.**

5. Navegue para a secção **de pontos finais personalizados** e clique em **Adicionar**. Selecione a fila de **ônibus de serviço** ou **o tópico do ônibus de serviço** (conforme aplicável) como o tipo de ponto final.

6. Na página que aparece, forneça um nome para o seu ponto final, selecione o espaço de nome do seu autocarro de serviço e a fila ou tópico (conforme aplicável). Clique no botão **Criar**.

Agora, o seu ponto final de ônibus de serviço personalizado está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de ônibus de serviço, apesar das restrições de firewall. Agora pode usar este ponto final para estabelecer uma regra de encaminhamento.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Conectividade egress para contas de armazenamento para upload de ficheiros

A funcionalidade de upload de ficheiros do IoT Hub permite que os dispositivos carreguem ficheiros para uma conta de armazenamento do cliente. Para permitir que o upload do ficheiro funcione, tanto os dispositivos como o IoT Hub precisam de ter conectividade com a conta de armazenamento. Se houver restrições de firewall na conta de armazenamento, os seus dispositivos precisam de utilizar qualquer um dos mecanismos da conta de armazenamento suportado (incluindo [pontos finais privados,](../private-link/create-private-endpoint-storage-portal.md) [pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md)de serviço ou [configuração de firewall direta)](../storage/common/storage-network-security.md)para obter conectividade. Da mesma forma, se houver restrições de firewall na conta de armazenamento, o IoT Hub precisa de ser configurado para aceder ao recurso de armazenamento através da exceção de serviços confiáveis da Microsoft. Para este efeito, o seu Hub IoT deve ter uma identidade gerida. Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder à sua conta de armazenamento.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** sob a secção de atribuição de **funções.**

2. Selecione O Colaborador de **Dados blob** de armazenamento[*(não* Colaborador ou Contribuinte](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)de Conta de Armazenamento ) como **função,** utilizador, grupo ou principal de **serviço azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**. Clique no botão **Guardar**.

4. Na página de recursos do ioT Hub, navegue para **o separador de upload do Ficheiro.**

5. Na página que aparece, selecione o recipiente que pretende utilizar no seu armazenamento de blob, configure as definições de notificação de **Ficheiros**, **SAS TTL,** **Default TTL**e Contagem máxima de **entrega** conforme desejado. Selecione **Sistema Atribuído** como o **tipo de Autenticação** ao seu ponto final de armazenamento. Clique no botão **Criar**.

Agora, o seu ponto final de armazenamento para upload de ficheiros está configurado para usar a identidade atribuída ao seu hub, e tem permissão para aceder ao seu recurso de armazenamento, apesar das restrições de firewall.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Conectividade egress com contas de armazenamento para importação/exportação de dispositivos a granel

O IoT Hub suporta a funcionalidade de informação sobre [a importação/exportação](./iot-hub-bulk-identity-mgmt.md) de dispositivos a granel de/para uma bolha de armazenamento fornecida pelo cliente. Para permitir que a função de importação/exportação a granel funcione, tanto os dispositivos como o IoT Hub precisam de ter conectividade com a conta de armazenamento.

Esta funcionalidade requer conectividade do IoT Hub para a conta de armazenamento. Para aceder a um recurso de ônibus de serviço enquanto existem restrições de firewall, o seu IoT Hub precisa de ter uma identidade gerida. Uma vez que uma identidade gerida é aprovisionada, siga os passos abaixo para dar permissão rBAC à identidade de recursos do seu hub para aceder ao seu ônibus de serviço.

1. No portal Azure, navegue para o separador de controlo de **acesso (IAM)** da sua conta de armazenamento e clique em **Adicionar** sob a secção de atribuição de **funções.**

2. Selecione O Colaborador de **Dados blob** de armazenamento[*(não* Colaborador ou Contribuinte](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)de Conta de Armazenamento ) como **função,** utilizador, grupo ou principal de **serviço azure AD** como **Atribuír acesso** e selecione o nome de recursos do seu IoT Hub na lista de drop-down. Clique no botão **Guardar**.

3. Navegue para as **Firewalls e redes virtuais** na sua conta de armazenamento e permita o acesso a partir da opção de **redes selecionadas.** De acordo com a lista **de Exceções,** verifique a caixa para **permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento**. Clique no botão **Guardar**.

Pode agora utilizar as APIs de repouso Azure IoT para [criar postos](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) de trabalho de importação para informações sobre a utilização da funcionalidade de importação/exportação a granel. Terá de fornecer o corpo e utilização do `storageAuthenticationType="identityBased"` seu pedido e como `inputBlobContainerUri="https://..."` `outputBlobContainerUri="https://..."` urLs de entrada e saída da sua conta de armazenamento, respectivamente.

Os SDKs Azure IoT Hub também suportam esta funcionalidade no gestor de registo do cliente de serviço. O seguinte código de snippet mostra como iniciar um trabalho de importação ou exportação na utilização do C# SDK.

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

Para utilizar esta versão dos SDKs Azure IoT com suporte de rede virtual para C#, Java e Node.js:

1. Crie uma variável ambiental nomeada `EnableStorageIdentity` e detetete o seu valor para `1` .

2. Baixe o SDK: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [Node.js](https://aka.ms/vnetnodesdk)
 
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
