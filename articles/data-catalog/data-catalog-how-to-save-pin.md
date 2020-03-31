---
title: Guardar pesquisas e pin dados ativos no Catálogo de Dados do Azure
description: Como fazer artigo destacando capacidades no Catálogo de Dados do Azure para salvar fontes de dados e ativos de dados para posterior utilização.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976805"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Guardar pesquisas e pin dados ativos no Catálogo de Dados do Azure
## <a name="introduction"></a>Introdução
O Azure Data Catalog fornece capacidades para a descoberta de fontes de dados. Pode pesquisar e filtrar rapidamente o catálogo para localizar fontes de dados e compreender o seu propósito pretendido, facilitando a localização dos dados certos para o trabalho em questão.

Mas e se precisar trabalhar regularmente com os mesmos dados? E se você e outros utilizadores contribuírem regularmente com o seu conhecimento para as mesmas fontes de dados do catálogo? Nestas situações, ter de emitir repetidamente as mesmas buscas pode ser ineficiente. É aqui que os ativos de pesquisa e dados guardados podem ajudar.

## <a name="saved-searches"></a>Pesquisas guardadas
Uma pesquisa guardada no Data Catalog é uma definição de pesquisa reutilizável por utilizador. Pode definir uma pesquisa, incluindo termos de pesquisa, etiquetas e outros filtros, e depois guardá-la. Pode reexecutar a definição de pesquisa guardada mais tarde para devolver quaisquer ativos de dados que correspondam aos seus critérios de pesquisa.

### <a name="create-a-saved-search"></a>Criar uma pesquisa guardada
Para criar uma pesquisa guardada, faça o seguinte:
1. No portal Do Catálogo de Dados Azure, na janela **'Pesquisa Corrente',** clique em **Guardar**. 

    ![Definições de pesquisa de correnteS Salvar o link](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Introduza os critérios de pesquisa que pretende reutilizar e, em seguida, clique em **Guardar**.

    ![Definições de pesquisa de correntes salvou nome de pesquisa](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando for solicitado, insira um nome para a pesquisa guardada. Escolha um nome significativo e que descreva os ativos de dados que serão devolvidos pela pesquisa.

### <a name="manage-saved-searches"></a>Gerir pesquisas guardadas
Depois de ter guardado uma ou mais pesquisas, é apresentada uma opção **"Pesquisas Guardadas"** por baixo da caixa **de Pesquisa Atual.** Quando a lista é expandida, todas as pesquisas guardadas são exibidas.

 ![Catálogo de Dados - Lista de pesquisas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Siga um dos seguintes procedimentos:

* Para executar uma pesquisa, selecione uma pesquisa guardada na lista.

* Para ver uma lista de opções de gestão para uma pesquisa guardada, clique na seta para baixo ao lado do nome de pesquisa.

    ![Opções para gerir pesquisas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Para introduzir um novo nome para a pesquisa guardada, selecione **Rename**. A definição de pesquisa não é alterada.

* Para remover a procura guardada da sua lista, selecione **Eliminar**e, em seguida, confirmar a eliminação.

* Para marcar a procura guardada como a sua pesquisa predefinida, selecione **Guardar Como Padrão**. Se realizar uma pesquisa "vazia" na página inicial do Catálogo de Dados do Azure, a sua pesquisa por defeito é executada. Além disso, a pesquisa que está marcada à medida que a pesquisa padrão é exibida no topo da lista de **Pesquisas Guardadas.**

### <a name="organizational-saved-searches"></a>Buscas organizacionais salvas
Todos os utilizadores da sua organização podem guardar pesquisas para uso próprio. Os administradores do Data Catalog também podem guardar pesquisas para todos os utilizadores dentro da organização. Quando os administradores guardam uma pesquisa, são apresentados com uma **Partilha dentro da opção da empresa.** A seleção desta opção partilha a pesquisa guardada para todos os utilizadores da organização.

 ![Catálogo de Dados - Pesquisas guardadas organizacional](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Ativos de dados fixados
Com pesquisas guardadas, pode guardar e reutilizar definições de pesquisa. Os ativos de dados que são devolvidos pelas pesquisas podem mudar ao longo do tempo à medida que o conteúdo do catálogo muda. Ao fixar os ativos de dados, pode identificar explicitamente os ativos específicos dos dados para facilitar o acesso sem necessidade de utilizar uma pesquisa.

Fixar um ativo de dados é simples. Para adicionar o ativo de dados à sua lista fixa, basta clicar no ícone do **pino.** O ícone é exibido no canto do azulejo do ativo na vista de azulejos, e na coluna mais à esquerda na vista da lista no portal Do Catálogo de Dados Do Azure.

![Catálogo de dados - O ícone do pino de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

Desonrem um ativo de dados é igualmente simples. Basta clicar no ícone **de unpin** para alternar a definição para o ativo selecionado.

![Catálogo de Dados - O ícone de despin de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A secção My Assets
A página inicial do portal Data Catalog inclui uma secção **My Assets** que exibe ativos de interesse para o utilizador atual. Esta secção inclui tanto os ativos presos como as buscas guardadas.

![A secção My Assets na página inicial](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
O Azure Data Catalog fornece capacidades que facilitam a descoberta das fontes de dados de que necessita, para que você e outros membros da organização possam passar menos tempo à procura de dados e mais tempo a trabalhar com ele. Pesquisas guardadas e ativos de dados fixados baseiam-se nestas capacidades fundamentais para que os utilizadores possam identificar facilmente fontes de dados com as quais trabalham repetidamente.
