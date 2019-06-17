---
title: Notas de versão do StorSimple 8000 Series Update 5 | Documentos da Microsoft
description: Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 Series Update 5.
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
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844096"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de versão do StorSimple 8000 Series Update 5

## <a name="overview"></a>Descrição geral

As notas de versão seguintes descrevem os novos recursos e identificar os problemas em aberto críticos para a StorSimple 8000 Series Update 5. Também contêm uma lista das atualizações de software StorSimple incluídos nesta versão.

Atualização 5 pode ser aplicada a todos os dispositivos StorSimple que executam a atualização 0.1 a atualização 4. A versão de dispositivo associada a atualização 5 é 6.3.9600.17845.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Atualização 5 é uma atualização obrigatória e tem de ser instalada imediatamente. Para obter mais informações, consulte como [aplicam-se a atualização 5](storsimple-8000-install-update-5.md).
> * Atualização 5 tem o software de dispositivos, firmware do disco, segurança do sistema operacional e outras atualizações de sistema operacional. Demora cerca de quatro horas para instalar esta atualização. Atualização de firmware do disco é uma atualização disruptivas e resulta num período de indisponibilidade para o seu dispositivo. Recomendamos que aplique a atualização 5 para manter o seu dispositivo atualizado.
> * Para novas versões, poderá não ver atualizações imediatamente porque fazemos uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise para atualizações novamente como essas atualizações estarão disponível em breve.

## <a name="whats-new-in-update-5"></a>O que há de novo na atualização 5

Foram efetuadas as seguintes principais melhoramentos e correções de erros no Update 5.

* **Utilização do Azure Active Directory (AAD) para autenticar com o serviço StorSimple Device Manager** – da atualização 5 e posteriores, o Azure Active Directory é utilizado para autenticar com o serviço StorSimple Device Manager. O mecanismo de autenticação antigo vai ser preterido de Dezembro de 2017. Todos os utilizadores têm de incluir os novos URLs de autenticação nas suas regras de firewall. Para obter mais informações, aceda a [autenticação URLs listadas nos requisitos de rede para o dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se o URL de autenticação não está incluído nas regras da firewall, os utilizadores vão ver um alerta crítico que o dispositivo StorSimple não foi possível autenticar com o serviço. Se os utilizadores veem este alerta, terá de incluir o novo URL de autenticação. Para obter mais informações, aceda a [StorSimple alertas de sistema de rede](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nova versão do Snapshot Manager do StorSimple** -uma nova versão do Snapshot Manager do StorSimple é fornecida com Update 5 e é compatível com todos os dispositivos do StorSimple que estejam a executar a atualização 4 ou posterior. Recomendamos que Atualize para esta versão. A versão anterior do Snapshot Manager do StorSimple é utilizado para dispositivos do StorSimple que estejam a executar a atualização 3 ou anterior. [Baixe a versão adequada do Snapshot Manager do StorSimple](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e consulte [implementar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas corrigidos na atualização 5

A tabela seguinte fornece um resumo dos problemas que foram corrigidas na atualização 5.

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Comunicação remota do Windows PowerShell |Na versão anterior, um utilizador receberia um erro ao tentar estabelecer uma ligação remota para a StorSimple Cloud Appliance através do Windows PowerShell. Este problema foi causado de raiz e corrigido nesta versão. |Não |Sim |
| 2 |Modelos de largura de banda |Na versão anterior, Ocorreu um problema com os modelos de largura de banda que resultaram em que o que o dispositivo foi configurado para menor largura de banda. Este problema é resolvido nesta versão. |Sim |Sim |
| 3 |Ativação pós-falha |Na versão anterior, quando um dispositivo com um grande número de volumes foi a ativação pós-falha para outro dispositivo a executar a atualização 4, o processo de falha ao tentar aplicar os registos de controlo de acesso. Este problema é corrigido nesta versão. |Sim |Sim |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas conhecidos em 5 de atualização de versões anteriores

Não existem não existem problemas conhecidos de novo na atualização 5. Para obter uma lista dos problemas transferidas para a atualização 5 de versões anteriores, aceda a [notas de versão de atualização 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Ligação série SCSI (SAS) controlador e atualizações de firmware em 5 de atualização

Esta versão tem o controlador SAS e atualizações de controladores e firmware LSI. Para obter mais informações sobre como instalar estas atualizações, consulte [instalar atualização 5](storsimple-8000-install-update-5.md) no dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Atualizações do StorSimple Cloud Appliance atualização 5

Esta atualização não é possível aplicar a StorSimple Cloud Appliance (também conhecido como o dispositivo virtual). Novas aplicações de cloud tem de ser criado usando a imagem de atualização 5. Para informações sobre como criar uma StorSimple Cloud Appliance, aceda a [implementar e gerir uma StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar atualização 5](storsimple-8000-install-update-5.md) no dispositivo StorSimple.

