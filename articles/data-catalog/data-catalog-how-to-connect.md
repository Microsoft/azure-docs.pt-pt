---
title: Como se conectar a fontes de dados no catálogo de dados do Azure
description: Artigo de instruções que destaca como se conectar a fontes de dados descobertas com o catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c91c09da31e4ecf42257b8f9c86f25c6ec39b9df
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68734646"
---
# <a name="how-to-connect-to-data-sources"></a>Como ligar a origens de dados
## <a name="introduction"></a>Introdução
**Microsoft Azure catálogo de dados** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados empresariais. Em outras palavras, o **Catálogo de dados do Azure** está prestes a ajudar as pessoas a descobrir, compreender e usar fontes de dados e ajudar as organizações a obter mais valor de seus dados existentes. Um aspecto fundamental desse cenário é usar os dados – depois que um usuário descobre uma fonte de dados e entende sua finalidade, a próxima etapa é conectar-se à fonte de dados para colocar seus dados em uso.

## <a name="data-source-locations"></a>Localizações de origem de dados
Durante o registro da fonte de dados, o **Catálogo de dados do Azure** recebe metadados sobre a fonte de dados. Esses metadados incluem os detalhes do local da fonte de dados. Os detalhes do local variam de fonte de dados para fonte de dados, mas sempre conterão as informações necessárias para se conectar. Por exemplo, o local de uma tabela de SQL Server inclui o nome do servidor, o nome do banco de dados, o nome do esquema e o nome da tabela, enquanto o local de um relatório de SQL Server Reporting Services inclui o nome do servidor e o caminho para o relatório. Outros tipos de fonte de dados terão locais que refletem a estrutura e os recursos do sistema de origem.

## <a name="integrated-client-tools"></a>Ferramentas de cliente integradas
A maneira mais simples de se conectar a uma fonte de dados é usar a opção "abrir em..." no portal do **Catálogo de dados do Azure** . Esse menu exibe uma lista de opções para se conectar ao ativo de dados selecionado.
Ao usar o modo de exibição de bloco padrão, esse menu estará disponível em cada bloco.

 ![Abrindo uma tabela SQL Server no Excel a partir do bloco ativo de dados](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Ao usar o modo de exibição de lista, o menu estará disponível na barra de pesquisa na parte superior da janela do Portal.

 ![Abrir um relatório de SQL Server Reporting Services no Report Manager da barra de pesquisa](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Aplicativos cliente com suporte
Ao usar o botão "abrir em..." menu para fontes de dados no portal do catálogo de dados do Azure, o aplicativo cliente correto deve ser instalado no computador cliente.

| Abrir no aplicativo | Extensão de arquivo/protocolo | Versões de aplicativos com suporte |
| --- | --- | --- |
| Excel |.odc |Excel 2010 ou posterior |
| Excel (Top 1000) |.odc |Excel 2010 ou posterior |
| Power Query |.xlsx |Excel 2016 ou Excel 2010 ou Excel 2013 com o Power Query para o suplemento do Excel instalado |
| Power BI Desktop |. pbix |Power BI Desktop de julho de 2016 ou posterior |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 ou posterior com ferramentas SQL Server instaladas |
| Report Manager |http:// |Consulte [os requisitos de navegador para SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Seus dados, suas ferramentas
As opções disponíveis no menu dependerão do tipo de ativo de dados selecionado no momento. É claro que nem todas as ferramentas possíveis serão incluídas na "abrir em..." , mas ainda é fácil se conectar à fonte de dados usando qualquer ferramenta de cliente. Quando um ativo de dados é selecionado no portal do **Catálogo de dados do Azure** , o local completo é exibido no painel Propriedades.

 ![Informações de conexão para uma tabela de SQL Server](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

Os detalhes de informações de conexão serão diferentes do tipo de fonte de dados para o tipo de fonte de dados, mas as informações incluídas no portal fornecerão tudo o que você precisa para se conectar à fonte de dados em qualquer ferramenta de cliente. Os usuários podem copiar os detalhes da conexão para as fontes de dados que eles descobriram usando o **Catálogo de dados do Azure**, permitindo que eles trabalhem com os dados na ferramenta de sua escolha.

## <a name="connecting-and-data-source-permissions"></a>Permissões de conexão e fonte de dados
Embora o **Catálogo de dados do Azure** torne as fontes de dados detectáveis, o acesso aos próprios dados permanece sob o controle do proprietário ou administrador da fonte de dados. A descoberta de uma fonte de dados no **Catálogo de dados do Azure** não dá a um usuário nenhuma permissão para acessar a própria fonte de dados.

Para facilitar para os usuários que descobrem uma fonte de dados, mas que não têm permissão para acessar seus dados, os usuários podem fornecer informações na propriedade solicitar acesso ao anotar uma fonte de dados. As informações fornecidas aqui – incluindo links para o processo ou ponto de contato para obter acesso à fonte de dados – são apresentadas junto com as informações de local da fonte de dados no Portal.

 ![Informações de conexão com instruções de acesso de solicitação fornecidas](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Resumo
O registro de uma fonte de dados com o **Catálogo de dados do Azure** torna esses dados detectáveis por meio da cópia de metadados estruturais e descritivos da fonte de dados para o serviço de catálogo. Depois que uma fonte de dados é registrada e descoberta, os usuários podem se conectar à fonte de dados do portal do **Catálogo de dados do Azure** "abrir em..." " ou usando suas ferramentas de dados de sua escolha.

## <a name="see-also"></a>Consulte também
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) para obter detalhes passo a passo sobre como se conectar a fontes de dados.
