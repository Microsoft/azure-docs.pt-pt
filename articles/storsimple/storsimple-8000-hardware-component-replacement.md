---
title: Substituição de componente de hardware de série 8000 do StorSimple | Documentos da Microsoft
description: Descreve como substituir com segurança o PCMs, bateria, módulos de controlador, EBOD controladores, unidades de disco e chassis de um dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: e05a37122647d4979089f0ba00b1fc15f9b84b0f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433659"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Substituir um componente de hardware no seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
Os tutoriais de substituição de componente de hardware descrevem os componentes de hardware do seu dispositivo da série StorSimple 8000 do Microsoft Azure e as etapas necessárias para remover e substituí-los. Este artigo descreve os ícones de segurança, fornece indicadores para os tutoriais detalhados e indica os componentes que sejam substituíveis.

> [!IMPORTANT]
> Antes de tentar remover ou substituir qualquer componente do StorSimple, certifique-se de que revê os [convenções do ícone de segurança](#safety-icon-conventions) e outras [precauções de segurança](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Convenções de ícone de segurança
A tabela seguinte descreve os ícones de segurança utilizados nestes tutoriais. Preste muita atenção a esses ícones de segurança à medida que revê os passos para remover e substituir componentes de dispositivo.

| Ícone | Texto | Informações adicionais |
|:--- |:--- |:--- |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**PERIGO!** |Indica uma situação perigosa que, se não evitado, resultará em morte ou grave tendão. Esta palavra de sinal está limitada as situações mais graves. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) |**AVISO!** |Indica uma situação perigosa que, se não evitado, poderá resultar em morte ou grave tendão. |
| ![Ícone de atenção](./media/storsimple-hardware-component-replacement/Caution.png) |**ATENÇÃO!** |Indica uma situação perigosa que, se não evitado, poderá resultar em lesão moderada ou pouco frequentes. |
| ![Ícone de aviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**AVISO:** |Indica informações consideradas importantes, mas não relacionados com o perigo. |
| ![Ícone de um choque elétrico](./media/storsimple-hardware-component-replacement/Electric.png) |**Perigo choque elétrico** |Indica a tensão elevada. |
| ![Ícone de peso pesadas](./media/storsimple-hardware-component-replacement/Weight.png) |**Peso pesado** | |
| ![Nenhum ícone de partes serviceable do utilizador](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Não existem partes serviceable do utilizador** |Não acedam a não ser devidamente treinados. |
| ![Ícone de instruções de leitura](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Ler todas as instruções em primeiro lugar** | |
| ![Ícone de perigo de sugestão](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Sugestão de perigo** | |

### <a name="before-you-begin"></a>Antes de começar
Familiarize-se com as informações de segurança sobre seus ícones de dispositivo e de segurança utilizado neste tutorial. Aceda a [com segurança, instalar e operar o dispositivo StorSimple](storsimple-safety.md) para obter informações completas. Certifique-se de que reveja os [precauções de segurança](storsimple-safety.md#handling-precautions) antes de processar o seu dispositivo StorSimple.

Antes de tentar substituir um componente, considere as seguintes informações.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![ícone choque Elétrico](./media/storsimple-hardware-component-replacement/Electric.png) **aviso!**

* A partir do zero sozinho corretamente utilizando um mecânico altas ou antistatic mat quando o processamento de módulos e componentes do dispositivo StorSimple.
* Não é utilizada qualquer conjunto de circuitos. Utilize os guias e identificadores fornecidos durante o processamento de componentes que podem ter expostas circuitos.

![Ícone de aviso](./media/storsimple-hardware-component-replacement/Warning.png) ![observe o ícone](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **aviso:**

Se substituir um módulo **nunca deixam uma distância vazia no traseiro do bastidor**. Obtenha uma substituição ou o módulo em branco antes de remover a parte do problema.

## <a name="hardware-component-replacement-procedures"></a>Procedimentos de substituição de componente de hardware
O dispositivo da série StorSimple 8000 consiste em vários módulos de plug-ins no principal e/ou os bastidores de EBOD. O 8100 tem um bastidor principal único, ao passo que o 8600 é um dispositivo de bastidor dupla com um bastidor principal e um bastidor EBOD.

Os componentes de hardware principal no seu dispositivo estão resumidos nas tabelas seguintes. Clique na ligação no **procedimento de substituição** coluna para ir para o tutorial associado.

| Componentes | # Presente | Módulo de plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Não |[Substituir o chassis no dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores de principal |2 |Sim |[Substituir um módulo de controlador do seu dispositivo StorSimple](storsimple-8000-controller-replacement.md) |
| 764W energia e resfriamento de módulos (PCMs) |2 |Sim |[Substituir um módulo refrigerador de energia no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Bateria de segurança |2 |Sim |[Substituir um módulo de bateria de reserva no seu dispositivo StorSimple](storsimple-8000-battery-replacement.md) |
| Unidades de disco |12 |Sim |[Substituir uma unidade de disco no dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 1** componentes de Hardware o bastidor principal

O bastidor principal e a inclusão EBOD diferem em seus módulos de e/s. Além disso, os PCMs tem wattage diferente. PCMs no bastidor principal são 764 W, ao passo que são aqueles em que o bastidor EBOD 580 W. PCMs no bastidor principal também contenham um módulo de bateria de segurança.

| Componentes | # Presente | Módulo de plug-in? | Procedimento de substituição |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Não |[Substituir o chassis no dispositivo StorSimple](storsimple-8000-chassis-replacement.md) |
| Controladores de EBOD |2 |Sim |[Substituir um controlador EBOD no dispositivo StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W energia e resfriamento de módulos (PCMs) |2 |Sim |[Substituir um módulo refrigerador de energia no seu dispositivo StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Unidades de disco |12 |Sim |[Substituir uma unidade de disco no dispositivo StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabela 2** componentes de Hardware a inclusão EBOD

Os módulos de plug-ins no dispositivo estão realçados na frente seguinte e diagramas rear. Pode usar esses diagramas para determinar a localização de vários módulos de plug-ins se uma substituição é necessária. O front-diagrama mostra as unidades de disco e os diagramas rear do bastidor EBOD e mostrar o bastidor principal os módulos de plug-ins.

![Painel frontal de dispositivo com unidades de disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** frontal do dispositivo

| Label | Descrição |
|:--- |:--- |
| 0 - 11 |Unidades de disco (total de 12) |

O bastidor principal e a inclusão EBOD têm módulos de operadora de unidade. O chassis hospeda doze 3,5" unidades de disco organizadas num formato de 3 a 4.

![Backplane dos módulos de bastidor principal do dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** verso o bastidor principal

| Label | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador 0 |
| 4 |Controlador 1 |

![Backplane dos módulos de plug-ins do bastidor EBOD do dispositivo](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** verso o bastidor EBOD

| Label | Descrição |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controlador 0 de EBOD |
| 4 |1 o controlador de EBOD |

## <a name="field-replaceable-units"></a>Unidades de substituível em campo
As seguintes unidades de substituível em campo (FRUs) estão disponíveis para o dispositivo StorSimple:

* Chassis (incluindo o painel de operações integradas)
* 764 W AC PCM
* 580 W AC PCM
* Unidade de disco rígido com o módulo de deteção de carrier da unidade
* Módulo de controlador
* Módulo de controlador EBOD
* Módulo de bateria de segurança
* Bastidor montar o kit do rail

Volte [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para encomendar a qualquer um destas unidades de substituição.

## <a name="next-steps"></a>Passos Seguintes
Reveja todas [informações de segurança](storsimple-safety.md) antes de tentar substituir um componente de hardware do StorSimple.

