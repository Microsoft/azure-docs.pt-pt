---
title: Guardar pesquisas e pin ativos de dados no Catálogo de Dados Azure
description: Como-a-lei destacando as capacidades no Catálogo de Dados Azure para guardar fontes de dados e ativos de dados para posterior utilização.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 52a8d8fcd0b9b9ee1ff918ead250c88b83f86cb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674840"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Guardar pesquisas e pin ativos de dados no Catálogo de Dados Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução
O Azure Data Catalog fornece capacidades para a descoberta de fontes de dados. Pode pesquisar e filtrar rapidamente o catálogo para localizar fontes de dados e compreender o propósito pretendido, facilitando a procura dos dados certos para o trabalho em questão.

Mas e se precisar trabalhar regularmente com os mesmos dados? E se você e outros utilizadores contribuírem regularmente com os seus conhecimentos para as mesmas fontes de dados do catálogo? Nestas situações, ter de emitir repetidamente as mesmas pesquisas pode ser ineficiente. É aqui que os ativos de pesquisa e dados fixos guardados podem ajudar.

## <a name="saved-searches"></a>Pesquisas guardadas
Uma pesquisa guardada no Catálogo de Dados é uma definição reutilizável por utilizador. Pode definir uma pesquisa, incluindo termos de pesquisa, etiquetas e outros filtros, e depois guardá-la. Pode re-executar a definição de pesquisa guardada mais tarde para devolver quaisquer ativos de dados que correspondam aos seus critérios de pesquisa.

### <a name="create-a-saved-search"></a>Criar uma pesquisa guardada
Para criar uma pesquisa guardada, faça o seguinte:
1. No portal do Catálogo de Dados Azure, na janela **'Pesquisa Corrente',** clique em **Guardar**. 

    ![Definições de pesquisa atuais Guardar link](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Introduza os critérios de pesquisa que pretende reutilizar e, em seguida, clique em **Guardar**.

    ![As definições de Pesquisa atuais salvaram o nome da pesquisa](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando for solicitado, insira um nome para a procura guardada. Escolha um nome que seja significativo e que descreva os ativos de dados que serão devolvidos pela pesquisa.

### <a name="manage-saved-searches"></a>Gerir pesquisas guardadas
Depois de ter guardado uma ou mais pesquisas, é apresentada uma opção **De Pesquisas Guardadas** por baixo da caixa **'Corrente'.** Quando a lista é expandida, todas as pesquisas guardadas são exibidas.

 ![Catálogo de Dados - Lista de pesquisas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Siga um dos seguintes procedimentos:

* Para executar uma pesquisa, selecione uma pesquisa guardada na lista.

* Para ver uma lista de opções de gestão para uma pesquisa guardada, clique na seta para baixo ao lado do nome de pesquisa.

    ![Opções para gerir pesquisas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Para introduzir um novo nome para a pesquisa guardada, **selecione Rename**. A definição de pesquisa não é alterada.

* Para remover a procura guardada da sua lista, **selecione Delete** e, em seguida, confirme a eliminação.

* Para marcar a procura guardada como a sua pesquisa predefinitiva, **selecione 'Guardar como padrão'.** Se efetuar uma pesquisa "vazia" na página inicial do Catálogo de Dados do Azure, a sua pesquisa padrão é executada. Além disso, a procura marcada como a pesquisa predefinida é exibida no topo da lista **de Pesquisas Salvas.**

### <a name="organizational-saved-searches"></a>Pesquisas organizacionais guardadas
Todos os utilizadores da sua organização podem guardar pesquisas para o seu próprio uso. Os administradores do Catálogo de Dados também podem guardar pesquisas para todos os utilizadores dentro da organização. Quando os administradores guardam uma pesquisa, são apresentados com uma **Partilha dentro da opção da empresa.** A seleção desta opção partilha a procura guardada para todos os utilizadores da organização.

 ![Catálogo de Dados - Pesquisas guardadas organizacionais](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Ativos de dados fixados
Com pesquisas guardadas, pode guardar e reutilizar definições de pesquisa. Os ativos de dados que são devolvidos pelas pesquisas podem mudar ao longo do tempo à medida que o conteúdo do catálogo muda. Ao fixar ativos de dados, pode identificar explicitamente ativos de dados específicos para facilitar o acesso sem necessidade de utilizar uma pesquisa.

Fixar um ativo de dados é simples. Para adicionar o ativo de dados à sua lista fixada, basta clicar no ícone **pino.** O ícone é apresentado no canto do azulejo do ativo na vista do azulejo e na coluna mais à esquerda na vista da lista no portal do Catálogo de Dados Azure.

![Catálogo de dados - O ícone do pino de dados-activo](./media/data-catalog-how-to-save-pin/05-pinning.png)

Desapinar um ativo de dados é igualmente simples. Basta clicar no ícone **unpin** para alternar a definição para o ativo selecionado.

![Catálogo de dados - O ícone unpin de dados-activo](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A secção My Assets
A página inicial do portal data catalog inclui uma secção **My Assets** que exibe ativos de interesse para o utilizador atual. Esta secção inclui tanto os ativos presos como as pesquisas guardadas.

![A secção My Assets na página inicial](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
O Azure Data Catalog fornece capacidades que facilitam a descoberta das fontes de dados de que necessita, para que você e outros membros da organização possam passar menos tempo à procura de dados e mais tempo a trabalhar com os mesmos. Pesquisas guardadas e ativos de dados fixos baseiam-se nestas capacidades fundamentais para que os utilizadores possam identificar facilmente as fontes de dados com as quais trabalham repetidamente.
