---
title: Tipos de preço do banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve os tipos de preço do banco de dados do Azure para PostgreSQL-servidor único.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 2f6be256801983924cc794d6c8b8fa31e39959e1
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967862"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Tipos de preço no banco de dados do Azure para PostgreSQL-servidor único

Você pode criar um servidor de banco de dados do Azure para PostgreSQL em um dos três tipos de preço diferentes: Básico, Uso Geral e otimizado para memória. Os tipos de preço são diferenciados pela quantidade de computação em vCores que pode ser provisionada, memória por vCore e a tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são provisionados no nível do servidor PostgreSQL. Um servidor pode ter um ou vários bancos de dados.

|    | **Básica** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Geração de computação | Gen 4, Gen 5 | Gen 4, Gen 5 | Geração 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memória por vCore | 2 GB | 5 GB | 10 GB |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tipo de armazenamento | Armazenamento standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Período de retenção do backup do banco de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um tipo de preço, use a tabela a seguir como um ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Básica | Cargas de trabalho que exigem desempenho de e/s e computação leve. Os exemplos incluem servidores usados para desenvolvimento ou teste ou aplicativos usados com pouca frequência de pequena escala. |
| Fins Gerais | A maioria das cargas de trabalho comerciais que exigem computação e memória balanceadas com taxa de transferência de e/s escalonável. Os exemplos incluem servidores para hospedar aplicativos Web e móveis e outros aplicativos empresariais.|
| Memória Otimizada | Cargas de trabalho de banco de dados de alto desempenho que exigem desempenho na memória para processamento mais rápido de transações e simultaneidade mais alta. Os exemplos incluem servidores para o processamento de dados em tempo real e aplicativos analíticos ou transacionais de alto desempenho.|

