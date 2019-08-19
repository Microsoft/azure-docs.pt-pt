---
title: Conceitos de desenvolvedor do catálogo de dados do Azure
description: Introdução aos principais conceitos no modelo conceitual do catálogo de dados do Azure, conforme exposto por meio da API REST do catálogo.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976835"
---
# <a name="azure-data-catalog-developer-concepts"></a>Conceitos de desenvolvedor do catálogo de dados do Azure
O **Catálogo de dados do Microsoft Azure** é um serviço de nuvem totalmente gerenciado que fornece recursos para a descoberta de fonte de dados e para os metadados de fonte de dados crowdsourcing. Os desenvolvedores podem usar o serviço por meio de suas APIs REST. Entender os conceitos implementados no serviço é importante para os desenvolvedores se integrarem com êxito ao **Catálogo de dados do Azure**.

## <a name="key-concepts"></a>Conceitos-chave
O modelo conceitual do **Catálogo de dados do Azure** baseia-se em quatro conceitos principais: O **Catálogo**, **os usuários, os** **ativos**e as **anotações**.

![Ilustração do modelo conceitual do catálogo de dados do Azure](./media/data-catalog-developer-concepts/concept2.png)

*Figura 1-modelo conceitual simplificado do catálogo de dados do Azure*

### <a name="catalog"></a>catálogo
Um **Catálogo** é o contêiner de nível superior para todos os metadados que uma organização armazena. Há um **Catálogo** permitido por conta do Azure. Os catálogos estão vinculados a uma assinatura do Azure, mas apenas um **Catálogo** pode ser criado para qualquer conta específica do Azure, mesmo que uma conta possa ter várias assinaturas.

Um catálogo contém **usuários** e **ativos**.

### <a name="users"></a>Utilizadores
Os usuários são entidades de segurança que têm permissões para executar ações (Pesquisar no catálogo, adicionar, editar ou remover itens, etc.) no catálogo.

Há várias funções diferentes que um usuário pode ter. Para obter informações sobre funções, consulte a seção funções e autorização.

Usuários individuais e grupos de segurança podem ser adicionados.

O catálogo de dados do Azure usa Azure Active Directory para gerenciamento de identidade e acesso. Cada usuário do catálogo deve ser membro do Active Directory para a conta.

### <a name="assets"></a>Elementos
Um **Catálogo** contém ativos de dados. **Ativos** são a unidade de granularidade gerenciada pelo catálogo.

A granularidade de um ativo varia de acordo com a fonte de dados. Para SQL Server ou Oracle Database, um ativo pode ser uma tabela ou uma exibição. Por SQL Server Analysis Services, um ativo pode ser uma medida, uma dimensão ou um KPI (indicador chave de desempenho). Por SQL Server Reporting Services, um ativo é um relatório.

Um **ativo** é a coisa que você adiciona ou remove de um catálogo. É a unidade de resultado que você retorna da **pesquisa**.

Um **ativo** é composto de seu nome, local e tipo e anotações que o descrevem mais detalhadamente.

### <a name="annotations"></a>Anotações
Anotações são itens que representam metadados sobre ativos.

Exemplos de anotações são descrição, marcas, esquema, documentação, etc. Uma lista completa dos tipos de ativo e dos tipos de anotação estão na seção de modelo de objeto de ativo.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing anotações e perspectiva do usuário (multiplicidade de opinião)
Um aspecto fundamental do catálogo de dados do Azure é como ele dá suporte ao crowdsourcing de metadados no sistema. Em oposição a uma abordagem do wiki – em que há apenas uma opinião e o último escritor vence – o modelo do catálogo de dados do Azure permite que várias opiniões sejam ativadas lado a lado no sistema.

Essa abordagem reflete o mundo real dos dados corporativos em que diferentes usuários podem ter perspectivas diferentes em um determinado ativo:

