---
title: O que há de novo no catálogo de dados do Azure
description: Este artigo fornece uma visão geral dos novos recursos adicionados ao catálogo de dados do Azure.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 0f328c4edeaa35210c5379d84fa47980cb2f07cf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317090"
---
# <a name="whats-new-in-azure-data-catalog"></a>O que há de novo no catálogo de dados do Azure
Atualiza para **catálogo de dados do Azure** são lançadas regularmente. Nem todas as versão inclui novas funcionalidades destinadas ao utilizador, como algumas versões estão focalizadas em capacidades do serviço de back-end. Esta página destaca os novos recursos de destinada ao utilizador adicionados ao serviço catálogo de dados do Azure.

## <a name="whats-new-for-november-2017"></a>O que há de novo em Novembro de 2017 
A partir de Novembro de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para criar uma ligação diretamente para os termos do glossário de negócios específicas no portal do catálogo de dados. Os utilizadores podem copiar links do glossário comercial e inseri-los em documentos, mensagens de e-mail, relatórios ou em outros locais para ligar diretamente a definição do termo do glossário.
* Suporte para principais de serviço do Azure Active Directory. Os administradores do catálogo de dados podem autorizar a utilização de principais de serviço para aceder ao catálogo de aplicações de cliente e podem conceder esses aplicativos permissões específicas como eles podem conceder permissões a utilizadores e grupos de segurança. Para obter mais informações, consulte [aplicativos e objetos de principal de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).
* Suporte para a autenticação do Azure Active Directory ao ligar a origens de dados do Azure SQL Database e o Azure SQL Data Warehouse, utilizando a ferramenta de registo de origem de dados do catálogo de dados. Para obter mais informações, consulte [utilizar do Azure Active Directory a autenticação para a autenticação com base de dados SQL ou SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>O que há de novo em Setembro de 2017 
A partir de Setembro de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para extrair Junte-se a relação de metadados de origens de dados de DB2 ao registar as tabelas relacionadas com a ferramenta de registo da origem de dados.
* Suporte para o registo de origens de dados de 3.4 de versão do MongoDB com a ferramenta de registo da origem de dados.
* Suporte para eliminar todos os metadados de objetos contidos numa única operação ao remover uma base de dados ou de outro contentor a partir do catálogo de dados.
* Suporte para exibir até 1.000 etiquetas, termos do glossário de negócios ou outra pesquisa facetas, ao refinar uma pesquisa no portal do catálogo de dados.


## <a name="whats-new-for-august-2017"></a>O que há de novo em Agosto de 2017 
A partir de Agosto de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

*   Uma nova amostra de desenvolvedor está disponível para criar e gerir metadados de relação com a API de REST do catálogo de dados. O *importar informações de relação para o catálogo de dados* exemplo está disponível no [página de exemplos de código do catálogo de dados](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Suporte para extrair Junte-se a relação de metadados de origens de dados Teradata ao registar as tabelas relacionadas com a ferramenta de registo da origem de dados.
* Suporte para objetos de função (TVF) de valor de tabela do SQL Server ao registar as origens de dados do SQL Server com a ferramenta de registo da origem de dados.
* Várias atualizações e refinamentos para aumentar o desempenho e a usabilidade do portal do catálogo de dados.

## <a name="whats-new-for-july-2017"></a>O que há de novo em Julho de 2017 
A partir de Julho de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Suporte para um controle mais granular sobre as operações de metadados permitidos, incluindo:
    - Os administradores do catálogo podem restringir a capacidade dos próprios utilizadores contribuam com etiquetas e os metadados relacionados para o catálogo, ativar o acesso só de leitura ao catálogo de.
    - Os administradores do catálogo podem restringir a capacidade dos próprios utilizadores para registar novas origens de dados no catálogo.
    - Os administradores do catálogo podem restringir a capacidade dos próprios utilizadores para assumir a propriedade de metadados do recurso de dados no catálogo.
    - Podem ser concedidas permissões para grupos de segurança do Azure Active Directory e os utilizadores para facilitar a gestão de permissões.
* Suporte para relações entre os recursos de dados registados e deteção de recursos de dados relacionados no portal do catálogo de dados, incluindo:
    - Extração de metadados de relação do MySQL, Oracle e SQL Server (incluindo a base de dados do Azure SQL) ao utilizar a ferramenta de registo de origem de dados do catálogo de dados de origens de dados.
    - Deteção de ativos de dados relacionados ao visualizar os metadados de recurso no portal do catálogo de dados.
    - Operações para definir, detetar e gerir relações entre os recursos de dados utilizando a API de REST do catálogo de dados.

Para obter detalhes adicionais sobre o gerenciamento de permissões no catálogo de dados, consulte [como proteger o acesso ao catálogo de dados e recursos de dados](data-catalog-how-to-secure-catalog.md).
Para obter detalhes adicionais sobre as relações no catálogo de dados, veja [como ver os recursos de dados relacionados no catálogo de dados do Azure](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Quais são as novidades para Junho de 2017 
A partir de Junho de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Suporte para origens de dados Sybase, Apache Cassandra e MongoDB. Os utilizadores podem agora registar e detetar vistas e tabelas de bancos de dados e tabelas e bancos de dados Sybase, Cassandra e MongoDB.

> [!NOTE]
> Ao registar o MongoDB e Cassandra tabelas que inclua colunas com tipos de dados complexos, como registos e matrizes, essas colunas não serão incluídas nos metadados adicionados ao catálogo de dados.

## <a name="whats-new-for-may-2017"></a>O que há de novo em Maio de 2017 
A partir de Maio de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Uma nova amostra de desenvolvedor está disponível para a importação em massa de termos do glossário de negócios. O exemplo de importação em massa de glossário está disponível no [página de exemplos de programador do catálogo de dados](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Suporte para editar informações de ligação de ODBC no portal do catálogo de dados do Azure. Os proprietários dos recursos de dados e os administradores do catálogo de dados agora podem editar as informações de ligação para origens de dados ODBC registadas sem a necessidade de voltar a registar as origens de dados.
*   Suporte para URLs clicáveis em definições de glossário de negócios e descrições. Quando os URLs estão incluídos nas propriedades de descrição e a definição de termos do glossário de negócios, os URLs serão apresentados como hiperlinks no portal do catálogo de dados.
*   Suporte para apresentar os nomes de especialistas além dos endereços de e-mail de especialistas. Ao visualizar especialistas nas propriedades de um recurso de dados no portal do catálogo de dados, nome completo do especialista do Azure Active Directory será apresentado na dica de ferramenta.

## <a name="whats-new-for-april-2017"></a>O que há de novo em Abril de 2017 
A partir de Abril de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Suporte para origens de dados ODBC. Os utilizadores podem agora registar e detetar as bases de dados, tabelas e vistas utilizando a ferramenta de registo de origem de dados do catálogo de dados ODBC.

## <a name="whats-new-for-march-2017"></a>O que há de novo em Março de 2017 
A partir de Março de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Suporte para utilizar grupos de segurança do AAD para administradores do catálogo de dados.
*   O catálogo de dados do Azure está agora disponível numa nova região do Azure. Os clientes podem aprovisionar o catálogo de dados do Azure na e.u.a. Centro-Oeste região, além dos E.U.A. leste, E.U.A. oeste, Europa Ocidental e leste da Austrália, Europa do Norte e Sudeste asiático. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>O que há de novo em Fevereiro de 2017 
A partir de Fevereiro de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Suporte para definições avançadas na ferramenta de registo de origem de dados do catálogo de dados. Os utilizadores podem especificar valores de tempo limite do comando ao registar as origens de dados grandes.
*   Suporte para origens de dados FTP e PostgreSQL. Os utilizadores podem agora registar e detetar FTP ficheiros e diretórios e as bases de dados PostgreSQL, tabelas e vistas.

## <a name="whats-new-for-january-2017"></a>O que há de novo para Janeiro de 2017 
A partir de Janeiro de 2017, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Catálogo de dados do Azure é agora [CSA STAR](https://www.microsoft.com/en-us/trustcenter/compliance/csa-star-certification) em conformidade.
*   Integração com o [obter e transformar no Excel 2016 e o Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Os usuários do Excel podem partilhar consultas e detetar consultas com o catálogo de dados do Azure de dentro do Excel. Esta funcionalidade está disponível para utilizadores com licenças do Power BI Pro.

## <a name="whats-new-for-december-2016"></a>O que há de novo em Dezembro de 2016
A partir de Dezembro de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Catálogo de dados do Azure é agora [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) e [cláusulas modelo da UE](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) em conformidade.
*   Suporte para editar informações de ligação de origem de dados. Os proprietários dos recursos de dados e os administradores do catálogo de dados agora podem editar as informações de ligação para origens de dados registados sem a necessidade de voltar a registar as origens de dados.
*   Suporte para origens de dados do Salesforce.com. Os utilizadores podem agora registar e detetar objetos do Salesforce.


## <a name="whats-new-for-november-2016"></a>O que há de novo em Novembro de 2016
A partir de Novembro de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:
*   Catálogo de dados do Azure é agora [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) e [ISO/IEC 27018](https://www.microsoft.com/en-us/TrustCenter/Compliance/iso-iec-27018) em conformidade.
*   Suporte para o registo manual das origens de dados ODBC com o portal do catálogo de dados e a REST API.

## <a name="whats-new-for-september-2016"></a>O que há de novo em Setembro de 2016
A partir de Setembro de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para origens de dados IBM DB2. Os utilizadores podem agora registar e detetar as bases de dados DB2, tabelas e vistas.
* Suporte para origens de dados do Azure Cosmos DB. Os utilizadores podem agora registar e detetar as bases de dados do Cosmos DB e coleções.
* Suporte para personalizar o nome do catálogo no portal do catálogo de dados. Os administradores do catálogo agora podem fornecer o texto que é apresentado no título do portal, como o nome da organização.

## <a name="whats-new-for-august-2016"></a>O que há de novo em Agosto de 2016
Em Agosto de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Aprimoramentos para o registo de origens de dados do SQL Server Master Data Services (MDS). Os utilizadores agora podem incluir perfis e dados de pré-visualizações ao registar as entidades do MDS com a ferramenta de registo de origem de dados do catálogo de dados.
* Suporte para pesquisas guardadas organizacionais definida pelo administrador. Ao salvar uma pesquisa no portal do catálogo de dados, os administradores do catálogo de dados agora podem optar por guardar pesquisas para uso pessoal ou para todos os utilizadores do catálogo. Pesquisas guardadas organizacionais são partilhadas com todos os utilizadores do catálogo e podem fornecer pontos de partida padronizados para deteção de origens de dados.
* Atualizações para a vista de propriedades no portal do catálogo de dados. Todas as propriedades de recurso de dados são agora apresentadas e gerenciadas num painel único, redimensionável para fornecer mais consistente e experiência detetável.

## <a name="whats-new-for-july-2016"></a>O que há de novo em Julho de 2016
A partir de Julho de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para origens de dados do SQL Server Master Data Services (MDS). Os utilizadores podem agora registar e detetar MDS modelos e entidades.
* Suporte para obter os procedimentos armazenados do SQL Server. Os utilizadores podem agora registar e detetar os objetos de procedimento armazenado em origens de dados do SQL Server.
* Suporte para idiomas adicionais no portal do catálogo de dados do Azure e a ferramenta de registo de origem de dados, para um total de 18 idiomas suportados. A experiência de utilizador do catálogo de dados do Azure é localizada com base nas preferências de idioma definido no Windows ou no navegador da web.
* Atualizações e o refinamento para o catálogo de dados home page do portal, incluindo aprimoramentos de desempenho e uma experiência de usuário simplificada.

## <a name="whats-new-for-june-2016"></a>Quais são as novidades para Junho de 2016
A partir de Junho de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para redimensionamento de colunas na vista de lista quando detetar recursos de dados no portal do catálogo de dados. Os utilizadores agora podem redimensionar colunas individuais para ler mais facilmente os metadados de recurso longas, como etiquetas e descrições.
* Power Query para Excel foi adicionado ao menu de "Abrir em" no portal do catálogo de dados. Os utilizadores agora podem abrir origens de dados suportadas no Excel 2016 ou no Excel 2010 e o Excel 2013 com o [Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) suplemento instalado.
* Suporte para origens de dados do armazenamento de tabelas do Azure. Os utilizadores podem agora registar e detetar os objetos de tabela em origens de dados do armazenamento do Azure.

## <a name="whats-new-for-may-2016"></a>O que há de novo em Maio de 2016
A partir de Maio de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Um glossário comercial que permite aos administradores do catálogo definir termos de negócios e hierarquias para criar um vocabulário de negócios comuns. Os utilizadores podem Etiquetar recursos de dados registados com os termos do glossário para facilitar a detetar e compreender o conteúdo do catálogo. Para obter mais informações, veja [How to set up the Business Glossary for Governed Tagging (Como configurar o Glossário Comercial para Etiquetagem Regida)](data-catalog-how-to-business-glossary.md).  
* Aprimoramentos para o glossário de negócios de catálogo de dados que permite aos utilizadores atualizar vários termos do glossário numa única operação. Os utilizadores podem selecionar vários termos para editar os campos seguintes:
  * Termo principal: O utilizador pode selecionar um novo termo de principal e todos os termos selecionados são atualizados para ser subordinados do termo principal selecionado. Se selecionado de termos de todos os tenham o mesmo elemento principal, o pai é mostrado na caixa de texto, caso contrário, o termo principal campo é definido como em branco.   
  * As etiquetas e partes interessadas: Os utilizadores podem adicionar e remover etiquetas e partes interessadas para vários termos do glossário usando a mesma experiência como marcação de vários recursos de dados.

> [!NOTE]
> O glossário comercial está disponível apenas a Standard Edition do catálogo de dados Azure. A edição gratuita não fornece capacidades para a marcação governados ou um glossário de negócios.

## <a name="whats-new-for-march-2016"></a>O que há de novo em Março de 2016
A partir de Março de 2016, foram adicionadas as seguintes capacidades para Azure dados. o catálogo: g:

* Um endpoint de REST API consolidada para acessar programaticamente os recursos de pesquisa e capacidades de gestão de recursos de catálogo do serviço catálogo de dados do Azure. Este ponto final de API de catálogo e o ponto final de API de pesquisa foi preterida e descontinuada a 21 de Março de 2016. Não há nenhuma alteração para a semântica da API. Apenas o ponto final de URI alterado. Para obter mais informações, consulte a [referência da API do REST do Azure Data catálogo](https://msdn.microsoft.com/library/azure/mt267595.aspx). Para exemplos de API, consulte [exemplos de programador do catálogo de dados do Azure](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>O que há de novo em Fevereiro de 2016
A partir de Fevereiro de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Uma seleção de fonte de dados recém-reestruturado experiência na ferramenta de registo de origem de dados do catálogo de dados do Azure. A ferramenta de registo da origem de dados foi atualizada para que seja mais fácil de localizar e selecionar a partir de origens de dados suportados pelo catálogo de dados do Azure.
* Suporte para 10 idiomas adicionais no portal do catálogo de dados do Azure e a ferramenta de registo da origem de dados. Para além de inglês, a experiência de catálogo de dados do Azure está agora disponível em alemão, espanhol, francês, italiano, japonês, coreano, português (Brasil), russo, chinês simplificado e chinês tradicional. A experiência de utilizador do catálogo de dados do Azure é localizada com base nas preferências de idioma definido no Windows ou no browser do utilizador.
* Suporte para georreplicação de dados do catálogo de dados do Azure para a recuperação de desastre e continuidade de negócio. Todos os conteúdos do catálogo de dados do Azure, incluindo origem extraídos da Comunidade e metadados anotações de dados, agora são replicados entre duas regiões do Azure sem custos adicionais aos clientes. Regiões do Azure previamente estão emparelhadas, pelo menos de 500 quilómetros de distância e siga o mapeamento, conforme descrito em [Business continuidade e recuperação após desastre (BCDR): Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).
* Suporte para alterar a subscrição do Azure utilizada pelo catálogo de dados do Azure. Os administradores do catálogo de dados do Azure podem utilizar a página de definições no portal do catálogo de dados do Azure para selecionar uma subscrição diferente do Azure para fins de faturação.

## <a name="whats-new-for-january-2016"></a>O que há de novo para Janeiro de 2016
A partir de Janeiro de 2016, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para o registo manualmente a origens de dados adicionais. Pode agora utilizar "Criar entrada Manual" no portal do catálogo de dados do Azure ou utilize a API de REST do catálogo de dados do Azure para registar as origens de dados seguintes:
  * OData - função, o conjunto de entidades e o contentor de entidades
  * HTTP - ficheiro, o ponto final, o relatório e o Site
  * Sistema de ficheiros - ficheiros
  * SharePoint - lista
  * FTP - File e Directory
  * Salesforce.com - objeto
  * DB2 - tabela, exibição e da base de dados
  * PostgreSQL – tabela, exibição e da base de dados
* Suporte para "Abrir no SQL Server Data Tools" para origens de dados do SQL Server (incluindo a BD SQL do Azure e Azure SQL Data Warehouse).  
* Suporte para registar e detetar vistas do SAP HANA e pacotes. Pode registar origens de dados do SAP HANA com o catálogo de dados do Azure, dados de origem de ferramenta de registo e podem anotar e detetar origens de dados registadas do SAP HANA com o portal do catálogo de dados do Azure.
* A capacidade de fixar e desafixar recursos de dados no portal do catálogo de dados do Azure. Pode optar por afixar recursos de dados para torná-los mais fáceis de detetar novamente e reutilizar.
* Uma página inicial recém-reestruturado no portal do catálogo de dados do Azure. A nova home page inclui informações sobre a atividade de utilizadores atual - incluindo ativos recentemente publicados, Ativos fixos e pesquisas guardadas - e informações sobre a atividade no catálogo de como um todo.
* Suporte para definições de utilizador persistentes no portal do catálogo de dados do Azure. Definições de experiência do usuário - incluindo exibição de grade ou mosaico, o número de resultados por página e o detetor de ocorrências ou desativar - são mantidas entre sessões de utilizador.
* O catálogo de dados do Azure está agora disponível em duas novas regiões do Azure. Os clientes podem aprovisionar o catálogo de dados do Azure nas regiões Europa do Norte e Sudeste asiático, além dos E.U.A. leste, E.U.A. oeste, Europa Ocidental e leste da Austrália. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Abrir no SQL Server Data Tools" requer o Visual Studio 2013 com a atualização 4 e ferramentas do SQL Server para ser instalado. Para instalar a versão mais recente do SQL Server Data Tools, visite [transferir SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>O que há de novo em Dezembro de 2015
A partir de Dezembro de 2015, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para perfis de dados para origens de dados do Azure SQL Data Warehouse. Ao registar o Azure SQL Data Warehouse tabelas e vistas, os utilizadores podem optar por incluir métricas de perfil de dados com os metadados extraídos da origem de dados.
* Suporte para registar e detetar objetos MySQL e bases de dados. Os utilizadores podem registar origens de dados MySQL com o catálogo de dados do Azure, dados de origem de ferramenta de registo e podem anotar e detetar origens de dados MySQL registadas através do portal do catálogo de dados do Azure.
* Suporte para a autenticação SPNEGO e do Windows para origens de dados Teradata. Ao registar a Teradata tabelas e vistas, podem escolher os utilizadores liguem a Teradata através da autenticação SPNEGO e Windows e LDAP e TD2.
* Suporte para origens de dados do Azure Data Lake Store. Os utilizadores podem agora registar e detetar origens de dados do Azure Data Lake Store com o catálogo de dados do Azure.
* Suporte para especificar manualmente as definições de proxy de rede em que a ferramenta de registo de origem de dados do catálogo de dados do Azure. Os utilizadores podem selecionar "Modificar as definições de proxy" da página de boas-vindas da ferramenta e podem especificar o endereço de proxy e a porta a ser utilizado pela ferramenta.

## <a name="whats-new-for-november-2015"></a>O que há de novo em Novembro de 2015
A partir de Novembro de 2015, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* A capacidade de ver e copiar as cadeias de ligação no portal do catálogo de dados do Azure para SQL Server (incluindo a base de dados do Azure SQL) e origens de dados Oracle. Os utilizadores podem clicar "Ver cadeias de ligação" link as informações de ligação para uma tabela do SQL Server ou Oracle, a vista ou a base de dados, para ver as cadeias de ligação utilizadas para ligar à origem de dados. As cadeias de ligação do ADO.NET, ODBC, OLEDB e JDBC são fornecidas para origens de dados do SQL Server. Cadeias de ligação de OLEDB e ODBC são fornecidas para origens de dados Oracle.
* Suporte para perfis de dados, incluindo ao registar a Teradata tabelas e vistas.
* Suporte para "Abrir no Power BI Desktop" para o SQL Server (incluindo a BD SQL do Azure e Azure SQL Data Warehouse), SQL Server Analysis Services, o armazenamento do Azure e origens HDFS.  
* Suporte para autenticação LDAP para origens de dados Teradata. Ao registar a Teradata tabelas e vistas, os utilizadores podem escolher para ligar a Teradata usando o LDAP e a autenticação de TD2.
* Suporte para "Abrir no Excel" para origens de dados Teradata.
* Suporte para os termos de pesquisa recentes no portal do catálogo de dados do Azure. Ao pesquisar no portal, os utilizadores podem selecionar dos termos de pesquisa utilizados recentemente para acelerar a experiência de deteção.
* Suporte para pré-visualização para origens de dados Teradata. Ao registar a Teradata tabelas e vistas, os utilizadores podem optar por incluir registos de instantâneo com os metadados extraídos da origem de dados.
* Suporte para "Abrir no Excel" para origens de dados do Azure SQL Data Warehouse.
* Suporte para definir e editar esquemas de nível de coluna para recursos de dados registados manualmente. Depois de criar manualmente um recurso de dados com o portal do catálogo de dados do Azure, os usuários podem adicionar definições de coluna nas propriedades de recurso de dados.
* Suporte para consultas de "tem" ao pesquisar o catálogo de dados do Azure, para ativar a deteção de recursos de dados registados que possuem metadados específicos. Sintaxe de consulta de catálogo de dados do Azure agora inclui:

| Sintaxe de consulta | Objetivo |
| --- | --- |
| `has:previews` |Encontrar recursos de dados que incluem uma pré-visualização |
| `has:documentation` |Encontrar recursos de dados para o qual documentação foi fornecida |
| `has:tableDataProfiles` |Encontrar recursos de dados com informações de perfil de dados de nível de tabela |
| `has:columnsDataProfiles` |Encontrar recursos de dados com informações de perfil de dados de nível de coluna |


> [!NOTE]
> "Abrir no Power BI Desktop" necessita de uma versão atual da aplicação do Power BI Desktop para ser instalado. Se tiver problemas ou erros ao utilizar esta funcionalidade, certifique-se de que tem a versão mais recente do Power BI Desktop a partir [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>O que há de novo em Outubro de 2015
A partir de Outubro de 2015, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para encriptação inativa de dados em perfis de pré-visualizações e os dados para origens de dados registados. O catálogo de dados do Azure encripta transparente quaisquer registos de pré-visualização e origens de dados registadas com o serviço, sem a necessidade de gestão de chaves por administradores do catálogo de perfis de dados.
* Suporte para origens de dados Teradata. Os utilizadores podem agora registar e detetar Teradata tabelas e vistas.
* Suporte para origens de dados do Hive no local. Os utilizadores podem agora registar e detetar tabelas do Hive para Apache Hive nas origens de dados do Hadoop no local.
* Suporte para pesquisas guardadas no portal do catálogo de dados do Azure. Os utilizadores podem guardar termos de pesquisa e seleções facilmente repetir pesquisas anteriores e definir vistas útil do conteúdo do catálogo de filtro. Utilizador também pode marcar uma procura guardada como sua pesquisa predefinida. Quando um usuário clica no ícone de pesquisa "Lupa" partir da home page de portal do catálogo de dados do Azure ou da página de "Introdução", o utilizador é direcionado diretamente para a pesquisa guardada sinalizada como predefinição.
* Suporte para documentação de texto formatado para recursos de dados registados e contentores no portal do catálogo de dados do Azure. Os utilizadores agora podem fornecer documentação para recursos de dados, como tabelas, vistas e relatórios e para contentores, tais como bases de dados e modelos, para cenários em que as etiquetas e descrições não são suficientes.
* Suporte para o registo manualmente os tipos de origens de dados conhecidos. Os utilizadores podem introduzir manualmente as informações de origem de dados através do portal do catálogo de dados do Azure para todos os tipos de origem de dados suportados pelo catálogo de dados do Azure.
* Suporte para autorizar grupos de segurança do Azure Active Directory. Os administradores do catálogo podem ativar o acesso de catálogo para grupos de segurança, contas de utilizador, tornando mais fácil gerir o acesso ao catálogo de dados do Azure.
* Suporte para abrir o origens de dados do Hive no Excel a partir do portal do catálogo de dados do Azure.

> [!NOTE]
> A versão atual, apenas a autenticação do Teradata TD2 é suportada. Autenticação adicional mecanismos são suportados em futuras versões.

> [!NOTE]
> Para utilizar a funcionalidade de "Abrir no Excel" para origens de dados de Hive, os utilizadores devem ter instalado o controlador ODBC para o Hive.

## <a name="whats-new-for-september-2015"></a>O que há de novo em Setembro de 2015
A partir de Setembro de 2015, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para perfis de dados, incluindo ao registar as origens de dados do Hive.
* Suporte para detetar programaticamente a API do catálogo, tornando mais fácil para os aplicativos integrem o catálogo de dados do Azure.
* Experiência de deteção no portal do catálogo de dados do Azure de origem de dados novos "Introdução". Quando os utilizadores introduzem a página "detetar" do portal do catálogo de dados do Azure sem introduzir um termo de pesquisa, é-lhes apresentada uma visão geral do conteúdo de catálogo incluindo etiquetas utilizadas com mais frequência, especialistas, os tipos de origens de dados e tipos de objeto.
* Suporte para registar e detetar objetos do armazém de dados SQL do Azure e bases de dados. Para obter informações adicionais sobre o Azure SQL Data Warehouse, consulte [o SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Suporte para registar e detetar servidores do SQL Server Reporting Services como contentores e modelos de SQL Server Analysis Services. Quando o registro de objetos SSAS e o SSRS, o catálogo de dados do Azure cria uma entrada para o modelo SSAS e o servidor SSRS e para os relatórios e outros objetos. Os contentores podem ser detetados e anotado com o portal do catálogo de dados do Azure. Os utilizadores também podem procurar e filtrar o conteúdo de um modelo ou do servidor, além de pesquisar e filtrar o conteúdo do catálogo.
* Suporte para registar e detetar objetos de SQL Server Analysis Services através de HTTP/HTTPS. Os utilizadores podem agora ligar aos servidores do SSAS através de um URL (por exemplo, https:\//servername/olap/msmdpump.dll) em vez de um servidor dê um nome e pode utilizar a autenticação básica e ligações anónimas para além da autenticação do Windows. Para obter informações adicionais sobre as ligações HTTP/HTTPS para SSAS, consulte [configurar o acesso HTTP ao Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Suporte para origens de dados do Hive no HDInsight. Os utilizadores podem agora registar e detetar tabelas do Hive para Apache Hive no Hadoop no HDInsight origens de dados. Para obter informações adicionais sobre o Hive no HDInsight, consulte a [Centro de documentação do HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Suporte para registar e detetar as bases de dados Oracle e clusters HDFS como contentores. Ao registar as tabelas de Oracle e modos de exibição ou HDFS, o catálogo de dados do Azure cria uma entrada para a base de dados, tabelas e vistas. A base de dados pode ser detetado e anotado com o portal do catálogo de dados do Azure. Os utilizadores também podem procurar e filtrar o conteúdo de uma base de dados ou o cluster, além de pesquisar e filtrar o conteúdo do catálogo.
* Suporte para o registo manualmente os tipos de origens de dados desconhecido. Os utilizadores podem introduzir manualmente as informações de origem de dados através do portal do catálogo de dados do Azure, para que não tenham sido explicitamente suportadas pela ferramenta de registo de origem de dados de origens de dados possam ser anotadas e detetadas.
* Suporte para registar e detetar as bases de dados do SQL Server como contentores. Ao registar as vistas e tabelas do SQL Server, o catálogo de dados do Azure cria uma entrada para a base de dados, tabelas e vistas. A base de dados pode ser detetado e anotado com o portal do catálogo de dados do Azure. Os utilizadores também podem procurar e filtrar o conteúdo de uma base de dados, além de pesquisar e filtrar o conteúdo do catálogo.

> [!NOTE]
> Objetos SSAS e o SSRS que foram registados antes do lançamento de 18 de Setembro tem de estar registados novamente usando a ferramenta de registo da origem de dados antes da entrada do modelo ou do servidor é adicionada ao catálogo. Voltar a registar uma origem de dados não afeta qualquer anotações que foram adicionadas por utilizadores no portal do catálogo de dados do Azure.

> [!NOTE]
> Tabelas de Oracle e exibições e arquivos HDFS e diretórios que foram registados antes do lançamento de 11 de Setembro tem de ser novamente registados com a ferramenta de registo da origem de dados antes da entrada de base de dados ou o cluster é adicionada ao catálogo. Voltar a registar uma origem de dados não afeta qualquer anotações que foram adicionadas por utilizadores no portal do catálogo de dados do Azure.

> [!NOTE]
> Tabelas do SQL Server e vistas que foram registados antes do lançamento de 4 de Setembro tem de ser novamente registadas com a ferramenta de registo da origem de dados antes da entrada de base de dados é adicionada ao catálogo. Voltar a registar uma origem de dados não afeta qualquer anotações que foram adicionadas por utilizadores no portal do catálogo de dados do Azure.

## <a name="whats-new-for-august-2015"></a>O que há de novo em Agosto de 2015
A partir de Agosto de 2015, as seguintes funcionalidades foram adicionadas ao catálogo de dados do Azure:

* Suporte para criação de perfis de dados de origens de dados do SQL Server e Oracle. Ao registar o SQL Server e Oracle tabelas e vistas, os usuários podem escolher incluir informações de perfil de dados para os objetos que está a ser registado. O perfil de dados inclui estatísticas ao nível do objeto e o nível de coluna.
* Suporte para origens de dados do Hadoop HDFS. Os utilizadores podem agora registar e detetar HDFS arquivos e diretórios.
* Suporte para fornecer informações de pedido de acesso para origens de dados registados. Para recursos de dados registados, os usuários agora podem fornecer instruções para pedir acesso, incluindo ligações de e-mail ou URLs, fácil integração com ferramentas e processos existentes.
* Dicas de ferramentas para etiquetas e especialistas, para que seja mais fácil de descobrir o que os utilizadores tenham fornecido que metadados para recursos de dados registados.
* Adicionámos um novo botão de "User" e um menu para nossa barra de navegação superior. Esse menu permite que o utilizador veja a conta utilizada para iniciar sessão no catálogo de dados do Azure e terminar sessão, se assim o desejar. Esse menu também exibe o nome do catálogo, o que é valioso para os desenvolvedores que usam a API de REST do catálogo de dados do Azure.
* Standard Edition: Ao adicionar os proprietários de recursos de dados, o catálogo de dados do Azure suporta agora as contas de utilizador e grupos de segurança como proprietários. Para adicionar um grupo de segurança como um proprietário para recursos de dados selecionada, pode introduzir o nome a apresentar do grupo ou o endereço de email UPN do grupo, se ele tiver um.
* Suporte para origens de dados do armazenamento de Blobs do Azure. Os utilizadores podem agora registar e detetar blobs de armazenamento do Azure e diretórios.