Depois de criar um servidor, o número de vCores, a geração de hardware e o tipo de preço (exceto para e do básico) podem ser alterados ou reduzidos em segundos. Você também pode ajustar de forma independente a quantidade de armazenamento e o período de retenção de backup para cima ou para baixo, sem tempo de inatividade do aplicativo. Você não pode alterar o tipo de armazenamento de backup depois que um servidor é criado. Para obter mais informações, consulte a seção [Scale](#scale-resources) Resources.

## <a name="compute-generations-and-vcores"></a>Gerações de computação e vCores

Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. Leste da China 1, Norte da China 1, US DoD Central e US DoD Leste utilizam CPUs lógicas Gen 4 baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz. Todas as outras regiões utilizam CPUs lógicas de Gen 5 baseadas em processadores Intel E5-2673 V4 (Broadwell) de 2,3 GHz.

## <a name="storage"></a>Armazenamento

O armazenamento que você provisiona é a quantidade de capacidade de armazenamento disponível para o servidor do banco de dados do Azure para PostgreSQL. O armazenamento é usado para os arquivos de banco de dados, arquivos temporários, logs de transações e logs do servidor PostgreSQL. A quantidade total de armazenamento que você provisiona também define a capacidade de e/s disponível para o servidor.

|    | **Básica** | **Uso Geral** | **Otimizado para memória** |
|:---|:----------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento standard do Azure | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho de armazenamento | 5 GB a 1 TB | 5 GB a 4 TB | 5 GB a 4 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB | 1 GB |
| IOPS | Variável |3 IOPS/GB<br/>IOPS mín. 100<br/>IOPS máx. 6000 | 3 IOPS/GB<br/>IOPS mín. 100<br/>IOPS máx. 6000 |

Você pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor e permitir que o sistema aumente o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho. 

>[!NOTE]
> O armazenamento só pode ser escalado verticalmente, não inativo.

A camada básica não fornece uma garantia de IOPS. Nos tipos de preço Uso Geral e com otimização de memória, a escala de IOPS com o tamanho de armazenamento provisionado em uma proporção de 3:1.

Você pode monitorar o consumo de e/s no portal do Azure ou usando comandos CLI do Azure. As métricas relevantes para monitorar são o [limite de armazenamento, a porcentagem de armazenamento, o armazenamento usado e a porcentagem de e/s](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Armazenamento grande (visualização)

Estamos aumentando os limites de armazenamento em nossas camadas de Uso Geral e com otimização de memória. Servidores recém-criados que aceitam a visualização podem provisionar até 16 TB de armazenamento. A escala de IOPS em uma proporção de 3:1 até 20.000 IOPS. Assim como acontece com o armazenamento atual disponível, você pode adicionar capacidade de armazenamento adicional após a criação do servidor e permitir que o sistema aumente o armazenamento automaticamente com base no consumo de armazenamento de sua carga de trabalho.

|              | **Uso Geral** | **Otimizado para memória** |
|:-------------|:--------------------|:---------------------|
| Tipo de armazenamento | Armazenamento Premium do Azure | Armazenamento Premium do Azure |
| Tamanho de armazenamento | 32 GB a 16 TB| 32 a 16 TB |
| Tamanho do incremento de armazenamento | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>IOPS mín. 100<br/>IOPS máx. 20.000 | 3 IOPS/GB<br/>IOPS mín. 100<br/>IOPS máx. 20.000 |

> [!IMPORTANT]
> O armazenamento grande está atualmente em visualização pública nas seguintes regiões: Leste dos EUA, leste dos EUA 2, EUA Central, oeste dos EUA, Europa Setentrional, Europa Ocidental, Sul do Reino Unido, Oeste do Reino Unido, Sudeste Asiático, Ásia Oriental, leste do Japão, oeste do Japão, Coreia central, Coreia do Sul, leste da Austrália, sudeste da Austrália.
>
> Atualmente, a visualização de armazenamento grande não oferece suporte a:
>
> * Conexões de entrada por meio de pontos de extremidade de serviço de rede virtual
> * Backups com redundância geográfica
> * Réplicas de leitura

### <a name="reaching-the-storage-limit"></a>Alcançando o limite de armazenamento

Os servidores com menos de 100 GB de armazenamento provisionado serão marcados como somente leitura se o armazenamento livre for inferior a 512MB ou 5% do tamanho de armazenamento provisionado. Servidores com mais de 100 GB de armazenamento provisionado são marcados como somente leitura quando o armazenamento livre é menor que 5 GB.

Por exemplo, se você tiver provisionado 110 GB de armazenamento e a utilização real passar de 105 GB, o servidor será marcado como somente leitura. Como alternativa, se você tiver provisionado 5 GB de armazenamento, o servidor será marcado como somente leitura quando o armazenamento livre atingir menos de 512 MB.

Quando o servidor é definido como somente leitura, todas as sessões existentes são desconectadas e as transações não confirmadas são revertidas. Todas as operações de gravação e confirmações de transação subsequentes falham. Todas as consultas de leitura subsequentes funcionarão sem interrupção.  

Você pode aumentar a quantidade de armazenamento provisionado para o servidor ou iniciar uma nova sessão no modo de leitura/gravação e remover os dados para recuperar o armazenamento gratuito. A `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` execução define a sessão atual para o modo de leitura. Para evitar dados corrompidos, não execute nenhuma operação de gravação quando o servidor ainda estiver no status somente leitura.

Recomendamos que você ative o aumento automático do armazenamento ou configure um alerta para notificá-lo quando o armazenamento do servidor estiver se aproximando do limite para que você possa evitar entrar no estado somente leitura. Para obter mais informações, consulte a documentação sobre [como configurar um alerta](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Crescimento automático do armazenamento

O crescimento automático de armazenamento impede que o servidor fique sem armazenamento e se torne somente leitura. Se o crescimento automático do armazenamento estiver habilitado, o armazenamento aumentará automaticamente sem afetar a carga de trabalho. Para servidores com menos de 100 GB de armazenamento provisionado, o tamanho do armazenamento provisionado aumenta em 5 GB assim que o armazenamento livre está abaixo do maior que 1 GB ou 10% do armazenamento provisionado. Para servidores com mais de 100 GB de armazenamento provisionado, o tamanho de armazenamento provisionado aumenta em 5% quando o espaço livre de armazenamento está abaixo de 5% do tamanho de armazenamento provisionado. Os limites de armazenamento máximos especificados acima se aplicam.

Por exemplo, se você tiver provisionado 1000 GB de armazenamento e a utilização real passar de 950 GB, o tamanho do armazenamento do servidor será aumentado para 1050 GB. Como alternativa, se você tiver provisionado 10 GB de armazenamento, o tamanho do armazenamento será aumentado para 15 GB quando menos de 1 GB de armazenamento for gratuito.

Lembre-se de que o armazenamento pode ser escalado verticalmente, não inativo.

## <a name="backup"></a>Criar cópia de segurança

O serviço automaticamente faz backups de seu servidor. O período de retenção mínimo para backups é de sete dias. Você pode definir um período de retenção de até 35 dias. A retenção pode ser ajustada a qualquer momento durante o tempo de vida do servidor. Você pode escolher entre backups localmente redundantes e com redundância geográfica. Os backups com redundância geográfica também são armazenados na [região emparelhada geograficamente](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) da região em que o servidor é criado. Essa redundância fornece um nível de proteção em caso de desastre. Você também pode obter a capacidade de restaurar o servidor para qualquer outra região do Azure na qual o serviço esteja disponível com backups com redundância geográfica. Não é possível alterar entre as duas opções de armazenamento de backup depois que o servidor é criado.

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o servidor, você pode alterar independentemente o vCores, a geração de hardware, o tipo de preço (exceto para e do básico), a quantidade de armazenamento e o período de retenção de backup. Você não pode alterar o tipo de armazenamento de backup depois que um servidor é criado. O número de vCores pode ser aumentado ou reduzido verticalmente. O período de retenção de backup pode ser reduzido ou reduzido de 7 a 35 dias. O tamanho do armazenamento só pode ser aumentado. O dimensionamento dos recursos pode ser feito por meio do portal ou CLI do Azure. Para obter um exemplo de dimensionamento usando CLI do Azure, consulte [monitorar e dimensionar um banco de dados do Azure para o servidor PostgreSQL usando CLI do Azure](scripts/sample-scale-server-up-or-down.md).

> [!NOTE] 
> O tamanho do armazenamento só pode ser aumentado. Não é possível voltar para um tamanho de armazenamento menor após o aumento.

Quando você altera o número de vCores, a geração de hardware ou o tipo de preço, uma cópia do servidor original é criada com a nova alocação de computação. Depois de o novo servidor estar a funcionar em pleno, as ligações são passadas para o novo servidor. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia mas, na maioria dos casos, é inferior a um minuto.

O dimensionamento do armazenamento e a alteração do período de retenção do backup são operações reais online. Não há nenhum tempo de inatividade e seu aplicativo não é afetado. Conforme o IOPS é dimensionado com o tamanho do armazenamento provisionado, você pode aumentar o IOPS disponível para o servidor ao escalar verticalmente o armazenamento.

## <a name="pricing"></a>Preços

Para obter as informações de preços mais atualizadas, consulte a página de [preços](https://azure.microsoft.com/pricing/details/PostgreSQL/)do serviço. Para ver o custo da configuração desejada, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia tipo de **preço** com base nas opções selecionadas. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , selecione **Adicionar itens**, expanda a categoria **bancos** de dados e escolha **banco de dados do Azure para PostgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um servidor PostgreSQL no portal](tutorial-design-database-using-azure-portal.md).
- Saiba mais sobre [os limites de serviço](concepts-limits.md). 
- Saiba como [escalar horizontalmente com réplicas de leitura](howto-read-replicas-portal.md).
