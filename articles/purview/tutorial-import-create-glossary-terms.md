---
title: 'Tutorial: Criar e importar termos glossários em Azure Purview (pré-visualização)'
description: Este tutorial descreve como criar termos glossários, adicionar termos glossários a um ativo, e importar termos glossários.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 3a81d9480dfb07703d5f1ccfb495d069f140dd71
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555839"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Tutorial: Criar e importar termos glossários em Azure Purview (pré-visualização)

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Um glossário é uma ferramenta importante para manter e organizar o seu catálogo. Constrói o seu glossário definindo novos termos ou importando uma lista de prazos e aplicando esses termos aos seus ativos.

Este tutorial é *parte 5 de uma série tutorial* de cinco partes na qual você aprende os fundamentos de como gerir a governação de dados através de uma propriedade de dados usando Azure Purview. Este tutorial baseia-se no trabalho que concluiu na [Parte 4: Explore conjuntos de recursos, detalhes, esquemas e classificações em Azure Purview (pré-visualização)](tutorial-schemas-and-classifications.md).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar termos glossários.
> * Adicione termos glossários a um ativo.
> * Importar termos glossários.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial Completo: Explore conjuntos de recursos, detalhes, esquemas e classificações em Azure Purview (pré-visualização)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Criar termos glossários

Pode criar termos de negócio ou técnicos no glossário. Também pode anotar os seus bens aplicando-lhes termos.

Aqui está um resumo de alguns dos campos mais comuns na página do **termo Glossário:**

* **Estado**: Atribuir um estatuto ao termo **(Projeto,** **Aprovado,** **Expirado,** ou **Alerta**).

* **Definição:** Introduza uma definição do termo.

* **Acrónimo**: Introduza uma versão abreviada do termo utilizando as letras iniciais de cada palavra.

* **Sinônios**: Selecione outros termos com as mesmas definições ou similares.

* **Termos Relacionados**: Selecione outros termos no glossário que estejam relacionados com este, mas que tenham definições diferentes. Exemplos são termos técnicos que estão relacionados com um termo de negócio, um nome de código, ou outros termos que devem ser associados ao termo.

Criar um termo glossário seguindo estes passos:

1. Navegue para o seu recurso Azure Purview no portal Azure e selecione **Open Purview Studio**. É automaticamente levado para a página inicial do seu Estúdio de Purview.

1. Selecione **Glossary** a partir do painel esquerdo para abrir a página **de termos Glossários.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Screenshot mostrando a página de termos Glossário.":::

1. Selecione **Novo termo**  >  **Sistema padrão**  >  **Continue**.

1. No **separador Visão Geral** da página do **Novo Termo,** insira o **Nome** para o novo termo: *Financeiro*.

1. Insira a **Definição**: *Este termo representa informação financeira para a Contoso Inc.*

1. Na lista de abandono **do estado,** selecione **Aprovado**.

1. Quando terminar, **selecione Criar**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Screenshot mostrando como criar um novo termo glossário.":::

## <a name="add-glossary-terms-to-an-asset"></a>Adicionar termos glossários a um ativo

1. Use os passos que aprendeu na [parte 1 desta série tutorial](tutorial-scan-data.md) para encontrar um ativo. Por exemplo, **Contoso_CashFlow_{N}.csv**.

1. Na página de detalhes do ativo, **selecione Editar**.

1. Na lista de abandono em **termos glossários** no **separador Visão Geral,** selecione **Financial** e, em seguida, selecione **Save**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Screenshot mostrando como adicionar um termo glossário a um ativo.":::

1. Aceda à secção **de termos glossários** no separador **Visão Geral** e note que o termo *Financeiro* foi aplicado ao ativo.

## <a name="import-glossary-terms"></a>Termos glossários de importação

Para importar termos ou atualizar os termos existentes a granel, faça o upload de um modelo pré-povoado para o seu glossário.

Neste procedimento, importa termos glossários através de um ficheiro .csv:

1. Note onde guardou o ficheiro denominado *StarterKitTerms.csv,* que faz parte do kit de arranque que descarregou na [parte 1 desta série tutorial](tutorial-scan-data.md).

   Este ficheiro contém uma lista de termos pré-povoados que são relevantes para o seu espólio de dados.

