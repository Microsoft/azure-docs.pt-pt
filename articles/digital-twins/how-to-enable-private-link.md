---
title: Permitir o acesso privado com Link Privado (pré-visualização)
titleSuffix: Azure Digital Twins
description: Veja como permitir o acesso privado a soluções Azure Digital Twins com Private Link
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a5328f52975e72298b93107792692b178dac8a97
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948819"
---
# <a name="enable-private-access-with-private-link-preview"></a>Permitir o acesso privado com Link Privado (pré-visualização)

Este artigo descreve as diferentes formas de permitir o [Private Link com um ponto final privado para uma instância Azure Digital Twins](concepts-security.md#private-network-access-with-azure-private-link-preview) (atualmente em pré-visualização). Configurar um ponto final privado para a sua instância Azure Digital Twins permite-lhe proteger a sua instância Azure Digital Twins e eliminar a exposição pública, bem como evitar a exfiltração de dados da sua [Rede Virtual Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Aqui estão os passos que estão cobertos neste artigo: 
1. Ligue o Link Privado e configuure um ponto final privado para uma instância Azure Digital Twins.
1. Desativar ou permitir que as bandeiras de acesso à rede pública, apenas para restringir o acesso da API às ligações private link.

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um ponto final privado, precisará de uma [**Rede Virtual Azure (VNet)**](../virtual-network/virtual-networks-overview.md) onde o ponto final possa ser implantado. Se ainda não tiver um VNet, pode seguir um dos [quickstarts](../virtual-network/quick-create-portal.md) da Rede Virtual Azure para configurar isto.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Adicione um ponto final privado para um exemplo de Azure Digital Twins 

Pode ligar o Private Link com um ponto final privado para uma instância Azure Digital Twins como parte da configuração inicial do caso, ou capacitá-lo mais tarde num caso que já existe. 

Qualquer um destes métodos de criação dará as mesmas opções de configuração e o mesmo resultado final para o seu exemplo. Esta secção descreve como fazer cada um no [portal Azure.](https://portal.azure.com)

>[!TIP]
> Também pode configurar um ponto final de Ligação Privada através do serviço Private Link, em vez de através da sua instância Azure Digital Twins. Isto também dá as mesmas opções de configuração e o mesmo resultado final.
>
> Para obter mais detalhes sobre a criação de recursos de Ligação Privada, consulte a documentação private link para o [portal Azure](../private-link/create-private-endpoint-portal.md), [Azure CLI,](../private-link/create-private-endpoint-cli.md) [ARM](../private-link/create-private-endpoint-template.md)ou [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Adicione um ponto final privado durante a criação de exemplo

Nesta secção, você usará o [portal Azure](https://portal.azure.com) para ativar o Private Link com um ponto final privado sobre um caso Azure Digital Twins que está atualmente a ser criado. Esta secção centra-se no passo de networking do processo de criação; para uma passagem completa da criação de um novo exemplo de Azure Digital Twins, consulte [*Como-a- Configurar um exemplo e autenticação*](how-to-set-up-instance-portal.md).

As opções de Ligação Privada estão localizadas no separador **de rede** de configuração de instância.

Neste separador, pode ativar pontos finais privados selecionando a opção **ponto final Privado** para o método **conectividade**.

Isto irá adicionar uma secção chamada **Private endpoint connections** onde pode configurar os detalhes do seu ponto final privado. Selecione o botão **+ Adicionar** para continuar.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Screenshot do portal Azure mostrando o separador de Rede do diálogo Criar Recursos para Gémeos Digitais Azure. Há um destaque em torno do nome do separador, a opção ponto final privado para o método conectividade, e o botão + Adicionar para criar uma nova ligação de ponto final privado." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Isto abrirá uma página para introduzir os detalhes de um novo ponto final privado.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Screenshot do portal Azure mostrando a página de ponto final privado Create. Contém os campos descritos abaixo.":::

1. Preencha as seleções para o seu grupo **de subscrição** e **recursos.** Desa esta medida **para** o mesmo local que o VNet que irá utilizar. Escolha um **Nome** para o ponto final e para **sub-recursos de destino** selecione *API*.

1. Em seguida, selecione a **rede Virtual** e **a Subnet** que pretende utilizar para implementar o ponto final.

1. Por último, selecione se integra com a **zona privada de DNS**. Pode utilizar o padrão de **Sim** ou, para ajuda com esta opção, pode seguir o link no portal para saber mais sobre a [integração privada de DNS](../private-link/private-endpoint-overview.md#dns-configuration).

Depois de preencher as opções de configuração, **acerte OK** para terminar.

Isto irá devolvê-lo ao separador **de rede** da configuração de exemplos Azure Digital Twins, onde o seu novo ponto final deve ser visível em conexões de ponto final **Private.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Screenshot do portal Azure mostrando o separador de Rede do diálogo Criar Recursos para Gémeos Digitais Azure. Há um destaque em torno da nova ligação de ponto final privado, e os botões de navegação (Rever + criar, Anterior, Seguinte: Avançado)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Em seguida, pode utilizar os botões de navegação inferiores para continuar com o resto da configuração do caso.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Adicione um ponto final privado a um caso existente

Nesta secção, você usará o [portal Azure](https://portal.azure.com) para ativar o Private Link com um ponto final privado para um caso Azure Digital Twins que já existe.

1. Primeiro, navegue até ao [portal Azure](https://portal.azure.com) num browser. Traga a sua instância Azure Digital Twins procurando o seu nome na barra de pesquisa do portal.

1. Selecione **Networking (pré-visualização)** no menu da esquerda.

1. Mude para o **separador de ligações de ponto final privado.**

1. Selecione **+ ponto final privado** para abrir a configuração do ponto final **privado.**

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Screenshot do portal Azure mostrando a página de Networking (pré-visualização) para uma instância Azure Digital Twins. O separador de ligações de ponto final privado é realçado e o botão + ponto final privado também é realçado." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. No  ****   separador Básicos, insira ou selecione o **grupo** de **Subscrição** e Recursos do seu projeto e um **Nome** e **Região** para o seu ponto final. A região tem de ser a mesma que a região para o VNet que está a usar.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Screenshot do portal Azure mostrando o primeiro separador (Básicos) do diálogo de ponto final privado. Contém os campos descritos acima.":::

    Quando terminar, selecione o seguinte : O botão **>de recursos** para ir ao separador seguinte.

1. No **separador Recursos,** insira ou selecione estas informações: 
    * **Método de ligação**: Selecione **Connect to a a azure resource in my directy** to search for your Azure Digital Twins instance.
    * **Subscrição**: Introduza a sua subscrição.
    * **Tipo de recurso**: Selecione **Microsoft.DigitalTwins/digitalTwinsInstances**
    * **Recurso**: Selecione o nome da sua instância Azure Digital Twins.
    * **Sub-recurso-alvo**: Selecione **API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Screenshot do portal Azure mostrando o segundo separador (Recurso) do diálogo de ponto final privado. Contém os campos descritos acima.":::

    Quando terminar, selecione o seguinte **: Configuração >** botão para ir para o separador seguinte.    

1. No **separador Configuração,** introduza ou selecione estas informações:
    * **Rede virtual**: Selecione a sua rede virtual.
    * **Sub-rede**: Escolha uma sub-rede da sua rede virtual.
    * **Integre-se com a zona privada de DNS**: Selecione se **integra com a zona privada de DNS**. Pode utilizar o padrão de **Sim** ou, para ajuda com esta opção, pode seguir o link no portal para saber mais sobre a [integração privada de DNS](../private-link/private-endpoint-overview.md#dns-configuration).
    Se selecionar **Sim,** pode deixar as informações de configuração predefinidos.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Screenshot do portal Azure mostrando o terceiro separador (Configuração) do diálogo de ponto final privado. Contém os campos descritos acima.":::

    Quando terminar, pode selecionar o botão **'Rever +' para terminar** a configuração. 

1. No **separador 'Rever +' criar,** rever as suas seleções e selecionar o botão  **Criar.** 

Quando o ponto final estiver concluído, deve aparecer nas ligações de ponto final privado para a sua instância Azure Digital Twins.

>[!TIP]
> O ponto final também pode ser visto a partir do Private Link Center no portal Azure.

## <a name="disable--enable-public-network-access-flags"></a>Desativar / permitir bandeiras de acesso à rede pública

Pode configurar a sua instância Azure Digital Twins para negar todas as ligações públicas e permitir apenas ligações através de pontos finais privados para aumentar a segurança da rede. Esta ação é feita com uma **bandeira de acesso à rede pública.** 

Esta política permite-lhe restringir apenas o acesso da API às ligações private link. Quando a bandeira de acesso à rede pública estiver definida para *desativação,* todas as chamadas da API para o avião de dados de exemplo das Gémeas Digitais Azure da nuvem pública regressarão `403, Unauthorized` . Em alternativa, quando a política é definida para *deficientes* e um pedido é feito através de um ponto final privado, a chamada da API será bem sucedida.

Pode atualizar o valor da bandeira da rede utilizando o [portal Azure](https://portal.azure.com) [CLI](/cli/azure/)ou a [ferramenta de comando ARMClient](https://github.com/projectkudu/ARMClient).

### <a name="option-1-using-the-azure-portal"></a>Opção 1: Utilização do portal Azure

Para desativar ou permitir o acesso à rede pública no [portal Azure,](https://portal.azure.com)abra o portal e navegue para a sua instância Azure Digital Twins.

1. Selecione **Networking (pré-visualização)** no menu da esquerda.

1. No separador **de acesso público,** definir **Permita o acesso da rede pública a** redes **desativadas** ou **a todas as redes**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Screenshot do portal Azure mostrando a página de Networking (pré-visualização) para uma instância Azure Digital Twins. Destaca-se também o separador de acesso público, destacando-se também a opção de escolher se permite o acesso à rede pública." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Selecione **Guardar**.

### <a name="option-2-using-the-azure-cli"></a>Opção 2: Utilização do CLI Azure

Com o CLI Azure, esta ação é feita usando o `az resource update` comando como mostrado abaixo. A propriedade publicNetworkAccess em uma instância Azure Digital Twins pode ser configurada para um `disabled` ou `enabled` .

Para desativar a bandeira, utilize o seguinte comando, substituindo os espaços reservados pelos valores da sua instância.
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=disabled  
```

Aqui está uma imagem de como a saída parece.

:::image type="content" source="media/how-to-enable-private-link/network-flag-cli.png" alt-text="Screenshot de uma janela terminal que corre o comando Azure CLI acima. A saída contém detalhes do caso, incluindo uma propriedade chamada publicNetworkAccess com um valor de Desativado." lightbox="media/how-to-enable-private-link/network-flag-cli.png":::

Para ativar a bandeira, utilize o seguinte comando semelhante.
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=enabled  
```

### <a name="option-3-usingarmclient"></a>Opção 3: Utilização de ARMClient  

Com a [ferramenta de comando ARMClient,](https://github.com/projectkudu/ARMClient)o acesso à rede pública está ativado ou desativado utilizando os comandos abaixo. 

Para desativar o acesso à rede pública:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Para permitir o acesso à rede pública:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre o Private Link for Azure: 
* [*O que é o serviço Azure Private Link?*](../private-link/private-link-service-overview.md)