---
title: Criar um serviço QnA Maker - QnA Maker
description: Antes de poder criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro tem de configurar um serviço QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode configurar o serviço QnA Maker.
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 663cbce0e096c6189d97cf7872d466383d272f06
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650423"
---
# <a name="manage-qna-maker-resources"></a>Gerir os recursos do Fabricante qna

Antes de poder criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro tem de configurar um serviço QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos numa subscrição pode configurar o serviço QnA Maker.

Uma compreensão sólida dos seguintes conceitos é útil antes de criar o seu recurso:

* [Recursos da QnA Maker](../Concepts/azure-resources.md)
* [Chaves de autoria e publicação](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço QnA Maker

Este procedimento cria os recursos Azure necessários para gerir o conteúdo da base de conhecimentos. Depois de completar estes passos, encontrará as chaves de _subscrição_ na página **Keys** para o recurso no portal Azure.

1. Inscreva-se no portal Azure e crie um recurso [QnA Maker.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Selecione **Criar** depois de ler os termos e condições:

    ![Criar um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. No **QnA Maker,** selecione os níveis e regiões apropriados:

    ![Criar um novo serviço QnA Maker - regiões e escalão de preço](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No campo **Nome,** insira um nome único para identificar este serviço QnA Maker. Este nome também identifica o ponto final do Fabricante qnA a que as suas bases de conhecimento estarão associadas.
    * Escolha a **Subscrição** sob a qual será implantado o recurso QnA Maker.
    * Selecione o **nível de preços** para os serviços de gestão qnA Maker (portal e APIs de gestão). Veja [mais detalhes sobre os preços do SKU.](https://aka.ms/qnamaker-pricing)
    * Crie um novo **grupo de Recursos** (recomendado) ou utilize um existente para implantar este recurso QnA Maker. A QnA Maker cria vários recursos Azure. Quando se cria um grupo de recursos para deter estes recursos, pode facilmente encontrar, gerir e eliminar esses recursos pelo nome do grupo de recursos.
    * Selecione uma **localização do grupo Recursos**.
    * Escolha o **nível** de preços de Pesquisa do serviço de Pesquisa Cognitiva Azure. Se a opção Free tier não estiver disponível (aparece reduzida), significa que já tem um serviço gratuito implementado através da sua subscrição. Nesse caso, terá de começar pelo nível básico. Consulte os detalhes dos preços da [Pesquisa Cognitiva Azure](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o local de **pesquisa** onde pretende que os índices de Pesquisa Cognitiva Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar a localização que escolhe para a Pesquisa Cognitiva Azure.
    * No campo de nome da **App,** insira um nome para a sua instância de Serviço de Aplicações Azure.
    * Por predefinição, o Serviço de Aplicações não se aplica ao nível padrão (S1). Pode alterar o plano após a criação. Saiba mais sobre os preços do Serviço de [Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)
    * Escolha a localização do **Site** onde o Serviço de Aplicações será implementado.

        > [!NOTE]
        > O Local de **Pesquisa** pode diferir da Localização do **Site**.

    * Escolha se pretende ou não ativar insights de **aplicação**. Se os **Insights de Aplicação** estiverem ativados, o QnA Maker recolhe telemetria no tráfego, registos de chat e erros.
    * Escolha o local de insights da **App** onde o recurso Application Insights será implementado.
    * Para medidas de poupança de custos, você pode [compartilhar](#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos Azure criados para a QnA Maker.

1. Depois de todos os campos serem validados, selecione **Criar**. O processo pode levar alguns minutos para ser concluído.

1. Após a implementação ser concluída, verá os seguintes recursos criados na sua subscrição:

   ![Recurso criado um novo serviço QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso do tipo _Serviços Cognitivos_ tem as suas chaves _de subscrição._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Encontre chaves de subscrição no portal Azure

Pode visualizar e redefinir as suas chaves de subscrição a partir do portal Azure, onde criou o recurso QnA Maker.

1. Vá ao recurso QnA Maker no portal Azure e selecione o recurso que tem o tipo _de Serviços Cognitivos:_

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ir para **Chaves:**

    ![Chave de subscrição](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Encontre chaves de ponto final no portal QnA Maker

O ponto final está na mesma região que o recurso porque as chaves do ponto final são usadas para fazer uma chamada para a base de conhecimento.

As chaves endpoint podem ser geridas a partir do [portal QnA Maker](https://qnamaker.ai).

1. Inscreva-se no [portal QnA Maker,](https://qnamaker.ai)vá ao seu perfil e, em seguida, selecione **as definições**de Serviço:

    ![Chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Ver ou redefinir as chaves:

    > [!div class="mx-imgBorder"]
    > ](../media/qnamaker-how-to-key-management/Endpoint-keys1.png) de gerente-chave ![Endpoint

    >[!NOTE]
    >Refresque as chaves se achar que foram comprometidas. Isto pode exigir alterações correspondentes à sua aplicação de cliente ou código bot.

### <a name="upgrade-qna-maker-sku"></a>Upgrade QnA Maker SKU

Quando quiser ter mais perguntas e respostas na sua base de conhecimentos, para além do seu nível atual, atualize o seu nível de preços de serviço QnA Maker.

Para atualizar a gestão da QnA Maker SKU:

1. Vá ao seu recurso QnA Maker no portal Azure e selecione **o nível**de preços .

    ![Recurso QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha o SKU apropriado e prima **Selecione**.

    ![Preços do Fabricante qna](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Upgrade App Service

 Quando a sua base de conhecimento precisa de servir mais pedidos da sua aplicação de cliente, atualize o seu nível de preços do Serviço de Aplicações.

Pode [escalar](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou escalar o Serviço de Aplicações.

Vá ao recurso do Serviço de Aplicações no portal Azure e selecione a opção **Scale up** ou **Scale out** conforme necessário.

![Escala de serviço de aplicativo sapateiro QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva Azure

Se pretende ter muitas bases de conhecimento, atualize o seu nível de preços do serviço de Pesquisa Cognitiva Azure.

Atualmente, não é possível realizar uma atualização no local da pesquisa Azure SKU. No entanto, pode criar um novo recurso de pesquisa Azure com o SKU desejado, restaurar os dados com o novo recurso e, em seguida, ligá-lo à pilha De Fabricante QnA. Para tal, siga estes passos:

1. Crie um novo recurso de pesquisa Azure no portal Azure e selecione o SKU desejado.

    ![Recurso de pesquisa QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices do seu recurso de pesquisa original azure para o novo. Consulte o código de amostra de [restauro de cópia de segurança](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois de restaurados os dados, vá ao seu novo recurso de pesquisa Azure, selecione **Keys,** e escreva o **Nome** e a **tecla Administrador:**

    ![Chaves de pesquisa QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para ligar o novo recurso de pesquisa Azure à pilha De Fabricante QnA, vá à instância qnA Maker App Service.

    ![QnA Maker App Service instância](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **as definições** de Aplicação e modifique as definições nos campos **AzureSearchName** e **AzureSearchAdminKey** a partir do passo 3.

    ![Definição de serviço de aplicativo soro de máquinas QnA](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reiniciar a instância do Serviço de Aplicações.

    ![Reinício da instância qnA maker app](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Obtenha as últimas atualizações do tempo de execução

O tempo de execução do QnAMaker faz parte da instância do Serviço de Aplicações Azure que é implementada quando [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal Azure. As atualizações são feitas periodicamente o tempo de execução. A instância QnA Maker App Service encontra-se em modo de atualização automática após o lançamento da extensão do site de abril de 2019 (versão 5+). Esta atualização foi concebida para tratar do tempo de inatividade ZERO durante as atualizações.

Pode verificar a sua versão atual em https://www.qnamaker.ai/UserSettings. Se a sua versão for mais antiga do que a versão 5.x, tem de reiniciar o Serviço de Aplicações para aplicar as últimas atualizações:

1. Vá ao seu serviço QnAMaker (grupo de recursos) no [portal Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![grupo de recursos QnAMaker Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância do Serviço de Aplicações e abra a secção **De visão geral.**

    > [!div class="mx-imgBorder"]
    > ![qnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Reiniciar o Serviço de Aplicações. O processo de atualização deve terminar em alguns segundos. Quaisquer aplicações ou bots dependentes que utilizem este serviço QnAMaker não estarão disponíveis para os utilizadores finais durante este período de reinício.

    ![Reinício da instância de Serviço de Aplicações QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configure O Fabricante qnA para usar diferentes recursos de pesquisa cognitiva

Se criar um serviço QnA e as suas dependências (como search) através do portal, é criado um serviço de Pesquisa para si e ligado ao serviço QnA Maker. Depois de criados estes recursos, pode atualizar a definição do Serviço de Aplicações para utilizar um serviço de Pesquisa anteriormente existente e remover o que acabou de criar.

O recurso qnA Maker's **App Service** utiliza o recurso De pesquisa cognitiva. Para alterar o recurso de Pesquisa Cognitiva utilizado pelo Fabricante QnA, é necessário alterar a definição no portal Azure.

1. Obtenha a **chave Deadministração** e o **Nome** do recurso de Pesquisa Cognitiva que pretende utilizar o Fabricante qnA.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e encontre o Serviço de **Aplicações** associado ao seu recurso QnA Maker. Ambos com o mesmo nome.

1. Selecione **Definições,** em **seguida, Configurar**. Isto mostrará todas as definições existentes para o Serviço de Aplicações do Fabricante qnA.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do portal Azure mostrando configurações de configuração do Serviço de Aplicações](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Alterar os valores para as seguintes teclas:

    * **AzureSearchAdminKey**
    * **Nome AzureSearch**

1. Para utilizar as novas definições, é necessário reiniciar o serviço app. Selecione **a visão geral**e, em seguida, selecione **Reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do portal Azure reiniciando o Serviço de Aplicações após configurações mudarem](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se criar um serviço QnA através de modelos do Gestor de Recursos Azure, pode criar todos os recursos e controlar a criação do Serviço de Aplicações para utilizar um serviço de Pesquisa existente.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [serviço de Aplicações](../../../app-service/index.yml) e [Pesquisa.](../../../search/index.yml)

> [!div class="nextstepaction"]
> [Criar e publicar uma base de conhecimento](../Quickstarts/create-publish-knowledge-base.md)