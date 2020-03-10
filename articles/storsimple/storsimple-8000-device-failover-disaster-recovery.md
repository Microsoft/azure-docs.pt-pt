---
title: Falha e recuperação de desastres para dispositivos da série StorSimple 8000
description: Aprenda a falhar sobre o seu dispositivo StorSimple para si mesmo, outro dispositivo físico ou um aparelho em nuvem.
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
ms.openlocfilehash: c1acc084d5abe3385fe311873dfd64c9009e83f2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394557"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Failover e recuperação de desastres para o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral

Este artigo descreve a funcionalidade de failover do dispositivo para os dispositivos da série StorSimple 8000 e como esta funcionalidade pode ser usada para recuperar dispositivos StorSimple se ocorrer um desastre. O StorSimple utiliza o dispositivo falha ndo para migrar os dados de um dispositivo de origem no datacenter para outro dispositivo alvo. A orientação neste artigo aplica-se aos dispositivos físicos da série StorSimple 8000 e aos aparelhos em nuvem que executam as versões de software Update 3 e posteriormente.

O StorSimple utiliza a lâmina **do Dispositivo** para iniciar a funcionalidade de falha do dispositivo em caso de desastre. Esta lâmina lista todos os dispositivos StorSimple ligados ao seu serviço StorSimple Device Manager.

