---
title: Conceitos de desenvolvedor estoque do Catálogo de Dados Azure
description: Introdução aos conceitos-chave no modelo conceptual do Catálogo de Dados Do Azure, conforme exposto através da API DO CATÁLOGO REST.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 80adc98255cfc9145d583ac775bbc490d599234e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68976835"
---
# <a name="azure-data-catalog-developer-concepts"></a>Conceitos de desenvolvedor estoque do Catálogo de Dados Azure
O Microsoft **Azure Data Catalog** é um serviço de cloud totalmente gerido que fornece capacidades para a descoberta de fontes de dados e para metadados de fonte de dados de crowdsourcing. Os desenvolvedores podem usar o serviço através das suas APIs REST. Compreender os conceitos implementados no serviço é importante para que os desenvolvedores se integrem com sucesso com o Catálogo de **Dados Azure.**

## <a name="key-concepts"></a>Conceitos-chave
O modelo conceptual do Catálogo de **Dados Azure** baseia-se em quatro conceitos-chave: **Catálogo,** **Utilizadores,** **Ativos**e **Anotações.**

![Ilustração do modelo conceptual do catálogo de dados azure](./media/data-catalog-developer-concepts/concept2.png)

*Figura 1 - Modelo conceptual simplificado do Catálogo de Dados do Azure*

### <a name="catalog"></a>Catálogo
Um **Catálogo** é o recipiente de alto nível para todos os metadados que uma organização armazena. Existe um **Catálogo** permitido por Conta Azure. Os catálogos estão ligados a uma subscrição do Azure, mas apenas um **Catálogo** pode ser criado para qualquer conta Azure, mesmo que uma conta possa ter várias subscrições.

Um catálogo contém **Utilizadores** e **Ativos.**

### <a name="users"></a>Utilizadores
Os utilizadores são diretores de segurança que têm permissões para executar ações (pesquisar o catálogo, adicionar, editar ou remover itens, etc.) no Catálogo.

Existem várias funções diferentes que um utilizador pode ter. Para obter informações sobre funções, consulte a secção Funções e Autorização.

Podem ser adicionados utilizadores individuais e grupos de segurança.

O Azure Data Catalog utiliza o Diretório Ativo Azure para gestão de identidade e acesso. Cada utilizador do Catálogo deve ser membro do Diretório Ativo para a conta.

### <a name="assets"></a>Elementos
Um **Catálogo** contém ativos de dados. **Os ativos** são a unidade de granularidade gerida pelo catálogo.

A granularidade de um ativo varia consocada por fonte de dados. Para o SQL Server ou oracle Database, um ativo pode ser uma Tabela ou uma Vista. Para os Serviços de Análise de Servidores SQL, um ativo pode ser uma Medida, uma Dimensão ou um Indicador de Desempenho Chave (KPI). Para os Serviços de Reporte de Servidores SQL, um ativo é um Relatório.

Um **Ativo** é a coisa que adiciona ou remove de um Catálogo. É a unidade de resultado que obtém de volta da **Search**.

Um **Ativo** é composto pelo seu nome, localização e tipo, e anotações que o descrevem ainda mais.

### <a name="annotations"></a>Anotações
Anotações são itens que representam metadados sobre Ativos.

Exemplos de anotações são descrição, etiquetas, esquema, documentação, etc. Uma lista completa dos tipos de ativos e tipos de anotação estão na secção do modelo Do Objeto de Ativos.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Anotações crowdsourcing e perspetiva do utilizador (multiplicidade de opinião)
Um aspeto chave do Catálogo de Dados Azure é a forma como suporta o crowdsourcing de metadados no sistema. Ao contrário de uma abordagem wiki – onde há apenas uma opinião e o último escritor ganha –, o modelo do Catálogo de Dados Azure permite que múltiplas opiniões vivam lado a lado no sistema.

Esta abordagem reflete o mundo real dos dados empresariais onde diferentes utilizadores podem ter diferentes perspetivas sobre um determinado ativo:

