---
title: Falha e recuperação de desastres para dispositivo da série StorSimple 8000
description: Aprenda a falhar sobre o seu dispositivo StorSimple para si mesmo, outro dispositivo físico ou um aparelho em nuvem.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85511802"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Failover and disaster recovery for your StorSimple 8000 series devices (Ativação pós-falha e recuperação após desastre para dispositivos da série StorSimple 8000)

## <a name="overview"></a>Descrição geral

Este artigo descreve a funcionalidade de falha do dispositivo para os dispositivos da série StorSimple 8000 e como esta funcionalidade pode ser usada para recuperar dispositivos StorSimple em caso de desastre. O StorSimple utiliza o dispositivo falha na migração dos dados de um dispositivo de origem no datacenter para outro dispositivo alvo. A orientação deste artigo aplica-se a dispositivos físicos da série StorSimple 8000 e a aparelhos em nuvem que executam versões de software Update 3 e posteriormente.

O StorSimple utiliza a lâmina **devices** para iniciar a função de falha do dispositivo em caso de desastre. Esta lâmina lista todos os dispositivos StorSimple ligados ao seu serviço StorSimple Device Manager.

![Lâmina de dispositivos](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Recuperação de desastres (DR) e falha do dispositivo

Num cenário de recuperação de desastres (DR), o dispositivo primário deixa de funcionar. O StorSimple utiliza o dispositivo primário como _fonte_ e move os dados de nuvem associados para outro dispositivo _alvo._ Este processo é referido como o *fracasso*. O gráfico que se segue ilustra o processo de failover.

![O que acontece no dispositivo que falha?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

O dispositivo-alvo para uma falha pode ser um dispositivo físico ou até mesmo um aparelho em nuvem. O dispositivo-alvo pode estar localizado na mesma localização geográfica ou num local geográfico diferente do dispositivo de origem.

Durante a falha, pode selecionar recipientes de volume para migração. StorSimple muda então a propriedade destes recipientes de volume do dispositivo de origem para o dispositivo alvo. Uma vez que os recipientes de volume mudam de propriedade, o StorSimple elimina estes recipientes do dispositivo de origem. Depois de a eliminação estar completa, pode falhar de volta o dispositivo alvo. _O failback_ transfere a propriedade de volta para o dispositivo de origem original.

### <a name="cloud-snapshot-used-during-device-failover"></a>Snapshot de nuvem usado durante a falha do dispositivo

Após um DR, a cópia de segurança na nuvem mais recente é usada para restaurar os dados para o dispositivo alvo. Para obter mais informações sobre as imagens em nuvem, consulte [utilize o serviço StorSimple Device Manager para obter uma cópia de segurança manual](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Numa série StorSimple 8000, as políticas de backup estão associadas a cópias de segurança. Se houver várias políticas de backup para o mesmo volume, então o StorSimple seleciona a política de backup com o maior número de volumes. StorSimple utiliza então a cópia de segurança mais recente da política de backup selecionada para restaurar os dados no dispositivo-alvo.

Suponha que existam duas políticas de backup, *defaultPol* e *customPol:*

* *defaultPol*: Um volume, *vol1,* funciona diariamente a partir das 22:30.
* *customPol*: Quatro volumes, *vol1,* *vol2,* *vol3,* *vol4,* funciona diariamente a partir das 22:00.

Neste caso, a StorSimple prioriza-se para a consistência do crash e utiliza *a customPol* uma vez que tem mais volumes. O mais recente backup desta política é usado para restaurar os dados. Para obter mais informações sobre como criar e gerir políticas de backup, vá ao [Serviço StorSimple Device Manager para gerir as políticas de backup](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Considerações comuns sobre a ativação pós-falha do dispositivo

Antes de falhar sobre um dispositivo, reveja as seguintes informações:

* Antes de o dispositivo falhar, todos os volumes dentro dos recipientes de volume devem estar desligados. Numa falha não planeada, os volumes StotSimple ficarão automaticamente offline. Mas se estiver a realizar uma falha planeada (para testar o DR), tem de desligar todos os volumes.
* Apenas os recipientes de volume que têm uma foto de nuvem associada estão listados para DR. Deve haver pelo menos um recipiente de volume com uma imagem de nuvem associada para recuperar dados.
* Se existirem instantâneos em nuvem que se estendem por vários recipientes de volume, o StorSimple falha sobre estes recipientes de volume como um conjunto. Num caso raro, se houver instantâneos locais que se estendem por vários recipientes de volume, mas os instantâneos de nuvem associados não, o StorSimple falha sobre os instantâneos locais e os dados locais perdem-se após o DR.
* Os dispositivos-alvo disponíveis para DR são dispositivos que têm espaço suficiente para acomodar os recipientes de volume selecionados. Quaisquer dispositivos que não tenham espaço suficiente, não estão listados como dispositivos-alvo.
* Após um DR (por uma duração limitada), o desempenho do acesso aos dados pode ser afetado significativamente, uma vez que o dispositivo necessita de aceder aos dados a partir da nuvem e armazená-lo localmente.

#### <a name="device-failover-across-software-versions"></a>Falha no dispositivo através de versões de software

Um serviço StorSimple Device Manager numa implementação pode ter vários dispositivos, tanto físicos como em nuvem, todos executando diferentes versões de software.

Utilize a tabela seguinte para determinar se pode falhar ou falhar de novo para outro dispositivo que executa uma versão de software diferente e como os tipos de volume se comportam durante o DR.

#### <a name="failover-and-failback-across-software-versions"></a>Failover e failback em todas as versões de software

| Falha mais/ Falha de volta de | Dispositivo físico | Aplicação da cloud |
| --- | --- | --- |
| Atualização 3 para atualização 4 |Os volumes hierárquicos falham como hierarquizados. <br></br>Os volumes fixados localmente falham como fixados localmente. <br></br> Na sequência de uma falha quando tira uma fotografia no dispositivo Update 4, o rastreio baseado em [mapas térmicos](storsimple-update4-release-notes.md#whats-new-in-update-4) entra em vigor. |Os volumes fixados localmente falham como hierarquizados. |
| Atualização 4 para Atualização 3 |Os volumes hierárquicos falham como hierarquizados. <br></br>Os volumes fixados localmente falham como fixados localmente. <br></br> Cópias de segurança usadas para restaurar retêm metadados de mapas de calor. <br></br>O rastreio baseado em heatmap não está disponível no Update 3 na sequência de uma falha. |Os volumes fixados localmente falham como hierarquizados. |


## <a name="device-failover-scenarios"></a>Cenários de falha do dispositivo

Em caso de desastre, pode optar por falhar sobre o seu dispositivo StorSimple:

* [Para um dispositivo físico.](storsimple-8000-device-failover-physical-device.md)
* [Para si mesmo.](storsimple-8000-device-failover-same-device.md)
* [Para um aparelho em nuvem](storsimple-8000-device-failover-cloud-appliance.md).

Os artigos anteriores fornecem passos pormenorizados para cada um dos casos de failover acima.


## <a name="failback"></a>Reativação pós-falha

Para as versões Update 3 e posteriores, o StorSimple também suporta falhas. O failback é apenas o inverso do failover, o alvo torna-se a fonte e o dispositivo de origem original durante a falha torna-se agora o dispositivo alvo. 

Durante o failback, o StorSimple re-sincroniza os dados de volta à localização primária, interrompe a atividade de I/O e aplicação e volta para a localização original.

Após a conclusão de uma falha, a StorSimple executa as seguintes ações:

* O StorSimple limpa os recipientes de volume que são ressaridos do dispositivo de origem.
* A StorSimple inicia um trabalho de fundo por recipiente de volume (falhado) no dispositivo de origem. Se tentar recuar enquanto o trabalho está em curso, receberá uma notificação nesse sentido. Espere até que o trabalho esteja completo para começar a falha.
* O tempo necessário para completar a supressão dos recipientes de volume depende de vários fatores, tais como a quantidade de dados, a idade dos dados, o número de cópias de segurança e a largura de banda de rede disponível para a operação.

Se estiver a planear falhas de teste ou falhas de teste, recomendamos que teste recipientes de volume com menos dados (Gbs). Normalmente, pode iniciar o failback 24 horas após a falha estar completa.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

P. **O que acontece se o DR falhar ou tiver sucesso parcial?**

A. Se o DR falhar, recomendamos que tente de novo. O segundo dispositivo de falha no trabalho está ciente do progresso do primeiro emprego e começa a partir daí.

P. **Posso eliminar um dispositivo enquanto o dispositivo está em curso?**

A. Não é possível eliminar um dispositivo enquanto um DR estiver em curso. Só pode apagar o seu dispositivo depois de o DR estar completo. Pode monitorizar o progresso do trabalho falhado no trabalho da lâmina **Jobs.**

P. **Quando é que a recolha de lixo começa no dispositivo de origem para que os dados locais sobre o dispositivo de origem seja eliminado?**

A. A recolha de lixo só é ativada no dispositivo de origem depois de o dispositivo estar completamente limpo. A limpeza inclui a limpeza de objetos que tenham falhado no dispositivo de origem, tais como volumes, objetos de backup (não dados), recipientes de volume e políticas.

P. **O que acontece se o trabalho de eliminação associado aos recipientes de volume no dispositivo de origem falhar?**

A.  Se o trabalho de eliminação falhar, pode apagar manualmente os recipientes de volume. Na lâmina **dispositivos,** selecione o seu dispositivo de origem e clique em **recipientes de volume**. Selecione os recipientes de volume que falhou por cima e na parte inferior da lâmina, clique em **Eliminar**. Depois de ter apagado todos os recipientes de volume falhados no dispositivo de origem, pode iniciar a falha. Para obter mais informações, [aceda](storsimple-8000-manage-volume-containers.md#delete-a-volume-container)a Eliminar um recipiente de volume .

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastres de continuidade do negócio (BCDR)

Um cenário de recuperação de desastres de continuidade do negócio (BCDR) ocorre quando todo o datacenter Azure deixa de funcionar. Este cenário pode afetar o seu serviço StorSimple Device Manager e os dispositivos StorSimple associados.

Se um dispositivo StorSimple foi registado pouco antes de ocorrer um desastre, então este dispositivo poderá ter de ser submetido a um reset de fábrica. Após o desastre, o dispositivo StorSimple aparece no portal Azure como offline. Este dispositivo deve ser eliminado do portal. Repôs o dispositivo às falhas de fábrica e registe-o novamente com o serviço.

## <a name="next-steps"></a>Passos seguintes

Se estiver pronto para executar um dispositivo de falha, escolha um dos seguintes cenários para instruções detalhadas:

- [Falha em outro dispositivo físico](storsimple-8000-device-failover-physical-device.md)
- [Falha no mesmo dispositivo](storsimple-8000-device-failover-same-device.md)
- [Falha no StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Se falhou no seu dispositivo, escolha entre uma das seguintes opções:

* [Desativar ou eliminar o seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).
* [Utilize o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

