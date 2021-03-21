---
title: Monitorize o seu dispositivo da série StorSimple 8000
description: Descreve como utilizar o serviço StorSimple Device Manager para monitorizar o uso, desempenho de E/S e utilização da capacidade.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: de4cae90f2cc1223271672d9fc5ba5b42e4409e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94962438"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Utilize o serviço StorSimple Device Manager para monitorizar o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral
Pode utilizar o serviço StorSimple Device Manager para monitorizar dispositivos específicos dentro da sua solução StorSimple. Pode criar gráficos personalizados com base no desempenho de E/S, utilização da capacidade, produção de rede e métricas de desempenho do dispositivo e fixá-las no painel de instrumentos. Para mais informações, aceda para [personalizar o seu painel de instrumentos do portal.](../azure-portal/azure-portal-dashboards.md)

Para visualizar as informações de monitorização de um dispositivo específico, no portal Azure, selecione o serviço StorSimple Device Manager. A partir da lista de dispositivos, selecione o seu dispositivo e, em seguida, vá ao **Monitor**. Em seguida, pode ver as tabelas **capacidade,** **utilização** e **desempenho** para o dispositivo selecionado.

## <a name="capacity"></a>Capacidade
**A capacidade** rastreia o espaço a provisionado e o espaço restante no dispositivo. A capacidade restante é então exibida como fixada localmente ou nivelada.

A capacidade a provisionada e a capacidade remanescente são ainda desacompusadas por volumes hierárquicos e fixados localmente. Para cada volume, é apresentada a capacidade a provisionada e a capacidade remanescente do aparelho.

