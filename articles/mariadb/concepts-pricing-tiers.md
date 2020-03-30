---
title: Escalões de preço – Azure Database for MariaDB
description: Conheça os vários níveis de preços para a Base de Dados Azure para OMariaDB, incluindo gerações de cálculo, tipos de armazenamento, tamanho de armazenamento, vCores, memória e períodos de retenção de backup.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 39af6850810fa471003cea27ed274972fb2ff046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528035"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Base de Dados Azure para níveis de preços MariaDB

Pode criar uma Base de Dados Azure para o servidor MariaDB num dos três diferentes níveis de preços: Básico, Propósito Geral e Otimização da Memória. Os níveis de preços são diferenciados pela quantidade de computação em vCores que pode ser provisionado, memória por vCore, e a tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são aprovisionados ao nível do servidor MariaDB. Um servidor pode ter uma ou muitas bases de dados.

|    | **Básico** | **Propósito Geral** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Geração computacional | Gen 5 |Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2GB | 5 GB | 10 GB |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Período de retenção de backup de base de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um nível de preços, utilize a tabela seguinte como ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Básico | Cargas de trabalho que requerem computação leve e desempenho em I/S. Exemplos incluem servidores usados para desenvolvimento ou teste ou aplicações de pequena escala pouco utilizadas. |
| Fins Gerais | A maioria das cargas de trabalho empresariais que requerem computação equilibrada e memória com uma potência de I/S escalável. Exemplos incluem servidores para hospedar aplicações web e móveis e outras aplicações empresariais.|
| Otimizada para Memória | Cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento mais rápido de transações e uma maior conmoedação. Exemplos incluem servidores para processamento de dados em tempo real e aplicações transalíticas ou analíticas de alto desempenho.|