* Um administrador de banco de dados pode fornecer informações sobre contratos de nível de serviço ou a janela de processamento disponível para operações de ETL em massa
* Um administrador de dados pode fornecer informações sobre os processos de negócios aos quais o ativo se aplica ou as classificações que a empresa aplicou a ele
* Um analista financeiro pode fornecer informações sobre como os dados são usados durante as tarefas de relatório do fim do período

Para dar suporte a este exemplo, cada usuário – o DBA, o administrador de dados e o analista – pode adicionar uma descrição a uma única tabela que foi registrada no catálogo. Todas as descrições são mantidas no sistema e, no portal do catálogo de dados do Azure, todas as descrições são exibidas.

Esse padrão é aplicado à maioria dos itens no modelo de objeto, de modo que os tipos de objeto na carga JSON são geralmente matrizes para propriedades, em que você pode esperar um singleton.

Por exemplo, sob a raiz do ativo há uma matriz de objetos de descrição. A propriedade de matriz é denominada "descrições". Um objeto de descrição tem uma descrição de propriedade. O padrão é que cada usuário que digita Description obtém um objeto Description criado para o valor fornecido pelo usuário.

A UX pode escolher como exibir a combinação. Há três padrões diferentes para exibição.

* O padrão mais simples é "Mostrar tudo". Nesse padrão, todos os objetos são mostrados em uma exibição de lista. O UX do portal do catálogo de dados do Azure usa esse padrão para descrição.
* Outro padrão é "Merge". Nesse padrão, todos os valores dos diferentes usuários são mesclados juntos, com remoção duplicada. Exemplos desse padrão na UX do portal do catálogo de dados do Azure são as propriedades de marcas e especialistas.
* Um terceiro padrão é "último escritor vence". Nesse padrão, somente o valor mais recente digitado em é mostrado. FriendlyName é um exemplo desse padrão.

## <a name="asset-object-model"></a>Modelo de objeto de ativo
Conforme apresentado na seção conceitos principais, o modelo de objeto do **Catálogo de dados do Azure** inclui itens que podem ser ativos ou anotações. Os itens têm propriedades, que podem ser opcionais ou obrigatórias. Algumas propriedades se aplicam a todos os itens. Algumas propriedades se aplicam a todos os ativos. Algumas propriedades se aplicam somente a tipos de ativos específicos.

### <a name="system-properties"></a>Propriedades do sistema
<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>A última vez em que o item foi modificado. Esse campo é gerado pelo servidor quando um item é inserido e toda vez que um item é atualizado. O valor dessa propriedade é ignorado na entrada de operações de publicação.</td></tr><tr><td>id</td><td>URI</td><td>URL absoluta do item (somente leitura). É o URI endereçável exclusivo para o item.  O valor dessa propriedade é ignorado na entrada de operações de publicação.</td></tr><tr><td>type</td><td>Cadeia</td><td>O tipo do ativo (somente leitura).</td></tr><tr><td>etag</td><td>Cadeia</td><td>Uma cadeia de caracteres correspondente à versão do item que pode ser usada para controle de simultaneidade otimista ao executar operações que atualizam itens no catálogo. "*" pode ser usado para corresponder a qualquer valor.</td></tr></table>

### <a name="common-properties"></a>Propriedades comuns
Essas propriedades se aplicam a todos os tipos de ativos raiz e todos os tipos de anotação.

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleano</td><td>Indica se os dados do item são derivados de um sistema de origem (como banco de dados do SQL Server, Oracle Database) ou criados por um usuário.</td></tr>
</table>

### <a name="common-root-properties"></a>Propriedades de raiz comuns
<p>
Essas propriedades se aplicam a todos os tipos de ativos raiz.

