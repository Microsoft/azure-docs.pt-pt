---
title: Custo total de propriedade (TCO) com Azure Cosmos DB
description: Este artigo compara o custo total de propriedade de Azure Cosmos DB com IaaS e bancos de dados locais
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754798"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Custo total de propriedade (TCO) com Azure Cosmos DB

O Azure Cosmos DB é projetado com a multilocação e a governança de recursos refinados. Esse design permite que Azure Cosmos DB operem com custo significativamente menor e ajudam os usuários a salvar. Atualmente Azure Cosmos DB dá suporte a mais de 280 cargas de trabalho do cliente em um único computador com a densidade aumentando continuamente e milhares de cargas de trabalho do cliente em um cluster. Ela equilibra as réplicas das cargas de trabalho dos clientes em diferentes máquinas em um cluster e em vários clusters dentro de um data center. Para saber mais, confira [Azure Cosmos DB: enviando a fronteira de bancos de dados distribuídos globalmente](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Por causa do controle de recursos, multilocação e integração nativa com o restante da infraestrutura do Azure, Azure Cosmos DB está em média 4 a 6 vezes mais barata do que MongoDB, Cassandra ou outros NoSQL OSS em execução em IaaS e até 10 vezes mais barato do que o banco de dados mecanismos em execução no local. Confira o artigo sobre [o custo total de propriedade (não) de um serviço de nuvem de banco de dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

As soluções de banco de dados NoSQL OSS, como Apache Cassandra, MongoDB, HBase, mecanismos foram projetadas para locais. Quando oferecido como um serviço gerenciado, eles são equivalentes a um modelo do Resource Manager com um banco de dados de locatário para gerenciar os clusters provisionados e o suporte ao monitoramento. As arquiteturas de NoSQL OSS exigem sobrecarga operacional significativa e a experiência pode ser difícil e dispendiosa de ser encontrada. Por outro lado, Azure Cosmos DB é um serviço de nuvem totalmente gerenciado, que permite aos desenvolvedores se concentrar na inovação de negócios em vez de gerenciar e manter a infraestrutura de banco de dados. 

Ao contrário de um serviço de banco de dados nativo de nuvem Azure Cosmos DB, os mecanismos de banco de dados NoSQL OSS não foram projetados e compilados com a governança de recursos ou multilocação refinada como os princípios fundamentais da arquitetura. Os mecanismos de banco de dados NoSQL OSS, como Cassandra e MongoDB, fazem uma suposição fundamental de que todos os recursos da máquina virtual em que estão sendo executados estejam disponíveis para seu uso. Muitos desses mecanismos de banco de dados não podem funcionar se a quantidade de recursos cair abaixo de um determinado limite. Por exemplo, para instâncias de VM pequenas, e elas estão disponíveis com configurações recomendadas pelo fornecedor sugerindo normalmente VMs de grande escala com custo mais alto. Portanto, não é possível hospedar um NoSQL OSS ou qualquer outro mecanismo de banco de dados local e disponibilizá-lo usando um modelo de carregamento baseado em consumo como solicitações por segundo ou armazenamento consumido.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Custo total de propriedade de Azure Cosmos DB 

O modelo de provisionamento sem servidor do Azure Cosmos DB elimina a necessidade de provisionar o provisionamento da infraestrutura do banco de dados. Azure Cosmos DB recursos são fornecidos sem a necessidade de configurações especializadas ou licenciamento. Como resultado, os aplicativos com backup Azure Cosmos DB podem ser executados com uma economia de custo total de propriedade de 70% em comparação com os bancos de dados NoSQL OSS. Para alguns exemplos em tempo real, consulte [casos de uso do cliente](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Outros benefícios do modelo de preços Azure Cosmos DB incluem:

* **Valor ótimo para o preço:** Analistas de mercado, clientes e parceiros confirmaram um valor maior de todos os recursos que a Azure Cosmos DB oferece para um preço muito menor em comparação com o que os clientes podem obter ao implementar essas soluções por conta própria ou por meio de outros fornecedores. O banco de dados apresenta modelos de consistência de distribuição global, de vários mestres, bem definidos e intuitivos, a indexação automática é bastante simplificada com Azure Cosmos DB sem nenhuma complexidade, sobrecarga ou tempo de inatividade.

* **Nenhuma administração de DevOps NoSQL é necessária:** Com Azure Cosmos DB um não precisa empregar DevOps para gerenciar implantações, executar a manutenção, a escala ou o patch. Você pode executar todas as cargas de trabalho que faria com o cluster NoSQL OSS hospedado no local ou na infraestrutura de nuvem.

![Azure Cosmos DB custo de propriedade](./media/total-cost-ownership/tco.png)

* **Capacidade de dimensionar de forma elástica:** A taxa de transferência de Azure Cosmos DB pode ser ampliada e reduzida, permitindo que você reduza o custo de propriedade fora do horário de pico. Os clusters NoSQL OSS implantados na infraestrutura de nuvem oferecem elasticidade limitada, e implantações locais não são elásticas por definição. Em Azure Cosmos DB, se você provisionar mais taxa de transferência, a taxa de transferência é garantida para escalar linearmente. Essa garantia é submetida a backup por SLAs financeiros e no 99 º percentil em qualquer escala.

* **Economias de escala:** Um serviço gerenciado como o Azure Cosmos DB opera com um grande número de nós, integrado nativamente com rede, armazenamento e computações. Devido à grande escala, a padronização de Azure Cosmos DB você pode economizar custos.

* **Otimizado para a nuvem:** O Azure Cosmos DB foi projetado desde o início com multilocação e isolamento de desempenho refinados. Isso permite colocar, executar e balancear de forma ideal milhares de locatários e suas cargas de trabalho entre clusters e data centers. Por outro lado, a geração atual de bancos de dados NoSQL OSS Opera localmente com toda a máquina virtual assumida para executar a carga de trabalho de um único locatário. Esses bancos de dados também não são projetados para aproveitar a infraestrutura e o hardware de um provedor de nuvem para toda a extensão. Por exemplo, um mecanismo de banco de dados NoSQL OSS não está ciente das diferenças entre uma máquina virtual estar inoperante versus uma atualização de imagem de rotina ou o fato de que o disco Premium já é replicado de três vias. Ele não pode aproveitar esses benefícios e passar os benefícios e as economias para os clientes.

* **Você paga por hora:** Para cargas de trabalho de grande escala que precisam ser dimensionadas a qualquer momento, você só é cobrado por hora. As cargas de trabalho em um aplicativo normalmente variam em horários do ano e pelos dados que são consultados. Com o Azure Cosmos DB, você pode escalar ou reduzir verticalmente conforme necessário e pagar apenas pelo que precisa. Com sistemas locais ou instalados em IaaS, você não pode corresponder a esse modelo, pois não há uma maneira de encerrar o hardware a cada hora. Nesses casos, você pode potencialmente economizar entre 10 e 14 vezes em uma média com Azure Cosmos DB.

* **Você obtém vários recursos gratuitamente:** Em Azure Cosmos DB, as cargas de trabalho de gravação são consideravelmente mais baratas em comparação com os serviços de banco de dados alternativos. Além disso, o Azure Cosmos DB oferece recursos como, por exemplo, [indexação automática](indexing-policies.md), [TTL (vida útil)](time-to-live.md), [feed de alterações](change-feed.md) e outros sem cobranças adicionais, algo que os outros serviços de banco de dados normalmente cobram.

* **Usa moeda unificada para cargas de trabalho diversas:** Ao contrário das ofertas alternativas, no Azure Cosmos DB, você não precisa segmentar cargas de trabalho, por exemplo, em leituras e gravações. Ou Provisione a taxa de transferência em um tipo por carga de trabalho de leitura versus taxa de transferência de gravação. Em Azure Cosmos DB, a taxa de transferência provisionada é reservada usando uma moeda unificada e normalizada em termos de unidades de solicitação ou RU/s. Azure Cosmos DB não força você a atribuir prioridade às suas cargas de trabalho, executar planejamento de capacidade ou pagar por cada tipo de capacidade parte. Essa abordagem permite trocar facilmente os mesmos RU/s entre várias operações e tipos de carga de trabalho.

* **Não requer que as VMs de provisionamento sejam dimensionadas:** A maioria dos bancos de dados operacionais exige que você vá para máquinas virtuais grandes para evitar vizinhos com ruído e controle de recursos flexíveis, se desejar a escala. Isso coloca a carga e o comprometimento antecipado do custo dos clientes. Com o Azure Cosmos DB, você pode começar pequeno e crescer em tamanhos de carga de trabalho de grande escala sem problemas, sem nenhum tempo de inatividade ou impacto na disponibilidade dos dados.

* **Você pode utilizar a taxa de transferência provisionada para um limite máximo:** Em virtude da multiplexação de subnúcleos no Azure Cosmos DB, você pode saturar a taxa de transferência provisionada para uma extensão maior do que as opções hospedadas de IaaS ou ofertas de terceiros. Esse método economiza muito mais do que as soluções alternativas.

* **Integração profunda do Azure Cosmos DB com outros serviços do Azure.** O Azure Cosmos DB tem uma integração nativa com rede, computação, Azure Functions (sem servidor), IoT do Azure e outros serviços do Azure. Com essa integração, você obtém o melhor desempenho, a velocidade da replicação de dados em todo o mundo, com garantias robustas. As soluções de terceiros não poderão ser correspondidas ou, normalmente, cobrarão uma Premium para oferecer esses recursos.

* **Você obtém automaticamente alta disponibilidade, com pelo menos 10-20 domínios de falha por padrão:** O Azure Cosmos DB dá suporte à distribuição de cargas de trabalho entre domínios de falha, um recurso essencial para alta disponibilidade. Ele oferece alta disponibilidade de 99,999 para leituras e gravações no 99 º percentil em qualquer lugar do mundo. O custo da implementação de algo como esse por conta própria ou por meio de uma solução de terceiros seria alto.

* **Você obtém automaticamente todos os recursos corporativos, sem nenhum custo adicional.** A Azure Cosmos DB oferece o conjunto mais abrangente de certificações de conformidade, segurança e criptografia em repouso e em movimento sem custo adicional (em comparação com nossa competição). Você obtém automaticamente a disponibilidade regional em qualquer lugar do mundo. Você pode expandir seu banco de dados em qualquer número de regiões do Azure e adicionar ou remover regiões em qualquer ponto.

* **Você pode economizar até 65% dos custos com capacidade reservada:** Azure Cosmos DB [capacidade reservada](cosmos-db-reserved-capacity.md) ajuda você a economizar dinheiro por meio do pagamento antecipado de Azure Cosmos DB recursos por um ou três anos. Você pode reduzir significativamente os custos com compromissos antecipados de um ou três anos e economizar entre 20-65% de descontos quando comparado ao preço regular. Em suas cargas de trabalho de missão crítica, você pode obter SLAs melhores em termos de capacidade de provisionamento.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [como Azure Cosmos DB modelo de preços é econômico para os clientes](total-cost-ownership.md)
* Saiba mais sobre como [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre como [otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre como [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre como [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre como [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre como [otimizar o custo de contas de Cosmos de várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [o custo total de propriedade (não) de um serviço de nuvem de banco de dados NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
