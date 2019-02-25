---
title: Tente uma solução - Azure de monitorização remota de IoT baseado na nuvem | Documentos da Microsoft
description: Neste início rápido, vai implementar o acelerador de soluções de IoT de Monitorização Remota do Azure e iniciar sessão e utilizar o dashboard de soluções.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2018
ms.author: dobett
ms.openlocfilehash: 60876cb95e6efe4fbde55bd8a224eab658b10d66
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749992"
---
# <a name="quickstart-try-a-cloud-based-remote-monitoring-solution"></a>Início rápido: Tente uma solução de monitorização remota com base na cloud

Este início rápido mostra como implementar o acelerador de soluções de Monitorização Remota de IoT do Azure. Nesta solução baseada na cloud, utilize a página **Dashboard** da solução para visualizar os dispositivos simulados num mapa e a página **Manutenção** para responder a um alerta de pressão de um dispositivo chiller simulado. Pode utilizar este acelerador de soluções como o ponto de partida para a sua própria implementação ou como uma ferramenta de aprendizagem.

A implementação inicial configura o acelerador de soluções para uma empresa denominada Contoso. Enquanto operador na Contoso gere uma seleção de diferentes tipos de dispositivos, como chillers, implementados em ambientes físicos diferentes. Um dispositivo chiller envia telemetria de temperatura, humidade e pressão para o acelerador de soluções de Monitorização Remota.

Para concluir este início rápido, precisa de uma subscrição ativa do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="deploy-the-solution"></a>Implementar a solução

Ao implementar o acelerador de soluções na sua subscrição do Azure, tem de definir algumas opções de configuração.

Inicie sessão em [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) com as credenciais da conta do Azure.

Clique nas **monitorização remota** mosaico. Sobre o **monitorização remota** página, clique em **experimentar agora**:

![Selecionar a Monitorização Remota](./media/quickstart-remote-monitoring-deploy/remotemonitoring.png)

Na página **Criar solução de Monitorização Remota**, selecione uma implementação **Básica**. Se estiver a implementar o acelerador de soluções para saber como funciona ou executar uma demonstração, selecione a opção **Básica** para minimizar os custos.

Selecione **.NET** como linguagem. As implementações de Java e .NET têm as mesmas funcionalidades.

Introduza um **Nome da solução** exclusivo para o acelerador de soluções de Monitorização Remota. Neste início rápido, estamos a telefonar para nosso **contoso-rm**.

Selecione a **Subscrição** e a **Região** que quer utilizar para implementar o acelerador de soluções. Normalmente, pode escolher a região mais próxima para si. Neste início rápido, estamos usando **E.U.A. Leste**.
Pode escolher **Visual Studio Enterprise**, mas tem de ser um [administrador global ou de utilizador](iot-accelerators-permissions.md) para fazer isso.

Para começar a implementação, clique em **criar**. Este processo demora, pelo menos, cinco minutos a ser executado:

