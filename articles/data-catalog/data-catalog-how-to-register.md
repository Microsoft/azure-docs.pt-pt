---
title: Registrar fontes de dados no catálogo de dados do Azure
description: Este artigo realça como registrar fontes de dados no catálogo de dados do Azure, incluindo os campos de metadados extraídos durante o registro.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736329"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrar fontes de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
O catálogo de dados do Azure é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e descoberta para fontes de dados empresariais. Em outras palavras, o catálogo de dados ajuda as pessoas a descobrir, entender e usar fontes de dados, além de ajudar as organizações a obter mais valor de seus dados existentes. A primeira etapa para tornar uma fonte de dados detectável por meio do catálogo de dados é registrar essa fonte de dados.

## <a name="register-data-sources"></a>Registar origens de dados
O registro é o processo de extração de metadados da fonte de dados e de cópia desses dados para o serviço de catálogo de dados. Os dados permanecem no local onde residem atualmente e continuam sob o controlo dos administradores e das políticas do sistema atual.

Para registrar uma fonte de dados, faça o seguinte:
1. No portal do catálogo de dados do Azure, inicie a ferramenta de registro de fonte de dados do catálogo de dados. 
2. Entre com sua conta corporativa ou de estudante com as mesmas credenciais de Azure Active Directory que você usa para entrar no Portal.
3. Selecione a fonte de dados que você deseja registrar.

Para obter mais detalhes passo a passo, consulte o tutorial [introdução ao catálogo de dados do Azure](data-catalog-get-started.md) .

Depois de registrar a fonte de dados, o catálogo rastreia seu local e indexa seus metadados. Os usuários podem pesquisar, procurar e descobrir a fonte de dados e, em seguida, usar seu local para se conectar a ele usando o aplicativo ou a ferramenta de sua escolha.

## <a name="supported-data-sources"></a>Origens de dados suportadas
Para obter uma lista de fontes de dados com suporte no momento, consulte [Catálogo de dados DSR](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadados estruturais
Quando você registra uma fonte de dados, a ferramenta de registro extrai informações sobre a estrutura dos objetos selecionados. Essas informações são chamadas de metadados estruturais.

Para todos os objetos, esses metadados estruturais incluem o local do objeto, para que os usuários que descobrirem os dados possam usar essas informações para se conectarem ao objeto nas ferramentas de cliente de sua escolha. Outros metadados estruturais incluem o nome e o tipo do objeto, bem como o nome do atributo/coluna e o tipo de dados.

## <a name="descriptive-metadata"></a>Metadados descritivos
Além dos metadados estruturais principais que são extraídos da fonte de dados, a ferramenta de registro de fonte de dados extrai metadados descritivos. Para SQL Server Analysis Services e SQL Server Reporting Services, esses metadados são obtidos das propriedades de descrição expostas por esses serviços. Por SQL Server, os valores fornecidos usando a\_propriedade estendida MS Description são extraídos. Por Oracle Database, a ferramenta de registro de fonte de dados extrai a coluna comentários da exibição\_todos\_os comentários guia.

Além dos metadados descritivos extraídos da fonte de dados, os usuários podem inserir metadados descritivos usando a ferramenta de registro de fonte de dados. Os usuários podem adicionar marcas e podem identificar especialistas para os objetos que estão sendo registrados. Todos esses metadados descritivos são copiados para o serviço de catálogo de dados junto com os metadados estruturais.

## <a name="include-previews"></a>Incluir visualizações
Por padrão, somente os metadados são extraídos das fontes de dados e copiados para o serviço de catálogo de dados, mas a compreensão de uma fonte de dados geralmente é mais fácil quando você pode exibir uma amostra dos dados que ela contém.

Usando a ferramenta de registro de fonte de dados do catálogo de dados, você pode incluir uma visualização de instantâneo dos dados em cada tabela e exibição registrada. Se você optar por incluir visualizações durante o registro, a ferramenta de registro incluirá até 20 registros de cada tabela e exibição. Esse instantâneo é copiado para o catálogo junto com os metadados estruturais e descritivos.

> [!NOTE]
> Tabelas largas com um grande número de colunas podem ter menos de 20 registros incluídos em sua visualização.
>
>

## <a name="include-data-profiles"></a>Incluir perfis de dados
Assim como a inclusão de visualizações pode fornecer um contexto valioso para os usuários que pesquisam fontes de dados no catálogo de dados, a inclusão de um perfil de dados pode facilitar a compreensão das fontes de dados descobertas.

Usando a ferramenta de registro de fonte de dados do catálogo de dados, você pode incluir um perfil de dados para cada tabela e exibição registrada. Se você optar por incluir um perfil de dados durante o registro, a ferramenta de registro incluirá estatísticas de agregação sobre os dados em cada tabela e exibição, incluindo:

* O número de linhas e o tamanho dos dados no objeto.
* A data para a atualização mais recente dos dados e o esquema de objeto.
* O número de registros nulos e valores distintos para colunas.
* Os valores de desvio mínimo, máximo, médio e padrão para colunas.

Essas estatísticas são então copiadas para o catálogo junto com os metadados estruturais e descritivos.

> [!NOTE]
> As colunas de texto e de data não incluem estatísticas de desvio médio ou padrão em seu perfil de dados.
>
>

## <a name="update-registrations"></a>Atualizar registros
O registro de uma fonte de dados torna-o detectável no catálogo de dados quando você usa os metadados e a visualização opcional extraída durante o registro. Se a fonte de dados precisar ser atualizada no catálogo (por exemplo, se o esquema de um objeto tiver sido alterado, as tabelas originalmente excluídas deverão ser incluídas ou você desejar atualizar os dados que estão incluídos nas visualizações), a ferramenta de registro de fonte de dados poderá ser executada novamente.

O novo registro de uma fonte de dados já registrada executa uma operação de mesclagem "Upsert": os objetos existentes são atualizados e novos objetos são criados. Todos os metadados fornecidos pelos usuários por meio do portal do catálogo de dados são mantidos.

## <a name="summary"></a>Resumo
Como ele copia metadados estruturais e descritivos de uma fonte de dados para o serviço de catálogo, registrar a fonte de dados no catálogo de dados torna os dados mais fáceis de descobrir e entender. Depois de registrar a fonte de dados, você pode anotar, gerenciar e descobri-la usando o portal do catálogo de dados.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como registrar fontes de dados, consulte o tutorial [introdução ao catálogo de dados do Azure](data-catalog-get-started.md) .
