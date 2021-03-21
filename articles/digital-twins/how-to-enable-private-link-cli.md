---
title: Permitir o acesso privado com Link Privado (pré-visualização) - CLI
titleSuffix: Azure Digital Twins
description: Veja como permitir o acesso privado a soluções Azure Digital Twins com Private Link, utilizando o Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5bd7ffda508980a9a56d86037887fc53a0fed640
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202948"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Permitir o acesso privado com Link Privado (pré-visualização): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Este artigo descreve as diferentes formas de permitir o [Private Link com um ponto final privado para uma instância Azure Digital Twins](concepts-security.md#private-network-access-with-azure-private-link-preview) (atualmente em pré-visualização). Configurar um ponto final privado para a sua instância Azure Digital Twins permite-lhe proteger a sua instância Azure Digital Twins e eliminar a exposição pública, bem como evitar a exfiltração de dados da sua [Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Este artigo percorre o processo utilizando o [**Azure CLI**](/cli/azure/what-is-azure-cli).

Aqui estão os passos que estão cobertos neste artigo: 
1. Ligue o Link Privado e configuure um ponto final privado para uma instância Azure Digital Twins.
1. Desativar ou permitir que as bandeiras de acesso à rede pública, apenas para restringir o acesso da API às ligações private link.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um ponto final privado, precisará de uma [**Rede Virtual Azure (VNet)**](../virtual-network/virtual-networks-overview.md) onde o ponto final possa ser implantado. Se ainda não tiver um VNet, pode seguir um dos [quickstarts](../virtual-network/quick-create-portal.md) da Rede Virtual Azure para configurar isto.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Gerir pontos finais privados para um exemplo de Azure Digital Twins 

Ao utilizar o [Azure CLI,](/cli/azure/what-is-azure-cli)pode configurar pontos finais privados com Private Link num caso Azure Digital Twins que já existe (não pode ser adicionado como parte da criação de exemplos). Em seguida, pode continuar a vê-los e a geri-los através de comandos CLI adicionais. 

>[!TIP]
> Também pode configurar um ponto final de Ligação Privada através do serviço Private Link, em vez de através da sua instância Azure Digital Twins. Isto também dá as mesmas opções de configuração e o mesmo resultado final.
>
> Para obter mais detalhes sobre a criação de recursos de Ligação Privada, consulte a documentação private link para o [portal Azure,](../private-link/create-private-endpoint-portal.md) [Azure CLI,](../private-link/create-private-endpoint-cli.md) [modelos ARM](../private-link/create-private-endpoint-template.md)ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Adicione um ponto final privado a um caso existente

Para criar um ponto final privado e ligá-lo a uma instância Azure Digital Twins, use o [**comando de criação de ponto de terminação da rede Az.**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) Identifique a instância Azure Digital Twins utilizando o seu ID totalmente qualificado no `--private-connection-resource-id` parâmetro.

Aqui está um exemplo que usa o comando para criar um ponto final privado, com apenas os parâmetros necessários.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Para obter uma lista completa dos parâmetros necessários e opcionais, bem como exemplos de criação de pontos finais mais privados, consulte a [ **rede az-private-endpoint criar** documentação de referência](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Gerir ligações privadas de ponto final no caso

Uma vez criado um ponto final privado para a sua instância Azure Digital Twins, pode utilizar os comandos [**de ligação de pontos de terminação da rede Az DT**](/cli/azure/ext/azure-iot/dt/network/private-endpoint/connection) para continuar a gerir **ligações privadas** de ponto final no que diz respeito à instância. As operações incluem:
* Mostrar uma ligação de ponto final privado
* Definir o estado da ligação private-endpoint
* Eliminar a ligação de ponto final privado
* Listar todas as ligações de ponto final privado para um exemplo

Para obter mais informações e exemplos, consulte a [documentação de referência de referência **de ponto final privado da rede AZ DT**](/cli/azure/ext/azure-iot/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Gerir outras informações de Private Link sobre um caso Azure Digital Twins

Pode obter informações adicionais sobre o estado do Link Privado do seu caso com os comandos [**de ligação privada da rede Az DT.**](/cli/azure/ext/azure-iot/dt/network/private-link) As operações incluem:
* Listar links privados associados a uma instância Azure Digital Twins
* Mostrar uma ligação privada associada ao caso

Para obter mais informações e exemplos, consulte a [documentação de referência **de ligação privada da rede Az DT**](/cli/azure/ext/azure-iot/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Desativar / permitir bandeiras de acesso à rede pública

Pode configurar a sua instância Azure Digital Twins para negar todas as ligações públicas e permitir apenas ligações através de pontos finais privados para aumentar a segurança da rede. Esta ação é feita com uma **bandeira de acesso à rede pública.** 

Esta política permite-lhe restringir apenas o acesso da API às ligações private link. Quando a bandeira de acesso à rede pública estiver definida para *desativação,* todas as chamadas da API para o avião de dados de exemplo das Gémeas Digitais Azure da nuvem pública regressarão `403, Unauthorized` . Em alternativa, quando a política é definida para *deficientes* e um pedido é feito através de um ponto final privado, a chamada da API será bem sucedida.

Este artigo mostra como atualizar o valor da bandeira da rede utilizando o [CLI Azure](/cli/azure/) ou a [ferramenta de comando ARMClient](https://github.com/projectkudu/ARMClient). Para obter instruções sobre como fazê-lo com o portal Azure, consulte a [versão porta](how-to-enable-private-link-portal.md) deste artigo.

### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

No CLI Azure, pode desativar ou permitir o acesso à rede pública adicionando um `--public-network-access` parâmetro ao `az dt create` comando. Embora este comando também possa ser usado para criar um novo exemplo, você pode usá-lo para editar as propriedades de uma instância existente, fornecendo-lhe o nome de um caso que já existe. (Para obter mais informações sobre este comando, consulte a sua [documentação de referência](/cli/azure/ext/azure-iot/dt#ext_azure_iot_az_dt_create) ou as [instruções gerais para a criação de um exemplo de Gémeos Digitais Azure).](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)

Para **desativar** o acesso à rede pública para uma instância Azure Digital Twins, utilize o `--public-network-access` parâmetro como este:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Para **permitir o** acesso à rede pública num caso em que esteja atualmente desativado, utilize o seguinte comando similar:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Utilize a ferramenta de comando ARMClient 

Com a [ferramenta de comando ARMClient,](https://github.com/projectkudu/ARMClient)o acesso à rede pública está ativado ou desativado utilizando os comandos abaixo. 

Para **desativar o** acesso à rede pública:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Para **permitir o** acesso à rede pública:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Private Link for Azure: 
* [*O que é o serviço Azure Private Link?*](../private-link/private-link-service-overview.md)