* Um administrador de base de dados pode fornecer informações sobre acordos de nível de serviço, ou a janela de processamento disponível para operações ETL a granel
* Um gestor de dados pode fornecer informações sobre os processos de negócio a que o ativo se aplica, ou as classificações que a empresa lhe aplicou
* Um analista financeiro pode fornecer informações sobre como os dados são usados durante tarefas de reporte de fim de período

Para suportar este exemplo, cada utilizador – o DBA, o administrador de dados e o analista – pode adicionar uma descrição a uma única tabela que tenha sido registada no Catálogo. Todas as descrições são mantidas no sistema e no portal Do Catálogo de Dados Azure todas as descrições são apresentadas.

Este padrão é aplicado na maioria dos itens no modelo do objeto, por isso os tipos de objetos na carga útil JSON são frequentemente matrizes para propriedades onde você pode esperar um singleton.

Por exemplo, sob a raiz do ativo está uma série de objetos de descrição. A propriedade da matriz chama-se "descrições". Um objeto de descrição tem uma propriedade - descrição. O padrão é que cada utilizador que typedescrição obtém um objeto de descrição criado para o valor fornecido pelo utilizador.

O UX pode então escolher como exibir a combinação. Existem três padrões diferentes para exibição.

* O padrão mais simples é "Show All". Neste padrão, todos os objetos são mostrados numa vista de lista. O portal de catálogo de dados Azure UX utiliza este padrão para descrição.
* Outro padrão é "Fusão". Neste padrão, todos os valores dos diferentes utilizadores são fundidos em conjunto, com duplicado removido. Exemplos deste padrão no portal De Catálogo de Dados Azure UX são as propriedades de tags e especialistas.
* Um terceiro padrão é "o último escritor ganha". Neste padrão, apenas é mostrado o valor mais recente digitado. friendlyName é um exemplo deste padrão.

## <a name="asset-object-model"></a>Modelo de objeto de ativo
Tal como introduzido na secção Conceitos Chave, o modelo de objetos do Catálogo de **Dados Azure** inclui itens, que podem ser ativos ou anotações. Os itens têm propriedades, que podem ser opcionais ou necessárias. Algumas propriedades aplicam-se a todos os itens. Algumas propriedades aplicam-se a todos os ativos. Algumas propriedades aplicam-se apenas a tipos de ativos específicos.

### <a name="system-properties"></a>Propriedades do sistema
<table><tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr><tr><td>carimbo de data/hora</td><td>DateTime</td><td>A última vez que o artigo foi modificado. Este campo é gerado pelo servidor quando um item é inserido e cada vez que um item é atualizado. O valor deste imóvel é ignorado na entrada de operações editoriais.</td></tr><tr><td>ID</td><td>Uri</td><td>Url absoluto do item (apenas leitura). É o URI único endereçado para o item.  O valor deste imóvel é ignorado na entrada de operações editoriais.</td></tr><tr><td>tipo</td><td>String</td><td>O tipo do ativo (apenas para leitura).</td></tr><tr><td>etag</td><td>String</td><td>Uma cadeia correspondente à versão do item que pode ser usada para um controlo de moeda otimista ao executar operações que atualizam itens no catálogo. "*" pode ser usado para corresponder a qualquer valor.</td></tr></table>

### <a name="common-properties"></a>Common properties (Propriedades comuns)
Estas propriedades aplicam-se a todos os tipos de ativos radiculares e a todos os tipos de anotação.

<table>
<tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleano</td><td>Indica se os dados do item são derivados de um sistema de origem (como a Base de Dados do Servidor Sql, Base de Dados Oracle) ou da autoria de um utilizador.</td></tr>
</table>

### <a name="common-root-properties"></a>Propriedades de raiz comuns
<p>
Estas propriedades aplicam-se a todos os tipos de ativos radiculares.