![Lâmina de dispositivos](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação de desastres (DR) e falha do dispositivo

Num cenário de recuperação de desastres (DR), o dispositivo primário para de funcionar. O StorSimple utiliza o dispositivo primário como _fonte_ e move os dados da nuvem associados para outro _dispositivo-alvo._ Este processo é referido como o *fracasso.* O gráfico seguinte ilustra o processo de failover.

![O que acontece no dispositivo falha?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

O dispositivo-alvo para uma falha pode ser um dispositivo físico ou mesmo um aparelho em nuvem. O dispositivo-alvo pode estar localizado no mesmo local geográfico ou numa localização geográfica diferente do dispositivo de origem.

Durante a falha, pode selecionar recipientes de volume para migração. O StorSimple altera então a propriedade destes contentores de volume do dispositivo de origem para o dispositivo-alvo. Assim que os contentores de volume mudam de propriedade, o StorSimple elimina estes recipientes do dispositivo de origem. Depois de concluída a eliminação, pode voltar a falhar o dispositivo-alvo. _O failback_ transfere a propriedade de volta para o dispositivo de origem original.

### <a name="cloud-snapshot-used-during-device-failover"></a>Imagem de nuvem utilizada durante o failover do dispositivo

Seguindo um DR, a mais recente cópia de segurança em nuvem é usada para restaurar os dados no dispositivo alvo. Para obter mais informações sobre imagens em nuvem, consulte [Utilize o serviço StorSimple Device Manager para obter uma cópia de segurança manual](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Numa série StorSimple 8000, as políticas de backup estão associadas a backups. Se houver múltiplas políticas de backup para o mesmo volume, então o StorSimple seleciona a política de backup com o maior número de volumes. O StorSimple utiliza então a cópia de segurança mais recente da política de backup selecionada para restaurar os dados no dispositivo alvo.

Suponha que existam duas políticas de backup, *defaultPol* e *customPol:*

* *padrãoPol*: Um volume, *vol1,* funciona diariamente a partir das 22:30.
* *customPol*: Quatro volumes, *vol1,* *vol2,* *vol3,* *vol4,* corre diariamente a partir das 22:00.

Neste caso, a StorSimple dá prioridade à consistência do crash e utiliza *a CustomPol* como tem mais volumes. O mais recente backup desta política é usado para restaurar dados. Para obter mais informações sobre como criar e gerir políticas de backup, vá utilizar [o serviço StorSimple Device Manager para gerir as políticas](storsimple-8000-manage-backup-policies-u2.md)de backup .

## <a name="common-considerations-for-device-failover"></a>Considerações comuns para falha no dispositivo

Antes de falhar sobre um dispositivo, reveja as seguintes informações:

* Antes de um dispositivo falhar, todos os volumes dentro dos contentores de volume devem estar offline. Numa falha não planeada, os volumes StotSimple ficarão automaticamente offline. Mas se estiver a realizar uma falha planeada (para testar dr), deve desligar todos os volumes.
* Apenas os recipientes de volume que têm um instantâneo de nuvem associado estão listados para DR. Deve haver pelo menos um recipiente de volume com um instantâneo de nuvem associado para recuperar dados.
* Se houver instantâneos em nuvem que se estendem por vários contentores de volume, o StorSimple falha sobre estes recipientes de volume como conjunto. Num caso raro, se houver instantâneos locais que se estendem por vários contentores de volume, mas instantâneos de nuvem associados não, o StorSimple falha sobre os instantâneos locais e os dados locais perdem-se após o DR.
* Os dispositivos-alvo disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os recipientes de volume selecionados. Quaisquer dispositivos que não tenham espaço suficiente, não estão listados como dispositivos-alvo.
* Após um DR (por uma duração limitada), o desempenho do acesso aos dados pode ser afetado significativamente, uma vez que o dispositivo precisa de aceder aos dados a partir da nuvem e armazená-lo localmente.

#### <a name="device-failover-across-software-versions"></a>Falha no dispositivo através de versões de software

Um serviço StorSimple Device Manager numa implementação pode ter vários dispositivos, tanto físicos como em nuvem, todos executando diferentes versões de software.

Utilize a tabela seguinte para determinar se pode falhar ou falhar em outro dispositivo que executa uma versão de software diferente e como os tipos de volume se comportam durante o DR.

#### <a name="failover-and-failback-across-software-versions"></a>Failover e failback através de versões de software

| Falhar mais/ Falhar de volta | Dispositivo físico | Aplicação da cloud |
| --- | --- | --- |
| Atualização 3 para Atualizar 4 |Os volumes hierárquicos falham como hierárquicos. <br></br>Os volumes fixados localmente falham como fixadolocalmente. <br></br> Após uma falha quando tira uma foto no dispositivo Update 4, o [rastreio baseado em mapas de calor](storsimple-update4-release-notes.md#whats-new-in-update-4) faz efeito. |Os volumes fixados localmente falham como hierárquicos. |
| Atualização 4 para Atualização 3 |Os volumes hierárquicos falham como hierárquicos. <br></br>Os volumes fixados localmente falham como fixadolocalmente. <br></br> As cópias de segurança utilizadas para restaurar retêm metadados de mapas de calor. <br></br>O rastreio baseado em mapas de calor não está disponível na Atualização 3 na sequência de uma falha. |Os volumes fixados localmente falham como hierárquicos. |


## <a name="device-failover-scenarios"></a>Cenários de falha do dispositivo

Se houver um desastre, poderá optar por falhar em relação ao seu dispositivo StorSimple:

* [Para um dispositivo físico.](storsimple-8000-device-failover-physical-device.md)
* [Para si mesmo.](storsimple-8000-device-failover-same-device.md)
* [Para um aparelho](storsimple-8000-device-failover-cloud-appliance.md)de nuvem .

Os artigos anteriores fornecem passos detalhados para cada um dos casos de failover acima referidos.


## <a name="failback"></a>Reativação pós-falha

Para versões Atualizadas 3 e posteriores, o StorSimple também suporta o failback. O failback é apenas o inverso da falha, o alvo torna-se a fonte e o dispositivo de origem original durante a falha torna-se agora o dispositivo alvo. 

Durante o failback, o StorSimple resincroniza os dados de volta ao local primário, para a atividade de I/O e aplicação e faz transições de volta para a localização original.

Depois de uma falha estar completa, a StorSimple executa as seguintes ações:

* O StorSimple limpa os recipientes de volume que são falhados a partir do dispositivo de origem.
* O StorSimple inicia um trabalho de fundo por recipiente de volume (falhado) no dispositivo de origem. Se tentar falhar enquanto o trabalho está em curso, receberá uma notificação nesse sentido. Espere até que o trabalho esteja completo para iniciar o recuo.
* O tempo de conclusão da eliminação dos contentores de volume depende de vários fatores, tais como a quantidade de dados, a idade dos dados, o número de cópias de segurança e a largura de banda da rede disponível para a operação.

Se estiver a planear falhas no teste ou a testar falhas, recomendamos que teste recipientes de volume com menos dados (Gbs). Normalmente, pode iniciar o recuo 24 horas após a falha estar completa.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. **O que acontece se o DR falhar ou tiver sucesso parcial?**

A. Se o DR falhar, recomendamos que tente de novo. O segundo dispositivo está ciente do progresso do primeiro trabalho e começa a partir daí.

P. **Posso apagar um dispositivo enquanto o dispositivo falha?**

A. Não é possível apagar um dispositivo enquanto um DR estiver em curso. Só pode eliminar o seu dispositivo depois de o DR estar completo. Pode monitorizar o progresso do trabalho falhado do dispositivo na lâmina **Jobs.**

P. **Quando é que a recolha de lixo começa no dispositivo de origem para que os dados locais sobre o dispositivo de origem sejam eliminados?**

A. A recolha de lixo só está ativada no dispositivo de origem depois de o dispositivo estar completamente limpo. A limpeza inclui a limpeza de objetos que falharam no dispositivo de origem, tais como volumes, objetos de reserva (não dados), contentores de volume e políticas.

P. **O que acontece se o trabalho de exclusão associado aos contentores de volume no dispositivo de origem falhar?**

A.  Se o trabalho de exclusão falhar, pode eliminar manualmente os recipientes de volume. Na lâmina **dos Dispositivos,** selecione o seu dispositivo de origem e clique em **recipientes volume**. Selecione os recipientes de volume que falhou e na parte inferior da lâmina, clique em **Apagar**. Depois de ter eliminado todos os recipientes de volume falhados no dispositivo de origem, pode iniciar o reinicializador. Para mais informações, vá a [Eliminar um recipiente](storsimple-8000-manage-volume-containers.md#delete-a-volume-container)de volume .

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastres de continuidade de negócios (BCDR)

Um cenário de recuperação de desastres de continuidade de negócios (BCDR) ocorre quando todo o datacenter Azure deixa de funcionar. Este cenário pode afetar o seu serviço StorSimple Device Manager e os dispositivos StorSimple associados.

Se um dispositivo StorSimple foi registado pouco antes de ocorrer um desastre, então este dispositivo poderá ter de ser submetido a um reset de fábrica. Após o desastre, o dispositivo StorSimple aparece no portal Azure como offline. Este dispositivo deve ser eliminado do portal. Reset o dispositivo para as predefinições da fábrica e registá-lo novamente com o serviço.

## <a name="next-steps"></a>Passos seguintes

Se estiver pronto para executar uma falha no dispositivo, escolha um dos seguintes cenários para instruções detalhadas:

- [Falhar em outro dispositivo físico](storsimple-8000-device-failover-physical-device.md)
- [Falhar no mesmo dispositivo](storsimple-8000-device-failover-same-device.md)
- [Falhar no StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Se falhou em relação ao seu dispositivo, escolha entre uma das seguintes opções:

* [Desative ou elimine o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Utilize o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

