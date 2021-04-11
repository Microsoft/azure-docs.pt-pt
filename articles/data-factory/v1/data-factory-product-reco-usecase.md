---
title: Caso de utilização da fábrica de dados - Recomendações do produto
description: Saiba mais sobre um caso de uso implementado utilizando a Azure Data Factory juntamente com outros serviços.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 44c66f5fa89e7293667c930bfd1720d72dc26bf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785075"
---
# <a name="use-case---product-recommendations"></a>Caso de Utilização - Recomendações do Produto
A Azure Data Factory é um dos muitos serviços utilizados para implementar o Conjunto de Inteligência Cortana de aceleradores de soluções.  Consulte a página [cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) para mais detalhes sobre esta suite. Neste documento, descrevemos um caso de uso comum que os utilizadores do Azure já resolveram e implementaram usando a Azure Data Factory e outros serviços de componentes da Cortana Intelligence.

## <a name="scenario"></a>Scenario
Os retalhistas online geralmente querem cativar os seus clientes a comprar produtos, apresentando-os com produtos em que são mais propensos a interessar-se e, portanto, mais propensos a comprar. Para isso, os retalhistas online precisam de personalizar a experiência online do seu utilizador utilizando recomendações personalizadas de produtos para esse utilizador específico. Estas recomendações personalizadas devem ser feitas com base nos seus dados de comportamento de compras atuais e históricos, informações sobre o produto, marcas recém-introduzidas e dados de segmentação de produtos e clientes.  Além disso, podem fornecer as recomendações do produto do utilizador com base na análise do comportamento geral de utilização de todos os seus utilizadores combinados.

O objetivo destes retalhistas é otimizar as conversões de clique para venda do utilizador e obter maiores receitas de vendas.  Conseguem esta conversão através da entrega de recomendações contextuais e baseadas em comportamentos com base nos interesses e ações do cliente. Para este caso de utilização, utilizamos os retalhistas online como um exemplo de empresas que querem otimizar para os seus clientes. No entanto, estes princípios aplicam-se a qualquer negócio que pretenda envolver os seus clientes em torno dos seus bens e serviços e melhorar a experiência de compra dos seus clientes com recomendações personalizadas de produtos.

## <a name="challenges"></a>Desafios
Há muitos desafios que os retalhistas online enfrentam ao tentarem implementar este tipo de caso de utilização. 

Em primeiro lugar, os dados de diferentes tamanhos e formas devem ser ingeridos a partir de múltiplas fontes de dados, tanto no local como na nuvem. Estes dados incluem dados do produto, dados históricos de comportamento do cliente e dados do utilizador à medida que o utilizador navega no site de retalho online. 

Em segundo lugar, as recomendações personalizadas do produto devem ser razoavelmente e corretas e previstas. Além do comportamento do produto, da marca e do cliente e dos dados do navegador, os retalhistas online também precisam de incluir o feedback dos clientes sobre compras passadas para ter em conta a determinação das melhores recomendações do produto para o utilizador. 

Em terceiro lugar, as recomendações devem ser imediatamente entregues ao utilizador para proporcionar uma experiência de navegação e compra sem emenda e fornecer as recomendações mais recentes e pertinentes. 

Por último, os retalhistas precisam de medir a eficácia da sua abordagem, acompanhando os sucessos globais de vendas de venda e venda cruzada de cliques para conversão e ajustando-se às suas futuras recomendações.

## <a name="solution-overview"></a>Descrição Geral da Solução
Este caso de uso de exemplo foi resolvido e implementado por utilizadores reais do Azure através da utilização da Azure Data Factory e de outros serviços de componentes da Cortana Intelligence, incluindo [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

O retalhista on-line utiliza uma loja Azure Blob, um SQL Server no local, base de dados Azure SQL e um mercado de dados relacional como as suas opções de armazenamento de dados ao longo do fluxo de trabalho.  A loja blob contém informações do cliente, dados de comportamento do cliente e dados de informação do produto. Os dados de informação do produto incluem informações sobre a marca de produtos e um catálogo de produtos armazenado no local num Azure Synapse Analytics. 

Todos os dados são combinados e alimentados num sistema de recomendação de produto para entregar recomendações personalizadas baseadas nos interesses e ações do cliente, enquanto o utilizador navega nos produtos do catálogo no site. Os clientes também vêem produtos relacionados com o produto que estão a analisar com base em padrões globais de utilização do site que não estão relacionados com nenhum utilizador.

![usar diagrama de caso](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de ficheiros de registo sonoro são gerados diariamente a partir do site do retalhista online como ficheiros semi-estruturados. Os ficheiros de registo sonoro bruto e as informações do catálogo de clientes e produtos são ingeridos regularmente num armazenamento Azure Blob utilizando o movimento de dados globalmente implementado da Data Factory como um serviço. Os ficheiros de registo bruto do dia são divididos (por ano e mês) no armazenamento de bolhas para armazenamento a longo prazo.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é usado para dividir os ficheiros de log cru na loja blob e processar os troncos ingeridos em escala usando scripts de Colmeia e Porco. Os dados de registos web divididos são então processados para extrair as entradas necessárias para um sistema de recomendação de machine learning para gerar as recomendações personalizadas do produto.

O sistema de recomendação utilizado para a aprendizagem automática neste exemplo é uma plataforma de recomendação de aprendizagem automática de código aberto da [Apache Mahout.](https://mahout.apache.org/)  Qualquer [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) ou modelo personalizado pode ser aplicado ao cenário.  O modelo Mahout é usado para prever a semelhança entre itens no site com base em padrões de utilização globais, e para gerar as recomendações personalizadas com base no utilizador individual.

Finalmente, o conjunto de resultados das recomendações personalizadas do produto é transferido para um mercado de dados relacional para consumo pelo site retalhista.  O conjunto de resultados também poderia ser acedido diretamente a partir do armazenamento de blob por outra aplicação, ou transferido para lojas adicionais para outros consumidores e casos de utilização.

## <a name="benefits"></a>Benefícios
Ao otimizar a sua estratégia de recomendação de produtos e alinhar-a com os objetivos de negócio, a solução cumpriu os objetivos de merchandising e marketing do retalhista online. Além disso, conseguiram operacionalizar e gerir o fluxo de trabalho de recomendação do produto de forma eficiente, fiável e rentável. A abordagem facilitou-lhes a atualização do seu modelo e afinar a sua eficácia com base nas medidas de sucesso de vendas click-to-conversion. Ao utilizarem a Azure Data Factory, conseguiram abandonar a sua gestão de recursos manuais em nuvem demorada e dispendiosa e passar para a gestão de recursos em nuvem a pedido. Por isso, conseguiram poupar tempo, dinheiro e reduzir o seu tempo para a implementação de soluções. As vistas de linhagem de dados e a saúde do serviço operacional tornaram-se fáceis de visualizar e resolver problemas com a intuitiva monitorização e gestão da UI disponível a partir do portal Azure. A sua solução pode agora ser programada e gerida de modo a que os dados acabados sejam produzidos e entregues de forma fiável aos utilizadores, e as dependências de dados e processamento são geridas automaticamente sem intervenção humana.

Ao proporcionar esta experiência de compras personalizada, o retalhista online criou uma experiência de cliente mais competitiva e envolvente e, portanto, aumentou as vendas e a satisfação geral do cliente.

