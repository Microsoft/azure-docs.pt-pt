---
title: Como gerir modelos de prazo para glossário de negócios
description: Aprenda a gerir modelos de prazo para glossário de negócios num catálogo de dados Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555338"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Como gerir modelos de prazo para glossário de negócios

O Azure Purview permite-lhe criar um glossário de termos que são importantes para enriquecer os seus dados. Cada novo termo adicionado ao seu Glossário de Catálogo de Dados de Purview baseia-se num modelo de termo que determina os campos para o termo. Este artigo descreve como criar um modelo de termo e atributos personalizados que podem ser associados a termos glossários.

## <a name="manage-term-templates-and-custom-attributes"></a>Gerir modelos de prazo e atributos personalizados

Usando modelos de termos, você pode criar atributos personalizados, agrupar-los e aplicar um modelo enquanto cria termos. Uma vez criado um termo, o modelo associado ao termo não pode ser alterado.

1. Na página de **termos Glossários,** **selecione Gerir os modelos de prazo .**

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Screenshot dos termos glossários > Gerir o botão de modelos de termo.":::

2. A página mostra tanto o sistema como os atributos personalizados. Selecione o **separador Personalizado** para criar ou editar modelos de prazo.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Screenshot dos termos glossários > Gerir a página de modelos de termo.":::

3. Selecione **+ modelo de novo termo** e introduza um nome e descrição do modelo.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Screenshot de termos glossários > Gerir modelos de prazo > modelos de novo termo":::

4. Selecione **+ Novo atributo** para criar um novo atributo personalizado para o modelo de termo. Introduza um nome de atributo, descrição. O nome de atributo personalizado deve ser único dentro de um modelo de termo, mas pode ser o mesmo nome pode ser reutilizado através de modelos.

   Escolha o tipo de campo na lista de opções **Texto,** **Escolha Única,** **Escolha Múltipla** ou  **Data**. Também pode fornecer uma cadeia de valor padrão para tipos de campo de texto.  O atributo também pode ser marcado conforme **necessário**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Screenshot dos termos glossários > nova página de atributos.":::

5. Assim que todos os atributos personalizados forem criados, selecione **Preview** para organizar a sequência de atributos personalizados. Pode arrastar e largar atributos personalizados na sequência desejada.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Screenshot dos termos glossários > modelo de termo de pré-visualização.":::

6. Uma vez definidos todos os atributos personalizados, **selecione Criar** para criar um modelo de termo com atributos personalizados.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Screenshot dos termos glossários > novo modelo de termo - Criar botão.":::

7. Os atributos personalizados existentes podem ser marcados como expirados, verificando **a Marca como Expirada**. Uma vez expirado, o atributo não pode ser reativado. O atributo expirado será acinzentado para os termos existentes. Futuros novos termos criados com este modelo de termo deixarão de mostrar o atributo que foi marcado expirado.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Screenshot de termos glossários > Editar para marcar como expirado.":::

## <a name="next-steps"></a>Passos seguintes

Siga o [Tutorial: Crie e importe termos glossários](tutorial-import-create-glossary-terms.md) para saber mais.