![Capacidade io](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Utilização
**A utilização** rastreia métricas relacionadas com a quantidade de espaço de armazenamento de dados que é utilizado pelos volumes, recipientes de volume ou dispositivo. Pode criar relatórios baseados na utilização da capacidade do seu armazenamento primário, do seu armazenamento em nuvem ou do armazenamento do seu dispositivo. A utilização da capacidade pode ser medida num volume específico, num recipiente de volume específico ou em todos os recipientes de volume.
Por padrão, o uso para as últimas 24 horas é reportado. Pode editar o gráfico para alterar a duração em que a utilização é reportada selecionando a partir de:
* Últimas 24 horas
* Últimos 7 dias
* Últimas 30 dias
* Últimas 90 dias
* Ano passado

Duas métricas-chave, crescimento e alcance são reportados para os gráficos de utilização. O intervalo refere-se ao valor máximo e aos valores mínimos da utilização reportados durante a duração selecionada (instância fo, passados 7 dias).

O crescimento refere-se ao aumento da utilização do primeiro ao último dia durante a duração selecionada. 

O crescimento e a gama também podem ser representados pelas seguintes equações:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

O armazenamento primário, nuvem e local utilizado pode ser descrito da seguinte forma:

### <a name="primary-storage-usage"></a>Utilização primária do armazenamento
Estes gráficos mostram a quantidade de dados escritos aos volumes StorSimple antes de os dados serem desativados e comprimidos. Pode ver o armazenamento primário utilizado por todos os volumes num recipiente de volume ou por um único volume. O armazenamento primário utilizado é ainda discriminado pelo armazenamento hierárquico primário utilizado e pelo armazenamento primário fixado localmente utilizado.

Os gráficos seguintes mostram o armazenamento primário utilizado para um dispositivo StorSimple antes e depois de uma foto em nuvem ter sido tirada. Como se trata apenas de dados de volume, uma imagem em nuvem não deve alterar o armazenamento primário. Como pode ver, o gráfico não mostra nenhuma diferença no armazenamento primário tiered ou localmente fixado usado como resultado de tirar uma foto em nuvem. A imagem da nuvem começou por volta das 23h50 naquele dispositivo.

![Utilização da capacidade primária após instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Se agora executar IO no hospedeiro ligado ao seu dispositivo StorSimple, verá um aumento no armazenamento primário hierárquico ou armazenamento primário fixado localmente utilizado dependendo dos volumes (tiered ou localmente fixados) para o qual escreve os dados. Aqui estão os gráficos de utilização de armazenamento primário para um dispositivo StorSimple. Neste dispositivo, o anfitrião StorSimple começou a servir escritos por volta das 14h30 num volume hierárquico no dispositivo. Pode ver o pico nos bytes/s de escrita correspondentes ao IO em execução no hospedeiro.

![Desempenho quando IO funciona em volumes hierárquicos](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Se olharmos para o armazenamento hierárquico primário utilizado, que subiu, enquanto que o uso primário fixado localmente permanece inalterado, uma vez que não existem escritos servidos aos volumes fixados localmente no dispositivo.

![Utilização da capacidade primária quando a IO está a funcionar em volumes hierárquicos](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se estiver a executar a Atualização 3 ou superior, pode decompor a utilização da capacidade de armazenamento primária por um volume individual, todos os volumes, todos os volumes hierárquicos e todos os volumes fixados localmente, como mostrado abaixo. A divisão por todos os volumes fixados localmente permitir-lhe-á verificar rapidamente quanto do nível local está usado.

![Utilização da capacidade primária para todos os volumes hierárquicos](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilização da capacidade primária para todos os volumes fixados localmente](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Pode clicar ainda em cada um dos volumes da lista e ver a utilização correspondente.

![Utilização da capacidade primária para todos os volumes fixados localmente 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Uso de armazenamento em nuvem
Estes gráficos mostram a quantidade de armazenamento em nuvem usado. Estes dados são desduplicados e comprimidos. Esta quantidade inclui instantâneos em nuvem que podem conter dados que não se refletem em nenhum volume primário e são mantidos para fins legados ou de retenção necessários. Pode comparar os números de consumo de armazenamento primário e em nuvem para obter uma ideia da taxa de redução de dados, embora o número não seja exato.

Os gráficos seguintes mostram a utilização do armazenamento em nuvem de um dispositivo StorSimple quando uma fotografia em nuvem foi tirada.

* O instantâneo da nuvem começou por volta das 11:50 naquele dispositivo e pode ver que antes do instantâneo da nuvem, não havia armazenamento na nuvem usado. 
* Uma vez concluída a imagem em nuvem, a utilização do armazenamento na nuvem disparou 0,89 GB. 
* Enquanto o instantâneo de nuvem estava em andamento, há também um pico correspondente no IO de dispositivo para nuvem.

    ![Utilização do armazenamento em nuvem antes do instantâneo da nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Utilização do armazenamento em nuvem após instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![IO de dispositivo para nuvem durante uma imagem em nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Uso de armazenamento local
Estes gráficos mostram a utilização total do dispositivo, que será mais do que o uso primário de armazenamento porque inclui o nível linear SSD. Este nível contém uma quantidade de dados que também existem nos outros níveis do dispositivo. A capacidade no nível linear SSD é ciclamada de modo a que, quando novos dados entram, os dados antigos sejam transferidos para o nível HDD (altura em que é desativado e comprimido) e posteriormente para a nuvem.

Com o tempo, o armazenamento primário utilizado e o armazenamento local utilizado provavelmente aumentarão em conjunto até que os dados comecem a ser hierárquicos para a nuvem. Nessa altura, o armazenamento local utilizado provavelmente começará a planar, mas o armazenamento primário utilizado aumentará à medida que mais dados são escritos.

Os gráficos seguintes mostram o armazenamento primário utilizado para um dispositivo StorSimple quando uma fotografia em nuvem foi tirada. O instantâneo da nuvem começou às 11h50 e o armazenamento local começou a diminuir nessa altura. O armazenamento local utilizado baixou de 1,445 GB para 1,09 GB. Isto indica que, muito provavelmente, os dados não comprimidos no nível linear de SSD foram desduplicados, comprimidos e transferidos para o nível HDD. Note que se o dispositivo já tiver uma grande quantidade de dados nos níveis SSD e HDD, poderá não ver esta diminuição. Neste exemplo, o dispositivo tem uma pequena quantidade de dados.

![Utilização local do armazenamento após instantâneo de nuvem](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Desempenho
**O desempenho** acompanha as métricas relacionadas com o número de operações de leitura e escrita entre as interfaces de iniciador iSCSI no servidor anfitrião e o dispositivo ou o dispositivo e a nuvem. Este desempenho pode ser medido para um volume específico, um recipiente de volume específico ou todos os recipientes de volume. O desempenho também inclui a utilização do CPU e o rendimento da Rede para as várias interfaces de rede no seu dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Desempenho de I/O para iniciador ao dispositivo
O gráfico abaixo mostra o E/O do iniciador do seu dispositivo para todos os volumes de um dispositivo de produção. As métricas traçadas são lidas e escrevem bytes por segundo. Também pode traçar a leitura, a escrita e a io pendente, ou ler e escrever latências.

![Desempenho do IO para iniciador ao dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Desempenho de I/O para dispositivo para cloud
Para o mesmo dispositivo, as operações de E/S são traçadas para os dados do dispositivo para a nuvem para todos os recipientes de volume. Neste dispositivo, os dados estão apenas no nível linear e nada foi derramado para a nuvem. Não existem operações de leitura-escrita que ocorram do dispositivo para a nuvem. Portanto, os picos na tabela estão num intervalo de 5 minutos que corresponde à frequência em que o batimento cardíaco é verificado entre o dispositivo e o serviço.

Para o mesmo dispositivo, foi tirada uma fotografia em nuvem para dados de volume a partir das 11h50. Isto resultou em dados que fluem do dispositivo para a nuvem. As gravações foram servidas à nuvem nesta duração. O gráfico IO mostra um pico nos Bytes/s de Escrita correspondente ao momento em que o instantâneo foi tirado.

![IO de dispositivo para nuvem durante uma imagem em nuvem](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Produção de rede para interfaces de rede de dispositivos
**O produtor da rede** rastreia métricas relacionadas com a quantidade de dados transferidos das interfaces de rede de iniciadores iSCSI no servidor anfitrião e no dispositivo e entre o dispositivo e a nuvem. Pode monitorizar esta métrica para cada uma das interfaces de rede iSCSI no seu dispositivo.

Os gráficos seguintes mostram a produção da rede Data 0, 1 1 GbE no seu dispositivo, que estava ativada pela nuvem (padrão) e ativada pelo iSCSI. Neste dispositivo, no dia 14 de junho, por volta das 21h00, os dados foram colocados na nuvem (não foram tiradas imagens de nuvem na altura, o que aponta para que o tiering seja o mecanismo para mover os dados para a nuvem) o que resultou no IO ser servido à nuvem. Há um pico correspondente no gráfico de produção da rede para o mesmo tempo e a maior parte do tráfego de rede está fora da nuvem.

![Produção de rede para dados 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Se olharmos para o gráfico de produção de interface data 1, outra interface de rede de 1 GbE que estava apenas ativada pelo iSCSI, então não havia praticamente nenhum tráfego de rede nesta duração.

![Produção de rede para dados 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilização do CPU para dispositivo
**A utilização do CPU** rastreia métricas relacionadas com o CPU utilizado no seu dispositivo. O gráfico seguinte mostra as estatísticas de utilização do CPU para um dispositivo em produção.

![Utilização do CPU para dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Passos seguintes
* Saiba como [utilizar o painel de instrumentos do StorSimple Device Manager](./storsimple-8000-device-dashboard.md).
* Saiba como [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](./storsimple-8000-manager-service-administration.md).