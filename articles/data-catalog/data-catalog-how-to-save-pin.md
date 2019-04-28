---
title: Guardar pesquisas e afixar recursos de dados no catálogo de dados do Azure
description: Artigo que mostra como realce capacidades no catálogo de dados do Azure para guardar as origens de dados e recursos de dados para utilização posterior.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: df3220ddb80ebc329ed6b0024ca4eefd2bdfb321
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61002067"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Guardar pesquisas e afixar recursos de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
O catálogo de dados do Azure fornece capacidades de deteção de origens de dados. Rapidamente, pode pesquisar e filtrar o catálogo para localizar as origens de dados e compreender o respetivo objetivo pretendido, tornando mais fácil encontrar os dados corretos para a tarefa em questão.

Mas e se precisar trabalhar regularmente com os mesmos dados? E, e se e a outros utilizadores regularmente contribuem seu conhecimento para as mesmas origens de dados no catálogo? Nestas situações, a necessidade de emitir repetidamente as mesmas pesquisas pode ser ineficiente. Isso é onde podem ajudar a pesquisa guardada e recursos de dados afixados.

## <a name="saved-searches"></a>Pesquisas guardadas
Uma pesquisa guardada no catálogo de dados é uma definição de pesquisa de usuário reutilizáveis. Pode definir uma pesquisa, incluindo termos de pesquisa, etiquetas e outros filtros e, em seguida, guardá-lo. Pode executar novamente a definição de pesquisa guardada mais tarde para retornar a qualquer recurso de dados que correspondem aos seus critérios de pesquisa.

### <a name="create-a-saved-search"></a>Criar uma pesquisa guardada
Para criar uma procura guardada, faça o seguinte:
1. No portal do catálogo de dados do Azure, no **pesquisa atual** janela, clique em **guardar**. 

    ![Ligação de salvamento de definições de pesquisa atual](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Introduza os critérios de pesquisa que pretende reutilizar e, em seguida, clique em **guardar**.

    ![Nome da pesquisa guardadas as configurações de pesquisa atuais](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando lhe for pedido, introduza um nome para a pesquisa guardada. Escolha um nome significativo e que descreve os recursos de dados que vão ser devolvidos pela pesquisa.

### <a name="manage-saved-searches"></a>Gerir as pesquisas guardadas
Depois de guardar um ou mais pesquisas, um **pesquisas guardadas** opção é apresentada abaixo da **pesquisa atual** caixa. Quando a lista é expandida, todas as pesquisas guardadas são apresentadas.

 ![Lista de pesquisas guardadas](./media/data-catalog-how-to-save-pin/03-list.png)

Efetue um dos seguintes procedimentos:

* Para executar uma pesquisa, selecione uma procura guardada na lista.

* Para ver uma lista de opções de gestão para uma procura guardada, clique na seta para baixo junto ao nome da pesquisa.

    ![Opções para gerir as pesquisas guardadas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Para introduzir um novo nome para a pesquisa guardada, selecione **mudar o nome**. A definição de pesquisa não é alterada.

* Para remover a pesquisa guardada de sua lista, selecione **eliminar**e, em seguida, confirmar a eliminação.

* Para marcar a pesquisa guardada como a pesquisa de predefinido, selecione **guardar como predefinição**. Se realizar uma pesquisa "vazia" a partir da home page do catálogo de dados do Azure, a pesquisa do padrão é executada. Além disso, a pesquisa que está marcada como a pesquisa predefinida é apresentada na parte superior a **pesquisas guardadas** lista.

### <a name="organizational-saved-searches"></a>Pesquisas guardadas organizacionais
Todos os utilizadores na sua organização podem guardar pesquisas para utilização pessoal. Os administradores do catálogo de dados também podem guardar pesquisas para todos os utilizadores dentro da organização. Quando os administradores de salvar uma pesquisa, são apresentados com um **partilha dentro da empresa** opção. A seleção desta opção compartilha a pesquisa guardada para todos os utilizadores na organização.

 ![Pesquisas guardadas organizacionais](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Recursos de dados afixados
Com pesquisas guardadas, pode guardar e reutilizar as definições de pesquisa. Os recursos de dados que são devolvidos pelas pesquisas podem ser alterados ao longo do tempo, como o conteúdo da alteração de catálogo. Quando afixar recursos de dados, pode identificar explicitamente recursos de dados específicos para que fiquem mais fáceis de acessar sem a necessidade de utilizar uma pesquisa.

A afixação de um recurso de dados é simples. Para adicionar o recurso de dados à sua lista afixada, basta clicar o **pin** ícone. O ícone é apresentado no canto do mosaico do recurso na vista de mosaico e na coluna mais à esquerda na vista de lista no portal do catálogo de dados do Azure.

![O ícone de pin do recurso de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

Um recurso de dados, está também é igualmente simples. Basta clicar o **desafixar** ícone para ativar/desativar a definição para o recurso selecionado.

![Ícone de remover o recurso de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A secção de meus recursos
A home page de portal do catálogo de dados inclui um **My ativos** secção que apresenta os ativos de interesse para o utilizador atual. Esta secção inclui os dois recursos afixados e pesquisas guardadas.

![A secção de meus recursos na home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
O catálogo de dados do Azure fornece capacidades que torna mais fácil detetar as origens de dados que for necessário, para que e outros membros da organização podem gastar menos tempo à procura de dados e mais tempo a trabalhar com ele. Pesquisas guardadas e afixados dados ativos criar com esses recursos principais, para que os utilizadores podem identificar facilmente origens de dados que funcionam com repetidamente.