<table><tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr><tr><td>nome</td><td>String</td><td>Um nome derivado das informações de localização de fonte de dados</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>De forma única, descreve a fonte de dados e é um dos identificadores para o ativo. (Ver secção de identidade dupla).  A estrutura do dsl varia pelo protocolo e tipo de origem.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Mais detalhes sobre o tipo de ativo.</td></tr><tr><td>últimoRegistadoBy</td><td>Diretor de Segurança</td><td>Descreve o utilizador que mais recentemente registou este ativo.  Contém tanto o id único para o utilizador (o upn) como um nome de exibição (apelido e primeiro nome).</td></tr><tr><td>recipienteId</td><td>String</td><td>Identificação do ativo do contentor para a fonte de dados. Esta propriedade não é suportada para o tipo contentor.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Propriedades comuns de anotação não singleton
Estas propriedades aplicam-se a todos os tipos de anotação não singleton (anotações, que permitiram ser múltiplas por ativo).

<table>
<tr><td><b>Nome da Propriedade</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>key</td><td>String</td><td>Uma chave especificada pelo utilizador, que identifica exclusivamente a anotação na coleção atual. O comprimento da chave não pode exceder 256 caracteres.</td></tr>
</table>

### <a name="root-asset-types"></a>Tipos de ativos de raiz
Os tipos de ativos de raiz são os tipos que representam os vários tipos de ativos de dados que podem ser registados no catálogo. Para cada tipo de raiz, existe uma vista, que descreve ativos e anotações incluídas na vista. O nome da visualização deve ser utilizado no segmento de url correspondente {view_name} ao publicar um ativo utilizando a API REST.

<table><tr><td><b>Tipo de ativo (Nome de ver)</b></td><td><b>Propriedades Adicionais</b></td><td><b>Tipo de Dados</b></td><td><b>Anotações Permitidas</b></td><td><b>Comentários</b></td></tr><tr><td>Tabela ("mesas")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Esquema<p>Descrição da coluna<p>ColunaTag<p> Especialista<p>Pré-visualização<p>Instrução de Acesso<p>Perfil de dados de tabela<p>Perfil de Dados de Colunas<p>Classificação de Dados de Colunas<p>Documentação<p></td><td>Uma tabela representa qualquer dado tabular.  Por exemplo: Tabela SQL, Vista SQL, Tabela Tabular dos Serviços de Análise, Dimensão Multidimensional dos Serviços de Análise, Tabela Oracle, etc.   </td></tr><tr><td>Medida ("medidas")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>Instrução de Acesso<p>Documentação<p></td><td>Este tipo representa uma medida de Serviços de Análise.</td></tr><tr><td></td><td>medida</td><td>Coluna</td><td></td><td>Metadados descrevendo a medida</td></tr><tr><td></td><td>é Calculado </td><td>Booleano</td><td></td><td>Especifica se a medida é calculada ou não.</td></tr><tr><td></td><td>medidaGrupo</td><td>String</td><td></td><td>Recipiente físico para medida</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>Instrução de Acesso<p>Documentação</td><td></td></tr><tr><td></td><td>medidaGrupo</td><td>String</td><td></td><td>Recipiente físico para medida</td></tr><tr><td></td><td>objetivoExpressão</td><td>String</td><td></td><td>Uma expressão numérica MDX ou um cálculo que retorna o valor-alvo do KPI.</td></tr><tr><td></td><td>valorExpressão</td><td>String</td><td></td><td>Uma expressão numérica MDX que devolve o valor real do KPI.</td></tr><tr><td></td><td>estadoExpressão</td><td>String</td><td></td><td>Uma expressão MDX que representa o estado do KPI num determinado momento.</td></tr><tr><td></td><td>tendênciasExpressão</td><td>String</td><td></td><td>Uma expressão MDX que avalia o valor do KPI ao longo do tempo. A tendência pode ser qualquer critério baseado no tempo que seja útil num contexto de negócio específico.</td>
<tr><td>Relatório ("relatórios")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>Instrução de Acesso<p>Documentação<p></td><td>Este tipo representa um relatório de Serviços de Reporte de Servidores SQL </td></tr><tr><td></td><td>activoCreatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>activoCreatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>activoData Modificada</td><td>String</td><td></td><td></td></tr><tr><td></td><td>activoModificadoBy</td><td>String</td><td></td><td></td></tr><tr><td>Recipiente ("contentores")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>Instrução de Acesso<p>Documentação<p></td><td>Este tipo representa um contentor de outros ativos, como uma base de dados SQL, um recipiente Azure Blobs ou um modelo de Serviços de Análise.</td></tr></table>

