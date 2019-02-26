---
title: Resolver problemas relacionados com alterações numa máquina virtual do Azure | Microsoft Docs
description: Utilize o Controlo de Alterações para resolver problemas relacionados com alterações numa máquina virtual do Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: alterações, controlo, automatização
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 77dda5f113a10f0bfb59457b1059563c58db0dde
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816949"
---
# <a name="troubleshoot-changes-in-your-environment"></a>Resolver problemas relacionados com alterações no seu ambiente

Neste tutorial, irá aprender a resolver problemas relacionados com alterações numa máquina virtual do Azure. Ao ativar o Controlo de alterações, pode controlar as alterações efetuadas ao nível do software, dos ficheiros, dos daemons do Linux, dos Serviços do Windows e das chaves do Registo do Windows nos seus computadores.
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

* Uma subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um [conta de Automatização](automation-offering-get-started.md) para reter os runbooks de observador e ação e a Tarefa de Observador.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de alterações e Inventário

Primeiro, tem de ativar o Controlo de alterações e Inventário para a VM para este tutorial. Se ativou anteriormente outra solução de automatização para uma VM, este passo não é necessário.

1. No menu da esquerda, selecione **Máquinas virtuais** e selecione uma VM da lista
1. No menu da esquerda, na secção **OPERAÇÕES**, clique em **Inventário**. A página **Controlo de alterações** abre-se.

![Ativar a mudança](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) O ecrã **Controlo de Alterações** abre-se. Configure a localização, a área de trabalho do Log Analytics e a conta de Automatização a utilizar e clique em **Ativar**. Se os campos estiverem desativados, significa que outra solução de automatização está ativada para a VM e terá de ser utilizada a mesmo área de trabalho e conta de Automatização.

A área de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) serve para recolher dados gerados pelas funcionalidades e serviços, como o Inventário.
A área de trabalho fornece uma localização única para rever e analisar dados de várias origens.

Durante a inclusão, a VM está aprovisionada com o Microsoft Monitoring Agent (MMA) e a função de trabalho híbrida.
Este agente serve para comunicar com a VM e obter informações sobre o software instalado.

A ativação da solução pode demorar até 15 minutos. Durante este período, não deve fechar a janela do browser.
Após a solução está ativada, informações sobre alterações e de software instalado na VM são transmitidas para registos do Azure Monitor.
Pode demorar entre 30 minutos e 6 horas até que os dados fiquem disponíveis para análise.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-change-tracking-in-azure-monitor-logs"></a>Utilizar o controlo de alterações no registos do Azure Monitor

Controlo de alterações gera dados de registo que são enviados para os registos do Azure Monitor.
Para pesquisar os registos através da execução de consultas, selecione **Log Analytics** na parte superior da janela **Controlo de alterações**.
Os dados de Controlo de alterações são armazenados abaixo do tipo **ConfigurationChange**.
O exemplo de consulta do Log Analytics que se segue devolve todos os Serviços do Windows que foram parados.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