<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>name</td><td>Cadeia</td><td>Um nome derivado das informações de local da fonte de dados</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Descreve exclusivamente a fonte de dados e é um dos identificadores do ativo. (Consulte a seção de identidade dupla).  A estrutura da DSL varia de acordo com o protocolo e o tipo de origem.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mais detalhes sobre o tipo de ativo.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descreve o usuário que registrou mais recentemente este ativo.  Contém a ID exclusiva para o usuário (o UPN) e um nome de exibição (lastName e firstName).</td></tr><tr><td>containerId</td><td>Cadeia</td><td>ID do ativo de contêiner para a fonte de dados. Não há suporte para essa propriedade no tipo de contêiner.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Propriedades comuns de anotação não singleton
Essas propriedades se aplicam a todos os tipos de anotação não singleton (anotações, que podem ser múltiplos por ativo).

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>key</td><td>Cadeia</td><td>Uma chave especificada pelo usuário, que identifica exclusivamente a anotação na coleção atual. O comprimento da chave não pode exceder 256 caracteres.</td></tr>
</table>

### <a name="root-asset-types"></a>Tipos de ativos raiz
Os tipos de ativos raiz são aqueles que representam os vários tipos de ativos de dados que podem ser registrados no catálogo. Para cada tipo de raiz, há uma exibição, que descreve o ativo e as anotações incluídas na exibição. O nome da exibição deve ser usado no segmento de URL {view_name} correspondente ao publicar um ativo usando a API REST.

<table><tr><td><b>Tipo de ativo (nome da exibição)</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Anotações permitidas</b></td><td><b>Comentários</b></td></tr><tr><td>Tabela ("tabelas")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Esquema<p>ColumnDescription<p>ColumnTag<p> Especialista<p>Pré-visualização<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Documentação<p></td><td>Uma tabela representa todos os dados tabulares.  Por exemplo: Tabela SQL, exibição SQL, Analysis Services tabela tabular, Analysis Services dimensão multidimensional, tabela Oracle, etc.   </td></tr><tr><td>Measure ("medidas")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Esse tipo representa uma medida Analysis Services.</td></tr><tr><td></td><td>medida</td><td>Coluna</td><td></td><td>Metadados que descrevem a medida</td></tr><tr><td></td><td>iscalculad </td><td>Booleano</td><td></td><td>Especifica se a medida é calculada ou não.</td></tr><tr><td></td><td>measureGroup</td><td>Cadeia</td><td></td><td>Contêiner físico para medida</td></tr><td>KPI ("KPIs")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação</td><td></td></tr><tr><td></td><td>measureGroup</td><td>Cadeia</td><td></td><td>Contêiner físico para medida</td></tr><tr><td></td><td>goaly</td><td>Cadeia</td><td></td><td>Uma expressão numérica MDX ou um cálculo que retorna o valor de destino do KPI.</td></tr><tr><td></td><td>valueExpression</td><td>Cadeia</td><td></td><td>Uma expressão numérica MDX que retorna o valor real do KPI.</td></tr><tr><td></td><td>status da</td><td>Cadeia</td><td></td><td>Uma expressão MDX que representa o estado do KPI em um ponto específico no tempo.</td></tr><tr><td></td><td>trendy</td><td>Cadeia</td><td></td><td>Uma expressão MDX que avalia o valor do KPI ao longo do tempo. A tendência pode ser qualquer critério baseado em tempo que seja útil em um contexto de negócios específico.</td>
<tr><td>Relatório ("relatórios")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa um relatório de SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Cadeia</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Cadeia</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Cadeia</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Cadeia</td><td></td><td></td></tr><tr><td>Contêiner ("contêineres")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Esse tipo representa um contêiner de outros ativos, como um banco de dados SQL, um contêiner de BLOBs do Azure ou um modelo de Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Tipos de anotação
Os tipos de anotação representam tipos de metadados que podem ser atribuídos a outros tipos no catálogo.

