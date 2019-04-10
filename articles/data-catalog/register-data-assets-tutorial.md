---
title: Registar recursos de dados no catálogo de dados do Azure
description: Como registar recursos de dados no seu catálogo de dados do Azure
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: tutorial
ms.date: 04/08/2019
ms.openlocfilehash: e89bd4806e2bb2369c100e948be51dcf32f2e123
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362505"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Tutorial: Registar recursos de dados no catálogo de dados do Azure

Neste tutorial, utilize a ferramenta de registo para registar recursos de dados do exemplo de base de dados SQL do Azure com o catálogo. O registo é o processo de extrair metadados estruturais chave, por exemplo, nomes, tipos e localizações, a partir da origem de dados e dos recursos que contém e copiar esses metadados para o catálogo. A origem de dados e os recursos de dados permanecem onde estão, mas os metadados são utilizados pelo catálogo para os tornar mais facilmente detetáveis e compreensíveis.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Registar recursos de dados 
> * Recursos de dados de pesquisa
> * Anotar recursos de dados
> * Ligar a recursos de dados
> * Gerir recursos de dados
> * Eliminar recursos de dados

## <a name="prerequisites"></a>Pré-requisitos

Para começar a utilizar, tem de concluir o [guia de introdução](register-data-assets-tutorial.md).

