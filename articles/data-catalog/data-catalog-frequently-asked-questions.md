---
title: Catálogo de Dados Azure frequentemente fez perguntas
description: Frequentemente, perguntas sobre o Azure Data Catalog, incluindo capacidades para a descoberta de fontes de dados, anotação e gestão.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 98854a4588b59cf0c19877da870e6124fa7c3b9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674687"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Catálogo de Dados Azure frequentemente fez perguntas

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Este artigo fornece respostas a perguntas frequentes relacionadas com o serviço Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
Data Catalog é um serviço totalmente gerido, hospedado no Microsoft Azure, que serve como um sistema de registo e descoberta para fontes de dados da empresa. Com o Data Catalog, qualquer utilizador, desde analistas a cientistas de dados e desenvolvedores, pode registar, descobrir, compreender e consumir fontes de dados.

## <a name="what-customer-challenges-does-it-solve"></a>Que desafios para os clientes resolve?
O Data Catalog aborda os desafios da descoberta de fontes de dados e "dados escuros" para que os utilizadores possam descobrir e compreender as fontes de dados da empresa.

## <a name="what-are-its-target-audiences"></a>Quais são os seus públicos-alvo?
O Catálogo de Dados é projetado para utilizadores técnicos e não técnicos, incluindo:

* Desenvolvedores de dados e profissionais de BI e analíticos: Pessoas responsáveis pela produção de dados e conteúdos de análise para outros consumirem.
* Administradores de dados: Pessoas que têm conhecimento sobre os dados, o que significa e como se destina a ser usado.
* Consumidores de dados: Pessoas que precisam de ser capazes de descobrir, compreender e conectar-se facilmente aos dados de que precisam para fazer o seu trabalho, utilizando a ferramenta à sua escolha.
* Te central: Pessoas que precisam de tornar centenas de fontes de dados detetáveis pelos utilizadores empresariais, e que precisam de manter a supervisão sobre como os dados estão a ser usados e por quem.

## <a name="what-is-its-availability-by-region"></a>Qual é a sua disponibilidade por região?
Os serviços do Data Catalog estão atualmente disponíveis nos seguintes centros de dados:

* E.U.A. Oeste
* E.U.A. Leste
* Europa Ocidental
* Europa do Norte
* Leste da Austrália
* Sudeste Asiático

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quais são os seus limites no número de ativos de dados?
A Edição Gratuita do Catálogo de Dados está limitada a 5.000 ativos de dados registados.

A Standard Edition of Data Catalog suporta até 100.000 ativos de dados registados.

