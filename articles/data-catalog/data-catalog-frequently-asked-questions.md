---
title: Perguntas frequentes sobre o catálogo de dados do Azure
description: Perguntas frequentes sobre o catálogo de dados do Azure, incluindo recursos para descoberta, anotação e gerenciamento de fonte de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 2e286854b9a38c1189ff85307f3e29454be46fb9
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898721"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Perguntas frequentes sobre o catálogo de dados do Azure
Este artigo fornece respostas para perguntas frequentes relacionadas ao serviço do catálogo de dados do Azure.

## <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
O catálogo de dados é um serviço totalmente gerenciado, hospedado em Microsoft Azure, que serve como um sistema de registro e descoberta para fontes de dados empresariais. Com o catálogo de dados, qualquer usuário, de analistas a cientistas de dados e desenvolvedores, pode registrar, descobrir, compreender e consumir fontes de dados.

## <a name="what-customer-challenges-does-it-solve"></a>Quais desafios do cliente ele resolve?
O catálogo de dados aborda os desafios de descoberta de fonte de dados e "dados escuros" para que os usuários possam descobrir e entender as fontes de dados empresariais.

## <a name="what-are-its-target-audiences"></a>Quais são seus públicos-alvo?
O catálogo de dados foi projetado para usuários técnicos e não técnicos, incluindo:

* Desenvolvedores de dados e profissionais de BI e de análise: Pessoas responsáveis por produzir conteúdo de dados e análises para que outras pessoas consumam.
* Administradores de dados: Pessoas que têm o conhecimento sobre os dados, o que isso significa e como eles devem ser usados.
* Consumidores de dados: As pessoas que precisam ser capazes de descobrir, entender e conectar-se facilmente aos dados de que precisam para realizar seu trabalho, usando a ferramenta de sua escolha.
* TI central: As pessoas que precisam tornar centenas de fontes de dados detectáveis por usuários empresariais e que precisam manter a supervisão sobre como os dados estão sendo usados e por quem.

## <a name="what-is-its-availability-by-region"></a>Qual é sua disponibilidade por região?
Os serviços do catálogo de dados estão disponíveis no momento nos seguintes data centers:

* EUA Oeste
* East US
* Europa Ocidental
* Europa do Norte
* Leste da Austrália
* Sudeste Asiático

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quais são seus limites no número de ativos de dados?
A edição gratuita do catálogo de dados é limitada a 5.000 ativos de dados registrados.

A edição Standard do catálogo de dados dá suporte a até 100.000 ativos de dados registrados.

