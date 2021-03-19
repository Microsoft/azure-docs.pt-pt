---
title: Quickstart - Criar uma instância de Gestão API Azure
description: Crie uma nova instância de serviço Azure API Management utilizando o portal Azure.
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/08/2020
ms.author: apimpm
ms.openlocfilehash: 66d0874536b358b720b43780d6ecce879ac866ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90708211"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-portal"></a>Quickstart: Criar uma nova instância de serviço AZure API Management utilizando o portal Azure

A Gestão de API (APIM) do Azure ajuda as organizações a publicar APIs para programadores externos, parceiros e internos, de modo a que estes possam explorar ao máximo o potencial dos seus dados e serviços. A Gestão de API fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção. A APIM permite-lhe criar e gerir os modernos gateways API para serviços de backend existentes hospedados em qualquer lugar. Para obter mais informações, consulte [Descrição Geral](api-management-key-concepts.md).

Este início rápido descreve os passos para a criação de uma nova instância de serviço da Gestão de API através do portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

:::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="Instância de Gestão da API":::

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-new-service"></a>Criar um novo serviço

1. No menu do portal do Azure, selecione **Criar um recurso**. Também pode selecionar **Criar um recurso** na página Azure **Home.** 
   
   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-01.png" alt-text="Selecione Criar um recurso":::

   
1. Na **nova** página, selecione  >  **Integration API Management**.

   :::image type="content" source="media/get-started-create-service-instance/00-CreateResource-02.png" alt-text="Nova instância da Gestão de API do Azure":::
   
1. Na página de serviço de gestão da **API,** introduza as definições.

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-create-new.png" alt-text="Novo exemplo":::
   
   | Definições                 | Descrição   |                                                                     
   |-------------------------|-----------------------------------------------|
   | **Nome**                | Um nome único para o seu serviço de Gestão API. O nome não pode ser alterado mais tarde. O nome de serviço refere-se tanto ao serviço como ao recurso Azure correspondente. <br/> O nome de serviço é utilizado para gerar um nome de domínio predefinido: *\<name\> .azure-api.net.* Se gostaria de utilizar um nome de domínio personalizado, consulte [Configurar um domínio personalizado](configure-custom-domain.md). |
   | **Subscrição**          | A subscrição sob a qual esta nova instância do serviço será criada.   |
   | **Grupo de recursos**      |  Selecione um grupo de recursos novo ou existente. Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. |
   | **Localização**          | Selecione uma região geográfica perto de si a partir dos locais disponíveis do serviço de gestão da API. | 
   | **Nome da organização**   | O nome da sua organização. Este nome é utilizado em vários sítios, incluindo o título do portal do programador e o remetente de e-mails de notificação. |                                                         
   | **E-mail do administrador** | O endereço de e-mail para o qual serão enviadas todas as notificações da **API Management.**   |  
   | **Escalão de preço**        | **Selecione o** nível de desenvolvedor para avaliar o serviço. Este nível não é para uso de produção. Para obter mais informações sobre o dimensionamento dos escalões da Gestão de API, consulte [Atualização de versão do software e dimensionamento](upgrade-and-scale.md). |

3. Selecione **Criar**.

    > [!TIP]
    > Pode levar entre 30 a 40 minutos para criar e ativar um serviço de Gestão API neste nível. Ao selecionar **Afixar ao dashboard**, é mais fácil localizar um serviço criado recentemente.

[!INCLUDE [api-management-navigate-to-instance](../../includes/api-management-navigate-to-instance.md)]

Reveja as propriedades do seu serviço na página **'Vista Geral'.**

   :::image type="content" source="media/get-started-create-service-instance/get-started-create-service-instance-created.png" alt-text="Instância de Gestão da API":::

Quando o seu serviço de gestão API estiver online, está pronto para usá-lo. Comece com o tutorial para [importar e publicar a sua primeira API.](import-and-publish.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode remover o grupo de recursos e todos os recursos relacionados seguindo estes passos:

1. No portal Azure, procure e selecione **grupos de Recursos.** Também pode selecionar **grupos de recursos** na página **inicial.** 

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-01.png" alt-text="Navegação de grupo de recursos":::

1. Na página **dos grupos de recursos,** selecione o seu grupo de recursos.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-02.png" alt-text="Selecionar o grupo de recursos":::

1. Na página do grupo de recursos, selecione **Eliminar o grupo de recursos**. 
   
1. Digite o nome do seu grupo de recursos e, em seguida, **selecione Delete**.

   :::image type="content" source="media/get-started-create-service-instance/00-DeleteResource-03.png" alt-text="Eliminar grupo de recursos":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar e publicar a sua primeira API](import-and-publish.md)
