---
title: Configure o serviço QnA Maker - QnA Maker
description: Este documento descreve configurações avançadas para os seus recursos QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220156"
---
# <a name="configure-qna-maker-resources"></a>Configure recursos do Fabricante QnA

O utilizador pode configurar o QnA Maker para utilizar um recurso de pesquisa cognitiva diferente. Também podem configurar as definições do serviço app se estiverem a utilizar o QnA Maker GA.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configure o Fabricante QnA para usar diferentes recursos de Pesquisa Cognitiva

Se criar um serviço QnA e as suas dependências (como procurar) através do portal, é criado um serviço de Pesquisa para si e ligado ao serviço QnA Maker. Após a criação destes recursos, pode atualizar a definição de Serviço de Aplicações para utilizar um serviço de Pesquisa anteriormente existente e remover o que acabou de criar.

O recurso do Serviço de **Aplicações** do QnA Maker utiliza o recurso De Pesquisa Cognitiva. Para alterar o recurso de Pesquisa Cognitiva utilizado pelo QnA Maker, é necessário alterar a definição no portal Azure.

1. Obtenha a **chave de administração** e o **nome** do recurso de Pesquisa Cognitiva que pretende que o Fabricante QnA utilize.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e encontre o **Serviço de Aplicações** associado ao seu recurso QnA Maker. Ambos com o mesmo nome.

