---
title: Registar origens de dados no catálogo de dados do Azure
description: Este artigo destaca como registar origens de dados no catálogo de dados do Azure, incluindo os campos de metadados extraídos durante o registo.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 57b9a040b875c584b126e2062e4938b37875a31b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61001298"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registar origens de dados no catálogo de dados do Azure
## <a name="introduction"></a>Introdução
Catálogo de dados do Azure é um serviço cloud totalmente gerido que funciona como um sistema de registo e deteção de origens de dados empresariais. Em outras palavras, o catálogo de dados ajuda as pessoas a detetar, compreender e utilizar origens de dados, e ajuda as organizações a obter mais valor dos respetivos dados existentes. A primeira etapa para fazer uma origem de dados detetável através do catálogo de dados é registrar essa origem de dados.

## <a name="register-data-sources"></a>Registar origens de dados
O registo é o processo de extrair metadados da origem de dados e copiar esses dados para o serviço de catálogo de dados. Os dados permanecem no local onde residem atualmente e continuam sob o controlo dos administradores e das políticas do sistema atual.

Para registar uma origem de dados, faça o seguinte:
1. No portal do catálogo de dados do Azure, inicie a ferramenta de registo de origem de dados do catálogo de dados. 
2. Inicie sessão com a sua conta escolar ou profissional com as mesmas credenciais do Azure Active Directory que utiliza para iniciar sessão portal.
3. Selecione a origem de dados que pretende registar.

Para obter mais detalhes passo a passo, consulte a [introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial.

Depois de registar a origem de dados, o catálogo controla a localização e indexa os metadados. Os utilizadores podem pesquisar, procurar e descobrir a origem de dados e, em seguida, utilize a localização para ligá-la usando o aplicativo ou a ferramenta de sua preferência.

## <a name="supported-data-sources"></a>Origens de dados suportadas
Para obter uma lista de origens de dados atualmente suportadas, consulte [DSR do catálogo de dados](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Metadados estruturais
Ao registar uma origem de dados, a ferramenta de registo extrai informações sobre a estrutura dos objetos que selecionar. Esta informação é referida como metadados estruturais.

Para todos os objetos, estes metadados estruturais incluem a localização do objeto, para que os utilizadores que detetar os dados podem utilizar essas informações para ligar para o objeto nas ferramentas do cliente de sua preferência. Outros metadados estruturais incluem o nome do objeto e o tipo e digite o nome de atributo/colunas e os dados.

## <a name="descriptive-metadata"></a>Metadados descritivos
Além dos metadados estruturais principais que são extraídos da origem de dados, a ferramenta de registo da origem de dados extrai metadados descritivos. Para SQL Server Analysis Services e o SQL Server Reporting Services, estes metadados é retirado das propriedades de descrição expostas por esses serviços. Para o SQL Server, valores fornecidos com o ms\_descrição de propriedade estendida é extraída. Base de dados Oracle, a ferramenta de registo da origem de dados extrai a coluna de comentários do todas\_SEPARADOR\_vista de comentários.

Além dos metadados descritivos que são extraídos da origem de dados, os utilizadores podem introduzir metadados descritivos utilizando a ferramenta de registo da origem de dados. Os usuários podem adicionar etiquetas e podem identificar especialistas para os objetos que está a ser registados. Estes metadados descritivos é copiado para o serviço de catálogo de dados, juntamente com os metadados estruturais.

## <a name="include-previews"></a>Incluir pré-visualizações
Por predefinição, é que apenas os metadados extraídos de origens de dados e copiados para o serviço de catálogo de dados, mas o entendimento de que uma origem de dados, muitas vezes, se torna mais fácil quando pode ver um exemplo de dados que contém.

Ao utilizar a ferramenta de registo de origem de dados do catálogo de dados, pode incluir uma pré-visualização de instantâneo dos dados em cada tabela e a vista que está registrada. Se optar por incluir pré-visualizações durante o registo, a ferramenta de registo inclui até 20 registos de cada tabela e o modo de exibição. Este instantâneo é então copiado para o catálogo, juntamente com os metadados estruturais e descritivo.

> [!NOTE]
> Tabelas grandes com um grande número de colunas podem ter menos de 20 registos incluídos na sua visualização.
>
>

## <a name="include-data-profiles"></a>Incluem perfis de dados
Tal como incluindo pré-visualizações de podem fornecer o contexto valioso para os utilizadores que pesquisar origens de dados no catálogo de dados, incluindo um perfil de dados pode tornar mais fácil de compreender origens de dados detetadas.

Ao utilizar a ferramenta de registo de origem de dados do catálogo de dados, pode incluir um perfil de dados para cada tabela e a vista que está registrada. Se optar por incluir um perfil de dados durante o registo, a ferramenta de registo incluem estatísticas agregadas sobre os dados em cada tabela e a vista, incluindo:

* O número de linhas e tamanho dos dados no objeto.
* A data para a atualização mais recente dos dados e o esquema do objeto.
* O número de registros nulo e valores distintos para colunas.
* Os valores mínimo, máximo, média e desvio padrão para colunas.

Estas estatísticas, em seguida, são copiadas para o catálogo, juntamente com os metadados estruturais e descritivo.

> [!NOTE]
> Colunas de texto e a data não incluem as estatísticas de desvio padrão ou uma média no seu perfil de dados.
>
>

## <a name="update-registrations"></a>Registos de atualização
Registar uma origem de dados torna detectável no catálogo de dados quando utiliza os metadados e visualização opcionais extraídos durante o registo. Se a origem de dados tem de ser atualizado no catálogo (por exemplo, se o esquema de um objeto foi alterado, tabelas, originalmente excluídas devem ser incluídas ou que pretende atualizar os dados que estão incluídos nas pré-visualizações), a ferramenta de registo da origem de dados pode ser executada novamente.

Voltar a registar uma origem de dados já está registado executa uma operação de "upsert" de intercalação: objetos existentes são atualizados e são criados novos objetos. Todos os metadados fornecidos por utilizadores através do portal do catálogo de dados são retidos.

## <a name="summary"></a>Resumo
Uma vez que copia os metadados estruturais e descritivo de uma origem de dados para o serviço de catálogo, registar a origem de dados no catálogo de dados torna os dados mais fáceis de detetar e compreender. Após o registro da origem de dados, pode anotar, gerir e detetá-los com o portal do catálogo de dados.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como registar origens de dados, consulte a [introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial.
