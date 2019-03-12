---
title: Caso de utilização de fábrica de dados - recomendações do produto
description: Saiba mais sobre um caso de utilização implementado com o Azure Data Factory, juntamente com outros serviços.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 4a3d1c513bcfb6449ca73d873c0dd9831c6fe01d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540135"
---
# <a name="use-case---product-recommendations"></a>Caso de Utilização - Recomendações do Produto
O Azure Data Factory é um dos vários serviços utilizados para implementar o Cortana Intelligence Suite de Aceleradores de solução.  Ver [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) página para obter detalhes sobre este conjunto. Neste documento, descrevemos um caso de utilização comuns que os utilizadores do Azure já resolvido e implementadas com o Azure Data Factory e outros serviços de componentes do Cortana Intelligence.

## <a name="scenario"></a>Cenário
Os retalhistas online normalmente querem convencer seus clientes para adquirir produtos apresentando-los com os produtos que são mais probabilidade de ter interesse em e, portanto, mais probabilidade de comprar. Para tal, os retalhistas online necessário personalizar experiência online do seu utilizador ao utilizar as recomendações de produtos personalizadas para esse utilizador específico. Estas recomendações personalizadas devem ser feitas com base em seus atuais e históricas de dados de comportamento, informações do produto, recentemente introduzidas marcas e dados de segmentação de produto e de clientes de compras.  Além disso, podem fornecer as recomendações de produto do usuário com base na análise do comportamento de utilização geral de todos os seus usuários combinados.

O objetivo dessas varejistas é otimizar para conversões de clique para venda do utilizador e ganhe receita de vendas superior.  Eles obtenham essa conversão, fornecendo recomendações de produto contextuais, com base no comportamento com base nos interesses dos clientes e ações. Neste caso, vamos utilizar os retalhistas online como um exemplo de empresas que queiram otimizar para seus clientes. No entanto, esses princípios se aplicam a todas as empresas que desejam interagir com os seus clientes em torno de seus bens e serviços e melhorar a experiência de compra dos clientes com recomendações de produtos personalizadas.

## <a name="challenges"></a>Desafios
Existem muitos desafios que enfrentam os retalhistas online ao tentar implementar este tipo de caso de utilização. 

Em primeiro lugar, dados de diferentes formas e tamanhos têm de ser ingeridos de várias origens de dados, tanto no local e na cloud. Estes dados incluem dados de produto, dados de comportamento do histórico dos clientes e os dados de utilizador, como o usuário pesquisa o site de setor do retalho online. 

Recomendações de produto do segundo e personalizados, devem ser razoavelmente e precisão calculadas e previstas. Além de produto, marca e dados de comportamento e o navegador do cliente, os retalhistas online também tem de incluir comentários dos clientes sobre compras anteriores da determinação das melhores recomendações de produto para o utilizador. 

Em terceiro lugar, as recomendações tem de ser imediatamente resultado final para o utilizador para fornecer uma navegação totalmente integrada e a experiência de compra e fornecer recomendações de mais recentes e relevantes. 

Por fim, os varejistas precisam medir a eficácia da sua abordagem ao controlar geral superior e êxitos de vendas de conversão de clique de cross-selling e ajustar às suas recomendações futuras.

## <a name="solution-overview"></a>Descrição Geral da Solução
Este caso de utilização de exemplo foi resolvido e implementado por utilizadores reais do Azure com o Azure Data Factory e outros serviços de componentes do Cortana Intelligence, incluindo [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power BI](https://powerbi.microsoft.com/).

O varejista online usa um armazenamento de Blobs do Azure, um servidor SQL no local, BD SQL do Azure e um data mart relacional como suas opções de armazenamento de dados em todo o fluxo de trabalho.  O armazenamento de blob contém informações de clientes, dados de comportamento do cliente e dados de informações do produto. Os dados de informações do produto incluem informações de marca de produto e armazenados no local num SQL data warehouse do catálogo de um produto. 

Todos os dados são combinados e inseridos num sistema de recomendação de produtos para fornecer recomendações personalizadas com base nos interesses dos clientes e as ações, enquanto o usuário navegar de produtos no catálogo no Web site. Os clientes também veem os produtos que estão relacionados com o produto está olhando com base nos padrões de utilização do Web site global que não estão relacionadas com qualquer um usuário.

![Utilize maiúsculas diagrama](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de ficheiros de registo de web não processados são gerados diariamente partir do site do varejista online, como ficheiros semiestruturados. Os ficheiros de registo não processados da web e as informações do catálogo de produto e de cliente são ingeridos regularmente num armazenamento de Blobs do Azure com o movimento de dados implementados globalmente do Data Factory como um serviço. Os ficheiros de registo não processados para o dia são particionados (por ano e mês) no armazenamento de BLOBs para o armazenamento de longo prazo.  [O Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é utilizado para particionar os ficheiros de registo não processados no armazenamento de BLOBs e processar os registos ingeridos em escala com scripts Pig e Hive. Particionada web regista os dados são processados, em seguida, para extrair as entradas necessárias para um sistema de recomendação para gerar as recomendações de produtos personalizadas de aprendizagem automática.

O sistema de recomendação utilizado para o machine learning neste exemplo é uma máquina de código-fonte aberto, plataforma de recomendação a partir de aprendizagem [Apache Mahout](https://mahout.apache.org/).  Qualquer [do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) ou um modelo personalizado pode ser aplicado para o cenário.  O modelo de Mahout é usado para prever a semelhança entre os itens no Web site com base nos padrões de utilização geral e para gerar as recomendações personalizadas com base no utilizador individual.

Por fim, o conjunto de resultados de recomendações de produtos personalizadas é movido para um data mart relacional para consumo pelo Web site varejista.  O conjunto de resultados também poderia ser aceder diretamente a partir do armazenamento de BLOBs por outra aplicação ou movido para arquivos adicionais para outros consumidores e casos de utilização.

## <a name="benefits"></a>Benefícios
Ao otimizar a sua estratégia de recomendação do produto e alinhando esta informação com objetivos de negócio, a solução cumpridos os objetivos de marketing e mercadorias do varejista online. Além disso, era possível operacionalizar e gerir o fluxo de trabalho de recomendação do produto de forma eficiente, fiável e económica. A abordagem tornou fácil para os mesmos atualizar o seu modelo e ajustar sua eficácia com base em medidas de vendas êxitos de clique para conversão. Ao utilizar o Azure Data Factory, os estudantes conseguiram abandonar a gestão de recursos de cloud manual demorado e dispendioso e mover para a gestão de recursos de nuvem a pedido. Por conseguinte, os estudantes conseguiram poupar tempo, dinheiro e reduzir o tempo para implantação da solução. Vistas de linhagem de dados e estado de funcionamento do serviço operacional ficaram fácil visualizar e resolver problemas com a monitorização de fábrica de dados intuitivos e o gerenciamento da interface do Usuário disponível a partir do portal do Azure. Sua solução agora pode ser agendada e gerida para que os dados concluídos fiável são produzidos e entregues aos utilizadores e dados e processamento de dependências são geridas automaticamente sem intervenção humana.

Ao fornecer esta experiência de compras personalizada, o varejista online criado um cliente mais competitivo e cativantes experiência e, portanto, aumentar a satisfação do cliente de vendas e geral.