### <a name="annotation-types"></a>Tipos de anotação
Os tipos de anotação representam tipos de metadados que podem ser atribuídos a outros tipos dentro do catálogo.

<table>
<tr><td><b>Tipo de anotação (nome de visão aninhada)</b></td><td><b>Propriedades Adicionais</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>Descrição ("descrições")</td><td></td><td></td><td>Esta propriedade contém uma descrição para um ativo. Cada utilizador do sistema pode adicionar a sua própria descrição.  Só esse utilizador pode editar o objeto Descrição.  (Os proprietários de administradores e ativos podem eliminar o objeto Descrição, mas não editá-lo). O sistema mantém as descrições dos utilizadores separadamente.  Assim, existe uma série de descrições sobre cada ativo (uma para cada utilizador que tenha contribuído com o seu conhecimento sobre o ativo, além de possivelmente uma que contenha informações derivadas da fonte de dados).</td></tr>
<tr><td></td><td>descrição</td><td>string</td><td>Uma descrição curta (2-3 linhas) do ativo</td></tr>

<tr><td>Etiqueta ("tags")</td><td></td><td></td><td>Esta propriedade define uma etiqueta para um ativo. Cada utilizador do sistema pode adicionar várias tags para um ativo.  Só o utilizador que criou objetos Tag pode editá-los.  (Os proprietários de administradores e ativos podem eliminar o objeto Tag, mas não editá-lo). O sistema mantém as etiquetas dos utilizadores separadamente.  Assim, há uma variedade de objetos Tag em cada ativo.</td></tr>
<tr><td></td><td>etiqueta</td><td>string</td><td>Uma etiqueta descrevendo o ativo.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Esta propriedade contém um nome amigável para um ativo. FriendlyName é uma anotação singleton - apenas um Nome Amigável pode ser adicionado a um ativo.  Só o utilizador que criou o objeto FriendlyName pode editá-lo. (Os proprietários de administradores e ativos podem eliminar o objeto FriendlyName, mas não editá-lo). O sistema mantém os nomes amigáveis dos utilizadores separadamente.</td></tr>
<tr><td></td><td>nome amigável</td><td>string</td><td>Um nome amigável do ativo.</td></tr>

<tr><td>Schema ("esquema")</td><td></td><td></td><td>O Schema descreve a estrutura dos dados.  Ele lista os nomes do atributo (coluna, atributo, campo, etc.) e outros metadados.  Esta informação é toda derivada da fonte de dados.  Schema é uma anotação singleton - apenas um Schema pode ser adicionado para um ativo.</td></tr>
<tr><td></td><td>colunas</td><td>Coluna[]</td><td>Uma variedade de objetos de coluna. Descrevem a coluna com informações derivadas da fonte de dados.</td></tr>

