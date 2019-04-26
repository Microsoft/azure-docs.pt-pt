---
title: Notas de versão do StorSimple 8000 Series Update 4 | Documentos da Microsoft
description: Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 Series Update 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530978"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Notas de versão do StorSimple 8000 Series Update 4

## <a name="overview"></a>Descrição geral

As notas de versão seguintes descrevem os novos recursos e identificar os problemas em aberto críticos para a StorSimple 8000 Series Update 4. Também contêm uma lista das atualizações de software StorSimple incluídos nesta versão. 

Atualização 4 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 por meio de atualização 3.1. A versão de dispositivo associada a atualização 4 é 6.3.9600.17820.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 4 tem o software de dispositivos, USM de firmware, controladores de LSI e firmware, firmware do disco, Storport e Spaceport, segurança e outras atualizações de SO. Demora cerca de quatro horas para instalar esta atualização. Atualização de firmware do disco é uma atualização disruptivas e resulta num período de indisponibilidade para o seu dispositivo. Recomendamos que aplique a atualização 4 para manter o seu dispositivo atualizado. 
> * Para novas versões, poderá não ver atualizações imediatamente porque fazemos uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise para atualizações novamente como estes ficarão disponível em breve.

## <a name="whats-new-in-update-4"></a>O que há de novo na atualização 4

Foram efetuadas as seguintes principais melhoramentos e correções de erros na atualização 4.

* **Mais inteligentes de algoritmos de recuperação de espaço automatizada** – na atualização 4, a recuperação de espaço automatizada algoritmos estão otimizados para ajustar os ciclos de recuperação de espaço com base no espaço reclaimed esperado disponível na cloud. 

* **Melhorias de desempenho para volumes afixados localmente** – atualização 4 melhorou o desempenho de volumes localmente afixados em cenários que têm a ingestão de dados de elevado (comparáveis ao tamanho do volume de dados).

* **Restauro baseada em mapa térmico** -nas versões anteriores, seguindo uma recuperação após desastre (DR), os dados foi restaurados da cloud com base nos padrões de acesso, resultando num desempenho lento. 

    Um novo recurso está implementado na atualização 4 que roteiros dados acedidos com frequência para criar um mapa térmico quando o dispositivo está a ser utilizada antes de DR (segmentos de dados mais utilizados tem térmico elevado, ao passo que o menor utilizado segmentos ter térmico baixo). Depois de DR, o StorSimple utiliza o mapa térmico para restaurar e os dados da cloud de reidratação automaticamente. 

    Todas as restaurações estão agora restaurações de mapa térmico com base. Para obter mais informações sobre como consultar e cancelar tarefas de restauro e a reidratação de mapa térmico com base, aceda a [Windows PowerShell para StorSimple referência de cmdlet](https://technet.microsoft.com/library/dn688168.aspx).

* **A ferramenta Diagnósticos de StorSimple** – na atualização 4, uma ferramenta de diagnóstico do StorSimple está a ser lançado para permitir a fácil diagnosticar e resolução de problemas dos problemas relacionados com o estado de funcionamento de componente do sistema, rede, desempenho e hardware. Esta ferramenta é executada através do Windows PowerShell para StorSimple. Para obter mais informações, aceda a [resolver problemas com a ferramenta Diagnósticos de StorSimple](storsimple-8000-diagnostics.md).

* **Ferramenta de migração do StorSimple baseada em interface do Usuário** -antes desta versão, a migração de dados da 5000-7000 série necessários os utilizadores executar uma parte do fluxo de trabalho de migração através da interface do PowerShell do Azure. Nesta versão, uma ferramenta de migração de StorSimple baseada em interface do Usuário fácil de usar é disponibilizada para o suporte facilitar o mesmo fluxo de trabalho de migração. Essa ferramenta também permitem a consolidação de buckets de recuperação. 

* **Alterações relacionadas com a FIPS** – esta versão e posteriores, FIPS está ativado por predefinição em todos os dispositivos de série 8000 do StorSimple para o Microsoft Azure Government e o Azure público contas da cloud.

* **Atualizar alterações** -nesta versão, tem sido corrigidos erros relacionados com falhas de atualização.

* **Alerta de falhas de disco** -um novo alerta que avisa o usuário de falhas de disco iminentes é adicionado nesta versão. Se encontrar este alerta, entre em contato com Support da Microsoft para enviar um disco de substituição. Para obter mais informações, aceda a [alertas de hardware no dispositivo StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Alterações de substituição de controlador** -um cmdlet que permita ao usuário consultar o estado do processo de substituição de controlador é adicionado nesta versão. Para obter mais informações, vá para o [cmdlet para o estado de substituição de controlador de consulta](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problemas corrigidos na atualização 4

A tabela seguinte fornece um resumo dos problemas que foram corrigidas na atualização 4.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Ativação pós-falha |Na versão anterior, após a ativação pós-falha, Ocorreu um problema relacionado com a limpeza observada no site do cliente. Este problema é corrigido nesta versão. |Sim |Sim |
| 2 |volumes afixados localmente |Na versão anterior, Ocorreu um problema para a criação do volume relacionados para volumes afixados localmente que iria resultar em falhas de criação do volume. Este problema foi causado de raiz e corrigido nesta versão. |Sim |Não |
| 3 |Pacote de suporte |Na versão anterior, ocorreram problemas relacionados com o pacote de suporte que resultaria numa exceção de System.OutOfMemory ou outros erros, resultando numa falha de criação do pacote de suporte. Esses bugs são corrigidos nesta versão. |Sim |Sim |
| 4 |Monitorização |Na versão anterior, existe um problema relacionado à monitorização localmente os gráficos para volumes afixados em que o consumo foi mostrado na EB. Esse bug foi resolvido nesta versão. |Sim |Sim |
| 5 |Migração |Na versão anterior, havia várias questões relacionadas à confiabilidade de migração da 5000-7000 série aos dispositivos da 8000 série. Esses problemas foram solucionados nesta versão. |Sim |Sim |
| 6 |Atualizar |Em versões anteriores, se tiver ocorrido uma falha de atualização, os controladores deveria Ir para o modo de recuperação e, por conseguinte, o utilizador não foi possível continuar com a atualização e será necessário entrar em contacto Support da Microsoft. <br> Esse comportamento foi alterado nesta versão. Se o utilizador tiver uma falha de atualização depois dos dois controladores estiverem a executar a mesma versão (atualização 4), os controladores não entram em modo de recuperação. Se o usuário o perceba desta falha, recomendamos que aguarde um pouco e, em seguida, repita a atualização. A repetição pode ter êxito. Se a nova tentativa falhar, em seguida, deverá contactar o Support da Microsoft. |Sim |Sim |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemas conhecidos na atualização 4 de versões anteriores

Não existem não existem problemas conhecidos de novo na atualização 4. Para obter uma lista dos problemas transferidas para a atualização 4 de versões anteriores, aceda a [notas de versão de atualização 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Ligação série SCSI (SAS) controlador e atualizações de firmware na atualização 4

Esta versão tem o controlador SAS e atualizações de controladores e firmware LSI. Para obter mais informações sobre como instalar estas atualizações, consulte [instalar a atualização 4](storsimple-install-update-4.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Atualizações do dispositivo virtual na atualização 4

Esta atualização não é possível aplicar a StorSimple Cloud Appliance (também conhecido como o dispositivo virtual). Novos dispositivos virtual tem de ser criado. 

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar a atualização 4](storsimple-install-update-4.md) no dispositivo StorSimple.

