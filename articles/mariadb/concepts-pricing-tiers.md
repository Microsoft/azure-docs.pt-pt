---
title: Escalões de preço – Azure Database for MariaDB
description: Saiba mais sobre os vários níveis de preços para Azure Database para MariaDB, incluindo gerações de cálculo, tipos de armazenamento, tamanho de armazenamento, vCores, memória e períodos de retenção de backup.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/9/2020
ms.openlocfilehash: 95891e35d2f30bfceb6282c6d9b3f9e7e21b3df8
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660884"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Base de Dados Azure para os níveis de preços mariaDB

Pode criar uma Base de Dados Azure para servidor MariaDB num dos três níveis de preços diferentes: Básico, Propósito Geral e Memória Otimizada. Os níveis de preços são diferenciados pela quantidade de cálculo em vCores que podem ser a provisionados, memória por vCore e a tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são abastados ao nível do servidor MariaDB. Um servidor pode ter uma ou muitas bases de dados.

|    | **Básica** | **Finalidade Geral** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Geração computacional | Gen 5 |Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Período de retenção de backup da base de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um nível de preços, utilize a tabela seguinte como ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Básica | Cargas de trabalho que requerem computação leve e desempenho de I/O. Exemplos incluem servidores utilizados para desenvolvimento ou teste ou aplicações pouco utilizadas em pequena escala. |
| Fins Gerais | A maioria das cargas de trabalho do negócio que requerem cálculo equilibrado e memória com rendimento escalável de I/O. Exemplos incluem servidores para hospedar aplicações web e móveis e outras aplicações empresariais.|
| Otimizada para Memória | Cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento de transações mais rápido e maior conúcência. Exemplos incluem servidores para o processamento de dados em tempo real e aplicações transacionais ou analíticas de alto desempenho.|

