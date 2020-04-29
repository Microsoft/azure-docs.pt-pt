---
title: Catálogo de Dados Azure frequentemente fez perguntas
description: Perguntas frequentes sobre o Catálogo de Dados Azure, incluindo capacidades para a descoberta de fontes de dados, anotação e gestão.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7dbb1b4a8b85350b8bf8a6df0c9035a19055444c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409024"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Catálogo de Dados Azure frequentemente fez perguntas
Este artigo fornece respostas a perguntas frequentes relacionadas com o serviço de Catálogo de Dados Azure.

## <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
Data Catalog é um serviço totalmente gerido, hospedado no Microsoft Azure, que serve como um sistema de registo e descoberta para fontes de dados da empresa. Com o Data Catalog, qualquer utilizador, desde analistas a cientistas de dados e desenvolvedores, pode registar, descobrir, compreender e consumir fontes de dados.

## <a name="what-customer-challenges-does-it-solve"></a>Que desafios para o cliente resolve?
O Data Catalog aborda os desafios da descoberta de fontes de dados e de "dados escuros" para que os utilizadores possam descobrir e compreender as fontes de dados da empresa.

## <a name="what-are-its-target-audiences"></a>Quais são os seus públicos-alvo?
O Data Catalog foi concebido para utilizadores técnicos e não técnicos, incluindo:

* Desenvolvedores de dados e profissionais de BI e analítica: Pessoas responsáveis pela produção de dados e conteúdos de análise para outros consumirem.
* Comissários de dados: Pessoas que têm conhecimento sobre os dados, o que significa e como se destinam a ser usados.
* Consumidores de dados: Pessoas que precisam de ser capazes de descobrir, compreender e conectar facilmente aos dados de que precisam para fazer o seu trabalho, utilizando a ferramenta da sua escolha.
* TI central: Pessoas que precisam de tornar centenas de fontes de dados descobertas pelos utilizadores das empresas, e que precisam manter a supervisão sobre como os dados estão a ser usados e por quem.

## <a name="what-is-its-availability-by-region"></a>Qual é a sua disponibilidade por região?
Os serviços de Catálogo de Dados estão atualmente disponíveis nos seguintes centros de dados:

* E.U.A. Oeste
* E.U.A. Leste
* Europa ocidental
* Europa do Norte
* Leste da Austrália
* Ásia Sudeste

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quais são os seus limites no número de ativos de dados?
A Edição Gratuita do Catálogo de Dados está limitada a 5.000 ativos de dados registados.

A Edição Standard do Catálogo de Dados suporta até 100.000 ativos de dados registados.

