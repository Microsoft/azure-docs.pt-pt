---
title: Ligue o Gestor de Configuração ao monitor Azure | Microsoft Docs
description: Este artigo mostra os passos para ligar o Gestor de Configuração ao espaço de trabalho no Azure Monitor e começar a analisar dados.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/30/2020
ms.openlocfilehash: 7302f61fce6763fdbb41c2b4899dea749ac3225f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031077"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Ligue o Gestor de Configuração ao Monitor Azure
Pode ligar o ambiente do Microsoft Endpoint Configuration Manager ao Azure Monitor para sincronizar os dados de recolha de dispositivos e fazer referência a estas coleções no Azure Monitor e na Azure Automation.  

> [!IMPORTANT]
> A partir da versão De Configuração Manager 2010, esta funcionalidade é depreciada.<!-- 8269855 --> Para obter mais informações, consulte [funcionalidades removidas e preprecadas para o Gestor de Configuração](/mem/configmgr/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).

## <a name="prerequisites"></a>Pré-requisitos

O Azure Monitor suporta o atual ramo do Gestor de Configuração, versão 1606 e superior.

>[!NOTE]
>A funcionalidade para ligar o Gestor de Configuração a um espaço de trabalho Do Log Analytics é opcional e não é ativada por padrão. Deve ativar esta função antes de a utilizar. Para mais informações, consulte [Enable optional features from updates](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Descrição geral da configuração

Os passos seguintes resumem os passos para configurar a integração do Gestor de Configuração com o Azure Monitor.  

1. No Azure Ative Directory, registe o Gestor de Configuração como aplicação Web e/ou Web API e certifique-se de que tem a chave secreta do cliente e do cliente a partir do registo do Azure Ative Directory. Consulte [o portal Use para criar aplicações e chefes de serviço do Ative Directory que possam aceder](../../active-directory/develop/howto-create-service-principal-portal.md) a recursos para informações detalhadas sobre como realizar este passo.

2. No Azure Ative Directory, [conceda o Gestor de Configuração (a aplicação web registada) com permissão para aceder ao Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. No Gestor de Configurações, adicione uma ligação utilizando o assistente **Azure Services.**

4. [Descarregue e instale o agente Log Analytics para](#download-and-install-the-agent) o Windows no computador que executa a função do sistema de sistema de ligação do ponto de ligação do Gestor de Configuração. O agente envia dados do Gestor de Configuração para o espaço de trabalho do Log Analytics no Azure Monitor.

5. No Azure Monitor, [as coleções de importação do Gestor de Configuração](#import-collections) como grupos informáticos.

6. No Azure Monitor, veja os dados do Gestor de Configuração como [grupos de computador](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Grant Configuration Manager com permissões para Registar Analytics

No procedimento seguinte, concede o papel *de Contribuinte* no seu espaço de trabalho Log Analytics ao responsável pela aplicação e serviço de AD que criou anteriormente para o Gestor de Configuração. Se ainda não tiver um espaço de trabalho, consulte [Criar um espaço de trabalho no Azure Monitor](./quick-create-workspace.md) antes de prosseguir. Isto permite que o Gestor de Configuração autuça e ligue ao seu espaço de trabalho Log Analytics.  

> [!NOTE]
> Tem de especificar permissões no espaço de trabalho do Log Analytics para o Gestor de Configurações. Caso contrário, recebe uma mensagem de erro quando utiliza o assistente de configuração no Gestor de Configurações.
>

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione o **Log Analytics**.

2. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho para modificar.

3. A partir do painel esquerdo, selecione **Controlo de acesso (IAM)**.

4. Na página Access control (IAM), clique em **Adicionar a atribuição de funções** e aparece o painel de atribuição de **funções Add.**

5. No painel de atribuição de **funções Add,** na lista de **drop-down** role selecione a **função Contribuinte.**  

6. No âmbito da lista **de acesso a** drop-down, selecione a aplicação 'Gestor de Configuração' criada anteriormente em AD e, em seguida, clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Descarregue e instale o agente

Reveja o artigo [Conecte os computadores Windows ao Azure Monitor em Azure](../agents/agent-windows.md) para entender os métodos disponíveis para instalar o agente Log Analytics para o Windows no computador que hospeda a função de sistema de sistema de ligação do Gestor de Configuração.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Conecte o Gestor de Configuração para registar o espaço de trabalho do Analytics

>[!NOTE]
> Para adicionar uma ligação Log Analytics, o ambiente do Gestor de Configuração deve ter um [ponto de ligação](/configmgr/core/servers/deploy/configure/about-the-service-connection-point) de serviço configurado para o modo online.

> [!NOTE]
> Tem de ligar o local de primeira linha da sua hierarquia ao Monitor Azure. Se ligar um site primário autónomo ao Azure Monitor e, em seguida, adicionar um site de administração central ao seu ambiente, tem de eliminar e recriar a ligação dentro da nova hierarquia.

1. No espaço de trabalho da **Administração** do Gestor de Configuração, selecione **Clouds Services** e, em seguida, selecione **Azure Services**. 

2. Clique à direita **nos Serviços Azure** e, em seguida, selecione **Serviços Configure Azure**. Aparece a página **Serviços Configure Azure.** 
   
3. No ecrã **Geral,** confirme que fez as seguintes ações e que tem detalhes para cada item e, em seguida, selecione **Next**.

4. Na página Azure Services do Assistente de Serviços Azure:

    1. Especifique um **nome** para o objeto no Gestor de Configuração.
    2. Especifique uma **descrição** opcional para ajudá-lo a identificar o serviço.
    3. Selecione o **conector OMS** do serviço Azure .

    >[!NOTE]
    >O OMS é agora referido como Log Analytics, que é uma característica do Azure Monitor.

5. Selecione **Seguinte** para continuar a página de propriedades da aplicação Azure do Assistente de Serviços Azure.

6. Na página da **Aplicação** do Assistente de Serviços Azure, primeiro selecione o ambiente Azure da lista e, em seguida, clique em **Importar**.

7. Na página **De Aplicações de Importação,** especifique as seguintes informações:

    1. Especifique o **Nome do Inquilino AZURE** para a aplicação.

    2. Especificar para **Azure AD Tenant ID** o inquilino Azure AD. Pode encontrar esta informação na página Azure Ative Directory **Properties.** 

    3. Especificar para **nome de candidatura** o nome da aplicação.

    4. Especificar para **o ID** do Cliente, o ID de aplicação da aplicação AD criada criada anteriormente.

    5. Especificar para **chave Secreta,** a chave secreta do Cliente da aplicação AD criada.

    6. Especificar para **a expiração da chave secreta,** a data de validade da sua chave.

    7. Especificar para **app ID URI**, a App ID URI da aplicação AD criada anteriormente.

    8. **Selecione Verificar** e à direita os resultados devem **apresentar-se com sucesso verificados!**

8. Na página **configuração,** reveja as informações para verificar as **subscrições do Azure**, **grupo de recursos Azure** e os campos **de espaço de trabalho da Suite de Gestão** de Operações são pré-povoados indicando que a aplicação AD AD AZure tem permissões suficientes no grupo de recursos. Se os campos estiverem vazios, indica que a sua aplicação não tem os direitos exigidos. Selecione as coleções do dispositivo para recolher e encaminhar para o espaço de trabalho e, em seguida, **selecione Adicionar**.

9. Reveja as opções na página **Confirmar as definições** e selecione **Seguinte** para começar a criar e configurar a ligação.

10. Quando a configuração estiver terminada, aparece a página **'Conclusão'.** Selecione **Fechar**. 

Depois de ter ligado o Gestor de Configuração ao Azure Monitor, pode adicionar ou remover coleções e ver as propriedades da ligação.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar propriedades de conexão do espaço de trabalho Log Analytics

Se uma palavra-passe ou chave secreta do cliente expirar ou se perder, terá de atualizar manualmente as propriedades de ligação Log Analytics.

1. No espaço de trabalho da **Administração** do Gestor de Configuração, selecione **serviços de nuvem** e, em seguida, selecione **o Conector OMS** para abrir a página **OMS Connection Properties.**
2. Nesta página, clique no separador **Azure Ative Directory** para ver o seu **Inquilino,** **ID do Cliente,** **expiração da chave secreta do cliente.** **Verifique a** **chave secreta do** cliente se expirou.

## <a name="import-collections"></a>Coleções de importação

Depois de ter adicionado uma ligação Log Analytics ao Gestor de Configuração e ter instalado o agente no computador que executa a função do sistema de sistema de ligação do conjunto de pontos de ligação do Gestor de Configuração, o próximo passo é importar coleções do Gestor de Configuração no Azure Monitor como grupos informáticos.

Depois de ter concluído a configuração inicial para importar coleções de dispositivos da sua hierarquia, a informação de recolha é recuperada a cada 3 horas para manter a adesão atual. Pode optar por desativar isto a qualquer momento.

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **log analytics espaços de trabalho**.
2. Na sua lista de espaços de trabalho log Analytics, selecione o Gestor de Configuração do espaço de trabalho está registado.  
3. Selecione **Definições avançadas**.
4. Selecione **Grupos de Computadores** e, em seguida, selecione **SCCM**.  
5. Selecione **os membros de coleção do Gestor de Configuração de Importação** e, em seguida, clique em **Guardar**.  
   
    ![Screenshot dos Grupos de Computadores definições avançadas para S C C M contendo a opção para membros de coleção de Gestor de Configuração de Importação.](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Ver dados do Gestor de Configuração

Depois de ter adicionado uma ligação Log Analytics ao Gestor de Configuração e ter instalado o agente no computador que executa a função do sistema de sistema de ligação do conjunto de pontos de ligação do Gestor de Configuração, os dados do agente são enviados para o espaço de trabalho do Log Analytics no Azure Monitor. No Azure Monitor, as coleções do Gestor de Configuração aparecem como [grupos de computadores.](./computer-groups.md) Pode ver os grupos a partir da página **'Gestor de Configurações'** em **Definições\Grupos de computador**.

Depois de importadas as coleções, pode ver quantos computadores com membros de recolha foram detetados. Pode também ver o número de coleções que foram importadas.

![Screenshot dos Grupos de Computadores definições avançadas para S C C M mostrando a opção para membros de coleção de gestor de configuração de importação selecionados.](./media/collect-sccm/sccm-computer-groups02.png)

Quando clica em qualquer um, o editor de consulta de registos abre a exibição de todos os grupos importados ou de todos os computadores que pertencem a cada grupo. Utilizando [a Pesquisa de Registo,](./log-query-overview.md)pode efetuar uma análise mais aprofundada dos dados de membros da recolha.

## <a name="next-steps"></a>Passos seguintes

Utilize [a Pesquisa de Registo](./log-query-overview.md) para ver informações detalhadas sobre os dados do seu Gestor de Configuração.