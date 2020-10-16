---
title: Opções de Computação e Armazenamento - Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Este artigo descreve as opções de cálculo e armazenamento na Base de Dados Azure para PostgreSQL - Servidor Flexível.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ca60c44d1e167367e2c138af1e7bfd4ba1a69417
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710078"
---
# <a name="compute-and-storage-options-in-azure-database-for-postgresql---flexible-server"></a>Opções de computação e armazenamento em Base de Dados Azure para PostgreSQL - Servidor Flexível

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

Pode criar uma Base de Dados Azure para servidor PostgreSQL num dos três níveis de preços diferentes: Burstable, General Purpose e Memory Optimized. Os níveis de preços são diferenciados pela quantidade de cálculo em vCores que podem ser a provisionados, memória por vCore e a tecnologia de armazenamento usada para armazenar os dados. Todos os recursos são a provisionados ao nível do servidor PostgreSQL. Um servidor pode ter uma ou muitas bases de dados.

| Recurso / Nível | **Rebentado** | **Fins Gerais** | **Memória Otimizada** |
|:---|:----------|:--------------------|:---------------------|
| vCores | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| Memória por vCore | Variável | 4GB | 6,75 a 8 GB |
| Tamanho do armazenamento | 32 GB a 16 TB | 32 GB a 16 TB | 32 GB a 16 TB |
| Período de retenção de backup da base de dados | 7 a 35 dias | 7 a 35 dias | 7 a 35 dias |

Para escolher um nível de preços, utilize a tabela seguinte como ponto de partida.

| Escalão de preço | Cargas de trabalho de destino |
|:-------------|:-----------------|
| Rebentado | O melhor para cargas de trabalho que não precisam de CPU completo continuamente. |
| Fins Gerais | A maioria das cargas de trabalho do negócio que requerem cálculo equilibrado e memória com rendimento escalável de I/O. Os exemplos incluem servidores de alojamento de aplicações para dispositivos móveis e Web, entre outras aplicações empresariais.|
| Otimizada para Memória | Cargas de trabalho de base de dados de alto desempenho que requerem desempenho na memória para um processamento de transações mais rápido e maior conúcência. Os exemplos incluem servidores para processamento de dados em tempo real e aplicações com elevado desempenho transacional ou analítico.|

