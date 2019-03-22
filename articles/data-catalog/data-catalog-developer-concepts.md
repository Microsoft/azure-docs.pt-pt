---
title: Conceitos de programador do catálogo de dados do Azure
description: Introdução a conceitos chave no modelo conceitual do catálogo de dados do Azure, como expostos através da API de REST do catálogo.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: bca006ab33379f52281f77fb5a04a24022bac373
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314558"
---
# <a name="azure-data-catalog-developer-concepts"></a>Conceitos de programador do catálogo de dados do Azure
Microsoft **catálogo de dados do Azure** é um serviço cloud totalmente gerido que fornece capacidades para deteção de origens de dados e para crowdsourcing de metadados de origem de dados. Os programadores podem utilizar o serviço através de suas APIs de REST. Compreender os conceitos implementados no serviço é importante para os programadores integrar com êxito **catálogo de dados do Azure**.

## <a name="key-concepts"></a>Conceitos-chave
O **catálogo de dados do Azure** modelo conceitual é baseado em quatro conceitos-chave: O **catálogo**, **utilizadores**, **ativos**, e **anotações**.

![Conceito][1]

*Figura 1 – modelo conceitual simplificado de catálogo de dados do Azure*

### <a name="catalog"></a>catálogo
R **catálogo** é o contentor de nível superior para todos os metadados de uma organização armazena. Há um **catálogo** permitidas por conta do Azure. Catálogos estão associados a uma subscrição do Azure, mas apenas um **catálogo** podem ser criados para qualquer determinada conta do Azure, mesmo que uma conta pode ter várias subscrições.

Contém um catálogo **usuários** e **ativos**.

### <a name="users"></a>Utilizadores
Os utilizadores são entidades de segurança que têm permissão para efetuar ações (pesquisar o catálogo, adicionar, editar ou remover itens, etc.) no catálogo.

Existem várias funções diferentes de que um utilizador pode ter. Para informações sobre as funções, consulte a secção funções e a autorização.

Utilizadores individuais e grupos de segurança podem ser adicionados.

O catálogo de dados do Azure utiliza o Azure Active Directory para gestão de identidades e acesso. Cada utilizador do catálogo de tem de ser membro do Active Directory para a conta.

### <a name="assets"></a>Elementos
R **catálogo** contém recursos de dados. **Ativos** são a unidade de granularidade gerenciada pelo catálogo.

A granularidade de um ativo varia consoante a origem de dados. Para o SQL Server ou Oracle Database, um recurso pode ser uma tabela ou numa vista. Para o SQL Server Analysis Services, um recurso pode ser uma medida, uma dimensão ou um indicador chave de desempenho (KPI). Para o SQL Server Reporting Services, um recurso é um relatório.

Uma **Asset** é o que adicionar ou remover de um catálogo. É a unidade do resultado que é obtido da **pesquisa**.

Uma **Asset** é feita partir do seu nome, a localização e o tipo e a anotações que descrevem melhor.

### <a name="annotations"></a>anotações
Anotações são itens que representam os metadados sobre os recursos.

Exemplos de anotações são descrição, etiquetas, esquema, documentação, etc. Uma lista completa dos tipos de recurso e tipos de anotação estão na secção de modelo de objeto de ativo.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Anotações de Crowdsourcing e ponto de vista do usuário (multiplicidade da opinião)
Um aspecto fundamental do catálogo de dados do Azure é como ele suporta o crowdsourcing de metadados no sistema. Vez para uma abordagem de wiki – em que existe apenas uma opinião e o último wins de escritor – o modelo de catálogo de dados do Azure permite várias opiniões TTL lado a lado no sistema.

Essa abordagem reflete o mundo real dos dados empresariais em que diferentes usuários podem ter diferentes perspectivas num determinado recurso:

* Um administrador de banco de dados pode fornecer informações sobre contratos de nível de serviço ou a janela de processamento disponível para operações de ETL em massa
* Uma responsável pelos dados pode fornecer informações sobre os processos de negócio a que se aplique o elemento ou as classificações que tem aplicada a empresa
* Um analista financeiro pode fornecer informações sobre como os dados são utilizados durante tarefas de criação de relatórios do fim do período

Para suportar este exemplo, cada utilizador, o DBA, o responsável pelos dados e o analista, pode adicionar uma descrição para uma única tabela que foi registrada no catálogo. Todas as descrições são mantidas no sistema e, no portal do catálogo de dados do Azure todas as descrições são apresentadas.

