---
title: Conceitos de desenvolvimento do Catálogo de Dados Azure
description: Introdução aos conceitos-chave no modelo conceptual do Azure Data Catalog, conforme exposto através do Catálogo REST API.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ddeab4838feb07d1101993cab4ebc86581b4d8b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674704"
---
# <a name="azure-data-catalog-developer-concepts"></a>Conceitos de desenvolvimento do Catálogo de Dados Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

O Microsoft **Azure Data Catalog** é um serviço de cloud totalmente gerido que fornece capacidades para a descoberta de fontes de dados e para o crowdsourcing de metadados de origem de dados. Os desenvolvedores podem utilizar o serviço através das suas APIs REST. Compreender os conceitos implementados no serviço é importante para que os desenvolvedores se integrem com sucesso com **o Azure Data Catalog.**

## <a name="key-concepts"></a>Conceitos-chave 
O modelo conceptual **do Azure Data Catalog** baseia-se em quatro conceitos-chave: **Catálogo,** **Utilizadores,** **Ativos** e **Anotações.**

![Ilustração de modelo conceptual do catálogo de dados Azure](./media/data-catalog-developer-concepts/concept2.png)

### <a name="catalog"></a>Catálogo
Um **Catálogo** é o recipiente de alto nível para todos os metadados que uma organização armazena. Há um **Catálogo** permitido por Conta Azure. Os catálogos estão ligados a uma subscrição do Azure, mas apenas um **Catálogo** pode ser criado para qualquer conta Azure, mesmo que uma conta possa ter várias subscrições.

Um catálogo contém **Utilizadores** e **Ativos.**

### <a name="users"></a>Utilizadores
Os utilizadores são principais de segurança que têm permissões para realizar ações (pesquisar o catálogo, adicionar, editar ou remover itens, etc.) no Catálogo.

Existem vários papéis diferentes que um utilizador pode ter. Para obter informações sobre as funções, consulte a secção Funções e Autorização.

Podem ser adicionados utilizadores individuais e grupos de segurança.

O Azure Data Catalog utiliza o Azure Ative Directory para gestão de identidade e acesso. Cada utilizador do Catálogo deve ser membro do Ative Directory para a conta.

### <a name="assets"></a>Elementos
Um **Catálogo** contém ativos de dados. **Os ativos** são a unidade de granularidade gerida pelo catálogo.

A granularidade de um ativo varia por fonte de dados. Para o SQL Server ou Oracle Database, um ativo pode ser uma tabela ou uma vista. Para os Serviços de Análise de Servidores SQL, um ativo pode ser uma medida, uma dimensão ou um indicador de desempenho chave (KPI). Para os Serviços de Relato de Servidores SQL, um ativo é um Relatório.

Um **Ativo** é a coisa que adiciona ou remove de um Catálogo. É a unidade de resultado que recebe de **Search.**

Um **Ativo** é composto pelo seu nome, localização e tipo, e anotações que o descrevem ainda mais.

### <a name="annotations"></a>Anotações
Anotações são itens que representam metadados sobre ativos.