Para saber mais sobre a execução e pesquisa de ficheiros de registo nos registos do Azure Monitor, consulte [registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="configure-change-tracking"></a>Configurar o Controlo de alterações

O Controlo de alterações permite controlar as alterações de configuração na VM. Os passos que se seguem mostram como configurar o controlo de ficheiros e chaves do Registo.

Para escolher os ficheiros e as chaves do Registo que devem ser recolhidos e controlados, selecione **Editar definições** na parte superior da página **Controlo de alterações**.

> [!NOTE]
> O Inventário e o Controlo de alterações utilizam as mesmas definições de coleção, sendo as definições configuradas ao nível da área de trabalho.

Na janela **Configuração da Área de Trabalho**, adicione as chaves do Registo do Windows, os ficheiros do Windows ou os ficheiros do Linux a controlar, conforme descrito nas três secções que se seguem.

### <a name="add-a-windows-registry-key"></a>Adicionar uma chave do Registo do Windows

1. No separador **Registo do Windows**, selecione **Adicionar**.
    É aberta a janela **Adicionar Registo do Windows para Controlo de Alterações**.

1. Em **Adicionar Registo do Windows para Controlo de Alterações**, introduza as informações da chave a controlar e clique em **Guardar**

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Chave do Registo do Windows   | O caminho para verificar o ficheiro, por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="add-a-windows-file"></a>Adicionar um ficheiro do Windows

1. No separador **Ficheiros do Windows**, selecione **Adicionar**. É aberta a janela **Adicionar Ficheiro do Windows para Controlo de Alterações**.

1. Em **Adicionar Ficheiro do Windows para Controlo de Alterações**, introduza as informações do ficheiro ou diretório a controlar e clique em **Guardar**

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a definição foi aplicada        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "c:\temp\\\\*.txt"<br>Também pode utilizar variáveis de ambiente, tais como "%winDir%\System32\\\*.*"         |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **TRUE** ou **False**.|

### <a name="add-a-linux-file"></a>Adicionar um ficheiro do Linux

1. No separador **Ficheiros do Linux**, selecione **Adicionar**. É aberta a janela **Adicionar Ficheiro do Linux para Controlo de Alterações** .

1. Em **Adicionar Ficheiro do Linux para Controlo de Alterações**, introduza as informações do ficheiro ou diretório a controlar e clique em **Guardar**

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
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **TRUE** ou **False**.|

   > [!NOTE]
   > A opção “Gerir” ligações não é recomendada. A obtenção de conteúdo do ficheiro não é suportada.

## <a name="enable-activity-log-connection"></a>Ativar a ligação do Registo de atividades

Na página **Controlo de alterações** da sua VM, selecione **Gerir Ligação de Registo de Atividades**. Esta tarefa abre a página **Registo de atividades do Azure**. Selecione **Ligar** para ligar o Controlo de alterações ao registo de atividades do Azure para a sua VM.

Com esta definição ativada, navegue para a página **Descrição Geral** da sua VM e selecione **Parar** para parar a VM. Quando lhe for pedido, selecione **Sim** para parar a VM. Quando esta estiver desalocada, selecione **Iniciar** para reiniciar a sua VM.

As operações de paragem e início de uma VM registam um evento no respetivo registo de atividades. Regresse à página **Controlo de alterações**. Selecione o separador **Eventos** na parte inferior da página. Passado algum tempo, os eventos são apresentados no gráfico e na tabela. Tal como no passo anterior, é possível selecionar cada um dos eventos para ver informações detalhadas sobre o evento.

![Ver detalhes da alteração no portal](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Ver as alterações

Depois de ativar a solução Controlo de alterações e Inventário, pode ver os resultados na página **Controlo de alterações**.

Na sua VM, selecione **Controlo de alterações** em **OPERAÇÕES**.

![Captura de ecrã que mostra a lista de alterações da VM](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

O gráfico mostra as alterações ocorridas ao longo do tempo.
Após ter adicionado uma ligação do Registo de Atividades, o gráfico de linhas existente na parte superior apresenta os eventos do Registo de Atividades do Azure.
Cada linha do gráfico de barras representa um tipo diferente de alteração passível de controlo.
Estes tipos incluem daemons do Linux, ficheiros, chaves do Registo do Windows, software e serviços do Windows.
O separador Alterações mostra os detalhes das alterações apresentadas na visualização por ordem descendente de data/hora em que a alteração ocorreu (mais recente primeiro).
No separador **Eventos**, a tabela apresenta os eventos do Registo de Atividades ligados e os respetivos detalhes, com o mais recente listado primeiro.

Como é possível constatar nos resultados, foram efetuadas várias alterações no sistema, incluindo alterações ao nível do software e serviços. Pode utilizar os filtros na parte superior da página para filtrar os resultados consoante o **Tipo de alteração** ou um intervalo de tempo.

Selecione uma alteração do tipo **WindowsServices** para abrir a janela **Detalhes da Alteração**. A janela Detalhes da Alteração mostra detalhes sobre a alteração e os valores antes e após a alteração. Neste caso, o serviço Proteção de Software foi parado.

![Ver detalhes da alteração no portal](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Configurar alertas

Ver as alterações no portal do Azure pode ser útil, mas poder ser alertado quando ocorre uma alteração, como um serviço parado é mais vantajoso.

Para adicionar um alerta a um serviço parado, no portal do Azure, aceda a **Monitorizar**. E, em **Serviços Partilhados**, selecione **Alertas** e clique em **+Nova regra de alerta**

Clique em **selecione** para escolher um recurso. Na **selecionar um recurso** , selecione **do Log Analytics** do **filtrar por tipo de recurso** pendente. Selecione a sua área de trabalho do Log Analytics e, em seguida, selecione **Concluído**.

![Selecionar um recurso](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Clique em **adicionar condição**, na **configurar lógica de sinal** página, na tabela, selecione **pesquisa de registos personalizado**. Introduza a seguinte consulta na caixa de texto Consulta de pesquisa:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

Esta consulta devolve os computadores que tinham o serviço W3SVC parado no período de tempo especificado.

Em **Lógica de alerta**, para **Limiar**, introduza **0**. Quando tiver terminado, selecione **Concluído**.

![Configurar lógica de sinal](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

Sob **grupos de ação**, selecione **criar nova**. Um grupo de ação é um grupo de ações que podem ser utilizadas em vários alertas. As ações podem incluir, mas não estão limitadas a notificações por e-mail, runbooks, webhooks e muitas mais. Para saber mais sobre grupos de ação, veja [Criar e gerir grupos de ações](../azure-monitor/platform/action-groups.md).

Sob **detalhes do alerta**, introduza um nome e descrição do alerta. Defina a **Gravidade** para **Informativa (Grav 2)**, **Aviso (Grav 1)** ou **Crítica (Grav 0)**.

Na caixa **Nome do grupo de ações**, introduza um nome para o alerta e um nome abreviado. O nome abreviado é utilizado em vez de um nome de grupo de ações completo quando as notificações são enviadas ao utilizar deste grupo.

Em **Ações**, introduza um nome para a ação, como **Administradores por E-mail**. Em **TIPO DE AÇÃO**, selecione **E-Mail/SMS/Push/voz**. Em **DETALHES**, selecione **Editar detalhes**.

![Adicionar grupo de ação](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

Na página **E-mail/SMS/Push/Voz**, introduza um nome. Selecione a caixa de verificação **E-mail** e, em seguida, introduza um endereço de e-mail válido. Clique em **OK** na página **E-mail/SMS/Push/Voz** e clique em **OK** na página **Adicionar grupo de ação**.

Para personalizar o assunto do e-mail de alerta, em **Criar regra**, em **Personalizar Ações**, selecione **Assunto do e-mail**. Quanto terminar, selecione **Criar regra de alerta**. O alerta informa-o quando a implementação de uma atualização for concluída com êxito e que máquinas faziam parte da execução dessa implementação de atualização.

A imagem seguinte é um e-mail de exemplo recebido quando o serviço W3SVC para.

![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>Passos Seguintes

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