Este padrão é aplicado à maioria dos itens no modelo de objeto, portanto, tipos de objetos no payload de JSON são, muitas vezes, as matrizes de propriedades onde pode esperar um singleton.

Por exemplo, sob o elemento raiz é uma matriz de objetos de descrição. A propriedade de matriz com o nome "descrições". Um objeto de descrição tem uma propriedade - descrição. O padrão é que cada utilizador que a descrição de tipos obtém um objeto de descrição criado para o valor fornecido pelo utilizador.

A experiência do Usuário, em seguida, pode escolher como exibir a combinação. Existem três padrões diferentes para exibição.

* O padrão mais simples é "Mostrar tudo". Neste padrão, todos os objetos são apresentados numa vista de lista. O portal do catálogo de dados do Azure UX usa esse padrão para a descrição.
* Outro padrão é "Mesclar". Neste padrão, todos os valores de diferentes usuários são mesclados em conjunto, com os duplicados removidos. Os exemplos desse padrão no portal do catálogo de dados do Azure UX são as propriedades de marcas e especialistas.
* Um terceiro padrão é "último wins de escritor". Neste padrão, é mostrado apenas o valor mais recente de digitar. friendlyName é um exemplo desse padrão.

## <a name="asset-object-model"></a>Modelo de objeto do recurso
Como a apresentada na secção conceitos chave, o **catálogo de dados do Azure** modelo de objeto inclui itens, que podem ser ativos ou anotações. Itens de ter propriedades, que podem ser obrigatório ou opcional. Algumas propriedades aplicam-se a todos os itens. Algumas propriedades que se aplicam a todos os recursos. Algumas propriedades aplicam-se apenas aos tipos de recurso específico.

### <a name="system-properties"></a>Propriedades do sistema
<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>carimbo de data/hora</td><td>DateTime</td><td>A última vez em que o item foi modificado. Este campo é gerado pelo servidor quando um item é inserido e sempre que um item é atualizado. O valor desta propriedade é ignorado na entrada de operações de publicação.</td></tr><tr><td>ID</td><td>URI</td><td>Url absoluto do item (só de leitura). É o URI endereçável exclusivo para o item.  O valor desta propriedade é ignorado na entrada de operações de publicação.</td></tr><tr><td>tipo</td><td>String</td><td>O tipo de recurso (só de leitura).</td></tr><tr><td>etag</td><td>String</td><td>Uma cadeia de caracteres correspondente para a versão do item que pode ser utilizado para controlo de simultaneidade otimista ao realizar operações que atualizam itens no catálogo. "*" pode ser utilizado para corresponder qualquer valor.</td></tr></table>

### <a name="common-properties"></a>Propriedades comuns
Estas propriedades aplicam-se a todos os tipos de recurso de raiz e todos os tipos de anotação.

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>fromSourceSystem</td><td>Booleano</td><td>Indica se os dados do item são derivados de um sistema de origem (como o Sql Server Database, Oracle Database) ou criados por um utilizador.</td></tr>
</table>

### <a name="common-root-properties"></a>Propriedades comuns de raiz
<p>
Estas propriedades aplicam-se a todos os tipos de recurso de raiz.

<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>nome</td><td>String</td><td>Um nome derivado das informações de localização de origem de dados</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>Exclusivamente descreve a origem de dados e é um dos identificadores para o elemento. (Consulte a seção de identidade dupla).  A estrutura da dsl varia consoante o tipo de protocolo e a origem.</td></tr><tr><td>dataSource</td><td>DataSourceInfo</td><td>Obter mais detalhes sobre o tipo de recurso.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descreve o utilizador que mais recentemente registados deste recurso.  Contém o id exclusivo para o utilizador (upn) e um nome a apresentar (lastName e firstName).</td></tr><tr><td>containerId</td><td>String</td><td>ID do recurso de contentor para a origem de dados. Esta propriedade não é suportada para o tipo de contentor.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Propriedades comuns de anotação não singleton
Estas propriedades aplicam-se a todos os tipos de anotação não singleton (anotações, que podem para ser vários por ativo).

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>key</td><td>String</td><td>Um utilizador especificado chave, que identifica exclusivamente a anotação da coleção atual. O comprimento da chave não pode exceder os 256 carateres.</td></tr>
</table>

