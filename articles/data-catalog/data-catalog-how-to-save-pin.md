---
title: Salvar pesquisas e fixar ativos de dados no catálogo de dados do Azure
description: Artigo de instruções destacando recursos no catálogo de dados do Azure para salvar fontes de dados e ativos de dados para uso posterior.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 8e2a98748f4fef69b4fd6e3b32d15560641a6336
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737047"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Salvar pesquisas e fixar ativos de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
O catálogo de dados do Azure fornece recursos para descoberta de fonte de dados. Você pode pesquisar e filtrar rapidamente o catálogo para localizar fontes de dados e entender a finalidade desejada, facilitando a localização dos dados corretos para o trabalho em questão.

Mas e se você precisar trabalhar regularmente com os mesmos dados? E se você e outros usuários contribuirem regularmente com o seu conhecimento para as mesmas fontes de dados no catálogo? Nessas situações, ter que emitir repetidamente as mesmas pesquisas pode ser ineficiente. É aqui que os ativos de pesquisa salva e dados fixados podem ajudar.

## <a name="saved-searches"></a>Pesquisas guardadas
Uma pesquisa salva no catálogo de dados é uma definição de pesquisa reutilizável por usuário. Você pode definir uma pesquisa, incluindo termos de pesquisa, marcas e outros filtros, e salvá-lo. Você pode executar novamente a definição de pesquisa salva posteriormente para retornar quaisquer ativos de dados que correspondam aos seus critérios de pesquisa.

### <a name="create-a-saved-search"></a>Criar uma pesquisa salva
Para criar uma pesquisa salva, faça o seguinte:
1. No portal do catálogo de dados do Azure, na janela **pesquisa atual** , clique em **salvar**. 

    ![Link para salvar configurações da pesquisa atual](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Insira os critérios de pesquisa que você deseja reutilizar e, em seguida, clique em **salvar**.

    ![Configurações da pesquisa atual-nome de pesquisa salvo](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando solicitado, insira um nome para a pesquisa salva. Escolha um nome que seja significativo e que descreva os ativos de dados que serão retornados pela pesquisa.

### <a name="manage-saved-searches"></a>Gerenciar pesquisas salvas
Depois de salvar uma ou mais pesquisas, uma opção **pesquisas salvas** é exibida abaixo da caixa de **pesquisa atual** . Quando a lista é expandida, todas as pesquisas salvas são exibidas.

 ![Lista de pesquisas salvas](./media/data-catalog-how-to-save-pin/03-list.png)

Siga um destes procedimentos:

* Para executar uma pesquisa, selecione uma pesquisa salva na lista.

* Para exibir uma lista de opções de gerenciamento para uma pesquisa salva, clique na seta para baixo ao lado do nome da pesquisa.

    ![Opções para gerenciar pesquisas salvas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Para inserir um novo nome para a pesquisa salva, selecionerenomear. A definição de pesquisa não é alterada.

* Para remover a pesquisa salva da lista, selecione **excluir**e confirme a exclusão.

* Para marcar a pesquisa salva como sua pesquisa padrão, selecione **salvar como padrão**. Se você executar uma pesquisa "vazia" do catálogo de dados do Azure home page, sua pesquisa padrão será executada. Além disso, a pesquisa marcada como a pesquisa padrão é exibida na parte superior da lista de **pesquisas salvas** .

### <a name="organizational-saved-searches"></a>Pesquisas salvas na organização
Todos os usuários em sua organização podem salvar pesquisas para seu próprio uso. Os administradores do catálogo de dados também podem salvar pesquisas para todos os usuários na organização. Quando os administradores salvam uma pesquisa, são apresentados um **compartilhamento dentro da opção da empresa** . A seleção dessa opção compartilha a pesquisa salva para todos os usuários na organização.

 ![Pesquisas salvas na organização](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Ativos de dados fixados
Com as pesquisas salvas, você pode salvar e reutilizar definições de pesquisa. Os ativos de dados retornados pelas pesquisas podem mudar ao longo do tempo conforme o conteúdo do catálogo é alterado. Ao fixar ativos de dados, você pode identificar explicitamente ativos de dados específicos para torná-los mais fáceis de acessar sem a necessidade de usar uma pesquisa.

A fixação de um ativo de dados é simples. Para adicionar o ativo de dados à lista fixada, basta clicar no ícone de **pino** . O ícone é exibido no canto do bloco do ativo na exibição de bloco e na coluna mais à esquerda no modo de exibição de lista no portal do catálogo de dados do Azure.

![O ícone de PIN de ativo de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

A desfixação de um ativo de dados é igualmente simples. Basta clicar no ícone Desafixar para alternar a configuração do ativo selecionado.

![O ícone de desafixação de ativo de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A seção meus ativos
O portal do catálogo de dados home page inclui uma seção **meus ativos** que exibe os ativos de interesse do usuário atual. Esta seção inclui ativos fixos e pesquisas salvas.

![A seção meus ativos na home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
O catálogo de dados do Azure fornece recursos que facilitam a descoberta das fontes de dados de que você precisa para que você e outros membros da organização possam gastar menos tempo procurando dados e mais tempo trabalhando com ele. Pesquisas salvas e ativos de dados fixados se baseiam nesses principais recursos para que os usuários possam identificar facilmente as fontes de dados com as quais trabalham com várias vezes.
