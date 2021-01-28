---
title: Opções de computação e armazenamento - Azure Database for MySQL - Servidor Flexível
description: Este artigo descreve as opções de cálculo e armazenamento na Base de Dados Azure para MySQL - Servidor Flexível.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 267b362c94b04b3be634f7e61c2b6d67604d7854
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954686"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Opções de computação e armazenamento em Azure Database para MySQL - Servidor Flexível (Pré-visualização)

> [!IMPORTANT] 
> A azure Database for MySQL - Flexible Server encontra-se atualmente em pré-visualização pública.

Pode criar uma Base de Dados Azure para o MySQL Flexible Server num dos três níveis de computação diferentes: Burstable, General Purpose e Memory Optimized. Os níveis de cálculo são diferenciados pelas séries B, séries D e E utilizadas pela VM SKU. A escolha do nível e tamanho do cálculo determina a memória e vCores disponíveis no servidor. A mesma tecnologia de armazenamento é usada em todos os níveis de computação. Todos os recursos são a provisionados ao nível do servidor MySQL. Um servidor pode ter uma ou muitas bases de dados.

| Recurso / Nível | **Rebentado** | **Fins Gerais** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| Série das VMs| Série B | Série Ddsv4 | Série Edsv4|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memória por vCore | Variável | 4 GiB | 8 GiB * |
| Tamanho do armazenamento | 5 GiB a 16 TiB | 5 GiB a 16 TiB | 5 GiB a 16 TiB |
| Período de retenção de backup da base de dados | 1 a 35 dias | 1 a 35 dias | 1 a 35 dias |

\* Com exceção do SKU E64ds_v4 (Memory Optimized), que tem 504 GB de memória

Para escolher um nível de cálculo, utilize a tabela seguinte como ponto de partida.

| Escalão de computação | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Rebentado | O melhor para cargas de trabalho que não precisam de CPU completo continuamente. |
| Fins Gerais | A maioria das cargas de trabalho do negócio que requerem cálculo equilibrado e memória com rendimento escalável de I/O. Os exemplos incluem servidores de alojamento de aplicações para dispositivos móveis e Web, entre outras aplicações empresariais.|
| Otimizada para Memória | Cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento de transações mais rápido e maior conúcência. Os exemplos incluem servidores para processamento de dados em tempo real e aplicações com elevado desempenho transacional ou analítico.|