### <a name="root-asset-types"></a>Tipos de recurso de raiz
Tipos de recurso de raiz são os tipos que representam os vários tipos de recursos de dados que podem ser registados no catálogo. Para cada tipo de raiz, há uma exibição, que descreve os ativos e anotações incluídas na vista. Nome da vista deve ser usado no segmento de url {view_name} correspondente ao publicar um elemento com a REST API.

<table><tr><td><b>Tipo de recurso (nome de exibição)</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Anotações permitidas</b></td><td><b>Comentários</b></td></tr><tr><td>Tabela ("tabelas")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Esquema<p>ColumnDescription<p>ColumnTag<p> Especialista<p>Pré-visualização<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Documentação<p></td><td>Uma tabela representa dados de tabela.  Por exemplo: Tabela SQL, vista de SQL, tabela Tabular de Analysis Services, Analysis Services Multidimensional de dimensão, tabela do Oracle, etc.   </td></tr><tr><td>Medida ("medidas")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa uma medida do Analysis Services.</td></tr><tr><td></td><td>medida</td><td>Coluna</td><td></td><td>Metadados que descrevem a medida</td></tr><tr><td></td><td>isCalculated </td><td>Booleano</td><td></td><td>Especifica se a medida é calculada ou não.</td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Contentor de físico para a medida</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação</td><td></td></tr><tr><td></td><td>measureGroup</td><td>String</td><td></td><td>Contentor de físico para a medida</td></tr><tr><td></td><td>goalExpression</td><td>String</td><td></td><td>Uma expressão numérica de MDX ou um cálculo que devolve o valor de destino do KPI.</td></tr><tr><td></td><td>valueExpression</td><td>String</td><td></td><td>Uma expressão MDX numérica que retorna o valor real do KPI.</td></tr><tr><td></td><td>statusExpression</td><td>String</td><td></td><td>Uma expressão MDX que representa o estado do KPI num determinado ponto no tempo.</td></tr><tr><td></td><td>trendExpression</td><td>String</td><td></td><td>Uma expressão MDX que avalia o valor do KPI ao longo do tempo. A tendência pode ser qualquer critério com base no tempo que é útil num contexto de negócios específicas.</td>
<tr><td>Relatório ("relatórios")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa um relatório do SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>String</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>String</td><td></td><td></td></tr><tr><td>Contentor ("contentores")</td><td></td><td></td><td>Descrição<p>FriendlyName<p>Etiqueta<p>Especialista<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa um contentor de outros recursos, como uma base de dados SQL, um contentor de Blobs do Azure ou um modelo do Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Tipos de anotação
Tipos de anotação representam os tipos de metadados que podem ser atribuídos a outros tipos no catálogo.

<table>
<tr><td><b>Tipo de anotação (nome da vista aninhados)</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>Descrição ("descrições")</td><td></td><td></td><td>Esta propriedade contém uma descrição para um recurso. Cada utilizador do sistema pode adicionar seus próprios descrição.  Apenas esse utilizador pode editar o objeto de descrição.  (Os proprietários de recursos e os administradores podem eliminar o objeto de descrição, mas não a editá-lo). O sistema mantém as descrições dos utilizadores em separado.  Portanto, é uma matriz de descrições de cada ativo (um para cada utilizador que tem contribuído com seus conhecimentos sobre o elemento, além de, possivelmente, um que contenha informações derivadas da origem de dados).</td></tr>
<tr><td></td><td>descrição</td><td>string</td><td>Uma breve descrição (linhas 2 e 3) do recurso</td></tr>

<tr><td>Etiqueta ("etiquetas")</td><td></td><td></td><td>Esta propriedade define uma etiqueta para um recurso. Cada utilizador do sistema pode adicionar várias etiquetas para um recurso.  Apenas o utilizador que criou objetos de marca pode editá-los.  (Os proprietários de recursos e os administradores podem eliminar o objeto de etiqueta, mas não a editá-lo). O sistema mantém as etiquetas dos utilizadores em separado.  Portanto, é uma matriz de objetos de marca de cada ativo.</td></tr>
<tr><td></td><td>etiqueta</td><td>string</td><td>Uma etiqueta que descrevem o recurso.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Esta propriedade contém um nome amigável para um recurso. FriendlyName é uma anotação de singleton - FriendlyName apenas um pode ser adicionado a um recurso.  Apenas o utilizador que criou o objeto de FriendlyName pode editá-lo. (Os proprietários de recursos e os administradores podem eliminar o objeto de FriendlyName, mas não a editá-lo). O sistema mantém os nomes amigáveis dos utilizadores em separado.</td></tr>
<tr><td></td><td>friendlyName</td><td>string</td><td>Um nome amigável do ativo.</td></tr>

