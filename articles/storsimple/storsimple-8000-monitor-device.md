---
title: Monitorizar o seu dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Descreve como utilizar o serviço StorSimple Device Manager para monitorizar a utilização, desempenho de e/s e a utilização de capacidade.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: 602514df69977891167f341db0ab20913bcacc9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634632"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Utilizar o serviço StorSimple Device Manager para monitorizar o dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Pode utilizar o serviço StorSimple Device Manager para monitorizar dispositivos específicos dentro da sua solução StorSimple. Pode criar gráficos personalizados com base no desempenho de e/s, utilização da capacidade, débito de rede e métricas de desempenho do dispositivo e afixá-los ao dashboard. Para obter mais informações, aceda a [personalizar o dashboard do portal](../azure-portal/azure-portal-dashboards.md).

Para ver as informações de monitorização para um dispositivo específico, no portal do Azure, selecione o serviço StorSimple Device Manager. Na lista de dispositivos, selecione o seu dispositivo e, em seguida, aceda a **Monitor**. Pode ver, em seguida, o **capacidade**, **utilização**, e **desempenho** gráficos para o dispositivo selecionado.

## <a name="capacity"></a>Capacidade
**Capacidade** controla o espaço aprovisionado e o espaço restante no dispositivo. A capacidade restante, em seguida, é apresentada afixado localmente ou em camadas.

A capacidade aprovisionada e restante ainda mais é dividida pelo volumes afixados localmente e em camadas. Para cada volume, é apresentada a capacidade aprovisionada e a capacidade restante no dispositivo.

