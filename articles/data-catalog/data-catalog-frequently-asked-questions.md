---
title: Perguntas mais frequentes sobre o catálogo de dados do Azure
description: Perguntas mais frequentes sobre o catálogo de dados do Azure, incluindo capacidades de deteção de origens de dados, anotação e gestão.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 7c5241b9df23bb0334a39f2c684fd1bdff40b4c2
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998460"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Perguntas mais frequentes sobre o catálogo de dados do Azure
Este artigo fornece respostas para perguntas freqüentes relacionadas com o serviço de catálogo de dados do Azure.

## <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
Catálogo de dados é um serviço totalmente gerido, alojado no Microsoft Azure, que funciona como um sistema de registo e deteção de origens de dados empresariais. Com o catálogo de dados, qualquer utilizador, desde analistas a cientistas de dados e programadores, pode registar, detetar, compreender e consumir origens de dados.

## <a name="what-customer-challenges-does-it-solve"></a>Desafios que customer faz isso resolver?
Catálogo de dados face aos desafios de deteção de origem de dados e de "dados menos claros", para que os utilizadores podem detetar e compreender origens de dados empresariais.

## <a name="what-are-its-target-audiences"></a>Quais são seus públicos-alvo?
Catálogo de dados foi concebido para utilizadores técnicos e, incluindo:

* Os desenvolvedores de dados e os profissionais de BI e análise: Pessoas que são responsáveis por produzir conteúdo de dados e análise para outras pessoas consumir.
* Stewards de dados: Pessoas que possuem o conhecimento sobre os dados, o que significa que e como ele deve ser usado.
* Consumidores de dados: As pessoas que precisam de ser capaz de facilmente detetar, compreender e ligar aos dados que precisam para realizar o trabalho, utilizando a ferramenta de sua preferência.
* Central IT: Pessoas que precisa para facilitar a centenas de origens de dados detetável por utilizadores empresariais e que precisam de manter a supervisão sobre como os dados estiverem sendo usados e por quem.

## <a name="what-is-its-availability-by-region"></a>O que é a sua disponibilidade por região?
Serviços de catálogo de dados estão atualmente disponíveis nos seguintes centros de dados:

* EUA Oeste
* EUA Leste
* Europa Ocidental
* Europa do Norte
* Leste da Austrália
* Sudeste Asiático

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quais são seus limites no número de ativos de dados?
Edição gratuita do catálogo de dados está limitada a recursos de dados registados 5000.

Edição Standard do catálogo de dados suporta até 100.000 recursos de dados registados.