<table>
<tr><td><b>Tipo de anotação (nome da exibição aninhada)</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>Descrição ("descrições")</td><td></td><td></td><td>Esta propriedade contém uma descrição para um ativo. Cada usuário do sistema pode adicionar sua própria descrição.  Somente esse usuário pode editar o objeto Description.  (Administradores e proprietários de ativos podem excluir o objeto de descrição, mas não editá-lo). O sistema mantém as descrições dos usuários separadamente.  Portanto, há uma matriz de descrições em cada ativo (uma para cada usuário que contribuiu com seu conhecimento sobre o ativo, além de possivelmente um que contenha informações derivadas da fonte de dados).</td></tr>
<tr><td></td><td>description</td><td>Cadeia de caracteres</td><td>Uma breve descrição (2-3 linhas) do ativo</td></tr>

<tr><td>Marca ("marcas")</td><td></td><td></td><td>Esta propriedade define uma marca para um ativo. Cada usuário do sistema pode adicionar várias marcas para um ativo.  Somente o usuário que criou os objetos de marca pode editá-los.  (Administradores e proprietários de ativos podem excluir o objeto de marca, mas não editá-lo). O sistema mantém as marcas dos usuários separadamente.  Portanto, há uma matriz de objetos de marca em cada ativo.</td></tr>
<tr><td></td><td>tag</td><td>Cadeia de caracteres</td><td>Uma marca que descreve o ativo.</td></tr>

<tr><td>FriendlyName ("FriendlyName")</td><td></td><td></td><td>Essa propriedade contém um nome amigável para um ativo. FriendlyName é uma anotação singleton-somente um FriendlyName pode ser adicionado a um ativo.  Somente o usuário que criou o objeto FriendlyName pode editá-lo. (Administradores e proprietários de ativos podem excluir o objeto FriendlyName, mas não editá-lo). O sistema mantém os nomes amigáveis dos usuários separadamente.</td></tr>
<tr><td></td><td>friendlyName</td><td>Cadeia de caracteres</td><td>Um nome amigável do ativo.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>O esquema descreve a estrutura dos dados.  Ele lista os nomes de atributo (coluna, atributo, campo, etc.), tipos, bem como outros metadados.  Essas informações são todas derivadas da fonte de dados.  O esquema é uma anotação singleton-somente um esquema pode ser adicionado a um ativo.</td></tr>
<tr><td></td><td>Colunas</td><td>Column[]</td><td>Uma matriz de objetos de coluna. Elas descrevem a coluna com informações derivadas da fonte de dados.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Esta propriedade contém uma descrição para uma coluna.  Cada usuário do sistema pode adicionar suas próprias descrições para várias colunas (no máximo uma por coluna). Somente o usuário que criou objetos ColumnDescription pode editá-los.  (Administradores e proprietários de ativos podem excluir o objeto ColumnDescription, mas não editá-lo). O sistema mantém essas descrições de coluna do usuário separadamente.  Portanto, há uma matriz de objetos ColumnDescription em cada ativo (uma por coluna para cada usuário que contribuiu com seu conhecimento sobre a coluna, além de possivelmente uma que contenha informações derivadas da fonte de dados).  O ColumnDescription está acoplado livremente ao esquema para que ele possa ficar fora de sincronia. O ColumnDescription pode descrever uma coluna que não existe mais no esquema.  Cabe ao escritor manter a descrição e o esquema em sincronia.  A fonte de dados também pode ter informações de descrição de colunas e são objetos ColumnDescription adicionais que seriam criados durante a execução da ferramenta.</td></tr>
<tr><td></td><td>columnName</td><td>Cadeia</td><td>O nome da coluna à qual esta descrição se refere.</td></tr>
<tr><td></td><td>description</td><td>Cadeia</td><td>uma breve descrição (2-3 linhas) da coluna.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Esta propriedade contém uma marca para uma coluna. Cada usuário do sistema pode adicionar várias marcas para uma determinada coluna e pode adicionar marcas para várias colunas. Somente o usuário que criou objetos ColumnTag pode editá-los. (Administradores e proprietários de ativos podem excluir o objeto ColumnTag, mas não editá-lo). O sistema mantém as marcas de coluna dos usuários separadamente.  Portanto, há uma matriz de objetos ColumnTag em cada ativo.  O ColumnTag está acoplado livremente ao esquema para que ele possa ficar fora de sincronia. O ColumnTag pode descrever uma coluna que não existe mais no esquema.  Cabe ao gravador manter a marca de coluna e o esquema em sincronia.</td></tr>
<tr><td></td><td>columnName</td><td>Cadeia</td><td>O nome da coluna à qual essa marca se refere.</td></tr>
<tr><td></td><td>tag</td><td>Cadeia</td><td>Uma marca que descreve a coluna.</td></tr>

