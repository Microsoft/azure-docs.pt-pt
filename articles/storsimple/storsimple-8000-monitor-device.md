---
title: Monitorize o seu dispositivo da série StorSimple 8000
description: Descreve como utilizar o serviço StorSimple Device Manager para monitorizar a utilização, desempenho de I/S e utilização da capacidade.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: b3b77024606c5cdb02ff7bdd357c2d14a2415efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277047"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Utilize o serviço StorSimple Device Manager para monitorizar o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Pode utilizar o serviço StorSimple Device Manager para monitorizar dispositivos específicos dentro da sua solução StorSimple. Pode criar gráficos personalizados baseados no desempenho de I/S, utilização da capacidade, entrada de rede e métricas de desempenho do dispositivo e fixá-los no painel de instrumentos. Para mais informações, vá [personalizar o seu portal dashboard](../azure-portal/azure-portal-dashboards.md).

Para visualizar as informações de monitorização de um dispositivo específico, no portal Azure, selecione o serviço StorSimple Device Manager. A partir da lista de dispositivos, selecione o seu dispositivo e depois vá para **o Monitor**. Em seguida, pode ver as **tabelas de capacidade,** **utilização**e **desempenho** para o dispositivo selecionado.

## <a name="capacity"></a>Capacidade
**A capacidade** rastreia o espaço aprovisionado e o espaço restante no dispositivo. A capacidade restante é então apresentada como fixada localmente ou nificada.

A capacidade aprovisionada e remanescente é ainda mais discriminada por volumes nicosos e fixados localmente. Para cada volume, a capacidade aprovisionada e a capacidade restante no dispositivo são apresentadas.

