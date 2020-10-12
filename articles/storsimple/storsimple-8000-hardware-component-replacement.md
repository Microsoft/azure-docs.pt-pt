---
title: Substituição de componentes de hardware da série StorSimple 8000 Microsoft Docs
description: Descreve como substituir com segurança os PCMs, bateria, módulos controladores, controladores EBOD, unidades de disco e chassis de um dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564293"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Substitua um componente de hardware no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
Os tutoriais de substituição de componentes de hardware descrevem os componentes de hardware do seu dispositivo da série Microsoft Azure StorSimple 8000 e os passos necessários para removê-los e substituí-los. Este artigo descreve os ícones de segurança, fornece ponteiros para os tutoriais detalhados e lista os componentes que são substituíveis.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente StorSimple, certifique-se de que revê as [convenções](#safety-icon-conventions) de ícones de segurança e [outras precauções de segurança](storsimple-8000-safety.md).

### <a name="safety-icon-conventions"></a>Convenções de ícones de segurança

A tabela seguinte descreve os ícones de segurança utilizados nestes tutoriais. Preste muita atenção a estes ícones de segurança à medida que passa pelos passos para remover e substituir os componentes do dispositivo.

| Ícone | Texto | Informações adicionais |
|:--- |:--- |:--- |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**PERIGO, PERIGO!** |Indica uma situação perigosa que, se não for evitada, resultará em morte ou ferimentos graves. Esta palavra de sinal limita-se às situações mais extremas. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**ATENÇÃO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em morte ou ferimentos graves. |
| ![Ícone de precaução](./media/storsimple-hardware-component-replacement/Caution.png) |**CUIDADO, CUIDADO!** |Indica uma situação perigosa que, se não for evitada, pode resultar em ferimentos ligeiros ou moderados. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**AVISO:** |Indica informações consideradas importantes, mas não relacionadas com riscos. |
| ![Ícone de choque elétrico](./media/storsimple-hardware-component-replacement/Electric.png) |**Risco de choque elétrico** |Indica alta tensão. |
| ![Ícone de peso pesado](./media/storsimple-hardware-component-replacement/Weight.png) |**Peso Pesado** | |
| ![Nenhum ícone de peças reparador do utilizador](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Sem peças de serviço do utilizador** |Não aceda a menos que esteja devidamente treinado. |
| ![Leia o ícone das instruções](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Leia todas as instruções primeiro** | |
| ![Ícone de perigo de ponta](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Risco de ponta** | |

### <a name="before-you-begin"></a>Antes de começar

Familiarize-se com as informações de segurança sobre o seu dispositivo e ícones de segurança utilizados neste tutorial. Vá para [instalar com segurança e opere o seu dispositivo StorSimple](storsimple-8000-safety.md) para obter informações completas. Certifique-se de rever as [precauções de segurança](storsimple-8000-safety.md#handling-precautions) antes de manusear o seu dispositivo StorSimple.

Antes de tentar substituir um componente, considere as seguintes informações.

![Aviso Ícone ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ícone ícone de choque ](./media/storsimple-hardware-component-replacement/Electric.png) **elétrico AVISO!**

* Mova-se corretamente utilizando uma descarga eletrostática ou um tapete antiestático ao manusear módulos e componentes do seu dispositivo StorSimple.
* Não toque em circuitos. Utilize as pegas e guias fornecidos enquanto manusear componentes que possam ter circuitos expostos.

![Aviso ](./media/storsimple-hardware-component-replacement/Warning.png) ![ de aviso ícone ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **aviso AVISO:**

Quando substituir um módulo, **NUNCA deixe uma baía vazia na parte de trás do recinto**. Obtenha um módulo de substituição ou em branco antes de retirar a parte do problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimentos de substituição de componentes de hardware

O seu dispositivo da série StorSimple 8000 é composto por vários módulos plug-in nos compartimentos primário e/ou EBOD. O 8100 tem um único recinto primário, enquanto o 8600 é um dispositivo de recinto duplo com um recinto primário e um recinto EBOD.

Os principais componentes de hardware do seu dispositivo são resumidos nas seguintes tabelas. Clique no link na coluna de **procedimento de substituição** para ir ao tutorial associado.

| Componentes | # Presente | Módulo plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Não |[Substitua o chassis no seu dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores primários |2 |Sim |[Substitua um módulo controlador no seu dispositivo StorSimple](storsimple-8000-controller-replacement.md) |
| Módulos de potência e arrefecimento de 764W (PCMs) |2 |Sim |[Substituir um módulo refrigerador de energia no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Bateria de reserva |2 |Sim |[Substituir um módulo de bateria de reserva no seu dispositivo StorSimple](storsimple-8000-battery-replacement.md) |
| Unidades de disco |12 |Sim |[Substitua uma unidade de disco no seu dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** Componentes de hardware no recinto primário

O recinto primário e o recinto EBOD diferem nos seus módulos de E/S. Além disso, os PCMs têm uma potência diferente. Os PCMs no recinto primário são de 764 W, enquanto os do recinto EBOD são de 580 W. Os PCMs no compartimento primário também contêm um módulo de bateria de reserva.

| Componentes | # Presente | Módulo plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Não |[Substitua o chassis no seu dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores EBOD |2 |Sim |[Substitua um controlador EBOD no seu dispositivo StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| Módulos de potência e arrefecimento de 580W (PCMs) |2 |Sim |[Substituir um módulo refrigerador de energia no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Unidades de disco |12 |Sim |[Substitua uma unidade de disco no seu dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** Componentes de hardware no recinto EBOD

Os módulos plug-in do dispositivo são destacados nos seguintes diagramas frontais e traseiros. Pode utilizar estes diagramas para determinar a localização dos vários módulos plug-in se for necessária uma substituição. O diagrama frontal mostra as unidades do disco, e os diagramas traseiros do recinto EBOD e do recinto primário mostram os módulos plug-in.

![Painel frontal do dispositivo com unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Frente do dispositivo

| Etiqueta | Descrição |
|:--- |:--- |
| 0 - 11 |Unidades de disco (total de 12) |

Tanto o recinto primário como o recinto da EBOD têm módulos porta-unidades. O chassis abriga 12 unidades de disco de 3,5" dispostas num formato 3 por 4.

![Backplane dos módulos de recinto primário do dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Parte de trás do recinto primário

| Etiqueta | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador 0 |
| 4 |Controlador 1 |

![Backplane dos módulos plug-in do recinto EBOD do dispositivo](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Parte de trás do recinto da EBOD

| Etiqueta | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador EBOD 0 |
| 4 |Controlador EBOD 1 |

## <a name="field-replaceable-units"></a>Unidades substituíveis de campo

As seguintes unidades substituíveis de campo (FUs) estão disponíveis para o seu dispositivo StorSimple:

* Chassis (incluindo o painel de operações integrado)
* 764 W AC PCM
* 580 W AC PCM
* Unidade de disco rígido com módulo porta-unidade
* Módulo controlador
* Módulo controlador EBOD
* Módulo de bateria de reserva
* Kit de trilho de montagem de rack

Entre [em contato com o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para encomendar qualquer uma destas unidades de substituição.

## <a name="next-steps"></a>Passos seguintes

Reveja todas as [informações de segurança](storsimple-8000-safety.md) antes de tentar substituir um componente de hardware StorSimple.
