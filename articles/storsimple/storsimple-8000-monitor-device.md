---
title: Monitorar seu dispositivo StorSimple série 8000
description: Descreve como usar o serviço de Device Manager do StorSimple para monitorar o uso, O desempenho de e/s e a utilização da capacidade.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277047"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Usar o serviço de Device Manager do StorSimple para monitorar seu dispositivo StorSimple

## <a name="overview"></a>Visão geral
Você pode usar o serviço de Device Manager do StorSimple para monitorar dispositivos específicos em sua solução do StorSimple. Você pode criar gráficos personalizados com base em desempenho de e/s, utilização de capacidade, taxa de transferência de rede e métricas de desempenho do dispositivo e fixá-los no painel. Para obter mais informações, acesse [Personalizar o painel do portal](../azure-portal/azure-portal-dashboards.md).

Para exibir as informações de monitoramento de um dispositivo específico, na portal do Azure, selecione o serviço StorSimple Device Manager. Na lista de dispositivos, selecione o dispositivo e vá para **Monitor**. Em seguida, você pode ver os gráficos de **capacidade**, **uso**e **desempenho** para o dispositivo selecionado.

## <a name="capacity"></a>Capacidade
A **capacidade** controla o espaço provisionado e o espaço restante no dispositivo. A capacidade restante é exibida como fixada localmente ou em camadas.

A capacidade provisionada e restante é dividida por volumes em camadas e fixados localmente. Para cada volume, a capacidade provisionada e a capacidade restante no dispositivo são exibidas.