![Capacidade de IO](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Utilização
**A utilização** rastreia métricas relacionadas com a quantidade de espaço de armazenamento de dados que é utilizado pelos volumes, contentores de volume ou dispositivo. Pode criar relatórios com base na utilização da capacidade do seu armazenamento primário, no seu armazenamento em nuvem ou no armazenamento do seu dispositivo. A utilização da capacidade pode ser medida num volume específico, num recipiente de volume específico ou em todos os contentores de volume.
Por padrão, o uso por mais de 24 horas é reportado. Pode editar o gráfico para alterar a duração sobre a qual o uso é relatado selecionando a partir de:
* Passados 24 horas
* Últimos 7 dias
* Últimos 30 dias
* Últimos 90 dias
* Ano passado

Duas métricas-chave, crescimento e alcance são reportados para os gráficos de utilização. A gama refere-se ao valor máximo e aos valores mínimos da utilização reportados ao longo da duração selecionada (fo instance, passados 7 dias).

O crescimento refere-se ao aumento do uso desde o primeiro dia até ao último dia ao longo da duração selecionada. 

O crescimento e a gama também podem ser representados pelas seguintes equações:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

O armazenamento primário, nuvem e local utilizado pode ser descrito da seguinte forma:

### <a name="primary-storage-usage"></a>Utilização primária do armazenamento
Estes gráficos mostram a quantidade de dados escritos aos volumes StorSimple antes de os dados forem desduplicados e comprimidos. Pode visualizar o armazenamento primário utilizado por todos os volumes num recipiente de volume ou num único volume. O armazenamento primário utilizado é ainda decomposto pelo armazenamento primário de nível utilizado e pelo armazenamento primário local utilizado.

Os gráficos seguintes mostram o armazenamento primário usado para um dispositivo StorSimple antes e depois de ter sido tirado um instantâneo em nuvem. Como se trata apenas de dados de volume, um instantâneo em nuvem não deve alterar o armazenamento primário. Como pode ver, o gráfico não mostra nenhuma diferença no armazenamento primário ou fixado localmente usado como resultado de tirar uma imagem em nuvem. O instantâneo da nuvem começou por volta das 23h50 naquele dispositivo.

![Utilização da capacidade primária após instantâneo em nuvem](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Se agora executar IO no hospedeiro ligado ao seu dispositivo StorSimple, verá um aumento no armazenamento primário ou armazenamento primário local mente utilizado, dependendo dos volumes (hierárquicos ou fixados localmente) para os quais escreve os dados. Aqui estão os principais gráficos de armazenamento para um dispositivo StorSimple. Neste dispositivo, o hospedeiro StorSimple começou a servir escritos por volta das 14h30 num volume hierárquico no dispositivo. Pode ver o pico dos bytes/s de escrita correspondente s ao IO que corre no hospedeiro.

![Desempenho quando IO funciona em volumes hierárquicos](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Se olharmos para o armazenamento primário de nível utilizado, isso aumentou, enquanto o uso primário fixado localmente permanece inalterado, uma vez que não existem escritos servidos aos volumes fixados localmente no dispositivo.

![Utilização da capacidade primária quando a IO está a funcionar em volumes hierárquicos](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se estiver a executar a Atualização 3 ou superior, pode decompor a utilização da capacidade de armazenamento primário por um volume individual, todos os volumes, todos os volumes hierárquicos e todos os volumes fixados localmente, como mostrado abaixo. A quebra por todos os volumes fixados localmente permitirá verificar rapidamente quanto do nível local está esgotado.

![Utilização da capacidade primária para todos os volumes hierárquicos](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilização da capacidade primária para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Pode clicar mais em cada um dos volumes da lista e ver o uso correspondente.

![Utilização da capacidade primária para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Uso de armazenamento em nuvem
Estes gráficos mostram a quantidade de armazenamento em nuvem usado. Estes dados são desduplicados e comprimidos. Esta quantidade inclui instantâneos em nuvem que podem conter dados que não se refletem em qualquer volume primário e são mantidos para fins de retenção legado ou necessário. Pode comparar os números de consumo de armazenamento primário e na nuvem para ter uma ideia da taxa de redução de dados, embora o número não seja exato.

Os gráficos seguintes mostram a utilização do armazenamento em nuvem de um dispositivo StorSimple quando um instantâneo em nuvem foi tirado.

* O instantâneo da nuvem começou por volta das 11h50 naquele dispositivo e pode ver que antes do instantâneo da nuvem, não foi utilizado nenhum armazenamento em nuvem. 
* Uma vez concluída a imagem em nuvem, a utilização do armazenamento em nuvem disparou 0,89 GB. 
* Enquanto o instantâneo da nuvem estava em andamento, há também um pico correspondente na IO de dispositivo para nuvem.

    ![Utilização de armazenamento em nuvem antes do instantâneo da nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Utilização de armazenamento em nuvem após instantâneo em nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![IO de dispositivo para nuvem durante um instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Uso de armazenamento local
Estes gráficos mostram a utilização total do dispositivo, que será mais do que o uso primário do armazenamento porque inclui o nível linear SSD. Este nível contém uma quantidade de dados que também existem nos outros níveis do dispositivo. A capacidade no nível linear SSD é ciclizada de modo a que, quando chegam novos dados, os dados antigos sejam transferidos para o nível HDD (altura em que é desduplicado e comprimido) e posteriormente para a nuvem.

Com o tempo, o armazenamento primário utilizado e o armazenamento local utilizado provavelmente aumentarão juntos até que os dados comecem a ser nividados para a nuvem. Nessa altura, o armazenamento local utilizado provavelmente começará a planalto, mas o armazenamento primário utilizado aumentará à medida que mais dados forem escritos.

Os gráficos seguintes mostram o armazenamento primário usado para um dispositivo StorSimple quando uma imagem de nuvem foi tirada. O instantâneo da nuvem começou às 11h50 e o armazenamento local começou a diminuir na altura. O armazenamento local usado baixou de 1.445 GB para 1,09 GB. Isto indica que, muito provavelmente, os dados não comprimidos no nível linear de SSD foram desduplicados, comprimidos e transferidos para o nível HDD. Note que se o dispositivo já tiver uma grande quantidade de dados nos níveis SSD e HDD, poderá não ver esta diminuição. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização de armazenamento local após instantâneo em nuvem](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Desempenho
**O desempenho** rastreia métricas relacionadas com o número de operações de leitura e escrita entre as interfaces de iniciador iSCSI no servidor de anfitriões e o dispositivo ou o dispositivo e a nuvem. Este desempenho pode ser medido para um volume específico, um recipiente de volume específico ou todos os recipientes de volume. O desempenho também inclui a utilização do CPU e a entrada da Rede para as várias interfaces de rede no seu dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Desempenho de I/O para iniciador ao dispositivo
O gráfico abaixo mostra o I/S para o iniciador do seu dispositivo para todos os volumes de um dispositivo de produção. As métricas traçadas são lidas e escrevem bytes por segundo. Você também pode gráfico ler, escrever, e excelente IO, ou ler e escrever latenciências.

![Desempenho iO para iniciador ao dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Desempenho de I/S para o dispositivo para a nuvem
Para o mesmo dispositivo, as operações de I/S são traçadas para os dados do dispositivo para a nuvem para todos os recipientes de volume. Neste dispositivo, os dados estão apenas no nível linear e nada derramou para a nuvem. Não há operações de leitura que ocorram do dispositivo à nuvem. Portanto, os picos da tabela são a um intervalo de 5 minutos que corresponde à frequência a que o batimento cardíaco é verificado entre o dispositivo e o serviço.

Para o mesmo dispositivo, foi tirada uma imagem em nuvem para dados de volume a partir das 11h50. Isto resultou em dados que fluem do dispositivo para a nuvem. Os escritos foram servidos à nuvem nesta duração. O gráfico iO mostra um pico nos Write Bytes/s correspondentes ao momento em que o instantâneo foi tirado.

![IO de dispositivo para nuvem durante um instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Entrada de rede para interfaces de rede de dispositivos
**A utilização** da rede rastreia métricas relacionadas com a quantidade de dados transferidos das interfaces de rede de iniciadores iSCSI no servidor de anfitriões e no dispositivo e entre o dispositivo e a nuvem. Pode monitorizar esta métrica para cada uma das interfaces de rede iSCSI no seu dispositivo.

Os gráficos seguintes mostram a entrada da rede para a rede Data 0, 1 1 GbE no seu dispositivo, que foi ativada pela nuvem (padrão) e iSCSI ativada pelo iSCSI. Neste dispositivo, no dia 14 de junho, por volta das 21:00, os dados foram colocados na nuvem (não foram tiradas imagens de nuvens na altura, o que aponta para o tiering ser o mecanismo de deslocação dos dados para a nuvem) o que resultou na ser servida a IO para a nuvem. Há um pico correspondente no gráfico de saída da rede para o mesmo tempo e a maior parte do tráfego de rede está de saída para a nuvem.

![Entrada de rede para Dados 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Se olharmos para o gráfico de entrada de interface Data 1, outra interface de rede de 1 GbE que só estava ativada pelo iSCSI, então não havia praticamente nenhum tráfego de rede nesta duração.

![Entrada de rede para Dados 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilização do CPU para dispositivo
A utilização do **CPU** rastreia as métricas relacionadas com o CPU utilizados no seu dispositivo. O gráfico seguinte mostra as estatísticas de utilização do CPU para um dispositivo em produção.

![Utilização do CPU para dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba como utilizar o painel de instrumentos do [dispositivo StorSimple Device Manager](storsimple-device-dashboard.md).
* Aprenda a [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