* R [Microsoft Azure](https://azure.microsoft.com/) subscrição.
* Tem de ter sua própria [inquilino do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Para configurar o catálogo de dados, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

## <a name="register-data-assets"></a>Registar recursos de dados

### <a name="register-a-data-source"></a>Registar origens de dados

Registar recursos de dados (tabelas) de uma [exemplo de base de dados SQL do Azure](../sql-database/sql-database-single-database-get-started.md), mas pode utilizar qualquer origem de dados suportada se preferir trabalhar com dados que lhe sejam familiares e relevantes para a sua função. Para obter uma lista de origens de dados suportadas, veja [Origens de dados suportadas](data-catalog-dsr.md).

O nome de base de dados SQL do Azure, estamos a utilizar neste tutorial é *RLSTest*.

Agora, pode registar recursos de dados do exemplo de base de dados SQL do Azure utilizando o catálogo de dados do Azure.

1. Vá para o [home page do catálogo de dados do Azure](http://azuredatacatalog.com) e selecione **publicar dados**.

   ![Catálogo de Dados do Azure – Botão “Publicar Dados”](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Selecione **iniciar aplicação** para transferir, instalar e executar a ferramenta de registo no seu computador.

   ![Catálogo de Dados do Azure – Botão “Iniciar”](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Sobre o **bem-vindo** página, selecione **iniciar sessão** e introduza as credenciais.

    ![Catálogo de Dados do Azure – página Boas-vindas](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Sobre o **catálogo de dados do Microsoft Azure** página, selecione **do SQL Server** e **seguinte**.

    ![Catálogo de Dados do Azure – origens de dados](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Introduza as propriedades de ligação do SQL Server para o seu exemplo de base de dados SQL do Azure e selecione **CONNECT**.

   ![Catálogo de Dados do Azure – definições de ligação do SQL Server](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registe os metadados do recurso de dados. Neste exemplo, registra **produto** objetos a partir do espaço de nomes de exemplo de base de dados SQL do Azure:

    1. Na **hierarquia de servidores** de árvore, expanda o seu exemplo de base de dados SQL do Azure e selecione **SalesLT**.

    2. Selecione **produto**, **ProductCategory**, **ProductDescription**, e **ProductModel** utilizando Ctrl + selecionar.

    3. Selecione o **seta selecionada mover** (**>**). Esta ação move todos os objetos selecionados para a lista **Objetos a registar**.

          ![Tutorial do Catálogo de Dados do Azure – procurar e selecionar objetos](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Selecione **Incluir uma Pré-visualização** para incluir uma pré-visualização de instantâneo dos dados. O instantâneo inclui até 20 registos de cada tabela e é copiado para o catálogo.

    5. Selecione **Incluir Perfil de Dados** para incluir um instantâneo das estatísticas dos objetos relativas ao perfil de dados (por exemplo, valores mínimos, máximos e médios de uma coluna, número de linhas, etc.).

    6. Na **adicionar etiquetas** , insira **vendas, produto, sql do azure**. Esta ação irá adicionar etiquetas de pesquisa a estes recursos de dados. As etiquetas são uma excelente forma de ajudar os utilizadores a localizar uma origem de dados registada.

    7. Especifique o nome de um **especialista** sobre estes dados (opcional).

          ![Tutorial do Catálogo de Dados do Azure – objetos a registar](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Selecione **registar**. O Catálogo de Dados do Azure regista os objetos selecionados. Neste exercício, os objetos selecionados de seu exemplo de base de dados SQL do Azure estão registados. A ferramenta de registo extrai metadados do recurso de dados e copia-os para o serviço Catálogo de Dados do Azure. Os dados permanecem onde atualmente permanece. Os dados permanecem sob o controle dos administradores e políticas do sistema de origem.

          ![Catálogo de Dados do Azure – objetos registados](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Para ver os objetos de origem de dados registados, selecione **ver Portal**. No portal do catálogo de dados do Azure, confirme que vê as quatro tabelas e a base de dados na vista de grelha (Certifique-se de que a barra de pesquisa é clara).

        ![Objetos no portal do Catálogo de Dados do Azure](media/register-data-assets-tutorial/data-catalog-view-portal.png)

Neste exercício, registou objetos do exemplo de base de dados SQL do Azure para que possam ser facilmente detetados pelos utilizadores na sua organização.

No exercício seguinte, vai aprender a detetar recursos de dados registados.

## <a name="discover-data-assets"></a>Detetar recursos de dados

A deteção no Catálogo de Dados do Azure utiliza dois mecanismos principais – pesquisa e filtragem.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Pode selecionar características específicas, como especialistas, tipo de origem de dados, tipo de objeto e etiquetas, para ver os recursos de dados correspondentes e para restringir os resultados da pesquisa a recursos correspondentes.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente as origens de dados que estão registadas no catálogo de dados do Azure.

Neste exercício, vai utilizar o portal do Catálogo de Dados do Azure para detetar os recursos de dados que registou no exercício anterior. Para obter detalhes sobre a sintaxe da pesquisa, veja [Data Catalog Search syntax reference (Referência para a sintaxe da Pesquisa do Catálogo de Dados)](/rest/api/datacatalog/#search-syntax-reference).

Seguem-se alguns exemplos para detetar recursos de dados no catálogo.  

### <a name="discover-data-assets-with-basic-search"></a>Detetar recursos de dados com a pesquisa básica

A pesquisa básica ajuda-o a pesquisar catálogos com um ou mais termos de pesquisa. Os resultados são qualquer recurso que corresponda a uma propriedade com um ou mais dos termos especificados.

1. Selecione **home page** no portal do catálogo de dados do Azure. Se tiver fechado o browser, vá para o [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com).

2. Na caixa de pesquisa, introduza `product` e prima **ENTER**.

    ![Catálogo de Dados do Azure – pesquisa de texto básica](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Confirme que vê as quatro tabelas e a base de dados nos resultados. Pode alternar entre **vista de grelha** e **vista de lista** selecionando botões na barra de ferramentas, conforme mostrado na imagem seguinte. Tenha em atenção que a palavra-chave da pesquisa é realçada nos resultados da pesquisa porque a opção **Realçar** está **LIGADA**. Também pode especificar o número de **resultados por página** nos resultados de pesquisa.

    ![Catálogo de Dados do Azure – resultados da pesquisa de texto básica](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    O painel **Pesquisas** está no lado esquerdo e o painel **Propriedades** no lado direito. No painel **Pesquisas**, pode alterar os critérios de pesquisa e filtrar os resultados. O painel **Propriedades** apresenta as propriedades de um objeto selecionado na grelha ou na lista.

4. Selecione **produto** nos resultados da pesquisa. Selecione o **pré-visualização**, **colunas**, **perfil de dados**, e **documentação** guias ou selecione a seta para expandir o painel inferior.  

    ![Catálogo de Dados do Azure – painel inferior](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    No separador **Pré-visualização**, é apresentada uma pré-visualização dos dados na tabela **Produto**.  
5. Selecione o **colunas** separador para obter detalhes sobre as colunas (como **nome** e **tipo de dados**) no recurso de dados.

6. Selecione o **perfil de dados** guia para ver a criação de perfis de dados (por exemplo: número de linhas, o tamanho dos dados ou o valor mínimo numa coluna) no recurso de dados.

### <a name="discover-data-assets-with-property-scoping"></a>Detetar recursos de dados com o âmbito de propriedade

O âmbito de propriedade ajuda-o a detetar recursos de dados quando o termo de pesquisa tem correspondência com a propriedade especificada.

1. Limpe o filtro **Tabela** em **Tipo de Objeto**, nos **Filtros**.  

2. Na caixa de pesquisa, introduza `tags:product` e prima **ENTER**. Veja [Data Catalog Search syntax reference (Referência para a sintaxe da Pesquisa do Catálogo de Dados)](/rest/api/datacatalog/#search-syntax-reference) para ver todas as propriedades que pode utilizar para pesquisar o catálogo de dados.

3. Confirme que vê as tabelas e a base de dados nos resultados.  

    ![Catálogo de Dados – resultados de pesquisa de âmbito de propriedade](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Guardar a pesquisa

1. Na **pesquisas** painel na **pesquisa atual** secção, introduza um nome para a pesquisa e selecione **guardar**.

    ![Catálogo de Dados do Azure – guardar pesquisa](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Confirme que a pesquisa guardada aparece em **Pesquisas Guardadas**.

3. Selecione uma das ações que pode realizar na pesquisa guardada (**Mudar o nome**, **Eliminar**, **Guardar Como Pesquisa Predefinida**).

### <a name="grouping-with-parentheses"></a>Agrupar com parênteses

Ao agrupar com parênteses, pode agrupar partes da consulta para alcançar isolamento lógico, especialmente em conjunto com operadores Booleanos.

1. Na caixa de pesquisa, introduza `name:product AND (tags:product AND objectType:table)` e prima **ENTER**.

2. Confirme que só vê a tabela **Product** nos resultados da pesquisa.

    ![Catálogo de Dados do Azure – agrupar pesquisa](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Operadores de Comparação

Com os operadores de comparação, pode utilizar comparações além das de igualdade para propriedades que têm tipos de dados numéricos e de data.

1. Na caixa de pesquisa, introduza `lastRegisteredTime:>"06/09/2016"`.

2. Limpe o filtro **Tabela** em **Tipo de Objeto**.

3. Prima **ENTER**.

4. Confirme que vê a **produto**, **ProductCategory**, e **ProductDescription** tabelas e a base de dados SQL do Azure que registou nos resultados da pesquisa.

    ![Catálogo de Dados do Azure – resultados da pesquisa de comparação](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Ver [como detetar recursos de dados](data-catalog-how-to-discover.md) para obter informações detalhadas sobre a deteção de recursos de dados. Para obter mais informações sobre a sintaxe de pesquisa, consulte [referência de sintaxe de pesquisa do catálogo de dados](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Anotar recursos de dados

Neste exercício, vai utilizar o portal do catálogo de dados do Azure para anotar (adicionar informações como descrições, etiquetas ou especialistas) recursos de dados existentes no catálogo. As anotações complementam os metadados estruturais extraídos da origem de dados durante o registo. Anotação torna muito mais fácil detetar e compreender os recursos de dados.

Neste exercício, vai anotar um recurso de dados individual (ProductPhoto). Vai adicionar um nome amigável e uma descrição ao recurso de dados ProductPhoto.  

1. Vá para o [home page do catálogo de dados do Azure](https://www.azuredatacatalog.com) e pesquise com `tags:product` para encontrar os recursos de dados que registou.

2. Selecione **ProductModel** nos resultados da pesquisa.  

3. Introduza **Imagens do produto** em **Nome Amigável** e **Fotografias do produto para os materiais de marketing** na **Descrição**.

    ![Catálogo de Dados do Azure – descrição de ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    A **Descrição** ajuda os outros utilizadores a detetar e compreender porquê e como utilizar os recursos de dados selecionados. Também pode adicionar mais etiquetas e ver as colunas. Pode pesquisar e filtrar as origens de dados utilizando os metadados descritivos que adicionou ao catálogo.

Também pode efetuar os seguintes passos nesta página:

* Adicionar especialistas ao recurso de dados. Selecione **Add** no **especialistas** área.

* Adicionar etiquetas ao nível do conjunto de dados. Selecione **Add** no **etiquetas** área. Uma etiqueta pode ser uma etiqueta de utilizador ou uma etiqueta do glossário. A Edição Standard do Catálogo de Dados inclui um glossário comercial que ajuda os administradores do catálogo a definir uma taxonomia comercial central. Os utilizadores do catálogo podem assim anotar recursos de dados com os termos do glossário. Para obter mais informações, veja [How to set up the Business Glossary for Governed Tagging (Como configurar o Glossário Comercial para Etiquetagem Regida)](data-catalog-how-to-business-glossary.md).

* Adicionar etiquetas ao nível da coluna. Selecione **Add** sob **etiquetas** para a coluna que quer anotar.

* Adicionar descrição ao nível da coluna. Introduza **Descrição** numa coluna. Também pode ver os metadados da descrição extraídos da origem de dados.

* Adicionar informações de **Pedir acesso** que mostra aos utilizadores como pedir acesso ao recurso de dados.
  
* Escolha o separador **Documentação** e disponibilize documentação para o recurso de dados. Com a documentação do Catálogo de Dados do Azure, pode utilizar o seu catálogo de dados como um repositório de conteúdos para criar uma narrativa completa dos seus recursos de dados.
  
Também pode adicionar uma anotação a vários recursos de dados. Por exemplo, pode selecionar todos os recursos de dados que registou e especificar um especialista para os mesmos.

![Catálogo de Dados do Azure – anotar vários recursos de dados](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

O Catálogo de Dados do Azure suporta uma abordagem baseada em “crowd-sourcing” nas anotações. Qualquer utilizador do catálogo de dados pode adicionar etiquetas (utilizador ou glossário), descrições e outros metadados. Ao fazer isso, os utilizadores adicionar o ponto de vista de um recurso de dados e seu uso e compartilham essa perspetiva com outros utilizadores.

Veja [How to annotate data assets (Como anotar recursos de dados)](data-catalog-how-to-annotate.md) para obter informações detalhadas sobre como anotar recursos de dados.

## <a name="connect-to-data-assets"></a>Ligar a recursos de dados

Neste exercício, vai abrir os recursos de dados numa ferramenta de cliente integrada (Excel) e numa ferramenta não integrada (SQL Server Management Studio) mediante a utilização de informações de ligação.

> [!NOTE]
> É importante lembrar-se de que o Catálogo de Dados do Azure não lhe dá acesso à origem de dados real, apenas lhe permite detetá-la e compreendê-la mais facilmente. Quando se liga a uma origem de dados, a aplicação cliente que escolher utiliza as suas credenciais do Windows ou pede-lhe as credenciais, conforme necessário. Se não lhe tiver sido concedido anteriormente acesso à origem de dados, este tem de lhe ser concedido para que se possa ligar.

### <a name="connect-to-a-data-asset-from-excel"></a>Ligar a um recurso de dados a partir do Excel

1. Selecione **Product** a partir dos resultados de pesquisa. Selecione **abrir em** na barra de ferramentas e selecione **Excel**.

    ![Catálogo de Dados do Azure – ligar ao recurso de dados](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Selecione **aberto** na janela de pop-up de transferência. Esta experiência pode variar consoante o browser.

3. Na **aviso de segurança do Microsoft Excel** janela, selecione **ativar**.

    ![Catálogo de Dados do Azure – pop-up de segurança do Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Mantenha as predefinições na **importar dados** caixa de diálogo e selecione **OK**.

    ![Catálogo de Dados do Azure – importar dados do Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Veja a origem de dados no Excel.

    ![Catálogo de Dados do Azure – tabela “product” no Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Neste exercício, ligou a recursos de dados que foram detetados com o Catálogo de Dados do Azure. Com o portal do Catálogo de Dados do Azure, pode ligar diretamente através das aplicações cliente integradas no menu **Abrir em**. Também pode utilizar as informações de localização incluídas nos metadados do recurso para ligar com qualquer aplicação que quiser. Por exemplo, pode utilizar o SQL Server Management Studio para ligar à base de dados SQL do Azure para acessar os dados nos recursos de dados registados neste tutorial.

1. Abra o **SQL Server Management Studio**.

2. Na caixa de diálogo **Ligar ao Servidor**, introduza o nome do servidor do painel **Propriedades** no portal do Catálogo de Dados do Azure.

3. Utilize a autenticação e as credenciais adequadas para aceder ao recurso de dados. Se não tiver acesso, utilize as informações no campo **Pedir Acesso** para o obter.

    ![Catálogo de Dados do Azure – pedir acesso](media/register-data-assets-tutorial/data-catalog-request-access.png)

Selecione **ver cadeias de ligação** para ver e copiar as cadeias de ligação do ADO.NET, ODBC e OLEDB para a área de transferência para utilização na sua aplicação.

## <a name="manage-data-assets"></a>Gerir recursos de dados

Neste passo, vai saber como configurar a segurança dos seus recursos de dados. Catálogo de dados não concede acesso a dados propriamente ditos. O proprietário da origem de dados controla o acesso aos dados.

Pode utilizar o Catálogo de Dados para detetar origens de dados e ver os metadados relacionados com as origens registadas no catálogo. Contudo, poderá haver situações em que as origens de dados devem estar visíveis apenas a determinados utilizadores ou a membros de grupos específicos. Nestes cenários, pode utilizar o catálogo de dados para assumir a propriedade dos recursos de dados registados e controlar a visibilidade dos quais que é proprietário.

> [!NOTE]
> As capacidades de gestão descritas neste exercício só estão disponíveis na Edição Standard do Catálogo de Dados do Azure e não na Edição Gratuita.
> No Catálogo de Dados do Azure, pode assumir a propriedade, adicionar coproprietários e definir a visibilidade dos recursos de dados.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Assumir a propriedade de recursos de dados e restringir a visibilidade

1. Aceda à [home page do Catálogo de Dados do Azure](https://www.azuredatacatalog.com). Na caixa de **Pesquisa**, introduza `tags:cycles` e prima **ENTER**.

2. Selecione um item na lista de resultados e selecione **obter propriedade** na barra de ferramentas.

3. No **gerenciamento** secção a **propriedades** painel, selecione **obter propriedade**.

    ![Catálogo de Dados do Azure – obter propriedade](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Para restringir a visibilidade, escolha **proprietários e estes utilizadores** no **visibilidade** secção e selecione **Add**. Introduza os endereços de e-mail dos utilizadores na caixa de texto e prima **ENTER**.

    ![Catálogo de Dados do Azure – restringir acesso](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Remover recursos de dados

Neste exercício, vai utilizar o portal do Catálogo de Dados do Azure para remover dados de pré-visualização dos recursos de dados registados e eliminar recursos de dados do catálogo.

No Catálogo de Dados do Azure, pode eliminar um recurso individual ou vários recursos.

1. Aceda à [home page do Catálogo de Dados do Azure](https://www.azuredatacatalog.com).

2. Na **pesquisa** texto, digite `tags:cycles` e selecione **ENTER**.

3. Selecione um item na lista de resultados e selecione **eliminar** na barra de ferramentas, conforme mostrado na imagem seguinte:

    ![Catálogo de Dados do Azure – eliminar item de grelha](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Se estiver a utilizar a vista de lista, é a caixa de verificação à esquerda do item, conforme mostrado na imagem seguinte:

    ![Catálogo de Dados do Azure – eliminar item de lista](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    Também pode selecionar vários recursos de dados e eliminá-los, conforme mostrado na imagem seguinte:

    ![Catálogo de Dados do Azure – eliminar vários recursos de dados](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> O comportamento predefinido do catálogo é permitir que qualquer utilizador registe uma origem de dados e permitir que qualquer utilizador possa eliminar qualquer recurso de dados que foi registado. As capacidades de gestão incluídas na Edição Standard do Catálogo de Dados do Azure oferecem opções adicionais para obter a propriedade de recursos, restringir quem os detetar e quem os pode eliminar.

## <a name="summary"></a>Resumo

Neste tutorial, explorou as capacidades essenciais do Catálogo de Dados do Azure, incluindo registar, anotar, detetar e gerir recursos de dados empresariais. Agora que concluiu o tutorial, está na altura de começar. Pode começar hoje, registando as origens de dados de que você e a sua equipa dependem e convidando colegas para utilizarem o catálogo.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Origens de dados suportadas](data-catalog-dsr.md)