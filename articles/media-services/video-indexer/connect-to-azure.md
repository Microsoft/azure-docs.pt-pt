---
title: Criar uma conta do Video Indexer no portal do Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como criar uma conta do Video Indexer no portal do Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: 55828ea2235e42920a5179846d81711b1ada5dc2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763678"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Criar uma conta do Video Indexer ligada ao Azure

Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com a avaliação gratuita, o Video Indexer fornece até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com a opção paga, pode criar uma conta do Video Indexer que está ligada à sua subscrição do Azure e uma conta de Media Services do Azure. Irá pagar pelos minutos indexados, bem como pelas cobranças relacionadas com a Conta de Multimédia. 

Este artigo mostra como criar uma conta do Video Indexer que está ligada a uma subscrição do Azure e uma conta de Media Services do Azure. O tópico fornece passos para ligar ao Azure com o fluxo automático (predefinição). Também mostra como ligar ao Azure manualmente (avançado).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.

    Se não tiver uma subscrição do Azure, inscreva-se [avaliação gratuita do Azure](https://azure.microsoft.com/free/).

* Um domínio do Azure Active Directory (AD).

    Se não tiver um domínio do Azure AD, crie este domínio com a sua subscrição do Azure. Para obter mais informações, consulte [gerir nomes de domínio personalizados no Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md)

* Um utilizador e o membro no seu domínio do Azure AD. Usará esse membro quando ligar a sua conta do Video Indexer para o Azure.

    Este utilizador deve ser um utilizador com uma ou conta profissional, não uma conta pessoal, como hotmail.com, live.com ou outlook.com.

    ![todos os utilizadores do AAD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Pré-requisitos adicionais para o fluxo automático

Um utilizador e o membro no seu domínio do Azure AD. Usará esse membro quando ligar a sua conta do Video Indexer para o Azure.

Este utilizador deve ser um membro na sua subscrição do Azure com um um **proprietário** função, ou ambos **contribuinte** e **administrador de acesso de utilizador** funções. Um utilizador pode ser adicionado duas vezes, com 2 funções. Uma vez com e outra com o utilizador administrador de acesso de contribuinte.

![Controlo de acesso](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Pré-requisitos adicionais para o manual flow

Registe o fornecedor de recursos de EventGrid no portal do Azure.

Na [portal do Azure](https://portal.azure.com/), aceda à **subscrições**-> [subscrição] ->**ResourceProviders**. 

Procure **Microsoft.Media** e **Microsoft.EventGrid**. Se não estiver no estado "Registado", clique em **registar**. Demora alguns minutos para se registar.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Ligar ao Azure

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.

2. Clique nas **ligar ao Azure** botão:

    ![Ligar ao Azure](./media/create-account/connect-to-azure.png)

3. Quando for apresentada a lista de subscrições, selecione a subscrição que pretende utilizar.

    ![ligar o indexador de vídeo para o Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Selecione uma região do Azure a partir de localizações suportadas: E.U.A. oeste 2, Europa do Norte ou Ásia Oriental.
5. Sob **conta de Media Services do Azure**, escolha uma destas opções:

    * Para criar uma nova conta de Media Services, selecione **criar novo grupo de recursos**. Forneça um nome para o grupo de recursos.

        Azure irá criar a sua nova conta na sua subscrição, incluindo uma nova conta de armazenamento do Azure. A nova conta de Media Services tem uma configuração inicial do padrão com um ponto final de transmissão em fluxo e de 10 unidades reservadas de S3.
    * Para utilizar uma conta de Media Services existente, selecione **usar o recurso existente**. Na lista de contas, selecione a sua conta.

        Conta dos Media Services tem de ter a mesma região que a sua conta do Video Indexer. 

        > [!NOTE]
        > Para minimizar a duração de indexação e baixo débito, é altamente recomendável para ajustar o tipo e número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) ao **10 unidades reservadas de S3** na sua conta de Media Services. Ver [utilize o portal para alterar as unidades reservadas](../previous/media-services-portal-scale-media-processing.md).

    * Para configurar manualmente a ligação, clique nas **mudar para a configuração manual** ligação.

        Para obter informações detalhadas, consulte a [ligar ao Azure manualmente](#connect-to-azure-manually-advanced-option) secção (opção avançada), que se segue.
6. Quando tiver terminado, escolha **Connect**. Esta operação poderá demorar alguns minutos. 

    Depois que estiver ligado ao Azure, a sua nova conta do Video Indexer aparece na lista conta:

    ![nova conta](./media/create-account/new-account.png)

7. Navegue para a sua nova conta:

    ![Conta de indexador de vídeo](./media/create-account/vi-account.png)

## <a name="connect-to-azure-manually-advanced-option"></a>Ligar ao Azure manualmente (opções avançadas)

Se a falha na ligação para o Azure, pode tentar resolver o problema ao ligar-se manualmente.

> [!NOTE]
> É altamente recomendável ter as seguintes contas de três na mesma região: a conta do Video Indexer que estiver a estabelecer ligação com a conta de Media Services, bem como a conta de armazenamento do Azure ligado à mesma conta de Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta de serviços de multimédia

1. Utilize o [Azure](https://portal.azure.com/) portal para criar uma conta de Media Services do Azure, conforme descrito na [criar uma conta](../previous/media-services-portal-create-account.md).

    Ao criar uma conta de armazenamento para a sua conta de Media Services, selecione **StorageV2** para o tipo de conta e **georredundante (RGS)** para campos de replicação.

    ![nova conta de AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Lembre-se de que o anota os nomes de recursos e a conta de Media Services. Irá precisar dele para obter os passos na secção seguinte.

2. Ajuste o tipo e número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) ao **10 unidades reservadas de S3** na conta de Media Services que criou. Ver [utilize o portal para alterar as unidades reservadas](../previous/media-services-portal-scale-media-processing.md).
3. Antes de pode reproduzir os seus vídeos no aplicativo da web de indexador de vídeo, tem de começar a predefinição **ponto final de transmissão em fluxo** da nova conta de Media Services.

    Na nova conta de Media Services, clique em **pontos finais de transmissão em fluxo**. Selecione o ponto final de transmissão em fluxo e prima início.

    ![nova conta de AMS](./media/create-account/create-ams-account2.png)

4. Indexador de vídeos autenticar com a API de serviços de multimédia, uma aplicação do AD tem de ser criado. O guia de passos seguintes, pelo processo de autenticação do Azure AD descritas [introdução à autenticação do Azure AD com o portal do Azure](../previous/media-services-portal-get-started-with-aad.md):

    1. Na nova conta de Media Services, selecione **acesso à API**.
    2. Selecione [método de autenticação do principal de serviço](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Obter o ID de cliente e o segredo de cliente, conforme descrito no [obter o ID de cliente e o segredo de cliente](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) secção.

        Depois de selecionar **configurações**->**chaves**, adicionar **Descrição**, prima **guardar**, o valor da chave é povoado.

        Se a chave expirar o proprietário da conta terá de contactar o suporte de indexador de vídeo para renovar a chave.

        > [!NOTE]
        > Lembre-se de que anota o valor da chave e o ID da aplicação. Irá precisar dele para obter os passos na secção seguinte.

### <a name="connect-manually"></a>Ligue-se manualmente

Na **ligar uma subscrição do Azure do Video Indexer** caixa de diálogo de sua [Video Indexer](https://www.videoindexer.ai/) página, selecione o **mudar para a configuração manual** ligação.

Na caixa de diálogo, forneça as seguintes informações:

|Definição|Descrição|
|---|---|
|Região de conta de indexador vídeo|O nome da região de conta do Video Indexer. Para um melhor desempenho e reduzir os custos, recomenda-se elevada para especificar o nome da região onde se encontram os recursos de serviços de multimédia do Azure e a conta de armazenamento do Azure. |
|Inquilino do Azure Active Directory (AAD)|O nome do inquilino do Azure AD, por exemplo "contoso.onmicrosoft.com". As informações de inquilino podem ser obtidas a partir do portal do Azure. Coloque o cursor sobre o nome de utilizador com sessão iniciada no canto superior direito. Encontrar o nome para a direita da **domínio**.|
|ID da subscrição|A subscrição do Azure sob a qual esta ligação deverá ser criada. O ID de subscrição pode ser obtido a partir do portal do Azure. Clique em **todos os serviços** no painel esquerdo e procure "subscrições". Selecione **subscrições** e escolha o ID de pretendida na lista das suas subscrições.|
|Nome do grupo de recursos de serviços de multimédia do Azure|O nome para o grupo de recursos no qual criou a conta de Media Services.|
|Nome de recurso do serviço de suporte de dados|O nome da conta de Media Services do Azure que criou na secção anterior.|
|ID da aplicação|O Azure AD ID da aplicação (com permissões para a conta de Media Services especificada) que criou na secção anterior.|
|Chave da Aplicação|A chave da aplicação do Azure AD que criou na secção anterior. |

## <a name="considerations"></a>Considerações

As seguintes considerações relacionadas de serviços de multimédia do Azure aplicam-se:

* Se se conectar automaticamente, verá um novo grupo de recursos, conta de Media Services e uma conta de armazenamento na sua subscrição do Azure.
* Se ligar automaticamente, o indexador de vídeo define o suporte de dados **unidades reservadas** para 10 unidades de S3:

    ![Unidades reservadas de serviços de multimédia](./media/create-account/ams-reserved-units.png)

* Se ligar a uma conta de Media Services existente, o indexador de vídeo não altera o suporte de dados existente **unidades reservadas** configuração.

   Poderá ter de ajustar o tipo e número de unidades reservadas de multimédia, de acordo com a carga em planeadas. Tenha em atenção que, se a carga for elevada e não tiver suficiente unidades ou velocidade, o processamento de vídeos podem resultar em falhas de tempo limite.

* Se ligar a uma nova conta de Media Services, o indexador de vídeo inicia automaticamente a predefinição **ponto final de transmissão em fluxo** no mesmo:

    ![Ponto final de transmissão em fluxo dos serviços de multimédia](./media/create-account/ams-streaming-endpoint.png)

    Pontos finais de transmissão em fluxo tem um tempo de inicialização considerável. Por conseguinte, pode demorar vários minutos a partir do momento em que ligado sua conta para o Azure, até que os seus vídeos podem ser transmitidos em fluxo e viu no aplicativo da web de indexador de vídeo.

* Se ligar a uma conta de Media Services existente, o indexador de vídeo não altera a configuração de ponto final de transmissão em fluxo predefinido. Se não houver nenhuma execução **ponto final de transmissão em fluxo**, não será capaz de ver vídeos desta conta dos serviços de multimédia ou em Video Indexer.

## <a name="next-steps"></a>Passos Seguintes

Pode interagir programaticamente com a sua conta de avaliação e/ou com as suas contas do indexador de vídeo que estiver ligadas ao azure ao seguir as instruções em: [Utilizar APIs](video-indexer-use-apis.md).

Deve usar o mesmo utilizador do Azure AD que utilizou quando ligar ao Azure.


