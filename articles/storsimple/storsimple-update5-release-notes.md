---
title: Notas de lançamento StorSimple 8000 Series Update 5
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021049"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de lançamento StorSimple 8000 Series Update 5

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 5. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão.

A atualização 5 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar a atualização 0.1 através da Atualização 4. A versão do dispositivo associada ao Update 5 é de 6.3.9600.17845.

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 5 é uma atualização obrigatória e deve ser instalada imediatamente. Para mais informações, consulte como [aplicar a Atualização 5](storsimple-8000-install-update-5.md).
> * A Atualização 5 tem software de dispositivos, firmware de disco, segurança de SO e outras atualizações de SISTEMA. Leva aproximadamente 4 horas para instalar esta atualização. A atualização do firmware em disco é uma atualização disruptiva e resulta num tempo de inatividade para o seu dispositivo. Recomendamos que aplique o Update 5 para manter o seu dispositivo atualizado.
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente por atualizações, uma vez que estas atualizações ficarão disponíveis em breve.

## <a name="whats-new-in-update-5"></a>Novidades na Atualização 5

As seguintes melhorias chave e correções de erros foram feitas na Atualização 5.

* **Utilização do Azure Ative Directory (AAD) para autenticar com o serviço StorSimple Device Manager** – A partir da Atualização 5, o Azure Ative Directory é utilizado para autenticar com o serviço StorSimple Device Manager. O antigo mecanismo de autenticação será depreciado até dezembro de 2017. Todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall. Para mais informações, aceda aos [URLs de autenticação listados nos requisitos de rede para o seu dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se o URL de autenticação não estiver incluído nas regras de firewall, os utilizadores verão um alerta crítico de que o seu dispositivo StorSimple não poderia autenticar com o serviço. Se os utilizadores virem este alerta, têm de incluir o novo URL de autenticação. Para mais informações, aceda aos [alertas de networking StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nova versão do StorSimple Snapshot Manager** - Uma nova versão do StorSimple Snapshot Manager é lançada com o Update 5 e é compatível com todos os dispositivos StorSimple que estão a executar a Atualização 4 ou posterior. Recomendamos que atualize esta versão. A versão anterior do StorSimple Snapshot Manager é utilizada para dispositivos StorSimple que estão a executar a Atualização 3 ou mais cedo. [Descarregue a versão adequada do StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e consulte o [StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas corrigidos na Atualização 5

A tabela seguinte fornece um resumo das questões que foram corrigidas na Atualização 5.

| No | Destaque | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Remoting De Windows PowerShell |No lançamento anterior, um utilizador receberia um erro ao tentar estabelecer uma ligação remota ao StorSimple Cloud Appliance via Windows PowerShell. Esta questão foi causada por raízes e corrigida nesta versão. |Não |Sim |
| 2 |Modelos de largura de banda |Em versão anterior, houve um problema com os modelos de largura de banda que resultou numa largura de banda mais baixa do que o dispositivo configurado. Esta questão é resolvida nesta versão. |Sim |Sim |
| 3 |Ativação pós-falha |Em versão anterior, quando um dispositivo com um grande número de volumes foi falhado em outro dispositivo em execução Atualização 4, o processo falharia ao tentar aplicar os registos de controlo de acesso. Esta questão é corrigida nesta versão. |Sim |Sim |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas conhecidos na Atualização 5 de lançamentos anteriores

Não existem novos problemas conhecidos na Atualização 5. Para obter uma lista de problemas transitadas para a Atualização 5 de versões anteriores, aceda às [notas de lançamento do Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>Atualizações de controlador e firmware SCSI (SAS) anexadas em série na Atualização 5

Esta versão tem o controlador SAS e as atualizações do controlador LSI e do firmware. Para obter mais informações sobre como instalar estas atualizações, consulte [a instalação do Update 5](storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>Atualizações de aparelhos em nuvem StorSimple na Atualização 5

Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). É necessário criar novos aparelhos em nuvem utilizando a imagem Update 5. Para obter informações sobre como criar um aparelho de nuvem StorSimple, vá ao [Implementar e gere um aparelho de nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passo seguinte

Saiba como instalar o [Update 5](storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.

