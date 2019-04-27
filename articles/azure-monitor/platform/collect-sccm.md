---
title: Ligar o Configuration Manager para o Azure Monitor | Documentos da Microsoft
description: Este artigo mostra-lhe os passos para ligar o Configuration Manager à área de trabalho no Azure Monitor e começar a analisar dados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741667"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Ligar o Configuration Manager para o Azure Monitor
Pode ligar o seu ambiente do System Center Configuration Manager para o Azure Monitor para sincronizar dados de coleção de dispositivo e fazem referência a essas coleções no Azure Monitor e automatização do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Monitor do Azure suporta o ramo atual do System Center Configuration Manager, versão 1606 e superior.  

## <a name="configuration-overview"></a>Descrição geral da configuração
Os passos seguintes resumem os passos para configurar a integração do Configuration Manager com o Azure Monitor.  

1. No portal do Azure, registe o Configuration Manager como uma aplicação de aplicativo Web e/ou Web API e certifique-se de que tem o ID de cliente e a chave secreta do cliente do registo do Azure Active Directory. Ver [utilize o portal para criar o principal de aplicações e serviço que pode aceder aos recursos do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) para obter informações detalhadas sobre como realizar este passo.
2. No portal do Azure, [conceder o Configuration Manager (a aplicação web registada) com permissão para aceder ao Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. No Configuration Manager, adicione uma ligação com o Assistente para adicionar ligação OMS.
4. No Configuration Manager, Atualize as propriedades de ligação se a chave secreta do cliente ou a palavra-passe nunca expira ou é perdida.
5. [Transfira e instale o Microsoft Monitoring Agent](#download-and-install-the-agent) no computador que executa a função de sistema de sites de ponto do Configuration Manager service ligação. O agente envia dados do Configuration Manager para a área de trabalho do Log Analytics no Azure Monitor.
6. No Azure Monitor [importar coleções do Configuration Manager](#import-collections) como grupos de computadores.
7. No Azure Monitor, ver dados do Configuration Manager como [grupos de computadores](computer-groups.md).

Pode ler mais sobre como ligar o Configuration Manager para o Azure Monitor na [sincronizar dados do Configuration Manager para a área de trabalho do Log Analytics da Microsoft](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Gestor de configuração de concessão com permissões para o Log Analytics
No procedimento seguinte, pode conceder a *contribuinte* função na sua área de trabalho do Log Analytics para a aplicação do AD e principal de serviço que criou anteriormente para o Configuration Manager.  Se ainda não tiver uma área de trabalho, consulte [criar uma área de trabalho no Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) antes de continuar.  Isso permite que o Configuration Manager para se autenticar e ligar à sua área de trabalho do Log Analytics.  

> [!NOTE]
> Tem de especificar permissões na área de trabalho do Log Analytics para o Configuration Manager. Caso contrário, receberá uma mensagem de erro quando utiliza o Assistente de configuração no Configuration Manager.
>

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.
2. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho para modificar.
3. No painel esquerdo, selecione **controlo de acesso (IAM)**.
4. Na página de controle (IAM) de acesso, clique em **adicionar atribuição de função** e o **adicionar atribuição de função** é apresentado o painel.
5. Na **adicionar atribuição de função** painel, no **função** selecione na lista pendente o **contribuinte** função.  
6. Sob o **atribuir acesso aos** na lista pendente, selecione a aplicação do Configuration Manager que criou anteriormente no AD e, em seguida, clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Transfira e instale o agente
Consulte o artigo [computadores Windows ligar para o Azure Monitor no Azure](agent-windows.md) para compreender os métodos disponíveis para instalar o Microsoft Monitoring Agent no computador que aloja o ponto de ligação de serviço do Configuration Manager função de sistema de sites.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Adicionar uma ligação do Log Analytics para o Configuration Manager
Para adicionar a ligação do Log Analytics, o ambiente do Configuration Manager tem de ter uma [ponto de ligação de serviço](https://technet.microsoft.com/library/mt627781.aspx) configurados para o modo online.

1. Na **Administration** área de trabalho do Configuration Manager, selecione **conector OMS**. Esta ação abre o **Adicionar Assistente de ligação do Log Analytics**. Selecione **Seguinte**.

   >[!NOTE]
   >OMS agora é referido como o Log Analytics, que é um recurso do Azure Monitor.
   
2. Sobre o **gerais** ecrã, confirme que o tiver feito as seguintes ações e que tiver os detalhes para cada item, em seguida, selecione **próxima**.

   1. No portal do Azure, se registra o Configuration Manager como uma aplicação de aplicativo Web e/ou Web API e de que tem o [ID de cliente do registo](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md).
   2. No portal do Azure, que criou uma chave secreta da aplicação para a aplicação registada no Azure Active Directory.  
   3. No portal do Azure, que forneceu a aplicação web registada com permissão para aceder à área de trabalho do Log Analytics no Azure Monitor.  
      ![Ligação à página geral de assistente do Log Analytics](./media/collect-sccm/sccm-console-general01.png)
3. Sobre o **do Azure Active Directory** ecrã, configure as definições de ligação para a área de trabalho do Log Analytics, fornecendo seu **inquilino**, **ID de cliente**e **Chave de segredo de cliente**, em seguida, selecione **próxima**.  
   ![Ligação à página do Log Analytics Assistente do Azure Active Directory](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Se fizemos todos os outros procedimentos com êxito, em seguida, as informações sobre o **configuração da ligação OMS** ecrã aparece automaticamente nesta página. Informações para as definições de ligação devem ser apresentado em sua **subscrição do Azure**, **grupo de recursos do Azure**, e **área de trabalho do Operations Management Suite**.  
   ![Ligação à página de ligação de análise do registo de assistente do Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. O assistente liga-se à área de trabalho do Log Analytics utilizando as informações que tenha de entrada. Selecione as coleções de dispositivos que pretende sincronizar com o serviço e, em seguida, clique em **adicionar**.  
   ![Selecionar coleções](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Verifique as definições de ligação no **resumo** ecrã, em seguida, selecione **próxima**. O **progresso** ecrã mostra o estado da ligação, em seguida, deve **concluída**.

> [!NOTE]
> Tem de ligar o site de nível superior da hierarquia para o Azure Monitor. Se se conectar um site primário autónomo para o Azure Monitor e, em seguida, adiciona um site de administração central ao seu ambiente, tem de eliminar e recriar a ligação dentro da hierarquia de novo.
>
>

Depois de ter ligado o Configuration Manager para o Azure Monitor, pode adicionar ou remover coleções e ver as propriedades da ligação.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar propriedades de ligação de área de trabalho do Log Analytics
Se uma chave secreta do cliente ou a palavra-passe nunca expira ou é perdida, terá de atualizar manualmente as propriedades de ligação do Log Analytics.

1. No Configuration Manager, navegue até **serviços Cloud**, em seguida, selecione **conector OMS** para abrir o **propriedades de ligação do OMS** página.
2. Nesta página, clique nas **do Azure Active Directory** separador para ver seu **inquilino**, **ID de cliente**, **expiração da chave secreta do cliente**. **Certifique-se** sua **chave secreta do cliente** se tiver expirado.

## <a name="import-collections"></a>Importar coleções
Depois de ter adicionado uma ligação do Log Analytics para o Configuration Manager e o agente instalado no computador com a ligação de serviço do Configuration Manager a função de sistema de sites de ponto, o próximo passo é importar coleções do Configuration Manager no Azure Monitorizar como grupos de computadores.

Depois de concluir a configuração inicial para importar coleções de dispositivos da hierarquia, as informações de associação de coleção são recuperadas em três horas para manter a associação atual. Pode optar por desativar isso em qualquer altura.

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **áreas de trabalho do Log Analytics**.
2. Na sua lista de áreas de trabalho do Log Analytics, selecione a área de trabalho do Configuration Manager está registado.  
3. Selecione **Definições avançadas**.
4. Selecione **grupos de computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **associações de coleção do Gestor de configuração de importação** e, em seguida, clique em **guardar**.  
   ![Grupos de computadores - separador do SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Vista de dados do Configuration Manager
Depois de ter adicionado uma ligação do Log Analytics para o Configuration Manager e instalar o agente no computador que executa a função de sistema de sites de ponto do Configuration Manager service ligação, os dados do agente são enviados para a área de trabalho do Log Analytics no Azure Monitor. No Azure Monitor, suas coleções do Configuration Manager são apresentados como [grupos de computadores](../../azure-monitor/platform/computer-groups.md). Pode ver os grupos do **Configuration Manager** página sob **Settings\Computer grupos**.

Depois das coleções são importadas, pode ver quantos computadores com associações de coleção foram detetados. Também pode ver o número de coleções que foram importados.

![Grupos de computadores - separador do SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando clicar em qualquer um, é aberta a pesquisa, exibindo a todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md), pode iniciar uma análise aprofundada de dados do Configuration Manager.

## <a name="next-steps"></a>Passos Seguintes
* Uso [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para ver informações detalhadas sobre os seus dados do Configuration Manager.
