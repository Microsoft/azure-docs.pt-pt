---
title: Configurar definições de ferramenta de revisão - Content Moderator
titlesuffix: Azure Cognitive Services
description: Utilize a ferramenta de revisão para configurar ou obtenha a sua equipa, etiquetas, conectores, fluxos de trabalho e as credenciais para o Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61285553"
---
# <a name="configure-the-review-tool"></a>Configurar a Ferramenta de revisão

O [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) possui vários recursos importantes que podem aceder através da **definições** menu no dashboard.

![Revisão do moderador de conteúdo também menu Definições](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gerir equipa e subequipas

O **equipe** separador permite-lhe gerir a sua equipa e subequipas&mdash;grupos de utilizadores que possam ser notificados quando determinados [revisões realizadas por pessoas](../review-api.md#reviews) são iniciados. Só pode ter uma equipe (que pode ser criado quando se inscreve com a ferramenta de revisão), mas pode criar vários subequipas. O administrador de equipe pode convidar os membros, definir as respetivas permissões e atribuí-las a subequipas diferentes.

![Configurações de equipe de ferramenta de revisão](images/settings-2-team.png)

Subequipas são úteis para a criação de equipes de escalonamento ou equipas dedicadas para rever as categorias específicas de conteúdo. Por exemplo, poderá enviar conteúdo para adultos a uma equipa separada para examinar detalhadamente.

Esta secção explica como criar subequipas e atribuir rapidamente revisões em tempo real. No entanto, pode usar [fluxos de trabalho](workflows.md) para atribuir as revisões com base em critérios específicos.

### <a name="create-a-subteam"></a>Criar um subteam

Vá para o **subequipas** secção e clique em **Subteam adicionar**. Introduza o seu nome de subteam na caixa de diálogo e clique em **guardar**.

![Nome de subteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Convidar colegas

Não é possível atribuir alguém para uma subteam se não estiverem já um membro da Equipe do padrão, por isso terá de adicionar revisores à equipa do padrão primeiro. Clique em **convidar** sobre o **equipe** separador.

![Convidar utilizadores](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Atribuir a seus colegas para subteam

Clique nas **Adicionar membro** botão para atribuir membros da sua equipa de predefinição a uma ou mais subequipas. Apenas pode adicionar utilizadores existentes para um subteam. Para adicionar novos utilizadores que não estão a ser a ferramenta de revisão, convidá-los através do botão "Convidar" na página de configurações de equipe.

![Atribuir membros subteam](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Atribuir a revisões para subequipas

Assim que criou sua subequipas e atribuída a membros, pode começar a atribuir conteúdo [revisões](../review-api.md#reviews) para esses subequipas. Isso é feito a partir da **revisão** separador do site.
Para atribuir conteúdo a um subteam, clique nas reticências no canto superior direito, selecione **mover para**e selecione um subteam.

![Atribuir a revisão da imagem para subteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alternar entre subequipas

Se for um membro de mais do que um subteam, pode alternar entre esses subequipas para alterar as revisões de conteúdo são apresentadas para. Na **revisão** separador, selecione o menu de lista pendente o nome **predefinido** e selecione **escolha Subteam**. Pode ver as conteúdo revisões para subequipas diferentes, mas apenas os que sua for um membro.

![Alternar entre subequipas](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

O **etiquetas** separador permite-lhe definir as etiquetas de moderação personalizados, além das etiquetas de moderação de predefinidos&mdash;**isadult** (**um**) e **isracy**  (**r**). Quando cria uma marca personalizada, torna-se disponíveis nas revisões em conjunto com as etiquetas predefinidas. Pode alterar as etiquetas aparecem nas revisões alternando as definições de visibilidade.

![Ver as etiquetas, incluindo as caixas de verificação "Está visível"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Criar etiquetas personalizadas

Para criar uma nova etiqueta, tem de introduzir um código curtos, um nome e uma descrição nos respectivos campos.

- **Código de curta**: Introduza um código de duas letras para sua marca. Exemplo: **cb**
- **Nome**: Introduza um nome de etiqueta de curto e descritivo em minúsculas sem espaços. Exemplo: **isbullying**.
- **Descrição**: (opcional) introduza uma descrição do tipo de conteúdo que seus destinos de marca. Exemplo: **As representações gráficas ou instâncias de cibersegurança bullying**.

Clique em **Add** para adicionar uma etiqueta e clique em **guardar** quando tiver terminado de criar etiquetas.

![Ferramenta de revisão criar nova caixa de diálogo de etiqueta](images/settings-3-tags.png)

### <a name="delete-tags"></a>Eliminar etiquetas

Pode eliminar etiquetas personalizadas ao selecionar o ícone de caixote do lixo junto a suas entradas na lista de etiquetas, mas não é possível eliminar as etiquetas predefinidas.

## <a name="connectors"></a>Conectores

O **conectores** separador permite-lhe gerir os conectores, que são Plug-ins de serviços específicos que podem processar o conteúdo de formas diferentes como parte do conteúdo [fluxos de trabalho](../review-api.md#workflows).

O conector predefinido quando cria um fluxo de trabalho é o conector do Content Moderator, o que pode marcar o conteúdo, tal como **adulto** ou **ousado**, encontrar linguagem inapropriada e assim por diante. No entanto, pode utilizar outros conectores, listados aqui, desde que tenha credenciais para os respetivos serviços (para utilizar o conector de Face API, por exemplo, terá de obter um [a API Face](https://docs.microsoft.com/azure/cognitive-services/face/overview) chave de subscrição).

O [ferramenta de revisão](./human-in-the-loop.md) inclui os conectores seguintes:

- API de Emoções
- API Face
- Serviço em nuvem de PhotoDNA
- API de Análise de Texto

### <a name="add-a-connector"></a>Adicionar um conector

Para adicionar um conector (e disponibilizá-lo para utilização no conteúdo [fluxos de trabalho](../review-api.md#workflows)), selecione o adequado **Connect** botão. Na caixa de diálogo seguinte, introduza a chave de subscrição para esse serviço. Quando tiver terminado, o seu novo conector deve aparecer na parte superior da página.

![Definições de conectores do moderador de conteúdo](images/settings-4-connectors.png)

## <a name="workflows"></a>Fluxos de trabalho

O **fluxos de trabalho** separador permite-lhe gerir sua [fluxos de trabalho](../review-api.md#workflows). Fluxos de trabalho são filtros baseados na nuvem para o conteúdo e trabalhar com conectores para ordenar o conteúdo de formas diferentes e executar as ações apropriadas. Aqui, pode definir, editar e testar os fluxos de trabalho. Ver [fluxos de trabalho de definir e usar](Workflows.md) para obter orientações sobre como fazer isso.

![Definições de fluxo de trabalho do moderador de conteúdo](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

O **credenciais** separador fornece acesso rápido para a chave de subscrição do Content Moderator, que terá de aceder a qualquer um dos serviços de moderação de uma chamada REST ou o SDK de cliente.

![O Content Moderator credenciais](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Utilizar credenciais externas para fluxos de trabalho

O [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com) gera uma chave de avaliação gratuita para os serviços do Azure Content Moderator quando se inscreve, mas também pode configurar para utilizar um existente da chave da sua conta do Azure. Isto é recomendado para cenários em grande escala, como as chaves de avaliação gratuitas têm limites de utilização strict ([preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Se tiver criado uma [recursos do Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) no Azure, navegue para o mesmo no portal do Azure e selecione o **chaves** painel. Copie uma das suas chaves.

![Chaves de moderador de conteúdo no portal do Azure](images/credentials-azure-portal-keys.PNG)

Na [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com)do **credenciais** separador, vá para o **definições de fluxo de trabalho** painel, selecione **editar**e cole a chave para o **Ocp-Apim-Subscription-Key** campo. Agora, os fluxos de trabalho que chamam as APIs de moderação utilizará as credenciais do Azure.

> [!NOTE]
> Os outros dois campos na **definições de fluxo de trabalho** painel são para as listas personalizadas do termo e imagem. Consulte a [termos de personalizado](../try-terms-list-api.md) ou [imagens personalizadas de](../try-image-list-api.md) guias para saber mais sobre eles.

### <a name="use-your-azure-account-with-the-review-apis"></a>Utilizar a sua conta do Azure com as APIs de revisão

Para utilizar a sua chave do Azure com as APIs de revisão, tem de obter o seu ID de recurso. Aceda ao seu recurso do Content Moderator no portal do Azure e selecione o **propriedades** painel. Copie o valor de ID de recurso e cole-o para o **IDs do recurso na lista de permissões** campo a ferramenta de revisão **credenciais** separador.

![ID de recurso de moderador de conteúdo no portal do Azure](images/credentials-azure-portal-resourceid.PNG)

Se introduzir a chave de subscrição em ambos os locais, a chave de avaliação que vem com a sua conta da ferramenta de revisão não será utilizada, mas permanecerão disponível.

## <a name="next-steps"></a>Passos Seguintes

Siga os [guia de introdução de ferramenta de revisão](../quick-start.md) para começar a utilizar a ferramenta de revisão em cenários de moderação de conteúdos.