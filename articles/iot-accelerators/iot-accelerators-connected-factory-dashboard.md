---
title: Usar o painel de fábrica conectado – Azure | Microsoft Docs
description: Este artigo descreve como usar os recursos do painel de fábrica conectado para monitorar e gerenciar seus dispositivos IoT industriais.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: b53177d578768428665891704269e63bd8edb09e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820181"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Usar recursos no painel do Solution Accelerator da fábrica conectada

O início rápido [implantar uma solução baseada em nuvem para gerenciar meus dispositivos IOT industriais](quickstart-connected-factory-deploy.md) mostrou como navegar no painel e responder a alarmes. Este guia de instruções mostra alguns recursos adicionais do painel que você pode usar para monitorar e gerenciar seus dispositivos IoT industriais.

## <a name="apply-filters"></a>Aplicar filtros

Você pode filtrar as informações exibidas no painel no painel locais de **fábrica** ou no painel **alarmes** :

1. Clique no ícone **funil** para ver uma lista dos filtros disponíveis no painel de localizações das fábricas ou de alarmes.

1. O painel filtros é exibido:

    [![filtros do Solution Accelerator da fábrica conectada](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Escolha o filtro que você precisa e clique em **aplicar**. Também é possível digitar texto livre nos campos de filtro.

1. O filtro é então aplicado. O ícone de funil extra indica que um filtro é aplicado:

    [filtro do acelerador de solução de fábrica conectada ![aplicado](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Um filtro ativo não afeta os valores de OEE e KPI exibidos, apenas filtra o conteúdo da lista.

1. Para limpar um filtro, clique no funil e clique em **limpar** no painel de filtro.

## <a name="browse-an-opc-ua-server"></a>Procurar servidores OPC UA

Ao implantar o Solution Accelerator, você provisiona automaticamente um conjunto de servidores OPC UA simulados que você pode navegar no painel. Os servidores simulados facilitam o teste do Solution Accelerator sem a necessidade de implantar servidores reais.

1. Clique no **ícone do navegador** na barra de navegação do painel:

    [navegador do servidor do acelerador de solução de fábrica conectada ![](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Escolha um dos servidores na lista que mostra os servidores implantados para você no Solution Accelerator:

    [![lista de servidores do Solution Accelerator da fábrica conectada](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Clique em **Ligar**; é apresentada uma caixa de diálogo de segurança. Para a simulação, é seguro clicar em **continuar**.

1. Para expandir um dos nós na árvore de servidores, clique no mesmo. Os nós que estão publicando telemetria têm uma marca de seleção ao lado deles:

    [árvore do servidor do acelerador de solução de fábrica conectada ![](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Clique com o botão direito do rato num item para ler, escrever, publicar ou chamar esse nó. As ações à sua disposição dependem das suas permissões e dos atributos do nó. A opção de leitura apresenta um painel de contexto que mostra o valor do nó específico. A opção de escrita apresenta um painel de contexto, onde pode introduzir um valor novo. A opção de chamada apresenta um nó onde pode introduzir os parâmetros da chamada.

## <a name="publish-a-node"></a>Publicar um nó

Quando procura um *servidor OPC UA simulado*, também pode optar por publicar nós novos. Pode analisar a telemetria desses nós na solução. Esses *servidores OPC UA simulados* facilitam o teste do Solution Accelerator sem implantar dispositivos reais:

1. Navegue para um nó na árvore do browser de servidores OPC UA que quer publicar.

1. Clique com o botão direito do nó. Clique em **publicar**:

    [nó de publicação do Solution Accelerator da fábrica conectada ![](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. É mostrado um painel de contexto que lhe diz que a publicação foi bem-sucedida. O nó aparece na exibição em nível de estação com uma marca de seleção ao lado:

    [êxito na publicação do Solution Accelerator da fábrica conectada ![](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Comando e controlo

A Fábrica Ligada permite-lhe comandar e controlar os seus dispositivos industriais diretamente a partir da cloud. Pode utilizar esta funcionalidade para responder a alarmes gerados pelos dispositivos. Por exemplo, você pode enviar um comando para o dispositivo para abrir uma válvula de liberação de pressão. Os comandos disponíveis encontram-se no nó **StationCommands**, na árvore do browser de servidores OPC UA. Neste cenário, vai abrir uma válvula de saída de pressão na estação de montagem de uma linha de produção em Munique. Para usar a funcionalidade de comando e controle, você deve estar na função **administrador** para a implantação do Solution Accelerator:

1. Navegue até o nó **StationCommands** na árvore do navegador de servidor do OPC UA para o Munique, linha de produção 0, estação de assembly.

1. Escolha o comando que pretende utilizar. Clique com o botão direito do mouse no nó **OpenPressureReleaseValve** . Clique em **chamar**:

    [![comando de chamada do Solution Accelerator da fábrica conectada](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Um painel de contexto é exibido informando a você qual método você está prestes a chamar e os detalhes do parâmetro. Clique em **chamar**:

    [![parâmetros de chamada do Solution Accelerator da fábrica conectada](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. O painel de contexto é atualizado, para o informar de que a chamada do método foi bem-sucedida. Para verificar se a chamada teve êxito, pode ler o valor do nó de pressão que foi atualizado como resultado da mesma.

    [![êxito na chamada do Solution Accelerator da fábrica conectada](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Nos bastidores

Quando implementa um acelerador da solução, o processo de implementação cria vários recursos na subscrição do Azure que selecionou. Você pode exibir esses recursos no [portal](https://portal.azure.com)do Azure. O processo de implementação cria um **grupo de recursos** com um nome baseado no nome que escolheu para o acelerador da solução:

[grupo de recursos do acelerador de solução de fábrica ![conectado](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Pode ver as definições de cada recurso, selecionando-o na lista de recursos no grupo de recursos.

Você também pode exibir o código-fonte do Solution Accelerator no repositório GitHub [Azure-IOT-Connected-Factory](https://github.com/Azure/azure-iot-connected-factory) .

Quando terminar, você poderá excluir o Solution Accelerator de sua assinatura do Azure no site do [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) . Este site permite-lhe eliminar facilmente todos os recursos que foram aprovisionados quando criou o acelerador da solução.

> [!NOTE]
> Para garantir que você exclua tudo relacionado ao Solution Accelerator, exclua-o no site do [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) . Não elimine o grupo de recursos no portal.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou um acelerador da solução funcional, pode ler os artigos seguintes para continuar a introdução aos aceleradores de soluções de IoT:

* [Configurar o acelerador de solução de fábrica conectada](iot-accelerators-connected-factory-configure.md)
* [Permissões no site azureiotsolutions.com](iot-accelerators-permissions.md)
