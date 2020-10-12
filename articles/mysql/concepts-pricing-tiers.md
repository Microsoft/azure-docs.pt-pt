---
title: Escalões de preço – Base de Dados do Azure para MySQL
description: Saiba mais sobre os vários níveis de preços para Azure Database para MySQL, incluindo gerações de cálculo, tipos de armazenamento, tamanho de armazenamento, vCores, memória e períodos de retenção de backup.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 163f440c9f98a1d53793fddab2590f7345944fda
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87171006"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Base de Dados Azure para os níveis de preços do MySQL

Pode criar uma Base de Dados Azure para o servidor MySQL num dos três níveis de preços diferentes: Básico, Propósito Geral e Memória Otimizada. Os níveis de preços são diferenciados pela quantidade de cálculo em vCores que podem ser a provisionados, memória por vCore e a tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são a provisionados ao nível do servidor MySQL. Um servidor pode ter uma ou muitas bases de dados.

| Atributo   | **Básica** | **Fins Gerais** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Geração computacional | Gen 4, Gen 5 | Gen 4, Gen 5 | Geração 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Período de retenção de backup da base de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um nível de preços, utilize a tabela seguinte como ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Básico | Cargas de trabalho que requerem computação leve e desempenho de I/O. Exemplos incluem servidores utilizados para desenvolvimento ou teste ou aplicações pouco utilizadas em pequena escala. |
| Fins Gerais | A maioria das cargas de trabalho do negócio que requerem cálculo equilibrado e memória com rendimento escalável de I/O. Os exemplos incluem servidores de alojamento de aplicações para dispositivos móveis e Web, entre outras aplicações empresariais.|
| Otimizada para Memória | Cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento de transações mais rápido e maior conúcência. Os exemplos incluem servidores para processamento de dados em tempo real e aplicações com elevado desempenho transacional ou analítico.|