<tr><td>Especialista ("especialistas")</td><td></td><td></td><td>Essa propriedade contém um usuário que é considerado um especialista no conjunto de dados. A bolha das opiniões (descrições) dos especialistas na parte superior do UX ao listar descrições. Cada usuário pode especificar seus próprios especialistas. Somente esse usuário pode editar o objeto experts. (Administradores e proprietários de ativos podem excluir os objetos especialistas, mas não editá-los).</td></tr>
<tr><td></td><td>especialista</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Visualização ("visualizações")</td><td></td><td></td><td>A visualização contém um instantâneo das 20 linhas de dados principais para o ativo. A visualização só faz sentido para alguns tipos de ativos (faz sentido para a tabela, mas não para a medida).</td></tr>
<tr><td></td><td>pré-visualização</td><td>object[]</td><td>Matriz de objetos que representam uma coluna.  Cada objeto tem um mapeamento de propriedade para uma coluna com um valor para essa coluna para a linha.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>MIME</td><td>Cadeia de caracteres</td><td>O tipo MIME do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>Cadeia de caracteres</td><td>As instruções sobre como obter acesso a esse ativo de dados. O conteúdo pode ser uma URL, um endereço de email ou um conjunto de instruções.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>O número de linhas no conjunto de dados</td></tr>
<tr><td></td><td>size</td><td>long</td><td>O tamanho em bytes do conjunto de dados.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>Cadeia de caracteres</td><td>A última vez em que o esquema foi modificado</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>Cadeia de caracteres</td><td>A última vez em que o conjunto de dados foi modificado (dados foram adicionados, modificados ou excluídos)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Colunas</td></td><td>ColumnDataProfile[]</td><td>Uma matriz de perfis de dados de coluna.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Cadeia</td><td>O nome da coluna à qual essa classificação se refere.</td></tr>
<tr><td></td><td>Classificação</td><td>Cadeia</td><td>A classificação dos dados nesta coluna.</td></tr>

<tr><td>Documentação ("documentação")</td><td></td><td></td><td>Um determinado ativo pode ter apenas uma documentação associada a ele.</td></tr>
<tr><td></td><td>MIME</td><td>Cadeia de caracteres</td><td>O tipo MIME do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>Cadeia de caracteres</td><td>O conteúdo da documentação.</td></tr>

</table>

### <a name="common-types"></a>Tipos comuns
Tipos comuns podem ser usados como os tipos de propriedades, mas não são itens.

