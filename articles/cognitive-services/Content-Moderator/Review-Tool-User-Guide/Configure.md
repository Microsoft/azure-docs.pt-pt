---
title: Configurar definições de ferramentas de revisão - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Utilize a ferramenta Review para configurar ou recuperar a sua equipa, etiquetas, conectores, fluxos de trabalho e credenciais para O Moderador de Conteúdo.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220129"
---
# <a name="configure-the-review-tool"></a>Configurar a Ferramenta de revisão

A [ferramenta 'Rever'](https://contentmoderator.cognitive.microsoft.com) tem várias funcionalidades importantes a que pode aceder através do menu **Definições** no painel de instrumentos.

![Menu de definições de moderador de conteúdo também](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Gerir equipas e subequipas

O separador **Team** permite-lhe gerir a sua equipa e subequipa&mdash;grupos de utilizadores que podem ser notificados quando determinadas [avaliações humanas](../review-api.md#reviews) são iniciadas. Só pode ter uma equipa (que cria quando se inscreve na ferramenta Review), mas pode criar várias subequipas. O administrador da equipa pode convidar os membros, definir as suas permissões e atribuí-los a diferentes subequipas.

![Rever as definições da equipa de ferramentas](images/settings-2-team.png)

As subequipas são úteis para criar equipas de escalada ou equipas dedicadas a rever categorias específicas de conteúdo. Por exemplo, pode enviar conteúdo para adultos para uma equipa separada para posterior revisão.

Esta secção explica como criar subequipas e atribuir rapidamente avaliações no voo. No entanto, pode utilizar [fluxos de trabalho](workflows.md) para atribuir avaliações com base em critérios específicos.

### <a name="create-a-subteam"></a>Criar uma subequipa

Vá à secção **Subteams** e clique em **Adicionar Subteam**. Introduza o nome do sub-team no diálogo e clique em **Guardar**.

![Nome sub-team](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Convidar colegas de equipa

Não é possível atribuir alguém a um subteam se ainda não for membro da equipa padrão, por isso precisa adicionar revisores à equipa padrão primeiro. Clique em **Convidar** no separador **Team.**

![Convidar utilizadores](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Atribuir colegas de equipa à subequipa

Clique no botão **Adicionar Membro** para atribuir membros da sua equipa predefinida a uma ou mais subequipas. Só é possível adicionar utilizadores existentes a um subteam. Para adicionar novos utilizadores que não estejam na ferramenta de revisão, convide-os utilizando o botão "Convidar" na página definições da equipa.

![Atribuir membros subvapores](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Atribuir avaliações a subequipas

Depois de ter criado as suas subequipas e os seus membros atribuídos, pode começar a atribuir [avaliações](../review-api.md#reviews) de conteúdo a essas subequipas. Isto é feito a partir do separador **Review** do site.
Para atribuir conteúdo a um subteam, clique na elipse no canto superior direito, selecione **Mover para**, e selecione um subteam.

![Atribuir revisão de imagem ao subteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Alternar entre subequipas

Se for membro de mais de um subteam, pode alternar entre esses sub-teams para alterar as avaliações de conteúdo que são apresentadas para si. No separador **Rever,** selecione o menu suspenso rotulado **Predefinido** e selecione **Escolha subteam**. Pode ver as avaliações de conteúdo para diferentes subequipas, mas apenas aquelas das quais são membros.

![Alternar entre subequipas](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Etiquetas

O separador **Tags** permite definir etiquetas de moderação personalizadas para além das duas etiquetas de moderação padrão&mdash;**é adulto** (**a**) e **isracia** **(r).** Quando cria uma etiqueta personalizada, fica disponível em comentários ao lado das etiquetas predefinidas. Pode alterar quais as etiquetas que aparecem nas avaliações, mudando as suas definições de visibilidade.

![Vista de etiquetas, incluindo as caixas de verificação "É visível"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Criar etiquetas personalizadas

Para criar uma nova etiqueta, deve introduzir um código, nome e descrição curtos nos respetivos campos.

- **Código curto**: Introduza um código de duas letras para a sua etiqueta. Exemplo: **cb**
- **Nome**: Introduza um nome de etiqueta curta e descritiva em minúsculas sem espaços. Exemplo: **isbullying**.
- **Descrição**: (opcional) Introduza uma descrição do tipo de conteúdo que a sua etiqueta visa. Exemplo: **Representações ou casos de ciberbullying**.

Clique em **Adicionar** uma etiqueta e clique em **Guardar** quando terminar de criar etiquetas.

![Ferramenta de revisão criar novo diálogo de etiquetas](images/settings-3-tags.png)

### <a name="delete-tags"></a>Eliminar etiquetas

Pode eliminar etiquetas personalizadas selecionando o ícone do lixo ao lado das suas entradas na lista de Tags, mas não pode eliminar as etiquetas predefinidas.

## <a name="connectors"></a>Conectores

O separador **Conectores** permite-lhe gerir os seus conectores, que são plugins específicos do serviço que podem processar conteúdos de diferentes maneiras como parte dos [fluxos](../review-api.md#workflows)de trabalho de conteúdo .

O conector predefinido quando cria um fluxo de trabalho é o conector Moderador de Conteúdo, que pode marcar o conteúdo como **adulto** ou **picante,** encontrar profanação, e assim por diante. No entanto, pode utilizar outros conectores, listados aqui, desde que tenha credenciais para os respetivos serviços (para utilizar o conector Face, por exemplo, terá de obter uma chave de subscrição [Face).](https://docs.microsoft.com/azure/cognitive-services/face/overview)

A [ferramenta Review](./human-in-the-loop.md) inclui os seguintes conectores:

- Emoções
- Rostos
- Serviço de Nuvem de FotodNA
- Análise de Texto

### <a name="add-a-connector"></a>Adicione um conector

Para adicionar um conector (e disponibilizá-lo para utilização em [fluxos](../review-api.md#workflows)de trabalho de conteúdo), selecione o botão **Connect** apropriado. No dialog seguinte, introduza a sua chave de subscrição para esse serviço. Quando terminar, o seu novo conector deve aparecer no topo da página.

![Definições de conectores moderadores de conteúdo](images/settings-4-connectors.png)

## <a name="workflows"></a>Fluxos de trabalho

O separador **workflows** permite-lhe gerir os seus [fluxos de trabalho](../review-api.md#workflows). Os fluxos de trabalho são filtros baseados na nuvem para conteúdo, e trabalham com conectores para classificar o conteúdo de diferentes maneiras e tomar as ações apropriadas. Aqui, pode definir, editar e testar os seus fluxos de trabalho. Consulte [definir e utilizar fluxos de trabalho](Workflows.md) para obter orientações sobre como fazê-lo.

![Definições de fluxo de trabalho moderador de conteúdo](images/settings-5-workflows.png)

## <a name="credentials"></a>Credenciais

O separador **Credenciais** fornece acesso rápido à sua chave de subscrição Moderador de Conteúdo, que necessitará de aceder a qualquer um dos serviços de moderação a partir de uma chamada REST ou sDK cliente.

![Credenciais moderadoras de conteúdo](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Utilize credenciais externas para fluxos de trabalho

A [ferramenta Review](https://contentmoderator.cognitive.microsoft.com) gera uma chave de teste gratuita para os serviços de Moderador de Conteúdo Azure quando se inscreve, mas também pode configurá-la para usar uma chave existente a partir da sua conta Azure. Isto é recomendado para cenários de grande escala, uma vez que as chaves de ensaio gratuitas têm limites de utilização rigorosos[(Preços e limites).](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)

Se criou um [recurso Moderador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) de Conteúdo em Azure, navegue para ele no portal Azure e selecione a lâmina **Keys.** Copie uma das suas chaves.

![Chaves moderadoras de conteúdo no portal Azure](images/credentials-azure-portal-keys.PNG)

No separador **Credenciais** da [ferramenta 'Revisão's,](https://contentmoderator.cognitive.microsoft.com)vá ao painel de definições de **fluxo de trabalho,** selecione **Editar**e colar a sua chave no campo **Ocp-Apim-Subscription-Key.** Agora, fluxos de trabalho que chamam de APIs de moderação usarão a sua credencial Azure.

> [!NOTE]
> Os outros dois campos do painel **definições** de fluxo de trabalho são para termo personalizado e listas de imagens. Consulte os [termos personalizados](../try-terms-list-api.md) ou guias de [imagens personalizadas](../try-image-list-api.md) para saber sobre estes.

### <a name="use-your-azure-account-with-the-review-apis"></a>Utilize a sua conta Azure com as APIs de revisão

Para utilizar a sua chave Azure com as APIs de revisão, precisa de recuperar o seu ID de recursos. Vá ao seu recurso Moderador de Conteúdo no portal Azure e selecione a lâmina **Propriedades.** Copie o valor de ID do recurso e cole-o no campo **id(s)** do separador **Credenciais** da ferramenta de revisão.

![ID de recurso moderador de conteúdo no portal Azure](images/credentials-azure-portal-resourceid.PNG)

Se inseriu a sua chave de subscrição em ambos os locais, a chave de teste que vem com a sua conta de ferramentas De revisão não será utilizada, mas permanecerá disponível.

## <a name="next-steps"></a>Passos seguintes

Siga a [ferramenta Review rapidamente para](../quick-start.md) começar a utilizar a ferramenta Rever em cenários de moderação de conteúdo.