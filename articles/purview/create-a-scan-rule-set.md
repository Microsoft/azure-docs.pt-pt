---
title: Criar um conjunto de regras de digitalização
description: Crie uma regra de digitalização definida no Azure Purview para digitalizar rapidamente fontes de dados na sua organização.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554743"
---
# <a name="create-a-scan-rule-set"></a>Criar um conjunto de regras de digitalização

Num catálogo Azure Purview, pode criar conjuntos de regras de digitalização para lhe permitir digitalizar rapidamente fontes de dados na sua organização.

Um conjunto de regras de digitalização é um recipiente para agrupar um conjunto de regras de digitalização para que possa facilmente as associar a uma varredura. Por exemplo, pode criar um conjunto de regras de verificação predefinidos para cada um dos seus tipos de origem de dados e, em seguida, utilizar estes conjuntos de regras de digitalização por padrão para todas as verificações dentro da sua empresa. Também pode querer que os utilizadores com as permissões certas criem outros conjuntos de regras de digitalização com configurações diferentes baseadas na necessidade do negócio.

## <a name="steps-to-create-a-scan-rule-set"></a>Passos para criar um conjunto de regras de digitalização

Para criar um conjunto de regras de digitalização:

1. No seu catálogo Azure Purview, selecione **Management Center**.

1. Selecione **os conjuntos** de regras de digitalização a partir do painel esquerdo e, em seguida, selecione **New**.

1. A partir da página de definição de nova regra de **digitalização,** selecione as fontes de dados que o scanner de catálogo suporta a partir da lista de drop-down do Tipo de **Origem.** Pode criar um conjunto de regras de digitalização para cada tipo de fonte de dados que pretende digitalizar.

1. Dê a sua regra de digitalização definir um **nome**. O comprimento máximo é de 63 caracteres, sem lugares permitidos. Opcionalmente, introduza uma **Descrição**. O comprimento máximo é de 256 caracteres.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Screenshot mostrando a página definida por regras de digitalização." border="true":::

1. Selecione **Continuar**.

   Aparece a página **'Selecionar os tipos de** ficheiros'. Note que as opções de tipo de ficheiro nesta página variam em função do tipo de fonte de dados que escolheu na página anterior. Todos os tipos de ficheiros estão ativados por predefinição.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Screenshot mostrando a página de tipos de ficheiros Select.":::

   A seleção **de tipos de ficheiros documental** nesta página permite-lhe incluir ou excluir os seguintes tipos de ficheiros de escritório: .doc, .docm, .docx, .dot, .odp, .ods, .odt, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .xlc, .xls, .xlsb, .xlsm, .xlsx e .xlt.

1. Ativar ou desativar um azulejo do tipo de ficheiro selecionando ou limpando a sua caixa de verificação. Se escolher uma fonte de dados do tipo Data Lake (por exemplo, Azure Data Lake Storage Gen2 ou Azure Blob), ative os tipos de ficheiros para os quais pretende ter esquemas extraídos e classificados.