<tr><td>Descrição da coluna ("descrições das colunas")</td><td></td><td></td><td>Esta propriedade contém uma descrição para uma coluna.  Cada utilizador do sistema pode adicionar as suas próprias descrições para várias colunas (no máximo uma por coluna). Só o utilizador que criou objetos ColumnDescription pode editá-los.  (Os proprietários de administradores e ativos podem eliminar o objeto ColumnDescription, mas não editá-lo). O sistema mantém separadamente as descrições da coluna deste utilizador.  Assim, existe um conjunto de objetos ColumnDescription em cada ativo (um por coluna para cada utilizador que tenha contribuído com o seu conhecimento sobre a coluna, além de possivelmente um que contenha informações derivadas da fonte de dados).  A ColumnDescription está vagamente ligada ao Schema para que possa ficar dessincronizada. A ColumnDescription pode descrever uma coluna que já não existe no esquema.  Cabe ao escritor manter a descrição e o esquema sincronizados.  A fonte de dados também pode ter informações de descrição de colunas e são objetos de Descrição coluna adicionais que seriam criados durante a execução da ferramenta.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna a que se refere esta descrição.</td></tr>
<tr><td></td><td>descrição</td><td>String</td><td>uma descrição curta (2-3 linhas) da coluna.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Esta propriedade contém uma etiqueta para uma coluna. Cada utilizador do sistema pode adicionar várias etiquetas para uma determinada coluna e pode adicionar tags para várias colunas. Só o utilizador que criou objetos ColumnTag pode editá-los. (Os proprietários de administradores e ativos podem eliminar o objeto ColumnTag, mas não editá-lo). O sistema mantém as etiquetas de coluna destes utilizadores separadamente.  Assim, há uma variedade de objetos ColumnTag em cada ativo.  O ColumnTag está vagamente ligado ao esquema para que possa ficar dessincronizado. O ColumnTag pode descrever uma coluna que já não existe no esquema.  Cabe ao escritor manter a etiqueta da coluna e o esquema sincronizados.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna a que se refere esta etiqueta.</td></tr>
<tr><td></td><td>etiqueta</td><td>String</td><td>Uma etiqueta descrevendo a coluna.</td></tr>

<tr><td>Especialista ("peritos")</td><td></td><td></td><td>Esta propriedade contém um utilizador que é considerado um perito no conjunto de dados. As opiniões (descrições) dos peritos bolham no topo do UX ao enumerar descrições. Cada utilizador pode especificar os seus próprios especialistas. Só esse utilizador pode editar o objeto dos especialistas. (Os proprietários de administradores e ativos podem eliminar os objetos Expert, mas não editá-lo).</td></tr>
<tr><td></td><td>especialista</td><td>Diretor de Segurança</td><td></td></tr>

<tr><td>Pré-visualização ("pré-visualizações")</td><td></td><td></td><td>A pré-visualização contém uma imagem das 20 melhores linhas de dados para o ativo. A pré-visualização só faz sentido para alguns tipos de ativos (faz sentido para a Tabela, mas não para a Medida).</td></tr>
<tr><td></td><td>preview</td><td>objeto[]</td><td>Uma série de objetos que representam uma coluna.  Cada objeto tem uma propriedade mapeando para uma coluna com um valor para essa coluna para a linha.</td></tr>

<tr><td>Instrução de acesso ("instruções de acesso")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>O tipo de mímica do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>string</td><td>As instruções para como obter acesso a este ativo de dados. O conteúdo pode ser um URL, um endereço de e-mail ou um conjunto de instruções.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>númeroOfRows</td></td><td>int</td><td>O número de linhas no conjunto de dados</td></tr>
<tr><td></td><td>size</td><td>long</td><td>O tamanho em bytes do conjunto de dados.  </td></tr>
<tr><td></td><td>schemaTempo modificado</td><td>string</td><td>A última vez que o esquema foi modificado</td></tr>
<tr><td></td><td>dadosTempo modificado</td><td>string</td><td>A última vez que o conjunto de dados foi modificado (os dados foram adicionados, modificados ou excluídos)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>colunas</td></td><td>ColumnDataProfile[]</td><td>Uma série de perfis de dados de colunas.</td></tr>

<tr><td>Classificação de Dados de Colunas ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna a que se refere esta classificação.</td></tr>
<tr><td></td><td>classificação</td><td>String</td><td>A classificação dos dados nesta coluna.</td></tr>

<tr><td>Documentação ("documentação")</td><td></td><td></td><td>Um dado ativo só pode ter uma documentação associada a ele.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>O tipo de mímica do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>string</td><td>O conteúdo da documentação.</td></tr>

</table>

### <a name="common-types"></a>Tipos comuns
Os tipos comuns podem ser usados como tipos de propriedades, mas não são itens.