Qualquer objeto registado no catálogo de dados, como tabelas, vistas, ficheiros e relatórios, contabilizado como um recurso de dados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quais são os tipos de recursos e de origem de dados suportados?
Para obter uma lista de origens de dados atualmente suportadas, consulte [DSR do catálogo de dados](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Como posso pedir suporte para outra origem de dados?
Para submeter pedidos de funcionalidades e outros comentários, vá para o [catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Como posso começar a utilizar com o catálogo de dados?
A melhor maneira de começar é acedendo a [introdução ao catálogo de dados](data-catalog-get-started.md). Este artigo é uma visão geral de ponto-a-ponto dos recursos no serviço.

## <a name="how-do-i-register-my-data"></a>Como me Registro meus dados?
Para registar os seus dados no catálogo de dados:
1. No portal do catálogo de dados do Azure, no **publicar** área, iniciar a ferramenta de registo do catálogo de dados do Azure. 
2. Na ferramenta registo de origem de dados do catálogo de dados, inicie sessão com as mesmas credenciais que utilizou para aceder ao portal do catálogo de dados.
3. Selecione a origem de dados e os recursos específicos que pretende registar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>As propriedades que isso extrair para recursos de dados que estão registados?
As propriedades específicas diferem de origem de dados à origem de dados, mas, em geral, o serviço de publicação do catálogo de dados extrai as seguintes informações:

* Nome do elemento
* Tipo de Recurso
* Descrição do recurso
* Nomes de atributo/colunas
* Tipos de dados de atributo/colunas
* Descrição do atributo/colunas

> [!IMPORTANT]
> Registar recursos de dados no catálogo de dados não mover ou copiar os dados para a cloud. Registar ativos de uma origem de dados copia os metadados dos ativos para o Azure, mas os dados permanecem na localização de origem de dados existente. A exceção a esta regra é se optar por carregar os registos de pré-visualização ou um perfil de dados ao registar os ativos. Quando inclui uma pré-visualização, até 20 registos são copiados de cada ativo e armazenados como instantâneos no catálogo de dados. Ao incluir um perfil de dados, agregam informações são calculadas e incluídas nos metadados armazenados no catálogo. Agregam informações podem incluir o tamanho de tabelas, a porcentagem de valores nulos por coluna ou os valores de mínimos, máximo e médios para colunas. 
>
>

> [!NOTE]
> Para origens de dados, como o SQL Server Analysis Services que tenha uma primeira classe **Descrição** propriedade, a ferramenta de registo de origem de dados do catálogo de dados extrai esse valor de propriedade. Para bases de dados relacionais do SQL Server, que não têm uma primeira classe **Descrição** propriedade, a ferramenta de registo de origem de dados do catálogo de dados extrai o valor a partir do **ms_description** estendido de propriedade para objetos e colunas. Para obter mais informações, consulte [usando propriedades expandidas em objetos de base de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Quanto deve necessário para que os recursos recentemente registados para que sejam apresentadas no catálogo?
Depois de registar ativos no catálogo de dados, pode haver um período de 5 a 10 segundos antes de aparecerem no portal do catálogo de dados.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Como anotar e enriquecer os metadados para os meus recursos de dados registados?
É a forma mais simples para fornecer metadados para recursos registados selecionar o elemento no portal do catálogo de dados e, em seguida, introduza os valores no painel de propriedades ou painel de esquema para o objeto selecionado.

Também pode fornecer alguns metadados, como especialistas e etiquetas, durante o processo de registo. Os valores que fornecer no serviço de publicação do catálogo de dados se aplicam a todos os recursos a ser registados nessa altura. Para ver os objetos recentemente registados no portal para anotações adicionais, selecione o **ver Portal** botão no ecrã de final da ferramenta de registo de origem de dados do catálogo de dados.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Como faço para excluir meus objetos de dados registados?
Pode eliminar um objeto do catálogo de dados, selecionando o objeto no portal e, em seguida, clicando a **eliminar** botão. Remover o objeto remove seus metadados do catálogo de dados, mas não afeta a origem de dados subjacente.

## <a name="what-is-an-expert"></a>O que é um especialista?
Um especialista é uma pessoa que tem um ponto de vista informado sobre um objeto de dados. Um objeto pode ter muitos especialistas. Um especialista não precisa de ser o "proprietário" para um objeto, mas é simplesmente a alguém que sabe como os dados podem e devem ser utilizados.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Como posso partilhar informações com a equipa do catálogo de dados se eu encontrar problemas?
Para reportar problemas, compartilhar informações e fazer perguntas, vá para o [fórum do catálogo de dados do Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>O catálogo funciona com outra origem de dados que estou interessado em?
Estamos a trabalhar ativamente sobre como adicionar mais origens de dados no catálogo de dados. Se quiser ver uma origem de dados específicos suportada, sugeri-lo (ou o suporte de voz, se já tiver sido sugerido) ao aceder a [catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Como é o catálogo de dados do Azure relacionada com o catálogo de dados no Power BI para Office 365?
Pode pensar o catálogo de dados do Azure como uma evolução do catálogo de dados no Power BI. A partir da Primavera de 2017, o catálogo de dados do Azure é utilizado para ativar a partilha e a deteção de consultas no Excel 2016 e o Power Query para Excel. Capacidades do catálogo de dados no Excel estão disponíveis para os utilizadores com licenças do Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Que permissões preciso registar recursos de catálogo de dados?
Para executar a ferramenta de registo do catálogo de dados, precisa de permissões na origem de dados que permite-lhe ler os metadados da origem. Para incluir também uma pré-visualização, tem de ter as permissões que permitem-lhe ler os dados de objetos a ser registados.

Catálogo de dados também permite que os administradores do catálogo restringir os utilizadores e grupos que podem adicionar metadados para o catálogo. Para obter mais informações, consulte [como proteger o acesso ao catálogo de dados e recursos de dados](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Catálogo de dados estarão disponível para também a implementação no local?
Catálogo de dados é um serviço cloud que pode trabalhar com origens de dados na cloud e no local para oferecer uma solução de deteção de origem de dados híbrida. Não existem atualmente não existem planos para uma versão do serviço catálogo de dados que é executado no local.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Pode extrair metadados mais rico ou mais das origens de dados que me Registro?
Estamos a trabalhar ativamente para expandir as capacidades do catálogo de dados. Se quiser ter metadados adicionais extraídos da origem de dados durante o registo, sugerimos (ou um voto para ele, se já tiver sido sugerido) no [catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Se gostaria de incluir metadados da coluna/esquema, pré-visualizações ou perfis de dados, para origens de dados onde estes metadados não é extraído pela ferramenta de registo de origem de dados, pode utilizar a API do catálogo de dados para adicionar estes metadados. Para obter mais informações, consulte [API de REST do catálogo de dados do Azure](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Como posso restringir a visibilidade dos recursos de dados registados, para que apenas determinadas pessoas podem detetá-los?
Selecione os recursos de dados no catálogo de dados e, em seguida, clique nas **obter propriedade** botão. Os proprietários de recursos de dados no catálogo de dados podem alterar as definições de visibilidade para optar por permitir que todos os utilizadores a detetar os recursos pertencentes à empresa ou restringir a visibilidade para utilizadores específicos. Para obter mais informações, consulte [gerir recursos de dados no catálogo de dados do Azure](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Como posso atualizar o registo para um recurso de dados para que as alterações na origem de dados são refletidas no catálogo?
Para atualizar os metadados para os recursos de dados que já estão registados no catálogo, simplesmente volte a registar a origem de dados que contém os ativos. Todas as alterações na origem de dados, como colunas a ser adicionadas ou removidas de tabelas ou vistas, são atualizadas no catálogo, mas qualquer anotações fornecidas pelos utilizadores são retidas.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Minha pergunta não respondida aqui. Onde posso ir para obter respostas?
Vá para o [fórum do catálogo de dados do Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Perguntas mais frequentes lá encontrarão seu caminho aqui.
