---
title: Escalões de preços para a base de dados do Azure para MariaDB
description: Este artigo descreve os escalões de preço da base de dados do Azure para MariaDB.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb2ba509983918a55370ae0deafd019e03f53d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60740289"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Base de dados do Azure para MariaDB escalões de preço

Pode criar uma base de dados do Azure para MariaDB servidor das três escalões de preço diferentes: Básico, fins gerais e com otimização de memória. Os escalões de preço são diferenciados pela quantidade de computação em vCores, que pode ser aprovisionado, memória por vCore e a tecnologia de armazenamento utilizada para armazenar os dados. Todos os recursos são aprovisionados ao nível do servidor de MariaDB. Um servidor pode ter uma ou mais bases de dados.

|    | **Básica** | **Fins gerais** | **Com otimização de memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Geração 5 |Geração 5 | Geração 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB e 4 TB | 5 GB e 4 TB |
| Tipo de armazenamento | Armazenamento Standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Período de retenção de cópia de segurança da base de dados | 7 de 35 dias | 7 de 35 dias | 7 de 35 dias |

Para escolher um escalão de preço, utilize a tabela seguinte como ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Básica | Cargas de trabalho que requerem capacidades de computação e desempenho de e/s. Os exemplos incluem servidores utilizados para programação ou testes ou dimensionamento pequeno raramente utilizadas aplicações. |
| Fins Gerais | A maioria das cargas de trabalho empresariais que requerem computação e memória com débito de e/s dimensionável equilibradas. Os exemplos incluem servidores para o alojamento de web e de aplicações móveis e de outras aplicações empresariais.|
| Memória Otimizada | Base de dados de elevado desempenho cargas de trabalho que requerem desempenho dentro da memória para processamento de transações mais rápido e uma simultaneidade mais elevada. Os exemplos incluem servidores que processam dados em tempo real e aplicações transacionais ou analíticas de elevado desempenho.|

