---
title: Instalar atualização 5 no dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Explica como instalar os StorSimple 8000 Series Update 5 em seu dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126754"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instalar atualização 5 do seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar o Update 5 num dispositivo StorSimple em execução uma versão anterior do software através do portal do Azure e usando o método de correção. O método de correção é usado quando estiver a tentar instalar atualização 5 num dispositivo com a pré-atualização 3 versões. O método de correção também é utilizado quando um gateway está configurado numa interface de rede que não sejam dados 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão de software de 1 de pré-atualização.

Atualização 5 inclui software de dispositivos, Storport e Spaceport, atualizações de segurança do sistema operacional e as atualizações do SO e atualizações de firmware do disco.  O software de dispositivos, Spaceport, Storport, segurança e outras atualizações de sistema operacional são atualizações não disruptivas. As atualizações não disruptivas ou regulares podem ser aplicadas através do portal do Azure ou o método de correção. As atualizações de firmware do disco são atualizações disruptivas e são aplicadas quando o dispositivo estiver no modo de manutenção por meio do método correção através da interface do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Atualização 5 é uma atualização obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte [notas de versão de atualização 5](storsimple-update5-release-notes.md).
> * Um conjunto de verificações prévias de manuais e automáticas são realizadas antes da instalação para determinar o estado de funcionamento do dispositivo em termos de conectividade de rede e estado de hardware. Estas verificações prévias são executadas apenas se aplicar as atualizações do portal do Azure.
> * Recomendamos vivamente que ao atualizar um dispositivo a executar versões anteriores à atualização 3, instale as atualizações com o método de correção. Se encontrar algum problema [registar um pedido de suporte](storsimple-8000-contact-microsoft-support.md).
> * Recomendamos que instale o software e outras atualizações regulares através do portal do Azure. Deve multiplicar apenas a interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de pré-atualização gateway falhar no portal. Consoante a versão que está a atualizar a partir, as atualizações poderão demorar quatro horas (ou superior) para instalar. As atualizações de modo de manutenção devem ser instaladas através da interface do Windows PowerShell do dispositivo. Como as atualizações de modo de manutenção são atualizações disruptivas, estes resultam em indisponíveis para o seu dispositivo.
> * Se executar o Snapshot Manager do StorSimple opcional, certifique-se de que atualizar sua versão do Snapshot Manager para a atualização 5 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalar atualização 5 através do portal do Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualização 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft transmite informações de diagnóstico adicionais do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipados recolher informações do dispositivo e diagnosticar problemas.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Certifique-se de que o seu dispositivo está em execução **StorSimple 8000 Series Update 5 (6.3.9600.17845)** . O **última atualização data** deve ser modificado.

Agora verá que as atualizações de modo de manutenção estão disponíveis (esta mensagem poderá continuar a ser apresentado até 24 horas depois de instalar as atualizações). Os passos para instalar a atualização do modo de manutenção são detalhados na secção seguinte.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instalar atualização 5 como uma correção

As versões de software que podem ser atualizadas usando o método de correção são:

* Atualização 0.1, 0,2, 0.3
* Atualizar 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1
* Atualização 4

> [!NOTE] 
> O método recomendado para instalar a atualização 5 é através do portal do Azure ao tentar atualizar a partir da atualização 3 e versão posterior. Ao atualizar um dispositivo a executar versões anteriores à atualização 3, utilize este procedimento. Também pode utilizar este procedimento se falhar a verificação de gateway, quando tentar instalar as atualizações através do portal do Azure. A verificação de falha quando tiver um gateway atribuído a uma interface de rede 0 que não sejam de dados e o dispositivo está em execução uma versão do software anteriores ao Update 1.

O método de correção envolve os seguintes três passos:

1. Baixe as correções do catálogo Microsoft Update.
2. Instalar e verificar correções do modo normal.
3. Instalar e verificar a correção do modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Transferir atualizações para o seu dispositivo

Tem de transferir e instalar as seguintes correções na ordem determinada e as pastas sugeridas:

| Encomenda | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar numa pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Atualização de software<br> Transferir ambos _HcsSoftwareUpdate.exe_ e _CisMSDAgent.exe_ |Regular <br></br>Não disruptivas |~ 25 minutos |FirstOrderUpdate|

Se atualizar a partir de um dispositivo a executar a atualização 4, só tem de instalar as atualizações cumulativas do sistema operacional como atualizações de segunda ordem.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar numa pasta|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Pacote de atualizações cumulativas do SO <br> Baixe a versão do Windows Server 2012 R2 |Regular <br></br>Não disruptivas |- |SecondOrderUpdate|

Se instalar a partir de um dispositivo a executar a atualização 3 ou anterior, instale o seguinte para além das atualizações cumulativas.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de instalação |Instalar numa pasta|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Driver de LSI e atualizações de firmware <br> Atualização de firmware USM (versão 3.38) |Regular <br></br>Não disruptivas |~ 3 horas <br> (inclui 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pacote de atualizações de segurança do SO <br> Baixe a versão do Windows Server 2012 R2 |Regular <br></br>Não disruptivas |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Baixe a versão do Windows Server 2012 R2 |Regular <br></br>Não disruptivas |- |SecondOrderUpdate|


Também poderá instalar atualizações de firmware do disco na parte superior de todas as atualizações mostradas nas tabelas anteriores. Pode verificar se tem as atualizações de firmware do disco ao executar o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, em seguida, não é necessário instalar estas atualizações.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de instalação | Instalar numa pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware do disco |Manutenção <br></br>Problemáticos |~ 30 minutos | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Se atualizar a partir do Update 4, o tempo de instalação total está próximo de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que tanto os controladores de dispositivo estão online e todos os componentes de hardware estão em bom Estados.

Execute os seguintes passos para transferir e instalar as correções.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [versão de atualização 5](storsimple-update5-release-notes.md).

