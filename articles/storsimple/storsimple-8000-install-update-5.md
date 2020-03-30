---
title: Instale a Atualização 5 no dispositivo da série StorSimple 8000 [ StorSimple 8000] Microsoft Docs
description: Explica como instalar o StorSimple 8000 Series Update 5 no seu dispositivo da série StorSimple 8000.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267888"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instale a Atualização 5 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar o Update 5 num dispositivo StorSimple que executa uma versão anterior do software através do portal Azure e utilizando o método hotfix. O método de correção de hotéis é utilizado quando está a tentar instalar o Update 5 num dispositivo que executa as versões pré-Actualização 3. O método hotfix também é usado quando um gateway é configurado numa interface de rede diferente do DATA 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão de software pré-Actualização 1.

A atualização 5 inclui software de dispositivos, Storport e Spaceport, atualizações de segurança do OS e atualizações de OS e atualizações de firmware de disco.  O software do dispositivo, Spaceport, Storport, segurança e outras atualizações de SO são atualizações não disruptivas. As atualizações não disruptivas ou regulares podem ser aplicadas através do portal Azure ou através do método hotfix. As atualizações do firmware do disco são atualizações disruptivas e são aplicadas quando o dispositivo está em modo de manutenção através do método de fixação de hotfix utilizando a interface Do Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * A atualização 5 é uma atualização obrigatória e deve ser instalada imediatamente. Para mais informações, consulte [as notas de lançamento do Update 5](storsimple-update5-release-notes.md).
> * Um conjunto de pré-verificações manuais e automáticas são feitos antes da instalação para determinar a saúde do dispositivo em termos de estado de hardware e conectividade de rede. Estas pré-verificações só são realizadas se aplicar as atualizações do portal Azure.
> * Recomendamos vivamente que, ao atualizar as versões de execução de um dispositivo antes do Update 3, instale as atualizações utilizando o método hotfix. Se encontrar algum problema, [faça loga num bilhete](storsimple-8000-contact-microsoft-support.md)de apoio .
> * Recomendamos que instale o software e outras atualizações regulares através do portal Azure. Só deverá ir à interface do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway pré-actualização falhar no portal. Dependendo da versão a partir da sua atualização, as atualizações podem demorar 4 horas (ou mais) a instalar. As atualizações do modo de manutenção devem ser instaladas através da interface Do Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações disruptivas, estas resultam num tempo de paragem para o seu dispositivo.
> * Se executar o StorSimple Snapshot Manager opcional, certifique-se de que atualizou a versão do Photo Manager para O Update 5 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalar Atualização 5 através do portal Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualizar 5](storsimple-update5-release-notes.md).

> [!NOTE]
> A Microsoft retira informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão com problemas, estamos mais bem equipados para recolher informações do dispositivo e diagnosticar problemas.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Verifique se o seu dispositivo está a executar **storSimple 8000 Series Update 5 (6.3.9600.17845)**. A **última data atualizada** deve ser modificada.

Verá agora que as atualizações do modo de manutenção estão disponíveis (esta mensagem pode continuar a ser exibida até 24 horas após a instalação das atualizações). As etapas para instalar a atualização do modo de manutenção são detalhadas na secção seguinte.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instale a Atualização 5 como um hotfix

As versões de software que podem ser atualizadas utilizando o método hotfix são:

* Atualização 0.1, 0.2, 0.3
* Atualização 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1
* Atualização 4

> [!NOTE] 
> O método recomendado para instalar o Update 5 é através do portal Azure ao tentar atualizar a partir do Update 3 e posterior versão. Ao atualizar as versões de execução de um dispositivo antes da Atualização 3, utilize este procedimento. Também pode utilizar este procedimento se falhar a verificação de gateway ao tentar instalar as atualizações através do portal Azure. A verificação falha quando tem um portal atribuído a uma interface de rede não DATA 0 e o seu dispositivo está a executar uma versão de software mais cedo do que o Update 1.

O método de fixação de calor envolve os seguintes três passos:

1. Descarregue os hotfixes do Catálogo de Atualizações da Microsoft.
2. Instale e verifique os hotfixes de modo regular.
3. Instale e verifique a correção do modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Descarregue atualizações para o seu dispositivo

Deve descarregar e instalar os seguintes hotfixes na ordem prescrita e nas pastas sugeridas:

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Atualização de software<br> Baixe tanto _hcsSoftwareUpdate.exe_ e _CisMSDAgent.exe_ |Normal <br></br>Não disruptivo |~ 25 minutos |FirstOrderUpdate|

Se atualizar a partir de um dispositivo que executa o Update 4, apenas precisa de instalar as atualizações cumulativas do OS como atualizações de segunda ordem.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Pacote de atualizações cumulativas do OS <br> Baixar versão R2 do Windows Server 2012 |Normal <br></br>Não disruptivo |- |SecondOrderUpdate|

Se instalar a partir de um dispositivo que executa o Update 3 ou mais cedo, instale o seguinte para além das atualizações cumulativas.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB40111842 |Atualizações de controlador e firmware LSI <br> Atualização de firmware USM (versão 3.38) |Normal <br></br>Não disruptivo |~ 3 horas <br> (inclui 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB31746444 <br> KB3139914   |Pacote de atualizações de segurança do OS <br> Baixar versão R2 do Windows Server 2012 |Normal <br></br>Não disruptivo |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pacote de atualizações do OS <br> Baixar versão R2 do Windows Server 2012 |Normal <br></br>Não disruptivo |- |SecondOrderUpdate|


Também poderá ser necessário instalar atualizações de firmware de disco para além de todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se precisa das atualizações do firmware do disco executando o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas `XMGJ`versões de `N003` `0107`firmware, `XGEG` `KZ50` `F6C2`não `VR08`precisa de instalar estas atualizações.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação | Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware de disco |Manutenção <br></br>Disruptivo |~ 30 minutos | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Se atualizar a partir do Update 4, o tempo total de instalação é de cerca de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores do dispositivo estão on-line e todos os componentes do hardware estão saudáveis.

Execute os seguintes passos para descarregar e instalar os hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o lançamento da [Atualização 5](storsimple-update5-release-notes.md).

