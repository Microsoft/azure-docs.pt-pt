---
title: Instalar atualização 5 no dispositivo da série StorSimple 8000 / Microsoft Docs
description: Explica como instalar o StorSimple 8000 Series Update 5 no seu dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: bbac6eade634ffcfdc47ae3d22b32e0bd429b7c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513179"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Instale a Atualização 5 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar o Update 5 num dispositivo StorSimple que executa uma versão de software anterior através do portal Azure e utilizando o método hotfix. O método hotfix é utilizado quando está a tentar instalar o Update 5 num dispositivo que executa as versões pré-Actualização 3. O método hotfix também é utilizado quando um gateway é configurado numa interface de rede diferente do DATA 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão de software pré-Actualização 1.

A atualização 5 inclui software de dispositivos, Storport e Spaceport, atualizações de segurança do SO e atualizações de firmware de disco.  O software do dispositivo, Spaceport, Storport, segurança e outras atualizações de SO são atualizações não disruptivas. As atualizações não disruptivas ou regulares podem ser aplicadas através do portal Azure ou através do método hotfix. As atualizações do firmware do disco são atualizações disruptivas e são aplicadas quando o dispositivo está em modo de manutenção através do método de hotfix utilizando a interface Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * A atualização 5 é uma atualização obrigatória e deve ser instalada imediatamente. Para obter mais informações, consulte [as notas de lançamento do Update 5](storsimple-update5-release-notes.md).
> * Um conjunto de pré-verificações manuais e automáticas são feitos antes da instalação para determinar a saúde do dispositivo em termos de estado de hardware e conectividade de rede. Estas pré-verificações só são realizadas se aplicar as atualizações a partir do portal Azure.
> * Recomendamos vivamente que ao atualizar um dispositivo que executa versões antes do Update 3, instale as atualizações utilizando o método hotfix. Se encontrar algum problema, [faça um registo de um bilhete de apoio](storsimple-8000-contact-microsoft-support.md).
> * Recomendamos que instale o software e outras atualizações regulares através do portal Azure. Só deverá ir à interface Do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway pré-actualização falhar no portal. Dependendo da versão a partir da versão a partir daí, as atualizações podem demorar 4 horas (ou mais) a instalar. As atualizações do modo de manutenção devem ser instaladas através da interface Windows PowerShell do dispositivo. Como as atualizações do modo de manutenção são atualizações disruptivas, estas resultam num tempo de mente para o seu dispositivo.
> * Se executar o Gestor Instantâneo StorSimple opcional, certifique-se de que atualizou a sua versão Snapshot Manager para Atualizar 5 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Instalar atualização 5 através do portal Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualizar 5](storsimple-update5-release-notes.md).

> [!NOTE]
> A Microsoft retira informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão com problemas, estamos mais bem equipados para recolher informações do dispositivo e diagnosticar problemas.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Verifique se o seu dispositivo está a executar **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. A **última data atualizada** deve ser alterada.

Verá agora que as atualizações do modo manutenção estão disponíveis (esta mensagem pode continuar a ser exibida até 24 horas após a instalação das atualizações). Os passos para instalar a atualização do modo de manutenção são detalhados na secção seguinte.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Instale a Atualização 5 como um hotfix

As versões de software que podem ser atualizadas utilizando o método hotfix são:

* Atualização 0.1, 0.2, 0.3
* Atualização 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1
* Atualização 4

> [!NOTE] 
> O método recomendado para instalar o Update 5 é através do portal Azure quando se tenta atualizar a partir da versão Update 3 e posterior. Ao atualizar um dispositivo em execução versões antes do Update 3, utilize este procedimento. Também pode utilizar este procedimento se falhar na verificação de gateway ao tentar instalar as atualizações através do portal Azure. A verificação falha quando tem um gateway atribuído a uma interface de rede não-DATA 0 e o seu dispositivo está a executar uma versão de software mais cedo do que o Update 1.

O método hotfix envolve os três passos seguintes:

1. Descarregue os hotfixes do Catálogo de Atualizações da Microsoft.
2. Instale e verifique os hotfixes do modo regular.
3. Instale e verifique o hotfix do modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Baixar atualizações para o seu dispositivo

Tem de descarregar e instalar os seguintes hotfixes na ordem prescrita e nas pastas sugeridas:

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Atualização de software<br> Descarregue tanto _HcsSoftwareUpdate.exe_ como _CisMSDAgent.exe_ |Normal <br></br>Não-disruptivo |~ 25 minutos |FirstOrderUpdate|

Se atualizar a partir de um dispositivo em execução Update 4, só precisa de instalar as atualizações cumulativas do SISTEMA como atualizações de segunda ordem.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Pacote de atualizações cumulativas do OS <br> Baixar versão R2 do Windows Server 2012 |Normal <br></br>Não-disruptivo |- |Segunda Encomenda|

Se instalar a partir de um dispositivo em execução a Atualização 3 ou mais cedo, instale o seguinte para além das atualizações cumulativas.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Atualizações de condução e firmware LSI <br> Atualização de firmware USM (versão 3.38) |Normal <br></br>Não-disruptivo |~ 3 horas <br> (inclui 2A. + 2B. + 2C.)|Segunda Encomenda|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Pacote de atualizações de segurança do SO <br> Baixar versão R2 do Windows Server 2012 |Normal <br></br>Não-disruptivo |- |Segunda Encomenda|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Baixar versão R2 do Windows Server 2012 |Normal <br></br>Não-disruptivo |- |Segunda Encomenda|


Pode também ser necessário instalar atualizações de firmware de disco em cima de todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se necessita das atualizações do firmware do disco executando o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGJ` , , , , `XGEG` `KZ50` `F6C2` `VR08` `N003` `0107` então não precisa de instalar estas atualizações.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação | Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Firmware de disco |Manutenção <br></br>Disruptivo |~ 30 minutos | Terceiro Acordo |

<br></br>

> [!IMPORTANT]
> * Se atualizar a partir da Atualização 4, o tempo total de instalação é de cerca de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores do dispositivo estão online e todos os componentes de hardware estão saudáveis.

Execute os seguintes passos para descarregar e instalar os hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o lançamento da [Atualização 5](storsimple-update5-release-notes.md).

