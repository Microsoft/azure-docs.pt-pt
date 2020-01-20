---
title: Notas de versão da atualização 5 para o StorSimple 8000 Series
description: Descreve os novos recursos, problemas e soluções alternativas para a atualização 5 do StorSimple 8000 Series.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275159"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de versão da atualização 5 para o StorSimple 8000 Series

## <a name="overview"></a>Visão geral

As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos do StorSimple 8000 Series Update 5. Eles também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão.

A atualização 5 pode ser aplicada a qualquer dispositivo StorSimple que esteja executando a atualização 0,1 até a atualização 4. A versão do dispositivo associada à atualização 5 é 6.3.9600.17845.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 5 é uma atualização obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte como [aplicar a atualização 5](storsimple-8000-install-update-5.md).
> * A atualização 5 tem software de dispositivo, firmware de disco, segurança do sistema operacional e outras atualizações do sistema operacional. Leva aproximadamente 4 horas para instalar essa atualização. A atualização de firmware de disco é uma atualização com interrupção e resulta em um tempo de inatividade para seu dispositivo. Recomendamos que você aplique a atualização 5 para manter seu dispositivo atualizado.
> * Para novas versões, talvez você não veja as atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e, em seguida, verifique se há atualizações novamente, pois essas atualizações serão disponibilizadas em breve.

## <a name="whats-new-in-update-5"></a>O que há de novo na atualização 5

As principais melhorias e correções de bugs a seguir foram feitas na atualização 5.

* O **uso de Azure Active Directory (AAD) para autenticar com o serviço storsimple Device Manager** – da atualização 5 em diante, Azure Active Directory é usado para autenticar com o serviço de Device Manager do storsimple. O antigo mecanismo de autenticação será preterido em dezembro de 2017. Todos os usuários devem incluir as novas URLs de autenticação em suas regras de firewall. Para obter mais informações, acesse [URLs de autenticação listadas nos requisitos de rede para seu dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se a URL de autenticação não estiver incluída nas regras de firewall, os usuários verão um alerta crítico de que seu dispositivo StorSimple não pôde se autenticar no serviço. Se os usuários veem esse alerta, eles precisam incluir a nova URL de autenticação. Para obter mais informações, acesse [alertas de rede do StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nova versão do storsimple snapshot Manager** -uma nova versão do storsimple snapshot Manager é lançada com a atualização 5 e é compatível com todos os dispositivos StorSimple que estão executando a atualização 4 ou posterior. Recomendamos que você atualize para esta versão. A versão anterior do StorSimple Snapshot Manager é usada para dispositivos StorSimple que estão executando a atualização 3 ou anterior. [Baixe a versão apropriada do storsimple snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e consulte [implantar o storsimple snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas corrigidos na atualização 5

A tabela a seguir fornece um resumo dos problemas que foram corrigidos na atualização 5.

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Comunicação remota do Windows PowerShell |Na versão anterior, um usuário receberia um erro ao tentar estabelecer uma conexão remota com o dispositivo de nuvem StorSimple por meio do Windows PowerShell. Esse problema foi causado pela raiz e foi corrigido nesta versão. |Não |Sim |
| 2 |Modelos de largura de banda |Na versão anterior, houve um problema com modelos de largura de banda que resultaram em menor largura de banda do que o dispositivo configurado. Esse problema foi resolvido nesta versão. |Sim |Sim |
| 3 |Ativação Pós-Falha |Na versão anterior, quando um dispositivo com um grande número de volumes passou por failover para outro dispositivo executando a atualização 4, o processo falha ao tentar aplicar os registros de controle de acesso. Esse problema foi corrigido nesta versão. |Sim |Sim |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas conhecidos na atualização 5 de versões anteriores

Não há novos problemas conhecidos na atualização 5. Para obter uma lista de problemas transferidos para a atualização 5 de versões anteriores, acesse [atualização 3 notas de versão](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Controlador SCSI (SAS) anexado em série e atualizações de firmware na atualização 5

Esta versão tem controlador SAS e atualizações de firmware e driver LSI. Para obter mais informações sobre como instalar essas atualizações, consulte [instalar a atualização 5](storsimple-8000-install-update-5.md) em seu dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Atualizações do dispositivo de nuvem StorSimple na atualização 5

Esta atualização não pode ser aplicada ao dispositivo de nuvem StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos de nuvem precisam ser criados usando a imagem da atualização 5. Para obter informações sobre como criar um dispositivo de nuvem StorSimple, vá para [implantar e gerenciar um dispositivo de nuvem storsimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar a atualização 5](storsimple-8000-install-update-5.md) em seu dispositivo StorSimple.