Depois de criar um servidor, o nível de cálculo, o número de vCores e o tamanho do armazenamento podem ser alterados para cima ou para baixo em segundos. Também pode ajustar o período de retenção de backup de forma independente para cima ou para baixo. Para mais informações, consulte a secção [de recursos da Escala.](#scale-resources)

## <a name="compute-tiers-vcores-and-server-types"></a>Níveis de cálculo, vCores e tipos de servidor

Os recursos computacional podem ser selecionados com base no nível, vCores e tamanho da memória. vCores representam o CPU lógico do hardware subjacente.

As especificações detalhadas dos tipos de servidores disponíveis são as seguintes:

| Nome SKU             | vCores | Tamanho da memória | IOPS apoiados por Max | Largura de banda suportada por I/O Max |
|----------------------|--------|-------------|--------------------|-----------------------------|
| **Rebentado**        |        |             |                    |                             |
| B1ms                 | 1      | 2 GiB       | 640                | 15 MiB/seg                  |
| B2s                  | 2      | 4 GiB       | 1280               | 15 MiB/seg                  |
| **Fins Gerais**  |        |             |                    |                             |
| D2s_v3               | 2      | 8 GiB       | 3200               | 48 MiB/seg                  |
| D4s_v3               | 4      | 16 GiB      | 6400               | 96 MiB/seg                  |
| D8s_v3               | 8      | 32 GiB      | 12800              | 192 MiB/seg                 |
| D16s_v3              | 16     | 64 GiB      | 18000              | 384 MiB/seg                 |
| D32s_v3              | 32     | 128 GiB     | 18000              | 750 MiB/seg                 |
| D48s_v3              | 48     | GiB de 192     | 18000              | 750 MiB/seg                 |
| D64s_v3              | 64     | 256 GiB     | 18000              | 750 MiB/seg                 |
| **Memória Otimizada** |        |             |                    |                             |
| E2s_v3               | 2      | 16 GiB      | 3200               | 48 MiB/seg                  |
| E4s_v3               | 4      | 32 GiB      | 6400               | 96 MiB/seg                  |
| E8s_v3               | 8      | 64 GiB      | 12800              | 192 MiB/seg                 |
| E16s_v3              | 16     | 128 GiB     | 18000              | 384 MiB/seg                 |
| E32s_v3              | 32     | 256 GiB     | 18000              | 750 MiB/seg                 |
| E48s_v3              | 48     | 384 GiB     | 18000              | 750 MiB/seg                 |
| E64s_v3              | 64     | 432 GiB     | 18000              | 750 MiB/seg                 |

## <a name="storage"></a>Armazenamento

O armazenamento que fornece é a quantidade de capacidade de armazenamento disponível para a sua Base de Dados Azure para servidor PostgreSQL. O armazenamento é utilizado para os ficheiros de base de dados, ficheiros temporários, registos de transações e registos de servidores PostgreSQL. A quantidade total de armazenamento que fornece também define a capacidade de E/S disponível para o seu servidor.

O armazenamento está disponível nos seguintes tamanhos fixos:

| Tamanho do disco | IOPS |
|:---|:---|
| 32 GiB | Provisionado 120, até 3.500 |
| 64 GiB | A provisionado 240, até 3.500 |
| 128 GiB | A provisionado 500, até 3.500 |
| 256 GiB | A provisionado 1100, até 3.500 |
| 512 GiB | A provisionado 2300, até 3.500 |
| 1 TiB | 5000 |
| 2 TiB | 7.500 |
| 4 TiB | 7.500 |
| 8 TiB | 16 000 |
| 16 TiB | 18 000 |

Note que o IOPS também está limitado pelo seu tipo VM. Mesmo que possa selecionar qualquer tamanho de armazenamento independente do tipo de servidor, pode não ser capaz de utilizar todos os IOPS que o armazenamento fornece, especialmente quando escolhe um servidor com um pequeno número de vCores.

Pode adicionar capacidade de armazenamento adicional durante e após a criação do servidor.

>[!NOTE]
> O armazenamento só pode ser aumentado, não para baixo.

Pode monitorizar o seu consumo de E/S no portal Azure ou utilizando comandos Azure CLI. As métricas relevantes a monitorizar são [o limite de armazenamento, a percentagem de armazenamento, o armazenamento utilizado e a IO por cento](concepts-monitoring.md).

### <a name="maximum-iops-for-your-configuration"></a>IOPS máximo para a sua configuração

|Nome SKU            |Tamanho do armazenamento em GiB                       |32 |64 |128 |256 |512  |1,024|2048|4,096|8,192 |16 384|
|--------------------|------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |IOPS máximos                              |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Rebentado**       |                                          |   |   |    |    |     |     |     |     |      |      |
|B1ms                |640 IOPS                                  |120|240|500 |640*|640* |640* |640* |640* |640*  |640*  |
|B2s                 |1280 iOPS                                 |120|240|500 |1100|1280*|1280*|1280*|1280*|1280* |1280* |
|**Fins Gerais** |                                          |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |3200 iOPS                                 |120|240|500 |1100|2300 |3200*|3200*|3200*|3200* |3200* |
|D4s_v3              |6.400 OPS                                |120|240|500 |1100|2300 |5000 |6400*|6400*|6400* |6400* |
|D8s_v3              |12.800 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800*|12800*|
|D16s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D32s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D48s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|D64s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|**Memória Otimizada**|                                          |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |3200 iOPS                                 |120|240|500 |1100|2300 |3200*|3200*|3200*|3200* |3200* |
|E4s_v3              |6.400 OPS                                |120|240|500 |1100|2300 |5000 |6400*|6400*|6400* |6400* |
|E8s_v3              |12.800 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |12800*|12800*|
|E16s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E32s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E48s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |
|E64s_v3             |18.000 OPS                               |120|240|500 |1100|2300 |5000 |7500 |7500 |16000 |18000 |

Quando marcado com um \* , o IOPS é limitado pelo tipo VM selecionado. Caso contrário, o IOPS é limitado pelo tamanho de armazenamento selecionado.

### <a name="maximum-io-bandwidth-mibsec-for-your-configuration"></a>Largura de banda máxima de I/O (MiB/seg) para a sua configuração

|Nome SKU            |Tamanho do armazenamento, GiB                             |32 |64 |128 |256 |512  |1,024|2048|4,096|8,192 |16 384|
|--------------------|----------------------------------------------|---|---|----|----|-----|-----|-----|-----|------|------|
|                    |**Largura de banda de armazenamento, MiB/seg**                |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Rebentado**       |                                              |   |   |    |    |     |     |     |     |      |      |
|B1ms                |10 MiB/seg                                    |10*|10*|10* |10* |10*  |10*  |10*  |10*  |10*   |10*   |
|B2s                 |15 MiB/seg                                    |15*|15*|15* |15* |15*  |15*  |15*  |15*  |15*   |15*   |
|**Fins Gerais** |                                              |   |   |    |    |     |     |     |     |      |      |
|D2s_v3              |48 MiB/seg                                    |25 |48*|48* |48* |48*  |48*  |48*  |48*  |48*   |48*   |
|D4s_v3              |96 MiB/seg                                    |25 |50 |96* |96* |96*  |96*  |96*  |96*  |96*   |96*   |
|D8s_v3              |192 MiB/seg                                   |25 |50 |100 |125 |150  |192* |192* |192* |192*  |192*  |
|D16s_v3             |384 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |384*  |384*  |
|D32s_v3             |750 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D48s_v3             |750 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|D64s_v3             |750 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|**Memória Otimizada**|                                              |   |   |    |    |     |     |     |     |      |      |
|E2s_v3              |48 MiB/seg                                    |25 |48*|48* |48* |48*  |48*  |48*  |48*  |48*   |48*   |
|E4s_v3              |96 MiB/seg                                    |25 |50 |96* |96* |96*  |96*  |96*  |96*  |96*   |96*   |
|E8s_v3              |192 MiB/seg                                   |25 |50 |100 |125 |150  |192* |192* |192* |192*  |192*  |
|E16s_v3             |384 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |384*  |384*  |
|E32s_v3             |750 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E48s_v3             |750 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |
|E64s_v3             |750 MiB/seg                                   |25 |50 |100 |125 |150  |200  |250  |250  |500   |750   |

Quando marcado com uma \* largura de banda , a largura de banda de I/S é limitada pelo tipo VM selecionado. Caso contrário, a largura de banda de I/S é limitada pelo tamanho de armazenamento selecionado.

### <a name="reaching-the-storage-limit"></a>Atingindo o limite de armazenamento

Quando atingir o limite de armazenamento, o servidor começará a retornar erros e evitará quaisquer modificações adicionais. Isto também pode causar problemas com outras atividades operacionais, tais como backups e arquivo WAL.

Recomendamos monitorizar ativamente o espaço do disco que está em uso e aumentar o tamanho do disco antes de qualquer situação fora do armazenamento. Pode configurar um alerta para o notificar quando o armazenamento do servidor se aproxima do disco para evitar qualquer problema com o fim do disco. Para mais informações, consulte a documentação sobre [como configurar um alerta.](howto-alert-on-metrics.md)

### <a name="storage-auto-grow"></a>Armazenamento auto-cultivo

O crescimento automático do armazenamento ainda não está disponível para o Servidor Flexível.

## <a name="backup"></a>Backup

O serviço retira automaticamente cópias de segurança do seu servidor. Pode selecionar um período de retenção de um intervalo de 7 a 35 dias. Saiba mais sobre backups no [artigo de conceitos.](concepts-backup-restore.md)

## <a name="scale-resources"></a>Dimensionar recursos

Depois de criar o seu servidor, pode alterar independentemente os vCores, o nível de cálculo, a quantidade de armazenamento e o período de retenção de backup. O número de vCores pode ser aumentado para cima ou para baixo. O período de retenção de backup pode ser aumentado para cima ou para baixo de 7 a 35 dias. O tamanho do armazenamento só pode ser aumentado. A escala dos recursos pode ser feita através do portal ou do Azure CLI.

> [!NOTE]
> O tamanho do armazenamento só pode ser aumentado. Não pode voltar a ter um tamanho de armazenamento menor após o aumento.

Quando altera o número de vCores ou o nível de cálculo, o servidor é reiniciado para que o novo tipo de servidor produza efeitos. Durante o período em que o sistema muda para o novo servidor, não se pode estabelecer nenhuma nova ligação e todas as transações não confirmadas são revertidas. Esta janela varia mas, na maioria dos casos, é inferior a um minuto. A escala do armazenamento funciona da mesma forma, e também requer um pequeno recomeço.

Alterar o período de retenção de backup é uma operação online.

## <a name="pricing"></a>Preços

Para obter as informações de preços mais atualizadas, consulte a [página de preços do](https://azure.microsoft.com/pricing/details/PostgreSQL/)serviço . Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal no **separador de nível de Preços** com base nas opções selecionadas. Se não tiver uma subscrição do Azure, pode utilizar a calculadora de preços Azure para obter um preço estimado. No site da [calculadora de preços Azure,](https://azure.microsoft.com/pricing/calculator/) selecione **Adicionar itens,** expanda a categoria **Base de Dados** e escolha a Base de **Dados Azure para PostgreSQL** para personalizar as opções.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um servidor PostgreSQL no portal](how-to-manage-server-portal.md).
- Saiba mais sobre [os limites de serviço.](concepts-limits.md)