Depois de criar um servidor, o número de vCores e o nível de preços (exceto de e para o Basic) podem ser alterados para cima ou para baixo em segundos. Também pode ajustar a quantidade de armazenamento para cima e o período de retenção de reserva para cima ou para baixo sem tempo de inatividade de aplicação. Não é possível alterar o tipo de armazenamento de cópia de segurança depois de um servidor ser criado. Para mais informações, consulte a secção de [recursos da Escala.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Gerações computadas e vCores

Os recursos computacionais são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. Os CPUs lógicos da Gen 5 baseiam-se em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz.

## <a name="storage"></a>Storage

O armazenamento que você disponibiliza é a quantidade de capacidade de armazenamento disponível para a sua Base de Dados Azure para o servidor MariaDB. O armazenamento é utilizado para os ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores MariaDB. A quantidade total de armazenamento que disponibiliza também define a capacidade de I/S disponível para o seu servidor.

|    | **Básico** | **Propósito Geral** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Básico | Armazenamento de propósito geral | Armazenamento de propósito geral |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS |

Pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor, e permitir que o sistema cresça o armazenamento automaticamente com base no consumo de armazenamento da sua carga de trabalho.

>[!NOTE]
> O armazenamento só pode ser dimensionado, não para baixo.

O nível básico não fornece uma garantia iops. Nos níveis de preços otimizados para fins gerais e memória, a escala IOPS com o tamanho de armazenamento provisionado numa relação de 3:1.

Pode monitorizar o seu consumo de I/S no portal Azure ou utilizando comandos Azure CLI. As métricas relevantes para monitorizar são o limite de [armazenamento, a percentagem de armazenamento, o armazenamento utilizado, e a OI por cento](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Atingir o limite de armazenamento

Os servidores com armazenamento com menos de 100 GB de armazenamento provisionado são marcados apenas de leitura se o armazenamento gratuito for inferior a 5% do tamanho de armazenamento provisionado. Os servidores com mais de 100 GB de armazenamento aprovisionado serão marcados como só de leitura se o armazenamento livre for inferior a 5 GB.

Por exemplo, se tiver aprovisionado 110 GB de armazenamento, e a utilização real ultrapassar os 105 GB, o servidor é marcado apenas para leitura. Em alternativa, se tiver aprovisionado 5 GB de armazenamento, o servidor é marcado apenas quando o armazenamento gratuito atinge menos de 256 MB.

Apesar de o serviço tentar tornar o servidor só de leitura, todos os novos pedidos de transação de escrita são bloqueados e as transações ativas existentes continuam a executar. Quando o servidor estiver definido como só de leitura, todas as subsequentes operações de escrita e de transação falham. As consultas de leitura continuam a trabalhar sem interrupções. Depois de aumentar o armazenamento aprovisionado, o servidor fica pronto para aceitar novamente as transações de escrita.

Recomendamos que ligue o armazenamento de modo a crescer automaticamente ou que instale um alerta para o notificar quando o armazenamento do servidor se aproxima do limiar para evitar entrar no estado de leitura. Para mais informações, consulte a documentação sobre [como configurar um alerta](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Armazenamento de automóveis

O armazenamento de forma automática impede que o servidor fique sem armazenamento e se torne apenas para leitura. Se o armazenamento automático crescer, o armazenamento cresce automaticamente sem afetar a carga de trabalho. Para servidores com armazenamento inferior a 100 GB, o tamanho de armazenamento provisionado é aumentado em 5 GB quando o armazenamento gratuito é inferior a 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 10 GB do tamanho de armazenamento provisionado. Aplicam-se os limites máximos de armazenamento acima referidos.

Por exemplo, se tiver aprovisionado 1000 GB de armazenamento, e a utilização real ultrapassar os 990 GB, o tamanho do armazenamento do servidor é aumentado para 1050 GB. Em alternativa, se tiver previsto 10 GB de armazenamento, o tamanho do armazenamento aumenta para 15 GB quando menos de 1 GB de armazenamento é gratuito.

Lembre-se que o armazenamento só pode ser dimensionado para cima, não para baixo.

## <a name="backup"></a>Cópia de segurança

O serviço recebe automaticamente cópias de segurança do seu servidor. Pode selecionar um período de retenção entre um intervalo de 7 a 35 dias. Os servidores otimizados para fins gerais e memória podem optar por ter armazenamento geo-redundante para cópias de segurança. Saiba mais sobre backups no [artigo conceitos.](concepts-backup.md)

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar de forma independente os vCores, o nível de preços (exceto de e para o Básico), a quantidade de armazenamento e o período de retenção de backup. Não é possível alterar o tipo de armazenamento de cópia de segurança depois de um servidor ser criado. O número de vCores pode ser dimensionado para cima ou para baixo. O período de retenção de reserva pode ser dimensionado para cima ou para baixo de 7 a 35 dias. O tamanho do armazenamento só pode ser aumentado. A escala dos recursos pode ser feita através do portal ou do Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Quando altera o número de vCores, ou o nível de preços, é criada uma cópia do servidor original com a nova alocação de cálculo. Depois de o novo servidor estar a funcionar em pleno, as ligações são passadas para o novo servidor. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia mas, na maioria dos casos, é inferior a um minuto.

O armazenamento de escala e a alteração do período de retenção de cópias de segurança são verdadeiras operações online. Não há tempo de descanso, e a sua candidatura não foi afetada. À medida que a escala IOPS com o tamanho do armazenamento provisionado, pode aumentar o IOPS disponível para o seu servidor, aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para obter as informações mais atualizadas sobre preços, consulte a página de [preços](https://azure.microsoft.com/pricing/details/mariadb/)do serviço . Para ver o custo da configuração que deseja, o [portal Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) mostra o custo mensal no separador **de nível** de preços com base nas opções que selecionar. Se não tiver uma subscrição Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da calculadora de [preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria Bases de **Dados** e escolher a Base de **Dados Azure para o MariaDB** personalizar as opções.

## <a name="next-steps"></a>Passos seguintes
- Conheça as limitações do [serviço.](concepts-limits.md)
- Aprenda a [criar um servidor MariaDB no portal Azure.](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
