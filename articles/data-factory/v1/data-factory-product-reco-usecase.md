---
title: Caso de uso Data Factory-recomendações de produtos
description: Saiba mais sobre um caso de uso implementado usando Azure Data Factory junto com outros serviços.
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
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139148"
---
# <a name="use-case---product-recommendations"></a>Caso de Utilização - Recomendações do Produto
Azure Data Factory é um dos muitos serviços usados para implementar o Cortana Intelligence Suite de aceleradores de solução.  Consulte [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) página para obter detalhes sobre esse pacote. Neste documento, descrevemos um caso de uso comum que os usuários do Azure já solucionaram e implementaram usando Azure Data Factory e outros serviços de componente Cortana Intelligence.

## <a name="scenario"></a>Cenário
Os varejistas online normalmente desejam convencer seus clientes a comprar produtos, apresentando a eles os produtos com os quais eles têm mais probabilidade de estar interessados e, portanto, provavelmente comprar. Para fazer isso, os varejistas online precisam personalizar a experiência online do usuário usando recomendações de produtos personalizadas para esse usuário específico. Essas recomendações personalizadas devem ser feitas com base em seus dados de comportamento de compras atuais e históricos, informações sobre produtos, marcas introduzidas recentemente e dados de segmentação de produtos e clientes.  Além disso, eles podem fornecer as recomendações de produto do usuário com base na análise do comportamento de uso geral de todos os seus usuários combinados.

O objetivo desses varejistas é otimizar as conversões de clique para venda do usuário e ganhar maior receita de vendas.  Eles obtêm essa conversão fornecendo recomendações de produtos contextuais e baseadas em comportamento com base em ações e interesses do cliente. Para esse caso de uso, usamos varejistas online como um exemplo de empresas que desejam otimizar para seus clientes. No entanto, esses princípios se aplicam a qualquer empresa que queira envolver seus clientes em todos os seus bens e serviços e aprimorar a experiência de compra de seus clientes com recomendações de produtos personalizadas.

## <a name="challenges"></a>Desafios
Há muitos desafios que os varejistas online enfrentam ao tentar implementar esse tipo de caso de uso. 

Primeiro, os dados de diferentes tamanhos e formas devem ser ingeridos de várias fontes de dados, tanto localmente quanto na nuvem. Esses dados incluem dados do produto, dados de comportamento histórico do cliente e dados do usuário à medida que o usuário navega no site de varejo online. 

Segundo, as recomendações de produtos personalizadas devem ser calculadas e previstas de forma razoável e precisa. Além dos dados do produto, da marca e do comportamento do cliente e do navegador, os varejistas online também precisam incluir os comentários dos clientes sobre as compras passadas para fatorar a determinação das melhores recomendações de produto para o usuário. 

Em terceiro lugar, as recomendações devem ser entregues imediatamente ao usuário para fornecer uma experiência de navegação e de compra direta e fornecer as recomendações mais recentes e relevantes. 

Por fim, os varejistas precisam medir a eficácia de sua abordagem ao controlar o sucesso geral de vendas de clique para conversão e venda cruzada, além de se ajustar às suas recomendações futuras.

## <a name="solution-overview"></a>Descrição Geral da Solução
Este caso de uso de exemplo foi resolvido e implementado por usuários reais do Azure usando Azure Data Factory e outros serviços de componente Cortana Intelligence, incluindo [HDInsight](https://azure.microsoft.com/services/hdinsight/) e [Power bi](https://powerbi.microsoft.com/).

O varejista online usa um repositório de blob do Azure, um SQL Server local, um banco de dados SQL do Azure e um data mart relacional como suas opções de armazenamento de dados em todo o fluxo de trabalho.  O repositório de BLOB contém informações do cliente, dados de comportamento do cliente e dados de informações do produto. Os dados de informações do produto incluem informações da marca do produto e um catálogo de produtos armazenado localmente em um data warehouse do SQL. 

Todos os dados são combinados e inseridos em um sistema de recomendação de produto para fornecer recomendações personalizadas com base em interesses e ações do cliente, enquanto o usuário procura produtos no catálogo no site. Os clientes também veem os produtos relacionados ao produto que estão examinando com base nos padrões gerais de uso do site que não estão relacionados a nenhum usuário.

![diagrama de caso de uso](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de arquivos de log da Web brutos são gerados diariamente no site do revendedor online como arquivos semiestruturados. Os arquivos de log da Web brutos e as informações do catálogo de produtos e do cliente são ingeridos regularmente em um armazenamento de BLOBs do Azure usando a movimentação de dados globalmente implantada da Data Factory como um serviço. Os arquivos de log brutos do dia são particionados (por ano e mês) no armazenamento de BLOBs para armazenamento de longo prazo.  O [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) é usado para particionar os arquivos de log brutos no repositório de BLOB e processar os logs ingeridos em escala usando scripts de Hive e Pig. Os dados de logs da Web particionados são processados para extrair as entradas necessárias para que um sistema de recomendação do Machine Learning gere as recomendações de produto personalizadas.

O sistema de recomendação usado para o aprendizado de máquina neste exemplo é uma plataforma de recomendação de aprendizado de máquina de software livre do [Apache Mahout](https://mahout.apache.org/).  Qualquer [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) ou modelo personalizado pode ser aplicado ao cenário.  O modelo Mahout é usado para prever a similaridade entre os itens no site com base nos padrões de uso geral e para gerar as recomendações personalizadas com base no usuário individual.

Por fim, o conjunto de resultados de recomendações de produtos personalizadas é movido para um data mart relacional para consumo pelo site varejista.  O conjunto de resultados também pode ser acessado diretamente do armazenamento de BLOBs por outro aplicativo ou movido para armazenamentos adicionais para outros consumidores e casos de uso.

## <a name="benefits"></a>Benefícios
Ao otimizar a estratégia de recomendação do produto e alinhá-la às metas de negócios, a solução atende aos objetivos de marketing e de mercadorias do revendedor online. Além disso, eles conseguiram colocar em operação e gerenciar o fluxo de trabalho de recomendação do produto de maneira eficiente, confiável e econômica. A abordagem facilitou para que eles atualizem seu modelo e ajustem sua eficácia com base nas medidas de vendas com sucessos de clique para conversão. Usando Azure Data Factory, eles conseguiram abandonar seu tempo demorado e caro gerenciamento de recursos de nuvem manual e mudar para o gerenciamento de recursos de nuvem sob demanda. Portanto, eles conseguiram economizar tempo, dinheiro e reduzir seu tempo para a implantação da solução. As exibições de linhagem de dados e a integridade do serviço operacional tornaram-se fáceis de Visualizar e solucionar problemas com a interface do usuário Data Factory intuitiva de monitoramento e gerenciamento disponível no portal do Azure Sua solução agora pode ser agendada e gerenciada para que os dados concluídos sejam produzidos e entregues de forma confiável aos usuários, e as dependências de dados e de processamento sejam gerenciadas automaticamente sem intervenção humana.

Ao fornecer essa experiência de compra personalizada, o varejista online criou uma experiência de cliente mais competitiva, envolvendo e, portanto, aumentar as vendas e a satisfação geral do cliente.