<table>
<tr><td><b>Tipo comum</b></td><td><b>Properties</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>Cadeia de caracteres</td><td>Descreve o tipo de fonte de dados.  Por exemplo: SQL Server, Oracle Database, etc.  </td></tr>
<tr><td></td><td>objectType</td><td>Cadeia de caracteres</td><td>Descreve o tipo de objeto na fonte de dados. Por exemplo: Tabela, exibição para SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>Cadeia de caracteres</td><td>Necessário. Descreve um protocolo usado para se comunicar com a fonte de dados. Por exemplo: "TDS" para SQl Server, "Oracle" para Oracle, etc. Consulte <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">especificação de referência da fonte de dados – estrutura de DSL</a> para obter a lista de protocolos com suporte no momento.</td></tr>
<tr><td></td><td>endereço</td><td>Cadeia&lt;de caracteres do dicionário, objeto&gt;</td><td>Necessário. Address é um conjunto de dados específicos para o protocolo que é usado para identificar a fonte de dados que está sendo referenciada. Os dados de endereço com escopo para um protocolo específico, o que significa que ele é inútil sem saber o protocolo.</td></tr>
<tr><td></td><td>autenticação</td><td>Cadeia de caracteres</td><td>Opcional. O esquema de autenticação usado para se comunicar com a fonte de dados. Por exemplo: Windows, OAuth, etc.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Cadeia&lt;de caracteres do dicionário, objeto&gt;</td><td>Opcional. Informações adicionais sobre como se conectar a uma fonte de dados.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>O back-end não executa nenhuma validação das propriedades fornecidas no AAD durante a publicação.</td></tr>
<tr><td></td><td>sufixo</td><td>Cadeia de caracteres</td><td>Endereço de email exclusivo do usuário. Deve ser especificado se objectId não for fornecido ou no contexto da propriedade "lastRegisteredBy", caso contrário, opcional.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Identidade do AAD do grupo de segurança ou do usuário. Opcional. Deve ser especificado se o UPN não for fornecido, caso contrário, opcional.</td></tr>
<tr><td></td><td>firstName</td><td>Cadeia de caracteres</td><td>Nome do usuário (para fins de exibição). Opcional. Válido somente no contexto da propriedade "lastRegisteredBy". Não pode ser especificado ao fornecer a entidade de segurança para "funções", "permissões" e "especialistas".</td></tr>
<tr><td></td><td>lastName</td><td>Cadeia de caracteres</td><td>Último nome do usuário (para fins de exibição). Opcional. Válido somente no contexto da propriedade "lastRegisteredBy". Não pode ser especificado ao fornecer a entidade de segurança para "funções", "permissões" e "especialistas".</td></tr>

<tr><td>Coluna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>Cadeia de caracteres</td><td>Nome da coluna ou do atributo.</td></tr>
<tr><td></td><td>type</td><td>Cadeia de caracteres</td><td>tipo de dados da coluna ou do atributo. Os tipos permitidos dependem do sourceType de dados do ativo.  Há suporte apenas para um subconjunto de tipos.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>O comprimento máximo permitido para a coluna ou o atributo. Derivado da fonte de dados. Aplicável somente a alguns tipos de origem.</td></tr>
<tr><td></td><td>precisão</td><td>byte</td><td>A precisão da coluna ou do atributo. Derivado da fonte de dados. Aplicável somente a alguns tipos de origem.</td></tr>
<tr><td></td><td>isNullable</td><td>Booleano</td><td>Se a coluna tem permissão para ter um valor nulo ou não. Derivado da fonte de dados. Aplicável somente a alguns tipos de origem.</td></tr>
<tr><td></td><td>expression</td><td>Cadeia de caracteres</td><td>Se o valor for uma coluna calculada, esse campo conterá a expressão que expressa o valor. Derivado da fonte de dados. Aplicável somente a alguns tipos de origem.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>Cadeia de caracteres</td><td>O nome da coluna</td></tr>
<tr><td></td><td>type </td><td>Cadeia de caracteres</td><td>O tipo da coluna</td></tr>
<tr><td></td><td>mín. </td><td>Cadeia de caracteres</td><td>O valor mínimo no conjunto de dados</td></tr>
<tr><td></td><td>máx. </td><td>Cadeia de caracteres</td><td>O valor máximo no conjunto de dados</td></tr>
<tr><td></td><td>média </td><td>double</td><td>O valor médio no conjunto de dados</td></tr>
<tr><td></td><td>STDEV </td><td>double</td><td>O desvio padrão para o conjunto de dados</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>A contagem de valores nulos no conjunto de dados</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>A contagem de valores distintos no conjunto de dados</td></tr>


</table>