Qualquer objeto registado no Data Catalog, tais como tabelas, vistas, ficheiros e relatórios, conta como um ativo de dados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quais são os seus tipos de dados suportados de origem de dados e ativos?
Para obter uma lista de fontes de dados atualmente suportadas, consulte data [Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Como solicito apoio a outra fonte de dados?
Para submeter pedidos de funcionalidades e outros feedbacks, vá ao Catálogo de Dados nos Fóruns de [Feedback do Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Porque é que recebo um catálogo de erros *já existe* quando tento criar um novo catálogo?

Ao adquirir o Office 365 E5 com licença Power BI Pro, a Microsoft cria automaticamente um catálogo predefinido na região da subscrição. Este catálogo utiliza o SKU gratuito. A licença de utilizador do Office 365 / Power BI é gerida na página de Administração do Office 365. 

No entanto, este tipo de catálogo de dados não tem **opção** de administrador e não é visível no **portal Azure**. Não é possível eliminar este tipo de catálogo de dados. Da mesma forma, não é permitido mudar o nome do catálogo de dados, e não pode movê-lo para outra região. 

As contas dos utilizadores que lhe são atribuídas uma licença Power BI Pro automática têm acesso ao catálogo de dados devido ao Contrato de Licença quando se inscreveram no Office 365 E5 com a Licença Power BI Pro. Este tipo de utilizador tem acesso total a ativos de catálogo de dados sem privilégios administrativos. Este tipo de utilizador *não* faz parte da função de Utilizador do **Catálogo** no Catálogo de Dados do Azure.


## <a name="how-do-i-get-started-with-data-catalog"></a>Como posso começar com o Data Catalog?
A melhor maneira de começar é indo para [Começar com Catálogo de Dados.](data-catalog-get-started.md) Este artigo é uma visão geral das capacidades do serviço.

## <a name="how-do-i-register-my-data"></a>Como registo os meus dados?
Para registar os seus dados no Catálogo de Dados:
1. No portal Azure Data Catalog, na área **de Publicação,** inicie a ferramenta de registo do Catálogo de Dados Azure. 
2. Na ferramenta de registo de fonte de dados do Data Catalog, inscreva-se com as mesmas credenciais que utiliza para aceder ao portal Data Catalog.
3. Selecione a fonte de dados e os ativos específicos que pretende registar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Que propriedades extrai para os ativos de dados registados?
As propriedades específicas diferem de fonte de dados para fonte de dados, mas, em geral, o serviço de publicação do Data Catalog extrai as seguintes informações:

* Nome do ativo
* Tipo de Ativo
* Descrição do ativo
* Nomes de atributo/coluna
* Tipos de dados de atributo/coluna
* Descrição do atributo/coluna

> [!IMPORTANT]
> Registar ativos de dados com data Catalog não move nem copia os seus dados para a nuvem. O registo de ativos de uma fonte de dados copia os metadados dos ativos para o Azure, mas os dados permanecem na localização de fonte de dados existente. A exceção a esta regra é se optar por fazer o upload de registos de pré-visualização ou um perfil de dados quando registar os ativos. Quando inclui uma pré-visualização, até 20 registos são copiados de cada ativo e armazenados como instantâneo no Data Catalog. Quando inclui um perfil de dados, a informação agregada é calculada e incluída nos metadados armazenados no catálogo. As informações agregadas podem incluir o tamanho das tabelas, a percentagem de valores nulos por coluna, ou os valores mínimos, máximos e médios para colunas. 
>
>

> [!NOTE]
> Para fontes de dados como os Serviços de Análise de Servidores SQL que possuem uma propriedade de primeira classe **Descrição,** a ferramenta de registo de fonte de dados do Catálogo de Dados extrai esse valor de propriedade. Para bases de dados relacionais do SQL Server *no local* que carecem de uma propriedade de primeira classe **Descrição,** a ferramenta de registo de dados do Data Catalog extrai o valor da **propriedade ms_description** estendida para objetos e colunas. Esta propriedade não é suportada para o SQL Azure. Para mais informações, consulte [a utilização de propriedades estendidas em objetos de base de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Quanto tempo deve levar para que os ativos recém-registados apareçam no catálogo?
Depois de registar ativos com data catalog, pode haver um período de 5 a 10 segundos antes de aparecerem no portal data Catalog.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Como anotar e enriquecer os metadados para os meus dados registados?
A forma mais simples de fornecer metadados para os ativos registados é selecionar o ativo no portal Data Catalog e, em seguida, introduzir os valores no painel de propriedades ou no painel de esquemas para o objeto selecionado.

Também pode fornecer alguns metadados, como especialistas e tags, durante o processo de registo. Os valores que fornece no serviço de publicação data Catalog aplicam-se a todos os ativos registados nessa altura. Para visualizar os objetos recentemente registados no portal para anotação adicional, selecione o botão **'Porta',** no ecrã final da ferramenta de registo de fonte de dados do Data Catalog.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Como apago os meus objetos de dados registados?
Pode eliminar um objeto do Data Catalog selecionando o objeto no portal e clicando no botão **Eliminar.** A remoção do objeto remove os seus metadados do Data Catalog, mas não afeta a fonte de dados subjacente.

## <a name="what-is-an-expert"></a>O que é um perito?
Um especialista é uma pessoa que tem uma perspetiva informada sobre um objeto de dados. Um objeto pode ter vários especialistas. Um especialista não precisa de ser o "proprietário" de um objeto, mas é simplesmente alguém que sabe como os dados podem e devem ser usados.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Como partilho informações com a equipa do Catálogo de Dados se encontro problemas?
Para relatar problemas, partilhar informações e fazer perguntas, vá ao fórum do Catálogo de [Dados Azure.](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>O catálogo funciona com outra fonte de dados que me interessa?
Estamos a trabalhar ativamente na adição de mais fontes de dados ao Data Catalog. Se quiser ver uma fonte específica de dados suportada, sugira que (ou voice o seu suporte se já foi sugerido) indo ao Catálogo de Dados nos Fóruns de [Feedback do Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Que permissões preciso para registar ativos no Data Catalog?
Para executar a ferramenta de registo do Data Catalog, necessita de permissões na fonte de dados que lhe permite ler os metadados a partir da fonte. Para incluir também uma pré-visualização, deve ter permissões que lhe permitam ler os dados dos objetos que estão a ser registados.

O Data Catalog também permite que os administradores de catálogo limitem quais os utilizadores e grupos que podem adicionar metadados ao catálogo. Para obter informações adicionais, consulte como garantir o acesso ao catálogo de [dados e aos ativos de dados.](data-catalog-how-to-secure-catalog.md)

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>O Data Catalog também será disponibilizado para a implantação no local?
Data Catalog é um serviço na nuvem que pode trabalhar com fontes de dados em nuvem e no local para fornecer uma solução híbrida de descoberta de fontede dados. Atualmente não existem planos para uma versão do serviço Data Catalog que funciona no local.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Posso extrair mais ou mais metadados das fontes de dados que registo?
Estamos a trabalhar ativamente para expandir as capacidades do Data Catalog. Se pretender extrair metadados adicionais da fonte de dados durante o registo, sugira-os (ou votem a favor, se já foi sugerido) no Catálogo de Dados dos Fóruns de [Feedback do Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Se quiser incluir metadados de coluna/esquema, pré-visualizações ou perfis de dados, para fontes de dados onde estes metadados não sejam extraídos pela ferramenta de registo de fonte de dados, pode utilizar a API do Catálogo de Dados para adicionar estes metadados. Para mais informações, consulte o Catálogo de [Dados Azure REST API](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Como restringir a visibilidade dos ativos de dados registados, para que apenas certas pessoas os descubram?
Selecione os ativos de dados no Catálogo de Dados e, em seguida, clique no botão **Tomar A Propriedade.** Os proprietários de ativos de dados no Data Catalog podem alterar as definições de visibilidade para permitir que todos os utilizadores descubram os ativos detidos ou restrinjam a visibilidade a utilizadores específicos. Para obter informações adicionais, consulte Gerir os ativos de dados no Catálogo de [Dados do Azure](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Como actualizao o registo de um ativo de dados para que as alterações na fonte de dados se reflitam no catálogo?
Para atualizar os metadados para os ativos de dados que já estão registados no catálogo, basta reregistar a fonte de dados que contém os ativos. Quaisquer alterações na fonte de dados, tais como colunas adicionadas ou removidas de tabelas ou vistas, são atualizadas no catálogo, mas quaisquer anotações fornecidas pelos utilizadores são mantidas.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A minha pergunta não é respondida aqui. Onde posso ir à procura de respostas?
Vá ao fórum do Catálogo de [Dados Azure.](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) As perguntas feitas lá vão encontrar o caminho para cá.