![Detalhes da solução de Monitorização Remota](./media/quickstart-remote-monitoring-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Iniciar sessão na solução

Quando a implementação na sua subscrição do Azure estiver concluída, verá uma marca de verificação verde e **Preparado** no mosaico da solução. Pode agora iniciar sessão no dashboard do acelerador de soluções de Monitorização Remota.

Na página **Soluções aprovisionadas**, clique no novo acelerador de soluções de Monitorização Remota:

![Escolher a nova solução](./media/quickstart-remote-monitoring-deploy/choosenew.png)

Pode ver informações sobre o acelerador de soluções de Monitorização Remota no painel apresentado. Escolher **ir do solution accelerator** para ver o seu acelerador de solução de monitorização remota:

![Painel de soluções](./media/quickstart-remote-monitoring-deploy/solutionpanel.png)

Clique em **Aceitar** para aceitar o pedido de permissões. O dashboard da solução de Monitorização Remota é apresentado no browser:

[![Dashboard de soluções](./media/quickstart-remote-monitoring-deploy/solutiondashboard-inline.png)](./media/quickstart-remote-monitoring-deploy/solutiondashboard-expanded.png#lightbox)

## <a name="view-your-devices"></a>Ver os seus dispositivos

O dashboard de soluções mostra as seguintes informações sobre os dispositivos simulados da Contoso:

* O painel **Estatísticas dos dispositivos** mostra informações de resumo sobre alertas e o número total de dispositivos. Na implementação predefinida, a Contoso tem 10 tipos diferentes de dispositivos simulados.

* O painel **Localizações dos dispositivos** mostra onde os dispositivos estão localizados fisicamente. A cor do alfinete mostra quando existem alertas do dispositivo.

* O painel **Alertas** mostra os detalhes de alertas dos dispositivos.

* O painel **Telemetria** mostra a telemetria dos dispositivos. Pode ver os diferentes fluxos de telemetria ao clicar nos tipos de telemetria na parte superior.

* O painel **Análise** mostra informações combinadas sobre os alertas dos dispositivos.

## <a name="respond-to-an-alert"></a>Responder a um alerta

Como operador na Contoso, pode monitorizar os dispositivos no dashboard de soluções. O painel **Estatísticas dos dispositivos** mostra que ocorreu um número de alertas críticos e o painel **Alertas** mostra que a maioria deles é proveniente de um dispositivo chiller. Para dispositivos chiller da Contoso, uma pressão interna superior a 250 PSI indica que o dispositivo não está a funcionar corretamente.

### <a name="identify-the-issue"></a>Identificar o problema

Na página **Dashboard**, no painel **Alertas**, pode ver o alerta **Pressão do chiller demasiado elevada**. O chiller tem um alfinete vermelho no mapa (pode ter de aplicar zoom ao mapa):

[![O dashboard mostra o alerta de pressão e o dispositivo no mapa](./media/quickstart-remote-monitoring-deploy/dashboardalarm-inline.png)](./media/quickstart-remote-monitoring-deploy/dashboardalarm-expanded.png#lightbox)

No painel **Alertas**, clique em **...** na coluna **Explorar** junto à regra **Pressão do chiller demasiado elevada**. Esta ação leva-o para a página **Manutenção** onde pode ver os detalhes da regra que acionou o alerta.

A página de manutenção **Pressão do chiller demasiado elevada** mostra os detalhes da regra que acionou os alertas. A página também indica quando os alertas ocorreram e o dispositivo que os acionaram:

[![A página de manutenção mostra a lista de alertas acionados](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancealarmlist-expanded.png#lightbox)

Identificou agora o problema que acionou o alerta e o dispositivo associado. Como operador, os passos seguintes são para reconhecer o alerta e corrigir o problema.

### <a name="fix-the-issue"></a>Corrigir o problema

Para indicar a outros operadores que está a trabalhar no alerta, selecione-o e altere o **Estado do alerta** para **Reconhecido**:

[![Selecionar e reconhecer o alerta](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceacknowledge-expanded.png#lightbox)

O valor na coluna de estado muda para **Reconhecido**.

Para agir sobre o chiller, desloque-se para baixo para **Informações relacionadas**, selecione o dispositivo chiller na lista **Dispositivos alertados** e, em seguida, selecione **Tarefas**:

[![Selecionar o dispositivo e agendar uma ação](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceschedule-expanded.png#lightbox)

No painel **Trabalhos**, escolha **Executar método** e, em seguida, o método **EmergencyValveRelease**. Adicionar o nome do trabalho **ChillerPressureRelease**e clique em **Aplicar**. Estas definições criam uma tarefa para si executada imediatamente.

Para ver o estado da tarefa, volte à página **Manutenção** e veja a lista de tarefas na vista **Tarefas**. Pode ter de aguardar alguns segundos antes de poder ver que a tarefa foi executada para libertar a pressão da válvula no chiller:

[![O estado das tarefas na vista Tarefas](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenancerunningjob-expanded.png#lightbox)

### <a name="check-the-pressure-is-back-to-normal"></a>Verificar se a pressão voltou ao normal

Para ver a telemetria de pressão do chiller, navegue para a página **Dashboard**, selecione **Pressão** no painel de telemetria e confirme se a pressão de **chiller-02.0** voltou ao normal:

[![A pressão voltou ao normal](./media/quickstart-remote-monitoring-deploy/pressurenormal-inline.png)](./media/quickstart-remote-monitoring-deploy/pressurenormal-expanded.png#lightbox)

Para fechar o incidente, navegue para a página **Manutenção**, selecione o alerta e defina o estado como **Fechado**:

[![Selecionar e fechar o alerta](./media/quickstart-remote-monitoring-deploy/maintenanceclose-inline.png)](./media/quickstart-remote-monitoring-deploy/maintenanceclose-expanded.png#lightbox)

O valor na coluna de estado muda para **Fechado**.

## <a name="clean-up-resources"></a>Limpar recursos

Se planear avançar para os tutoriais, deixe o acelerador de soluções de Monitorização Remota implementado.

Se já não precisar do acelerador de soluções, elimine-o na página [Soluções aprovisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) ao selecioná-lo e, em seguida, clique em **Eliminar Solução**:

![Eliminar solução](media/quickstart-remote-monitoring-deploy/deletesolution.png)


![Eliminar solução](media/quickstart-remote-monitoring-deploy/deletesolution-page.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou o acelerador de soluções de Monitorização Remota e concluiu uma tarefa de monitorização com os dispositivos simulados na implementação predefinida da Contoso.

Para saber mais sobre o acelerador de soluções que está a utilizar dispositivos simulados, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Tutorial: Monitorize os seus dispositivos IoT](iot-accelerators-remote-monitoring-monitor.md)