Depois de criar um servidor, o número de vCores, geração de hardware e nível de preços (exceto de e para o Básico) pode ser alterado para cima ou para baixo em segundos. Também pode ajustar de forma independente a quantidade de armazenamento para cima e o período de retenção de backup para cima ou para baixo sem tempo de inatividade da aplicação. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. Para mais informações, consulte a secção [de recursos da Escala.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Gerações de cálculo e vCores

Os recursos compute são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. China East 1, China Norte 1, US DoD Central, e EUA DoD East utilizam CPUs lógicos gen 4 que são baseados em processadores Intel E5-2673 v3 (Haswell) 2.4-GHz. Todas as outras regiões utilizam CPUs lógicos da Gen 5 que são baseados em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz.

## <a name="storage"></a>Armazenamento

O armazenamento que fornece é a quantidade de capacidade de armazenamento disponível para a sua Base de Dados Azure para o servidor MySQL. O armazenamento é utilizado para os ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores MySQL. A quantidade total de armazenamento que fornece também define a capacidade de E/S disponível para o seu servidor.

| Atributo de armazenamento   | Básico | Fins gerais | Com otimização de memória |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento básico | Armazenamento de Finalidade Geral | Armazenamento de Finalidade Geral |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 16 TB | 5 GB a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20.000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20.000 IOPS |

> [!NOTE]
> O armazenamento até 16TB e 20.000 IOPS é suportado nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Central EUA, EUA Ocidentais, Eua Central Do Norte, América do Sul, Europa Do Norte, Europa Ocidental, Reino Unido, Reino Unido Oeste, Sudeste Asiático, Ásia Oriental, Japão Ocidental, Coreia Central, Coreia do Sul, Austrália Oriental, Austrália Sudeste, Oeste dos EUA e Centro Ocidental dos EUA.
>
> Todas as outras regiões suportam até 4TB de armazenamento e até 6000 IOPS.
>

Pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema cresça armazenamento automaticamente com base no consumo de armazenamento da sua carga de trabalho. 

>[!NOTE]
> O armazenamento só pode ser aumentado, não para baixo.

O nível básico não oferece uma garantia de IOPS. Nos níveis de preços otimizados para fins gerais e memória, a escala de IOPS com o tamanho de armazenamento a provisionado numa relação 3:1.

Pode monitorizar o seu consumo de E/S no portal Azure ou utilizando comandos Azure CLI. As métricas relevantes a monitorizar são [o limite de armazenamento, a percentagem de armazenamento, o armazenamento utilizado e a IO por cento](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Atingindo o limite de armazenamento

Os servidores com menos de 100 GB de armazenamento a provisionado só são marcados de leitura se o armazenamento gratuito for inferior a 5% do tamanho de armazenamento a provisionado. Os servidores com mais de 100 GB de armazenamento aprovisionado serão marcados como só de leitura se o armazenamento livre for inferior a 5 GB.

Por exemplo, se tiver provisionado 110 GB de armazenamento, e a utilização real ultrapassar os 105 GB, o servidor está marcado apenas para leitura. Alternativamente, se tiver disponibilizado 5 GB de armazenamento, o servidor só está marcado quando o armazenamento gratuito atinge menos de 256 MB.

Apesar de o serviço tentar tornar o servidor só de leitura, todos os novos pedidos de transação de escrita são bloqueados e as transações ativas existentes continuam a executar. Quando o servidor estiver definido como só de leitura, todas as subsequentes operações de escrita e de transação falham. As consultas de leitura continuam a trabalhar sem interrupções. Depois de aumentar o armazenamento aprovisionado, o servidor fica pronto para aceitar novamente as transações de escrita.

Recomendamos que ligue o armazenamento de crescimento automático ou que crie um alerta para o notificar quando o armazenamento do servidor estiver a aproximar-se do limiar para evitar entrar no estado de leitura. Para mais informações, consulte a documentação sobre [como configurar um alerta.](howto-alert-on-metric.md)

### <a name="storage-auto-grow"></a>Armazenamento auto-cultivo

O crescimento automático do armazenamento impede que o seu servidor fique sem armazenamento e se torne apenas de leitura. Se o crescimento automático do armazenamento estiver ativado, o armazenamento cresce automaticamente sem afetar a carga de trabalho. Para servidores com menos de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5 GB quando o armazenamento gratuito é inferior a 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 10 GB do tamanho de armazenamento provisionado. Aplicam-se limites máximos de armazenamento especificados acima.

Por exemplo, se tiver provisionado 1000 GB de armazenamento, e a utilização real ultrapassar os 990 GB, o tamanho do armazenamento do servidor é aumentado para 1050 GB. Alternativamente, se tiver disponibilizado 10 GB de armazenamento, o tamanho de armazenamento aumenta para 15 GB quando menos de 1 GB de armazenamento é gratuito.

Lembre-se que o armazenamento só pode ser aumentado, não para baixo.

## <a name="backup-storage"></a>Armazenamento de backup 

A Azure Database for MySQL fornece até 100% do armazenamento do servidor a provisionado como armazenamento de backup sem custos adicionais. Qualquer armazenamento de reserva que utilize acima deste valor é cobrado em GB por mês. Por exemplo, se fornecer um servidor com 250 GB de armazenamento, terá 250 GB de armazenamento adicional disponível para cópias de segurança do servidor sem custos. O armazenamento para cópias de segurança superiores aos 250 GB é cobrado de acordo com o [modelo de preços](https://azure.microsoft.com/pricing/details/mysql/). Para compreender os fatores que influenciam o uso do armazenamento de backup, monitorizando e controlando o custo de armazenamento de backup, pode consultar a [documentação de backup](concepts-backup.md).

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar independentemente os vCores, a geração de hardware, o nível de preços (exceto de e para o Básico), a quantidade de armazenamento e o período de retenção de backup. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. O número de vCores pode ser aumentado para cima ou para baixo. O período de retenção de backup pode ser aumentado para cima ou para baixo de 7 a 35 dias. O tamanho do armazenamento só pode ser aumentado. A escala dos recursos pode ser feita através do portal ou do Azure CLI. Para um exemplo de escala através da utilização do Azure CLI, consulte [o Monitor e dimensione uma Base de Dados Azure para o servidor MySQL utilizando o Azure CLI](scripts/sample-scale-server.md).

Quando altera o número de vCores, a geração de hardware ou o nível de preços, uma cópia do servidor original é criada com a nova alocação de computação. Depois de o novo servidor estar a funcionar em pleno, as ligações são passadas para o novo servidor. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia mas, na maioria dos casos, é inferior a um minuto.

O armazenamento de escala e a alteração do período de retenção de backup são verdadeiras operações online. Não há tempo de inatividade, e a sua candidatura não é afetada. À escala de IOPS com o tamanho do armazenamento a provisionado, pode aumentar o IOPS disponível para o seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para obter as informações de preços mais atualizadas, consulte a [página de preços do](https://azure.microsoft.com/pricing/details/mysql/)serviço . Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) mostra o custo mensal no **separador de nível de Preços** com base nas opções selecionadas. Se não tiver uma subscrição do Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expanda a categoria **Base de Dados** e escolha a Base de **Dados Azure para o MySQL** para personalizar as opções.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um servidor MySQL no portal](howto-create-manage-server-portal.md).
- Saiba mais sobre [os limites de serviço.](concepts-limits.md)
- Aprenda a [escalar com réplicas de leitura.](howto-read-replicas-portal.md)
