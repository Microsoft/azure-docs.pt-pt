---
title: Custo Total de Propriedade (TCO) com Azure Cosmos DB
description: Este artigo compara o custo total de propriedade da Azure Cosmos DB com as bases de dados IaaS e on-premises
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754798"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Custo Total de Propriedade (TCO) com Azure Cosmos DB

O Azure Cosmos DB foi concebido com a funcionalidade detalhada de multi-inquilinos e governação de recursos. Este design permite ao Azure Cosmos DB operar a um custo significativamente mais baixo e ajuda os utilizadores a poupar. Atualmente, o Azure Cosmos DB suporta mais de 280 cargas de trabalho de clientes num único computador com a densidade continuamente a aumentar e milhares de cargas de trabalho de clientes num cluster. Faz o balanceamento de carga das réplicas das cargas de trabalho dos clientes em diferentes computadores num cluster e em vários clusters num datacenter. Para saber mais, consulte [Azure Cosmos DB: Empurrando a fronteira das bases de dados distribuídas globalmente.](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/) Devido à governação de recursos, ao multiarrendamento e à integração nativa com o resto da infraestrutura Azure, o Azure Cosmos DB é, em média, 4 a 6 vezes mais barato do que o MongoDB, Cassandra ou outros NoSQL da OSS que funciona no IaaS e até 10 vezes mais barato que a base de dados motores em funcionamento nas instalações. Consulte o documento sobre o custo total da (não) propriedade de um serviço de nuvem de base de [dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

As soluções de base de dados OSS NoSQL, tais como Apache Cassandra, MongoDB, HBase, motores foram projetados para as instalações. Quando oferecidos como um serviço gerido são equivalentes a um modelo de Gestor de Recursos com uma base de dados de inquilinos para gerir os clusters provisionados e monitorizar o suporte. As arquiteturas NOSQL da OSS requerem despesas operacionais significativas, e a experiência pode ser difícil e dispendiosa de encontrar. Por outro lado, o Azure Cosmos DB é um serviço de cloud totalmente gerido, que permite que os desenvolvedores se concentrem na inovação empresarial em vez de na gestão e manutenção de infraestruturas de base de dados. 

Ao contrário de um serviço de base de dados nativo-nuvem Azure Cosmos DB, os motores de base de dados OSS NoSQL não foram projetados e construídos com a governação de recursos ou multi-arrendamento de grãos finos como princípios arquitetónicos fundamentais. Os motores de base de dados OSS NoSQL, como Cassandra e MongoDB, assumem fundamentalmente que todos os recursos da máquina virtual em que estão a funcionar estão disponíveis para a sua utilização. Muitos destes motores de base de dados não podem funcionar se a quantidade de recursos descer abaixo de um determinado limiar. Por exemplo, para pequenos casos de VM, e estão disponíveis com configurações recomendadas pelo fornecedor, sugerindo VMs tipicamente de grande escala com custos mais elevados. Assim, não é possível hospedar um oss NoSQL ou qualquer outro motor de base de dados no local e disponibilizá-lo usando um modelo de carregamento baseado no consumo, como pedidos por segundo ou armazenamento consumido.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Custo total da propriedade da Azure Cosmos DB 

O modelo de provisionamento sem servidor estonteante do Azure Cosmos DB elimina a necessidade de sobreabastecer a infraestrutura de base de dados. Os recursos DB da Azure Cosmos são fornecidos sem necessidade de configurações especializadas ou licenciamento. Como resultado, as aplicações apoiadas por DB da Azure Cosmos podem funcionar com um custo total de poupança de propriedade de 70% em comparação com as bases de dados DAOsS NoSQL. Para alguns exemplos em tempo real, consulte os [casos de utilização do cliente.](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc) Outros benefícios do modelo de preços Azure Cosmos DB incluem:

* **Grande valor pelo preço:** Analistas de mercado, clientes e parceiros confirmaram um valor maior de todas as funcionalidades que a Azure Cosmos DB oferece por um preço muito mais baixo em comparação com o que os clientes podem obter na implementação destas soluções por conta própria ou através de outros fornecedores. A base de dados apresenta tais modelos de distribuição global, multi-master, bem definidos e intuitivos, indexação automática são muito simplificados com Azure Cosmos DB sem qualquer complexidade, sobrecarga ou tempo de inatividade.

* **Não é necessária nenhuma administração NoSQL DevOps:** Com a Azure Cosmos DB não é necessário empregar DevOps para gerir implementações, realizar manutenção, escala ou patch. Você pode executar todas as cargas de trabalho que você faria com o cluster OSS NoSQL hospedado no local ou em infraestruturas de nuvem.

![Custo de propriedade da Azure Cosmos DB](./media/total-cost-ownership/tco.png)

* **Capacidade de escala elástica:** A entrada de Azure Cosmos DB pode ser dimensionada para cima e para baixo, permitindo-lhe reduzir o custo de propriedade em tempos não-pico. Os clusters noSQL da OSS implantados em infraestruturas em nuvem oferecem elasticidade limitada, e as implementações no local não são elásticas por definição. Em Azure Cosmos DB, se fornecer mais entrada, a sua entrada é garantida para escalar linearmente. Esta garantia é apoiada por SLAs financeiros e no percentil 99 em qualquer escala.

* **Economias de escala:** Um serviço gerido como o Azure Cosmos DB opera com um grande número de nós, integrados nativamente com networking, armazenamento e computação. Devido à grande escala da Azure Cosmos DB, a normalização pode poupar custos.

* **Otimizado para a nuvem:** O Azure Cosmos DB foi projetado a partir do solo com vários arrendamentos e isolamento de desempenho. Isto permite colocar, executar e equilibrar idealmente milhares de inquilinos e suas cargas de trabalho em clusters e centros de dados. Em contrapartida, a atual geração de bases de dados ossoql opera no local com toda a máquina virtual assumida para executar a carga de trabalho de um único inquilino. Estas bases de dados também não foram concebidas para alavancar a infraestrutura e hardware de um fornecedor de nuvem em toda a extensão. Por exemplo, um motor de base de dados OSS NoSQL não está ciente das diferenças entre uma máquina virtual que está a ser despromovida por Vs uma atualização de imagem de rotina, ou o facto de o disco premium já estar replicado a três. Não pode aproveitar estes benefícios e passar os benefícios e poupanças para os clientes.

* **Paga-se à hora:** Para cargas de trabalho em larga escala, que precisam de escalar a qualquer momento, só é cobrado por hora. As cargas de trabalho numa aplicação variam tipicamente em todas as épocas do ano, e pelos dados que são consultados. Com o Azure Cosmos DB, pode escalar para cima ou para baixo como precisar e pagar apenas pelo que precisa. Com sistemas instalados no local ou iaaS, não pode comparar este modelo, porque não há uma maneira de desativar o hardware a cada hora. Nesses casos, pode potencialmente economizar entre 10 a 14 vezes em média com o Azure Cosmos DB.

* **Obtém inúmeras funcionalidades gratuitamente:** No Azure Cosmos DB, as cargas de trabalho escrita são substancialmente mais baratas em comparação com serviços de base de dados alternativos. Além disso, o Azure Cosmos DB oferece funcionalidades como [indexação automática](indexing-policies.md), [Time to Live (TTL)](time-to-live.md), [Change Feed](change-feed.md) e outros sem quaisquer encargos adicionais, algo que outros serviços de base de dados normalmente cobram.

* **Utiliza moeda unificada para diversas cargas de trabalho:** Ao contrário de ofertas alternativas, no Azure Cosmos DB, não é necessário segmentar cargas de trabalho, por exemplo, em leituras e escritos. Ou provisão de entrada num tipo de carga de trabalho que é lido através de entrada vs. escrever a entrada. No Azure Cosmos DB, a entrada prevista é reservada usando uma moeda unificada e normalizada em termos de Unidades de Pedido ou RU/sec. Azure Cosmos DB não o obriga a atribuir prioridade às suas cargas de trabalho, executar o planeamento da capacidade ou pagar por cada tipo de capacidade separadamente. Esta abordagem permite-lhe facilmente trocar o mesmo RU/s entre várias operações e tipos de carga de trabalho.

* **Não requer que o provisionamento de VMs dimensione:** A maioria das bases de dados operacionais exigeque você vá com grandes máquinas virtuais para evitar vizinhos barulhentos e para uma governação de recursos soltos, se você quiser escala. Isto coloca o fardo e o compromisso inicial de custos para os clientes. Com o Azure Cosmos DB, você pode começar pequeno e crescer para o tamanho da carga de trabalho em larga escala perfeitamente, e sem qualquer tempo de inatividade ou impacto na disponibilidade de dados.

* Pode utilizar a **entrada prevista para um limite máximo:** Em virtude do multiplexing sub-core em Azure Cosmos DB, você pode saturar a entrada provisionada em maior medida do que iaaS hospedado opções ou ofertas de terceiros. Este método poupa muito mais do que as soluções alternativas.

* **Integração profunda do Azure Cosmos DB com outros serviços Azure.** A Azure Cosmos DB tem uma integração nativa com networking, Compute, Funções Azure (servidor), Azure IoT, e outros serviços Azure. Com esta integração, obtém-se o melhor desempenho, velocidade de replicação de dados em todo o mundo com garantias robustas. As soluções de terceiros não serão capazes de corresponder ou normalmente cobrarum prémio para oferecer tais funcionalidades.

* **Obtém automaticamente uma elevada disponibilidade, com pelo menos 10-20 domínios** de falha por padrão: A Azure Cosmos DB suporta a distribuição de cargas de trabalho em domínios de falhas, uma funcionalidade que é fundamental para a elevada disponibilidade. Oferece 99.999 elevada disponibilidade para leituras e escritos no percentil 99 em qualquer lugar do mundo. O custo de implementar algo assim por conta própria ou através de uma solução de terceiros, seria elevado.

* **Obtém-se automaticamente todas as capacidades da empresa, sem custos adicionais.** A Azure Cosmos DB oferece o conjunto mais abrangente de certificações de conformidade, segurança e encriptação em repouso e em movimento sem custos adicionais (em comparação com a nossa concorrência). Obtém-se automaticamente disponibilidade regional em qualquer parte do mundo. Pode percorrer a sua base de dados em várias regiões do Azure e adicionar ou remover regiões em qualquer ponto.

* **Pode economizar até 65% dos custos com capacidade reservada:** A [capacidade reservada](cosmos-db-reserved-capacity.md) da Azure Cosmos DB ajuda-o a economizar dinheiro ao pagar os recursos DB da Azure Cosmos durante um ano ou três anos. Pode reduzir significativamente os seus custos com compromissos iniciais de um ano ou três anos e economizar entre 20 a 65% de descontos quando comparado com os preços regulares. Nas suas cargas de trabalho críticas de missão, você pode obter melhores SLAs em termos de capacidade de provisionamento.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como o modelo de [preços da Azure Cosmos DB é rentável para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas cosmos multi-regiões](optimize-cost-regions.md)
* Saiba mais sobre o Custo Total da (Não) Propriedade de um Serviço de Nuvem de Nuvem de Base de [Dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