1. Para começar a importar, selecione **Glossário** e, em seguida, **selecione As condições de Importação**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Screenshot mostrando como importar termos glossários.":::

1. Na página **de termos de Importação,** selecione **o padrão do Sistema** e, em seguida, selecione **Continue**.

1. **Selecione Procurar,** vá ao local onde descarregou *StarterKitTerms.csv* e, em seguida, selecione **Open**.

1. Selecione **OK** para começar a importar os termos.

   Após a importação ser concluída, os novos termos glossários são apresentados na página **de termos glossários.**

1. Veja cada um dos termos recentemente importados para ver como são definidos.

## <a name="create-custom-term-templates"></a>Crie modelos de termo personalizado

Nesta secção, você aprende a criar um modelo de termo personalizado com atributos personalizados e dados de importação usando um ficheiro csv modelo.

Existem vários modelos de termo do Sistema existentes, que pode ver selecionando **o Sistema** de  >  **Modelos de Prazo de Gestão** Glossária  >  **System**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="modelos de prazo do sistema.":::

Para criar um novo modelo de termo personalizado, faça os seguintes passos:

1. Selecione **Glossary** do menu do lado esquerdo.
1. **Selecione Gerir modelos de fim**  >  **personalizado** novo modelo  >  **de termo**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="screate um novo modelo de termo personalizado.":::

No ecrã do **modelo do novo termo,** faça os seguintes passos:

1. Insira **o nome do modelo**: `Sensitivity level` .
1. Insira a descrição desejada, como `Indicates the level of sensitivity for this data.`
1. Selecione **+ Novo atributo** para abrir um diálogo para adicionar atributos.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="início do ecrã do modelo de novo prazo.":::

1. No **novo** ecrã de atributos, introduza os seguintes parâmetros:

   |Definição|Valor sugerido|
   |---------|-----------|
   |Nome do atributo|é informação sensível|
   |Tipo de campo|dropdown|Escolha única|
   |Marca como necessário|selecione esta caixa de verificação.|
   |+ Adicionar uma escolha| Adicione duas opções. "Sim" e "Não".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="adicionar um novo atributo.":::

1. Repita os passos anteriores para adicionar outro atributo com o nome `can be shared externally` . Depois de ambos os atributos serem adicionados, finalmente selecione **Criar**.

## <a name="import-terms-from-a-template"></a>Termos de importação a partir de um modelo

Em seguida, importa um novo termo usando o modelo **de nível de sensibilidade** que criou. 

1. A partir do ecrã de **termos glossários,** selecione **os termos de importação**.

1. Selecione **o nível** de sensibilidade a partir do ecrã **de termos de importação.** Selecione **Continuar**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Selecione o nível de sensibilidade.":::

1. O modelo de termo para **nível de sensibilidade** contém atributos predefinidos do sistema, bem como os novos atributos que adicionou: `can be shared externally` e `is sensitive information` . Selecione **Descarregue uma amostra. Ficheiro CSV.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Descarregue o ficheiro csv da amostra.":::

1. Um ficheiro de modelo é descarregado para que você edite e carrete novo termo glossário com atributos do cliente. Abra o ficheiro CSV que descarregou. Adicione novos termos e os seus valores adequados como novas linhas no ficheiro CSV.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Criar termo em ficheiro csv.":::

   Quaisquer termos listados nos **Termos Relacionados** ou coluna **synonyms** que ainda não existam serão criados quando o ficheiro for carregado. Serão criados usando o modelo **padrão do Sistema.**

1. Para fazer o upload do ficheiro, volte ao ecrã **de termos de Importação** e selecione **Procurar** ao lado do Select the **complete . Ficheiro CSV para carregar caixa.** Selecione o seu ficheiro na caixa de diálogo que abre e, em seguida, selecione **OK**.

1. Os novos termos estão agora listados no ecrã de **termos Glossários.** Pode ver detalhes sobre os novos termos clicando no nome do termo na lista.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Criar termos glossários.
> * Adicione termos glossários a um ativo.
> * Importar termos glossários.

Avance para o próximo artigo para saber mais sobre diferentes insights de catálogo.

> [!div class="nextstepaction"]
> [Compreender Insights em Azure Purview](concept-insights.md)
