---
title: Criar uma conta indexante de vídeo ligada ao Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta de Indexer de Vídeo ligada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/22/2020
ms.author: juliako
ms.openlocfilehash: 89360f062f0198a6b0c857395b4956caa1109e82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82097192"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Criar uma conta indexante de vídeo ligada ao Azure

Ao criar uma conta de Indexer de Vídeo, pode escolher uma conta de teste gratuita (onde obtém um certo número de minutos de indexação gratuitos) ou uma opção paga (onde não está limitado pela quota). Com um teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita aos utilizadores do site e até 2400 minutos de indexação gratuita aos utilizadores de API. Com a opção paga, cria uma conta De Indexer de Vídeo que está ligada à sua subscrição Azure e a uma conta Azure Media Services. Paga-se por minutos indexados, bem como os encargos relacionados com a conta mediática.

Este artigo mostra como criar uma conta de Indexer de Vídeo que esteja ligada a uma subscrição do Azure e a uma conta Azure Media Services. O tópico fornece passos para a ligação ao Azure utilizando o fluxo automático (padrão). Também mostra como se conectar a Azure manualmente (avançado).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

    Se ainda não tiver uma subscrição Azure, inscreva-se no [Azure Free Trial](https://azure.microsoft.com/free/).

* Um domínio azure ative diretório (Azure AD).

    Se não tiver um domínio Azure AD, crie este domínio com a sua subscrição Azure. Para mais informações, consulte Gerir nomes de [domínio personalizados no seu Anúncio Azure](../../active-directory/users-groups-roles/domains-manage.md)

* Um utilizador no seu domínio AD Azure com uma função de administrador de **aplicação.** Utilizará este membro ao ligar a sua conta de Indexer de Vídeo ao Azure.

    Este utilizador deve ser um utilizador de Anúncios Azure com uma conta de trabalho ou escola. Não use uma conta pessoal, como outlook.com, live.com ou hotmail.com.

    ![todos os utilizadores aAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Pré-requisitos adicionais para o fluxo automático

* Um utilizador e membro no seu domínio Azure AD.

    Utilizará este membro ao ligar a sua conta de Indexer de Vídeo ao Azure.

    Este utilizador deve ser membro na sua subscrição Azure com uma função **de Proprietário,** ou tanto para o **Colaborador** como para o **Administrador de Acesso ao Utilizador.** Um utilizador pode ser adicionado duas vezes, com duas funções. Uma vez com o Colaborador e uma vez com o Administrador de Acesso ao Utilizador.

    ![controlo de acesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Pré-requisitos adicionais para o fluxo manual

* Registe o fornecedor de recursos EventGrid utilizando o portal Azure.

    No [portal Azure,](https://portal.azure.com/)vá a **Assinaturas**->[subscrição]->**ResourceProviders**.

    Pesquisa por **Microsoft.Media** e **Microsoft.EventGrid**. Se não estiver no estado "Registado", clique em **Registar**. Leva alguns minutos para se registar.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Ligar ao Azure

> [!NOTE]
> Se a sua subscrição Azure utilizar a autenticação multifactor baseada em certificados, é crucial que realize os seguintes passos num dispositivo que tenha os certificados necessários instalados.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.

2. Selecione o novo botão **criar** conta:

    ![Criar nova conta de Indexer de Vídeo](./media/create-account/connect-to-azure.png)

3. Quando a lista de subscrições aparecer, selecione a subscrição que pretende utilizar.

    ![Ligue o Indexer de Vídeo a Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região azure a partir dos locais apoiados: West US 2, Norte da Europa ou Leste asiático.
5. Na **conta Azure Media Services,** escolha uma destas opções:

    * Para criar uma nova conta de Media Services, selecione **Criar um novo grupo de recursos.** Forneça um nome para o seu grupo de recursos.

        O Azure criará a sua nova conta na sua subscrição, incluindo uma nova conta de Armazenamento Azure. A sua nova conta De Serviços de Media tem uma configuração inicial padrão com um ponto final de streaming e 10 unidades reservadas S3.
    * Para utilizar uma conta de Serviços de Media existentes, selecione **Use o recurso existente**. Na lista de contas, selecione a sua conta.

        A sua conta media Services deve ter a mesma região que a sua conta de Indexer de Vídeo.

        > [!NOTE]
        > Para minimizar a duração da indexação e a baixa pontuação, é altamente recomendado ajustar o tipo e o número de [Unidades Reservadas](../previous/media-services-scale-media-processing-overview.md ) na sua conta de Serviços de Media a **10 Unidades Reservadas S3**. Consulte [o portal Use para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).

    * Para configurar manualmente a sua ligação, selecione o Switch para o link de **configuração manual.**

        Para obter informações detalhadas, consulte a secção [Connect to Azure manualmente](#connect-to-azure-manually-advanced-option) (opção avançada) que se segue.
6. Quando terminar, escolha **Connect**. Esta operação pode demorar alguns minutos.

    Depois de estar ligado ao Azure, a sua nova conta de Indexer de Vídeo aparece na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue na sua nova conta.

## <a name="connect-to-azure-manually-advanced-option"></a>Ligue-se manualmente ao Azure (opção avançada)

Se a ligação ao Azure falhar, pode tentar resolver o problema ligando-se manualmente.

> [!NOTE]
> É altamente recomendado ter as seguintes três contas na mesma região: a conta Do Indexer de Vídeo que está a ligar à conta de Media Services, bem como a conta de armazenamento Azure ligada à mesma conta de Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta de Media Services

1. Utilize o portal [Azure](https://portal.azure.com/) para criar uma conta Azure Media Services, conforme descrito na [Create a account](../previous/media-services-portal-create-account.md).

    Ao criar uma conta de armazenamento para a sua conta Media Services, selecione **StorageV2** para tipos de conta e **Geo-redundante (GRS)** para campos de replicação.

    ![Nova conta AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Certifique-se de anotar os recursos dos Serviços de Media e os nomes das contas. Vai precisar deles para os passos na próxima secção.

2. Ajuste o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) a **10 Unidades Reservadas S3** na conta de Serviços de Media que criou. Consulte [o portal Use para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).
3. Antes de poder reproduzir os seus vídeos na aplicação web do Video Indexer, tem de iniciar o **ponto final** de streaming padrão da nova conta de Media Services.

    Na nova conta de Media Services, selecione **pontos finais de Streaming**. Em seguida, selecione o ponto final de streaming e prima o arranque.

    ![Nova conta AMS](./media/create-account/create-ams-account2.png)

4. Para que o Indexer de Vídeo autentime com a API dos Media Services, é necessário criar uma aplicação aD. Os seguintes passos guiam-no através do processo de autenticação Azure AD descrito no [Get started com a autenticação Azure AD utilizando o portal Azure:](../previous/media-services-portal-get-started-with-aad.md)

    1. Na nova conta de Media Services, selecione **acesso API**.
    2. Selecione o método de [autenticação principal do serviço.](../previous/media-services-portal-get-started-with-aad.md)
    3. Obtenha a identificação do cliente e o segredo do cliente

        Depois de selecionar**Teclas**de **definições,**->adicione **Descrição,** prima **Guardar**, e o valor-chave fica povoado.

        Se a chave expirar, o proprietário da conta terá de contactar o suporte do Indexer de Vídeo para renovar a chave.

        > [!NOTE]
        > Certifique-se de que anota o valor-chave e o ID da aplicação. Vai precisar para os passos na próxima secção.

### <a name="connect-manually"></a>Conecte-se manualmente

No Indexante de Vídeo Connect a um diálogo de **subscrição Azure** da sua página [do Indexer](https://www.videoindexer.ai/) de Vídeo, selecione o Switch para o link de **configuração manual.**

No diálogo, forneça as seguintes informações:

|Definição|Descrição|
|---|---|
|Região da conta indexante de vídeo|O nome da região da conta do Indexer de Vídeo. Para um melhor desempenho e custos mais baixos, é altamente recomendado especificar o nome da região onde estão localizados os recursos da Azure Media Services e a conta de Armazenamento Azure. |
|Inquilino da AD Azure|O nome do inquilino da AD Azure, por exemplo, "contoso.onmicrosoft.com". A informação do inquilino pode ser recuperada do portal Azure. Coloque o cursor sobre o nome do utilizador inscrito no canto superior direito. Encontre o nome à direita do **Domínio.**|
|ID da subscrição|A assinatura Azure sob a qual esta ligação deve ser criada. O ID de subscrição pode ser recuperado do portal Azure. Selecione **Todos os serviços** no painel esquerdo e procure "subscrições". Selecione **Subscrições** e escolha o ID desejado na lista das suas subscrições.|
|Nome do grupo de recursos azure Media Services|O nome do grupo de recursos em que criou a conta de Media Services.|
|Nome de recurso de serviço de mídia|O nome da conta Azure Media Services que criou na secção anterior.|
|ID da aplicação|O ID de aplicação Azure AD (com permissões para a conta de Serviços de Media especificado) que criou na secção anterior.|
|Chave de aplicação|A chave de aplicação Azure AD que criou na secção anterior. |

## <a name="import-your-content-from-the-trial-account"></a>Importe o seu conteúdo da conta *experimental*

Ao [criar uma nova conta,](#connect-to-azure)tem a opção de importar o seu conteúdo da conta *experimental* para a nova conta. Se verificar a opção *de importação* na Create uma nova conta num diálogo de **subscrição Do Azure,** todos os media e personalizações de modelos de conteúdo serão copiados da conta *experimental* para a nova conta.

A capacidade de importar o conteúdo é válida tanto para abordagens automatizadas como manuais acima descritas.

> [!NOTE]
> O conteúdo só pode ser importado uma vez de cada conta.

## <a name="considerations"></a>Considerações

Aplicam-se as seguintes considerações relacionadas com os Serviços de Comunicação Social da Azure:

* Se se ligar automaticamente, verá um novo Grupo de Recursos, conta media services e uma conta de Armazenamento na sua subscrição Azure.
* Se ligar automaticamente, o Indexante de Vídeo define as **Unidades Reservadas** de mídia a 10 unidades S3:

    ![Unidades reservadas aos Serviços de Media](./media/create-account/ams-reserved-units.png)

* Se se ligar a uma conta de Serviços de Media existente, o Indexer de Vídeo não altera a configuração de **Unidades Reservadas** de Meios existentes.

   Poderá ter de ajustar o tipo e o número de Unidades Reservadas de Meios de acordo com a sua carga planeada. Tenha em mente que se a sua carga for alta e não tiver unidades ou velocidade suficientes, o processamento de vídeos pode resultar em falhas no tempo limite.

* Se se ligar a uma nova conta de Media Services, o Indexer de vídeo inicia automaticamente o **ponto final** de streaming predefinido na seleção:

    ![Ponto final de streaming de Serviços de Media](./media/create-account/ams-streaming-endpoint.png)

    Os pontos finais de streaming têm um tempo de arranque considerável. Por isso, pode demorar vários minutos a partir do momento em que ligou a sua conta ao Azure até que os seus vídeos possam ser transmitidos e assistidos na aplicação web do Indexer de Vídeo.

* Se se ligar a uma conta de Media Services existente, o Indexer de Vídeo não altera a configuração padrão de streaming endpoint. Se não houver **streaming endpoint,** não pode ver vídeos desta conta de Media Services ou no Indexer de Vídeo.

## <a name="next-steps"></a>Passos seguintes

Pode interagir programaticamente com a sua conta de teste e/ou com as suas contas de Indexer de Vídeo que estão ligadas ao Azure seguindo as instruções em: [Utilize APIs](video-indexer-use-apis.md).

Deve utilizar o mesmo utilizador da AD Azure que utilizou quando se liga ao Azure.