<table>
<tr><td><b>Tipo comum</b></td><td><b>Propriedades</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>fonteTipo</td><td>string</td><td>Descreve o tipo de fonte de dados.  Por exemplo: SQL Server, Oracle Database, etc.  </td></tr>
<tr><td></td><td>objetoTipo</td><td>string</td><td>Descreve o tipo de objeto na fonte de dados. Por exemplo: Tabela, Vista para servidor SQL.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocolo</td><td>string</td><td>Necessário. Descreve um protocolo usado para comunicar com a fonte de dados. Por exemplo: "tds" para SQl Server, "oráculo" para oOráculo, etc. Consulte a especificação de referência de <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">origem de dados - Estrutura DSL</a> para a lista de protocolos atualmente suportados.</td></tr>
<tr><td></td><td>address</td><td>Corda&lt;do dicionário, objeto&gt;</td><td>Necessário. O endereço é um conjunto de dados específicos do protocolo que é utilizado para identificar a origem de dados que está a ser referenciada. Os dados de endereços âmbitoados a um determinado protocolo, o que significa que não tem sentido sem conhecer o protocolo.</td></tr>
<tr><td></td><td>autenticação</td><td>string</td><td>Opcional. O regime de autenticação utilizado para comunicar com a fonte de dados. Por exemplo: janelas, oauth, etc.</td></tr>
<tr><td></td><td>conexãoPropriedades</td><td>Corda&lt;do dicionário, objeto&gt;</td><td>Opcional. Informações adicionais sobre como se conectar a uma fonte de dados.</td></tr>

<tr><td>Diretor de Segurança</td><td></td><td></td><td>O backend não realiza qualquer validação das propriedades fornecidas contra a AAD durante a publicação.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Endereço de e-mail único do utilizador. Deve ser especificado se o objectId não for fornecido ou no contexto da propriedade "lastRegisteredBy", caso contrário opcional.</td></tr>
<tr><td></td><td>objectId</td><td>GUID</td><td>Identidade AAD do grupo de utilizador ou grupo de segurança. Opcional. Deve ser especificado se não for fornecido upn, caso contrário opcional.</td></tr>
<tr><td></td><td>nomePróprio</td><td>string</td><td>Primeiro nome do utilizador (para efeitos de exibição). Opcional. Apenas válido no contexto da propriedade "lastRegisteredBy". Não é possível especificar no fornecimento de diretor de segurança para "funções", "permissões" e "peritos".</td></tr>
<tr><td></td><td>apelido</td><td>string</td><td>Último nome do utilizador (para efeitos de exibição). Opcional. Apenas válido no contexto da propriedade "lastRegisteredBy". Não é possível especificar no fornecimento de diretor de segurança para "funções", "permissões" e "peritos".</td></tr>

<tr><td>Coluna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>string</td><td>Nome da coluna ou atributo.</td></tr>
<tr><td></td><td>tipo</td><td>string</td><td>tipo de dados da coluna ou atributo. Os tipos admissíveis dependem da fonte de dadosTipo do ativo.  Apenas um subconjunto de tipos é suportado.</td></tr>
<tr><td></td><td>comprimento máximo</td><td>int</td><td>O comprimento máximo permitido para a coluna ou atributo. Derivado da fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>precisão</td><td>byte</td><td>A precisão para a coluna ou atributo. Derivado da fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>é nullable</td><td>Booleano</td><td>Se a coluna pode ter ou não um valor nulo. Derivado da fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>expression</td><td>string</td><td>Se o valor for uma coluna calculada, este campo contém a expressão que expressa o valor. Derivado da fonte de dados. Apenas aplicável a alguns tipos de origem.</td></tr>

<tr><td>Perfil de Dados de Colunas</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>O nome da coluna</td></tr>
<tr><td></td><td>tipo </td><td>string</td><td>O tipo de coluna</td></tr>
<tr><td></td><td>min </td><td>string</td><td>O valor mínimo no conjunto de dados</td></tr>
<tr><td></td><td>máximo </td><td>string</td><td>O valor máximo no conjunto de dados</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>O valor médio no conjunto de dados</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>O desvio padrão para o conjunto de dados</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>Contagem de valores nulos no conjunto de dados</td></tr>
<tr><td></td><td>distintoCount  </td><td>int</td><td>A contagem de valores distintos no conjunto de dados</td></tr>


</table>

