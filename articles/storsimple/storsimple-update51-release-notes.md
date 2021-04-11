---
title: StorSimple 8000 Series Update 5.1 notas de lançamento
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 5.1.
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: cdb971851ba678ce18f5a1c7954e5620740f3a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657574"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 Series Update 5.1 notas de lançamento

## <a name="overview"></a>Descrição Geral

As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 5.1. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão.

A atualização 5.1 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar a Atualização 5. Se estiver a utilizar uma versão inferior a 5, aplique primeiro a atualização 5 e, em seguida, aplique 5.1. A versão do dispositivo associada ao Update 5.1 é de 6.3.9600.17885.

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
>
> * A atualização 5.1 é uma atualização obrigatória e deve ser instalada imediatamente. Para mais informações, consulte como [aplicar a atualização 5.1](storsimple-8000-install-update-51.md).
> * A atualização 5.1 tem apenas atualizações de segurança. Demora aproximadamente 30 minutos a instalar esta atualização. Recomendamos vivamente que aplique o Update 5.1 para garantir o funcionamento do seu dispositivo.
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente por atualizações, uma vez que estas atualizações ficarão disponíveis em breve.

## <a name="whats-new-in-update-51"></a>Novidades na Atualização 5.1

As seguintes melhorias-chave e correções de erros foram feitas na Atualização 5.1:

* **TLS 1.2** - Esta atualização StorSimple irá impor o TLS 1.2 a todos os clientes. Esta é uma atualização obrigatória para todos os dispositivos da série 8000 do StorSimple.

   Se vir o seguinte aviso, deve atualizar o software do dispositivo antes de prosseguir:

   Um ou mais dispositivos StorSimple estão a executar uma versão de software mais antiga. A mais recente atualização disponível para OLS 1.2 é uma atualização obrigatória e deve ser instalada imediatamente nestes dispositivos. O TLS 1.2 é utilizado para toda a comunicação do portal Azure e sem esta atualização, o dispositivo não será capaz de comunicar com o serviço StorSimple.

## <a name="known-issues-in-update-51-from-previous-releases"></a>Problemas conhecidos no Update 5.1 de lançamentos anteriores

Não existem novos problemas conhecidos no Update 5.1. Para obter uma lista de problemas transitadas para atualizar 5.1 de versões anteriores, aceda às [notas de lançamento do Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Atualizações storSimple cloud appliance na atualização 5.1

Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). É necessário criar novos aparelhos em nuvem utilizando a imagem Update 5.1. Para obter informações sobre como criar um aparelho de nuvem StorSimple, vá ao [Implementar e gere um aparelho de nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).

## <a name="next-step"></a>Passo seguinte

Saiba como [instalar o Update 5.1](storsimple-8000-install-update-51.md) no seu dispositivo StorSimple.