![Capacidade de e/s](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Utilização
O **uso** acompanha as métricas relacionadas à quantidade de espaço de armazenamento de dados usada pelos volumes, contêineres de volume ou dispositivo. Você pode criar relatórios com base na utilização da capacidade de seu armazenamento primário, no armazenamento em nuvem ou no armazenamento do dispositivo. A utilização da capacidade pode ser medida em um volume específico, em um contêiner de volume específico ou em todos os contêineres de volume.
Por padrão, o uso das últimas 24 horas é relatado. Você pode editar o gráfico para alterar a duração em que o uso é relatado, selecionando:
* Últimas 24 horas
* Últimos 7 dias
* Últimos 30 dias
* Últimos 90 dias
* Último ano

Duas métricas-chave, crescimento e intervalo são relatados para os gráficos de uso. Intervalo refere-se ao valor máximo e aos valores mínimos do uso relatados durante a duração selecionada (instância de Fo, últimos 7 dias).

O crescimento se refere ao aumento no uso desde o primeiro dia até o último dia durante a duração selecionada. 

O crescimento e o intervalo também podem ser representados pelas seguintes equações:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

O armazenamento primário, de nuvem e local usado pode ser descrito da seguinte maneira:

### <a name="primary-storage-usage"></a>Uso do armazenamento primário
Esses gráficos mostram a quantidade de dados gravados em volumes do StorSimple antes que os dados tenham eliminação de duplicação e sejam compactados. Você pode exibir o armazenamento primário usado por todos os volumes em um contêiner de volume ou para um único volume. O armazenamento primário usado é dividido por um armazenamento em camadas primário usado e pelo armazenamento primário fixado localmente usado.

Os gráficos a seguir mostram o armazenamento primário usado para um dispositivo StorSimple antes e depois que um instantâneo de nuvem foi tirado. Como esses são apenas dados de volume, um instantâneo de nuvem não deve alterar o armazenamento primário. Como você pode ver, o gráfico não mostra nenhuma diferença no armazenamento em camadas primária ou localmente fixado usado como resultado da criação de um instantâneo de nuvem. O instantâneo de nuvem começou em cerca de 11:50 PM nesse dispositivo.

![Utilização da capacidade primária após o instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Se agora você executar a e/s no host conectado ao seu dispositivo StorSimple, você verá um aumento no armazenamento em camadas primária ou no armazenamento primário fixado localmente usado, dependendo de quais volumes (em camadas ou localmente afixados) você gravará os dados. Aqui estão os gráficos de uso de armazenamento primário para um dispositivo StorSimple. Neste dispositivo, o host StorSimple começou a fornecer gravações em cerca de 2:30 PM em um volume em camadas no dispositivo. Você pode ver o pico nos bytes de gravação/s correspondentes à e/s em execução no host.

![Desempenho durante a execução de e/s em volumes em camadas](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Se você examinar o armazenamento em camadas primário usado, isso acontece enquanto o uso primário fixado localmente permanece inalterado, pois não há gravações servidas para os volumes fixados localmente no dispositivo.

![Utilização da capacidade primária quando a e/s está em execução em volumes em camadas](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se você estiver executando a atualização 3 ou superior, poderá dividir a utilização da capacidade de armazenamento principal por um volume individual, todos os volumes, todos os volumes em camadas e todos os volumes fixados localmente, conforme mostrado abaixo. Dividir por todos os volumes localmente afixados permitirá que você rapidamente determinar quanto da camada local está sendo usada.

![Utilização da capacidade primária para todos os volumes em camadas](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilização da capacidade primária para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Você pode clicar em cada um dos volumes na lista e ver o uso correspondente.

![Utilização da capacidade primária para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Uso do armazenamento em nuvem
Esses gráficos mostram a quantidade de armazenamento em nuvem usada. Esses dados têm eliminação de duplicação e são compactados. Esse valor inclui instantâneos de nuvem que podem conter dados que não são refletidos em nenhum volume primário e são mantidos para fins de retenção herdados ou necessários. Você pode comparar os números de consumo de armazenamento primário e de nuvem para ter uma ideia da taxa de redução de dados, embora o número não seja exato.

Os gráficos a seguir mostram a utilização do armazenamento em nuvem de um dispositivo StorSimple quando um instantâneo de nuvem foi tirado.

* O instantâneo de nuvem começou em cerca de 11:50 a.m. no dispositivo e você pode ver que, antes do instantâneo de nuvem, não havia nenhum armazenamento em nuvem usado. 
* Depois que o instantâneo de nuvem for concluído, a utilização do armazenamento em nuvem é capturada em 0,89 GB. 
* Enquanto o instantâneo de nuvem estava em andamento, também há um pico correspondente na e/s do dispositivo para a nuvem.

    ![Utilização do armazenamento em nuvem antes do instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Utilização do armazenamento em nuvem após o instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![E/s do dispositivo para a nuvem durante um instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Uso do armazenamento local
Esses gráficos mostram a utilização total para o dispositivo, que será mais do que o uso do armazenamento primário porque inclui a camada de SSD linear. Essa camada contém uma quantidade de dados que também existe nas outras camadas do dispositivo. A capacidade na camada de SSD linear é alternada para que, quando novos dados chegarem, os dados antigos sejam movidos para a camada de HDD (no momento em que ele é deduplicado e compactado) e subsequentemente para a nuvem.

Ao longo do tempo, o armazenamento primário usado e o armazenamento local usados provavelmente aumentarão em conjunto até que os dados comecem a ser enfileirados na nuvem. Nesse ponto, o armazenamento local usado provavelmente começará a limite, mas o armazenamento primário usado aumentará conforme mais dados forem gravados.

Os gráficos a seguir mostram o armazenamento primário usado para um dispositivo StorSimple quando um instantâneo de nuvem foi tirado. O instantâneo de nuvem começou às 11:50 e o armazenamento local começou a diminuir nesse momento. O armazenamento local usado caiu de 1,445 GB para 1, 9 GB. Isso indica que a maioria das vezes os dados descompactados na camada SSD linear foram deduplicados, compactados e movidos para a camada HDD. Observe que, se o dispositivo já tiver uma grande quantidade de dados nas camadas SSD e HDD, talvez você não veja essa redução. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização do armazenamento local após o instantâneo da nuvem](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Desempenho
O **desempenho** rastreia as métricas relacionadas ao número de operações de leitura e gravação entre as interfaces do iniciador iSCSI no servidor host e o dispositivo ou o dispositivo e a nuvem. Esse desempenho pode ser medido para um volume específico, um contêiner de volume específico ou todos os contêineres de volume. O desempenho também inclui a utilização da CPU e a taxa de transferência de rede para as várias interfaces de rede em seu dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Desempenho de e/s do iniciador para o dispositivo
O gráfico abaixo mostra a e/s do iniciador para seu dispositivo para todos os volumes de um dispositivo de produção. As métricas plotadas são bytes de leitura e gravação por segundo. Você também pode criar gráficos de leitura, gravação e e/s pendentes ou latências de leitura e gravação.

![Desempenho de e/s do iniciador para o dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Desempenho de e/s para o dispositivo para a nuvem
Para o mesmo dispositivo, as operações de e/s são plotadas para os dados do dispositivo para a nuvem para todos os contêineres de volume. Nesse dispositivo, os dados só estão na camada linear e nada foi despejado para a nuvem. Não há nenhuma operação de leitura/gravação ocorrendo do dispositivo para a nuvem. Portanto, os picos no gráfico estão em um intervalo de 5 minutos que corresponde à frequência em que a pulsação é verificada entre o dispositivo e o serviço.

Para o mesmo dispositivo, um instantâneo de nuvem foi tomado para dados de volume começando às 11:50. Isso resultou no fluxo de dados do dispositivo para a nuvem. As gravações foram servidas para a nuvem nessa duração. O gráfico de e/s mostra um pico nos bytes de gravação/s que corresponde à hora em que o instantâneo foi tirado.

![E/s do dispositivo para a nuvem durante um instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Taxa de transferência de rede para interfaces de rede do dispositivo
A **taxa de transferência de rede** controla as métricas relacionadas à quantidade de dados transferidos das interfaces de rede do iniciador iSCSI no servidor host e no dispositivo e entre o dispositivo e a nuvem. Você pode monitorar essa métrica para cada uma das interfaces de rede iSCSI em seu dispositivo.

Os gráficos a seguir mostram a taxa de transferência de rede para a rede data 0, 1 1 GbE em seu dispositivo, que estava habilitada para a nuvem (padrão) e habilitada para iSCSI. Neste dispositivo em 14 de junho às 9h, os dados eram colocados em camadas na nuvem (nenhum instantâneo de nuvem foi feito naquele momento que aponta para o mecanismo de movimentação dos dados para a nuvem), o que resultou na chegada da e/s para a nuvem. Há um pico correspondente no grafo de taxa de transferência de rede para o mesmo tempo e a maior parte do tráfego de rede é de saída para a nuvem.

![Taxa de transferência de rede para data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Se olharmos o gráfico de taxa de transferência da interface data 1, outra interface de rede de 1 GbE que era habilitada para iSCSI, praticamente não havia nenhum tráfego de rede nessa duração.

![Taxa de transferência de rede para dados 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilização da CPU para o dispositivo
A **utilização da CPU** acompanha as métricas relacionadas à CPU utilizada em seu dispositivo. O gráfico a seguir mostra as estatísticas de utilização da CPU para um dispositivo em produção.

![Utilização da CPU para o dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba como [usar o painel de dispositivo do serviço StorSimple Device Manager](storsimple-device-dashboard.md).
* Saiba como [usar o serviço de Device Manager do storsimple para administrar seu dispositivo storsimple](storsimple-manager-service-administration.md).

