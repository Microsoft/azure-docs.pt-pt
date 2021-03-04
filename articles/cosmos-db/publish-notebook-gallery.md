---
title: Publique cadernos na galeria de cadernos Azure Cosmos DB
description: Saiba como baixar os cadernos da galeria pública, editá-los e publicar os seus próprios cadernos na galeria.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039330"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publique cadernos na galeria de cadernos Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Os Cadernos Jupyter embutidos em Azure Cosmos estão diretamente integrados nas suas contas DB Azure Cosmos no portal Azure. Utilizando estes cadernos, pode analisar e visualizar os seus dados a partir do portal Azure. Os cadernos incorporados para a Azure Cosmos DB estão atualmente disponíveis em [muitas regiões.](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all) Para utilizar cadernos, [crie uma nova conta Cosmos](create-cosmosdb-resources-portal.md) ou permita [cadernos numa conta existente](enable-notebooks.md) numa dessas regiões.

O ambiente de cadernos no portal Azure tem algumas amostras publicadas pela equipa DB do Azure Cosmos. Dispõe ainda de uma galeria pública onde pode publicar e partilhar os seus próprios cadernos. Depois de um caderno ser publicado na galeria, está disponível para todos os utilizadores do Azure Cosmos DB para visualizar e usar. Neste artigo, você vai aprender a usar cadernos da galeria pública e publicar o seu caderno na galeria.

## <a name="download-a-notebook-from-the-gallery"></a>Faça o download de um caderno da galeria

Use os seguintes passos para ver e baixar cadernos da galeria para o seu espaço de trabalho de cadernos próprios:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para a sua conta DB Azure Cosmos que está ativada com ambiente de caderno.

1. Navegue para o  >  separador de galeria de **cadernos** data  >    >   explorer.

1. Aceite o [código de conduta](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  antes de ver ou publicar cadernos na galeria. O código de conduta é registado por utilizador, de acordo com o nível de subscrição. Quando mudar para uma subscrição diferente, deve aceitá-la novamente antes de aceder à galeria. Para aceitar o código de conduta, selecione a caixa de verificação e **continue:**

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Navegue na galeria pública dos cadernos e aceite o código de conduta.":::

1. Em seguida, pode adicionar um bloco de notas específico ao separador favorito ou descarregá-lo. Quando descarrega um caderno, é copiado para o espaço de trabalho dos seus cadernos onde pode executá-lo ou editá-lo.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Faça o download de um caderno da galeria para o seu espaço de trabalho.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publique um caderno na galeria

Quando constrói os seus próprios cadernos ou edita os cadernos existentes para se encaixar num cenário diferente, poderá querer publicá-los na galeria. Depois de um caderno ser publicado na galeria, outros utilizadores podem aceder ao mesmo. Pode explorar a [galeria de amostras de portátil](https://cosmos.azure.com/gallery.html) para ver os cadernos atualmente disponíveis.

Utilize os seguintes passos para publicar um caderno:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue para a sua conta DB Azure Cosmos que está ativada com ambiente de caderno.

1. Navegue para os cadernos do **Explorador**  >  **de dados**  >  **O separador Os meus cadernos.**

1. Vá ao caderno que quer publicar. Selecione o botão **Guardar** na barra de comando e, em seguida, **selecione Publicar para a galeria**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Escolha um caderno para publicar na galeria.":::

   Também pode encontrar a opção **Publicar para galeria** selecionando o **...** botão ao lado do nome do caderno:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Outra abordagem para escolher um caderno para publicar na galeria.":::

1. Preencha o **formulário de publicação para a galeria** com os seguintes detalhes:

   * **Nome:** Um nome amigável para identificar o seu caderno.
   * **Descrição:**  Uma breve descrição do que o seu caderno faz.
   * **Etiquetas:** As etiquetas são opcionais e são usadas para filtrar resultados quando pesquisadas por uma palavra-chave.
   * **Imagem de capa:** Uma imagem que é usada na página de capa quando o caderno é publicado. Pode escolher uma das seguintes opções:
   * **Imagem personalizada** - Pode fazer o upload de uma imagem a partir do seu computador. Escolha um ficheiro de imagem com a relação de aspeto 256x144.
   * **URL** - Forneça um URL acessível ao público onde a imagem está localizada.
   * **Tirar a imagem** - Uma imagem do seu caderno aberto é automaticamente tirada e carregada para a pré-visualização.
   * **Utilize a primeira saída do visor** - Saída da primeira célula que tenha uma saída de exibição. As células que apenas mostram a marcação/texto não contam como saída de exibição.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Preencha a publicação para o formulário de galeria.":::

   > [!NOTE]
   > Se estiver a utilizar a opção **Publicar para galeria** a partir da **...** botão ao lado do nome do portátil, não verá todas as opções **de imagem cover.** Isto porque o portátil pode não estar aberto e o Azure Cosmos DB não terá acesso para tirar uma imagem ou aceder à primeira saída do ecrã.

1. Verifique se a pré-visualização parece boa e **selecione Publicar**. Quando publicado, este caderno aparecerá na galeria pública de cadernos Azure Cosmos DB. Certifique-se de que removeu quaisquer dados ou saídas sensíveis antes de publicar. O conteúdo do portátil é analisado por qualquer violação das políticas da Microsoft antes de ser publicado. Este processo geralmente leva alguns segundos para ser concluído. Se forem encontradas violações, uma mensagem é exibida no separador de notificação. O seu caderno não será publicado se encontrar alguma violação, remova o texto violado e publique-o novamente.

   > [!NOTE]
   > Depois de os cadernos serem publicados na galeria pública, todos os utilizadores do Azure Cosmos DB podem vê-los. O acesso não se limita a por subscrição, ou por nível de organização.

1. Depois de o caderno ser publicado na galeria, pode vê-lo dentro do **separador de trabalho publicado.** Pode também remover ou eliminar os cadernos que publicou na galeria pública.

1. Também pode reportar um caderno que viola o código de conduta. Se for encontrada uma violação, a Cosmos DB remove automaticamente o caderno da galeria. Se um caderno for removido, os utilizadores podem vê-lo no **separador de trabalho publicado** com a nota removida. Para reportar um caderno, vá ao  >  separador de galeria de **cadernos** data  >    >  **Explorer.** Abra o caderno que pretende reportar, paire sobre o botão **de ajuda** no canto direito e selecione Report **Abuse**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Reporte um caderno que viole o código de conduta.":::

## <a name="next-steps"></a>Passos seguintes

* Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
* [Use funcionalidades e comandos de caderno Python](use-python-notebook-features-and-commands.md)
* [Use funcionalidades e comandos de caderno C#](use-csharp-notebook-features-and-commands.md)
* [Importar cadernos de um repo GitHub](import-github-notebooks.md)
