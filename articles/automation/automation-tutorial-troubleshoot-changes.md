---
title: Resolver problemas relacionados com alterações numa máquina virtual do Azure | Microsoft Docs
description: Utilize o Controlo de Alterações para resolver problemas relacionados com alterações numa máquina virtual do Azure.
services: automation
ms.subservice: change-inventory-management
keywords: alterações, controlo, automatização
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 89f5e00c75b6b85c9a14de02504136907cde62b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604702"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Resolver problemas relacionados com alterações no seu ambiente

Neste tutorial, irá aprender a resolver problemas relacionados com alterações numa máquina virtual do Azure. Ao ativar o Change Tracking, pode rastrear alterações em software, ficheiros, daemons Linux, Serviços Windows e windows registry chaves nos seus computadores.
A identificação destas alterações de configuração pode ajudar a localizar problemas operacionais em todo o ambiente.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Carregar uma VM para Controlo de alterações e Inventário
> * Pesquisar serviços parados em registos de alterações
> * Configurar o Controlo de alterações
> * Ativar a ligação do Registo de atividades
> * Acionar um evento
> * Ver as alterações
> * Configurar alertas

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um [conta de Automatização](automation-offering-get-started.md) para reter os runbooks de observador e ação e a Tarefa de Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de alterações e Inventário

Primeiro, tem de ativar o Change Tracking e o Inventário para o seu VM para este tutorial. Se ativou anteriormente outra solução de automatização para uma VM, este passo não é necessário.

1. No menu esquerdo, selecione **máquinas Virtuais** e selecione um VM da lista.
1. No menu esquerdo, selecione **Inventário** em **Operações**. A página de inventário abre.

![Ativar a mudança](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

Configure a localização, a área de trabalho do Log Analytics e a conta de Automatização a utilizar e clique em **Ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

A área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) serve para recolher dados gerados pelas funcionalidades e serviços, como o Inventário.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

Durante o embarque, o VM é aprovisionado com o agente Log Analytics para Windows e um Trabalhador de Runbook Híbrido.
O agente é utilizado para comunicar com o VM e obter informações sobre software instalado.

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.
Após a solução ser ativada, a informação sobre o software instalado e as alterações nos fluxos vm para os registos do Monitor Azure.
Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Utilização de rastreio de alterações nos registos do Monitor Azure

