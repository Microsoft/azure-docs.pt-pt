---
title: Notas de lançamento da Série 5 storSimple 8000
description: Descreve as novas funcionalidades, problemas e soluções para storSimple 8000 Series Update 5.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 4fed65e12a166c046a0c63d7c4849f86805bbe9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76275159"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>Notas de lançamento da Série 5 storSimple 8000

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões críticas em aberto para o StorSimple 8000 Series Update 5. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão.

A atualização 5 pode ser aplicada a qualquer dispositivo StorSimple que execute o Update 0.1 através do Update 4. A versão do dispositivo associada ao Update 5 é 6.3.9600.17845.

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 5 é uma atualização obrigatória e deve ser instalada imediatamente. Para mais informações, consulte como aplicar a [Atualização 5](storsimple-8000-install-update-5.md).
> * A Atualização 5 tem software de dispositivos, firmware de disco, segurança osso e outras atualizações de SO. Demora aproximadamente 4 horas a instalar esta atualização. A atualização do firmware do disco é uma atualização disruptiva e resulta numa paragem para o seu dispositivo. Recomendamos que aplique o Update 5 para manter o seu dispositivo atualizado.
> * Para novos lançamentos, pode não ver as atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente as atualizações, uma vez que estas atualizações estarão disponíveis em breve.

## <a name="whats-new-in-update-5"></a>Novidades na Atualização 5

As seguintes melhorias de teclas e correções de erros foram feitas no Update 5.

* **A utilização do Azure Ative Directory (AAD) para autenticar com o serviço StorSimple Device Manager** – A partir da Atualização 5, o Diretório Ativo Azure é utilizado para autenticar com o serviço StorSimple Device Manager. O antigo mecanismo de autenticação será depreto até dezembro de 2017. Todos os utilizadores devem incluir os novos URLs de autenticação nas suas regras de firewall. Para mais informações, vá a [URLs de autenticação listados nos requisitos de rede para o seu dispositivo StorSimple](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal).

    Se o URL de autenticação não estiver incluído nas regras de firewall, os utilizadores verão um alerta crítico de que o seu dispositivo StorSimple não poderia autenticar com o serviço. Se os utilizadores virem este alerta, têm de incluir o novo URL de autenticação. Para mais informações, vá a alertas de [rede StorSimple](storsimple-8000-manage-alerts.md#networking-alerts).

* **Nova versão do StorSimple Snapshot Manager** - Uma nova versão do StorSimple Snapshot Manager é lançada com o Update 5 e é compatível com todos os dispositivos StorSimple que estão a executar o Update 4 ou mais tarde. Recomendamos que atualize para esta versão. A versão anterior do StorSimple Snapshot Manager é utilizada para dispositivos StorSimple que estão a executar o Update 3 ou mais cedo. [Descarregue a versão apropriada do StorSimple Snapshot Manager](https://www.microsoft.com/en-us/download/details.aspx?id=44220) e consulte a implementação do [StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).


## <a name="issues-fixed-in-update-5"></a>Problemas corrigidos na Atualização 5

A tabela seguinte apresenta um resumo das questões que foram corrigidas na Atualização 5.

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- |
| 1 |Remo do Windows PowerShell |Na versão anterior, um utilizador receberia um erro ao tentar estabelecer uma ligação remota ao StorSimple Cloud Appliance via Windows PowerShell. Esta questão foi causada e corrigida nesta libertação. |Não |Sim |
| 2 |Modelos de largura de banda |No lançamento anterior, houve um problema com modelos de largura de banda que resultou em largura de banda mais baixa do que o dispositivo foi configurado. Esta questão está resolvida nesta libertação. |Sim |Sim |
| 3 |Ativação pós-falha |Na versão anterior, quando um dispositivo com um grande número de volumes foi falhado em outro dispositivo que executa o Update 4, o processo falharia ao tentar aplicar os registos de controlo de acesso. Esta questão está corrigida nesta versão. |Sim |Sim |



## <a name="known-issues-in-update-5-from-previous-releases"></a>Problemas conhecidos no Update 5 de lançamentos anteriores

Não há novos problemas conhecidos na Atualização 5. Para obter uma lista de problemas transportados para a Atualização 5 a partir de lançamentos anteriores, vá a notas de [lançamento do Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>As atualizações do controlador SCSI (SAS) e do firmware em série na Atualização 5

Esta versão tem o controlador SAS e as atualizações de controlador LSI e firmware. Para obter mais informações sobre como instalar estas atualizações, consulte [a instalação do Update 5](storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>StorSimple Cloud Appliance atualiza na Atualização 5

Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). É necessário criar novos aparelhos em nuvem utilizando a imagem Update 5. Para obter informações sobre como criar um StorSimple Cloud Appliance, vá para [o Deploy e gerencie um StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passo seguinte

Saiba como instalar o [Update 5](storsimple-8000-install-update-5.md) no seu dispositivo StorSimple.

