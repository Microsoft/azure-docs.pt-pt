---
title: Ligue o Gestor de Configuração ao Monitor Azure  Microsoft Docs
description: Este artigo mostra os passos para ligar o Gestor de Configuração ao espaço de trabalho no Monitor Azure e começar a analisar dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655261"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Conectar gestor de configuração ao Monitor Azure
Pode ligar o ambiente do Microsoft Endpoint Configuration Manager ao Azure Monitor para sincronizar dados de recolha de dispositivos e fazer referência a estas coleções no Azure Monitor e na Azure Automation.  

## <a name="prerequisites"></a>Pré-requisitos

O Azure Monitor suporta o atual ramo do Gestor de Configuração, versão 1606 e superior.

>[!NOTE]
>A funcionalidade de ligar o Gestor de Configuração a um espaço de trabalho de Log Analytics é opcional e não está ativada por padrão. Tem de ativar esta função antes de a utilizar. Para mais informações, consulte [Ativar funcionalidades opcionais a partir de atualizações](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Descrição geral da configuração

Os passos seguintes resumem os passos para configurar a integração do Gestor de Configuração com o Monitor Azure.  

1. No Diretório Ativo do Azure, registe o Gestor de Configuração como uma aplicação web e/ou aplicação Web API, e certifique-se de que tem a chave de id do cliente e segredo do cliente a partir do registo do Diretório Ativo azure. Consulte o [portal Use para criar aplicação de Diretório Ativo e diretor](../../active-directory/develop/howto-create-service-principal-portal.md) de serviço que possa aceder a recursos para informações detalhadas sobre como realizar este passo.

2. No Diretório Ativo do Azure, conceda ao Gestor de [Configuração (a aplicação web registada) permissão para aceder ao Monitor Azure](#grant-configuration-manager-with-permissions-to-log-analytics).

3. No 'Gestor de Configuração', adicione uma ligação utilizando o assistente dos **Serviços Azure.**

4. [Descarregue e instale o agente Log Analytics para Windows](#download-and-install-the-agent) no computador que executa a função do sistema de ponto de ligação do Gestor de Configuração. O agente envia dados do Gestor de Configuração para o espaço de trabalho do Log Analytics no Monitor Azure.

5. No Azure Monitor, [importa as coleções do Gestor de Configuração](#import-collections) como grupos informáticos.

6. No Monitor Azure, veja os dados do Gestor de Configuração como [grupos informáticos](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Gestor de configuração de concessão com permissões para o Log Analytics

No procedimento seguinte, concede o papel *de Colaborador* no seu espaço de trabalho Log Analytics ao diretor de aplicação e serviço aD que criou anteriormente para O Gestor de Configuração. Se ainda não tiver um espaço de trabalho, consulte Criar um espaço de [trabalho no Monitor Azure](../../azure-monitor/learn/quick-create-workspace.md) antes de prosseguir. Isso permite que o Configuration Manager para se autenticar e ligar à sua área de trabalho do Log Analytics.  

> [!NOTE]
> Deve especificar permissões no espaço de trabalho do Log Analytics para O Gestor de Configuração. Caso contrário, receberá uma mensagem de erro quando utiliza o Assistente de configuração no Configuration Manager.
>

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Log Analytics**.

2. Na lista de áreas de trabalho do Log Analytics, selecione a área de trabalho para modificar.

3. A partir do painel esquerdo, selecione controlo de **acesso (IAM)** .

4. Na página de controlo de acesso (IAM), clique em Adicionar a atribuição de **funções** e aparece o painel de atribuição de **funções Add.**

5. No painel de atribuição de **funções Add,** no âmbito da lista de abandono de **funções,** selecione a função **Contributiva.**  

6. No âmbito da **lista de acesso ao Signo para** a desistência, selecione a aplicação 'Gestor de Configuração' criada anteriormente em AD e, em seguida, clique em **OK**.  

## <a name="download-and-install-the-agent"></a>Transfira e instale o agente

Reveja o artigo [Connect Windows computers to Azure Monitor in Azure](agent-windows.md) para entender os métodos disponíveis para instalar o agente Log Analytics para Windows no computador que acolhe a função do sistema de ponto de ligação do Gestor de Configuração.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Connect Configuration Manager para log Analytics espaço de trabalho

>[!NOTE]
> Para adicionar uma ligação Log Analytics, o ambiente do Gestor de Configuração deve ter um ponto de [ligação](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) de serviço configurado para o modo on-line.

> [!NOTE]
> Deve ligar o site de topo da sua hierarquia ao Monitor Azure. Se ligar um site primário autónomo ao Azure Monitor e, em seguida, adicionar um site de administração central ao seu ambiente, tem de eliminar e recriar a ligação dentro da nova hierarquia.

1. No espaço de trabalho da **Administração** do Gestor de Configuração, selecione **Serviços Clouds** e, em seguida, selecione **Serviços Azure**. 

2. Clique à direita **nos Serviços Azure** e, em seguida, selecione **Serviços Configure Azure**. Aparece a página de **Serviços Configure Azure.** 
   
3. No ecrã **Geral,** confirme que fez as seguintes ações e que tem detalhes para cada item e, em seguida, selecione **Next**.

4. Na página dos Serviços Azure do Assistente de Serviços Azure:

    1. Especifique um **nome** para o objeto no Gestor de Configuração.
    2. Especifique uma **Descrição** opcional para o ajudar a identificar o serviço.
    3. Selecione o **conector OMS**do serviço Azure .

    >[!NOTE]
    >O OMS é agora referido como Log Analytics, que é uma característica do Monitor Azure.

5. Selecione **Next** para continuar na página de propriedades da aplicação Azure do Assistente de Serviços Azure.

6. Na página da **App** do Assistente de Serviços Azure, primeiro selecione o ambiente Azure da lista e, em seguida, clique **em Importar**.

7. Na página das **Aplicações de Importação,** especifique as seguintes informações:

    1. Especifique o Nome do **Inquilino AD Azure** para a aplicação.

    2. Especifique para **Azure AD Tenant ID** o inquilino da AD Azure. Pode encontrar esta informação na página Azure Ative Directory **Properties.** 

    3. Especificar para Nome de **Inscrição** o nome da candidatura.

    4. Especificar para **ID cliente**, o ID de aplicação da aplicação Azure AD criada anteriormente.

    5. Especifique para **a chave Secret**, a chave secreta cliente da aplicação Azure AD criada.

    6. Especifique para **A Caducidade da Chave Secreta,** a data de validade da sua chave.

    7. Especifique para **app ID URI**, o App ID URI da aplicação Azure AD criada anteriormente.

    8. Selecione **Verificar** e à direita os resultados devem mostrar **verificado com sucesso!**

8. Na página de **Configuração,** reveja as informações para verificar as **subscrições do Azure,** o grupo de **recursos Azure**e os campos de trabalho da Suite de Gestão de **Operações** são pré-povoados, indicando que a aplicação Azure AD tem permissões suficientes no grupo de recursos. Se os campos estiverem vazios, indica que a sua aplicação não tem os direitos necessários. Selecione as coleções do dispositivo para recolher e encaminhar para o espaço de trabalho e, em seguida, **selecione Adicionar**.

9. Reveja as opções na página **De confirmar** e selecione **Next** para começar a criar e configurar a ligação.

10. Quando a configuração estiver terminada, aparece a página **'Conclusão'.** Selecione **Fechar**. 

Depois de ter ligado o Gestor de Configuração ao Monitor Azure, pode adicionar ou remover coleções e ver as propriedades da ligação.

## <a name="update-log-analytics-workspace-connection-properties"></a>Atualizar propriedades de ligação do espaço de trabalho Log Analytics

Se uma palavra-passe ou chave secreta do cliente expirar ou estiver perdida, terá de atualizar manualmente as propriedades de ligação log analytics.

1. No espaço de trabalho da **Administração** do Gestor de Configuração, selecione **Serviços cloud** e, em seguida, selecione **OMS Connector** para abrir a página **OMS Connection Properties.**
2. Nesta página, clique no separador **Diretório Ativo Azure** para ver o seu **Inquilino,** **ID do Cliente,** Expiração da **chave secreta do cliente.** **Verifique a** **chave secreta do cliente** se tiver expirado.

## <a name="import-collections"></a>Importar coleções

Depois de ter adicionado uma ligação Log Analytics ao Gestor de Configuração e instalado o agente no computador que executa a função do site de ponto de ligação do Gestor de Configuração, o próximo passo é importar coleções do Gestor de Configuração em Azure Monitor como grupos informáticos.

Depois de ter concluído a configuração inicial para importar coleções de dispositivos da sua hierarquia, as informações de recolha são recuperadas a cada 3 horas para manter a adesão atual. Pode optar por desativar isso em qualquer altura.

1. No portal do Azure, clique em **Todos os serviços**, que se encontra no canto superior esquerdo. Na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.
2. Na sua lista de áreas de trabalho do Log Analytics, selecione a área de trabalho do Configuration Manager está registado.  
3. Selecione **Definições avançadas**.
4. Selecione **Grupos de Computador** e, em seguida, selecione **SCCM**.  
5. Selecione membros de **recolha de gestorde configuração de importação** e, em seguida, clique em **Guardar**.  
   
    ![Grupos de computadores - separador do SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Vista de dados do Configuration Manager

Depois de ter adicionado uma ligação Log Analytics ao Gestor de Configuração e instalado o agente no computador que executa a função do sistema de ponto de ligação do Gestor de Configuração, os dados do agente são enviados para o espaço de trabalho do Log Analytics no Monitor Azure. No Monitor Azure, as suas coleções do Gestor de Configuração aparecem como [grupos informáticos](../../azure-monitor/platform/computer-groups.md). Pode visualizar os grupos a partir da página 'Gestor de **Configuração'** em **Definições\Grupos de Computadores**.

Depois das coleções são importadas, pode ver quantos computadores com associações de coleção foram detetados. Também pode ver o número de coleções que foram importados.

![Grupos de computadores - separador do SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Quando clica em qualquer um deles, o editor de consulta de log abre exibindo todos os grupos importados ou todos os computadores que pertencem a cada grupo. Utilizando a Pesquisa de [Registos,](../../azure-monitor/log-query/log-query-overview.md)pode efetuar uma análise mais aprofundada dos dados de adesão à recolha.

## <a name="next-steps"></a>Passos seguintes

Utilize a Pesquisa de [Registos](../../azure-monitor/log-query/log-query-overview.md) para visualizar informações detalhadas sobre os dados do seu Gestor de Configuração.
