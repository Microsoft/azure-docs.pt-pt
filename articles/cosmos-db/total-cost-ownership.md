---
title: Custo Total de Propriedade (TCO) com Azure Cosmos DB
description: Este artigo compara o custo total de propriedade da Azure Cosmos DB com iaaS e bases de dados no local
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: eb06d101170685bea5ff231612e9ffbf8df467da
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079296"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Custo Total de Propriedade (TCO) com Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

O Azure Cosmos DB foi concebido com a funcionalidade detalhada de multi-inquilinos e governação de recursos. Este design permite ao Azure Cosmos DB operar a um custo significativamente mais baixo e ajuda os utilizadores a poupar. Atualmente, o Azure Cosmos DB suporta mais de 280 cargas de trabalho de clientes num único computador com a densidade continuamente a aumentar e milhares de cargas de trabalho de clientes num cluster. Faz o balanceamento de carga das réplicas das cargas de trabalho dos clientes em diferentes computadores num cluster e em vários clusters num datacenter. Para saber mais, consulte [Azure Cosmos DB: Empurrando a fronteira das bases de dados distribuídas globalmente.](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/) Devido à governação de recursos, ao arrendamento e à integração nativa com o resto da infraestrutura Azure, a Azure Cosmos DB é, em média, 4 a 6 vezes mais barata do que a MongoDB, Cassandra ou outros OSS NoSQL a funcionar em IaaS e até 10 vezes mais barato do que os motores de base de dados que estão a funcionar no local. Consulte o artigo sobre [o custo total de (não) propriedade de um serviço de nuvem de base de dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

As soluções de base de dados OSS NoSQL, tais como Apache Cassandra, MongoDB, HBase, foram projetados para motores no local. Quando oferecidos como um serviço gerido, são equivalentes a um modelo de Gestor de Recursos com uma base de dados de inquilinos para gerir os clusters aprovisionados e o suporte de monitorização. As arquiteturas NOSQL da OSS requerem uma sobrecarga operacional significativa, e a experiência pode ser difícil e dispendiosa de encontrar. Por outro lado, o Azure Cosmos DB é um serviço de nuvem totalmente gerido, que permite aos desenvolvedores focarem-se na inovação empresarial em vez de gerir e manter a infraestrutura de base de dados.

Ao contrário de um serviço de base de dados nativo-nuvem Azure Cosmos DB, os motores de base de dados OSS NoSQL não foram projetados e construídos com a governação de recursos ou multi-arrendamento de grãos finos como os princípios arquitetónicos fundamentais. Os motores de base de dados OSS NoSQL, como a Cassandra e a MongoDB, assumem fundamentalmente que todos os recursos da máquina virtual em que estão a funcionar estão disponíveis para a sua utilização. Muitos destes motores de base de dados não podem funcionar se a quantidade de recursos descer abaixo de um determinado limiar. Por exemplo, para pequenos casos de VM, e estão disponíveis com configurações recomendadas pelo fornecedor que sugerem VMs tipicamente em larga escala com custos mais elevados. Assim, não é possível hospedar um motor de base de dados OSS NoSQL ou qualquer outro motor de base de dados no local e disponibilizá-lo utilizando um modelo de carregamento baseado no consumo, como pedidos por segundo ou armazenamento consumido.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Custo total de propriedade da Azure Cosmos DB

O modelo de provisionamento sem servidor da Azure Cosmos DB elimina a necessidade de sobre-avisão da infraestrutura de base de dados. Os recursos DB da Azure Cosmos são fornecidos sem necessidade de configurações especializadas ou licenciamento. Como resultado, as aplicações apoiadas pelo Azure Cosmos podem funcionar com um custo total de 70% de poupança de propriedade quando comparadas com as bases de dados NOSQL da OSS. Para alguns exemplos em tempo real, consulte [os casos de utilização do cliente](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Outros benefícios do modelo de preços Azure Cosmos DB incluem:

* **Grande valor para o preço:** Analistas de mercado, clientes e parceiros confirmaram um valor maior de todas as funcionalidades que a Azure Cosmos DB oferece por um preço muito mais baixo em comparação com o que os clientes podem obter ao implementar estas soluções por conta própria ou através de outros fornecedores. A base de dados apresenta tais distribuição global, escritas multi-regiões, modelos de consistência bem definidos e intuitivos, indexação automática são muito simplificados com Azure Cosmos DB sem qualquer complexidade, sobrecarga ou tempo de inatividade.

* **Não é necessária nenhuma administração NoSQL DevOps:** Com a Azure Cosmos DB não é necessário empregar DevOps para gerir implementações, realizar manutenção, escala ou patch. Pode executar todas as cargas de trabalho que faria com o cluster OSS NoSQL alojado no local ou em infraestruturas de nuvem.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Custo de propriedade da Azure Cosmos DB" border="false":::

* **Capacidade de escalar elasticamente:** A produção DB Azure Cosmos pode ser dimensionada para cima e para baixo, permitindo-lhe reduzir o custo de propriedade durante os tempos não-pico. Os clusters NOSQL implantados em infraestruturas de nuvem oferecem elasticidade limitada, e as implementações no local não são elásticas por definição. Em Azure Cosmos DB, se você providenciar mais produção, a sua produção é garantida para escalar linearmente. Esta garantia é apoiada por SLAs financeiros e pelo percentil 99 em qualquer escala.

* **Economias de escala:** Um serviço gerido como a Azure Cosmos DB opera com um grande número de nós, integrados nativamente com networking, armazenamento e computas. Devido à grande escala da Azure Cosmos DB, a normalização pode economizar custos.

* **Otimizado para a nuvem:** Azure Cosmos DB é projetado a partir do solo com multi-arrendamento de grãos finos e isolamento de desempenho. Isto permite a colocação, execução e equilíbrio de milhares de inquilinos e as suas cargas de trabalho em clusters e centros de dados. Em contraste, a atual geração de bases de dados OSS NoSQL opera no local com toda a máquina virtual assumida para executar a carga de trabalho de um único inquilino. Estas bases de dados também não foram concebidas para alavancar em toda a extensão a infraestrutura e hardware de um fornecedor de nuvem. Por exemplo, um motor de base de dados OSS NoSQL não está ciente das diferenças entre uma máquina virtual estar em Baixo Vs uma atualização de imagem de rotina, ou o facto de o disco premium já estar replicado a três vias. Não pode tirar partido destes benefícios e passar os benefícios e poupanças para os clientes.

* **Paga-se à hora:** Para cargas de trabalho em larga escala, que precisam de escalar a qualquer momento, você só é cobrado por hora. As cargas de trabalho numa aplicação variam tipicamente em todas as épocas do ano, e pelos dados que são consultados. Com a Azure Cosmos DB, pode escalar para cima ou para baixo como precisar e pagar apenas pelo que precisa. Com sistemas no local ou hospedados em IaaS, não é possível comparar este modelo, porque não há uma maneira de desativar o hardware a cada hora. Nesses casos, pode potencialmente economizar entre 10 a 14 vezes, em média, com Azure Cosmos DB.

* **Você obtém inúmeras funcionalidades gratuitamente:** Em Azure Cosmos DB, as cargas de trabalho de escrita são substancialmente mais baratas em comparação com serviços de base de dados alternativos. Além disso, a Azure Cosmos DB oferece funcionalidades como [indexação automática,](index-policy.md) [Time to Live (TTL)](time-to-live.md), [Change Feed](change-feed.md) e outras sem quaisquer encargos adicionais, algo que outros serviços de base de dados normalmente cobram.

* **Utiliza moeda unificada para diversas cargas de trabalho:** Ao contrário de ofertas alternativas, em Azure Cosmos DB, não é necessário segmentar cargas de trabalho, por exemplo, em leituras e escritas. Ou provisão de produção em um tipo de carga de trabalho que é lido através da produção vs. escrever produção. Em Azure Cosmos DB, a produção aprovisionada é reservada utilizando uma moeda unificada e normalizada em termos de Unidades de Pedido ou RU/sec. A azure Cosmos DB não o obriga a atribuir prioridade às suas cargas de trabalho, realizar planeamento de capacidade ou pagar por cada tipo de capacidade separadamente. Esta abordagem permite-lhe facilmente trocar os mesmos RU/s entre várias operações e tipos de carga de trabalho.

* **Não requer que os VMs provisões se dimensionem:** A maioria das bases de dados operacionais exige que você vá com grandes máquinas virtuais para evitar vizinhos barulhentos e para uma governação de recursos soltos, se você quiser escala. Isto coloca o fardo e o compromisso inicial de custos para os clientes. Com o Azure Cosmos DB, você pode começar pequeno e crescer em tamanhos de carga de trabalho em larga escala perfeitamente, e sem qualquer tempo de inatividade ou impacto na disponibilidade de dados.

* **Pode utilizar a produção prevista para um limite máximo:** Em virtude do multiplexing sub-core em Azure Cosmos DB, você pode saturar a produção provisida em maior medida do que as opções hospedadas da IaaS ou ofertas de terceiros. Este método poupa muito mais do que as soluções alternativas.

* **Integração profunda da Azure Cosmos DB com outros serviços Azure.** A Azure Cosmos DB tem uma integração nativa com networking, Compute, Azure Functions (sem servidor), Azure IoT, e outros serviços Azure. Com esta integração, obtém-se o melhor desempenho, velocidade de replicação de dados em todo o mundo com garantias robustas. As soluções de terceiros não serão capazes de corresponder ou normalmente cobrariam um prémio para oferecer tais funcionalidades.

* **Obtém automaticamente alta disponibilidade, com pelo menos 10-20 domínios de avaria por padrão:** O Azure Cosmos DB suporta a distribuição de cargas de trabalho em domínios de falhas, uma funcionalidade que é fundamental para a alta disponibilidade. Oferece 99.999 alta disponibilidade para leituras e escritas no percentil 99 em qualquer lugar do mundo. O custo de implementar algo assim por conta própria ou através de uma solução de terceiros seria elevado.

* **Obtém-se automaticamente todas as capacidades da empresa, sem custos adicionais.** A Azure Cosmos DB oferece o conjunto mais abrangente de certificações de conformidade, segurança e encriptação em repouso e em movimento sem custos adicionais (em comparação com a nossa concorrência). Obtém-se automaticamente disponibilidade regional em qualquer parte do mundo. Pode abranger a sua base de dados em qualquer número de regiões de Azure e adicionar ou remover regiões em qualquer ponto.

* **Pode economizar até 65% dos custos com capacidade reservada:** A [capacidade reservada da](cosmos-db-reserved-capacity.md) Azure Cosmos ajuda-o a economizar dinheiro, pré-pagando os recursos DB da Azure Cosmos durante um ano ou três anos. Pode reduzir significativamente os seus custos com compromissos iniciais de um ano ou três anos e poupar entre 20 a 65% de descontos em comparação com o preço normal. Nas suas cargas de trabalho críticas à missão, pode obter melhores SLAs em termos de capacidade de avisão.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [como o modelo de preços da Azure Cosmos DB é rentável para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas cosmos multi-regiões](optimize-cost-regions.md)
* Saiba mais sobre [o custo total de (não) propriedade de um Serviço de Nuvem de Base de Dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
