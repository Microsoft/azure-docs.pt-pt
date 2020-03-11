---
title: Criar uma conta dos Serviços de Multimédia do Azure no portal do Azure
description: Este tutorial orienta-o ao longo dos passos para criar uma conta dos Serviços de Multimédia do Azure no portal do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: c90278eccf889595378d3b6b07de2468910c660c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080881"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Utilize o portal Azure para criar uma conta de Media Services

O portal Azure fornece uma forma de criar rapidamente uma conta Azure Media Services. Pode utilizar a sua conta para aceder aos Media Services que permitem armazenar, encriptar, codificar, gerir e transmitir conteúdo no Azure.

Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para:

* gerir os Media Services v3 [Eventos Ao Vivo,](live-events-outputs-concept.md) 
* vista (não gerir) [v3 Ativos,](assets-concept.md) 
* [obtenha informações sobre o acesso a APIs.](access-api-portal.md) 

Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.

Este artigo mostra como criar uma conta de Media Services utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-a-media-services-account"></a>Criar uma conta dos Media Services

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Clique **em +Criar um recurso** > **Media** > **Media Services**.
1. Na secção **Criar uma conta de Serviços de Media** insira os valores exigidos.
    
    | Nome | Descrição |
    | ---|---|
    |**Nome da Conta**|Insira o nome da nova conta de Serviços de Media. Um nome de conta dos Serviços de Multimédia é composto por letras minúsculas ou números sem espaços, com 3 a 24 carateres de comprimento.|
    |**Subscrição**|Se tiver mais de uma subscrição, selecione uma da lista de subscrições do Azure a que tem acesso.|
    |**Grupo de Recursos**|Selecione o novo recurso ou existente. Um grupo de recursos é uma coleção de recursos que partilham o ciclo de vida, as permissões e as políticas. Sabia mais [aqui](../../azure-resource-manager/management/overview.md#resource-groups).|
    |**Localização**|Selecione a região geográfica que será usada para armazenar os registos de mídia e metadados para a sua conta de Media Services. Esta região será utilizada para processar e transmitir em fluxo a sua multimédia. Apenas as regiões dos Media Services disponíveis são apresentadas na caixa de lista pendente. |
    |**Storage Account**|Selecione uma conta de armazenamento para fornecer armazenamento blob do conteúdo dos media na sua conta Media Services. Pode selecionar uma conta do Storage existente na mesma região geográfica da conta dos Media Services ou pode criar uma nova conta do Storage. É criada uma nova conta do Storage na mesma região. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.<br/><br/>Tem de ter uma conta de armazenamento **Principal** e pode ter qualquer número de contas de armazenamento **Secundárias** associadas à conta dos Serviços de Multimédia. Pode utilizar o portal Azure para adicionar contas de armazenamento secundárias. Para mais informações, consulte as contas de [Armazenamento Azure com contas azure media services](storage-account-concept.md).<br/><br/>A conta dos Serviços de Multimédia e todas as contas de armazenamento associadas têm de estar na mesma subscrição do Azure. Recomendamos vivamente que utilize as contas de armazenamento na mesma localização que a conta dos Serviços de Multimédia para evitar custos de saída de dados e latência adicionais.|
    
1. Selecione **Afixar no dashboard** para ver o progresso da implementação da conta.
1. Clique em **Criar** na parte inferior do formulário.
   
    Depois de criar a conta com êxito, a página de descrição geral é carregada. Na tabela final de streaming, a conta terá um ponto final de streaming predefinido no estado **de parado.** 

    Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, no estado **Parado**. Para começar a transmitir o seu conteúdo e tirar partido de [embalagens dinâmicas](dynamic-packaging-overview.md) e [encriptação dinâmica,](content-protection-overview.md)o ponto final de streaming a partir do qual pretende transmitir conteúdo tem de estar no estado **De Execução.** 

## <a name="next-steps"></a>Passos seguintes

Se planeia aceder programaticamente à Media Services API, consulte [Access the Azure Media Services API com autenticação Azure AD](access-api-portal.md).

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