Depois de criar um servidor, o nível de cálculo, o tamanho do cálculo e o tamanho do armazenamento mudaram. A escala de cálculo requer um reinício e demora entre 60 a 120 segundos, enquanto a escala de armazenamento não requer o reinício. Também pode ajustar o período de retenção de backup de forma independente para cima ou para baixo. Para mais informações, consulte a secção [de recursos da Escala.](#scale-resources)

## <a name="compute-tiers-size-and-server-types"></a>Camadas de cálculo, tamanho e tipos de servidor

Os recursos computacional podem ser selecionados com base no nível e tamanho. Isto determina o tamanho do vCores e da memória. vCores representam o CPU lógico do hardware subjacente.

As especificações detalhadas dos tipos de servidores disponíveis são as seguintes:

| Tamanho do cálculo         | vCores | Tamanho da memória (GiB) | 
|----------------------|--------|-------------------|
| **Rebentado**        |        |                   | 
| Standard_B1s         | 1      | 1                 |  
| Standard_B1ms        | 1      | 2                 | 
| Standard_B2s         | 2      | 4                 |  
| **Fins Gerais**  |        |                   | 
| Standard_D2ds_v4     | 2      | 8                 |  
| Standard_D4ds_v4     | 4      | 16                | 
| Standard_D8ds_v4     | 8      | 32                | 
| Standard_D16ds_v4    | 16     | 64                | 
| Standard_D32ds_v4    | 32     | 128               |  
| Standard_D48ds_v4    | 48     | 192               |  
| Standard_D64ds_v4    | 64     | 256               | 
| **Memória Otimizada** |        |                   |
| Standard_E2ds_v4     | 2      | 16                |
| Standard_E4ds_v4     | 4      | 32                |
| Standard_E8ds_v4     | 8      | 64                |
| Standard_E16ds_v4    | 16     | 128               |
| Standard_E32ds_v4    | 32     | 256               |
| Standard_E48ds_v4    | 48     | 384               |
| Standard_E64ds_v4    | 64     | 504               |

Para obter mais detalhes sobre a série computacional disponível, consulte a documentação Azure VM para [Burstable (série B)](../../virtual-machines/sizes-b-series-burstable.md), [Final Geral (série Ddsv4)](../../virtual-machines/ddv4-ddsv4-series.md)e [Memory Optimized (série Edsv4)](../../virtual-machines/edv4-edsv4-series.md).

>[!NOTE]
>Para o nível de cálculo [Burstable (série B)](../../virtual-machines/sizes-b-series-burstable.md) quando o servidor é reiniciado por qualquer motivo como a manutenção iniciada, planeada ou não planeada, o crédito acumulado pode perder-se. A razão é que sempre que a Base de Dados Azure para o MySQL reiniciá-la, permanecerá no mesmo nó, o crédito acumulado será mantido. Contrariamente, sempre que a Base de Dados Azure para o servidor MySQL começa fresca num novo nó, recebe um crédito inicial. Para mais informações, leia [as FAQ burstable (Série B).](https://docs.microsoft.com/azure/virtual-machines/sizes-b-series-burstable#q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart)

## <a name="storage"></a>Armazenamento

O armazenamento que você providenciar é a quantidade de capacidade de armazenamento disponível para o seu servidor flexível. O armazenamento é utilizado para os ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores MySQL. Em todos os níveis de cálculo, o armazenamento mínimo suportado é de 5 GiB e máximo é de 16 TiB. O armazenamento é dimensionado em 1 incrementos GiB e pode ser dimensionado após a criação do servidor.

>[!NOTE]
> O armazenamento só pode ser aumentado, não para baixo.

Pode monitorizar o seu consumo de armazenamento no portal Azure (com o Azure Monitor) utilizando o limite de armazenamento, percentagem de armazenamento e métricas usadas de armazenamento. Consulte o [artigo de monitorização](./concepts-monitoring.md) para saber mais sobre as métricas. 

### <a name="reaching-the-storage-limit"></a>Atingindo o limite de armazenamento

Quando o armazenamento consumido no servidor está perto de atingir o limite previsto, o servidor é colocado no modo apenas de leitura para proteger quaisquer escritas perdidas no servidor. Os servidores com menos de 100 giB de armazenamento provisitado só são marcados de leitura se o armazenamento gratuito for inferior a 5% do tamanho de armazenamento a provisionado. Os servidores com mais de 100 armazenamentos com giB são marcados apenas quando o armazenamento gratuito é inferior a 5 GiB.

Por exemplo, se tiver provisionado 110 GiB de armazenamento, e a utilização real ultrapassar 105 GiB, o servidor está marcado apenas para leitura. Em alternativa, se tiver disponibilizado 5 GiB de armazenamento, o servidor só está marcado quando o armazenamento gratuito atinge menos de 256 MB.

Apesar de o serviço tentar tornar o servidor só de leitura, todos os novos pedidos de transação de escrita são bloqueados e as transações ativas existentes continuam a executar. Quando o servidor estiver definido como só de leitura, todas as subsequentes operações de escrita e de transação falham. As consultas de leitura continuam a trabalhar sem interrupções. 

Para tirar o servidor do modo apenas de leitura, deverá aumentar o armazenamento foralizado no servidor. Isto pode ser feito usando o portal Azure ou Azure CLI. Uma vez aumentado, o servidor estará pronto para aceitar a escrita de transações novamente.

Recomendamos que crie um alerta para o notificar quando o armazenamento do servidor estiver a aproximar-se do limiar para evitar entrar no estado de leitura. Consulte o [artigo de monitorização](./concepts-monitoring.md) para saber mais sobre as métricas disponíveis. 

Recomendamos que <!--turn on storage auto-grow or to--> configurar um alerta para notificá-lo quando o armazenamento do servidor estiver a aproximar-se do limiar para evitar entrar no estado de leitura. Para mais informações, consulte a documentação em alerta [de como configurar um alerta.](how-to-alert-on-metric.md)

### <a name="storage-auto-grow"></a>Armazenamento auto-cultivo

O crescimento automático do armazenamento ainda não está disponível para a Base de Dados Azure para o MySQL Flexible Server.

## <a name="iops"></a>IOPS
O IOPS eficaz mínimo é de 100 em todos os tamanhos de cálculo e o IOPS eficaz máximo é determinado por ambos os seguintes atributos: 
- Compute: o IOPS eficaz máximo talvez limitado pelo máximo disponível IOPS do tamanho do cálculo selecionado.
- Armazenamento: em todos os níveis de cálculo, a escala IOPS com o tamanho de armazenamento a provisionado numa proporção de 3:1.

Pode escalar o IOPS eficaz disponível aumentando o armazenamento a provisionado ou movendo-se para um tamanho de computação maior (se o seu IOPS estiver limitado por computação). Na pré-visualização, o IOPS máximo eficaz suportado é de 20.000 IOPS.

Para saber mais sobre o máximo eficaz IOPS por tamanho de computação, utilizando a combinação de cálculo e armazenamento, é mostrado abaixo: 

| Tamanho do cálculo         | IOPS eficazes no máximo  | 
|----------------------|---------------------|
| **Rebentado**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **Fins Gerais**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20 000               |
| Standard_D32ds_v4    | 20 000               |
| Standard_D48ds_v4    | 20 000               | 
| Standard_D64ds_v4    | 20 000               | 
| **Memória Otimizada** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20 000               | 
| Standard_E32ds_v4    | 20 000               | 
| Standard_E48ds_v4    | 20 000               | 
| Standard_E64ds_v4    | 20 000               |  

O IOPS eficaz máximo depende do máximo disponível IOPS por tamanho de cálculo. Consulte a fórmula abaixo e consulte a coluna *Max desatado: IOPS/MBps* na [série B,](../../virtual-machines/sizes-b-series-burstable.md) [ddsv4 séries](../../virtual-machines/ddv4-ddsv4-series.md)e documentação [da série Edsv4.](../../virtual-machines/edv4-edsv4-series.md)

**IOPS eficaz máximo** = MINIMUM(*"Max uncached disk throughput: IOPS/MBps"* de tamanho computacional, armazenamento aprovisionado em GiB * 3)

Pode monitorizar o seu consumo de E/S no portal Azure (com o Azure Monitor) utilizando a métrica [IO%.](./concepts-monitoring.md) Se precisar de mais IOPS, terá de perceber se está limitado pelo tamanho do cálculo ou pelo armazenamento a provisionado. Dimensione o cálculo ou armazenamento do seu servidor em conformidade.

## <a name="backup"></a>Backup

O serviço retira automaticamente cópias de segurança do seu servidor. Pode selecionar um período de retenção de um intervalo de 1 a 35 dias. Saiba mais sobre backups na cópia de segurança e restaurar o [artigo conceitos.](concepts-backup-restore.md)

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar independentemente o nível de cálculo, o tamanho do cálculo (vCores e memória) e a quantidade de armazenamento e o período de retenção de backup. O tamanho da computação pode ser dimensionado para cima ou para baixo. O período de retenção de backup pode ser aumentado para cima ou para baixo de 1 a 35 dias. O tamanho do armazenamento só pode ser aumentado. A escala dos recursos pode ser feita através do portal ou do Azure CLI.

> [!NOTE]
> O tamanho do armazenamento só pode ser aumentado. Não pode voltar a ter um tamanho de armazenamento menor após o aumento.

Quando altera o nível de cálculo ou o tamanho do cálculo, o servidor é reiniciado para que o novo tipo de servidor faça efeito. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia, mas na maioria dos casos, está entre 60-120 segundos. 

O armazenamento de escala e a alteração do período de retenção de backup são operações online e não requerem o reinício do servidor.

## <a name="pricing"></a>Preços

Para obter as informações de preços mais atualizadas, consulte a [página de preços do](https://azure.microsoft.com/pricing/details/MySQL/)serviço . Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) mostra o custo mensal no separador **de armazenamento Compute +** com base nas opções selecionadas. Se não tiver uma subscrição do Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens**, expandir a categoria **Base de Dados,** escolher **Azure Database para MySQL,** e **Servidor Flexível** como tipo de implementação para personalizar as opções.

Se quiser otimizar o custo do servidor, pode considerar as seguintes dicas:

- Reduza o seu nível de cálculo ou tamanho de cálculo (vCores) se o cálculo for subutilizado.
- Considere mudar para o nível de cálculo burstable se a sua carga de trabalho não necessitar de toda a capacidade de computação continuamente a partir dos níveis otimizados para fins gerais e memória.
- Pare o servidor quando não estiver a ser utilizado.
- Reduza o período de retenção de backup se não for necessária uma maior retenção de cópias de segurança.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um servidor MySQL no portal](quickstart-create-server-portal.md).
- Conheça as [limitações de serviço.](concepts-limitations.md)