---
title: 'Tutorial: Configurar o encaminhamento de tráfego de rodada-robin ponderado com O Gestor de Tráfego Azure'
description: Este tutorial explica como carregar o tráfego de equilíbrio usando um método de rodada-robin em Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207844"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Tutorial: Configurar o método de encaminhamento de tráfego ponderado no Gestor de Tráfego

Um padrão comum de método de encaminhamento de tráfego é fornecer um conjunto de pontos finais idênticos, que incluem serviços de nuvem e websites, e enviar tráfego para cada um igualmente. Os passos a seguir descrevem como configurar este tipo de método de encaminhamento de tráfego.

> [!NOTE]
> A Azure Web App já fornece funcionalidade de equilíbrio de carga de robin redondo para websites dentro de uma Região de Azure (que pode incluir vários centros de dados). O Gestor de Tráfego permite-lhe distribuir tráfego por websites em diferentes datacenters.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> - Crie um perfil de Gestor de Tráfego com encaminhamento ponderado.
> - Utilize o perfil de Gestor de Tráfego.
> - Apague o perfil do Gestor de Tráfego.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

## <a name="configure-the-weighted-traffic-routing-method"></a>Configurar o método de encaminhamento do tráfego ponderado

1. Num browser, inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na barra de pesquisa do portal, procure o nome de **perfil do Gestor de Tráfego** que criou na secção anterior e selecione o perfil do gestor de tráfego nos resultados que o apresentado.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Pesquisa rumo ao perfil do Gestor de Tráfego&quot;:::

1. Selecione **Configuração** e selecione ou introduza as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Método de encaminhamento            | **Selecione Ponderado**. |    
    | Tempo DNS para viver (TTL) | Este valor controla com que frequência o servidor de nomes de caching local do cliente irá consultar o sistema de Gestor de Tráfego para entradas de DNS atualizadas. Qualquer alteração que ocorra com o Traffic Manager, como alterações no método de encaminhamento de tráfego ou alterações na disponibilidade de pontos finais adicionados, levará este período de tempo a ser atualizado em todo o sistema global de servidores DNS. |
    | Protocolo    | Selecione um protocolo para monitorização do ponto final. *Opções: HTTP, HTTPS e TCP* |
    | Porta | Especifique o número da porta. |
    | Caminho | Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro &quot;/" é uma entrada válida para o caminho relativo e implica que o ficheiro está no diretório raiz (predefinição). |
    | Configurações personalizadas do cabeçalho | Configure os Cabeçalhos Personalizados no formato host:contoso.com,newheader:newvalue. O par máximo suportado é 8. Aplicável ao protocolo Http e Https. Aplicável a todos os pontos finais no perfil |
    | Intervalos de código de estado esperados (padrão: 200) | Configurar os intervalos do Código de Estado no formato 200-299,301-301. O alcance máximo suportado é de 8. Aplicável ao protocolo Http e Https. Aplicável a todos os pontos finais no perfil |
    | Intervalo de pesquisa | Configure o intervalo de tempo entre as sondas de saúde do ponto final. Pode escolher 10 ou 30 segundos. |
    | Tolerar o número de falhas | Configure o número de falhas da sonda de saúde toleradas antes de uma falha no ponto final ser desencadeada. Pode introduzir um número entre 0 e 9. | 
    | Tempo limite de sonda | Configure o tempo necessário antes de uma sonda de saúde de ponto final esgotar o tempo. Este valor deve ser pelo menos 5 e menor do que o valor do intervalo de sondagem. |

1. **Selecione Guardar** para completar a configuração.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Pesquisa rumo ao perfil do Gestor de Tráfego&quot;:::

1. Selecione **Configuração** e selecione ou introduza as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Método de encaminhamento            | **Selecione Ponderado**. |    
    | Tempo DNS para viver (TTL) | Este valor controla com que frequência o servidor de nomes de caching local do cliente irá consultar o sistema de Gestor de Tráfego para entradas de DNS atualizadas. Qualquer alteração que ocorra com o Traffic Manager, como alterações no método de encaminhamento de tráfego ou alterações na disponibilidade de pontos finais adicionados, levará este período de tempo a ser atualizado em todo o sistema global de servidores DNS. |
    | Protocolo    | Selecione um protocolo para monitorização do ponto final. *Opções: HTTP, HTTPS e TCP* |
    | Porta | Especifique o número da porta. |
    | Caminho | Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro &quot;/"::: 

