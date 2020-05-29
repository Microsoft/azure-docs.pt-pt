---
title: Crie um serviço QnA Maker - QnA Maker
description: Antes de criar quaisquer bases de conhecimento do QnA Maker, tem primeiro de configurar um serviço QnA Maker em Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode criar um serviço QnA Maker.
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 521d0388e4ee739b1ac840e482174ac466781f5f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171179"
---
# <a name="manage-qna-maker-resources"></a>Gerir os recursos do Fabricante QnA

Antes de criar quaisquer bases de conhecimento do QnA Maker, tem primeiro de configurar um serviço QnA Maker em Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode criar um serviço QnA Maker.

Uma compreensão sólida dos seguintes conceitos é útil antes de criar o seu recurso:

* [Recursos do Fabricante QNA](../Concepts/azure-resources.md)
* [Chaves de autoria e publicação](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço QnA Maker

Este procedimento cria os recursos Azure necessários para gerir o conteúdo da base de conhecimento. Depois de completar estes passos, encontrará as teclas _de subscrição_ na página **Chaves** para o recurso no portal Azure.

1. Inscreva-se no portal Azure e crie um recurso [QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Selecione **Criar** depois de ler os termos e condições:

    ![Criar um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. No **QnA Maker,** selecione os níveis e regiões apropriados:

    ![Criar um novo serviço QnA Maker - nível de preços e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No campo **Nome,** insira um nome único para identificar este serviço QnA Maker. Este nome também identifica o ponto final do QnA Maker com o que as suas bases de conhecimento estarão associadas.
    * Escolha a **Subscrição** sob a qual o recurso QnA Maker será implantado.
    * Selecione o **nível de preços** para os serviços de gestão do QnA Maker (APIs de portal e gestão). Veja [mais detalhes sobre os preços da SKU.](https://aka.ms/qnamaker-pricing)
    * Crie um novo **grupo de Recursos** (recomendado) ou utilize um existente para implementar este recurso QnA Maker. A QnA Maker cria vários recursos Azure. Quando cria um grupo de recursos para deter estes recursos, pode facilmente encontrar, gerir e eliminar esses recursos pelo nome do grupo de recursos.
    * Selecione uma **localização do grupo de recursos**.
    * Escolha o **nível de preços de pesquisa** do serviço de Pesquisa Cognitiva Azure. Se a opção Free tier não estiver disponível (parece escurecida), significa que já tem um serviço gratuito implantado através da sua subscrição. Nesse caso, terás de começar pelo nível básico. Consulte os [detalhes dos preços da Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **local de pesquisa** onde deseja que os índices de Pesquisa Cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local que escolher para a Azure Cognitive Search.
    * No campo nome da **App,** insira um nome para a sua instância do Serviço de Aplicações Azure.
    * Por predefinição, o Serviço de Aplicações de Aplicação está em incumprimento do nível padrão (S1). Pode mudar o plano após a criação. Saiba mais sobre [os preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)
    * Escolha a **localização** do Site onde o Serviço de Aplicações será implementado.

        > [!NOTE]
        > O **Local de Pesquisa** pode diferir da Localização do **Site.**

    * Escolha se deseja ou não ativar insights de **aplicação**. Se **o Application Insights** estiver ativado, o QnA Maker recolhe telemetria no tráfego, registos de chat e erros.
    * Escolha o **local de insights** da App onde o recurso Application Insights será implementado.
    * Para medidas de poupança de custos, você pode [compartilhar](#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos Azure criados para a QnA Maker.

1. Depois de todos os campos serem validados, **selecione Criar.** O processo pode levar alguns minutos para ser concluído.

1. Após a implementação concluída, verá os seguintes recursos criados na sua subscrição:

   ![Recurso criou um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo _serviços cognitivos_ tem as suas chaves _de subscrição._


## <a name="find-subscription-keys-in-the-azure-portal"></a>Encontre chaves de subscrição no portal Azure

Pode visualizar e redefinir as suas teclas de subscrição a partir do portal Azure, onde criou o recurso QnA Maker.

1. Vá ao recurso QnA Maker no portal Azure e selecione o recurso que tem o tipo _de Serviços Cognitivos:_

    ![Lista de recursos do Fabricante QnA](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ir para **as Chaves:**

    ![Chave de subscrição](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Encontre chaves de ponto final no portal QnA Maker

O ponto final está na mesma região que o recurso porque as teclas de ponto final são usadas para fazer uma chamada para a base de conhecimento.

As teclas de ponto final podem ser geridas a partir do [portal QnA Maker](https://qnamaker.ai).

1. Inscreva-se no [portal QnA Maker,](https://qnamaker.ai)vá ao seu perfil e, em seguida, selecione **as definições de Serviço**:

    ![Chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Ver ou redefinir as suas chaves:

    > [!div class="mx-imgBorder"]
    > ![Gestor chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Refresque as chaves se acha que foram comprometidas. Isto pode requerer alterações correspondentes à aplicação do seu cliente ou código bot.

## <a name="upgrade-qna-maker-sku"></a>Upgrade QnA Maker SKU

Quando quiser ter mais perguntas e respostas na sua base de conhecimento, para além do seu nível atual, atualize o seu nível de preços de serviço QnA Maker.

Para atualizar a gestão da QnA Maker SKU:

1. Aceda ao seu recurso QnA Maker no portal Azure e **selecione o nível de preços**.

    ![Recurso do Fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha o SKU apropriado e prima **Select**.

    ![Preços do Fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Atualizar serviço de aplicativos

 Quando a sua base de conhecimentos precisar de servir mais pedidos a partir da sua aplicação de cliente, atualize o seu nível de preços do Serviço de Aplicações.

Pode [escalar](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou escalar o Serviço de Aplicações.

Aceda ao recurso Serviço de Aplicações no portal Azure e selecione a opção **Scale up** ou **Scale out** conforme necessário.

![Escala de serviço de aplicativos do Fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva Azure

Se planeia ter muitas bases de conhecimento, atualize o seu nível de preços do serviço de Pesquisa Cognitiva Azure.

Atualmente, não é possível realizar uma atualização no local da pesquisa Azure SKU. No entanto, pode criar um novo recurso de pesquisa Azure com o SKU pretendido, restaurar os dados para o novo recurso e, em seguida, ligá-lo à pilha QnA Maker. Para tal, siga estes passos:

1. Crie um novo recurso de pesquisa Azure no portal Azure e selecione o SKU desejado.

    ![QnA Maker Azure recurso de pesquisa](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaurar os índices do seu recurso de pesquisa original Azure para o novo. Consulte o [código de amostra de restauro de cópia de segurança](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois de os dados terem sido restaurados, aceda ao seu novo recurso de pesquisa Azure, selecione **Keys,** e escreva o **Nome** e a **tecla Admin**:

    ![Chaves de pesquisa do Fabricante QNA Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para ligar o novo recurso de pesquisa Azure à stack do QnA Maker, vá à instância do Serviço de Aplicações do Criador de QnA.

    ![QnA Maker App Service Instance](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **as definições** de aplicação e modifique as definições nos campos **AzureSearchName** e **AzureSearchAdminKey** a partir do passo 3.

    ![Definição do serviço de aplicações do fabricante QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie a instância do Serviço de Aplicações.

    ![Reiniciar a instância do Serviço de Aplicações do Criador QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Obtenha as últimas atualizações de tempo de execução

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

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configure o Fabricante QnA para usar diferentes recursos de Pesquisa Cognitiva

Se criar um serviço QnA e as suas dependências (como procurar) através do portal, é criado um serviço de Pesquisa para si e ligado ao serviço QnA Maker. Após a criação destes recursos, pode atualizar a definição de Serviço de Aplicações para utilizar um serviço de Pesquisa anteriormente existente e remover o que acabou de criar.

O recurso do Serviço de **Aplicações** do QnA Maker utiliza o recurso De Pesquisa Cognitiva. Para alterar o recurso de Pesquisa Cognitiva utilizado pelo QnA Maker, é necessário alterar a definição no portal Azure.

1. Obtenha a **chave de administração** e o **nome** do recurso de Pesquisa Cognitiva que pretende que o Fabricante QnA utilize.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e encontre o **Serviço de Aplicações** associado ao seu recurso QnA Maker. Ambos com o mesmo nome.

1. Selecione **Definições**e, em **seguida, Configuração**. Isto mostrará todas as definições existentes para o Serviço de Aplicações do Criador da QnA.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do portal Azure mostrando definições de configuração do Serviço de Aplicações](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Alterar os valores para as seguintes teclas:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Para utilizar as novas definições, é necessário reiniciar o serviço app. Selecione **a visão geral**e, em seguida, selecione **Reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do portal Azure reiniciando o Serviço de Aplicações após alteração das definições de configuração](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se criar um serviço QnA através dos modelos Azure Resource Manager, pode criar todos os recursos e controlar a criação do Serviço de Aplicações para utilizar um serviço de Pesquisa existente.

Saiba mais sobre como configurar as definições da Aplicação de Serviço de Aplicações de [Aplicações.](../../../app-service/configure-common.md#configure-app-settings)

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Configuração do serviço de aplicações de configuração ociosa para evitar o tempo limite

O serviço de aplicações, que serve o tempo de previsão do QnA Maker para uma base de conhecimento publicada, tem uma configuração de tempo de tempo ociosa, que predefine para o tempo de tempo automaticamente se o serviço estiver inativo. Para o QnA Maker, isto significa que o seu tempo de previsão gera API desativada ocasionalmente vezes após períodos sem tráfego.

Para manter a aplicação de previsão de ponta final carregada mesmo quando não há tráfego, coloque o inativo para sempre ligado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Procure e selecione o serviço de aplicações do seu QnA Maker. Terá o mesmo nome que o recurso QnA Maker, mas terá um **tipo** diferente de Serviço de Aplicações.
1. Encontrar **Definições** e selecionar **Configuração**.
1. No painel de configuração, selecione **definições gerais,** em seguida, encontre **Sempre ligado**e selecione **On** como o valor.

    > [!div class="mx-imgBorder"]
    > ![No painel de configuração, selecione as definições de **General**, em seguida, encontre **Sempre on*** e selecione **On** como o valor.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **Selecione Guardar** para guardar a configuração.
1. É-lhe perguntado se pretende reiniciar a aplicação para utilizar a nova definição. Selecione **Continuar**.

Saiba mais sobre como configurar as [definições gerais](../../../app-service/configure-common.md#configure-general-settings)do Serviço de Aplicações.

## <a name="business-continuity-with-traffic-manager"></a>Continuidade de negócio com gestor de tráfego

O principal objetivo do plano de continuidade do negócio é criar um ponto final resiliente de base de conhecimento, que garanta que não haja tempo de inacomprê-lo para o Bot ou para a aplicação que o consome.

> [!div class="mx-imgBorder"]
> ![Plano do QnA Maker bcp](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível, tal como acima representada, é a seguinte:

1. Crie dois [serviços paralelos da QnA Maker](set-up-qnamaker-service-azure.md) em [regiões emparelhadas Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

1. [Faça uma cópia de segurança](../../../app-service/manage-backup.md) do seu serviço de aplicações QnA Maker e [restaure-o](../../../app-service/web-sites-restore.md) na configuração secundária. Isto garantirá que ambas as configurações funcionam com o mesmo nome de hospedeiro e teclas.

1. Mantenha os índices de pesquisa Azure primários e secundários em sincronização. Use a amostra do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como restaurar os índices Azure de backup.

1. Apoie os Insights de Aplicação utilizando [uma exportação contínua.](../../../application-insights/app-insights-export-telemetry.md)

1. Uma vez configuradas as pilhas primárias e secundárias, utilize o [gestor de tráfego](../../../traffic-manager/traffic-manager-overview.md) para configurar os dois pontos finais e configure um método de encaminhamento.

1. Você precisaria de criar um segurança da camada de transporte (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), certificado para o seu ponto final de gestor de tráfego. [Ligue o certificado TLS/SSL](../../../app-service/configure-ssl-bindings.md) nos seus serviços de Aplicação.

1. Por fim, utilize o ponto final do gestor de tráfego no seu Bot ou App.

## <a name="delete-azure-resources"></a>Eliminar recursos do Azure

Se eliminar algum dos recursos Azure utilizados para as suas bases de conhecimento QnA Maker, as bases de conhecimento deixarão de funcionar. Antes de eliminar quaisquer recursos, certifique-se de que exporta as suas bases de conhecimento a partir da página **Definições.**

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre o [serviço app](../../../app-service/index.yml) e serviço [de pesquisa.](../../../search/index.yml)

> [!div class="nextstepaction"]
> [Saiba como autor com os outros](../how-to/collaborate-knowledge-base.md)
