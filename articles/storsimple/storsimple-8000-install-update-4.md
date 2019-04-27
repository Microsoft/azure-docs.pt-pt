---
title: Instalar atualização 4 no dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Explica como instalar os StorSimple 8000 Series Update 4 no seu dispositivo da série StorSimple 8000.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860513"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instalar atualização 4 no dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar a atualização 4 num dispositivo StorSimple em execução uma versão anterior do software através do portal do Azure e usando o método de correção. O método de correção é usado quando um gateway é configurado numa interface de rede que não sejam dados 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão de software de 1 de pré-atualização.

Atualização 4 inclui software de dispositivos, USM firmware, LSI controladores e firmware, Storport e Spaceport, SO atualizações de segurança e um host de outras atualizações de sistema operacional.  O software de dispositivos, USM de firmware, Spaceport, Storport e outras atualizações de sistema operacional são atualizações não disruptivas. As atualizações não disruptivas ou regulares podem ser aplicadas através do portal do Azure ou o método de correção. As atualizações de firmware do disco são atualizações disruptivas e só podem ser aplicadas por meio do método correção através da interface do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Um conjunto de verificações prévias de manuais e automáticas são realizadas antes da instalação para determinar o estado de funcionamento do dispositivo em termos de conectividade de rede e estado de hardware. Estas verificações prévias são executadas apenas se aplicar as atualizações do portal do Azure.
> * Recomendamos que instale o software e outras atualizações regulares através do portal do Azure. Deve multiplicar apenas a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização gateway falhar no portal. Consoante a versão que está a atualizar a partir, as atualizações poderão demorar quatro horas (ou superior) para instalar. As atualizações de modo de manutenção também tem de ser instaladas através da interface do Windows PowerShell do dispositivo. Como as atualizações de modo de manutenção são atualizações disruptivas, isso resultará em indisponíveis para o seu dispositivo.
> * Se executar o Snapshot Manager do StorSimple opcional, certifique-se de que atualizar sua versão do Snapshot Manager para a atualização 4 antes da atualização do dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalar atualização 4 através do portal do Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualização 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft transmite informações de diagnóstico adicionais do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipados recolher informações do dispositivo e diagnosticar problemas. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Certifique-se de que o seu dispositivo está em execução **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. O **última atualização data** também deve ser modificado.

* Agora verá que as atualizações de modo de manutenção estão disponíveis (esta mensagem poderá continuar a ser apresentado até 24 horas depois de instalar as atualizações). Atualizações do modo de manutenção são atualizações disruptivas que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface do Windows PowerShell do seu dispositivo.

* Transferir as atualizações de modo de manutenção, utilizando os passos listados na [para transferir correções](#to-download-hotfixes) para procurar e transferir o KB4011837, as atualizações de firmware do disco de instalações (as outras atualizações já deverá estar instaladas agora). Siga os passos listados na [instalar e verificar correções do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar o modo de manutenção de atualizações.

## <a name="install-update-4-as-a-hotfix"></a>Instalar atualização 4 como uma correção
O método recomendado para instalar a atualização 4 é através do portal do Azure.

Utilize este procedimento se falhar a verificação de gateway, quando tentar instalar as atualizações através do portal do Azure. A verificação falha à medida que tem um gateway atribuído a uma interface de rede 0 que não sejam de dados e o dispositivo está a executar uma versão anterior à atualização 1 do software.

As versões de software que podem ser atualizadas usando o método de correção são:

* Atualização 0.1, 0,2, 0.3
* Atualizar 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1


O método de correção envolve os seguintes três passos:

1. Baixe as correções do catálogo Microsoft Update.
2. Instalar e verificar correções do modo normal.
3. Instalar e verificar a correção do modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Transferir atualizações para o seu dispositivo

Tem de transferir e instalar as seguintes correções na ordem determinada e as pastas sugeridas:

| Encomenda | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar numa pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Atualização de software |Normal <br></br>Não disruptivas |~ 25 minutos |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Driver de LSI e atualizações de firmware <br> Atualização de firmware USM (versão 3.38) |Normal <br></br>Não disruptivas |~ 3 horas <br> (inclui 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pacote de atualizações de segurança do SO <br> Baixe o Windows Server 2012 R2 |Normal <br></br>Não disruptivas |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Baixe o Windows Server 2012 R2 |Normal <br></br>Não disruptivas |- |SecondOrderUpdate|

Também poderá instalar atualizações de firmware do disco na parte superior de todas as atualizações mostradas nas tabelas anteriores. Pode verificar se tem as atualizações de firmware do disco ao executar o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, em seguida, não é necessário instalar estas atualizações.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de instalação | Instalar numa pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmware do disco |Manutenção <br></br>Problemáticos |~ 30 minutos | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Este procedimento tem de ser efetuada apenas uma vez para aplicar a atualização 4. Pode utilizar o portal do Azure para aplicar atualizações subsequentes.
> * Se atualizar a partir do Update 3 ou 3.1, o tempo de instalação total está próximo de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores de dispositivo estão online e todos os componentes de hardware estão em bom Estados.

Execute os seguintes passos para transferir e instalar as correções.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [versão de atualização 4](storsimple-update4-release-notes.md).

