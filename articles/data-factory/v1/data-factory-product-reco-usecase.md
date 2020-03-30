---
title: Caso de utilização da fábrica de dados - Recomendações de produtos
description: Conheça um caso de utilização implementado utilizando a Azure Data Factory juntamente com outros serviços.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139148"
---
# <a name="use-case---product-recommendations"></a>Caso de utilização - Recomendações de produtos
A Azure Data Factory é um dos muitos serviços utilizados para implementar o Cortana Intelligence Suite de aceleradores de soluções.  Consulte a página [da Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) para obter detalhes sobre esta suite. Neste documento, descrevemos um caso de uso comum que os utilizadores do Azure já resolveram e implementaram utilizando a Azure Data Factory e outros serviços de componentes da Cortana Intelligence.

## <a name="scenario"></a>Cenário
Os retalhistas online geralmente querem aliciar os seus clientes a comprar produtos, apresentando-os com produtos que são mais propensos a interessarem-se e, portanto, mais propensos a comprar. Para isso, os retalhistas online precisam de personalizar a experiência online do seu utilizador utilizando recomendações personalizadas de produtos para esse utilizador específico. Estas recomendações personalizadas devem ser feitas com base nos seus dados de comportamento de compras atuais e históricos, informações sobre produtos, marcas recém-introduzidas e dados de segmentação de produtos e clientes.  Além disso, podem fornecer as recomendações do utilizador com base na análise do comportamento geral de utilização de todos os seus utilizadores combinados.

O objetivo destes retalhistas é otimizar as conversões de clique-venda do utilizador e obter maiores receitas de vendas.  Conseguem esta conversão fornecendo recomendações contextuais e baseadas no comportamento com base nos interesses e ações dos clientes. Para este caso de utilização, utilizamos os retalhistas online como um exemplo de empresas que querem otimizar para os seus clientes. No entanto, estes princípios aplicam-se a qualquer negócio que queira envolver os seus clientes em torno dos seus bens e serviços e melhorar a experiência de compra dos seus clientes com recomendações personalizadas de produtos.

## <a name="challenges"></a>Desafios
Há muitos desafios que os retalhistas online enfrentam quando tentam implementar este tipo de caso de uso. 

Em primeiro lugar, os dados de diferentes tamanhos e formas devem ser ingeridos a partir de múltiplas fontes de dados, tanto no local como na nuvem. Estes dados incluem dados do produto, dados históricos do comportamento do cliente e dados do utilizador à medida que o utilizador navega no site de retalho online. 

Em segundo lugar, as recomendações personalizadas do produto devem ser razoavelmente calculadas e corretamente calculadas e previstas. Além do comportamento do produto, da marca e do cliente e dos dados do navegador, os retalhistas online também precisam de incluir o feedback do cliente sobre compras passadas para ter em conta a determinação das melhores recomendações de produto para o utilizador. 

Em terceiro lugar, as recomendações devem ser imediatamente entregues ao utilizador para proporcionar uma experiência de navegação e compra perfeita, e fornecer as recomendações mais recentes e relevantes. 

Por último, os retalhistas têm de medir a eficácia da sua abordagem, acompanhando os sucessos globais de vendas de click-to-conversion e adaptando-se às suas recomendações futuras.

## <a name="solution-overview"></a>Descrição Geral da Solução
Este caso de utilização de exemplo foi resolvido e implementado por utilizadores reais do Azure utilizando a Azure Data Factory e outros serviços de componentes da Cortana Intelligence, incluindo [hDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

O retalhista online utiliza uma loja Azure Blob, um servidor SQL no local, o Azure SQL DB e um mercado de dados relacional como as suas opções de armazenamento de dados em todo o fluxo de trabalho.  A loja blob contém informações do cliente, dados de comportamento do cliente e dados de informação do produto. Os dados da informação do produto incluem informações sobre a marca do produto e um catálogo de produtos armazenado no local num armazém de dados DaQL. 

Todos os dados são combinados e introduzidos num sistema de recomendação de produto para fornecer recomendações personalizadas baseadas em interesses e ações do cliente, enquanto o utilizador navega nos produtos no catálogo no site. Os clientes também vêem produtos que estão relacionados com o produto que estão a analisar com base em padrões globais de utilização do website que não estão relacionados com nenhum utilizador.

![usar diagrama de caso](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de ficheiros de registo web crus são gerados diariamente a partir do site do retalhista online como ficheiros semi-estruturados. Os ficheiros de registo web brutos e as informações do catálogo de clientes e produtos são ingeridos regularmente num armazenamento Azure Blob utilizando o movimento de dados globalmente implantado da Data Factory como um serviço. Os ficheiros de registo bruto do dia são divididos (por ano e mês) em armazenamento de blob para armazenamento a longo prazo.  [O Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é utilizado para dividir os ficheiros de registo bruto na loja blob e processar os registos ingeridos em escala utilizando scripts hive e porco. Os dados de registos web divididos são então processados para extrair as inputs necessárias para um sistema de recomendação de aprendizagem automática para gerar as recomendações personalizadas do produto.

O sistema de recomendação utilizado para a aprendizagem automática neste exemplo é uma plataforma de recomendação de aprendizagem automática de código aberto da [Apache Mahout.](https://mahout.apache.org/)  Qualquer modelo de [Aprendizagem automática Azure](https://azure.microsoft.com/services/machine-learning/) ou personalizado pode ser aplicado ao cenário.  O modelo Mahout é usado para prever a semelhança entre itens no site com base em padrões de utilização globais, e para gerar as recomendações personalizadas com base no utilizador individual.

Finalmente, o conjunto de resultados de recomendações personalizadas do produto é transferido para um mercado de dados relacional para consumo pelo site retalhista.  O conjunto de resultados também poderia ser acedido diretamente a partir do armazenamento de blob por outra aplicação, ou transferido para lojas adicionais para outros consumidores e casos de utilização.

## <a name="benefits"></a>Vantagens
Ao otimizar a sua estratégia de recomendação de produtos e alinhando-a com objetivos de negócio, a solução cumpriu os objetivos de merchandising e marketing do retalhista online. Além disso, conseguiram operacionalizar e gerir o fluxo de trabalho de recomendação do produto de forma eficiente, fiável e rentável. A abordagem facilitou-lhes a atualização do seu modelo e a sua eficácia com base nas medidas de sucessos de clique-conversão de vendas. Ao utilizarem a Azure Data Factory, conseguiram abandonar a sua gestão de recursos manuais de nuvem demorada e dispendiosa e passar para a gestão de recursos em nuvem a pedido. Por conseguinte, conseguiram poupar tempo, dinheiro e reduzir o seu tempo para a implementação de soluções. As vistas de linhagem de dados e a saúde do serviço operacional tornaram-se fáceis de visualizar e resolver problemas com a intuitiva Data Factory de monitorização e gestão de UI disponíveis a partir do portal Azure. A sua solução pode agora ser programada e gerida para que os dados acabados sejam produzidos e entregues de forma fiável aos utilizadores, e os dados e dependências de processamento são geridos automaticamente sem intervenção humana.

Ao proporcionar esta experiência de compras personalizada, o retalhista online criou uma experiência mais competitiva e envolvente do cliente e, portanto, aumentar as vendas e a satisfação geral do cliente.

