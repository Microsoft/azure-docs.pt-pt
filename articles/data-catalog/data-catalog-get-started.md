---
title: Introdução ao Catálogo de Dados do Azure
description: Tutorial ponto-a-ponto que apresenta os cenários e as capacidades do Catálogo de Dados do Azure.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 5513948a6611e93833c7e70fa4e5667b3901a9f3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880228"
---
# <a name="get-started-with-azure-data-catalog"></a>Introdução ao Catálogo de Dados do Azure
O Catálogo de Dados do Azure é um serviço em nuvem totalmente gerido que funciona como um sistema de registo e um sistema de deteção de recursos de dados empresariais. Para obter uma descrição detalhada, veja [O que é o Catálogo de Dados do Azure](data-catalog-what-is-data-catalog.md).

Este tutorial ajuda-o a começar a utilizar o Catálogo de Dados do Azure. Neste tutorial, vai executar os seguintes procedimentos:

| Procedimento | Descrição |
|:--- |:--- |
| [Aprovisionar o catálogo de dados](#provision-data-catalog) |Neste procedimento, vai aprovisionar ou configurar o Catálogo de Dados do Azure. Só realizará este passo se o catálogo ainda não tiver sido configurado. Só pode ter um catálogo de dados por organização (domínio do Microsoft Azure Active Directory), apesar de existirem várias subscrições associadas à sua conta do Azure. |
| [Registar recursos de dados](#register-data-assets) |Neste procedimento, vai registar recursos de dados da base de dados de exemplo AdventureWorks2014 no catálogo de dados. O registo é o processo de extrair metadados estruturais chave, por exemplo, nomes, tipos e localizações, a partir da origem de dados e copiar esses metadados para o catálogo. A origem de dados e os recursos de dados permanecem onde estão, mas os metadados são utilizados pelo catálogo para os tornar mais facilmente detetáveis e compreensíveis. |
| [Detetar recursos de dados](#discover-data-assets) |Neste procedimento, vai utilizar o portal do Catálogo de Dados do Azure para detetar os recursos de dados que foram registados no passo anterior. Depois de uma origem de dados ter sido registada no Catálogo de Dados do Azure, o serviço indexa os metadados da mesma, para que os utilizadores possam procurar facilmente os dados de que precisam. |
| [Anotar recursos de dados](#annotate-data-assets) |Neste procedimento, vai fornecer anotações (informações como descrições, etiquetas, documentação ou especialistas) para os recursos de dados. Estas informações complementam os metadados extraídos da origem de dados, tornando a origem de dados mais compreensível para mais pessoas. |
| [Ligar a recursos de dados](#connect-to-data-assets) |Neste procedimento, vai abrir os recursos de dados em ferramentas de cliente integradas (como o Excel e os SQL Server Data Tools) e numa ferramenta não integrada (SQL Server Management Studio). |
| [Gerir recursos de dados](#manage-data-assets) |Neste procedimento, vai configurar a segurança dos recursos de dados. O Catálogo de Dados não dá acesso aos dados propriamente ditos aos utilizadores. O proprietário da origem de dados controla o acesso aos dados. <br/><br/> Com o Catálogo de Dados, pode detetar origens de dados e ver os **metadados** relacionados com as origens registadas no catálogo. Contudo, poderá haver situações em que as origens de dados devem estar visíveis apenas a determinados utilizadores ou a membros de grupos específicos. Nestes cenários, pode utilizar o Catálogo de Dados para assumir a propriedade dos recursos de dados registados no catálogo e controlar a visibilidade dos dados dos quais é proprietário. |
| [Remover recursos de dados](#remove-data-assets) |Neste procedimento, vai aprender a remover recursos de dados do catálogo de dados. |

## <a name="tutorial-prerequisites"></a>Pré-requisitos do tutorial
### <a name="azure-subscription"></a>Subscrição do Azure
Para configurar o Catálogo de Dados do Azure, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

As subscrições do Azure ajudam-no a organizar o acesso a recursos de serviços em nuvem, como o Catálogo de Dados do Azure. Também ajudam a controlar de que forma é que a utilização dos recursos é comunicada, faturada e paga. Cada subscrição pode ter uma configuração de faturação e pagamento diferente, para que possa ter subscrições e planos diferentes por departamento, projeto, escritório regional e assim sucessivamente. Cada serviço em nuvem pertence a uma subscrição e tem de ter uma subscrição antes de configurar o Catálogo de Dados do Azure. Para saber mais, veja [Gerir contas, subscrições e funções administrativas](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Se não tiver uma subscrição, pode criar uma conta gratuita em apenas alguns minutos. Veja [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.

### <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o Catálogo de Dados do Azure, tem de ter sessão iniciada com uma conta de utilizador do Azure Active Directory (Azure AD). Tem de ser o proprietário ou coproprietário de uma subscrição do Azure.  

O Azure AD proporciona uma forma fácil de a sua empresa gerir a identidade e o acesso, tanto na nuvem, como no local. Pode utilizar uma única conta escolar ou profissional para iniciar sessão em qualquer aplicação Web na nuvem ou no local. O Catálogo de Dados do Azure utiliza o Azure AD para autenticar o início de sessão. Para saber mais, veja [What is Azure Active Directory? (O que é o Azure Active Directory?)](../active-directory/fundamentals/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Configuração de políticas do Azure Active Directory
Pode deparar-se com casos em que consegue iniciar sessão no portal do Catálogo de Dados do Azure, mas quando tenta iniciá-la na ferramenta de registo da origem de dados, recebe uma mensagem de erro que o impede de o fazer. Este erro pode ocorrer quando está na rede da empresa ou está a ligar de fora da rede da empresa.

A ferramenta de registo utiliza a *autenticação de formulários* para validar os inícios de sessão dos utilizadores relativamente ao Azure Active Directory. Para um início de sessão bem-sucedido, o administrador do Azure Active Directory tem de ativar a autenticação de formulários na *política de autenticação global*.

Com a política de autenticação global, pode ativar a autenticação em separado para ligações de intranet e extranet, conforme mostrado na imagem seguinte. Se a autenticação de formulários não for ativada na rede a partir da qual se está a ligar, podem ocorrer erros de início de sessão.

 ![Política de autenticação global do Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obter mais informações, veja [Configuring authentication policies (Configurar políticas de autenticação)](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Aprovisionar o catálogo de dados
Pode aprovisionar apenas um catálogo de dados por organização (domínio do Azure Active Directory). Por conseguinte, se o proprietário ou coproprietário de uma subscrição do Azure que pertença a este domínio do Azure Active Directory já tiver criado um catálogo, não poderá criar um catálogo novamente, mesmo se tiver várias subscrições do Azure. Para testar se um catálogo de dados foi criado por um utilizador no seu domínio do Azure Active Directory, aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e verifique se vê o catálogo. Se já tiver sido criado um catálogo para si, ignore o procedimento seguinte e avance para a próxima secção.    

1. Aceda à [página do serviço Catálogo de Dados](https://azure.microsoft.com/services/data-catalog) e clique em **Começar**.
   
    ![Catálogo de Dados do Azure – página de destino de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Inicie sessão com uma conta de utilizador que seja proprietária ou coproprietária de uma subscrição do Azure. Após iniciar sessão, é apresentada a seguinte página.
   
    ![Catálogo de Dados do Azure – aprovisionar catálogo de dados](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Especifique um **nome** para o catálogo de dados, a **subscrição** que pretende utilizar e a **localização** para o catálogo.
4. Expanda **Preços** e selecione uma **edição** (Gratuita ou Standard) do Catálogo de Dados do Azure.
    ![Catálogo de Dados do Azure – selecionar edição](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Expanda **Utilizadores do Catálogo** e clique em **Adicionar** para adicionar utilizadores ao catálogo de dados. Você é adicionado automaticamente a este grupo.
    ![Catálogo de Dados do Azure – utilizadores](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Expanda **Administradores do Catálogo** e clique em **Adicionar** para adicionar outros administradores ao catálogo de dados. Você é adicionado automaticamente a este grupo.
    ![Catálogo de Dados do Azure – administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Clique em **Criar Catálogo** para criar o catálogo de dados da sua organização. Após criar o catálogo de dados, é apresentada a respetiva home page.
    ![Catálogo de Dados do Azure – criado](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Encontrar um catálogo de dados no portal do Azure
1. Noutro separador do browser ou numa janela separada do browser, aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a mesma conta que utilizou para criar o catálogo de dados no passo anterior.
2. Selecione **Procurar** e, em seguida, clique em **Catálogo de Dados**.
   
    ![Catálogo de Dados do Azure – procurar no Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) É apresentado o catálogo de dados criado.
   
    ![Catálogo de Dados do Azure – ver o catálogo na lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Clique no catálogo que criou. É apresentado o painel **Catálogo de Dados** no portal.
   
   ![Catálogo de Dados do Azure – painel no portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. Pode ver as propriedades do catálogo de dados, bem como atualizá-las. Por exemplo, clique em **Escalão de preço** e altere a edição.
   
    ![Catálogo de Dados do Azure – escalão de preço](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Base de dados de exemplo da Adventure Works
Neste tutorial, vai registar recursos de dados (tabelas) da base de dados de exemplo AdventureWorks2014 para o Motor de Base de Dados do SQL Server, mas pode utilizar qualquer origem de dados suportada, se preferir trabalhar com dados que lhe sejam familiares e relevantes para a sua função. Para obter uma lista de origens de dados suportadas, veja [Origens de dados suportadas](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Instalar a base de dados OLTP de 2014 do Adventure Works
A base de dados Adventure Works suporta cenários de processamento de transações online padrão para um fabricante de bicicletas fictício (Adventure Works Cycles), que incluem produtos, vendas e compras. Neste tutorial, vai registar informações sobre os produtos no Catálogo de Dados do Azure.

Para instalar a base de dados de exemplo Adventure Works:

1. Transfira [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661), no CodePlex.
2. Para restaurar a base de dados no seu computador, siga as instruções em [Restore a Database Backup by using SQL Server Management Studio (Restaurar uma Cópia de Segurança de Base de Dados com o SQL Server Management Studio)](https://msdn.microsoft.com/library/ms177429.aspx) ou estes passos:
   1. Abra o SQL Server Management Studio e ligue ao Motor de Base de Dados do SQL Server.
   2. Clique com o botão direito do rato em **Bases de Dados** e clique em **Restaurar Base de Dados**.
   3. Em **Restaurar Base de Dados**, clique na opção **Dispositivo** para **Origem** e clique em **Procurar**.
   4. Em **Selecionar dispositivos de cópia de segurança**, clique em **Adicionar**.
   5. Aceda à pasta onde tem o ficheiro **AdventureWorks2014.bak**, selecione-o e clique em **OK** para fechar a caixa de diálogo **Localizar Ficheiro de Cópia de Segurança**.
   6. Clique em **OK** para fechar a caixa de diálogo **Selecionar dispositivos de cópia de segurança**.    
   7. Clique em **OK** para fechar a caixa de diálogo **Restaurar Base de Dados**.

Agora, já pode utilizar o Catálogo de Dados do Azure para registar recursos de dados da base de dados de exemplo da Adventure Works.

## <a name="register-data-assets"></a>Registar recursos de dados
Neste exercício, vai utilizar a ferramenta de registo para registar recursos de dados da base de dados da Adventure Works no catálogo. O registo é o processo de extrair metadados estruturais chave, por exemplo, nomes, tipos e localizações, a partir da origem de dados e dos recursos que contém e copiar esses metadados para o catálogo. A origem de dados e os recursos de dados permanecem onde estão, mas os metadados são utilizados pelo catálogo para os tornar mais facilmente detetáveis e compreensíveis.

### <a name="register-a-data-source"></a>Registar origens de dados
1. Aceda à [home page do Catálogo de Dados do Azure](http://azuredatacatalog.com) e clique em **Publicar Dados**.
   
   ![Catálogo de Dados do Azure – Botão “Publicar Dados”](media/data-catalog-get-started/data-catalog-publish-data.png)
2. Clique em **Iniciar Aplicação** para transferir, instalar e executar a ferramenta de registo no seu computador.
   
   ![Catálogo de Dados do Azure – Botão “Iniciar”](media/data-catalog-get-started/data-catalog-launch-application.png)
3. Na página **Boas-vindas**, clique em **Iniciar sessão** e introduza as suas credenciais.     
   
    ![Catálogo de Dados do Azure – página Boas-vindas](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. Na página **Catálogo de Dados do Microsoft Azure**, clique em **SQL Server** e em **Seguinte**.
   
    ![Catálogo de Dados do Azure – origens de dados](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Introduza as propriedades de ligação do SQL Server para **AdventureWorks2014** (veja o exemplo abaixo) e clique em **LIGAR**.
   
   ![Catálogo de Dados do Azure – definições de ligação do SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Registe os metadados do recurso de dados. Neste exemplo, vai registar objetos de **Produção/Produto** do espaço de nomes AdventureWorks Production:
   
   1. Na árvore da **Hierarquia do Servidor**, expanda **AdventureWorks2014** e clique em **Production**.
   2. Selecione **Product**, **ProductCategory**, **ProductDescription** e **ProductPhoto** com Ctrl+clique.
   3. Clique na **seta “mover seleção”** (**>**). Esta ação move todos os objetos selecionados para a lista **Objetos a registar**.
      
      ![Tutorial do Catálogo de Dados do Azure – procurar e selecionar objetos](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Selecione **Incluir uma Pré-visualização** para incluir uma pré-visualização de instantâneo dos dados. O instantâneo inclui até 20 registos de cada tabela e é copiado para o catálogo.
   5. Selecione **Incluir Perfil de Dados** para incluir um instantâneo das estatísticas dos objetos relativas ao perfil de dados (por exemplo, valores mínimos, máximos e médios de uma coluna, número de linhas, etc.).
   6. No campo **Adicionar etiquetas**, introduza **adventure works, cycles**. Esta ação irá adicionar etiquetas de pesquisa a estes recursos de dados. As etiquetas são uma excelente forma de ajudar os utilizadores a localizar uma origem de dados registada.
   7. Especifique o nome de um **especialista** sobre estes dados (opcional).
      
      ![Tutorial do Catálogo de Dados do Azure – objetos a registar](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Clique em **REGISTER**. O Catálogo de Dados do Azure regista os objetos selecionados. Neste exercício, os objetos selecionados de Adventure Works estão registados. A ferramenta de registo extrai metadados do recurso de dados e copia-os para o serviço Catálogo de Dados do Azure. Os dados permanecem no local onde residem atualmente e continuam sob o controlo dos administradores e das políticas do sistema atual.
      
      ![Catálogo de Dados do Azure – objetos registados](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. Para ver os objetos da origem de dados registados, clique em **Ver Portal**. No portal do Catálogo de Dados do Azure, confirme que consegue ver as quatro tabelas e a base de dados na vista de grelha.
      
      ![Objetos no portal do Catálogo de Dados do Azure](media/data-catalog-get-started/data-catalog-view-portal.png)

Neste exercício, registou objetos da base de dados de exemplo Adventure Works, para que possam ser facilmente detetados pelos utilizadores da sua organização. No exercício seguinte, vai aprender a detetar recursos de dados registados.

## <a name="discover-data-assets"></a>Detetar recursos de dados
A deteção no Catálogo de Dados do Azure utiliza dois mecanismos principais – pesquisa e filtragem.

A pesquisa foi concebida para ser intuitiva e poderosa. Por predefinição, é feita a correspondência dos termos de pesquisa em relação a qualquer propriedade no catálogo, incluindo anotações fornecidas pelo utilizador.

A filtragem foi concebida para complementar a pesquisa. Pode selecionar características específicas, como especialistas, tipo de origem de dados, tipo de objeto e etiquetas, para ver os recursos de dados correspondentes e para restringir os resultados da pesquisa a recursos correspondentes.

Ao utilizar uma combinação de pesquisa e filtragem, pode navegar rapidamente nas origens de dados que foram registadas no Catálogo de Dados do Azure para detetar os recursos de dados de que precisa.

Neste exercício, vai utilizar o portal do Catálogo de Dados do Azure para detetar os recursos de dados que registou no exercício anterior. Para obter detalhes sobre a sintaxe da pesquisa, veja [Data Catalog Search syntax reference (Referência para a sintaxe da Pesquisa do Catálogo de Dados)](https://msdn.microsoft.com/library/azure/mt267594.aspx).

Seguem-se alguns exemplos para detetar recursos de dados no catálogo.  

### <a name="discover-data-assets-with-basic-search"></a>Detetar recursos de dados com a pesquisa básica
A pesquisa básica ajuda-o a pesquisar catálogos com um ou mais termos de pesquisa. Os resultados são qualquer recurso que corresponda a uma propriedade com um ou mais dos termos especificados.

1. No portal do Catálogo de Dados do Azure, clique em **Home page**. Caso tenha fechado o browser, aceda à [home page do Catálogo de Dados do Azure](https://www.azuredatacatalog.com).
2. Na caixa de pesquisa, introduza `cycles` e prima **ENTER**.
   
    ![Catálogo de Dados do Azure – pesquisa de texto básica](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Confirme que vê as quatro tabelas e a base de dados (AdventureWorkds2014) nos resultados. Pode alternar entre **vista de grelha** e **vista de lista** ao clicar nos botões da barra de ferramentas, conforme mostrado na imagem seguinte. Tenha em atenção que a palavra-chave da pesquisa é realçada nos resultados da pesquisa porque a opção **Realçar** está **LIGADA**. Também pode especificar o número de **resultados por página** nos resultados de pesquisa.
   
    ![Catálogo de Dados do Azure – resultados da pesquisa de texto básica](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    O painel **Pesquisas** está no lado esquerdo e o painel **Propriedades** no lado direito. No painel **Pesquisas**, pode alterar os critérios de pesquisa e filtrar os resultados. O painel **Propriedades** apresenta as propriedades de um objeto selecionado na grelha ou na lista.
4. Clique em **Produc**, nos resultados da pesquisa. Clique nos separadores **Pré-visualização**, **Colunas**, **Perfil de Dados** e **Documentação** ou clique na seta para expandir o painel inferior.  
   
    ![Catálogo de Dados do Azure – painel inferior](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    No separador **Pré-visualização**, é apresentada uma pré-visualização dos dados na tabela **Produto**.  
5. Clique no separador **Colunas** para obter detalhes sobre as colunas (como **nome** e **tipo de dados**) no recurso de dados.
6. Clique no separador **Perfil de Dados** para ver a criação de perfis de dados (por exemplo, o número de linhas, o tamanho dos dados ou o valor mínimo numa coluna) no recurso de dados.
7. Utilize os **Filtros**, à esquerda, para filtrar os resultados. Por exemplo, clique em **Tabela** para **Tipo de Objeto** e verá apenas as quatro tabelas, não a base de dados.
   
    ![Catálogo de Dados do Azure – filtrar os resultados da pesquisa](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Detetar recursos de dados com o âmbito de propriedade
O âmbito de propriedade ajuda-o a detetar recursos de dados quando o termo de pesquisa tem correspondência com a propriedade especificada.

1. Limpe o filtro **Tabela** em **Tipo de Objeto**, nos **Filtros**.  
2. Na caixa de pesquisa, introduza `tags:cycles` e prima **ENTER**. Veja [Data Catalog Search syntax reference (Referência para a sintaxe da Pesquisa do Catálogo de Dados)](https://msdn.microsoft.com/library/azure/mt267594.aspx) para ver todas as propriedades que pode utilizar para pesquisar o catálogo de dados.
3. Confirme que vê as quatro tabelas e a base de dados (AdventureWorkds2014) nos resultados.  
   
    ![Catálogo de Dados – resultados de pesquisa de âmbito de propriedade](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Guardar a pesquisa
1. No painel **Pesquisas**, na secção **Pesquisa Atual**, introduza um nome para a pesquisa e clique em **Guardar**.
   
    ![Catálogo de Dados do Azure – guardar pesquisa](media/data-catalog-get-started/data-catalog-save-search.png)
2. Confirme que a pesquisa guardada aparece em **Pesquisas Guardadas**.
   
    ![Catálogo de Dados do Azure – pesquisas guardadas](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Selecione uma das ações que pode realizar na pesquisa guardada (**Mudar o nome**, **Eliminar**, **Guardar Como Pesquisa Predefinida**).
   
    ![Catálogo de Dados do Azure – opções de pesquisas guardadas](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Operadores Booleanos
Pode alargar ou restringir as pesquisas com operadores Booleanos.

1. Na caixa de pesquisa, introduza `tags:cycles AND objectType:table` e prima **ENTER**.
2. Confirme que só vê as tabelas (e não a base de dados) nos resultados.  
   
    ![Catálogo de Dados do Azure – operador Booleano na pesquisa](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Agrupar com parênteses
Ao agrupar com parênteses, pode agrupar partes da consulta para alcançar isolamento lógico, especialmente em conjunto com operadores Booleanos.

1. Na caixa de pesquisa, introduza `name:product AND (tags:cycles AND objectType:table)` e prima **ENTER**.
2. Confirme que só vê a tabela **Product** nos resultados da pesquisa.
   
    ![Catálogo de Dados do Azure – agrupar pesquisa](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Operadores de Comparação
Com os operadores de comparação, pode utilizar comparações além das de igualdade para propriedades que têm tipos de dados numéricos e de data.

1. Na caixa de pesquisa, introduza `lastRegisteredTime:>"06/09/2016"`.
2. Limpe o filtro **Tabela** em **Tipo de Objeto**.
3. Prima **ENTER**.
4. Confirme que vê as tabelas **Product**, **ProductCategory**, **ProductDescription** e **ProductPhoto** e a base de dados AdventureWorks2014 que registou nos resultados da pesquisa.
   
    ![Catálogo de Dados do Azure – resultados da pesquisa de comparação](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Veja [How to discover data assets (Como detetar recursos de dados)](data-catalog-how-to-discover.md), para obter informações detalhes sobre a deteção de recursos de dados, e [Data Catalog Search syntax reference (Referência para a sintaxe da Pesquisa do Catálogo de Dados)](https://msdn.microsoft.com/library/azure/mt267594.aspx), para informações sobre a sintaxe das pesquisas.

## <a name="annotate-data-assets"></a>Anotar recursos de dados
Neste exercício, vai utilizar o portal do Catálogo de Dados do Azure para anotar (adicionar informações, como descrições, etiquetas ou especialistas) recursos de dados que registou anteriormente no catálogo. As anotações complementam e melhoram os metadados estruturais extraídos da origem de dados durante o registo, e farão com que os recursos de dados sejam muito fáceis de detetar e compreender.

Neste exercício, vai anotar um recurso de dados individual (ProductPhoto). Vai adicionar um nome amigável e uma descrição ao recurso de dados ProductPhoto.  

1. Aceda à [home page do Catálogo de Dados do Azure](https://www.azuredatacatalog.com) e pesquise com `tags:cycles` para encontrar os recursos de dados que registou.  
2. Clique em **ProductPhoto** nos resultados da pesquisa.  
3. Introduza **Imagens do produto** em **Nome Amigável** e **Fotografias do produto para os materiais de marketing** na **Descrição**.
   
    ![Catálogo de Dados do Azure – descrição de ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    A **Descrição** ajuda os outros utilizadores a detetar e compreender porquê e como utilizar os recursos de dados selecionados. Também pode adicionar mais etiquetas e ver as colunas. Agora, pode tentar pesquisar e filtrar para detetar recursos de dados com os metadados descritivos que adicionou ao catálogo.

Também pode fazer o seguinte nesta página:

* Adicionar especialistas ao recurso de dados. Clique em **Adicionar**, na área **Especialistas**.
* Adicionar etiquetas ao nível do conjunto de dados. Clique em **Adicionar**, na área **Etiquetas**. Uma etiqueta pode ser uma etiqueta de utilizador ou uma etiqueta do glossário. A Edição Standard do Catálogo de Dados inclui um glossário comercial que ajuda os administradores do catálogo a definir uma taxonomia comercial central. Os utilizadores do catálogo podem assim anotar recursos de dados com os termos do glossário. Para obter mais informações, veja [How to set up the Business Glossary for Governed Tagging (Como configurar o Glossário Comercial para Etiquetagem Regida)](data-catalog-how-to-business-glossary.md).
* Adicionar etiquetas ao nível da coluna. Clique em **Adicionar**, em **Etiquetas**, na coluna que quer anotar.
* Adicionar descrição ao nível da coluna. Introduza **Descrição** numa coluna. Também pode ver os metadados da descrição extraídos da origem de dados.
* Adicionar informações de **Pedir acesso** que mostra aos utilizadores como pedir acesso ao recurso de dados.
  
    ![Catálogo de Dados do Azure – adicionar etiquetas, descrições](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Escolha o separador **Documentação** e disponibilize documentação para o recurso de dados. Com a documentação do Catálogo de Dados do Azure, pode utilizar o seu catálogo de dados como um repositório de conteúdos para criar uma narrativa completa dos seus recursos de dados.
  
    ![Catálogo de Dados do Azure – separador Documentação](media/data-catalog-get-started/data-catalog-documentation.png)

Também pode adicionar uma anotação a vários recursos de dados. Por exemplo, pode selecionar todos os recursos de dados que registou e especificar um especialista para os mesmos.

![Catálogo de Dados do Azure – anotar vários recursos de dados](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

O Catálogo de Dados do Azure suporta uma abordagem baseada em “crowd-sourcing” nas anotações. Qualquer utilizador do Catálogo de Dados pode adicionar etiquetas (utilizador ou glossário), descrições e outros metadados, de modo a que qualquer utilizador com uma perspetiva de um recurso de dados e da sua utilização possa capturar e disponibilizar essa perspetiva a outros utilizadores.

Veja [How to annotate data assets (Como anotar recursos de dados)](data-catalog-how-to-annotate.md) para obter informações detalhadas sobre como anotar recursos de dados.

## <a name="connect-to-data-assets"></a>Ligar a recursos de dados
Neste exercício, vai abrir os recursos de dados numa ferramenta de cliente integrada (Excel) e numa ferramenta não integrada (SQL Server Management Studio) mediante a utilização de informações de ligação.

> [!NOTE]
> É importante lembrar-se de que o Catálogo de Dados do Azure não lhe dá acesso à origem de dados real, apenas lhe permite detetá-la e compreendê-la mais facilmente. Quando se liga a uma origem de dados, a aplicação cliente que escolher utiliza as suas credenciais do Windows ou pede-lhe as credenciais, conforme necessário. Se não lhe tiver sido concedido anteriormente acesso à origem de dados, este tem de lhe ser concedido para que se possa ligar.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Ligar a um recurso de dados a partir do Excel
1. Selecione **Product** a partir dos resultados de pesquisa. Clique em **Abrir Em**, na barra de ferramentas, e clique em **Excel**.
   
    ![Catálogo de Dados do Azure – ligar ao recurso de dados](media/data-catalog-get-started/data-catalog-connect1.png)
2. Clique em **Abrir**, na janela de pop-up de transferência. Esta experiência pode variar consoante o browser.
   
    ![Catálogo de Dados do Azure – ficheiro de ligação do Excel transferido](media/data-catalog-get-started/data-catalog-download-open.png)
3. Na janela **Aviso de Segurança do Microsoft Excel**, clique em **Ativar**.
   
    ![Catálogo de Dados do Azure – pop-up de segurança do Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Mantenha as predefinições na caixa de diálogo **Importar Dados** e clique em **OK**.
   
    ![Catálogo de Dados do Azure – importar dados do Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Veja a origem de dados no Excel.
   
    ![Catálogo de Dados do Azure – tabela “product” no Excel](media/data-catalog-get-started/data-catalog-connect2.png)

Neste exercício, ligou a recursos de dados que foram detetados com o Catálogo de Dados do Azure. Com o portal do Catálogo de Dados do Azure, pode ligar diretamente através das aplicações cliente integradas no menu **Abrir em**. Também pode utilizar as informações de localização incluídas nos metadados do recurso para ligar com qualquer aplicação que quiser. Por exemplo, pode utilizar o SQL Server Management Studio para ligar à base de dados AdventureWorks2014 para aceder aos dados nos recursos de dados registados neste tutorial.

1. Abra o **SQL Server Management Studio**.
2. Na caixa de diálogo **Ligar ao Servidor**, introduza o nome do servidor do painel **Propriedades** no portal do Catálogo de Dados do Azure.
3. Utilize a autenticação e as credenciais adequadas para aceder ao recurso de dados. Se não tiver acesso, utilize as informações no campo **Pedir Acesso** para o obter.
   
    ![Catálogo de Dados do Azure – pedir acesso](media/data-catalog-get-started/data-catalog-request-access.png)

Clique em **Ver Cadeias de Ligação** para ver e copiar as cadeias de ligação ADF.NET, ODBC e OLEDB para a área de transferência para utilizar na sua aplicação.

## <a name="manage-data-assets"></a>Gerir recursos de dados
Neste passo, vai saber como configurar a segurança dos seus recursos de dados. O Catálogo de Dados não dá acesso aos dados propriamente ditos aos utilizadores. O proprietário da origem de dados controla o acesso aos dados.

Pode utilizar o Catálogo de Dados para detetar origens de dados e ver os metadados relacionados com as origens registadas no catálogo. Contudo, poderá haver situações em que as origens de dados devem estar visíveis apenas a determinados utilizadores ou a membros de grupos específicos. Nestes cenários, pode utilizar o Catálogo de Dados para assumir a propriedade dos recursos de dados registados no catálogo e para, depois, controlar a visibilidade dos dados dos quais é proprietário.

> [!NOTE]
> As capacidades de gestão descritas neste exercício só estão disponíveis na Edição Standard do Catálogo de Dados do Azure e não na Edição Gratuita.
> No Catálogo de Dados do Azure, pode assumir a propriedade, adicionar coproprietários e definir a visibilidade dos recursos de dados.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Assumir a propriedade de recursos de dados e restringir a visibilidade
1. Aceda à [home page do Catálogo de Dados do Azure](https://www.azuredatacatalog.com). Na caixa de **Pesquisa**, introduza `tags:cycles` e prima **ENTER**.
2. Clique num item na lista de resultados e clique em **Obter Propriedade**, na barra de ferramentas.
3. Na secção **Gestão** do painel **Propriedades**, clique em **Obter Propriedade**.
   
    ![Catálogo de Dados do Azure – obter propriedade](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Para restringir a visibilidade, escolha **Proprietários e Estes Utilizadores**, na secção **Visibilidade**, e clique em **Adicionar**. Introduza os endereços de e-mail dos utilizadores na caixa de texto e prima **ENTER**.
   
    ![Catálogo de Dados do Azure – restringir acesso](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Remover recursos de dados
Neste exercício, vai utilizar o portal do Catálogo de Dados do Azure para remover dados de pré-visualização dos recursos de dados registados e eliminar recursos de dados do catálogo.

No Catálogo de Dados do Azure, pode eliminar um recurso individual ou vários recursos.

1. Aceda à [home page do Catálogo de Dados do Azure](https://www.azuredatacatalog.com).
2. Na caixa de **Pesquisa**, introduza `tags:cycles` e clique em **ENTER**.
3. Selecione um item na lista de resultados e clique em **Eliminar**, na barra de ferramentas, conforme mostrado na imagem seguinte:
   
    ![Catálogo de Dados do Azure – eliminar item de grelha](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    Se estiver a utilizar a vista de lista, a caixa de verificação estará à esquerda do item, conforme mostrado na imagem seguinte:
   
    ![Catálogo de Dados do Azure – eliminar item de lista](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    Também pode selecionar vários recursos de dados e eliminá-los, conforme mostrado na imagem seguinte:
   
    ![Catálogo de Dados do Azure – eliminar vários recursos de dados](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> O comportamento predefinido do catálogo é permitir que qualquer utilizador registe uma origem de dados e permitir que qualquer utilizador possa eliminar qualquer recurso de dados que foi registado. As capacidades de gestão incluídas na Edição Standard do Catálogo de Dados do Azure oferecem opções adicionais para obter a propriedade de recursos, restringir quem os detetar e quem os pode eliminar.
> 
> 

## <a name="summary"></a>Resumo
Neste tutorial, explorou as capacidades essenciais do Catálogo de Dados do Azure, incluindo registar, anotar, detetar e gerir recursos de dados empresariais. Agora que concluiu o tutorial, está na altura de começar. Pode começar hoje, registando as origens de dados de que você e a sua equipa dependem e convidando colegas para utilizarem o catálogo.

## <a name="references"></a>Referências
* [How to register data assets (Como registar recursos de dados)](data-catalog-how-to-register.md)
* [How to discover data assets (Como detetar recursos de dados)](data-catalog-how-to-discover.md)
* [How to annotate data assets (Como anotar recursos de dados)](data-catalog-how-to-annotate.md)
* [How to document data assets (Como documentar recursos de dados)](data-catalog-how-to-documentation.md)
* [How to connect data assets (Como ligar a recursos de dados)](data-catalog-how-to-connect.md)
* [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)