1. Para determinados tipos de fonte de dados, também pode [Criar um tipo de ficheiro personalizado](#create-a-custom-file-type).

1. Selecione **Continuar**.

   Aparece a página **de regras de classificação Select.** Esta página apresenta as **regras** do Sistema e **as regras personalizadas selecionadas,** e o número total de regras de classificação selecionadas. Por padrão, todas as **regras** do Sistema verificam as caixas

1. Para as regras que pretende incluir ou excluir, pode selecionar ou limpar as **regras** de classificação do Sistema para verificar caixas de verificação global por categoria.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Screenshot mostrando a página de regras de classificação Select.":::

1. Pode expandir o nó de categoria e selecionar ou limpar caixas de verificação individuais. Por exemplo, se a regra para o **Número Argentina.DNI** tiver elevados falsos positivos, pode limpar essa caixa de verificação específica.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Screenshot mostrando como selecionar as regras do sistema.":::

1. Selecione **Criar** para terminar a criação do conjunto de regras de digitalização.

### <a name="create-a-custom-file-type"></a>Criar um tipo de ficheiro personalizado

O Azure Purview suporta adicionar uma extensão personalizada e definir um delimiter de coluna personalizado num conjunto de regras de digitalização.

Para criar um tipo de ficheiro personalizado:

1. Siga os passos 1-5 em [Passos para criar um conjunto de regras](#steps-to-create-a-scan-rule-set) de digitalização ou edite um conjunto de regras de digitalização existente.

1. Na página **'Selecione' tipos de ficheiros,** selecione **Novo tipo de ficheiro** para criar um novo tipo de ficheiro personalizado.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Screenshot mostrando como selecionar novo tipo de ficheiro a partir da página de tipos de ficheiros Select.":::

1. Introduza uma **extensão de ficheiro** e uma **descrição** opcional.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Screenshot mostrando a nova página de tipo de ficheiro personalizado." border="true":::

1. Faça uma das seguintes seleções para **o conteúdo do Ficheiro no interior** para especificar o tipo de conteúdo de ficheiro dentro do seu ficheiro:

   - Selecione **O Delimiter Personalizado** e introduza o seu próprio **delimiter personalizado** (apenas um personagem).

   - Selecione **o Tipo de Ficheiro do Sistema** e escolha um tipo de ficheiro do sistema (por exemplo XML) da lista de down-down do tipo de ficheiro do **Sistema.**

1. Selecione **Criar** para guardar o ficheiro personalizado.

   O sistema volta à página **de tipos de ficheiros Select** e insere o novo tipo de ficheiro personalizado como um novo azulejo.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Screenshot mostrando o novo azulejo do tipo de ficheiro personalizado na página selecione de tipos de ficheiros.":::

1. **Selecione Editar** no novo azulejo do tipo de ficheiro se pretender alterá-lo ou eliminá-lo.

1. **Selecione Continue** a terminar configurando o conjunto de regras de digitalização.

## <a name="system-scan-rule-sets"></a>Conjuntos de regras de digitalização do sistema

Os conjuntos de regras de verificação do sistema são conjuntos de regras de verificação definidos pela Microsoft que são automaticamente criados para cada catálogo Azure Purview. Cada conjunto de regras de verificação do sistema está associado a um tipo específico de origem de dados. Quando criar uma varredura, pode associá-la a um conjunto de regras de digitalização do sistema. Sempre que a Microsoft fizer uma atualização a estas regras do sistema, pode atualizá-las no seu catálogo e aplicar a atualização a todas as verificações associadas.

1. Para visualizar a lista de conjuntos de regras de verificação do sistema, selecione **conjuntos de regras** de digitalização no **Centro de Gestão** e escolha o separador **Sistema.**

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Screenshot mostrando a lista de conjuntos de regras de verificação do sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Cada conjunto de regras de verificação do sistema tem um **Nome**, **tipo fonte** e uma **versão**. Se selecionar o número de versão de uma regra de digitalização definida na coluna **Versão,** vê as regras associadas à versão atual e às versões anteriores (se houver).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Screenshot mostrando uma página definida por regra de verificação do sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Se houver uma atualização disponível para uma definição de regra de verificação do sistema, pode selecionar **Update** na coluna **Versão.** Na página de regras de verificação do sistema, escolha entre uma versão da **versão Select uma nova versão para atualizar a** lista de drop-down. A página fornece uma lista de regras de classificação do sistema associadas à nova versão e versão atual.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Screenshot mostrando como alterar a versão de um conjunto de regras de verificação do sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Associar uma digitalização com um conjunto de regras de verificação do sistema

Quando [criar uma verificação,](tutorial-scan-data.md#scan-data-into-the-catalog)pode optar por associá-la a uma regra de verificação do sistema definida da seguinte forma:

1. Na página **de definição de regra de verificação Selecione,** selecione o conjunto de regras de verificação do sistema.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Screenshot mostrando como selecionar uma regra de verificação do sistema definida para uma verificação." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. **Selecione Continue** e, em seguida, selecione **Guardar e Executar**.