## <a name="asset-identity"></a>Identidade de ativo
O Azure Data Catalog utiliza propriedades de "protocolo" e identidade do saco de propriedade "address" da propriedade DataSourceLocation "dsl" para gerar identidade do ativo, que é usado para abordar o ativo dentro do Catálogo.
Por exemplo, o protocolo "tds" tem propriedades identitárias "servidor", "base de dados", "esquema" e "objeto". As combinações do protocolo e das propriedades identitárias são usadas para gerar a identidade do SQL Server Table Asset.
O Azure Data Catalog fornece vários protocolos de origem de dados incorporados, que estão listados na especificação de referência de [origem de dados - Estrutura DSL](data-catalog-dsr.md).
O conjunto de protocolos suportados pode ser alargado programáticamente (Consulte a referência rest API do Catálogo de Dados). Os administradores do Catálogo podem registar protocolos de origem de dados personalizados. A tabela seguinte descreve as propriedades necessárias para registar um protocolo personalizado.

### <a name="custom-data-source-protocol-specification"></a>Especificação personalizada do protocolo de origem de dados
<table>
<tr><td><b>Tipo</b></td><td><b>Propriedades</b></td><td><b>Tipo de Dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>espaço de nomes</td><td>string</td><td>O espaço de nome do protocolo. O espaço de nome deve ter entre 1 a 255 caracteres de comprimento, conter uma ou mais peças não vazias separadas por ponto (.). Cada peça deve ter 1 a 255 caracteres de comprimento, começar com uma letra e conter apenas letras e números.</td></tr>
<tr><td></td><td>nome</td><td>string</td><td>O nome do protocolo. O nome deve ter de 1 a 255 caracteres de comprimento, comece com uma letra e contenha apenas letras, números e o caráter (-) do traço.</td></tr>
<tr><td></td><td>identidadePropriedades</td><td>DataSourceProtocolIdentityProperty[]</td><td>A lista de propriedades identitárias deve conter pelo menos uma, mas não mais de 20 propriedades. Por exemplo: "servidor", "base de dados", "esquema", "objeto" são propriedades identitárias do protocolo "tds".</td></tr>
<tr><td></td><td>identidadeConjuntos</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista de conjuntos de identidade. Define conjuntos de propriedades identitárias, que representam a identidade do ativo válido. Deve conter pelo menos um, mas não mais de 20 conjuntos. Por exemplo: {"server", "database", "schema" e "object"} é um conjunto de identidade para o protocolo "tds", que define a identidade do ativo da Tabela sql Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>string</td><td>O nome da propriedade. O nome deve ter de 1 a 100 caracteres de comprimento, comece com uma letra e possa conter apenas letras e números.</td></tr>
<tr><td></td><td>tipo</td><td>string</td><td>O tipo de propriedade. Valores suportados: "bool", booleano, "byte", "guid", "int", "inteiro", "longo", "corda", "url"</td></tr>
<tr><td></td><td>ignorarCaso</td><td>bool</td><td>Indica se o caso deve ser ignorado quando se utiliza o valor da propriedade. Só pode ser especificado para propriedades com tipo "string". O valor predefinido é falso.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Indica se o caso deve ser ignorado para cada segmento do caminho do url. Só pode ser especificado para propriedades com tipo "url". O valor predefinido é [falso].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>string</td><td>O nome do conjunto de identidade.</td></tr>
<tr><td></td><td>propriedades</td><td>cadeia[]</td><td>A lista de propriedades identitárias incluídas neste conjunto de identidades. Não pode conter duplicados. Cada imóvel referenciado por conjunto de identidade deve ser definido na lista de "identidadePropriedades" do protocolo.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Funções e autorização
O Microsoft Azure Data Catalog fornece capacidades de autorização para operações de CRUD em ativos e anotações.

## <a name="key-concepts"></a>Conceitos-chave
O Catálogo de Dados Azure utiliza dois mecanismos de autorização:

* Autorização baseada em papéis
* Autorização baseada em permissão