1. Selecione **Definições** e, em **seguida, Configuração**. Isto mostrará todas as definições existentes para o Serviço de Aplicações do Criador da QnA.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do portal Azure mostrando definições de configuração do Serviço de Aplicações](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Alterar os valores para as seguintes teclas:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Para utilizar as novas definições, é necessário reiniciar o serviço app. Selecione **a visão geral** e, em seguida, selecione **Reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do portal Azure reiniciando o Serviço de Aplicações após alteração das definições de configuração](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se criar um serviço QnA através dos modelos Azure Resource Manager, pode criar todos os recursos e controlar a criação do Serviço de Aplicações para utilizar um serviço de Pesquisa existente.

Saiba mais sobre como configurar as definições da Aplicação de Serviço de Aplicações de [Aplicações.](../../../app-service/configure-common.md#configure-app-settings)

### <a name="get-the-latest-runtime-updates"></a>Obtenha as últimas atualizações de tempo de execução

O tempo de execução do QnAMaker faz parte da instância do Serviço de Aplicações Azure que é implementada quando [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal Azure. As atualizações são efetuadas periodicamente ao tempo de funcionação. A instância do Serviço de Aplicações QnA Maker encontra-se em modo de atualização automática após o lançamento da extensão do site de abril de 2019 (versão 5+). Esta atualização foi concebida para tratar do tempo de inatividade ZERO durante as atualizações.

Pode verificar a sua versão atual em https://www.qnamaker.ai/UserSettings . Se a sua versão for mais antiga que a versão 5.x, tem de reiniciar o Serviço de Aplicações para aplicar as atualizações mais recentes:

1. Aceda ao seu serviço QnAMaker (grupo de recursos) no [portal Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Grupo de recursos QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância do Serviço de Aplicações e abra a secção **'Vista Geral'.**

    > [!div class="mx-imgBorder"]
    > ![Instância do Serviço de Aplicações QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Reiniciar o Serviço de Aplicações. O processo de atualização deve terminar em alguns segundos. Quaisquer aplicações dependentes ou bots que utilizem este serviço QnAMaker não estarão disponíveis para os utilizadores finais durante este período de reinício.

    ![Reiniciar a instância do Serviço de Aplicações QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Configuração do serviço de aplicações de configuração ociosa para evitar o tempo limite

O serviço de aplicações, que serve o tempo de previsão do QnA Maker para uma base de conhecimento publicada, tem uma configuração de tempo de tempo ociosa, que predefine para sair automaticamente se o serviço estiver inativo. Para o QnA Maker, isto significa que o seu tempo de previsão gera API desativada ocasionalmente vezes após períodos sem tráfego.

Para manter a aplicação de previsão de ponta final carregada mesmo quando não há tráfego, coloque o inativo para sempre ligado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione o serviço de aplicações do seu QnA Maker. Terá o mesmo nome que o recurso QnA Maker, mas terá um **tipo** diferente de Serviço de Aplicações.
1. Encontrar **Definições** e selecionar **Configuração**.
1. No painel de configuração, selecione **definições gerais,** em seguida, encontre **Sempre ligado** e selecione **On** como o valor.

    > [!div class="mx-imgBorder"]
    > ![No painel de configuração, selecione as definições de **General**, em seguida, encontre **Sempre on*** e selecione **On** como o valor.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **Selecione Guardar** para guardar a configuração.
1. É-lhe perguntado se pretende reiniciar a aplicação para utilizar a nova definição. Selecione **Continuar**.

Saiba mais sobre como configurar as [definições gerais](../../../app-service/configure-common.md#configure-general-settings)do Serviço de Aplicações.

### <a name="business-continuity-with-traffic-manager"></a>Continuidade de negócio com gestor de tráfego

O principal objetivo do plano de continuidade do negócio é criar um ponto final resiliente de base de conhecimento, que garanta que não haja tempo de inacomprê-lo para o Bot ou para a aplicação que o consome.

> [!div class="mx-imgBorder"]
> ![Plano do QnA Maker bcp](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível, tal como acima representada, é a seguinte:

1. Crie dois [serviços paralelos da QnA Maker](set-up-qnamaker-service-azure.md) em [regiões emparelhadas Azure](../../../best-practices-availability-paired-regions.md).

1. [Faça uma cópia de segurança](../../../app-service/manage-backup.md) do seu serviço de aplicações QnA Maker e [restaure-o](../../../app-service/web-sites-restore.md) na configuração secundária. Isto garantirá que ambas as configurações funcionam com o mesmo nome de hospedeiro e teclas.

1. Mantenha os índices de pesquisa Azure primários e secundários em sincronização. Use a amostra do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como restaurar os índices Azure de backup.

1. Apoie os Insights de Aplicação utilizando [uma exportação contínua.](../../../azure-monitor/app/export-telemetry.md)

1. Uma vez configuradas as pilhas primárias e secundárias, utilize o [gestor de tráfego](../../../traffic-manager/traffic-manager-overview.md) para configurar os dois pontos finais e configure um método de encaminhamento.

1. Você precisaria de criar um segurança da camada de transporte (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), certificado para o seu ponto final de gestor de tráfego. [Ligue o certificado TLS/SSL](../../../app-service/configure-ssl-bindings.md) nos seus serviços de Aplicação.

1. Por fim, utilize o ponto final do gestor de tráfego no seu Bot ou App.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Configure o serviço QnA Maker gerido (Preview) para utilizar diferentes recursos de Pesquisa Cognitiva

Se criar um serviço QnA gerido (Preview) e as suas dependências (como procurar) através do portal, é criado um serviço de Pesquisa para si e ligado ao serviço gerido (Preview) do QnA Maker. Após a criação destes recursos, pode atualizar o serviço 'Procurar' no **separador Configuração.**

1. Aceda ao seu serviço gerido (Preview) do QnA Maker no portal Azure.

1. Selecione **Configuração** e selecione o serviço de Pesquisa Cognitiva Azure que pretende ligar com o seu serviço gerido (Preview) do Seu QnA Maker.

    ![Screenshot da página de configuração gerida (Preview) do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Clique em **Guardar**.

> [!NOTE]
> Se alterar o serviço de Pesquisa Azure associado ao QnA Maker, perderá o acesso a todas as bases de conhecimento já presentes no mesmo. Certifique-se de que exporta as bases de conhecimento existentes antes de alterar o serviço de Pesquisa Azure.

---
