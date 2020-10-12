---
title: Instalar atualização 4 no dispositivo da série StorSimple 8000 / Microsoft Docs
description: Explica como instalar o StorSimple 8000 Series Update 4 no seu dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: ed6f9d58c5c54c88acf8e3a0e7fda7d2d65b8637
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514321"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Instale a Atualização 4 no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este tutorial explica como instalar o Update 4 num dispositivo StorSimple que executa uma versão de software anterior através do portal Azure e utilizando o método hotfix. O método hotfix é utilizado quando um gateway é configurado numa interface de rede diferente do DATA 0 do dispositivo StorSimple e está a tentar atualizar a partir de uma versão de software pré-Actualização 1.

A atualização 4 inclui software de dispositivos, firmware USM, controlador LSI e firmware, Storport e Spaceport, atualizações de segurança do SO e uma série de outras atualizações de SO.  O software do dispositivo, firmware USM, Spaceport, Storport e outras atualizações de SISTEMA são atualizações não disruptivas. As atualizações não disruptivas ou regulares podem ser aplicadas através do portal Azure ou através do método hotfix. As atualizações do firmware do disco são atualizações disruptivas e só podem ser aplicadas através do método hotfix utilizando a interface Windows PowerShell do dispositivo.

> [!IMPORTANT]
> * Um conjunto de pré-verificações manuais e automáticas são feitos antes da instalação para determinar a saúde do dispositivo em termos de estado de hardware e conectividade de rede. Estas pré-verificações só são realizadas se aplicar as atualizações a partir do portal Azure.
> * Recomendamos que instale o software e outras atualizações regulares através do portal Azure. Só deverá ir à interface Do Windows PowerShell do dispositivo (para instalar atualizações) se a verificação de gateway pré-actualização falhar no portal. Dependendo da versão a partir da versão a partir daí, as atualizações podem demorar 4 horas (ou mais) a instalar. As atualizações do modo de manutenção também devem ser instaladas através da interface Windows PowerShell do dispositivo. Uma vez que as atualizações do modo de manutenção são atualizações disruptivas, estas resultarão num tempo de baixa para o seu dispositivo.
> * Se executar o Gestor Instantâneo StorSimple opcional, certifique-se de que atualizou a sua versão Snapshot Manager para Atualizar 4 antes de atualizar o dispositivo.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Instalar atualização 4 através do portal Azure
Execute os seguintes passos para atualizar o seu dispositivo para [atualizar 4](storsimple-update4-release-notes.md).

> [!NOTE]
> A Microsoft retira informações adicionais de diagnóstico do dispositivo. Como resultado, quando a nossa equipa de operações identifica dispositivos que estão com problemas, estamos mais bem equipados para recolher informações do dispositivo e diagnosticar problemas. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Verifique se o seu dispositivo está a executar **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. A **última data atualizada** também deve ser alterada.

* Verá agora que as atualizações do modo manutenção estão disponíveis (esta mensagem pode continuar a ser exibida até 24 horas após a instalação das atualizações). As atualizações do modo de manutenção são atualizações disruptivas que resultam em tempo de inatividade do dispositivo e só podem ser aplicadas através da interface Windows PowerShell do seu dispositivo.

* Descarregue as atualizações do modo de manutenção utilizando os passos [listados para descarregar hotfixes](#to-download-hotfixes) para procurar e descarregar KB4011837, que instala atualizações de firmware de disco (as outras atualizações já devem estar instaladas). Siga os passos indicados na [instalação e verifique os hotfixes do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfixes) para instalar as atualizações do modo de manutenção.

## <a name="install-update-4-as-a-hotfix"></a>Instale a Atualização 4 como um hotfix
O método recomendado para instalar o Update 4 é através do portal Azure.

Utilize este procedimento se falhar na verificação do gateway ao tentar instalar as atualizações através do portal Azure. A verificação falha uma vez que tem um gateway atribuído a uma interface de rede não-DATA 0 e o seu dispositivo está a executar uma versão de software antes do Update 1.

As versões de software que podem ser atualizadas utilizando o método hotfix são:

* Atualização 0.1, 0.2, 0.3
* Atualização 1, 1.1, 1.2
* Atualização 2, 2.1, 2.2
* Atualização 3, 3.1


O método hotfix envolve os três passos seguintes:

1. Descarregue os hotfixes do Catálogo de Atualizações da Microsoft.
2. Instale e verifique os hotfixes do modo regular.
3. Instale e verifique o hotfix do modo de manutenção.

#### <a name="download-updates-for-your-device"></a>Baixar atualizações para o seu dispositivo

Tem de descarregar e instalar os seguintes hotfixes na ordem prescrita e nas pastas sugeridas:

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação |Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Atualização de software |Normal <br></br>Não-disruptivo |~ 25 minutos |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Atualizações de condução e firmware LSI <br> Atualização de firmware USM (versão 3.38) |Normal <br></br>Não-disruptivo |~ 3 horas <br> (inclui 2A. + 2B. + 2C.)|Segunda Encomenda|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Pacote de atualizações de segurança do SO <br> Baixar Windows Server 2012 R2 |Normal <br></br>Não-disruptivo |- |Segunda Encomenda|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Pacote de atualizações do SO <br> Baixar Windows Server 2012 R2 |Normal <br></br>Não-disruptivo |- |Segunda Encomenda|

Pode também ser necessário instalar atualizações de firmware de disco em cima de todas as atualizações apresentadas nas tabelas anteriores. Pode verificar se necessita das atualizações do firmware do disco executando o `Get-HcsFirmwareVersion` cmdlet. Se estiver a executar estas versões de firmware: `XMGJ` , , , , `XGEG` `KZ50` `F6C2` `VR08` `N002` `0106` então não precisa de instalar estas atualizações.

| Encomenda | KB | Descrição | Tipo de atualização | Hora de Instalação | Instalar na pasta|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Firmware de disco |Manutenção <br></br>Disruptivo |~ 30 minutos | Terceiro Acordo |

<br></br>

> [!IMPORTANT]
> * Este procedimento só deve ser realizado uma vez para aplicar a Atualização 4. Pode utilizar o portal Azure para aplicar atualizações subsequentes.
> * Se atualizar a partir da Atualização 3 ou 3.1, o tempo total de instalação é de cerca de 4 horas.
> * Antes de utilizar este procedimento para aplicar a atualização, certifique-se de que ambos os controladores do dispositivo estão online e todos os componentes de hardware estão saudáveis.

Execute os seguintes passos para descarregar e instalar os hotfixes.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o lançamento da [Atualização 4](storsimple-update4-release-notes.md).

