---
title: Criar uma conta de Video Indexer no portal do Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como criar uma conta de Video Indexer no portal do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 0f67b2e37e264febf11f3fa55b4469d392c59712
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815678"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Criar uma conta de Video Indexer conectada ao Azure

Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com a opção pago, você cria uma conta de Video Indexer que está conectada à sua assinatura do Azure e uma conta dos serviços de mídia do Azure. Irá pagar pelos minutos indexados, bem como pelas cobranças relacionadas com a Conta de Multimédia. 

Este artigo mostra como criar uma conta de Video Indexer que está vinculada a uma assinatura do Azure e uma conta dos serviços de mídia do Azure. O tópico fornece etapas para se conectar ao Azure usando o fluxo automático (padrão). Ele também mostra como se conectar ao Azure manualmente (avançado).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

    Se você ainda não tiver uma assinatura do Azure, Inscreva-se para a [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

* Um domínio Azure Active Directory (AD).

    Se você não tiver um domínio do Azure AD, crie esse domínio com sua assinatura do Azure. Para obter mais informações, consulte [Gerenciando nomes de domínio personalizados no seu Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Um usuário em seu domínio do Azure AD com uma função de **administrador de aplicativos** . Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

    Esse usuário deve ser um usuário do Azure AD com uma conta corporativa ou de estudante, não uma conta pessoal, como outlook.com, live.com ou hotmail.com.

    ![todos os usuários do AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Pré-requisitos adicionais para o fluxo automático

Um usuário e um membro em seu domínio do Azure AD. Você usará esse membro ao conectar sua conta do Video Indexer ao Azure.

Esse usuário deve ser membro de sua assinatura do Azure com uma função de **proprietário** ou as funções de **administrador de acesso de usuário** e **colaborador** . Um usuário pode ser adicionado duas vezes, com duas funções. Uma vez com o colaborador e uma vez com o administrador de acesso do usuário.

![controle de acesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Pré-requisitos adicionais para o fluxo manual

Registre o provedor de recursos EventGrid usando o portal do Azure.

Na [portal do Azure](https://portal.azure.com/), acesse **assinaturas**-> [assinatura]->**ResourceProviders**. 

Procure **Microsoft. Media** e **Microsoft. EventGrid**. Se não estiver no estado "registrado", clique em **registrar**. É preciso registrar alguns minutos.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Ligar ao Azure

> [!NOTE]
> Se sua assinatura do Azure usa a autenticação multifator baseada em certificado, é crucial que você execute as etapas a seguir em um dispositivo que tenha os certificados necessários instalados.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.

2. Clique no botão **criar nova conta** :

    ![conectar-se ao Azure](./media/create-account/connect-to-azure.png)

3. Quando a lista de assinaturas for exibida, selecione a assinatura que você deseja usar.

    ![conectar Video Indexer ao Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região do Azure nos locais com suporte: Oeste dos EUA 2, Europa Setentrional ou Ásia Oriental.
5. Em **conta dos serviços de mídia do Azure**, escolha uma destas opções:

    * Para criar uma nova conta de serviços de mídia, selecione **criar novo grupo de recursos**. Forneça um nome para seu grupo de recursos.

        O Azure criará sua nova conta em sua assinatura, incluindo uma nova conta de armazenamento do Azure. Sua nova conta de serviços de mídia tem uma configuração inicial padrão com um ponto de extremidade de streaming e 10 unidades reservadas S3.
    * Para usar uma conta dos serviços de mídia existente, selecione **usar recurso existente**. Na lista contas, selecione sua conta.

        Sua conta de serviços de mídia deve ter a mesma região que sua conta de Video Indexer. 

        > [!NOTE]
        > Para minimizar a duração da indexação e a baixa taxa de transferência, é altamente recomendável ajustar o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) a **10 unidades reservadas S3** na sua conta de serviços de mídia. Consulte [usar o portal para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).

    * Para configurar manualmente a conexão, clique no link **alternar para configuração manual** .

        Para obter informações detalhadas, consulte a seção [conectar-se ao Azure manualmente](#connect-to-azure-manually-advanced-option) (opção avançada) a seguir.
6. Quando terminar, escolha **conectar**. Essa operação pode levar alguns minutos. 

    Depois que você estiver conectado ao Azure, sua nova conta de Video Indexer aparecerá na lista de contas:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue até sua nova conta

## <a name="connect-to-azure-manually-advanced-option"></a>Conectar-se ao Azure manualmente (opção avançada)

Se a conexão com o Azure falhar, você poderá tentar solucionar o problema conectando-se manualmente.

> [!NOTE]
> É altamente recomendável ter as três contas a seguir na mesma região: a conta de Video Indexer que você está conectando com a conta dos serviços de mídia, bem como a conta de armazenamento do Azure conectada à mesma conta de serviços de mídia.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta dos serviços de mídia

1. Use o portal [do Azure](https://portal.azure.com/) para criar uma conta dos serviços de mídia do Azure, conforme descrito em [criar uma conta](../previous/media-services-portal-create-account.md).

    Ao criar uma conta de armazenamento para sua conta de serviços de mídia, selecione **StorageV2** para os campos tipo de conta e **REDUNDÂNCIA geográfica (GRS)** para replicação.

    ![nova conta do AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Certifique-se de anotar os nomes de conta e recursos dos serviços de mídia. Você precisará dela para as etapas na próxima seção.

2. Ajuste o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) para **10 unidades reservadas S3** na conta dos serviços de mídia que você criou. Consulte [usar o portal para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).
3. Antes de executar seus vídeos no aplicativo Web Video Indexer, você deve iniciar o **ponto de extremidade de streaming** padrão da nova conta dos serviços de mídia.

    Na nova conta dos serviços de mídia, clique em **pontos de extremidade de streaming**. Selecione o ponto de extremidade de streaming e pressione Iniciar.

    ![nova conta do AMS](./media/create-account/create-ams-account2.png)

4. Para Video Indexer autenticar com a API dos serviços de mídia, um aplicativo do AD precisa ser criado. As etapas a seguir guiarão você pelo processo de autenticação do Azure AD descrito em [introdução à autenticação do Azure ad usando o portal do Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Na nova conta dos serviços de mídia, selecione **acesso à API**.
    2. Selecione o [método de autenticação da entidade de serviço](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Obtenha a ID do cliente e o segredo do cliente, conforme descrito na seção [obter a ID do cliente e o segredo do cliente](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) .

        Depois de selecionar **as configurações**->**chaves**, adicionar **Descrição**, pressione **salvar**, o valor da chave será preenchido.

        Se a chave expirar, o proprietário da conta precisará entrar em contato com Video Indexer suporte para renovar a chave.

        > [!NOTE]
        > Certifique-se de anotar o valor da chave e a ID do aplicativo. Você precisará dela para as etapas na próxima seção.

### <a name="connect-manually"></a>Conectar manualmente

Na caixa de diálogo **conectar Video indexer a uma assinatura do Azure** da página [Video indexer](https://www.videoindexer.ai/) , selecione o link **alternar para configuração manual** .

Na caixa de diálogo, forneça as seguintes informações:

|Definição|Descrição|
|---|---|
|Região da conta do Video Indexer|O nome da região da conta do Video Indexer. Para obter um melhor desempenho e reduzir os custos, é altamente recomendável especificar o nome da região em que o recurso dos serviços de mídia do Azure e a conta de armazenamento do Azure estão localizados. |
|Locatário do Azure Active Directory (AAD)|O nome do locatário do Azure AD, por exemplo, "contoso.onmicrosoft.com". As informações do locatário podem ser recuperadas do portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito. Localize o nome à direita do **domínio**.|
|ID da subscrição|A assinatura do Azure sob a qual essa conexão deve ser criada. A ID da assinatura pode ser recuperada do portal do Azure. Clique em **todos os serviços** no painel esquerdo e procure "assinaturas". Selecione **assinaturas** e escolha a ID desejada na lista de suas assinaturas.|
|Nome do grupo de recursos dos serviços de mídia do Azure|O nome do grupo de recursos no qual você criou a conta dos serviços de mídia.|
|Nome do recurso do serviço de mídia|O nome da conta dos serviços de mídia do Azure que você criou na seção anterior.|
|ID da aplicação|A ID do aplicativo do Azure AD (com permissões para a conta de serviços de mídia especificada) que você criou na seção anterior.|
|Chave da Aplicação|A chave de aplicativo do Azure AD que você criou na seção anterior. |

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas aos serviços de mídia do Azure se aplicam:

* Se você se conectar automaticamente, verá um novo grupo de recursos, a conta dos serviços de mídia e uma conta de armazenamento em sua assinatura do Azure.
* Se você se conectar automaticamente, Video Indexer definirá as **unidades reservadas** de mídia para 10 unidades S3:

    ![Unidades reservadas dos serviços de mídia](./media/create-account/ams-reserved-units.png)

* Se você se conectar a uma conta dos serviços de mídia existente, Video Indexer não alterará a configuração das **unidades reservadas** de mídia existentes.

   Talvez seja necessário ajustar o tipo e o número de unidades reservadas de mídia, de acordo com a carga planejada. Tenha em mente que, se a carga for alta e você não tiver unidades ou velocidade suficientes, o processamento de vídeos poderá resultar em falhas de tempo limite.

* Se você se conectar a uma nova conta dos serviços de mídia, Video Indexer iniciar automaticamente o **ponto de extremidade de streaming** padrão nele:

    ![Ponto final de transmissão em fluxo dos serviços de multimédia](./media/create-account/ams-streaming-endpoint.png)

    Os pontos de extremidade de streaming têm um tempo de inicialização considerável. Portanto, pode levar vários minutos a partir do momento em que você conectou sua conta ao Azure, até que seus vídeos possam ser transmitidos e observados no aplicativo Web Video Indexer.

* Se você se conectar a uma conta dos serviços de mídia existente, Video Indexer não alterará a configuração do ponto de extremidade de streaming padrão. Se não houver nenhum **ponto de extremidade de streaming**em execução, você não poderá assistir a vídeos desta conta de serviços de mídia ou em video indexer.

## <a name="next-steps"></a>Passos seguintes

Você pode interagir de forma programática com sua conta de avaliação e/ou com suas contas de Video Indexer que estão conectadas ao Azure seguindo as instruções em: [Usar APIs](video-indexer-use-apis.md).

Você deve usar o mesmo usuário do Azure AD usado ao conectar-se ao Azure.