Exemplos de anotações são descrição, tags, esquemas, documentação, etc. Consulte a secção de [modelos do Objeto de Ativo](#asset-object-model) para obter uma lista completa dos tipos de ativos e tipos de anotação.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Anotações de crowdsourcing e perspetiva do utilizador (multiplicidade de opinião)
Um aspeto chave do Azure Data Catalog é a forma como suporta o crowdsourcing de metadados no sistema. Ao contrário de uma abordagem wiki – onde existe apenas uma opinião e o último escritor ganha – o modelo Azure Data Catalog permite que várias opiniões vivam lado a lado no sistema.

Esta abordagem reflete o mundo real dos dados empresariais onde diferentes utilizadores podem ter diferentes perspetivas sobre um determinado ativo:

* Um administrador de base de dados pode fornecer informações sobre contratos de nível de serviço, ou a janela de processamento disponível para operações ETL a granel
* Um gestor de dados pode fornecer informações sobre os processos de negócio a que o ativo se aplica, ou as classificações que a empresa lhe aplicou
* Um analista financeiro pode fornecer informações sobre como os dados são usados durante tarefas de reporte de fim de período

Para apoiar este exemplo, cada utilizador – o DBA, o administrador de dados e o analista – pode adicionar uma descrição a uma única tabela que tenha sido registada no Catálogo. Todas as descrições são mantidas no sistema e no portal Azure Data Catalog todas as descrições são exibidas.

Este padrão é aplicado à maioria dos itens no modelo de objeto, por isso os tipos de objetos na carga útil JSON são muitas vezes matrizes para propriedades onde você pode esperar um singleton.

Por exemplo, sob a raiz do ativo está uma matriz de objetos de descrição. A propriedade da matriz é chamada de "descrições". Um objeto de descrição tem uma propriedade - descrição. O padrão é que cada utilizador que escreve a descrição obtém um objeto de descrição criado para o valor fornecido pelo utilizador.

O UX pode então escolher como exibir a combinação. Há três padrões diferentes para exibição.

* O padrão mais simples é "Mostrar Tudo". Neste padrão, todos os objetos são mostrados numa vista de lista. O portal Azure Data Catalog UX utiliza este padrão para descrição.
* Outro padrão é "Fusão". Neste padrão, todos os valores dos diferentes utilizadores são fundidos em conjunto, com duplicado removido. Exemplos deste padrão no portal Azure Data Catalog UX são as propriedades das tags e especialistas.
* Um terceiro padrão é "o último escritor ganha". Neste padrão, apenas é mostrado o valor mais recente dactilografado. friendlyName é um exemplo deste padrão.

## <a name="asset-object-model"></a>Modelo de objeto de ativo
Conforme introduzido na secção Conceitos Chave, o modelo de objeto **do Catálogo de Dados Azure** inclui itens, que podem ser ativos ou anotações. Os itens têm propriedades, que podem ser opcionais ou necessárias. Algumas propriedades aplicam-se a todos os itens. Algumas propriedades aplicam-se a todos os ativos. Algumas propriedades aplicam-se apenas a tipos específicos de ativos.

### <a name="system-properties"></a>Propriedades do sistema
<table><tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr><tr><td>carimbo de data/hora</td><td>DateTime</td><td>A última vez que o artigo foi modificado. Este campo é gerado pelo servidor quando um item é inserido e cada vez que um item é atualizado. O valor deste imóvel é ignorado na entrada de operações de publicação.</td></tr><tr><td>ID</td><td>Uri</td><td>Url absoluto do item (apenas para leitura). É o URI endereçada único para o item.  O valor deste imóvel é ignorado na entrada de operações de publicação.</td></tr><tr><td>tipo</td><td>String</td><td>O tipo de ativo (apenas para leitura).</td></tr><tr><td>etag</td><td>String</td><td>Uma cadeia correspondente à versão do item que pode ser usada para um controlo otimista de concordância ao executar operações que atualizam itens no catálogo. "*" pode ser usado para corresponder a qualquer valor.</td></tr></table>

### <a name="common-properties"></a>Common properties (Propriedades comuns)
Estas propriedades aplicam-se a todos os tipos de ativos de raiz e a todos os tipos de anotação.

<table>
<tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleano</td><td>Indica se os dados do item são derivados de um sistema de origem (como SQL Server Database, Oracle Database) ou da autoria de um utilizador.</td></tr>
</table>

### <a name="common-root-properties"></a>Propriedades de raiz comuns
<p>
Estas propriedades aplicam-se a todos os tipos de ativos de raiz.

<table><tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr><tr><td>name</td><td>String</td><td>Um nome derivado da informação de localização da fonte de dados</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Descreve exclusivamente a fonte de dados e é um dos identificadores do ativo. (Ver secção de dupla identidade).  A estrutura do dsl varia de acordo com o protocolo e o tipo de fonte.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mais detalhes sobre o tipo de ativo.</td></tr><tr><td>últimaRegisteredBy</td><td>SegurançaPrincipal</td><td>Descreve o utilizador que registou recentemente este ativo.  Contém tanto o ID único para o utilizador (o upn) como um nome de exibição (último Nome e primeiro Nome).</td></tr><tr><td>containerID</td><td>String</td><td>Identificação do ativo do contentor para a fonte de dados. Esta propriedade não é suportada para o tipo de Contentor.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Propriedades comuns de anotação não singleton
Estas propriedades aplicam-se a todos os tipos de anotação não singleton (anotações, que permitiram ser múltiplas por ativo).

<table>
<tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>key</td><td>String</td><td>Uma chave especificada pelo utilizador, que identifica exclusivamente a anotação na coleção atual. O comprimento da chave não pode exceder 256 caracteres.</td></tr>
</table>

### <a name="root-asset-types"></a>Tipos de ativos de raiz
Os tipos de ativos de raiz são os tipos que representam os vários tipos de ativos de dados que podem ser registados no catálogo. Para cada tipo de raiz, há uma vista, que descreve o ativo e as anotações incluídas na vista. O nome de visualização deve ser usado no segmento de url {view_name} correspondente ao publicar um ativo utilizando a API REST.

<table><tr><td><b>Tipo de ativo (ver nome)</b></td><td><b>Propriedades Adicionais</b></td><td><b>Tipo de Dados</b></td><td><b>Anotações permitidas</b></td><td><b>Comentários</b></td></tr><tr><td>Tabela ("mesas")</td><td></td><td></td><td>Description<p>FriendlyName<p>Etiqueta<p>Esquema<p>Descrição da Coluna<p>ColunaTag<p> Especialista<p>Pré-visualizar<p>AcessosIntrusão<p>TableDataProfile<p>ColumnDataProfile<p>ColunaDataClassificação<p>Documentação<p></td><td>Uma Tabela representa quaisquer dados tabulares.  Por exemplo: SqL Table, SQL View, Analysis Services Tabular Table, Analysis Services Multidimensional Dimension, Oracle Table, etc.   </td></tr><tr><td>Medida ("medidas")</td><td></td><td></td><td>Description<p>FriendlyName<p>Etiqueta<p>Especialista<p>AcessosIntrusão<p>Documentação<p></td><td>Este tipo representa uma medida dos Serviços de Análise.</td></tr><tr><td></td><td>medida</td><td>Coluna</td><td></td><td>Metadados que descrevem a medida</td></tr><tr><td></td><td>isCalculado </td><td>Booleano</td><td></td><td>Especifica se a medida é calculada ou não.</td></tr><tr><td></td><td>grupo de medidas</td><td>String</td><td></td><td>Recipiente físico para medida</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>Description<p>FriendlyName<p>Etiqueta<p>Especialista<p>AcessosIntrusão<p>Documentação</td><td></td></tr><tr><td></td><td>grupo de medidas</td><td>String</td><td></td><td>Recipiente físico para medida</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Uma expressão numérica MDX ou um cálculo que devolve o valor-alvo do KPI.</td></tr><tr><td></td><td>valorExpressão</td><td>String</td><td></td><td>Uma expressão numérica MDX que devolve o valor real do KPI.</td></tr><tr><td></td><td>statusExpressão</td><td>String</td><td></td><td>Uma expressão MDX que representa o estado do KPI num ponto especificado no tempo.</td></tr><tr><td></td><td>tendênciaExpressão</td><td>String</td><td></td><td>Uma expressão MDX que avalia o valor do KPI ao longo do tempo. A tendência pode ser qualquer critério baseado no tempo que seja útil num contexto de negócio específico.</td>
<tr><td>Relatório ("relatórios")</td><td></td><td></td><td>Description<p>FriendlyName<p>Etiqueta<p>Especialista<p>AcessosIntrusão<p>Documentação<p></td><td>Este tipo representa um relatório de Serviços de Relatório de Servidores SQL </td></tr><tr><td></td><td>activoCriatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetCriatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>activoModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>Contentor ("contentores")</td><td></td><td></td><td>Description<p>FriendlyName<p>Etiqueta<p>Especialista<p>AcessosIntrusão<p>Documentação<p></td><td>Este tipo representa um contentor de outros ativos, como uma base de dados SQL, um contentor Azure Blobs ou um modelo de Serviços de Análise.</td></tr></table>

### <a name="annotation-types"></a>Tipos de anotação
Os tipos de anotação representam tipos de metadados que podem ser atribuídos a outros tipos dentro do catálogo.

<table>
<tr><td><b>Tipo de anotação (nome de vista aninhado)</b></td><td><b>Propriedades Adicionais</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>Descrição ("descrições")</td><td></td><td></td><td>Esta propriedade contém uma descrição para um ativo. Cada utilizador do sistema pode adicionar a sua própria descrição.  Só esse utilizador pode editar o objeto Descrição.  (Os administradores e os proprietários de ativos podem eliminar o objeto Descrição, mas não editá-lo). O sistema mantém as descrições dos utilizadores separadamente.  Assim, há uma série de descrições em cada ativo (uma para cada utilizador que tenha contribuído com o seu conhecimento sobre o ativo, além de possivelmente uma que contenha informações derivadas da fonte de dados).</td></tr>
<tr><td></td><td>descrição</td><td>string</td><td>Uma breve descrição (2-3 linhas) do ativo</td></tr>

<tr><td>Tag ("tags")</td><td></td><td></td><td>Esta propriedade define uma etiqueta para um ativo. Cada utilizador do sistema pode adicionar várias tags para um ativo.  Apenas o utilizador que criou os objetos Tag pode editá-los.  (Os proprietários de administradores e ativos podem eliminar o objeto Tag, mas não editá-lo). O sistema mantém as etiquetas dos utilizadores separadamente.  Assim, há uma variedade de objetos tag em cada ativo.</td></tr>
<tr><td></td><td>etiqueta</td><td>string</td><td>Uma etiqueta descrevendo o ativo.</td></tr>

<tr><td>Nome amigável ("nome amigável")</td><td></td><td></td><td>Esta propriedade contém um nome amigável para um ativo. FriendlyName é uma anotação singleton - apenas um Bolo Amigável pode ser adicionado a um ativo.  Apenas o utilizador que criou o objeto FriendlyName pode editá-lo. (Os proprietários de administradores e ativos podem eliminar o objeto FriendlyName, mas não editá-lo). O sistema mantém os nomes amigáveis dos utilizadores separadamente.</td></tr>
<tr><td></td><td>nome amigável</td><td>string</td><td>Um nome amigável do bem.</td></tr>

<tr><td>Esquema ("schema")</td><td></td><td></td><td>O Schema descreve a estrutura dos dados.  Ele lista os nomes de atributos (coluna, atributo, campo, etc.) nomes, tipos e outros metadados.  Esta informação é derivada da fonte de dados.  Schema é uma anotação singleton - apenas um Esquema pode ser adicionado para um ativo.</td></tr>
<tr><td></td><td>colunas</td><td>Coluna[]</td><td>Uma série de objetos de coluna. Descrevem a coluna com informações derivadas da fonte de dados.</td></tr>

<tr><td>ColunaDescrição ("colunadescrições")</td><td></td><td></td><td>Esta propriedade contém uma descrição para uma coluna.  Cada utilizador do sistema pode adicionar as suas próprias descrições para várias colunas (no máximo uma por coluna). Apenas o utilizador que criou os objetos ColunaDescricção pode editá-los.  (Os administradores e os proprietários de ativos podem eliminar o objeto ColunaDscription, mas não editá-lo). O sistema mantém separadamente as descrições da coluna deste utilizador.  Assim, existe uma matriz de objetos Descrição de Colunas em cada ativo (um por coluna para cada utilizador que tenha contribuído com os seus conhecimentos sobre a coluna, além de possivelmente um que contenha informações derivadas da fonte de dados).  A ColunaDescriminação está vagamente ligada ao Esquema para que possa sair de sincronização. A ColunaDescriminação pode descrever uma coluna que já não existe no esquema.  Cabe ao escritor manter a descrição e o esquema sincronizados.  A fonte de dados também pode ter informações de descrição de colunas e são objetos adicionais de Descrição de Colunas que seriam criados ao executar a ferramenta.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna a que esta descrição se refere.</td></tr>
<tr><td></td><td>descrição</td><td>String</td><td>uma breve descrição (2-3 linhas) da coluna.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Esta propriedade contém uma etiqueta para uma coluna. Cada utilizador do sistema pode adicionar várias tags para uma determinada coluna e pode adicionar tags para várias colunas. Apenas o utilizador que criou os objetos ColumnTag pode editá-los. (Os proprietários de administradores e ativos podem eliminar o objeto ColumnTag, mas não editá-lo). O sistema mantém as etiquetas de coluna destes utilizadores separadamente.  Assim, há uma variedade de objetos ColumnTag em cada ativo.  O ColumnTag está vagamente ligado ao esquema para que possa sair de sincronização. O ColumnTag pode descrever uma coluna que já não existe no esquema.  Cabe ao escritor manter a etiqueta e o esquema sincronizados.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna a que esta etiqueta se refere.</td></tr>
<tr><td></td><td>etiqueta</td><td>String</td><td>Uma etiqueta descrevendo a coluna.</td></tr>

<tr><td>Perito ("especialistas")</td><td></td><td></td><td>Esta propriedade contém um utilizador que é considerado um perito no conjunto de dados. As opiniões dos peritos (descrições) bolha para o topo do UX ao enumerar descrições. Cada utilizador pode especificar os seus próprios especialistas. Só esse utilizador pode editar o objeto dos especialistas. (Os administradores e proprietários de ativos podem eliminar os objetos Expert, mas não editá-lo).</td></tr>
<tr><td></td><td>especialista</td><td>SegurançaPrincipal</td><td></td></tr>

<tr><td>Pré-visualização ("pré-visualizações")</td><td></td><td></td><td>A pré-visualização contém uma imagem das 20 principais linhas de dados para o ativo. A pré-visualização só faz sentido para alguns tipos de ativos (faz sentido para a Tabela, mas não para a Medida).</td></tr>
<tr><td></td><td>preview</td><td>objeto[]</td><td>Conjunto de objetos que representam uma coluna.  Cada objeto tem um mapeamento de propriedade para uma coluna com um valor para esta coluna para a linha.</td></tr>

<tr><td>Estrutura de Acessos ("acessibilidadesInstruções")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mímicaType</td><td>string</td><td>O tipo de mímica do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>string</td><td>As instruções para como obter acesso a este ativo de dados. O conteúdo pode ser um URL, um endereço de e-mail ou um conjunto de instruções.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>númeroOfRows</td></td><td>int</td><td>O número de linhas no conjunto de dados</td></tr>
<tr><td></td><td>size</td><td>long</td><td>O tamanho dos bytes do conjunto de dados.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>A última vez que o esquema foi modificado</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>A última vez que o conjunto de dados foi modificado (os dados foram adicionados, modificados ou apagados)</td></tr>

<tr><td>ColunasDataProfile ("colunasDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>colunas</td></td><td>ColumnDataProfile[]</td><td>Uma série de perfis de dados de colunas.</td></tr>

<tr><td>ColumnDataClassification ("colunaDataClassificações")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna a que esta classificação se refere.</td></tr>
<tr><td></td><td>classificação</td><td>String</td><td>A classificação dos dados nesta coluna.</td></tr>

<tr><td>Documentação ("documentação")</td><td></td><td></td><td>Um dado ativo pode ter apenas uma documentação associada a ele.</td></tr>
<tr><td></td><td>mímicaType</td><td>string</td><td>O tipo de mímica do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>string</td><td>O conteúdo da documentação.</td></tr>

</table>

### <a name="common-types"></a>Tipos comuns
Os tipos comuns podem ser usados como tipos de propriedades, mas não são itens.

<table>
<tr><td><b>Tipo comum</b></td><td><b>Propriedades</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>origemType</td><td>string</td><td>Descreve o tipo de fonte de dados.  Por exemplo: SQL Server, Oracle Database, etc.  </td></tr>
<tr><td></td><td>objectType</td><td>string</td><td>Descreve o tipo de objeto na fonte de dados. Por exemplo: Tabela, Vista para SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocolo</td><td>string</td><td>Obrigatório. Descreve um protocolo usado para comunicar com a fonte de dados. Por exemplo: `tds` para SQL Server, `oracle` para Oráculo, etc. Consulte a [especificação de referência de fonte de dados - Estrutura DSL](data-catalog-dsr.md) para a lista de protocolos atualmente suportados.</td></tr>
<tr><td></td><td>address</td><td>Cadeia de &lt; dicionário, objeto&gt;</td><td>Obrigatório. O endereço é um conjunto de dados específicos do protocolo que é utilizado para identificar a fonte de dados que está a ser referenciada. Os dados de endereços a um determinado protocolo, o que significa que não faz sentido sem conhecer o protocolo.</td></tr>
<tr><td></td><td>autenticação</td><td>string</td><td>Opcional. O esquema de autenticação utilizado para comunicar com a fonte de dados. Por exemplo: janelas, ausuth, etc.</td></tr>
<tr><td></td><td>conexõesProperties</td><td>Cadeia de &lt; dicionário, objeto&gt;</td><td>Opcional. Informações adicionais sobre como ligar-se a uma fonte de dados.</td></tr>

<tr><td>SegurançaPrincipal</td><td></td><td></td><td>O backend não efetua qualquer validação de propriedades fornecidas contra o Azure Ative Directory durante a publicação.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Endereço de e-mail único do utilizador. Deve ser especificado se o objectId não for fornecido ou no contexto da propriedade "lastRegisteredBy", caso contrário opcional.</td></tr>
<tr><td></td><td>objectId</td><td>GUID</td><td>Identidade do utilizador ou grupo de segurança Azure Ative Directory. Opcional. Deve ser especificado se upn não for fornecido, caso contrário opcional.</td></tr>
<tr><td></td><td>nomePróprio</td><td>string</td><td>Primeiro nome do utilizador (para efeitos de visualização). Opcional. Apenas válido no contexto da propriedade "lastRegisteredBy". Não é possível especificar quando se fornece o principal de segurança para "funções", "permissões" e "peritos".</td></tr>
<tr><td></td><td>apelido</td><td>string</td><td>Apelido do utilizador (para efeitos de visualização). Opcional. Apenas válido no contexto da propriedade "lastRegisteredBy". Não é possível especificar quando se fornece o principal de segurança para "funções", "permissões" e "peritos".</td></tr>

<tr><td>Coluna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>Nome da coluna ou atributo.</td></tr>
<tr><td></td><td>tipo</td><td>string</td><td>tipo de dados da coluna ou atributo. Os tipos admissíveis dependem da fonte de dadosType do ativo.  Apenas um subconjunto de tipos é suportado.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>O comprimento máximo permitido para a coluna ou atributo. Derivado de fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>precisão</td><td>byte</td><td>A precisão para a coluna ou atributo. Derivado de fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>isNullable</td><td>Booleano</td><td>Se a coluna pode ter um valor nulo ou não. Derivado de fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>expression</td><td>string</td><td>Se o valor for uma coluna calculada, este campo contém a expressão que expressa o valor. Derivado de fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>O nome da coluna</td></tr>
<tr><td></td><td>tipo </td><td>string</td><td>O tipo da coluna</td></tr>
<tr><td></td><td>min </td><td>string</td><td>O valor mínimo no conjunto de dados</td></tr>
<tr><td></td><td>max </td><td>string</td><td>O valor máximo no conjunto de dados</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>O valor médio no conjunto de dados</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>O desvio padrão para o conjunto de dados</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>A contagem de valores nulos no conjunto de dados</td></tr>
<tr><td></td><td>Distintas  </td><td>int</td><td>A contagem de valores distintos no conjunto de dados</td></tr>
</table>

## <a name="asset-identity"></a>Identidade de ativo
O Azure Data Catalog utiliza propriedades de "protocolo" e identidade a partir do saco de propriedade "address" da propriedade DataSourceLocation "dsl" para gerar identidade do ativo, que é usado para endereçar o ativo dentro do Catálogo.
Por exemplo, o protocolo Tabular Data Stream (TDS) tem propriedades de identidade "servidor", "base de dados", "esquema" e "objeto". As combinações do protocolo e das propriedades de identidade são usadas para gerar a identidade do SqL Server Table Asset.
O Azure Data Catalog fornece vários protocolos de fonte de dados incorporados, que estão listados na [especificação de referência de fonte de dados - Estrutura DSL](data-catalog-dsr.md).
O conjunto de protocolos suportados pode ser alargado programáticamente (Consulte a referência API do Catálogo de Dados REST). Os administradores do Catálogo podem registar protocolos de fonte de dados personalizados. A tabela seguinte descreve as propriedades necessárias para registar um protocolo personalizado.

### <a name="custom-data-source-protocol-specification"></a>Especificação personalizada do protocolo de fonte de dados
<table>
<tr><td><b>Tipo</b></td><td><b>Propriedades</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>espaço de nomes</td><td>string</td><td>O espaço de nome do protocolo. O espaço de nome deve ter entre 1 e 255 caracteres de comprimento, conter uma ou mais partes não vazias separadas por ponto (.). Cada parte deve ter entre 1 e 255 caracteres, comece com uma letra e contenha apenas letras e números.</td></tr>
<tr><td></td><td>name</td><td>string</td><td>O nome do protocolo. O nome deve ter entre 1 e 255 caracteres, comece com uma letra e contenha apenas letras, números e o caráter (-) traço.</td></tr>
<tr><td></td><td>identidadeProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>A lista de propriedades identitárias deve conter pelo menos uma, mas não mais de 20 propriedades. Por exemplo: "servidor", "base de dados", "esquema", "objeto" são propriedades de identidade do protocolo "tds".</td></tr>
<tr><td></td><td>conjuntos de identidades</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista de conjuntos de identidade. Define conjuntos de propriedades identitárias, que representam a identidade válida do ativo. Deve conter pelo menos um, mas não mais de 20 conjuntos. Por exemplo: {"servidor", "base de dados", "schema" e "objeto"} é um conjunto de identidade para o protocolo TDS, que define a identidade do ativo da Tabela de Servidor SQL.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>O nome da propriedade. O nome deve ter entre 1 e 100 caracteres, comece com uma letra e contenha apenas letras e números.</td></tr>
<tr><td></td><td>tipo</td><td>string</td><td>O tipo da propriedade. Valores apoiados: "bool", boolean, "byte", "guid", "int", "inteiro", "longo", "string", "url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Indica se o caso deve ser ignorado ao utilizar o valor da propriedade. Só pode ser especificado para propriedades com o tipo "string". O valor predefinido é falso.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Indica se o caso deve ser ignorado para cada segmento do caminho do url. Só pode ser especificado para propriedades com o tipo "url". O valor predefinido é [falso].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>name</td><td>string</td><td>O nome do conjunto de identidade.</td></tr>
<tr><td></td><td>propriedades</td><td>corda[]</td><td>A lista de propriedades identitárias incluídas neste conjunto de identidade. Não pode conter duplicados. Cada imóvel referenciado por conjunto de identidade deve ser definido na lista de "IdentidadeProperties" do protocolo.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Funções e autorização
O Microsoft Azure Data Catalog fornece capacidades de autorização para operações CRUD em ativos e anotações.

O Catálogo de Dados Azure utiliza dois mecanismos de autorização:

* Autorização baseada em funções
* Autorização baseada em permissão

### <a name="roles"></a>Funções
Existem três funções: **Administrador,** **Proprietário** e **Colaborador.**  Cada papel tem o seu âmbito e os seus direitos, que são resumidos no quadro seguinte.

<table><tr><td><b>Role</b></td><td><b>Âmbito</b></td><td><b>Direitos</b></td></tr><tr><td>Administrador</td><td>Catálogo (todos os ativos/anotações no Catálogo)</td><td>Ler Delete VerRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietário</td><td>Cada ativo (item raiz)</td><td>Ler Delete VerRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contribuinte</td><td>Cada ativo individual e anotação</td><td>Ler Atualização Eliminar VerRoles Nota: todos os direitos são revogados se o direito de leitura do artigo for revogado do Contribuinte</td></tr></table>

> [!NOTE]
> **Leia**, **Atualização**, **Apagar**, **VerRoles** os direitos são aplicáveis a qualquer item (ativo ou anotação) enquanto **TakeOwnership,** **ChangeOwnership,** **ChangeVisibility,** **ViewPermissions** são aplicáveis apenas ao ativo raiz.
> 
> **Eliminar** o direito aplica-se a um item e a quaisquer subitems ou item único por baixo dele. Por exemplo, a eliminação de um ativo também elimina quaisquer anotações para esse ativo.
> 

### <a name="permissions"></a>Permissões
A permissão é a lista de entradas de controlo de acesso. Cada entrada de controlo de acesso atribui um conjunto de direitos a um principal de segurança. As permissões só podem ser especificadas num ativo (isto é, raiz) e aplicar-se ao ativo e a quaisquer subíndices.

Durante a pré-visualização do **Catálogo de Dados Azure,** apenas o **direito de leitura** é suportado na lista de permissões para permitir que o cenário restrinja a visibilidade de um ativo.

Por predefinição, qualquer utilizador autenticado tem o direito de **ler** qualquer item no catálogo, a menos que a visibilidade seja restrita ao conjunto de principais nas permissões.

## <a name="rest-api"></a>API REST
**Os** pedidos de artigos de visualização PUT e **POST** podem ser utilizados para controlar funções e permissões: além da carga útil do item, duas propriedades do sistema podem ser especificadas **funções** e **permissões**.

> [!NOTE]
> **permissões apenas** aplicáveis a um item raiz.
> 
> **Função do proprietário** apenas aplicável a um item raiz.
> 
> Por predefinição, quando um item é criado no catálogo, o seu **Contribuinte** é definido para o utilizador atualmente autenticado. Se o item for exequível por todos, **o Contribuinte** deve ser definido para Todos &lt; os &gt; principais seguranças especiais na propriedade **de funções** quando o artigo for publicado pela primeira vez (consulte o exemplo seguinte). **O contribuinte** não pode ser alterado e permanece o mesmo durante o tempo de vida de um item (mesmo **o Administrador** ou **Proprietário** não tem o direito de alterar o **Contribuinte**). O único valor suportado para a definição explícita do **Contribuinte** é &lt; Todos : O &gt; **contribuinte** só pode ser um utilizador que criou um item ou &lt; todos &gt; .
> 

### <a name="examples"></a>Exemplos
**Desa estação de colaboradores para &lt; todos ao publicar um &gt; artigo.**
Diretor especial de segurança &lt; Todos &gt; têm objectid "0000000-0000-0000-0000-0000-00000000000201".
  **POST** https: \/ /api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Algumas implementações de clientes HTTP podem reeditar automaticamente os pedidos em resposta a um 302 do servidor, mas normalmente desmontam os cabeçalhos de autorização do pedido. Uma vez que o cabeçalho de Autorização é obrigado a fazer pedidos ao Catálogo de Dados do Azure, deve certificar-se de que o cabeçalho de Autorização ainda é fornecido ao reemissão de um pedido para um local de redirecionamento especificado pelo Catálogo de Dados Azure. O seguinte código de amostra demonstra-o utilizando o objeto .NET HttpWebRequest.
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

  **Atribuir aos proprietários e restringir a visibilidade para um item raiz existente**: **PUT** https: \/ /api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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
> Em PUT não é necessário especificar uma carga útil do item no corpo: O PUT pode ser usado para atualizar apenas funções e/ou permissões.
> 

## <a name="next-steps"></a>Passos seguintes
[Referência Azure Data Catalog REST API](/rest/api/datacatalog/)