### <a name="roles"></a>Funções
Existem três funções: **Administrador,** **Proprietário**e **Contribuinte.**  Cada papel tem o seu âmbito e direitos, que são resumidos na tabela seguinte.

<table><tr><td><b>Função</b></td><td><b>Âmbito</b></td><td><b>Direitos</b></td></tr><tr><td>Administrador</td><td>Catálogo (todos os ativos/anotações no Catálogo)</td><td>Ler Excluir ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietário</td><td>Cada ativo (item raiz)</td><td>Ler Excluir ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contribuinte</td><td>Cada ativo e anotação individual</td><td>Ler Atualização Eliminar ViewRoles Nota: todos os direitos são revogados se o Direito de Leitura no item for revogado do Contribuinte</td></tr></table>

> [!NOTE]
> **Ler**, **Atualizar**, **Excluir**, **VerRoles** os direitos são aplicáveis a qualquer item (ativo ou anotação) enquanto **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** são apenas aplicáveis ao ativo raiz.
> 
> **Excluir** o direito aplica-se a um item e quaisquer subitems ou itens únicos por baixo. Por exemplo, a eliminação de um ativo também elimina quaisquer anotações para esse ativo.
> 
> 

### <a name="permissions"></a>Permissões
A permissão é como lista de entradas de controlo de acesso. Cada entrada de controlo de acesso atribui um conjunto de direitos a um diretor de segurança. As permissões só podem ser especificadas num ativo (isto é, item raiz) e aplicam-se ao ativo e a quaisquer subitems.

Durante a pré-visualização do Catálogo de **Dados Do Azure,** apenas **o Direito de Leitura** é suportado na lista de permissões para permitir o cenário de restringir a visibilidade de um ativo.

Por predefinição, qualquer utilizador autenticado tem **lido** corretamente para qualquer item do catálogo, a menos que a visibilidade seja restrita ao conjunto de diretores nas permissões.

## <a name="rest-api"></a>API REST
**Os** pedidos de artigos de visualização PUT e **POST** podem ser utilizados para controlar funções e permissões: além da carga útil do item, duas propriedades do sistema podem ser especificadas **funções** e **permissões**.

> [!NOTE]
> **permissões** aplicáveis apenas a um item de raiz.
> 
> **A** função do proprietário só aplicável a um item de raiz.
> 
> Por predefinição, quando um item é criado no catálogo, o seu **Colaborador** está definido para o utilizador autenticado atualmente. Se o item deve ser atualizável por todos, **o Contribuinte** deve ser definido &lt;para todos os&gt; principais de segurança especiais na propriedade de **funções** quando o item é publicado pela primeira vez (consulte o seguinte exemplo). **O contribuinte** não pode ser alterado e permanece o mesmo durante a vida útil de um item (mesmo **administrador** ou **proprietário** não tem o direito de alterar o **Contribuinte).** O único valor suportado para a definição explícita do **Contribuinte** &lt;é Todos&gt;: &lt;&gt; **Contribuinte** só pode ser um utilizador que criou um item ou todos .
> 
> 

### <a name="examples"></a>Exemplos
**Desloque o Colaborador a &lt;Todos&gt; ao publicar um artigo.**
Diretor de &lt;&gt; segurança especial Todos têm objectid "00000000-0000-0000-0000-0000-0000000000000000000000000000000201".
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Algumas implementações de clientes HTTP podem reeditar automaticamente pedidos em resposta a um 302 do servidor, mas normalmente retiram os cabeçalhos de autorização do pedido. Uma vez que o cabeçalho de Autorização é necessário para fazer pedidos ao Catálogo de Dados Azure, deve garantir que o cabeçalho de Autorização ainda é fornecido ao reemitir um pedido para um local de redirecionamento especificado pelo Catálogo de Dados do Azure. O seguinte código de amostra demonstra-o utilizando o objeto .NET HttpWebRequest.
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

  **Atribuir proprietários e restringir a visibilidade para um item raiz existente**: **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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
> No PUT não é necessário especificar uma carga útil do item no corpo: PUT pode ser usado para atualizar apenas papéis e/ou permissões.
> 
