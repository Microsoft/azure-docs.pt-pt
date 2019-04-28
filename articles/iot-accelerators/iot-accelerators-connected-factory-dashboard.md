---
title: Utilize o dashboard de fábrica ligada - Azure | Documentos da Microsoft
description: Saiba como utilizar as funcionalidades do dashboard de fábrica ligada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 05befc72f512f502456f798f25b6c72571451363
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451047"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Utilizar as funcionalidades no dashboard de acelerador de solução de fábrica ligada

O [implantar uma solução baseada na nuvem para gerir os meus dispositivos de IoT industriais](quickstart-connected-factory-deploy.md) início rápido mostrou como navegar o dashboard e responder a alarmes. Este guia de procedimentos mostra alguns recursos adicionais do dashboard, que pode utilizar para monitorizar e gerir os seus dispositivos IoT industriais.

## <a name="apply-filters"></a>Aplicar filtros

Pode filtrar as informações apresentadas no dashboard ou no **localizações de fábrica** painel ou o **alarmes** painel:

1. Clique no ícone **funil** para ver uma lista dos filtros disponíveis no painel de localizações das fábricas ou de alarmes.

1. O painel filtros é apresentado:

    [![Filtros de acelerador de solução de fábrica ligados](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Escolha o filtro que necessárias e clique em **aplicar**. Também é possível escrever texto livre nos campos do filtro.

1. O filtro, em seguida, é aplicado. O ícone de funil extra indica que é aplicado um filtro:

    [![Filtro de acelerador de solução de fábrica ligado aplicado](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Um filtro de Active Directory não afeta os valores OEE e KPI apresentados, este filtra apenas o conteúdo de lista.

1. Para limpar um filtro, clique no funil e clique em **limpar** no painel de filtro.

## <a name="browse-an-opc-ua-server"></a>Procurar servidores OPC UA

Ao implementar o solution accelerator, Aprovisiona automaticamente um conjunto de servidores OPC UA simulados, que pode procurar a partir do dashboard. Servidores simulados tornam mais fácil para experimentar o solution accelerator sem a necessidade de implantar servidores reais. Se quiser ligar um servidor OPC UA real à solução, consulte a [ligar o seu dispositivo de OPC UA para o acelerador de solução de fábrica ligada](iot-accelerators-connected-factory-gateway-deployment.md) tutorial.

1. Clique nas **ícone de browser** na barra de navegação do dashboard:

    [![Navegador do servidor de acelerador de solução do conectados Factory](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Escolha um dos servidores da lista que mostra os servidores implementados por si no solution accelerator:

    [![Ligado lista de servidores do acelerador de solução de fábrica](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Clique em **Ligar**; é apresentada uma caixa de diálogo de segurança. A simulação, é seguro clicar **continuar**.

1. Para expandir um dos nós na árvore de servidores, clique no mesmo. Nós que estão a publicar telemetria têm uma marca de verificação ao lado:

    [![Ligado árvore de servidor do acelerador de solução de fábrica](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Clique com o botão direito do rato num item para ler, escrever, publicar ou chamar esse nó. As ações à sua disposição dependem das suas permissões e dos atributos do nó. A opção de leitura apresenta um painel de contexto que mostra o valor do nó específico. A opção de escrita apresenta um painel de contexto, onde pode introduzir um valor novo. A opção de chamada apresenta um nó onde pode introduzir os parâmetros da chamada.

## <a name="publish-a-node"></a>Publicar um nó

Quando procura um *servidor OPC UA simulado*, também pode optar por publicar nós novos. Pode analisar a telemetria desses nós na solução. Estes *servidores OPC UA simulados* tornam mais fácil fazer experiências com o solution accelerator, sem ter de implementar dispositivos reais:

1. Navegue para um nó na árvore do browser de servidores OPC UA que quer publicar.

1. Clique com o botão direito do nó. Clique em **publicar**:

    [![Nó de publicação do acelerador de solução de fábrica ligada](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. É mostrado um painel de contexto que lhe diz que a publicação foi bem-sucedida. O nó aparece na vista de nível de estação com uma marca de verificação junto à mesma:

    [![Publicação bem-sucedida da acelerador de solução de fábrica ligada](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Comando e controlo

A Fábrica Ligada permite-lhe comandar e controlar os seus dispositivos industriais diretamente a partir da cloud. Pode utilizar esta funcionalidade para responder a alarmes gerados pelos dispositivos. Por exemplo, pode enviar um comando para o dispositivo para abrir uma válvula de pressão. Os comandos disponíveis encontram-se no nó **StationCommands**, na árvore do browser de servidores OPC UA. Neste cenário, vai abrir uma válvula de saída de pressão na estação de montagem de uma linha de produção em Munique. Para utilizar a funcionalidade de comando e controlo, tem de constar da **administrador** função para a implementação de acelerador de solução:

1. Navegue para o **StationCommands** nó na árvore do browser de servidores OPC UA para Munique, a linha de produção 0, a estação de assembly.

1. Escolha o comando que pretende utilizar. Com o botão direito a **OpenPressureReleaseValve** nó. Clique em **chamar**:

    [![Ligado comando de chamada do acelerador de solução de fábrica](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. É apresentado um painel de contexto informando-o de qual o método que está prestes a chamada e eventuais detalhes de parâmetros. Clique em **chamar**:

    [![Ligado parâmetros de chamadas do acelerador de solução de fábrica](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. O painel de contexto é atualizado, para o informar de que a chamada do método foi bem-sucedida. Para verificar se a chamada teve êxito, pode ler o valor do nó de pressão que foi atualizado como resultado da mesma.

    [![Ligado êxito da chamada da fábrica solution accelerator](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Nos bastidores

Quando implementa um acelerador da solução, o processo de implementação cria vários recursos na subscrição do Azure que selecionou. Pode ver estes recursos no Azure [portal](https://portal.azure.com). O processo de implementação cria um **grupo de recursos** com um nome baseado no nome que escolheu para o acelerador da solução:

[![Ligado grupo de recursos do acelerador de solução de fábrica](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Pode ver as definições de cada recurso, selecionando-o na lista de recursos no grupo de recursos.

Também pode ver o código-fonte para o solution accelerator na [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) repositório do GitHub.

Quando tiver terminado, pode eliminar o solution accelerator da sua subscrição do Azure no [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) site. Este site permite-lhe eliminar facilmente todos os recursos que foram aprovisionados quando criou o acelerador da solução.

> [!NOTE]
> Para garantir que elimina tudo relacionado ao solution accelerator, elimine-o sobre o [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) site. Não elimine o grupo de recursos no portal.

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou um acelerador da solução funcional, pode ler os artigos seguintes para continuar a introdução aos aceleradores de soluções de IoT:

* [Instruções do acelerador de solução de fábrica ligada](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Ligar o seu dispositivo para o acelerador de solução de fábrica ligada](iot-accelerators-connected-factory-gateway-deployment.md)
* [Permissões no azureiotsolutions.com site](iot-accelerators-permissions.md)