## <a name="asset-identity"></a>Identidade do ativo
O catálogo de dados do Azure usa o "protocolo" e as propriedades de identidade do recipiente de propriedades "address" da propriedade "DSL" do DataSourceLocation para gerar a identidade do ativo, que é usada para tratar o ativo dentro do catálogo.
Por exemplo, o protocolo "TDS" tem as propriedades de identidade "Server", "Database", "Schema" e "Object". As combinações do protocolo e as propriedades de identidade são usadas para gerar a identidade do ativo de tabela de SQL Server.
O catálogo de dados do Azure fornece vários protocolos de fonte de dados internos, que são listados em [especificação de referência de fonte de dados – estrutura de DSL](data-catalog-dsr.md).
O conjunto de protocolos com suporte pode ser estendido programaticamente (consulte referência da API REST do catálogo de dados). Os administradores do catálogo podem registrar protocolos de fonte de dados personalizados. A tabela a seguir descreve as propriedades necessárias para registrar um protocolo personalizado.

### <a name="custom-data-source-protocol-specification"></a>Especificação do protocolo de fonte de dados personalizada
<table>
<tr><td><b>Tipo</b></td><td><b>Properties</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>espaço de nomes</td><td>Cadeia de caracteres</td><td>O namespace do protocolo. O namespace deve ter entre 1 e 255 caracteres de comprimento, conter uma ou mais partes não vazias separadas por ponto (.). Cada parte deve ter entre 1 e 255 caracteres de comprimento, começar com uma letra e conter apenas letras e números.</td></tr>
<tr><td></td><td>name</td><td>Cadeia de caracteres</td><td>O nome do protocolo. O nome deve ter entre 1 e 255 caracteres de comprimento, começar com uma letra e conter apenas letras, números e o caractere de traço (-).</td></tr>
<tr><td></td><td>identidadeproperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista de propriedades de identidade, deve conter pelo menos uma, mas não mais do que 20 Propriedades. Por exemplo: "Server", "Database", "Schema", "Object" são propriedades de identidade do protocolo "TDS".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista de conjuntos de identidades. Define conjuntos de propriedades de identidade, que representam a identidade do ativo válido. Deve conter pelo menos um, mas não mais do que 20 conjuntos. Por exemplo: {"Server", "Database", "Schema" e "Object"} é um conjunto de identidades para o protocolo "TDS", que define a identidade do ativo de tabela do SQL Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>Cadeia de caracteres</td><td>O nome da propriedade. O nome deve ter entre 1 e 100 caracteres de comprimento, começar com uma letra e pode conter apenas letras e números.</td></tr>
<tr><td></td><td>type</td><td>Cadeia de caracteres</td><td>O tipo da propriedade. Valores com suporte: "bool", booliano "," byte "," GUID "," int "," inteiro "," Long "," String "," URL "</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Indica se o caso deve ser ignorado ao usar o valor da propriedade. Só pode ser especificado para propriedades com o tipo "String". O valor padrão é false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Indica se o caso deve ser ignorado para cada segmento do caminho da URL. Só pode ser especificado para propriedades com o tipo "URL". O valor padrão é [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>Cadeia de caracteres</td><td>O nome do conjunto de identidades.</td></tr>
<tr><td></td><td>properties</td><td>string[]</td><td>A lista de propriedades de identidade incluídas nesse conjunto de identidades. Ele não pode conter duplicatas. Cada propriedade referenciada pelo conjunto de identidades deve ser definida na lista de "identityproperties" do protocolo.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Funções e autorização
Microsoft Azure catálogo de dados fornece recursos de autorização para operações CRUD em ativos e anotações.

## <a name="key-concepts"></a>Conceitos-chave
O catálogo de dados do Azure usa dois mecanismos de autorização:

* Autorização baseada em função
* Autorização baseada em permissão

### <a name="roles"></a>Funções
Há três funções: **Administrador**, **proprietário**e **colaborador**.  Cada função tem seu escopo e direitos, que são resumidos na tabela a seguir.