1. Selecione **Endpoint** e configuure o peso de cada ponto final. O peso pode estar entre 1-1000. Quanto maior o peso, maior a prioridade.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Pesquisa rumo ao perfil do Gestor de Tráfego&quot;:::

1. Selecione **Configuração** e selecione ou introduza as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Método de encaminhamento            | **Selecione Ponderado**. |    
    | Tempo DNS para viver (TTL) | Este valor controla com que frequência o servidor de nomes de caching local do cliente irá consultar o sistema de Gestor de Tráfego para entradas de DNS atualizadas. Qualquer alteração que ocorra com o Traffic Manager, como alterações no método de encaminhamento de tráfego ou alterações na disponibilidade de pontos finais adicionados, levará este período de tempo a ser atualizado em todo o sistema global de servidores DNS. |
    | Protocolo    | Selecione um protocolo para monitorização do ponto final. *Opções: HTTP, HTTPS e TCP* |
    | Porta | Especifique o número da porta. |
    | Caminho | Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro &quot;/"::: 

## <a name="use-the-traffic-manager-profile"></a>Utilize o perfil do Gestor de Tráfego

O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. O nome pode ser usado por qualquer cliente (por exemplo, navegando para ele usando um navegador web) para ser encaminhado para o ponto final direito, conforme determinado pelo tipo de encaminhamento. Neste caso, todos os pedidos são encaminhados cada ponto final de uma forma redonda.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Pesquisa rumo ao perfil do Gestor de Tráfego&quot;:::

1. Selecione **Configuração** e selecione ou introduza as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Método de encaminhamento            | **Selecione Ponderado**. |    
    | Tempo DNS para viver (TTL) | Este valor controla com que frequência o servidor de nomes de caching local do cliente irá consultar o sistema de Gestor de Tráfego para entradas de DNS atualizadas. Qualquer alteração que ocorra com o Traffic Manager, como alterações no método de encaminhamento de tráfego ou alterações na disponibilidade de pontos finais adicionados, levará este período de tempo a ser atualizado em todo o sistema global de servidores DNS. |
    | Protocolo    | Selecione um protocolo para monitorização do ponto final. *Opções: HTTP, HTTPS e TCP* |
    | Porta | Especifique o número da porta. |
    | Caminho | Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro &quot;/"::: 

## <a name="clean-up-resources"></a>Limpar recursos

Se já não necessitar do perfil do Gestor de Tráfego, localize o perfil e selecione **Excluir o perfil**.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Pesquisa rumo ao perfil do Gestor de Tráfego&quot;:::

1. Selecione **Configuração** e selecione ou introduza as seguintes definições:

    | Definição         | Valor                                              |
    | ---             | ---                                                |
    | Método de encaminhamento            | **Selecione Ponderado**. |    
    | Tempo DNS para viver (TTL) | Este valor controla com que frequência o servidor de nomes de caching local do cliente irá consultar o sistema de Gestor de Tráfego para entradas de DNS atualizadas. Qualquer alteração que ocorra com o Traffic Manager, como alterações no método de encaminhamento de tráfego ou alterações na disponibilidade de pontos finais adicionados, levará este período de tempo a ser atualizado em todo o sistema global de servidores DNS. |
    | Protocolo    | Selecione um protocolo para monitorização do ponto final. *Opções: HTTP, HTTPS e TCP* |
    | Porta | Especifique o número da porta. |
    | Caminho | Para monitorizar os pontos finais, tem de especificar um caminho e um nome de ficheiro. Um corte dianteiro &quot;/":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o método de encaminhamento ponderado, consulte:

> [!div class="nextstepaction"]
> [Método de encaminhamento de tráfego ponderado](traffic-manager-routing-methods.md#weighted)