Depois de criar um servidor, o número de vCores e escalão de preço (exceto para e do Basic) pode ser alterado ou reduzir verticalmente em segundos. Também independente pode ajustar a quantidade de armazenamento de cópia de segurança e o período de retenção de cópia de segurança ou reduza verticalmente sem períodos de indisponibilidade de aplicação. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. Para obter mais informações, consulte a [Dimensionar recursos](#scale-resources) secção.

## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Computação foram fornecidos recursos como vCores, que representam a CPU lógica do hardware subjacente. Geração 5 CPUs lógicas baseiam-se no Intel E5-2673 v4 (Broadwell) 2.3 GHz processadores.

## <a name="storage"></a>Armazenamento

O armazenamento que Aprovisiona é a quantidade de capacidade de armazenamento disponível para a base de dados do Azure para MariaDB server. O armazenamento é utilizado para os ficheiros de base de dados, arquivos temporários, registos de transações e o servidor de MariaDB registos. A quantidade total de armazenamento que Aprovisiona também define a capacidade de e/s disponível para o seu servidor.

|    | **Básica** | **Fins gerais** | **Com otimização de memória** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB e 4 TB | 5 GB e 4 TB |
| Tamanho de incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Mínimo de 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Mínimo de 100 IOPS<br/>Max 6000 IOPS |

É possível adicionar a capacidade de armazenamento adicional durante e após a criação do servidor. O escalão básico não fornece uma garantia IOPS. Nos fins gerais e com a otimização de memória escalões de preço, o IOPS dimensionar-se com o tamanho de armazenamento aprovisionado numa proporção de 3:1.

Pode monitorizar o consumo de e/s no portal do Azure ou através dos comandos da CLI do Azure. As métricas relevantes para monitorizar estão [limite de armazenamento, percentagem de armazenamento, armazenamento utilizado e por cento de e/s](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>For atingido o limite de armazenamento

O servidor está marcado como sendo só de leitura quando a quantidade de armazenamento gratuito alcançar a menor das opções de 5 GB ou 5% do armazenamento aprovisionado. Por exemplo, se aprovisionou 100 GB de armazenamento e a utilização real é feito por GB 95, o servidor está marcado como só de leitura. Como alternativa, se tiver aprovisionado 5 GB de armazenamento, o servidor é marcado como só de leitura quando o armazenamento gratuito chegar a menos de 250 MB.  

Apesar de o serviço tentar tornar o servidor só de leitura, todos os novos pedidos de transação de escrita são bloqueados e as transações ativas existentes continuam a executar. Quando o servidor estiver definido como só de leitura, todas as subsequentes operações de escrita e de transação falham. As consultas de leitura continuam a trabalhar sem interrupções. Depois de aumentar o armazenamento aprovisionado, o servidor fica pronto para aceitar novamente as transações de escrita.

Recomendamos que configure um alerta para ser notificado quando o armazenamento de servidor está prestes a atingir o limiar para que pode evitar que o estado só de leitura. 

Para obter mais informações, consulte a documentação sobre [como configurar um alerta](howto-alert-metric.md).

## <a name="backup"></a>Cópia de segurança

O serviço tira automaticamente cópias de segurança do seu servidor. O período de retenção mínimo para cópias de segurança é de sete dias. Pode definir um período de retenção máximo de 35 dias. O período de retenção pode ser ajustado em qualquer momento durante o tempo de vida do servidor. Pode escolher entre as cópias de segurança localmente redundantes e geograficamente redundante. As cópias de segurança georredundante também são armazenadas no [região geograficamente emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) da região em que o seu servidor é criado. Essa redundância fornece um nível de proteção em caso de desastre. Também ganha a capacidade de restaurar o servidor em qualquer outra região do Azure na qual o serviço está disponível com cópias de segurança georredundante. Não é possível alterar entre as duas opções de armazenamento de cópia de segurança depois do servidor é criado.

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar os vCores, o escalão de preço independentemente (exceto para e do Basic), a quantidade de armazenamento e o período de retenção de cópia de segurança. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. O número de vCores pode ser aumentado vertical ou horizontalmente. O período de retenção de cópia de segurança pode ser aumentado vertical ou horizontalmente de 7 35 dias. Só é possível aumentar o tamanho de armazenamento. Dimensionamento dos recursos pode ser feito através do portal ou a CLI do Azure. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Quando altera o número de vCores, ou o escalão de preço, é criada uma cópia do servidor original com a nova alocação de computação. Depois do novo servidor em execução, as ligações estão no modo ao longo para o novo servidor. Durante o momento em que quando o sistema muda ao longo para o novo servidor, podem ser estabelecidas sem ligações novas e todas as transações não confirmadas são revertidas. Esta janela varia, mas na maioria dos casos, é menos de um minuto.

Dimensionar o armazenamento e alterar o período de retenção de cópia de segurança são verdadeiras operações online. Não existe nenhum tempo de inatividade e seu aplicativo não é afetado. IOPS aumentar o tamanho do armazenamento de aprovisionamento, pode aumentar o IOPS disponível para o seu servidor, aumente verticalmente o armazenamento.

## <a name="pricing"></a>Preços

Para informações mais atualizado sobre preços, consulte o serviço [página de preços](https://azure.microsoft.com/pricing/details/mariadb/). Para ver o custo para a configuração que desejar, o [portal do Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) mostra o custo mensal sobre o **escalão de preço** separador com base nas opções que selecionar. Se não tiver uma subscrição do Azure, pode utilizar a Calculadora de preços do Azure para obter um preço estimado. Sobre o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) Web site, selecione **adicionar itens**, expanda o **bases de dados** categoria e escolha **base de dados do Azure para MariaDB**para personalizar as opções.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre o [limitações do serviço](concepts-limits.md).
- Saiba como [criar um servidor de MariaDB no portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