![Capacidade de e/s](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Utilização
**Utilização** controla as métricas relacionadas com a quantidade de espaço de armazenamento de dados que é utilizada pelos contentores de volumes, dispositivo ou volumes. Pode criar relatórios com base na utilização de capacidade do seu armazenamento primário, o seu armazenamento na nuvem ou o seu dispositivo de armazenamento. Utilização de capacidade pode ser medida num volume específico, um contentor de volume específico ou todos os contentores de volumes.
Por predefinição, é comunicada a utilização para as últimas 24 horas. Pode editar o gráfico para alterar o período durante o qual a utilização é comunicada ao selecionar a partir de:
* Últimas 24 horas
* Últimos 7 dias
* Últimos 30 dias
* Últimos 90 dias
* Último ano

Dois metrices chave, o crescimento e o intervalo são comunicados para os gráficos de utilização. Intervalo refere-se para o valor máximo e os valores mínimo de utilização comunicados ao longo da duração selecionada (instância de fo, nos últimos 7 dias).

Crescimento refere-se para o aumento na utilização ao primeiro dia ao último dia durante o período selecionado. 

Crescimento e o intervalo também podem ser representados pelas equações seguintes:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

A cloud principal, e o armazenamento local utilizado pode ser descrito da seguinte forma:

### <a name="primary-storage-usage"></a>Utilização de armazenamento primário
Estes gráficos mostram a quantidade de dados escritos para volumes do StorSimple antes dos dados são com eliminação de duplicados e comprimidos. Pode ver o armazenamento primário utilizado por todos os volumes no contentor de volumes ou de um único volume. O armazenamento primário utilizado ainda mais é dividido pelo armazenamento em camadas primário utilizado e o armazenamento utilizado afixado localmente primário.

Os gráficos seguintes mostram o armazenamento primário utilizado para um dispositivo StorSimple antes e depois foi obtido um instantâneo da cloud. Como se trata apenas de volume de dados, um instantâneo de cloud não deve alterar o armazenamento primário. Como pode ver, o gráfico não mostra nenhuma diferença no armazenamento em camadas ou afixado localmente primário utilizado como o resultado de um instantâneo de cloud. O instantâneo de cloud iniciou a aproximadamente 23:50 horas em que o dispositivo.

![Utilização da capacidade primário depois de instantâneo de cloud](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Se agora, executar e/s no anfitrião ligado ao seu dispositivo StorSimple, verá um aumento de armazenamento em camadas primário ou afixado localmente primário, dependendo de armazenamento utilizado após os volumes (em camadas ou afixado localmente) escrever os dados. Aqui estão os gráficos de utilização de armazenamento primário para um dispositivo StorSimple. Neste dispositivo, o anfitrião do StorSimple ao servir escreve em aproximadamente 2 17:30, num volume em camadas no dispositivo. Pode ver o pico em bytes/s de escrita correspondente para a e/s em execução no anfitrião.

![Desempenho quando volumes em camadas de e/s em execução no](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Se olhar o armazenamento em camadas primário utilizado, que tornou-, ao passo que o primário localmente afixado utilização permanece inalterada porque não há nenhum escritas servidas para os volumes afixados localmente no dispositivo.

![Utilização de capacidade primária quando estiver a executar e/s em volumes em camadas](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se estiver a executar a atualização 3 ou superior, pode mapear a utilização da capacidade de armazenamento primário, um volume individual, todos os volumes, todos os volumes em camadas e todos os volumes afixados localmente conforme mostrado abaixo. Dividir por todos os volumes afixados localmente permitirá que determine rapidamente quanto do escalão local estiver cheio.

![Utilização da capacidade de principal para todos os volumes em camadas](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilização da capacidade de principal para todos os volumes afixados localmente](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Ainda pode clicar em cada um dos volumes na lista e ver a utilização correspondente.

![Utilização da capacidade de principal para todos os volumes afixados localmente](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Utilização do armazenamento na cloud
Estes gráficos mostram a quantidade de armazenamento na cloud utilizados. Estes dados é a eliminação de duplicados e comprimidos. Este valor inclui os instantâneos de cloud que podem conter dados que não são refletidos no qualquer volume primário e são mantidos para fins de retenção de legado ou necessárias. Pode comparar o principal e na cloud números de consumo de armazenamento para ter uma idéia da taxa de redução de dados, embora o número não será exato.

Os gráficos seguintes mostram a utilização de armazenamento na cloud de um dispositivo StorSimple, quando foi criado um instantâneo de cloud.

* O instantâneo de cloud iniciou a cerca: 50 às 11H nesse dispositivo e pode ver que antes da instantâneo de cloud, não havia nenhum armazenamento na cloud utilizados. 
* Uma vez o instantâneo de cloud concluído, a utilização de armazenamento na cloud captura a 0.89 GB. 
* Enquanto o instantâneo de cloud estava em curso, há também um pico correspondente na e/s de dispositivo para a cloud.

    ![Utilização do armazenamento na cloud antes de instantâneo de cloud](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Utilização do armazenamento na cloud após o instantâneo de cloud](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![E/s de dispositivo para cloud durante um instantâneo de cloud](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Utilização do armazenamento local
Estes gráficos mostram a utilização total para o dispositivo, que será a utilização de armazenamento primário mais do que uma vez que ele inclui a camada SSD linear. Essa camada contém uma quantidade de dados que também existe no dispositivo do outros escalões. A capacidade em camada SSD linear é cycled, de modo que quando novos dados chega, os dados antigos são movidos para a camada HDD (nesse momento é com eliminação de duplicados e comprimido) e, em seguida, para a cloud.

Ao longo do tempo, principal de armazenamento local e utilizado armazenamento utilizado provavelmente aumentará em conjunto até que os dados começa a ser colocado em camadas para a cloud. Nesse ponto, o armazenamento local utilizado provavelmente começará a nivelar, mas o armazenamento primário utilizado aumentará à medida que são escritos mais dados.

Os gráficos seguintes mostram o armazenamento primário utilizado para um dispositivo StorSimple, quando foi criado um instantâneo de cloud. O instantâneo de cloud iniciou às 11H: 50 e o armazenamento local começou a diminuição nesse momento. O armazenamento local utilizado foi desativado de 1.445 GB a 1.09 GB. Isto indica que é muito provável que os dados não comprimidos na camada SSD linear era com eliminação de duplicados, comprimidos e movidos para a camada HDD. Tenha em atenção que, se o dispositivo já tiver uma grande quantidade de dados nos escalões de SSD e HDD, não poderão ver essa redução. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização do armazenamento local depois de instantâneo de cloud](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Desempenho
**Desempenho** controla as métricas relacionadas com o número de leitura e operações de escrita entre qualquer uma das interfaces de iniciador do iSCSI no servidor de anfitrião e o dispositivo ou o dispositivo e a cloud. Este desempenho pode ser medido para um volume específico, um contentor de volume específico ou todos os contentores de volumes. Desempenho também inclui a utilização da CPU e de débito de rede para as várias interfaces de rede no seu dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Desempenho de e/s de iniciador para o dispositivo
O gráfico abaixo mostra a e/s para o iniciador para o seu dispositivo para todos os volumes para um dispositivo de produção. As métricas desenhadas são ler e escrever bytes por segundo. Também pode do gráfico de leitura, escrita e e/s pendentes, ou de leitura e latências de escrita.

![Desempenho de e/s de iniciador para o dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Desempenho de e/s de dispositivo para a cloud
Para o mesmo dispositivo, as operações de e/s são desenhadas para os dados do dispositivo para a cloud para todos os contentores de volume. Neste dispositivo, os dados estão apenas no escalão linear e nada tem vazam para a cloud. Não existem não existem operações de leitura / escrita ocorrer a partir do dispositivo para a cloud. Por conseguinte, os picos no gráfico estão num intervalo de 5 minutos, que corresponde à freqüência em que a pulsação é verificada entre o dispositivo e o serviço.

Para o mesmo dispositivo, foi obtido um instantâneo da cloud para dados de volume, começando às 11H: 50. Isso resultou em fluxo de dados do dispositivo para a cloud. Escritas foram atendidas para a cloud nesta duração. O gráfico de e/s mostra um pico de Bytes/s de escrita correspondente para a hora quando o instantâneo foi tirado.

![E/s de dispositivo para cloud durante um instantâneo de cloud](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Débito de rede para interfaces de rede do dispositivo
**Débito de rede** controla as métricas relacionadas com a quantidade de dados transferidos a partir das interfaces de rede de iniciador do iSCSI no servidor de anfitrião e o dispositivo e entre o dispositivo e a cloud. Pode monitorizar esta métrica para cada uma das interfaces de rede iSCSI no seu dispositivo.

Os gráficos seguintes mostram o débito de rede para os dados 0, 1 de 1 rede GbE no seu dispositivo, o que era ambas as capacidade de cloud (predefinição) e o iSCSI ativado. Neste dispositivo em 14 de Junho às 21:00 cerca, dados foi camadas para a nuvem (não existem instantâneos de cloud foram executados nesse momento que aponta para a criação de camadas de que está a ser o mecanismo para mover os dados para a cloud) que resultaram em e/s que está a ser atendidos para a cloud. Existe um pico correspondente no gráfico de débito de rede para o mesmo tempo e a maioria do tráfego de rede é de saída para a cloud.

![Débito de rede para os dados 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Se observarmos o gráfico de débito da interface de 1 de dados, outra de 1 gbe interface que foi apenas o iSCSI ativado, em seguida, não foi praticamente nenhum tráfego de rede nesta duração.

![Débito de rede de 1 de dados](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilização da CPU para o dispositivo
**Utilização da CPU** controla as métricas relacionadas com a CPU utilizada no seu dispositivo. A tabela a seguir mostra as estatísticas de utilização da CPU para um dispositivo na produção.

![Utilização da CPU para o dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Passos Seguintes
* Saiba como [utilize o dashboard de dispositivo do serviço StorSimple Device Manager](storsimple-device-dashboard.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

