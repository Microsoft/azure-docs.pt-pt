---
title: Utilize o dashboard connected Factory - Azure / Microsoft Docs
description: Este artigo descreve como utilizar as funcionalidades do dashboard Connected Factory para monitorizar e gerir os seus dispositivos IoT industriais.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011222"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Utilize funcionalidades no painel de aceleração de solução de fábrica conectada

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

A [Implementação de uma solução baseada em nuvem para gerir os meus dispositivos IoT industriais](quickstart-connected-factory-deploy.md) de arranque rápido mostrou-lhe como navegar no painel de instrumentos e responder a alarmes. Este guia de como fazer mostra-lhe algumas funcionalidades adicionais do dashboard que pode usar para monitorizar e gerir os seus dispositivos IoT industriais.

## <a name="apply-filters"></a>Aplicar filtros

Pode filtrar as informações apresentadas no painel de **instrumentos,** quer no painel 'Localizações da Fábrica', quer no painel **Alarms:**

1. Clique no ícone **funil** para ver uma lista dos filtros disponíveis no painel de localizações das fábricas ou de alarmes.

1. O painel de filtros é apresentado:

    [![Filtros do acelerador da solução Fábrica Ligada](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Escolha o filtro que necessita e clique em **Aplicar.** Também é possível escrever texto gratuito nos campos de filtro.

1. O filtro é então aplicado. O ícone do funil extra indica que é aplicado um filtro:

    [![Filtro acelerador de solução de fábrica conectado aplicado](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Um filtro ativo não afeta os valores OEE e KPI apresentados, filtra apenas o conteúdo da lista.

1. Para limpar um filtro, clique no funil e clique em **Limpar** no painel do filtro.

## <a name="browse-an-opc-ua-server"></a>Procurar servidores OPC UA

Quando implementa o acelerador de solução, fornece automaticamente um conjunto de servidores OPC UA simulados que pode navegar a partir do painel de instrumentos. Servidores simulados facilitam a experimentação do acelerador de soluções sem a necessidade de implantar servidores reais.

1. Clique no ícone do **navegador** na barra de navegação do painel de instrumentos:

    [![Browser do servidor do acelerador da solução Fábrica Ligada](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Escolha um dos servidores da lista que mostra os servidores implantados para si no acelerador de solução:

    [![Lista de servidores de acelerador de solução de fábrica conectado](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Clique em **Ligar**; é apresentada uma caixa de diálogo de segurança. Para a simulação, é seguro clicar em **Continuar.**

1. Para expandir um dos nós na árvore de servidores, clique no mesmo. Os nódes que estão a publicar telemetria têm uma marca de verificação ao seu lado:

    [![Árvore de servidores do acelerador da solução Fábrica Ligada](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Clique com o botão direito do rato num item para ler, escrever, publicar ou chamar esse nó. As ações à sua disposição dependem das suas permissões e dos atributos do nó. A opção de leitura apresenta um painel de contexto que mostra o valor do nó específico. A opção de escrita apresenta um painel de contexto, onde pode introduzir um valor novo. A opção de chamada apresenta um nó onde pode introduzir os parâmetros da chamada.

## <a name="publish-a-node"></a>Publicar um nó

Quando procura um *servidor OPC UA simulado*, também pode optar por publicar nós novos. Pode analisar a telemetria desses nós na solução. Estes *servidores OPC UA simulados* facilitam a experimentação com o acelerador de solução sem implantar dispositivos reais:

1. Navegue para um nó na árvore do browser de servidores OPC UA que quer publicar.

1. Clique com o botão direito do nó. Clique **em Publicar:**

    [![Acelerador de solução de fábrica conectado publicar nó](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. É mostrado um painel de contexto que lhe diz que a publicação foi bem-sucedida. O nó aparece na vista de nível da estação com uma marca de verificação ao lado:

    [![Acelerador de solução de fábrica conectada publica sucesso](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Comando e controlo

A Fábrica Ligada permite-lhe comandar e controlar os seus dispositivos industriais diretamente a partir da cloud. Pode utilizar esta funcionalidade para responder a alarmes gerados pelos dispositivos. Por exemplo, pode enviar um comando ao dispositivo para abrir uma válvula de libertação de pressão. Os comandos disponíveis encontram-se no nó **StationCommands**, na árvore do browser de servidores OPC UA. Neste cenário, vai abrir uma válvula de saída de pressão na estação de montagem de uma linha de produção em Munique. Para utilizar a funcionalidade de comando e controlo, tem de estar na função **de Administrador** para a implementação do acelerador de solução:

1. Navegue pelo nó **StationCommands** na árvore do navegador do servidor OPC UA para o Munique, linha de produção 0, estação de montagem.

1. Escolha o comando que pretende utilizar. Clique com o botão direito no nó **OpenPressureReleaseValve.** Clique **em Chamada**:

    [![Comando de chamada do acelerador da solução Fábrica Ligada](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Um painel de contexto aparece informando-o de que método está prestes a ligar e quaisquer detalhes do parâmetro. Clique **em Chamada**:

    [![Parâmetros de chamada de acelerador de solução de fábrica conectados](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. O painel de contexto é atualizado, para o informar de que a chamada do método foi bem-sucedida. Para verificar se a chamada teve êxito, pode ler o valor do nó de pressão que foi atualizado como resultado da mesma.

    [![Chamada do acelerador da solução Fábrica Ligada realizada com êxito](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Nos bastidores

Quando implementa um acelerador da solução, o processo de implementação cria vários recursos na subscrição do Azure que selecionou. Pode ver estes recursos no [Portal](https://portal.azure.com) do Azure. O processo de implementação cria um **grupo de recursos** com um nome baseado no nome que escolheu para o acelerador da solução:

[![Grupo de recursos de acelerador de solução de fábrica conectado](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Pode ver as definições de cada recurso, selecionando-o na lista de recursos no grupo de recursos.

Também pode ver o código fonte para o acelerador de solução no repositório GitHub [ligado à fábrica azure-iot.](https://github.com/Azure/azure-iot-connected-factory)

Quando terminar, pode eliminar o acelerador de solução da sua subscrição Azure no site [azureiotsolutions.com.](https://www.azureiotsolutions.com/Accelerators#dashboard) Este site permite-lhe eliminar facilmente todos os recursos que foram aprovisionados quando criou o acelerador da solução.

> [!NOTE]
> Para garantir que elimina tudo o que está relacionado com o acelerador de solução, elimine-o no [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) site. Não elimine o grupo de recursos no portal.

## <a name="next-steps"></a>Passos seguintes

Agora que implementou um acelerador da solução funcional, pode ler os artigos seguintes para continuar a introdução aos aceleradores de soluções de IoT:

* [Configure o acelerador de solução de fábrica conectado](iot-accelerators-connected-factory-configure.md)
* [Permissões no site azureiotsolutions.com](iot-accelerators-permissions.md)
