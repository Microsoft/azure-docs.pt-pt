---
title: StorSimple a ativação pós-falha, recuperação após desastre para dispositivos de 8000 série | Documentos da Microsoft
description: Saiba como fazer a ativação pós-falha do dispositivo StorSimple em si, outro dispositivo físico ou uma aplicação da cloud.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60576377"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Ativação pós-falha e recuperação após desastre para o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Este artigo descreve a funcionalidade de ativação pós-falha do dispositivo para os dispositivos da série StorSimple 8000 e como esta funcionalidade pode ser usada para recuperar os dispositivos do StorSimple se ocorrer um desastre. O StorSimple utiliza ativação pós-falha do dispositivo para migrar os dados a partir de um dispositivo de origem no Centro de dados para outro dispositivo de destino. As orientações neste artigo aplica-se a dispositivos físicos da série StorSimple 8000 e cloud appliances que executam versões de software Update 3 e posterior.

O StorSimple utiliza o **dispositivos** painel para começar a funcionalidade de ativação pós-falha do dispositivo em caso de desastre. Este painel apresenta uma lista de todos os dispositivos do StorSimple ligados ao seu serviço StorSimple Device Manager.

![Painel de dispositivos](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação após desastre (DR) e ativação pós-falha do dispositivo

Num cenário de recuperação (DR) após desastre, o dispositivo primário deixa de funcionar. O StorSimple utiliza o dispositivo primário como _origem_ e move os dados na nuvem associadas para outro _destino_ dispositivo. Esse processo é chamado para o *ativação pós-falha*. O gráfico seguinte ilustra o processo de ativação pós-falha.

![O que acontece na ativação pós-falha do dispositivo?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

O dispositivo de destino para uma ativação pós-falha pode ser um dispositivo físico ou até mesmo uma aplicação da cloud. O dispositivo de destino pode estar localizado no mesmo ou para uma localização geográfica diferente do que o dispositivo de origem.

Durante a ativação pós-falha, pode selecionar contentores de volumes para a migração. StorSimple, em seguida, altera a propriedade destes contentores de volume do dispositivo de origem para o dispositivo de destino. Assim que os contentores de volumes alteram a propriedade, o StorSimple elimina desses contêineres do dispositivo de origem. Após a eliminação estiver concluída, pode efetuar a reativação pós-falha no dispositivo de destino. _Reativação pós-falha_ transfere a propriedade volta para o dispositivo de origem original.

### <a name="cloud-snapshot-used-during-device-failover"></a>Instantâneo de cloud utilizado durante a ativação pós-falha do dispositivo

Seguindo um DR, a cópia de segurança de cloud mais recente é utilizada para restaurar os dados para o dispositivo de destino. Para obter mais informações sobre instantâneos de cloud, consulte [utilizar o serviço StorSimple Device Manager para efetuar uma cópia de segurança manual](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Numa série StorSimple 8000, políticas de cópia de segurança estão associadas a cópias de segurança. Se existirem várias políticas de cópia de segurança para o mesmo volume, em seguida, o StorSimple seleciona a política de cópia de segurança com maior número de volumes. Em seguida, o StorSimple utiliza a cópia de segurança mais recente da política de cópia de segurança selecionada para restaurar os dados no dispositivo de destino.

Suponha que existam duas políticas de cópia de segurança, *defaultPol* e *customPol*:

* *defaultPol*: Um volume, *vol1*, é executada a partir de diária às 22:00: 30.
* *customPol*: Quatro volumes, *vol1*, *vol2*, *vol3*, *vol4*, é executada diariamente a partir de 10 21 horas.

Neste caso, o StorSimple dá prioridade para consistência com falhas e usa *customPol* porque tem mais volumes. A cópia de segurança mais recente desta política é utilizada para restaurar os dados. Para obter mais informações sobre como criar e gerir políticas de cópia de segurança, aceda a [utilizar o serviço StorSimple Device Manager para gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Considerações comuns para a ativação pós-falha do dispositivo

Antes de efetuar a ativação pós-falha de um dispositivo, reveja as seguintes informações:

* Antes de inicia uma ativação pós-falha do dispositivo, todos os volumes dentro de contentores de volumes têm de estar offline. Numa ativação pós-falha não planeada, StotSimple volumes serão automaticamente ficam offline. Mas se estiver a efetuar uma ativação pós-falha planeada (para testar DR), tem de colocar todos os volumes offline.
* Apenas os contentores de volumes que tenha associado ao instantâneo de cloud são listadas para o DR. Tem de existir pelo menos um contentor de volumes com um instantâneo na nuvem associadas para recuperar dados.
* Se existirem instantâneos de cloud que abrangem vários contentores de volume, o StorSimple efetua a ativação pós-falha estes contentores de volume como um conjunto. Numa instância raro, se existem instantâneos locais que abrangem vários contentores de volumes, mas os instantâneos de nuvem associada não o fizer, StorSimple efetuar a ativação pós-falha os instantâneos locais e os dados locais são perdidos após a DR.
* Os dispositivos de destino disponíveis para a DR são dispositivos que tem espaço suficiente para acomodar os contentores de volumes selecionados. Todos os dispositivos que não tem espaço suficiente, não são apresentados como dispositivos de destino.
* Após um DR (para um período de tempo limitado), o desempenho de acesso de dados pode ser afetado significativamente, conforme o dispositivo tem de aceder aos dados a partir da cloud e armazená-la localmente.

#### <a name="device-failover-across-software-versions"></a>Ativação pós-falha do dispositivo em todas as versões de software

Um serviço StorSimple Device Manager numa implementação pode ter vários dispositivos, tanto físicos e na cloud, todas as versões de software em execução diferentes.

Utilize a tabela seguinte para determinar se pode efetuar a ativação pós-falha ou reativação pós-falha para outro dispositivo que executam uma versão de software diferentes e como os tipos de volume se comportam durante a DR.

#### <a name="failover-and-failback-across-software-versions"></a>A ativação pós-falha e reativação pós-falha em todas as versões de software

| Efetuar a ativação pós-falha / reativação pós-falha do | Dispositivo físico | Aplicação da cloud |
| --- | --- | --- |
| Atualização 3 para a atualização 4 |Volumes em camadas falharem mais camadas. <br></br>Os volumes afixados localmente a ativação pós-falha como localmente afixado. <br></br> Após uma ativação pós-falha quando tirar um instantâneo do dispositivo de atualização 4 [controle baseado no mapa térmico](storsimple-update4-release-notes.md#whats-new-in-update-4) entra em ação. |Afixado localmente os volumes de ativação pós-falha como em camadas. |
| Atualização 4 para a atualização 3 |Volumes em camadas falharem mais camadas. <br></br>Os volumes afixados localmente a ativação pós-falha como localmente afixado. <br></br> As cópias de segurança utilizadas para restaurar retém os metadados de mapa térmico. <br></br>Controle baseado no mapa térmico não está disponível no Update 3, seguindo uma reativação pós-falha. |Afixado localmente os volumes de ativação pós-falha como em camadas. |


## <a name="device-failover-scenarios"></a>Cenários de ativação pós-falha do dispositivo

Se houver um desastre, pode optar por efetuar a ativação pós-falha do dispositivo StorSimple:

* [Para um dispositivo físico](storsimple-8000-device-failover-physical-device.md).
* [A próprio](storsimple-8000-device-failover-same-device.md).
* [Para uma aplicação da cloud](storsimple-8000-device-failover-cloud-appliance.md).

Os artigos anteriores fornecem os passos detalhados para cada um dos casos de ativação pós-falha acima.


## <a name="failback"></a>Reativação pós-falha

Para Update 3 e versões posteriores, o StorSimple suporta também a reativação pós-falha. Reativação pós-falha é simplesmente o inverso da ativação pós-falha, o destino se tornará a origem e o dispositivo de origem original durante a ativação pós-falha agora torna-se o dispositivo de destino. 

Durante a reativação pós-falha, StorSimple novamente sincroniza os dados de volta para a localização principal, a parar a e/s e a atividade das aplicações e as transições de volta para a localização original.

Após uma ativação pós-falha estiver concluída, o StorSimple executa as seguintes ações:

* StorSimple limpa os contentores de volumes com ativação pós-falha do dispositivo de origem.
* StorSimple inicia uma tarefa em segundo plano por contentor de volumes (a ativação pós-falha) no dispositivo de origem. Se está tentando falhar enquanto a tarefa está em curso, receberá uma notificação para o efeito. Aguarde até que a tarefa estiver concluída, para iniciar a reativação pós-falha.
* O tempo necessário para concluir a eliminação de contentores de volumes depende de vários fatores como a quantidade de dados, a idade dos dados, o número de cópias de segurança e a largura de banda disponível para a operação.

Se estiver a planear ativações pós-falha de teste ou reativações de teste, recomendamos que teste os contentores de volumes com menos dados (Gbs). Normalmente, pode começar a reativação pós-falha 24 horas depois de concluída a ativação pós-falha.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. **O que acontece se o DR falha ou se tiver êxito parcial?**

R. Se falhar a DR, é recomendável que tente novamente. A segunda tarefa de ativação pós-falha do dispositivo tem conhecimento sobre o progresso da primeira tarefa e começa a partir desse ponto em diante.

P. **Pode eliminar um dispositivo enquanto a ativação pós-falha está em curso?**

R. Não é possível eliminar um dispositivo, enquanto um DR está em curso. Só é possível eliminar o dispositivo após a conclusão do DR. Pode monitorizar o progresso de tarefa de ativação pós-falha do dispositivo na **tarefas** painel.

P. **Quando a coleta de lixo for iniciado no dispositivo de origem para que os dados locais no dispositivo de origem são eliminados?**

R. Coleta de lixo está ativada no dispositivo de origem apenas depois do dispositivo está totalmente limpos. A limpeza inclui a limpeza de objetos que falharam ao longo do dispositivo de origem, tais como volumes, objetos de cópia de segurança (não dados), contentores de volumes e políticas.

P. **O que acontece se falhar a tarefa de eliminação associada com os contentores de volumes no dispositivo de origem?**

R.  Se falhar a tarefa de eliminação, em seguida, pode eliminar manualmente os contentores de volumes. Na **dispositivos** painel, selecione o seu dispositivo de origem e clique em **contentores de volumes**. Selecione os contentores de volumes que falharam ao longo e na parte inferior do painel, clique em **eliminar**. Depois de ter eliminado todos a pós-falha dos contentores de volume no dispositivo de origem, pode começar a reativação pós-falha. Para obter mais informações, aceda a [eliminar um contentor de volumes](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastres de continuidade de negócio (BCDR)

Um cenário de recuperação (BCDR) de desastres de continuidade de negócio ocorre quando todo o datacenter do Azure deixa de funcionar. Este cenário pode afetar o seu serviço StorSimple Device Manager e os dispositivos associados do StorSimple.

Se um dispositivo StorSimple foi registrado antes de ocorrência de um desastre, em seguida, este dispositivo poderá ter de passar por uma reposição de fábrica. Após o desastre, o dispositivo StorSimple aparece no portal do Azure como offline. Este dispositivo tem de ser eliminado do portal. Repor o dispositivo para as predefinições de fábrica e registá-lo novamente com o serviço.

## <a name="next-steps"></a>Passos Seguintes

Se estiver pronto para executar uma ativação pós-falha do dispositivo, escolha um dos seguintes cenários para obter instruções detalhadas:

- [Efetuar a ativação pós-falha para outro dispositivo físico](storsimple-8000-device-failover-physical-device.md)
- [Ativação pós-falha para o mesmo dispositivo](storsimple-8000-device-failover-same-device.md)
- [Ativação pós-falha para o StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Se o ter falhado ao longo do seu dispositivo, escolha uma das seguintes opções:

* [Desativar ou eliminar o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

