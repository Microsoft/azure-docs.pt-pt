---
title: Como criar, importar e exportar termos glossários
description: Aprenda a criar, importar e exportar termos glossários em Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 6f7f481ae0e0c75b14d894080f791161346cd93f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952410"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Como criar, importar e exportar termos glossários

Este artigo descreve como criar um termo glossário de negócios no catálogo de dados Azure Purview, e importar e exportar termos glossários usando ficheiros .csv.

## <a name="create-a-new-term"></a>Criar um novo termo

Para criar um novo termo glossário, faça os seguintes passos:

1. Selecione o ícone glossário na navegação esquerda na página inicial para ir à página da lista de termos.

2. Na página **de termos glossários,** selecione **+ novo termo**. Uma página abre com o modelo **de Padrão do Sistema** selecionado. Escolha o modelo com o qual pretende criar um termo glossário e selecione **Continue**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Screenshot da criação do novo termo." border="true":::

3. Dê um nome ao seu novo termo, que deve ser único no catálogo. O nome do termo é sensível a casos, o que significa que pode ter um termo chamado **Sample** e **sample** no catálogo.

4. Adicione uma **definição**.

5. Desa estação o **estado do** termo. Novos termos por defeito do **estado do Projeto.**

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Screenshot das escolhas de estado." border="true":::

   Estes marcadores de estado são metadados associados ao termo. Atualmente pode definir o seguinte estado em cada termo:

   - **Rascunho**: Este termo ainda não está oficialmente implementado.
   - **Aprovado**: Este termo é oficial/standard/aprovado.
   - **Expirado**: Este termo não deve mais ser utilizado.
   - **Alerta:** Este termo precisa de atenção.

6. Adicionar **Recursos** e **Acrónimo.** Se o termo fizer parte da hierarquia, pode adicionar os termos dos pais no **separador "Pais"** no separador visão geral.

7. Adicione **Sinónimos** e **termos relacionados** no separador relacionado.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Screenshot do novo termo > separador Relacionado." border="true":::

8. Opcionalmente, selecione o separador **Contactos** para adicionar Peritos e Mordomos ao seu termo.

9. Selecione **Criar** para criar o seu termo.

## <a name="import-terms-into-the-glossary"></a>Termos de importação para o glossário

O Catálogo de Dados Azure Purview fornece um modelo .csv ficheiro para que você importe os seus termos no seu Glossário.

Pode importar termos no catálogo. Os termos duplicados em ficheiro serão substituídos.

Note que os nomes de termo são sensíveis a casos. Por exemplo, `Sample` e ambos podem existir no mesmo `saMple` glossário.

### <a name="to-import-terms-follow-these-steps"></a>Para importar termos, siga estes passos

1. Quando estiver na página **de termos glossários,** selecione **os termos de Importação**.

2. A página do modelo de termo abre. Combine o modelo do termo com o tipo de . CSV que quer importar.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Screenshot da página de termos glossários, botão de termos de importação.":::

3. Faça o download do modelo csv e use-o para introduzir os seus termos que gostaria de adicionar.

   > [!Important]
   > O sistema suporta apenas colunas de importação que estão disponíveis no modelo. O modelo "System Default" terá todos os atributos predefinidos.
   > No entanto, os modelos de prazo personalizado terão fora dos atributos da caixa e atributos personalizados adicionais definidos no modelo. Portanto, o . O ficheiro CSV difere tanto do número total de colunas como de nomes de colunas dependendo do modelo de termo selecionado. Também pode rever o ficheiro para problemas após o upload.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Screenshot da página de termos Glossário, selecione ficheiro para Importação.":::

4. Uma vez terminada a preenchimento do ficheiro .csv, selecione o seu ficheiro para importar e, em seguida, selecione **OK**.

5. O sistema irá carregar o ficheiro e adicionar todos os termos ao seu catálogo.
 
   > [!Important]
   > O endereço de e-mail para Stewards and Experts deve ser o endereço principal do utilizador do grupo AAD. O e-mail alternativo, o nome principal do utilizador e os e-mails não-AAD ainda não estão suportados. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Termos de exportação de glossário com atributos personalizados

Você deve ser capaz de exportar termos de glossário desde que os termos selecionados pertençam ao mesmo modelo de termo.

1. Quando estiver no Glossário, por predefinição o botão **Exportação** é desativado. Uma vez selecionado os termos que pretende exportar, o botão **Exportação** está ativado se os termos selecionados pertencerem ao mesmo modelo.

2. **Selecione Export** para baixar os termos selecionados.

 > [!Important]
   > Se os termos de uma hierarquia pertencerem a diferentes modelos de termo, então você precisa dividi-los em diferentes . Ficheiros CSV para importação. Além disso, a atualização de um termo não é atualmente apoiada através do processo de importação.


## <a name="next-steps"></a>Passos seguintes

Siga o [Tutorial: Crie e importe termos glossários](tutorial-import-create-glossary-terms.md) para saber mais.
