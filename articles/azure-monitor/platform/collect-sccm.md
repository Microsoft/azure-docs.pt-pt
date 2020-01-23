---
title: Conectar Configuration Manager ao Azure Monitor | Microsoft Docs
description: Este artigo mostra as etapas para se conectar Configuration Manager ao espaço de trabalho no Azure Monitor e iniciar a análise de dados.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 5b5af034b116ec1cdcefc811630683c9f560c840
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513672"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Conectar Configuration Manager ao Azure Monitor
Você pode conectar seu ambiente de Configuration Manager de ponto de extremidade da Microsoft para Azure Monitor para sincronizar dados de coleta de dispositivos e fazer referência a essas coleções no Azure Monitor e na automação do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

O Azure Monitor dá suporte Configuration Manager Branch atual, versão 1606 e superior.

>[!NOTE]
>O recurso para se conectar Configuration Manager com um espaço de trabalho de Log Analytics é opcional e não é habilitado por padrão. Você deve habilitar esse recurso antes de usá-lo. Para mais informações, consulte [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Descrição geral da configuração

As etapas a seguir resumem as etapas para configurar a integração de Configuration Manager com o Azure Monitor.  

1. No Azure Active Directory, registre Configuration Manager como um aplicativo Web e/ou aplicativo de API Web e verifique se você tem a ID do cliente e a chave secreta do cliente do registro de Azure Active Directory. Ver [utilize o portal para criar o principal de aplicações e serviço que pode aceder aos recursos do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) para obter informações detalhadas sobre como realizar este passo.

2. No Azure Active Directory, [conceda Configuration Manager (o aplicativo Web registrado) com permissão para acessar Azure monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. Em Configuration Manager, adicione uma conexão usando o assistente de **Serviços do Azure** .

4. [Baixe e instale o agente do log Analytics para Windows](#download-and-install-the-agent) no computador que executa a função do sistema de site do ponto de conexão de serviço Configuration Manager. O agente envia Configuration Manager dados para o espaço de trabalho Log Analytics no Azure Monitor.

5. No Azure Monitor, [importe coleções de Configuration Manager](#import-collections) como grupos de computadores.

6. Em Azure Monitor, exiba dados de Configuration Manager como [grupos de computadores](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Gestor de configuração de concessão com permissões para o Log Analytics

No procedimento seguinte, pode conceder a *contribuinte* função na sua área de trabalho do Log Analytics para a aplicação do AD e principal de serviço que criou anteriormente para o Configuration Manager. Se você ainda não tiver um espaço de trabalho, consulte [criar um espaço de trabalho no Azure monitor antes de](../../azure-monitor/learn/quick-create-workspace.md) continuar. Isso permite que o Configuration Manager para se autenticar e ligar à sua área de trabalho do Log Analytics.  

> [!NOTE]
> Você deve especificar permissões no espaço de trabalho Log Analytics para Configuration Manager. Caso contrário, receberá uma mensagem de erro quando utiliza o Assistente de configuração no Configuration Manager.
>

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.

2. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho para modificar.

3. No painel esquerdo, selecione **controlo de acesso (IAM)** .

4. Na página de controle (IAM) de acesso, clique em **adicionar atribuição de função** e o **adicionar atribuição de função** é apresentado o painel.

5. Na **adicionar atribuição de função** painel, no **função** selecione na lista pendente o **contribuinte** função.  

6. Sob o **atribuir acesso aos** na lista pendente, selecione a aplicação do Configuration Manager que criou anteriormente no AD e, em seguida, clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Transfira e instale o agente

Examine o artigo [conectar computadores Windows ao Azure monitor no Azure](agent-windows.md) para entender os métodos disponíveis para instalar o agente de log Analytics para Windows no computador que hospeda a função do sistema de sites do ponto de conexão de serviço do Configuration Manager.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Conectar Configuration Manager ao espaço de trabalho Log Analytics

>[!NOTE]
> Para adicionar uma conexão de Log Analytics, seu ambiente de Configuration Manager deve ter um [ponto de conexão de serviço](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) configurado para o modo online.

> [!NOTE]
> Você deve conectar o site de camada superior em sua hierarquia para Azure Monitor. Se você conectar um site primário autônomo a Azure Monitor e, em seguida, adicionar um site de administração central ao seu ambiente, será necessário excluir e recriar a conexão na nova hierarquia.

1. No espaço de trabalho **Administração** de Configuration Manager, selecione **nuvens serviços** e, em seguida, selecione **Serviços do Azure**. 

2. Clique com o botão direito do mouse em **Serviços do Azure** e selecione **Configurar serviços do Azure**. A página **Configurar serviços do Azure** é exibida. 
   
3. Sobre o **gerais** ecrã, confirme que o tiver feito as seguintes ações e que tiver os detalhes para cada item, em seguida, selecione **próxima**.

4. Na página serviços do Azure do assistente de serviços do Azure:

    1. Especifique um **nome** para o objeto em Configuration Manager.
    2. Especifique uma **Descrição** opcional para ajudá-lo a identificar o serviço.
    3. Selecione o conector do **OMS**de serviço do Azure.

    >[!NOTE]
    >O OMS agora é conhecido como Log Analytics, que é um recurso do Azure Monitor.

5. Selecione **Avançar** para continuar na página de propriedades do aplicativo do Azure do assistente de serviços do Azure.

6. Na página **aplicativo** do assistente de serviços do Azure, primeiro selecione o ambiente do Azure na lista e clique em **importar**.

7. Na página **importar aplicativos** , especifique as seguintes informações:

    1. Especifique o **nome do locatário do Azure ad** para o aplicativo.

    2. Especifique para a **ID de locatário do Azure ad** o locatário do Azure AD. Você pode encontrar essas informações na página de **propriedades** Azure Active Directory. 

    3. Especifique para **nome do aplicativo** o nome do aplicativo.

    4. Especifique para **ID do cliente**, a ID do aplicativo criado pelo aplicativo do Azure ad criado anteriormente.

    5. Especifique para a **chave secreta**, a chave secreta do cliente do aplicativo do Azure ad criado.

    6. Especifique a **expiração da chave secreta**, a data de validade da sua chave.

    7. Especifique para o **URI da ID do aplicativo**, o URI da ID do aplicativo criado no Azure ad criado anteriormente.

    8. Selecione **verificar** e, à direita, os resultados devem mostrar **verificados com êxito!** .

8. Na página **configuração** , examine as informações para verificar se os campos **assinaturas do Azure**, **grupo de recursos do Azure**e **espaço de trabalho do Operations Management Suite** estão preenchidos previamente, indicando que o aplicativo Azure ad tem permissões suficientes no grupo de recursos. Se os campos estiverem vazios, indica que seu aplicativo não tem os direitos necessários. Selecione as coleções de dispositivos a serem coletadas e encaminhadas para o espaço de trabalho e, em seguida, selecione **Adicionar**.

9. Examine as opções na página **confirmar as configurações** e selecione **Avançar** para começar a criar e configurar a conexão.

10. Quando a configuração for concluída, a página **conclusão** será exibida. Selecione **Fechar**. 

Depois de vincular Configuration Manager ao Azure Monitor, você pode adicionar ou remover coleções e exibir as propriedades da conexão.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar propriedades de conexão do espaço de trabalho Log Analytics

Se uma senha ou chave secreta do cliente expirar ou for perdida, você precisará atualizar manualmente as propriedades de conexão do Log Analytics.

1. No espaço de trabalho **Administração** do Configuration Manager, selecione **serviços de nuvem** e, em seguida, selecione conector do **OMS** para abrir a página Propriedades de **conexão do OMS** .
2. Nesta página, clique nas **do Azure Active Directory** separador para ver seu **inquilino**, **ID de cliente**, **expiração da chave secreta do cliente**. **Certifique-se** sua **chave secreta do cliente** se tiver expirado.

## <a name="import-collections"></a>Importar coleções

Depois de adicionar uma conexão de Log Analytics para Configuration Manager e instalar o agente no computador que executa a função de sistema de site do ponto de conexão de serviço Configuration Manager, a próxima etapa é importar coleções de Configuration Manager no Azure Monitorar como grupos de computadores.

Depois de concluir a configuração inicial para importar coleções de dispositivos de sua hierarquia, as informações de coleção são recuperadas a cada três horas para manter a associação atual. Pode optar por desativar isso em qualquer altura.

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log Analytics espaços de trabalho**.
2. Na sua lista de áreas de trabalho do Log Analytics, selecione a área de trabalho do Configuration Manager está registado.  
3. Selecione **Definições avançadas**.
4. Selecione **grupos de computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **associações de coleção do Gestor de configuração de importação** e, em seguida, clique em **guardar**.  
   
    ![Grupos de computadores - separador do SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Vista de dados do Configuration Manager

Depois de adicionar uma conexão de Log Analytics para Configuration Manager e instalar o agente no computador que executa a função de sistema de site do ponto de conexão de serviço Configuration Manager, os dados do agente serão enviados para o espaço de trabalho Log Analytics no Azure Monitor. Em Azure Monitor, suas coleções de Configuration Manager aparecem como [grupos de computadores](../../azure-monitor/platform/computer-groups.md). Pode ver os grupos do **Configuration Manager** página sob **Settings\Computer grupos**.

Depois das coleções são importadas, pode ver quantos computadores com associações de coleção foram detetados. Também pode ver o número de coleções que foram importados.

![Grupos de computadores - separador do SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando você clica em qualquer um, o editor de consultas de log é aberto exibindo todos os grupos importados ou todos os computadores que pertencem a cada grupo. Usando a [pesquisa de log](../../azure-monitor/log-query/log-query-overview.md), você pode executar uma análise mais detalhada dos dados de associação da coleção.

## <a name="next-steps"></a>Passos seguintes

Uso [pesquisa de registos](../../azure-monitor/log-query/log-query-overview.md) para ver informações detalhadas sobre os seus dados do Configuration Manager.