<table><tr><td><b>Função</b></td><td><b>Âmbito</b></td><td><b>Privilégios</b></td></tr><tr><td>Admistrador</td><td>Catálogo (todos os ativos/anotações no catálogo)</td><td>Ler excluir ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Owner</td><td>Cada ativo (item raiz)</td><td>Ler excluir ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contribuinte</td><td>Cada ativo individual e anotação</td><td>Read Update Delete ViewRoles Observação: todos os direitos serão revogados se o direito de leitura no item for revogado do colaborador</td></tr></table>

> [!NOTE]
> Os direitos de **leitura**, **atualização**, **exclusão**e **ViewRoles** são aplicáveis a qualquer item (ativo ou anotação) enquanto **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** são apenas aplicável ao ativo raiz.
> 
> **Excluir** direito aplica-se a um item e a todos os subitens ou a um único item abaixo dele. Por exemplo, a exclusão de um ativo também exclui todas as anotações desse ativo.
> 
> 

### <a name="permissions"></a>Permissões
A permissão é como uma lista de entradas de controle de acesso. Cada entrada de controle de acesso atribui um conjunto de direitos a uma entidade de segurança. As permissões só podem ser especificadas em um ativo (ou seja, item raiz) e se aplicam ao ativo e a qualquer subitem.

Durante a visualização do **Catálogo de dados do Azure** , somente a permissão de **leitura** tem suporte na lista de permissões para habilitar o cenário para restringir a visibilidade de um ativo.

Por padrão, qualquer usuário autenticado tem direito de **leitura** para qualquer item no catálogo, a menos que a visibilidade seja restrita ao conjunto de entidades de segurança nas permissões.

## <a name="rest-api"></a>API REST
As solicitações **Put** e **post** do item de exibição podem ser usadas para controlar funções e permissões: além da carga do item, duas propriedades do sistema podem ser **funções** e **permissões**especificadas.

> [!NOTE]
> **as permissões** se aplicam somente a um item raiz.
> 
> A função de **proprietário** é aplicável somente a um item raiz.
> 
> Por padrão, quando um item é criado no catálogo, seu **colaborador** é definido como o usuário atualmente autenticado. Se o item deve ser atualizável por todos, o **colaborador** deve ser &lt;definido&gt; como uma entidade de segurança especial de todos na propriedade de **funções** quando o item é publicado pela primeira vez (consulte o exemplo a seguir). O **colaborador** não pode ser alterado e permanece o mesmo durante o tempo de vida de um item (mesmo **administrador** ou **proprietário** não tem o direito de alterar o **colaborador**). O único valor com suporte para a configuração explícita do **colaborador** é &lt;todos&gt;: O **colaborador** só pode ser um usuário que criou um item &lt;ou&gt;todos.
> 
> 

### <a name="examples"></a>Exemplos
**Defina colaborador como &lt;todos&gt; ao publicar um item.**
A entidade &lt;de segurança&gt; especial Everyone tem ObjectID "00000000-0000-0000-0000-000000000201".
  **Postar** https:\//API.azuredatacatalog.com/catalogs/default/views/Tables/?API-Version=2016-03-30

> [!NOTE]
> Algumas implementações de cliente HTTP podem emitir solicitações automaticamente em resposta a um 302 do servidor, mas normalmente distribuem os cabeçalhos de autorização da solicitação. Como o cabeçalho de autorização é necessário para fazer solicitações ao catálogo de dados do Azure, você deve garantir que o cabeçalho de autorização ainda seja fornecido ao emitir novamente uma solicitação para um local de redirecionamento especificado pelo catálogo de dados do Azure. O código de exemplo a seguir demonstra isso usando o objeto HttpWebRequest do .NET.
> 
> 

**Corpo**
```json
    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }
```

  **Atribuir proprietários e restringir a visibilidade de um item raiz existente**: **Colocar** https:\//API.azuredatacatalog.com/catalogs/default/views/Tables/042297b0...1be45ecd462a?API-Version=2016-03-30

```json
    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }
```

> [!NOTE]
> Em PUT, não é necessário especificar uma carga de item no corpo: PUT pode ser usado para atualizar apenas funções e/ou permissões.
> 