<tr><td>Schema ("schema")</td><td></td><td></td><td>O esquema descreve a estrutura dos dados.  Ele mostra os nomes de atributo (coluna, atributo, campo, etc.), tipos também outros metadados.  Esta informação é derivada da origem de dados.  Esquema é uma anotação de singleton - apenas um esquema pode ser adicionada para um recurso.</td></tr>
<tr><td></td><td>Colunas</td><td>Column[]</td><td>Uma matriz de objetos de coluna. Eles descrevem a coluna com informações derivadas da origem de dados.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Esta propriedade contém uma descrição para uma coluna.  Cada utilizador do sistema pode adicionar suas próprias descrições para várias colunas (no máximo um por coluna). Apenas o utilizador que criou objetos ColumnDescription pode editá-los.  (Os proprietários de recursos e os administradores podem eliminar o objeto de ColumnDescription, mas não a editá-lo). O sistema mantém descrições das colunas destes utilizador em separado.  Portanto, é uma matriz de objetos de ColumnDescription de cada ativo (um por coluna para cada utilizador que tem contribuído com seus conhecimentos sobre a coluna além, possivelmente, um que contenha informações derivadas da origem de dados).  O ColumnDescription livremente está vinculado ao esquema para que ele pode ficar dessincronizado. O ColumnDescription poderá descrever uma coluna que já não existe no esquema.  É até o gravador para manter a descrição e o esquema em sincronia.  A origem de dados também pode ter informações de descrição de colunas e esses são objetos de ColumnDescription adicionais que seriam possível criar quando executar a ferramenta.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna que descrição refere-se a.</td></tr>
<tr><td></td><td>descrição</td><td>String</td><td>uma breve descrição (linhas 2 e 3) da coluna.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Esta propriedade contém uma etiqueta para uma coluna. Cada utilizador do sistema pode adicionar várias etiquetas para uma determinada coluna e pode adicionar etiquetas para várias colunas. Apenas o utilizador que criou objetos ColumnTag pode editá-los. (Os proprietários de recursos e os administradores podem eliminar o objeto de ColumnTag, mas não a editá-lo). O sistema mantém as etiquetas de coluna dos utilizadores, estes separadamente.  Portanto, é uma matriz de objetos de ColumnTag de cada ativo.  O ColumnTag livremente está vinculado ao esquema para que ele pode ficar dessincronizado. O ColumnTag poderá descrever uma coluna que já não existe no esquema.  É até o gravador para manter a etiqueta de coluna e o esquema em sincronia.</td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna que esta etiqueta refere-se a.</td></tr>
<tr><td></td><td>etiqueta</td><td>String</td><td>Uma etiqueta que descreve a coluna.</td></tr>

<tr><td>Especialista ("especialistas")</td><td></td><td></td><td>Esta propriedade contém um utilizador que é considerado um especialista no conjunto de dados. Bolhas de opinions(descriptions) dos especialistas na parte superior da experiência do Usuário quando lista as descrições. Cada utilizador pode especificar seus próprios especialistas. Apenas esse utilizador pode editar o objeto de especialistas. (Os proprietários de recursos e os administradores podem eliminar os objetos de especialistas, mas não a editá-lo).</td></tr>
<tr><td></td><td>especialista</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Pré-visualização ("pré-visualizações")</td><td></td><td></td><td>A pré-visualização contém um instantâneo de 20 linhas de dados para o elemento superior. Pré-visualização só fazem sentido para alguns tipos de ativos (que faz sentido para a tabela, mas não para a medida).</td></tr>
<tr><td></td><td>pré-visualização</td><td>object[]</td><td>Matriz de objetos que representam uma coluna.  Cada objeto tem um mapeamento de propriedades a uma coluna com um valor para essa coluna para a linha.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>O tipo de mime do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>string</td><td>As instruções sobre como obter acesso a este recurso de dados. O conteúdo pode ser um URL, um endereço de e-mail ou um conjunto de instruções.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>O número de linhas no conjunto de dados</td></tr>
<tr><td></td><td>tamanho</td><td>longa</td><td>O tamanho em bytes do conjunto de dados.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>string</td><td>A hora da última modificação do esquema</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>string</td><td>A hora da última modificação do conjunto de dados (dados foi adicionados, modificada, ou eliminar)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Colunas</td></td><td>ColumnDataProfile[]</td><td>Uma matriz dos perfis de dados de coluna.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>String</td><td>O nome da coluna que esta classificação refere-se a.</td></tr>
<tr><td></td><td>classification</td><td>String</td><td>A classificação de dados nesta coluna.</td></tr>