Alterar o rastreio gera dados de registo que são enviados para registos do Monitor Azure.
Para pesquisar os registos executando consultas, selecione **Log Analytics** no topo da página de rastreio de alteração.
Alterar os dados de rastreio `ConfigurationChange`é armazenado sob o tipo .
O exemplo de consulta do Log Analytics que se segue devolve todos os Serviços do Windows que foram parados.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo nos registos do Monitor Azure, consulte os [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Configurar o Controlo de alterações

O Controlo de alterações permite controlar as alterações de configuração na VM. Os passos que se seguem mostram como configurar o controlo de ficheiros e chaves do Registo.

Para escolher os ficheiros e as chaves do Registo que devem ser recolhidos e controlados, selecione **Editar definições** na parte superior da página Controlo de alterações.

> [!NOTE]
> O Inventário e o Controlo de alterações utilizam as mesmas definições de coleção, sendo as definições configuradas ao nível da área de trabalho.

Na página de Configuração workspace, adicione as teclas do Registo do Windows, ficheiros Windows ou ficheiros Linux a serem rastreados, conforme descrito nas próximas três secções.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registo do Windows

1. No separador **Registo do Windows**, selecione **Adicionar**. 

1. Na página Adicionar Registo do Windows para alterar, introduza a informação para a tecla rastrear e clicar em **Guardar**

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Chave do Registo do Windows   | O caminho para verificar o ficheiro, por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Adicionar um ficheiro do Windows

1. No separador **Ficheiros do Windows**, selecione **Adicionar**. 

1. Na página Adicionar Ficheiro windows para alterar, introduza as informações para o ficheiro ou diretório para rastrear e clicar em **Guardar**

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "c:\temp\\\\*.txt"<br>Também pode utilizar variáveis de ambiente, tais como "%winDir%\System32\\\*.*"         |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

### <a name="add-a-linux-file"></a>Adicionar um ficheiro do Linux

1. No separador **Ficheiros do Linux**, selecione **Adicionar**. 

1. Na página Add Linux para Alterar Tracking, introduza as informações para o ficheiro ou diretório para rastrear e clicar em **Guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "/etc/*.conf"       |
|Tipo de Caminho     | O tipo de item a controlar, em que valores possíveis são Ficheiro e Diretório        |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Utilizar o Sudo     | Esta definição determina se o sudo é utilizado ao verificar o item.         |
|Ligações     | Esta definição determina como as ligações simbólicas são processadas ao atravessar diretórios.<br> **Ignorar** - ignora as ligações simbólicas e não inclui os ficheiros/diretórios referenciados<br>**Seguir** - segue as ligações simbólicas durante a recursão e também inclui os ficheiros/diretórios referenciados<br>**Gerir** - segue as ligações simbólicas e permite alterar o tratamento do conteúdo devolvido      |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: Verdadeiro ou Falso.|

   > [!NOTE]
   > A opção **gerir links** não é recomendada. A obtenção de conteúdo do ficheiro não é suportada.

## <a name="enable-activity-log-connection"></a>Ativar a ligação do Registo de atividades

Na página Controlo de alterações da sua VM, selecione **Gerir Ligação de Registo de Atividades**. Esta tarefa abre a página Registo de atividades do Azure. Clique em Ligar o **Connect** para ligar o Rastreio de Mudança ao registo de atividade do Azure para o seu VM.

Com esta definição ativada, navegue para a página Descrição Geral da sua VM e selecione **Parar** para parar a VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Quando esta estiver desalocada, selecione **Iniciar** para reiniciar a sua VM.

As operações de paragem e início de uma VM registam um evento no respetivo registo de atividades. Regresse à página Controlo de alterações. Selecione o separador **Eventos** na parte inferior da página. Passado algum tempo, os eventos são apresentados no gráfico e na tabela. Tal como no passo anterior, é possível selecionar cada um dos eventos para ver informações detalhadas sobre o evento.

![Ver detalhes da alteração no portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Ver as alterações

Depois de ativar a solução Controlo de alterações e Inventário, pode ver os resultados na página Controlo de alterações.

Na sua VM, selecione **Controlo de alterações** em **OPERAÇÕES**.

![Captura de ecrã que mostra a lista de alterações da VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

O gráfico mostra as alterações ocorridas ao longo do tempo.
Após ter adicionado uma ligação do Registo de Atividades, o gráfico de linhas existente na parte superior apresenta os eventos do Registo de Atividades do Azure.
Cada linha do gráfico de barras representa um tipo diferente de alteração passível de controlo.
Estes tipos incluem daemons do Linux, ficheiros, chaves do Registo do Windows, software e serviços do Windows.
O separador Alterações mostra os detalhes das alterações apresentadas na visualização por ordem descendente de data/hora em que a alteração ocorreu (mais recente primeiro).
No separador **Eventos**, a tabela apresenta os eventos do Registo de Atividades ligados e os respetivos detalhes, com o mais recente listado primeiro.

Como é possível constatar nos resultados, foram efetuadas várias alterações no sistema, incluindo alterações ao nível do software e serviços. Pode utilizar os filtros na parte superior da página para filtrar os resultados consoante o **Tipo de alteração** ou um intervalo de tempo.

Selecione uma alteração **WindowsServices.** Esta seleção abre a página Change Details mostrando detalhes sobre a mudança e os valores antes e depois da alteração. Neste caso, o serviço Proteção de Software foi parado.

![Ver detalhes da alteração no portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Configurar alertas

Ver as alterações no portal do Azure pode ser útil, mas poder ser alertado quando ocorre uma alteração, como um serviço parado é mais vantajoso.

Para adicionar um alerta a um serviço parado, no portal do Azure, aceda a **Monitorizar**. E, em **Serviços Partilhados**, selecione **Alertas** e clique em **+Nova regra de alerta**

Clique em **Selecionar** para escolher um recurso. Na página Selecione uma página de recursos, selecione **Log Analytics** do Filtro por menu de dropdown do tipo **de recurso.** Selecione a sua área de trabalho do Log Analytics e, em seguida, selecione **Concluído**.

![Selecionar um recurso](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Clique em **Adicionar condição,** na página lógica de sinal Configurar, na tabela, selecione pesquisa de **registo personalizado**. Introduza a seguinte consulta na caixa de texto Consulta de pesquisa:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Esta consulta devolve os computadores que tinham o serviço W3SVC parado no período de tempo especificado.

Em **Lógica de alerta**, para **Limiar**, introduza **0**. Quando tiver terminado, selecione **Concluído**.

![Configurar lógica de sinal](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

Em **grupos de ação,** selecione **Criar Novo**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir, mas não estão limitadas a notificações por e-mail, runbooks, webhooks e muitas mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Em **detalhes de Alerta,** insira um nome e descrição para o alerta. Defina a **Gravidade** para **Informativa (Grav 2)**, **Aviso (Grav 1)** ou **Crítica (Grav 0)**.

Na caixa **Nome do grupo de ações**, introduza um nome para o alerta e um nome abreviado. O nome abreviado é utilizado em vez de um nome de grupo de ações completo quando as notificações são enviadas ao utilizar deste grupo.

Em **Ações**, introduza um nome para a ação, como **Administradores por E-mail**. Em **TIPO DE AÇÃO**, selecione **E-Mail/SMS/Push/voz**. Em **DETALHES**, selecione **Editar detalhes**.

![Adicionar grupo de ação](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

Na página E-mail/SMS/Push/Voz, introduza um nome. Selecione a caixa de verificação **E-mail** e, em seguida, introduza um endereço de e-mail válido. Clique **ok** no painel e, em seguida, clique **OK** na página do grupo de ação Adicionar.

Para personalizar o assunto do e-mail de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do e-mail**. Quanto terminar, selecione **Criar regra de alerta**. O alerta informa-o quando a implementação de uma atualização for concluída com êxito e que máquinas faziam parte da execução dessa implementação de atualização.

A imagem seguinte é um e-mail de exemplo recebido quando o serviço W3SVC para.

![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Carregar uma VM para Controlo de alterações e Inventário
> * Pesquisar serviços parados em registos de alterações
> * Configurar o Controlo de alterações
> * Ativar a ligação do Registo de atividades
> * Acionar um evento
> * Ver as alterações
> * Configurar alertas

Avance para a descrição geral da solução Controlo de alterações e Inventário para saber mais acerca dela.

> [!div class="nextstepaction"]
> [Solução Gestão de mudança e Inventário](automation-change-tracking.md)

