---
title: Criar uma conta de Indexer de Vídeo ligada ao Azure
titleSuffix: Azure Media Services
description: Saiba como criar uma conta de Indexer de Vídeo ligada ao Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: fe6be5778997f0ef4a3f53ae45d17352eca60d8e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727265"
---
# <a name="create-a-video-indexer-account"></a>Criar uma conta de Indexer de Vídeo

Quando criar uma conta do Video Indexer, pode optar por uma conta de avaliação gratuita (através da qual obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (não fica limitado pela quota). Com uma avaliação gratuita, o Video Indexer disponibiliza até 600 minutos de indexação gratuita a utilizadores de sites e até 2400 minutos de indexação gratuita a utilizadores de APIs. Com a opção paga, cria uma conta de Indexer de Vídeo que está ligada à sua subscrição Azure. Você paga por minutos indexados, para mais informações, consulte [os preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/).

Este artigo mostra como criar uma conta de Indexer de Vídeo que está ligada a uma subscrição do Azure e a uma conta Azure Media Services. O tópico fornece passos para a ligação ao Azure utilizando o fluxo automático (predefinido). Também mostra como ligar-se ao Azure manualmente (avançado).

Se estiver a passar de um *ensaio* para uma conta de Indexer de Vídeo *paga,* pode optar por copiar todos os vídeos e personalização do modelo para a nova conta, conforme discutido na secção De Importação do seu conteúdo a partir da secção [de conta experimental.](#import-your-content-from-the-trial-account)

O artigo também abrange [a ligação de uma conta de Indexer de Vídeo ao Governo Azure](#video-indexer-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Pré-requisitos para a ligação ao Azure

* Uma subscrição do Azure.

    Se ainda não tem uma subscrição do Azure, inscreva-se no [Azure Free Trial](https://azure.microsoft.com/free/).
* Um domínio Azure Ative Directory (Azure AD).

    Se não tiver um domínio AD Azure, crie este domínio com a sua subscrição Azure. Para obter mais informações, consulte [Gerir nomes de domínio personalizados no seu AD AZure](../../active-directory/enterprise-users/domains-manage.md)
* Um utilizador no seu domínio Azure AD com uma função **de administrador de aplicação.** Utilizará este membro ao ligar a sua conta de Indexer de Vídeo ao Azure.

    Este utilizador deve ser um utilizador AZure AD com uma conta de trabalho ou escola. Não utilize uma conta pessoal, como outlook.com, live.com ou hotmail.com.

    ![todos os utilizadores da AD Azure](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Pré-requisitos adicionais para o fluxo automático

* Um utilizador e membro no seu domínio Azure AD.

    Utilizará este membro ao ligar a sua conta de Indexer de Vídeo ao Azure.

    Este utilizador deve ser membro na sua subscrição Azure com uma função **Proprietário,** ou tanto as funções **de Administrador de Contribuinte** e De Acesso ao **Utilizador.** Um utilizador pode ser adicionado duas vezes, com duas funções. Uma vez com o Contribuinte e uma vez com o Administrador de Acesso ao Utilizador. Para mais informações, consulte [ver o acesso que um utilizador tem aos recursos da Azure.](../../role-based-access-control/check-access.md)

    ![controlo de acessos](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Pré-requisitos adicionais para o fluxo manual

* Registe o fornecedor de recursos EventGrid utilizando o portal Azure.

    No [portal Azure](https://portal.azure.com/), vá a **Subscrições**->[subscrição]->**ResourceProviders**.

    Procure por **Microsoft.Media** e **Microsoft.EventGrid**. Se não estiver no estado "Registado", clique em **Registar**. Leva alguns minutos para se registar.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Criar uma nova conta no Azure 

> [!NOTE]
> Se a sua subscrição Azure utilizar a autenticação multi-factor baseada em certificados, é crucial que execute os seguintes passos num dispositivo que tenha os certificados necessários instalados.

1. Aceda ao site do [Video Indexer](https://www.videoindexer.ai/) e inicie sessão.
1. Selecione o botão **de conta ilimitada Create:**

    ![Criar nova conta de Indexer de Vídeo](./media/create-account/create-unlimited-account.png)
1. Quando a lista de subscrições aparecer, selecione a subscrição que pretende utilizar.

    ![Ligue o Indexador de Vídeo ao Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Selecione uma região de Azure a partir dos locais suportados: West US 2, Norte da Europa ou Ásia Oriental.
1. Na **conta Azure Media Services,** escolha uma destas opções:

    * Para criar uma nova conta de Serviços de Mídia, selecione **Criar um novo grupo de recursos.** Forneça um nome para o seu grupo de recursos.

        O Azure criará a sua nova conta na sua subscrição, incluindo uma nova conta de Armazenamento Azure.  
    * Para utilizar uma conta de Serviços de Mídia existente, selecione **Utilize o recurso existente.** Na lista de contas, selecione a sua conta.

        A sua conta de Media Services deve ter a mesma região que a sua conta de Indexer de Vídeo.

        > [!NOTE]
        > Para minimizar a duração da indexação e baixa produção, é altamente recomendado ajustar o tipo e o número de [Unidades Reservadas](../previous/media-services-scale-media-processing-overview.md ) na sua conta de Serviços de Mídia a **10 Unidades Reservadas S3**. Consulte [o portal Utilizar para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md). As unidades reservadas são cobradas na sua conta, ver [detalhes de preços](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Para configurar manualmente a sua ligação, selecione o Interruptor para o link **de configuração manual.**

        Para obter informações detalhadas, consulte a secção ['Ligar ao Azure' manualmente](#connect-to-azure-manually-advanced-option) (opção avançada) que se segue.
1. Quando tiver terminado, escolha **Create** (Criar). Esta operação pode demorar alguns minutos.

    Depois de estar ligado ao Azure, a sua nova conta de Indexer de Vídeo aparece na lista de contas:

    ![nova conta](./media/create-account/new-account.png)
1. Certifique-se de que o Ponto final de streaming da conta Media Services está a funcionar antes de poder reproduzir os seus vídeos na aplicação web do Indexer de Vídeo (prima o início se for o estado parado).

> [!TIP]
> Para dar um hame de exibição amigável à sua conta aceda a **Definições**.

## <a name="connect-to-azure-manually-advanced-option"></a>Ligue-se ao Azure manualmente (opção avançada)

Se a ligação ao Azure falhar, pode tentar resolver o problema ligando manualmente.

> [!NOTE]
> É altamente recomendável ter as seguintes três contas na mesma região: a conta de Indexer de Vídeo que está a ligar à conta dos Serviços de Comunicação Social, bem como a conta de armazenamento Azure ligada à mesma conta de Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Criar e configurar uma conta de Serviços de Comunicação Social

1. Utilize o portal [Azure](https://portal.azure.com/) para criar uma conta Azure Media Services, conforme descrito na [Criar uma conta](../previous/media-services-portal-create-account.md).

     Certifique-se de que a conta dos Serviços de Comunicação social foi criada com as APIs clássicas. 
 
    ![API clássica dos Serviços de Media](./media/create-account/enable-classic-api.png)


    Ao criar uma conta de armazenamento para a sua conta De Serviços de Mídia, selecione **StorageV2** para o tipo de conta e **Geo-redundante** (GRS) para campos de replicação.

    ![Nova conta AMS](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Certifique-se de escrever os nomes dos recursos e contas dos Serviços de Comunicação Social. Vai precisar deles para os passos na próxima secção.
1. Ajuste o tipo e o número de [unidades reservadas](../previous/media-services-scale-media-processing-overview.md ) a **10 Unidades Reservadas S3** na conta serviços de comunicação que criou. Consulte [o portal Utilizar para alterar unidades reservadas](../previous/media-services-portal-scale-media-processing.md).

    As unidades reservadas são cobradas na sua conta, ver [detalhes de preços](https://azure.microsoft.com/pricing/details/media-services/#analytics).s
1. Antes de poder reproduzir os seus vídeos na aplicação web do Video Indexer, tem de iniciar o **ponto final** de streaming predefinido da nova conta de Media Services.

    Na nova conta dos Serviços de Comunicação Social, selecione **Streaming endpoints**. Em seguida, selecione o ponto final de streaming e prima o início.

    ![Pontos finais de transmissões em fluxo](./media/create-account/create-ams-account-se.png)
4. Para que o Indexante de Vídeo autentica com a API dos Serviços de Mídia, é necessário criar uma aplicação AD. Os seguintes passos guiam-no através do processo de autenticação AD Azure descrito em [Get start with Azure AD authentication utilizando o portal Azure:](../previous/media-services-portal-get-started-with-aad.md)

    1. Na nova conta dos Serviços de Comunicação Social, selecione **acesso API**.
    2. Selecione [o método de autenticação principal do Serviço](../previous/media-services-portal-get-started-with-aad.md).
    3. Obtenha a iD do cliente e o segredo do cliente

        Depois de selecionar -> **Definições Chaves**, adicione **Descrição**, prima **Guardar**, e o valor da chave é povoado.

        Se a chave expirar, o proprietário da conta terá de contactar o suporte do Video Indexer para renovar a chave.

        > [!NOTE]
        > Certifique-se de que anota o valor da chave e o ID da aplicação. Vai precisar para os passos na próxima secção.

### <a name="connect-manually"></a>Conecte-se manualmente

Na nova conta Criar um novo relato num diálogo de **subscrição Azure** da sua página [de Indexer](https://www.videoindexer.ai/) de Vídeo, selecione o Interruptor para o link **de configuração manual.**

No diálogo, forneça as seguintes informações:

|Definições|Descrição|
|---|---|
|Região de conta de Indexer de Vídeo|O nome da região da conta do Indexante de Vídeo. Para um melhor desempenho e custos mais baixos, é altamente recomendado especificar o nome da região onde estão localizados os recursos Azure Media Services e a conta de Armazenamento Azure. |
|Inquilino do Azure AD|O nome do inquilino da AD Azure, por exemplo , "contoso.onmicrosoft.com". A informação do inquilino pode ser recuperada a partir do portal Azure. Coloque o cursor sobre o nome do utilizador inscrito no canto superior direito. Encontre o nome à direita do **Domínio.**|
|ID da subscrição|A assinatura Azure sob a qual esta ligação deve ser criada. O ID de subscrição pode ser recuperado a partir do portal Azure. Selecione **Todos os serviços** no painel esquerdo e procure por "subscrições". Selecione **Subscrições** e escolha o ID desejado na lista das suas subscrições.|
|Nome do grupo de recursos Azure Media Services|O nome do grupo de recursos em que criou a conta dos Serviços de Comunicação Social.|
|Nome de recurso de serviço de mídia|O nome da conta Azure Media Services que criou na secção anterior.|
|ID da Aplicação|O ID de aplicação Azure (com permissões para a conta de Serviços de Mídia especificadas) que criou na secção anterior.|
|Chave de aplicação|A chave de aplicação AD AZure que criou na secção anterior. |

### <a name="import-your-content-from-the-trial-account"></a>Importe o seu conteúdo da conta *experimental*

Ao criar uma nova conta, tem a opção de importar o seu conteúdo da conta *experimental* para a nova conta. Se verificar a opção *de importação* na Criação de uma nova conta num diálogo de **subscrição Azure,** todas as personalizações de modelos de mídia e conteúdo serão copiadas da conta *experimental* para a nova conta.

A capacidade de importar o conteúdo é válida para abordagens automatizadas e manuais acima descritas.

> [!NOTE]
> O conteúdo só pode ser importado uma vez a partir de cada conta.
>
> A conta *do julgamento* não é availagle na nuvem do Governo Azure.

## <a name="azure-media-services-considerations"></a>Considerações dos Serviços Azure Media

Aplicam-se as seguintes considerações relacionadas com os Serviços de Comunicação Social da Azure:

* Se planeia ligar-se a uma conta de Serviços de Comunicação existente, certifique-se de que a conta de Serviços de Mídia foi criada com as APIs clássicas. 
 
    ![API clássica dos Serviços de Media](./media/create-account/enable-classic-api.png)
* Se ligar a uma conta de Serviços de Comunicação existente, o Índice de Vídeo não altera a configuração de **Unidades Reservadas** para os meios de comunicação existentes.

   Poderá ser necessário ajustar o tipo e o número de Unidades Reservadas de Mídia de acordo com a sua carga planeada. Tenha em mente que se a sua carga for elevada e não tiver unidades ou velocidade suficientes, o processamento de vídeos pode resultar em falhas de tempo.
* Se ligar a uma nova conta de Serviços de Mídia, o Video Indexer inicia automaticamente o **ponto de final de streaming** predefinido:

    ![Ponto final de streaming de serviços de mídia](./media/create-account/ams-streaming-endpoint.png)

    Os pontos finais de streaming têm um tempo considerável de arranque. Portanto, pode demorar vários minutos desde o momento em que ligou a sua conta ao Azure até que os seus vídeos possam ser transmitidos e assistidos na aplicação web do Indexer de Vídeo.
* Se ligar a uma conta de Serviços de Mídia existente, o Índice de Vídeo não altera a configuração padrão do Ponto Final de Streaming. Se não houver **streaming endpoint** em execução, não pode ver vídeos desta conta de Media Services ou no Video Indexer.
* Se ligar automaticamente, o Indexante de Vídeo define as **unidades reservadas** para 10 unidades S3:

    ![Unidades reservadas dos Serviços de Mídia](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-indexer-account"></a>Automatizar a criação da conta do Indexador de Vídeo

Automatizar a criação da conta é um processo de duas etapas:
 
1. Utilize o Azure Resource Manager para criar uma conta Azure Media Services + aplicação AZure AD.

    Veja um exemplo do modelo de criação de [conta dos Media Services.](https://github.com/Azure-Samples/media-services-v3-arm-templates)
1. Ligue para [Criar Conta com os Serviços de Comunicação Social e a aplicação AD Azure](https://videoindexer.ai.azure.us/account/login?source=apim).

## <a name="video-indexer-in-azure-government"></a>Indexante de Vídeo no Governo de Azure

### <a name="prerequisites-for-connecting-to-azure-government"></a>Pré-requisitos para a ligação ao Governo de Azure

-   Uma assinatura Azure no [Governo Azure.](../../azure-government/index.yml)
- Uma conta da AD Azure no Governo de Azure.
- Todos os pré-requisitos de permissões e recursos acima descritos em [Pré-requisitos para a ligação ao Azure](#prerequisites-for-connecting-to-azure).

### <a name="create-new-account-via-the-azure-government-portal"></a>Criar nova conta através do portal do Governo Azure

> [!NOTE]
> A nuvem do Governo Azure não inclui uma experiência *experimental* de Video Indexer.

Para criar uma conta paga através do portal Video Indexer:

1. Ir para https://videoindexer.ai.azure.us 
1. Faça login na sua conta Azure Government Azure AD.
1.  Se não tiver nenhuma conta de Indexer de Vídeo no Governo Azure a que é proprietário ou colaborador, terá uma experiência vazia a partir da qual poderá começar a criar a sua conta. 

    O resto do fluxo é como descrito acima, apenas as regiões a selecionar serão regiões do Governo em que o Indexante de vídeo está disponível 

    Se já for um contribuinte ou administrador de uma conta existente de um ou mais Índices de Vídeo no Governo Azure, será levado a essa conta e a partir daí poderá iniciar um seguinte passo para criar uma conta adicional, se necessário, conforme descrito acima.
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Criar nova conta através da API sobre o Governo Azure

Para criar uma conta paga no Governo Azure, siga as instruções na [Conta Desatada.]() Este ponto final da API inclui apenas as regiões de nuvens do Governo.

### <a name="limitations-of-video-indexer-on-azure-government"></a>Limitações do Indexante de Vídeo no Governo de Azure

*   Não há moderação de conteúdo manual disponível na nuvem do Governo. 

    Na nuvem pública quando o conteúdo é considerado ofensivo com base numa moderação de conteúdo, o cliente pode pedir a um humano que olhe para esse conteúdo e potencialmente reverta essa decisão.  
*   Sem contas de julgamento. 
* Bing descrição - na nuvem de Gov não apresentaremos uma descrição de celebridades e entidades nomeadas identificadas. Esta é apenas uma capacidade de UI. 

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminar este tutorial, elimine os recursos que não está a planear utilizar.

### <a name="delete-a-video-indexer-account"></a>Excluir uma conta de Indexer de Vídeo

Se pretender eliminar uma conta de Indexer de Vídeo, pode eliminar a conta do website do Indexer de Vídeo. Para apagar a conta, tem de ser o proprietário.

Selecione a conta -> **Definições**  ->  **Elimine esta conta**. 

A conta será permanentemente apagada em 90 dias.

## <a name="next-steps"></a>Passos seguintes

Pode interagir programáticamente com a sua conta experimental e/ou com as suas contas de Indexer de Vídeo que estão ligadas ao Azure seguindo as instruções em: [Use APIs](video-indexer-use-apis.md).

Deve utilizar o mesmo utilizador AZure AD que utilizou ao ligar ao Azure.