<tr><td>Documentação ("documentação")</td><td></td><td></td><td>Um determinado ativo pode ter apenas uma documentação associada a ele.</td></tr>
<tr><td></td><td>mimeType</td><td>string</td><td>O tipo de mime do conteúdo.</td></tr>
<tr><td></td><td>conteúdo</td><td>string</td><td>O conteúdo de documentação.</td></tr>

</table>

### <a name="common-types"></a>Tipos comuns
Tipos comuns podem ser utilizados como os tipos de propriedades, mas não são itens.

<table>
<tr><td><b>Tipo comum</b></td><td><b>Propriedades</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>string</td><td>Descreve o tipo de origem de dados.  Por exemplo: SQL Server, Oracle Database, etc.  </td></tr>
<tr><td></td><td>objectType</td><td>string</td><td>Descreve o tipo de objeto na origem de dados. Por exemplo: Tabela, vista para o SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocolo</td><td>string</td><td>Necessário. Descreve um protocolo utilizado para comunicar com a origem de dados. Por exemplo: "tds" para o SQl Server, "oracle" para o Oracle, etc. Consulte a <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">especificação de referência - estrutura de DSL da origem de dados</a> para a lista de protocolos suportados atualmente.</td></tr>
<tr><td></td><td>endereço</td><td>Dicionário<string, object></td><td>Necessário. O endereço é um conjunto de dados específicos para o protocolo que é utilizado para identificar a origem de dados a ser referenciada. Os dados de endereço no âmbito de um determinado protocolo, que significa que tem qualquer significado sem saber o protocolo.</td></tr>
<tr><td></td><td>autenticação</td><td>string</td><td>Opcional. O esquema de autenticação utilizado para comunicar com a origem de dados. Por exemplo: windows, oauth, etc.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Dicionário<string, object></td><td>Opcional. Obter informações adicionais sobre como ligar a uma origem de dados.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>O back-end não realiza nenhuma validação de propriedades fornecidas em relação a AAD durante a publicação.</td></tr>
<tr><td></td><td>upn</td><td>string</td><td>Endereço de e-mail exclusivo do utilizador. Tem de ser especificado se não for fornecido objectId ou no contexto da propriedade de "lastRegisteredBy", caso contrário, opcional.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>Identidade AAD de grupo ou utilizador administrativo. Opcional. Tem de ser especificado se upn não for fornecido, caso contrário, opcional.</td></tr>
<tr><td></td><td>firstName</td><td>string</td><td>Nome próprio do utilizador (para fins de exibição). Opcional. Só é válida no contexto da propriedade de "lastRegisteredBy". Não é possível especificar ao indicar o principal de segurança para "funções", "permissões" e "especialistas".</td></tr>
<tr><td></td><td>lastName</td><td>string</td><td>Apelido do utilizador (para fins de exibição). Opcional. Só é válida no contexto da propriedade de "lastRegisteredBy". Não é possível especificar ao indicar o principal de segurança para "funções", "permissões" e "especialistas".</td></tr>