Depois de criar um servidor, o número de vCores e o nível de preços (exceto de e para o Básico) podem ser alterados para cima ou para baixo em segundos. Também pode ajustar de forma independente a quantidade de armazenamento para cima e o período de retenção de backup para cima ou para baixo sem tempo de inatividade da aplicação. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. Para mais informações, consulte a secção [de recursos da Escala.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Gerações de cálculo e vCores

Os recursos compute são fornecidos como vCores, que representam o CPU lógico do hardware subjacente. Os CPUs lógicos da Gen 5 são baseados em processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz.

## <a name="storage"></a>Armazenamento

O armazenamento que o seu fornecimento é a quantidade de capacidade de armazenamento disponível para a sua Base de Dados Azure para o servidor MariaDB. O armazenamento é utilizado para os ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores MariaDB. A quantidade total de armazenamento que fornece também define a capacidade de E/S disponível para o seu servidor.

|    | **Básica** | **Finalidade Geral** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento básico | Armazenamento de Finalidade Geral | Armazenamento de Finalidade Geral |
| Tamanho do armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS |

Pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema cresça armazenamento automaticamente com base no consumo de armazenamento da sua carga de trabalho.

>[!NOTE]
> O armazenamento só pode ser aumentado, não para baixo.

O nível básico não oferece uma garantia de IOPS. Nos níveis de preços otimizados para fins gerais e memória, a escala de IOPS com o tamanho de armazenamento a provisionado numa relação 3:1.

Pode monitorizar o seu consumo de E/S no portal Azure ou utilizando comandos Azure CLI. As métricas relevantes a monitorizar são [o limite de armazenamento, a percentagem de armazenamento, o armazenamento utilizado e a IO por cento](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Grande armazenamento (pré-visualização)

Estamos a aumentar os limites de armazenamento nos nossos níveis otimizados de Finalidade Geral e Memória. Os servidores recém-criados que optam pela pré-visualização podem prever até 16 TB de armazenamento. A escala de IOPS a uma proporção de 3:1 até 20.000 IOPS. Tal como acontece com o armazenamento geralmente disponível, pode adicionar capacidade de armazenamento adicional após a criação do servidor, e permitir que o sistema cresça armazenamento automaticamente com base no consumo de armazenamento da sua carga de trabalho.

|              | **Finalidade Geral** | **Memória Otimizada** |
|:-------------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Azure Premium | Armazenamento Azure Premium |
| Tamanho do armazenamento | 32 GB a 16 TB| 32 a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20.000 IOPS| 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20.000 IOPS |

> [!IMPORTANT]
> O grande armazenamento está atualmente em pré-visualização pública nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Central EUA, Eua Ocidental, Eua Central Do Sul, Europa Do Norte, Europa Ocidental, Reino Unido Sul, Reino Unido, Sudeste Asiático, Ásia Oriental, Japão Ocidental, Coreia Central, Coreia do Sul, Austrália Oriental, Austrália Sudeste, Eua Ocidental 2 e Centro Ocidental dos EUA.

### <a name="reaching-the-storage-limit"></a>Atingindo o limite de armazenamento

Os servidores com menos de 100 GB de armazenamento a provisionado só são marcados de leitura se o armazenamento gratuito for inferior a 5% do tamanho de armazenamento a provisionado. Os servidores com mais de 100 GB de armazenamento aprovisionado serão marcados como só de leitura se o armazenamento livre for inferior a 5 GB.

Por exemplo, se tiver provisionado 110 GB de armazenamento, e a utilização real ultrapassar os 105 GB, o servidor está marcado apenas para leitura. Alternativamente, se tiver disponibilizado 5 GB de armazenamento, o servidor só está marcado quando o armazenamento gratuito atinge menos de 256 MB.

Apesar de o serviço tentar tornar o servidor só de leitura, todos os novos pedidos de transação de escrita são bloqueados e as transações ativas existentes continuam a executar. Quando o servidor estiver definido como só de leitura, todas as subsequentes operações de escrita e de transação falham. As consultas de leitura continuam a trabalhar sem interrupções. Depois de aumentar o armazenamento aprovisionado, o servidor fica pronto para aceitar novamente as transações de escrita.

Recomendamos que ligue o armazenamento de crescimento automático ou que crie um alerta para o notificar quando o armazenamento do servidor estiver a aproximar-se do limiar para evitar entrar no estado de leitura. Para mais informações, consulte a documentação sobre [como configurar um alerta.](howto-alert-metric.md)

### <a name="storage-auto-grow"></a>Armazenamento auto-cultivo

O crescimento automático do armazenamento impede que o seu servidor fique sem armazenamento e se torne apenas de leitura. Se o crescimento automático do armazenamento estiver ativado, o armazenamento cresce automaticamente sem afetar a carga de trabalho. Para servidores com menos de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5 GB quando o armazenamento gratuito é inferior a 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento a provisionado, o tamanho de armazenamento provisionado é aumentado em 5% quando o espaço de armazenamento gratuito é inferior a 10 GB do tamanho de armazenamento provisionado. Aplicam-se limites máximos de armazenamento especificados acima.

Por exemplo, se tiver provisionado 1000 GB de armazenamento, e a utilização real ultrapassar os 990 GB, o tamanho do armazenamento do servidor é aumentado para 1050 GB. Alternativamente, se tiver disponibilizado 10 GB de armazenamento, o tamanho de armazenamento aumenta para 15 GB quando menos de 1 GB de armazenamento é gratuito.

Lembre-se que o armazenamento só pode ser aumentado, não para baixo.

## <a name="backup"></a>Cópia de segurança

O serviço retira automaticamente cópias de segurança do seu servidor. Pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Os servidores otimizados para fins gerais e memória podem optar por ter armazenamento geo-redundante para cópias de segurança. Saiba mais sobre backups no [artigo de conceitos.](concepts-backup.md)

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar independentemente os vCores, o nível de preços (exceto de e para o Básico), a quantidade de armazenamento e o período de retenção de backup. Não é possível alterar o tipo de armazenamento de cópia de segurança após a criação de um servidor. O número de vCores pode ser aumentado para cima ou para baixo. O período de retenção de backup pode ser aumentado para cima ou para baixo de 7 a 35 dias. O tamanho do armazenamento só pode ser aumentado. A escala dos recursos pode ser feita através do portal ou do Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Quando altera o número de vCores, ou o nível de preços, é criada uma cópia do servidor original com a nova alocação de computação. Depois de o novo servidor estar a funcionar em pleno, as ligações são passadas para o novo servidor. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia mas, na maioria dos casos, é inferior a um minuto.

O armazenamento de escala e a alteração do período de retenção de backup são verdadeiras operações online. Não há tempo de inatividade, e a sua candidatura não é afetada. À escala de IOPS com o tamanho do armazenamento a provisionado, pode aumentar o IOPS disponível para o seu servidor aumentando o armazenamento.

## <a name="pricing"></a>Preços

Para obter as informações de preços mais atualizadas, consulte a [página de preços do](https://azure.microsoft.com/pricing/details/mariadb/)serviço . Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.MariaDBServer) mostra o custo mensal no **separador de nível de Preços** com base nas opções selecionadas. Se não tiver uma subscrição do Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expandir a categoria **Base de Dados de Bases de Dados** e escolher a Base **de Dados Azure para MariaDB** para personalizar as opções.

## <a name="next-steps"></a>Próximos passos
- Conheça as limitações de [serviço.](concepts-limits.md)
- Saiba como [criar um servidor MariaDB no portal Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