Qualquer objeto registado no Catálogo de Dados, como tabelas, visualizações, ficheiros e relatórios, conta como um ativo de dados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quais são as suas fontes de dados suportadas e tipos de ativos?
Para obter uma lista de fontes de dados atualmente suportadas, consulte [o Catálogo de Dados DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Como solicito apoio a outra fonte de dados?
Para submeter pedidos de funcionalidades e outros feedbacks, aceda ao [Catálogo de Dados nos Fóruns de Feedback Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Porque é que eu tenho um *catálogo de erros que já existe* quando tento criar um novo catálogo?

Ao comprar o Office 365 E5 com a Licença Power BI Pro, a Microsoft cria automaticamente um catálogo padrão na região da subscrição. Este catálogo utiliza o SKU gratuito. A licença de utilizador do Office 365 / Power BI é gerida na página de administração. 

No entanto, este tipo de catálogo de dados não tem uma **Opção de Administrador** e não está visível no **portal Azure**. Não é possível eliminar este tipo de catálogo de dados. Da mesma forma, não está autorizado a mudar o nome do catálogo de dados, e não pode movê-lo para outra região. 

As contas dos utilizadores que recebem uma licença Power BI Pro automática têm acesso ao catálogo de dados devido ao Contrato de Licença quando se inscreveram para o Office 365 E5 com a Licença Power BI Pro. Este tipo de utilizador tem acesso total a ativos de catálogo de dados sem privilégios administrativos. Este tipo de utilizador *não* faz parte da função de Utilizador do **Catálogo** no Catálogo de Dados Azure.


## <a name="how-do-i-get-started-with-data-catalog"></a>Como é que começo com o Catálogo de Dados?
A melhor maneira de começar é indo para [começar com o Catálogo de Dados.](data-catalog-get-started.md) Este artigo é uma visão geral das capacidades do serviço.

## <a name="how-do-i-register-my-data"></a>Como registo os meus dados?
Para registar os seus dados no Catálogo de Dados:
1. No portal Azure Data Catalog, na área **de Publicação,** inicie a ferramenta de registo do Catálogo de Dados Azure. 
2. Na ferramenta de registo de fonte de dados do Catálogo de Dados, inscreva-se com as mesmas credenciais que utiliza para aceder ao portal do Catálogo de Dados.
3. Selecione a fonte de dados e os ativos específicos que pretende registar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Que propriedades extrai para os ativos de dados registados?
As propriedades específicas diferem da fonte de dados para a fonte de dados mas, em geral, o serviço de publicação do Catálogo de Dados extrai as seguintes informações:

* Nome do ativo
* Tipo de Recurso
* Descrição do ativo
* Nomes de atributos/colunas
* Tipos de dados de atributo/coluna
* Descrição do atributo/coluna

> [!IMPORTANT]
> Registar ativos de dados com o Catálogo de Dados não move nem copia os seus dados para a nuvem. O registo de ativos a partir de uma fonte de dados copia os metadados dos ativos para o Azure, mas os dados permanecem na localização de fonte de dados existente. A exceção a esta regra é se optar por carregar registos de pré-visualização ou um perfil de dados quando registar os ativos. Quando se inclui uma pré-visualização, até 20 registos são copiados de cada ativo e armazenados como instantâneo no Catálogo de Dados. Quando se inclui um perfil de dados, a informação agregada é calculada e incluída nos metadados armazenados no catálogo. A informação agregada pode incluir o tamanho das tabelas, a percentagem de valores nulos por coluna, ou os valores mínimos, máximos e médios para colunas. 
>
>

> [!NOTE]
> Para fontes de dados como os SQL Server Analysis Services que possuem uma propriedade de **descrição** de primeira classe, a ferramenta de registo de fonte de dados do Catálogo de Dados extrai esse valor da propriedade. Para bases *de dados* relacionais do SQL Server que não dispõem de uma propriedade **descritiva** de primeira classe, a ferramenta de registo de fonte de dados do Catálogo de Dados extrai o valor da **propriedade ms_description** estendida para objetos e colunas. Esta propriedade não é suportada para SQL Azure. Para obter mais informações, consulte [a utilização de propriedades estendidas em objetos de base de dados.](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105))
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Quanto tempo deve demorar a aparecer no catálogo de novos ativos registados?
Depois de registar ativos com o Catálogo de Dados, pode haver um período de 5 a 10 segundos antes de aparecerem no portal do Catálogo de Dados.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Como anotar e enriquecer os metadados dos meus ativos de dados registados?
A forma mais simples de fornecer metadados para ativos registados é selecionar o ativo no portal do Catálogo de Dados e, em seguida, introduzir os valores no painel de propriedades ou painel de esquemas para o objeto selecionado.

Também pode fornecer alguns metadados, como especialistas e tags, durante o processo de registo. Os valores que fornece no serviço de publicação do Catálogo de Dados aplicam-se a todos os ativos registados nessa altura. Para visualizar os objetos recentemente registados no portal para anotação adicional, selecione o botão **'Ver Portal'** no ecrã final da ferramenta de registo de fonte de dados do Catálogo de Dados.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Como apago os meus objetos de dados registados?
Pode eliminar um objeto do Catálogo de Dados selecionando o objeto no portal e, em seguida, clicando no botão **Eliminar.** A remoção do objeto remove os seus metadados do Catálogo de Dados, mas não afeta a fonte de dados subjacente.

## <a name="what-is-an-expert"></a>O que é um perito?
Um perito é uma pessoa que tem uma perspetiva informada sobre um objeto de dados. Um objeto pode ter vários especialistas. Um especialista não precisa de ser o "proprietário" de um objeto, mas é simplesmente alguém que sabe como os dados podem e devem ser usados.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Como partilho informações com a equipa do Catálogo de Dados se encontro problemas?
Para relatar problemas, partilhar informações e fazer perguntas, vá ao [fórum do Catálogo de Dados do Azure.](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>O catálogo funciona com outra fonte de dados que me interessa?
Estamos a trabalhar ativamente na adição de mais fontes de dados ao Catálogo de Dados. Se quiser ver uma fonte de dados específica apoiada, sugira-a (ou expresse o seu suporte se já tiver sido sugerida) indo ao Catálogo de [Dados nos Fóruns de Feedback Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Que permissões preciso para registar ativos com o Catálogo de Dados?
Para executar a ferramenta de registo do Catálogo de Dados, necessita de permissões na fonte de dados que lhe permitem ler os metadados da fonte. Para incluir também uma pré-visualização, tem de ter permissões que lhe permitem ler os dados dos objetos registados.

O Catálogo de Dados também permite que os administradores de catálogos restringam quais os utilizadores e grupos que podem adicionar metadados ao catálogo. Para obter informações adicionais, consulte [como garantir o acesso ao catálogo de dados e aos ativos de dados.](data-catalog-how-to-secure-catalog.md)

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>O Catálogo de Dados também será disponibilizado para implantação no local?
O Data Catalog é um serviço de nuvem que pode funcionar com fontes de dados em nuvem e no local para fornecer uma solução híbrida de descoberta de fonte de dados. Atualmente não existem planos para uma versão do serviço data catalog que funciona no local.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Posso extrair metadados mais ou mais ricos das fontes de dados que registo?
Estamos a trabalhar ativamente para expandir as capacidades do Catálogo de Dados. Se pretender extrair metadados adicionais da fonte de dados durante o registo, sugira-os (ou votem nele, se já tiver sido sugerido) no Catálogo de [Dados dos Fóruns de Feedback Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Se pretender incluir metadados de coluna/esquema, pré-visualizações ou perfis de dados, para fontes de dados em que estes metadados não são extraídos pela ferramenta de registo de fonte de dados, pode utilizar a API do Catálogo de Dados para adicionar estes metadados. Para obter informações adicionais, consulte [a AZure Data Catalog REST API](/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Como posso restringir a visibilidade dos ativos de dados registados, para que apenas certas pessoas os possam descobrir?
Selecione os ativos de dados no Catálogo de Dados e, em seguida, clique no botão **Tomar Posse.** Os proprietários de ativos de dados no Catálogo de Dados podem alterar as definições de visibilidade para permitir que todos os utilizadores descubram os ativos detidos ou restringir a visibilidade a utilizadores específicos. Para obter informações adicionais, consulte [Gerir os ativos de dados no Catálogo de Dados Azure.](data-catalog-how-to-manage.md)

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Como au atualizar o registo de um ativo de dados para que as alterações na fonte de dados sejam refletidas no catálogo?
Para atualizar os metadados para os ativos de dados que já estão registados no catálogo, basta re-registar a fonte de dados que contém os ativos. Quaisquer alterações na fonte de dados, como colunas adicionadas ou removidas de tabelas ou vistas, são atualizadas no catálogo, mas quaisquer anotações fornecidas pelos utilizadores são mantidas.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A minha pergunta não é respondida aqui. Onde posso ir para obter respostas?
Vá ao [fórum do Catálogo de Dados Azure.](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) As perguntas feitas lá encontrarão o seu caminho para cá.