<tr><td>Coluna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>string</td><td>Nome da coluna ou atributo.</td></tr>
<tr><td></td><td>tipo</td><td>string</td><td>tipo de dados da coluna ou atributo. Os tipos permitidos dependem sourceType de dados do recurso.  Apenas um subconjunto de tipos é suportado.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>O comprimento máximo permitido para a coluna ou o atributo. Derivada da origem de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>precisão</td><td>byte</td><td>A precisão para a coluna ou o atributo. Derivada da origem de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>isNullable</td><td>Booleano</td><td>Se a coluna tem permissão para ter um valor nulo ou não. Derivada da origem de dados. Apenas aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>expressão</td><td>string</td><td>Se o valor é uma coluna calculada, este campo contém a expressão que expresse o valor. Derivada da origem de dados. Apenas aplicável a alguns tipos de origem.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>string</td><td>O nome da coluna</td></tr>
<tr><td></td><td>tipo </td><td>string</td><td>O tipo da coluna</td></tr>
<tr><td></td><td>min. </td><td>string</td><td>O valor mínimo do conjunto de dados</td></tr>
<tr><td></td><td>máx. </td><td>string</td><td>O valor máximo no conjunto de dados</td></tr>
<tr><td></td><td>média </td><td>double</td><td>O valor médio no conjunto de dados</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>O desvio padrão para o conjunto de dados</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>A contagem de valores nulos no conjunto de dados</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>A contagem de valores distintos no conjunto de dados</td></tr>


</table>

## <a name="asset-identity"></a>Identidade de recurso
O catálogo de dados do Azure utiliza propriedades "protocolo" e a identidade da matriz de propriedades de "address" da propriedade de "dsl" DataSourceLocation para gerar a identidade do recurso, que é utilizada para o elemento dentro do catálogo de endereços.
Por exemplo, o protocolo de "tds" tem identidade propriedades "server", "base de dados", "schema" e "objeto". As combinações do protocolo e as propriedades de identidade são utilizadas para gerar a identidade do recurso de tabela de SQL Server.
O catálogo de dados do Azure fornece vários protocolos de origem de dados incorporados, que estão listados em [especificação de referência - estrutura de DSL da origem de dados](data-catalog-dsr.md).
O conjunto de protocolos suportados pode ser estendido por meio de programação (consulte a referência da API de REST do catálogo de dados). Os administradores do catálogo podem registar os protocolos de origem de dados personalizados. A tabela seguinte descreve as propriedades necessárias para registrar um protocolo personalizado.

