---
title: Mudanças de resolução de problemas num Azure VM em Automação Azure Microsoft Docs
description: Este artigo diz como resolver mudanças num VM Azure.
services: automation
ms.subservice: change-inventory-management
keywords: alterar, rastrear, alterar rastreio, inventário, automatização
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 211b34b4424fa5bc9b82dc1cc2a2da574ffc5d96
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743679"
---
# <a name="troubleshoot-changes-on-an-azure-vm"></a>Mudanças de resolução de problemas num VM Azure

Neste tutorial, irá aprender a resolver problemas relacionados com alterações numa máquina virtual do Azure. Ao ativar o Change Tracking and Inventory, pode rastrear alterações em software, ficheiros, daemons Linux, Serviços Windows e Windows Registry chaves nos seus computadores.
A identificação destas alterações de configuração pode ajudar a localizar problemas operacionais em todo o ambiente.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o rastreio e o inventário de alterações para um VM
> * Pesquisar serviços parados em registos de alterações
> * Configurar o Controlo de alterações
> * Ativar a ligação do Registo de atividades
> * Acionar um evento
> * Ver as alterações
> * Configurar alertas

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Uma [conta de Automação](automation-offering-get-started.md) para manter os cadernos de observadores e de ação e a tarefa do Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para ativar a funcionalidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

Primeiro, tem de ativar o Change Tracking e o Inventário para este tutorial. Se já ativou a funcionalidade anteriormente, este passo não é necessário.

>[!NOTE]
>Se os campos estiverem acinzentados, outra funcionalidade de Automação está ativada para o VM, e deve utilizar o mesmo espaço de trabalho e conta de Automação.