Qualquer objeto registrado no catálogo de dados, como tabelas, exibições, arquivos e relatórios, conta como um ativo de dados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quais são seus tipos de ativos e fonte de dados com suporte?
Para obter uma lista de fontes de dados com suporte no momento, consulte [Catálogo de dados DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Como fazer solicitar suporte para outra fonte de dados?
Para enviar solicitações de recursos e outros comentários, vá para o [Catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Por que eu obtenho um catálogo de erros *já existe* quando tento criar um novo catálogo?

Quando você adquire o Office 365 E5 com licença Power BI Pro, a Microsoft cria um catálogo padrão na região da assinatura automaticamente. Este catálogo usa o SKU gratuito. A licença de usuário do Office 365/Power BI é gerenciada na página de administração do Office 365. 

No entanto, esse tipo de catálogo de dados não tem uma **opção de administrador** e não é visível no **portal do Azure**. Você não pode excluir este tipo de catálogo de dados. Da mesma forma, você não tem permissão para renomear o catálogo de dados e não pode movê-lo para outra região. 

Contas de usuários que recebem uma licença de Power BI Pro automática têm acesso ao catálogo de dados devido ao contrato de licença quando eles se inscreveram no Office 365 E5 com a licença de Power BI Pro. Esse tipo de usuário tem acesso completo aos ativos do catálogo de dados sem privilégios administrativos. Esse tipo de usuário *não* faz parte da função de **usuário de catálogo** no catálogo de dados do Azure.


## <a name="how-do-i-get-started-with-data-catalog"></a>Como fazer introdução ao catálogo de dados?
A melhor maneira de começar é [introdução com o catálogo de dados](data-catalog-get-started.md). Este artigo é uma visão geral de ponta a ponta dos recursos do serviço.

## <a name="how-do-i-register-my-data"></a>Como fazer registrar meus dados?
Para registrar seus dados no catálogo de dados:
1. No portal do catálogo de dados do Azure, na área **publicar** , inicie a ferramenta de registro do catálogo de dados do Azure. 
2. Na ferramenta de registro de fonte de dados do catálogo de dados, entre com as mesmas credenciais que você usa para acessar o portal do catálogo de dados.
3. Selecione a fonte de dados e os ativos específicos que você deseja registrar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Quais propriedades ele extrai para ativos de dados que são registrados?
As propriedades específicas diferem da fonte de dados para a fonte de dados, mas, em geral, o serviço de publicação do catálogo de dados extrai as seguintes informações:

* Nome do ativo
* Tipo de Recurso
* Descrição do ativo
* Nomes de atributo/coluna
* Tipos de dados de atributo/coluna
* Descrição do atributo/coluna

> [!IMPORTANT]
> O registro de ativos de dados com o catálogo de dados não move nem copia seus dados para a nuvem. O registro de ativos de uma fonte de dados copia os metadados dos ativos para o Azure, mas os dados permanecem no local da fonte de dados existente. A exceção a essa regra é se você optar por carregar registros de visualização ou um perfil de dados ao registrar os ativos. Quando você inclui uma visualização, até 20 registros são copiados de cada ativo e armazenados como um instantâneo no catálogo de dados. Quando você inclui um perfil de dados, as informações de agregação são calculadas e incluídas nos metadados armazenados no catálogo. As informações de agregação podem incluir o tamanho das tabelas, a porcentagem de valores nulos por coluna ou os valores mínimo, máximo e médio para colunas. 
>
>

> [!NOTE]
> Para fontes de dados como SQL Server Analysis Services que têm uma propriedade de **Descrição** de primeira classe, a ferramenta de registro de fonte de dados do catálogo de dados extrai esse valor de propriedade. Para SQL Server bancos de dados relacionais, que não têm uma propriedade de **Descrição** de primeira classe, a ferramenta de registro de fonte de dados do catálogo de dados extrai o valor da propriedade estendida **MS_Description** para objetos e colunas. Para obter mais informações, consulte [usando propriedades estendidas em objetos de banco de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Quanto tempo deve levar para que os ativos recentemente registrados apareçam no catálogo?
Depois de registrar ativos com o catálogo de dados, pode haver um período de 5 a 10 segundos antes que eles apareçam no portal do catálogo de dados.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Como fazer anotar e enriquecer os metadados para meus ativos de dados registrados?
A maneira mais simples de fornecer metadados para ativos registrados é selecionar o ativo no portal do catálogo de dados e, em seguida, inserir os valores no painel de propriedades ou no painel de esquema do objeto selecionado.

Você também pode fornecer alguns metadados, como especialistas e marcas, durante o processo de registro. Os valores que você fornece no serviço de publicação do catálogo de dados se aplicam a todos os ativos que estão sendo registrados nesse momento. Para exibir os objetos registrados recentemente no portal para anotação adicional, selecione o botão **Exibir portal** na tela final da ferramenta de registro de fonte de dados do catálogo de dados.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Como fazer excluir meus objetos de dados registrados?
Você pode excluir um objeto do catálogo de dados selecionando o objeto no portal e clicando no botão **excluir** . A remoção do objeto remove seus metadados do catálogo de dados, mas não afeta a fonte de dados subjacente.

## <a name="what-is-an-expert"></a>O que é um especialista?
Um especialista é uma pessoa que tem uma perspectiva informada sobre um objeto de dados. Um objeto pode ter vários especialistas. Um especialista não precisa ser o "proprietário" de um objeto, mas é simplesmente alguém que sabe como os dados podem e devem ser usados.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Como fazer compartilhar informações com a equipe do catálogo de dados se encontrar problemas?
Para relatar problemas, compartilhar informações e fazer perguntas, vá para o [Fórum do catálogo de dados do Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>O catálogo funciona com outra fonte de dados na qual estou interessado?
Estamos trabalhando ativamente para adicionar mais fontes de dados ao catálogo de dados. Se você quiser ver uma fonte de dados específica com suporte, sugira-a (ou o suporte de voz se ela já tiver sido sugerida) acessando o [Catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Quais permissões preciso para registrar ativos com o catálogo de dados?
Para executar a ferramenta de registro de catálogo de dados, você precisa de permissões na fonte de dados que permite ler os metadados da origem. Para incluir também uma visualização, você deve ter permissões que permitam que você leia os dados dos objetos que estão sendo registrados.

O catálogo de dados também permite que os administradores de catálogo restrinjam quais usuários e grupos podem adicionar metadados ao catálogo. Para obter informações adicionais, consulte [como proteger o acesso ao catálogo de dados e ativos de dados](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>O catálogo de dados também será disponibilizado para implantação local?
O catálogo de dados é um serviço de nuvem que pode funcionar com fontes de dados locais e na nuvem para fornecer uma solução de descoberta de fonte de dados híbrida. Atualmente, não há planos para uma versão do serviço de catálogo de dados que é executada localmente.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Posso extrair metadados mais ou mais ricos das fontes de dados que eu registro?
Estamos trabalhando ativamente para expandir os recursos do catálogo de dados. Se você quiser ter metadados adicionais extraídos da fonte de dados durante o registro, sugira-os (ou vote nele, se já tiver sido sugerido) no [Catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Se você quiser incluir metadados de coluna/esquema, visualizações ou perfis de dados, para fontes de dados em que esses metadados não são extraídos pela ferramenta de registro de fonte de dados, você pode usar a API do catálogo de dados para adicionar esses metadados. Para obter informações adicionais, consulte [API REST do catálogo de dados do Azure](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Como fazer restringir a visibilidade de ativos de dados registrados, para que apenas determinadas pessoas possam descobri-los?
Selecione os ativos de dados no catálogo de dados e, em seguida, clique no botão **apropriar-se** . Os proprietários de ativos de dados no catálogo de dados podem alterar as configurações de visibilidade para permitir que todos os usuários descubram os ativos de propriedade ou restringir a visibilidade a usuários específicos. Para obter informações adicionais, consulte [gerenciar ativos de dados no catálogo de dados do Azure](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Como fazer atualizar o registro de um ativo de dados para que as alterações na fonte de dados sejam refletidas no catálogo?
Para atualizar os metadados de ativos de dados que já estão registrados no catálogo, basta registrar novamente a fonte de dados que contém os ativos. As alterações na fonte de dados, como colunas que estão sendo adicionadas ou removidas de tabelas ou exibições, são atualizadas no catálogo, mas todas as anotações fornecidas pelos usuários são mantidas.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Minha pergunta não é respondida aqui. Onde posso obter respostas?
Vá para o [Fórum do catálogo de dados do Azure](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). As perguntas feitas aqui vão encontrá-las aqui.