### <a name="custom-data-source-protocol-specification"></a>Especificação de protocolo de origem de dados personalizados
<table>
<tr><td><b>Tipo</b></td><td><b>Propriedades</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>espaço de nomes</td><td>string</td><td>O espaço de nomes do protocolo. Espaço de nomes tem de ter entre 1 a 255 carateres de comprimento, conter uma ou mais partes não vazias separadas por ponto (.). Cada parte deve ter entre 1 e 255 carateres de comprimento, começar com uma letra e conter apenas letras e números.</td></tr>
<tr><td></td><td>nome</td><td>string</td><td>O nome do protocolo. Nome tem de ter entre 1 e 255 carateres de comprimento, começar com uma letra e conter apenas letras, números e o caráter de travessão (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>Lista de propriedades de identidade, tem de conter, pelo menos, um, mas não existem propriedades mais de 20. Por exemplo: "server", "base de dados", "schema", "objeto" é propriedades de identidade do protocolo "tds".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>Lista de conjuntos de identidade. Define os conjuntos de propriedades de identidade, que representam a identidade de recurso válido. Tem de conter, pelo menos, um, mas não existem conjuntos de mais de 20. Por exemplo: {"server", "da base de dados", "schema" e "objeto"} é uma identidade definida para o protocolo "tds", que define a identidade do recurso de tabela do Sql Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>string</td><td>O nome da propriedade. Nome deve ter entre 1 a 100 carateres de comprimento, começar com uma letra e pode conter apenas letras e números.</td></tr>
<tr><td></td><td>tipo</td><td>string</td><td>O tipo da propriedade. Valores suportados: "bool," booleano ","bytes","guid","int","integer","long","string","url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>booleano</td><td>Indica se o caso deve ser ignorado ao utilizar o valor da propriedade. Só pode ser especificado para propriedades com o tipo "cadeia". Valor predefinido é false.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>Indica se as maiúsculas e minúsculas devem ser ignoradas para cada segmento do caminho da url. Só pode ser especificado para propriedades com o tipo de "url". Valor predefinido é [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>string</td><td>O nome do conjunto de identidade.</td></tr>
<tr><td></td><td>propriedades</td><td>string[]</td><td>Definir a lista de propriedades de identidade incluído para essa identidade. Não pode conter duplicados. Cada propriedade referenciada pelo conjunto de identidade deve ser definida na lista de "identityProperties" do protocolo.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Funções e autorização
Catálogo de dados do Microsoft Azure proporciona capacidades de autorização para operações CRUD em ativos e anotações.

## <a name="key-concepts"></a>Conceitos-chave
O catálogo de dados do Azure utiliza dois mecanismos de autorização:

* Autorização baseada em funções
* Autorização baseada em permissão

### <a name="roles"></a>Funções
Existem três funções: **Administrador**, **proprietário**, e **contribuinte**.  Cada função tem seu escopo e os direitos, que estão resumidos na tabela seguinte.

<table><tr><td><b>Função</b></td><td><b>Âmbito</b></td><td><b>Direitos</b></td></tr><tr><td>Administrador</td><td>Catálogo (todos os ativos/anotações no catálogo)</td><td>ViewRoles de eliminação de leitura

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietário</td><td>Cada ativo (item de raiz)</td><td>ViewRoles de eliminação de leitura

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contribuinte</td><td>Cada recurso individual e a anotação</td><td>Read Update Delete ViewRoles Nota: todos os direitos são revogados se ao colaborador é revogada a leitura direito no item</td></tr></table>

> [!NOTE]
> **Leia**, **atualização**, **eliminar**, **ViewRoles** direitos são aplicáveis a qualquer item (ativo ou anotação) ao **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** só são aplicáveis ao elemento raiz.
> 
> **Eliminar** direito aplica-se a um item e qualquer subitems ou o único item abaixo dela. Por exemplo, eliminar um recurso também elimina quaisquer anotações para esse recurso.
> 
> 

### <a name="permissions"></a>Permissões
A permissão é como a lista de entradas de controlo de acesso. Cada entrada de controle de acesso atribui o conjunto de direitos para uma entidade de segurança. Permissões só podem ser especificadas num recurso (ou seja, o item de raiz) e aplicam-se para o elemento e qualquer subitems.

Durante a **catálogo de dados do Azure** pré-visualizar, apenas **leitura** direita é suportada na lista de permissões para ativar o cenário restringir a visibilidade de um ativo.

Por predefinição tem qualquer utilizador autenticado **leitura** botão direito do rato para qualquer item no catálogo, a menos que visibilidade é restrita para o conjunto de principais nas permissões.

## <a name="rest-api"></a>API REST
**COLOCAR** e **POST** podem ser utilizadas para controlar permissões e funções de pedidos de visualização do item: para além do payload de item, duas propriedades do sistema podem ser especificadas **funções** e  **permissões**.

> [!NOTE]
> **permissões** apenas aplicável a um item de raiz.
> 
> **Proprietário** função apenas aplicável a um item de raiz.
> 
> Por predefinição, quando um item é criado no catálogo do respetivo **contribuinte** está definido como utilizador autenticado atual. Se o item deve estar atualizável por todas as pessoas, **contribuinte** deve ser definido como &lt;todas as pessoas&gt; especial segurança principal no **funções** propriedade quando o item é a primeiro publicados (consulte para o exemplo a seguir). **Contribuinte** não pode ser alterado e permanece o mesmo durante o tempo de vida de um item (até mesmo **administrador** ou **proprietário** não tem permissão para alterar o **contribuinte**). O único valor suportado para a definição explícita dos **contribuinte** é &lt;todas as pessoas&gt;: **Contribuinte** só pode ser um utilizador que criou um item ou &lt;todas as pessoas&gt;.
> 
> 

### <a name="examples"></a>Exemplos
**Definir contribuinte &lt;todas as pessoas&gt; ao publicar um item.**
Entidade de segurança especial &lt;todas as pessoas&gt; tem o objectId "00000000-0000-0000-0000-000000000201".
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Algumas implementações de cliente HTTP podem automaticamente volte a emitir pedidos em resposta a um 302 do servidor, mas normalmente remover os cabeçalhos de autorização do pedido. Uma vez que o cabeçalho de autorização é necessário para fazer pedidos ao catálogo de dados do Azure, certifique-se de que cabeçalho de autorização ainda é fornecido quando voltando a emitir um pedido para uma localização de redirecionamento especificada pelo catálogo de dados do Azure. O código de exemplo seguinte demonstra isso usando o objeto HttpWebRequest do .NET.
> 
> 

**Corpo**

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

  **Atribua proprietários e restringir a visibilidade de um item existente de raiz**: **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

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

> [!NOTE]
> No PUT não seja necessário especificar um payload de item no corpo: PUT pode ser utilizado para atualizar apenas funções de e/ou permissões.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
