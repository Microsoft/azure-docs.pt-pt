---
title: Escalões de preços para a base de dados do Azure para PostgreSQL
description: Este artigo descreve os escalões de preço da base de dados do Azure para PostgreSQL.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 0df36f4c939ed9bd7bb6a134a356b37d699abc1d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586348"
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Base de dados do Azure para PostgreSQL escalões de preço

Pode criar uma base de dados do Azure para o servidor PostgreSQL dos três escalões de preço diferentes: Básico, fins gerais e com otimização de memória. Os escalões de preço são diferenciados pela quantidade de computação em vCores, que pode ser aprovisionado, memória por vCore e a tecnologia de armazenamento utilizada para armazenar os dados. Todos os recursos são aprovisionados ao nível do servidor PostgreSQL. Um servidor pode ter uma ou mais bases de dados.

|    | **Básica** | **Fins gerais** | **Com otimização de memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Geração 4, geração 5 | Geração 4, geração 5 | Geração 5 |
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

Depois de criar um servidor, o número de vCores, geração de hardware e preços do escalão (exceto para e do Basic) pode ser alterado ou reduzir verticalmente em segundos. Também independente pode ajustar a quantidade de armazenamento de cópia de segurança e o período de retenção de cópia de segurança ou reduza verticalmente sem períodos de indisponibilidade de aplicação. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. Para obter mais informações, consulte a [Dimensionar recursos](#scale-resources) secção.


## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Computação foram fornecidos recursos como vCores, que representam a CPU lógica do hardware subjacente. Atualmente, pode escolher entre duas gerações de computação, geração 4 e 5 de fins. Geração 4 CPUs lógicas baseiam-se no Intel E5-2673 v3 processadores de 2,4 GHz (Haswell). Geração 5 CPUs lógicas baseiam-se no Intel E5-2673 v4 (Broadwell) 2.3 GHz processadores. Gen 4 e 5 de fins estão disponíveis nas seguintes regiões (o "X" denota disponível). 

> [!IMPORTANT]
> A partir de 12 de Dezembro de 2018, os novos clientes serão não conseguir aprovisionar servidores de geração 4 de computação no sul do Brasil, Canadá Central, leste do Canadá, Ásia Oriental, E.U.A. Leste 2, Índia Central, Índia Ocidental, oeste do Japão, e.u.a. Centro-Norte, E.U.A. oeste. Geração de computação 4 servidores serão migrados para a geração 5 a partir de 1 de Fevereiro de 2019 nestas regiões de computação foi criado anteriormente.
>
> [!IMPORTANT]
> A partir de 19 de Fevereiro de 2019 novos clientes serão não conseguir aprovisionar servidores de geração 4 de computação no E.U.A. Central, E.U.A. leste, leste do Japão, Norte da Europa, Centro-Sul, Sudeste asiático, Europa Ocidental. Geração de computação 4 servidores serão migrados para a geração 5 a partir de 1 de Abril de 2019 nestas regiões de computação foi criado anteriormente.

| **Região do Azure** | **Geração 4** | **Geração 5** |
|:---|:----------:|:--------------------:|
| EUA Central |  | X |
| EUA Leste |  | X |
| EUA Leste 2 | X | X |
| EUA Centro-Norte | X | X |
| EUA Centro-Sul | X | X |
| EUA Oeste | X | X |
| EUA Oeste 2 |  | X |
| Sul do Brasil | X | X |
| Canadá Central | X | X |
| Leste do Canadá | X | X |
| Europa do Norte | X | X |
| Europa Ocidental |  | X |
| França Central |  | X |
| Reino Unido Sul |  | X |
| Reino Unido Oeste |  | X |
| Ásia Oriental | X | X |
| Sudeste Asiático | X | X |
| Leste da Austrália |  | X |
| Austrália Central |  | X |
| Austrália Central 2 |  | X |
| Sudeste da Austrália |  | X |
| Índia Central | X | X |
| Sul da Índia |  | X |
| Oeste da Índia | X | X |
| Leste do Japão | X | X |
| Oeste do Japão | X | X |
| Coreia do Sul Central |  | X |
| Coreia do Sul |  | X |
| Leste da China 1 | X |  |
| Leste da China 2 |  | X |
| Norte da China 1 | X |  |
| Norte da China 2 |  | X |
| Alemanha Central |  | X |
| US DoD Centro  | X |  |
| US DoD Leste  | X |  |
| Gov (US) - Arizona |  | X |
| Gov (US) - Texas |  | X |
| Gov (US) - Virginia |  | X |

## <a name="storage"></a>Armazenamento

O armazenamento que Aprovisiona é a quantidade de capacidade de armazenamento disponível para a base de dados do Azure para o servidor PostgreSQL. O armazenamento é utilizado para os ficheiros de base de dados, arquivos temporários, registos de transações e o servidor PostgreSQL registos. A quantidade total de armazenamento que Aprovisiona também define a capacidade de e/s disponível para o seu servidor.

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

Quando o servidor está definido só de leitura, todas as sessões existentes estão ligado à Internet e as transações não confirmadas são revertidas. Quaisquer operações de escrita subsequentes e a transação consolida a ativação. Todas as consultas de leitura subsequentes irão funcionar sem interrupções.  

Pode aumentar a quantidade de armazenamento aprovisionado para o seu servidor ou iniciar uma nova sessão nos dados de modo e soltar de leitura / escrita de reclamar armazenamento gratuito. Executar `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` define a sessão atual para ler o modo de escrita. Para evitar danos em dados, executa quaisquer operações de escrita quando o servidor ainda está no estado só de leitura.

Recomendamos que configure um alerta para ser notificado quando o armazenamento de servidor está prestes a atingir o limiar para que pode evitar que o estado só de leitura. Para obter mais informações, consulte a documentação sobre [como configurar um alerta](howto-alert-on-metric.md).

## <a name="backup"></a>Cópia de segurança

O serviço tira automaticamente cópias de segurança do seu servidor. O período de retenção mínimo para cópias de segurança é de sete dias. Pode definir um período de retenção máximo de 35 dias. O período de retenção pode ser ajustado em qualquer momento durante o tempo de vida do servidor. Pode escolher entre as cópias de segurança localmente redundantes e geograficamente redundante. As cópias de segurança georredundante também são armazenadas no [região geograficamente emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) da região em que o seu servidor é criado. Essa redundância fornece um nível de proteção em caso de desastre. Também ganha a capacidade de restaurar o servidor em qualquer outra região do Azure na qual o serviço está disponível com cópias de segurança georredundante. Não é possível alterar entre as duas opções de armazenamento de cópia de segurança depois do servidor é criado.

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, independente pode alterar os vCores, a geração de hardware, o escalão de preço (exceto para e do Basic), a quantidade de armazenamento e o período de retenção de cópia de segurança. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. O número de vCores pode ser aumentado vertical ou horizontalmente. O período de retenção de cópia de segurança pode ser aumentado vertical ou horizontalmente de 7 35 dias. Só é possível aumentar o tamanho de armazenamento. Dimensionamento dos recursos pode ser feito através do portal ou a CLI do Azure. Para obter um exemplo de dimensionamento ao utilizar a CLI do Azure, veja [monitorizar e dimensionar uma base de dados do Azure para o servidor PostgreSQL com a CLI do Azure](scripts/sample-scale-server-up-or-down.md).

Quando altera o número de vCores, a geração de hardware ou o escalão de preço, uma cópia do servidor original é criada com a nova alocação de computação. Depois do novo servidor em execução, as ligações estão no modo ao longo para o novo servidor. Durante o momento em que quando o sistema muda ao longo para o novo servidor, podem ser estabelecidas sem ligações novas e todas as transações não confirmadas são revertidas. Esta janela varia, mas na maioria dos casos, é menos de um minuto.

Dimensionar o armazenamento e alterar o período de retenção de cópia de segurança são verdadeiras operações online. Não existe nenhum tempo de inatividade e seu aplicativo não é afetado. IOPS aumentar o tamanho do armazenamento de aprovisionamento, pode aumentar o IOPS disponível para o seu servidor, aumente verticalmente o armazenamento.

## <a name="pricing"></a>Preços

Para informações mais atualizado sobre preços, consulte o serviço [página de preços](https://azure.microsoft.com/pricing/details/PostgreSQL/). Para ver o custo para a configuração que desejar, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal sobre o **escalão de preço** separador com base nas opções que selecionar. Se não tiver uma subscrição do Azure, pode utilizar a Calculadora de preços do Azure para obter um preço estimado. Sobre o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) Web site, selecione **adicionar itens**, expanda o **bases de dados** categoria e escolha **base de dados do Azure para PostgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um servidor PostgreSQL no portal do](tutorial-design-database-using-azure-portal.md).
- Saiba mais sobre [limites de serviço](concepts-limits.md). 
- Saiba como [aumentar horizontalmente com a leitura réplicas](howto-read-replicas-portal.md).