1. Selecione **máquinas Virtuais** e selecione um VM da lista.
2. No menu esquerdo, selecione **Inventário** em **Operações**. A página de inventário abre.

    ![Ativar a mudança](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

3. Escolha o espaço de trabalho [log Analytics.](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) Este espaço de trabalho recolhe dados que são gerados por funcionalidades como O Rastreio de Alterações e Inventário. A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

    [!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

4. Selecione a conta Automação para utilizar.

5. Configure a localização para a implantação.

5. Clique **em ativar** para implementar a funcionalidade para o seu VM. 

Durante a configuração, o VM é aprovisionado com o agente Log Analytics para Windows e um Trabalhador híbrido do livro de [corridas](automation-hybrid-runbook-worker.md). Ativar o Rastreio de Alterações e O Inventário pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.

Após a ativação da funcionalidade, as informações sobre o software instalado e as alterações nos fluxos vm para os registos do Monitor Azure.
Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

## <a name="use-change-tracking-and-inventory-in-azure-monitor-logs"></a>Utilizar rastreio de alterações e inventário em registos do Monitor Azure

Alterar o Rastreio e o Inventário gera dados de registo que são enviados para os registos do Monitor Azure. Para pesquisar os registos executando consultas, selecione **Log Analytics** no topo da página de rastreio de alteração. Alterar os dados de rastreio é armazenado sob o tipo `ConfigurationChange` .

O exemplo seguinte Log Analytics consulta devolve todos os serviços Windows que foram parados.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo nos registos do Monitor Azure, consulte os [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Configurar o Controlo de alterações

Com o rastreio de alterações, escolha os ficheiros e as teclas de registo para recolher e rastrear utilizando **as definições de Editar** na parte superior da página de rastreio de alteração no seu VM. Pode adicionar chaves de registo do Windows, ficheiros Windows ou ficheiros Linux para rastrear na página de Configuração workspace.

> [!NOTE]
> Tanto o rastreio de alterações como o inventário utilizam as mesmas definições de recolha, e as definições estão configuradas a nível de espaço de trabalho.

### <a name="add-a-windows-registry-key"></a>Adicione uma chave de registo do Windows

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
   > O valor **de Gestão** para a propriedade **Links** não é recomendado. A obtenção de conteúdo do ficheiro não é suportada.

## <a name="enable-activity-log-connection"></a>Ativar a ligação do Registo de atividades

1. Na página Controlo de alterações da sua VM, selecione **Gerir Ligação de Registo de Atividades**. 

2. Na página de registo da Atividade Azure, clique em **Ligar** para ligar o Rastreio de Alterações e o Inventário ao registo de atividade do Azure para o seu VM.

3. Navegue na página 'Visão Geral' para o seu VM e selecione **Stop** para parar o vM. 

4. Quando lhe for pedido, selecione **Sim** para parar a VM. 

5. Quando o VM estiver em lodo, selecione **Iniciar** a reinseri-lo. Parar e iniciar um VM regista um evento no seu Registo de Atividades. 

## <a name="view-changes"></a>Ver as alterações

1. Navegue de volta para a página de rastreio de alteração e selecione o separador **Eventos** na parte inferior da página. 

2. Após algum tempo, os eventos de rastreio de mudança são mostrados na tabela e na tabela. O gráfico mostra as alterações ocorridas ao longo do tempo. O gráfico de linha na parte superior exibe eventos de Log de Atividade Azure. Cada linha de gráficos de barras representa um tipo de mudança rastreável diferente. Estes tipos são daemons Linux, ficheiros, chaves de registo do Windows, software e serviços Windows. O separador de mudança mostra os detalhes das alterações apresentadas, com a alteração mais recente apresentada primeiro.

    ![Ver eventos no portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

3. Note que houve múltiplas alterações no sistema, incluindo alterações nos serviços e software. Pode utilizar os filtros na parte superior da página para filtrar os resultados consoante o **Tipo de alteração** ou um intervalo de tempo.

    ![Lista de alterações ao VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

4. Selecione uma alteração **WindowsServices.** Esta seleção abre a página Change Details mostrando detalhes sobre a mudança e os valores antes e depois da alteração. Neste caso, o serviço Proteção de Software foi parado.

    ![Ver detalhes da alteração no portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Configurar alertas

Ver as alterações no portal do Azure pode ser útil, mas poder ser alertado quando ocorre uma alteração, como um serviço parado é mais vantajoso. Vamos adicionar um alerta para um serviço de paragem. 

1. No portal Azure, vá ao **Monitor.** 

2. Selecione **Alertas** em **Serviços Partilhados**e clique **+ Nova regra de alerta**.

3. Clique em **Selecionar** para escolher um recurso. 

4. Na página Selecione uma página de recursos, escolha **o Log Analytics** do Filtro por menu de dropdown do tipo de **recurso.** 

5. Selecione o seu espaço de trabalho Log Analytics e, em seguida, clique em **Done**.

    ![Selecionar um recurso](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

6. Clique **em Adicionar condição**.

7. Na tabela na página lógica de sinal Configurar, selecione pesquisa de **registo personalizado**. 

8. Introduza a seguinte consulta na caixa de texto de consulta de pesquisa:

    ```loganalytics
    ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
    ```

    Esta consulta devolve os computadores que tinham o serviço W3SVC parado no período de tempo especificado.

9. Para **limiar** sob **lógica de alerta,** insira **0**. Quando terminar, clique em **Done**.

    ![Configurar lógica de sinal](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

10. Selecione **Criar Novos** em **Grupos de Ação**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir, mas não estão limitadas a notificações por e-mail, runbooks, webhooks e muitas mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

11. Em **detalhes de Alerta,** insira um nome e descrição para o alerta. 

12. Defina a **Gravidade** para **Informativa (Grav 2)**, **Aviso (Grav 1)** ou **Crítica (Grav 0)**.

13. Na caixa **Nome do grupo de ações**, introduza um nome para o alerta e um nome abreviado. O nome abreviado é utilizado em vez de um nome de grupo de ação completo quando as notificações são enviadas através deste grupo.

14. Para **Ações**, insira um nome para a ação, como administradores de **e-mail.** 

15. Para **TIPO DE AÇÃO,** selecione **Email/SMS/Push/Voice**. 

16. Para **MAIS DETALHES,** selecione **Editar detalhes**.

    ![Adicionar grupo de ação](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

17. No painel de e-mail/SMS/Push/Voice, introduza um nome, selecione a caixa de verificação de **e-mail** e, em seguida, introduza um endereço de e-mail válido. Quando terminar, **clique** ok no painel e, em seguida, clique **OK** na página do grupo de ação Adicionar.

18. Para personalizar o assunto do e-mail de alerta, selecione **Personalizar Ações.** 

19. Para **criar a regra**, selecione o assunto do **e-mail**e, em seguida, escolha criar a regra de **alerta**. O alerta informa-o quando a implementação de uma atualização for concluída com êxito e que máquinas faziam parte da execução dessa implementação de atualização. A imagem seguinte é um e-mail de exemplo recebido quando o serviço W3SVC para.

    ![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Ativar o rastreio e o inventário de alterações para um VM
> * Pesquisar serviços parados em registos de alterações
> * Configurar o Controlo de alterações
> * Ativar a ligação de registo de atividade
> * Acionar um evento
> * Ver as alterações
> * Configurar alertas

Continue a ver com a visão geral da funcionalidade De Rastreio e Inventário de Alterações para saber mais sobre o mesmo.

> [!div class="nextstepaction"]
> [Alterar visão geral do rastreio e do inventário](automation-change-tracking.md)