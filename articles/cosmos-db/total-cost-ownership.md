---
title: Custo total de Ownership(TCO) com o Azure Cosmos DB
description: Este artigo compara o custo total de propriedade do Azure Cosmos DB com a IaaS e bases de dados locais
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c3a3305197802906077dab330a6b51c1195c6c36
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879476"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Custo total de Ownership(TCO) com o Azure Cosmos DB

O Azure Cosmos DB foi concebido com a governação de recursos e vários inquilinos detalhada. Esse design permite que o Azure Cosmos DB funcionar em utilizadores significativamente menores de custo e a ajuda guardar. Atualmente o Azure Cosmos DB suporta mais de 280 cargas de trabalho do cliente num único computador com a densidade aumentando continuamente e milhares de cargas de trabalho do cliente dentro de um cluster. Ele carga equilibra as réplicas de cargas de trabalho de clientes em computadores diferentes num cluster e em vários clusters dentro de um centro de dados para obter mais informações, consulte [do Azure Cosmos DB: Enviar por push o frontier de bases de dados distribuídos globalmente](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Devido a governação de recursos, vários inquilinos e integração nativa com o restante da infraestrutura do Azure, Azure Cosmos DB é em média, 4 a 6 vezes mais barata do que o MongoDB, Cassandra ou outros NoSQL de sistemas operacionais em execução no IaaS e até 10 vezes mais barato do que a base de dados mecanismos de em execução no local. Consulte o documento sobre [o custo total de propriedade (não) de um serviço de nuvem de base de dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

As soluções de base de dados NoSQL de sistemas operacionais, como o Apache Cassandra, MongoDB, HBase, mecanismos foram projetadas para no local. Quando oferecido como um serviço gerido que são equivalentes a um modelo do Resource Manager com uma base de dados de inquilinos para gerir os clusters de aprovisionamento e ao suporte de monitoramento. Arquiteturas de sistemas operacionais NoSQL exigem significativa sobrecarga operacional e os conhecimentos podem ser difícil e dispendioso para encontrar. Por outro lado, o Azure Cosmos DB é um serviço cloud totalmente gerido, que permite aos desenvolvedores concentrar-se na inovação de negócios, em vez de em gerenciamento e manutenção de infraestrutura de base de dados. 

Ao contrário de um serviço de base de dados nativas da cloud do Azure Cosmos DB, motores de base de dados NoSQL de sistemas operacionais não foram concebidos e criados com a governação de recursos ou multi-inquilinos refinado, como os princípios fundamentais de arquitetura. Motores de base de dados de OSS NoSQL como Cassandra e MongoDB faz uma suposição fundamental que todos os recursos da máquina virtual no qual estão a executar estão disponíveis para utilização. Muitos desses mecanismos de banco de dados não podem funcionar se a quantidade de recursos cair abaixo de um determinado limiar. Por exemplo, para VM pequena instâncias e elas estão disponíveis com configurações recomendadas de fornecedor sugerindo VMs, normalmente, em grande escala com custo mais elevado. Portanto, não é possível hospedar um NoSQL de sistemas operacionais ou quaisquer outros locais motor de base de dados e disponibilizá-lo utilizando um modelo charging baseado no consumo, como pedidos por segundo ou consumido de armazenamento.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Custo total de propriedade do Azure Cosmos DB 

O modelo de aprovisionamento sem servidor do Azure Cosmos DB elimina a necessidade de sobreaprovisionar a infraestrutura de base de dados. Recursos do Azure Cosmos DB são fornecidos sem a necessidade de configurações especializadas ou licenciamento. Como resultado, os aplicativos com suporte do Azure Cosmos DB podem executar com tanto quanto um 70 por cento Custo Total de economia de propriedade em comparação com bases de dados NoSQL de sistemas operacionais. Para obter alguns exemplos em tempo real, consulte [casos de utilização do cliente](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Outros benefícios do modelo de preços do Azure Cosmos DB:

* **Grande valor para o preço:** Os analistas de mercado, os clientes e parceiros confirmou que um valor maior de todas as funcionalidades do Azure Cosmos DB oferece para um preço muito mais reduzido em comparação comparado o que os clientes podem obter ao implementar estas soluções por conta própria ou através de outros fornecedores. A base de dados apresenta essa distribuição global, modelos de consistência de vários mestres, bem definidos e intuitiva, indexação automática são significativamente simplificada com o Azure Cosmos DB sem qualquer complexidade, sobrecarga, ou de indisponibilidade.

* **Nenhuma administração de DevOps de NoSQL, é necessária:** Com o Azure Cosmos DB um não precisa empregar DevOps para gerir implementações, realizar a manutenção, o dimensionamento ou o patch. Pode executar as todas as cargas de trabalho que faria com sistemas operacionais NoSQL cluster alojado no local ou numa infraestrutura de cloud.

![Azure Cosmos DB custo de propriedade](./media/total-cost-ownership/tco.png)

* **Capacidade de dimensionar de forma:** Débito do Azure Cosmos DB pode ser aumentado e diminuído, permitindo-lhe reduzir o custo de propriedade durante as horas de pico. Clusters de sistemas operacionais NoSQL implementados numa infraestrutura de cloud oferecem elasticidade limitada e implementações no local não são elásticas por definição. No Azure Cosmos DB, se aprovisionar mais débito, o débito é garantido dimensionadas de forma linear. Essa garantia é uma cópia de segurança por SLAs financeiros e no percentil de 99 em qualquer escala.

* **Economias de escala:** Um serviço gerido, como o Azure Cosmos DB funciona com um grande número de nós, nativamente integrado com o funcionamento em rede, armazenamento e computações. Por causa grande escala do Azure Cosmos DB, pode reduzir os custos de padronização.

* **Otimizado para a cloud:** O Azure Cosmos DB foi concebido desde o início com o isolamento de vários inquilinos e de desempenho detalhado. Isto permite colocar ideal, a execução e a milhares de inquilinos e as cargas de trabalho de balanceamento em clusters e datacenters. Por outro lado, a geração atual de bases de dados NoSQL de OSS opere no local com toda a máquina virtual pressupõe-se para executar a carga de trabalho de um único inquilino. Esses bancos de dados são também não foi concebidos para tirar partido de infraestrutura e o hardware para a totalidade de um fornecedor de cloud. Por exemplo, atualizar de uma imagem de rotina um NoSQL de OSS motor de base de dados não conhece as diferenças entre uma máquina virtual para baixo do Vs ou o fato desse disco premium já está a ser trilateral replicados. Ele não é possível tirar partido destes benefícios e passar sobre os benefícios e economia para os clientes.

* **Paga por hora:** Para cargas de trabalho em grande escala, que precisam para dimensionar em qualquer ponto no tempo, apenas são cobradas por hora. As cargas de trabalho num aplicativo normalmente variem nos tempos do ano e pelos dados que são consultados. Com o Azure Cosmos DB, pode aumentar ou reduzir verticalmente à medida que precisa e paga apenas aquilo de que precisa. Com sistemas IaaS alojado ou no local, não pode corresponder a esse modelo, porque não existe uma forma de descomissionar o hardware a cada hora. Nesses casos, pode potencialmente salvar entre 10 a 14 vezes numa média com o Azure Cosmos DB.

* **Obtenha gratuitamente o inúmeros recursos:** No Azure Cosmos DB, escreva cargas de trabalho são substancialmente barato em comparação com serviços de banco de dados alternativa. Além disso, do Azure Cosmos DB oferece recursos, como por exemplo, [indexação automática](indexing-policies.md), [Live (TTL) do tempo](time-to-live.md), [de Feed de alterações](change-feed.md) e outros sem quaisquer custos adicionais, algo que outros serviços de base de dados cobram habitualmente.

* **Utiliza unified moeda para várias cargas de trabalho:** Ao contrário das ofertas alternativas, no Azure Cosmos DB, não precisa cargas de trabalho do segmento, por exemplo, no leituras e gravações. Ou o débito de aprovisionar num por tipo de carga de trabalho que é lidos débito vs débito de escrita. No Azure Cosmos DB, o débito aprovisionado está reservado com uma moeda unificada e normalizada em termos de unidades de pedido ou RU/seg. Azure Cosmos DB não força-lhe atribuir prioridade a suas cargas de trabalho, efetuar o planeamento de capacidade ou pagar para cada tipo de capacidade em separado. Essa abordagem permite-lhe facilmente intercâmbio RU/s mesmo entre as várias operações e tipos de carga de trabalho.

* **Não necessita de aprovisionamento de VMs para dimensionar:** A maioria dos bancos de dados operacionais exigem que vá com grandes máquinas virtuais para evitar vizinhos ruidosos e a governação de recursos flexível, se pretender que o dimensionamento. Isso coloca a carga e o compromisso inicial de custo em clientes. Com o Azure Cosmos DB, pode começar com pouco e crescer como os tamanhos de carga de trabalho de grande escala, de forma totalmente integrada e sem qualquer período de indisponibilidade ou afetar a disponibilidade de dados.

* **Pode utilizar o débito aprovisionado para um limite máximo:** Pelo virtue de multiplexação de núcleo secundárias no Azure Cosmos DB, pode saturar o débito aprovisionado em parte maior do que o IaaS alojados opções ou ofertas de terceiros. Este método poupa muito mais do que as soluções alternativas.

* **Integração profunda do Azure Cosmos DB com outros serviços do Azure.** Azure Cosmos DB tem uma integração nativa com o funcionamento em rede, computação, as funções do Azure (sem servidor), IoT do Azure e outros serviços do Azure. Com esta integração, obtém o melhor desempenho, a velocidade da replicação de dados em todo o mundo com garantias robustas. As soluções de terceiros não serão possível corresponder ou seriam normalmente cobrar um suplemento para oferecer esses recursos.

* **Obtém automaticamente a elevada disponibilidade, com domínios de falha, pelo menos, 10 a 20 por predefinição:** O Azure Cosmos DB suporta a distribuição de cargas de trabalho entre domínios de falha, uma funcionalidade que é fundamental para elevada disponibilidade. Ele oferece 99.999 de elevada disponibilidade para leituras e gravações no percentil de 99 em qualquer lugar do mundo. O custo da implementação de algo semelhante a isto por si ou por meio de uma solução de terceiros, será elevada.

* **Obtém automaticamente todas as capacidades de empresa, sem custos adicionais.** O Azure Cosmos DB oferece o mais abrangente conjunto de certificações de conformidade, segurança e encriptação de inativos e em movimento, sem custos adicionais (em comparação comparada a concorrência). Obtém automaticamente disponibilidade regional em qualquer lugar do mundo. Pode expandir a sua base de dados em qualquer número de regiões do Azure e adicionar ou remover regiões a qualquer momento.

* **Pode poupar até 65% dos custos com capacidade de reserva:** O Azure Cosmos DB [capacidade de reserva](cosmos-db-reserved-capacity.md) ajuda a economizar dinheiro ao pagar previamente para recursos do Azure Cosmos DB para um ano ou três anos. Significativamente pode reduzir os custos com um ano ou três anos compromissos imediatos e guardar entre 20 e 65% descontos em comparação com os preços normais. Em suas cargas de trabalho de missão crítica, pode obter SLAs melhor em termos de capacidade de aprovisionamento.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [como do Azure Cosmos DB modelo de preços é económico para clientes](total-cost-ownership.md)
* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos de várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [o Custo Total de propriedade (não) de um serviço de nuvem de base de dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
