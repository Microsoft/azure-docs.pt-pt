---
title: Configurar definições de ferramentas de revisão - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a ferramenta 'Rever' para configurar ou recuperar a sua equipa, etiquetas, conectores, fluxos de trabalho e credenciais para o Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92912026"
---
# <a name="configure-the-review-tool"></a>Configurar a Ferramenta de revisão

A [ferramenta 'Review'](https://contentmoderator.cognitive.microsoft.com) tem várias funcionalidades importantes a que pode aceder através do menu Definições no painel de **instrumentos.**

![Menu de definições de moderador de conteúdo](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gerir equipa e subeconsagens

O separador **Team** permite-lhe gerir a sua equipa e subtemas &mdash; grupos de utilizadores que podem ser notificados quando [determinadas avaliações humanas são iniciadas.](../review-api.md#reviews) Só pode ter uma equipa (que cria quando se inscreve na ferramenta 'Revisão'), mas pode criar várias subecons. O administrador da equipa pode convidar os membros, definir as suas permissões e atribuí-los a diferentes subteams.

![Rever as definições da equipa da ferramenta](images/settings-2-team.png)

As subemas são úteis para criar equipas de escalada ou equipas dedicadas a rever categorias específicas de conteúdos. Por exemplo, pode enviar conteúdo adulto para uma equipa separada para posterior revisão.

Esta secção explica como criar subteammas e atribuir rapidamente avaliações no voo. No entanto, pode utilizar [fluxos de trabalho](workflows.md) para atribuir revisões com base em critérios específicos.

### <a name="create-a-subteam"></a>Criar uma subeconda

Vá à secção **subteams** e clique **em Adicionar Subteam**. Introduza o nome da subeconhética no diálogo e clique em **Guardar**.

![Nome subteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Convidar colegas de equipa

Não é possível atribuir alguém a uma subequiga se já não for membro da equipa predefinida, por isso é necessário adicionar primeiro os revisores à equipa predefinida. Clique em **Convidar** no separador **Equipa.**

![Convidar utilizadores](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Atribuir colegas de equipa à subeconsemia

Clique no botão **Adicionar Membro** para atribuir os membros da sua equipa predefinido a uma ou mais subeconsagens. Só é possível adicionar utilizadores existentes a uma subedição. Para adicionar novos utilizadores que não estão na ferramenta de revisão, convide-os utilizando o botão "Convidar" na página 'Configurações da Equipa'.

![Atribuir membros da subeconhia](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Atribuir comentários a subeconsagens

Uma vez criado as suas subecondas e membros designados, pode começar a atribuir [avaliações](../review-api.md#reviews) de conteúdo a essas subedição. Isto é feito a partir do **separador 'Avaliação'** do site.
Para atribuir conteúdo a uma subequipa, clique na elipse no canto superior direito, selecione **Mover para**, e selecione uma subequipa.

![Atribuir revisão de imagem a subeconsão](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alternar entre subteames

Se for membro de mais de uma subeconsão, pode alternar entre essas subeetas para alterar quais as avaliações de conteúdo que são apresentadas para si. No **separador 'Revisão',** selecione o menu suspenso com a etiqueta **Predefinida** e selecione **Escolha Subteam**. Pode ver as avaliações de conteúdo para diferentes subteames, mas apenas aquelas das quais são membros.

![Alternar entre subteames](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Etiquetas

O separador **Tags** permite definir etiquetas de moderação personalizadas para além das duas etiquetas de moderação padrão &mdash; **isadult** **(a**) e **isracy** **(r**). Quando cria uma etiqueta personalizada, fica disponível em comentários ao lado das etiquetas predefinidos. Pode alterar quais as tags que aparecem nas avaliações, alterando as suas definições de visibilidade.

![Vista de tags, incluindo as caixas de verificação "É visível"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Criar tags personalizadas

Para criar uma nova etiqueta, tem de introduzir um código, nome e descrição curtos nos respetivos campos.

- **Código curto**: Introduza um código de duas letras para a sua etiqueta. Exemplo: **cb**
- **Nome**: Introduza um nome de etiqueta curto e descritivo em minúsculas sem espaços. Exemplo: **isbullying**.
- **Descrição**: (opcional) Introduza uma descrição do tipo de conteúdo que a sua etiqueta visa. Exemplo: **Representações ou casos de ciberbullying**.

Clique **em Adicionar** para adicionar uma etiqueta e clique em **Guardar** quando terminar de criar tags.

![Ferramenta de revisão criar novo diálogo de tag](images/settings-3-tags.png)

### <a name="delete-tags"></a>Eliminar tags

Pode eliminar as etiquetas personalizadas selecionando o ícone do lixo ao lado das suas entradas na lista de Tags, mas não é possível eliminar as etiquetas predefinidos.

## <a name="connectors"></a>Conectores

O **separador Conectores** permite-lhe gerir os seus conectores, que são plugins específicos de serviço que podem processar o conteúdo de diferentes maneiras como parte dos [fluxos](../review-api.md#workflows)de trabalho de conteúdo.

O conector predefinido quando cria um fluxo de trabalho é o conector content Moderador, que pode marcar o conteúdo como **adulto** ou **picante**, encontra profanidade, e assim por diante. No entanto, pode utilizar outros conectores, listados aqui, desde que tenha credenciais para os respetivos serviços (para utilizar o conector Face, por exemplo, terá de obter uma chave de subscrição [Face).](../../face/overview.md)

A [ferramenta Review](./human-in-the-loop.md) inclui os seguintes conectores:

- Emoções
- Face
- Serviço de Nuvem PhotoDNA
- Análise de Texto

### <a name="add-a-connector"></a>Adicione um conector

Para adicionar um conector (e disponibilizá-lo para utilização em [fluxos de trabalho de](../review-api.md#workflows)conteúdo), selecione o botão **Connect** apropriado. No diálogo seguinte, insira a sua chave de subscrição para esse serviço. Quando terminar, o seu novo conector deve aparecer no topo da página.

![Definições de conectores moderadores de conteúdo](images/settings-4-connectors.png)

## <a name="workflows"></a>Fluxos de trabalho

O **separador fluxos de trabalho** permite-lhe gerir os seus [fluxos de trabalho](../review-api.md#workflows). Os fluxos de trabalho são filtros baseados na nuvem para conteúdo, e trabalham com conectores para classificar o conteúdo de diferentes maneiras e tomar as ações apropriadas. Aqui, pode definir, editar e testar os seus fluxos de trabalho. Consulte [Definir e utilizar fluxos de trabalho](Workflows.md) para obter orientações sobre como fazê-lo.

![Definições de fluxo de trabalho do moderador de conteúdo](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

O separador **Credenciais** proporciona acesso rápido à sua chave de subscrição do Moderador de Conteúdo, à qual necessitará de aceder a qualquer um dos serviços de moderação a partir de uma chamada REST ou do cliente SDK.

![Credenciais moderadoras de conteúdo](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Utilize credenciais externas para fluxos de trabalho

A [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) gera uma chave de teste gratuita para os serviços de Moderador de Conteúdo Azure quando se inscreve, mas também pode configurá-la para utilizar uma chave existente na sua conta Azure. Isto é recomendado para cenários de grande escala, uma vez que as chaves de ensaio gratuitas têm limites de utilização rigorosos[(Preços e limites).](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Se criou um [recurso de Moderador de Conteúdo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) em Azure, navegue no portal Azure e selecione a lâmina **Keys.** Copie uma das suas chaves.

![Teclas de moderador de conteúdo no portal Azure](images/credentials-azure-portal-keys.PNG)

No separador Credenciais da ferramenta **'Obter' da ferramenta 'Obter',** vá ao painel de Definições de **Fluxo de Trabalho,** selecione **Editar** e cole a sua chave no campo **Ocp-Apim-Subscription-Key.** [](https://contentmoderator.cognitive.microsoft.com) Agora, os fluxos de trabalho que chamam de APIs de moderação usarão a sua credencial Azure.

> [!NOTE]
> Os outros dois campos no **painel de Definições** de Fluxo de Trabalho são para listas de termos e imagens personalizadas. Consulte os [termos personalizados](../try-terms-list-api.md) ou guias [de imagens personalizadas](../try-image-list-api.md) para saber sobre estes.

### <a name="use-your-azure-account-with-the-review-apis"></a>Use a sua conta Azure com as APIs de revisão

Para utilizar a sua chave Azure com as APIs de revisão, precisa de recuperar o seu ID de recursos. Aceda ao seu recurso Desemprovido de Conteúdo no portal Azure e selecione a lâmina **Propriedades.** Copie o valor de ID do recurso e cole-o no campo **whitelisted Resource Id(s)** do **separador credenciais** da ferramenta Review.

![ID de recursos moderador de conteúdo no portal Azure](images/credentials-azure-portal-resourceid.PNG)

Se inseriu a sua chave de subscrição em ambos os locais, a chave de teste que acompanha a sua conta de ferramenta Review não será utilizada, mas permanecerá disponível.

## <a name="next-steps"></a>Passos seguintes

Siga o [quickstart](../quick-start.md) da ferramenta Review para começar a usar a ferramenta Review em cenários de moderação de conteúdo.