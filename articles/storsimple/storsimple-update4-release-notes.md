---
title: StorSimple 8000 Series Update 4 notas de lançamento | Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 4.
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954057"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>StorSimple 8000 Series Update 4 notas de lançamento

## <a name="overview"></a>Descrição Geral

As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 4. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão. 

A atualização 4 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar o Release (GA) ou a Atualização 0.1 através da Atualização 3.1. A versão do dispositivo associada ao Update 4 é de 6.3.9600.17820.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A Atualização 4 tem software de dispositivos, firmware USM, controlador LSI e firmware, firmware de disco, Storport e Spaceport, segurança e outras atualizações de SISTEMA. Leva aproximadamente 4 horas para instalar esta atualização. A atualização do firmware em disco é uma atualização disruptiva e resulta num tempo de inatividade para o seu dispositivo. Recomendamos que aplique o Update 4 para manter o seu dispositivo atualizado. 
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente para atualizações, uma vez que estas ficarão disponíveis em breve.

## <a name="whats-new-in-update-4"></a>Novidades na Atualização 4

As seguintes melhorias chave e correções de erros foram feitas no Update 4.

* **Algoritmos de recuperação de espaço automatizados mais inteligentes** – No Update 4, os algoritmos automatizados de recuperação de espaço são melhorados para ajustar os ciclos de recuperação do espaço com base no espaço recuperado esperado disponível na nuvem. 

* **Melhorias de desempenho para volumes fixados localmente** – A atualização 4 melhorou o desempenho de volumes fixados localmente em cenários que têm elevada ingestão de dados (dados comparáveis ao tamanho do volume).

* **Restauro baseado em mapas** de calor - Nos lançamentos anteriores, após uma recuperação de desastres (DR), os dados foram restaurados a partir da nuvem com base nos padrões de acesso, resultando num desempenho lento. 

    Uma nova funcionalidade é implementada no Update 4 que rastreia dados frequentemente acedidos para criar um mapa de calor quando o dispositivo está em uso antes de DR (a maioria dos pedaços de dados usados têm calor elevado, enquanto os pedaços menos utilizados têm baixo calor). Depois de DR, storSimple utiliza o mapa de calor para restaurar e reidratar automaticamente os dados da nuvem. 

    Todos os restauros são agora restauradores à base de mapas de calor. Para obter mais informações sobre como consultar e cancelar trabalhos de restauro e reidratação baseados em mapas térmicos, vá ao [Windows PowerShell para obter referência de cmdlet StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

* **StorSimple Diagnostics tool** – In Update 4, está a ser lançada uma ferramenta StorSimple Diagnostics para permitir um fácil diagnóstico e resolução de problemas de problemas relacionados com a saúde do sistema, rede, desempenho e componente de hardware. Esta ferramenta é executada através do Windows PowerShell para StorSimple. Para obter mais informações, aceda à [resolução de problemas utilizando a ferramenta StorSimple Diagnostics](storsimple-8000-diagnostics.md).

* **Ferramenta de migração StorSimple baseada em UI** - Antes desta versão, a migração de dados da série 5000-7000 exigiu que os utilizadores executassem uma parte do fluxo de trabalho de migração utilizando a interface Azure PowerShell. Nesta versão, é disponibilizada uma ferramenta de migração StorSimple baseada em UI de fácil utilização para o Suporte para facilitar o mesmo fluxo de trabalho de migração. Esta ferramenta também permitiria a consolidação de baldes de recuperação. 

* **Alterações relacionadas com o FIPS** - Esta versão em diante, o FIPS é ativado por padrão em todos os dispositivos da série StorSimple 8000 para o Governo microsoft Azure e contas de nuvem pública Azure.

* **Alterações de atualização** - Nesta versão, foram corrigidos bugs relacionados com falhas de atualização.

* **Alerta para falhas no disco** - Um novo alerta que avisa o utilizador de falhas iminentes do disco é adicionado nesta versão. Se encontrar este alerta, contacte o Microsoft Support para enviar um disco de substituição. Para mais informações, aceda aos [alertas de hardware no seu dispositivo StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Alterações de substituição do controlador** - Um cmdlet que permite ao utilizador consultar o estado do processo de substituição do controlador é adicionado nesta versão. Para obter mais informações, aceda ao [cmdlet para consultar](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps)o estado de substituição do controlador .


## <a name="issues-fixed-in-update-4"></a>Problemas corrigidos na Atualização 4

A tabela seguinte fornece um resumo das questões que foram corrigidas na Atualização 4.    

| No | Destaque | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Ativação pós-falha |No lançamento anterior, após a falha, houve um problema relacionado com a limpeza observada no site do cliente. Esta questão é corrigida nesta versão. |Yes |Yes |
| 2 |volumes afixados localmente |No lançamento anterior, havia uma questão de criação de volume relacionado para volumes fixados localmente que resultaria em falhas de criação de volume. Esta questão foi causada por raízes e corrigida nesta versão. |Yes |No |
| 3 |Pacote de apoio |Em versão anterior, existiam questões relacionadas com o pacote de suporte que resultariam numa exceção System.OutOfMemory ou outros erros que resultaram numa falha de criação de pacotes de suporte. Estes insetos estão fixos nesta versão. |Yes |Yes |
| 4 |Monitorização |No lançamento anterior, existe uma questão relacionada com os gráficos de monitorização dos volumes fixados localmente, onde o consumo foi demonstrado em EB. Este bug é resolvido nesta versão. |Yes |Yes |
| 5 |Migração |Em versão anterior, houve várias questões relacionadas com a fiabilidade da migração de 5000-7000 para 8000 dispositivos da série. Estas questões foram abordadas nesta versão. |Yes |Yes |
| 6 |Atualizar |Em versões anteriores, caso houvesse uma falha de atualização, os controladores entrariam em modo de recuperação e, por isso, o utilizador não poderia prosseguir com a atualização e teria de entrar em contacto com o Microsoft Support. <br> Este comportamento foi alterado nesta libertação. Se o utilizador tiver uma falha de atualização depois de ambos os controladores estarem a executar a mesma versão (Atualização 4), os controladores não entram em modo de recuperação. Se o utilizador encontrar esta falha, recomendamos que aguardem um pouco e, em seguida, refaçam a atualização. A repetição pode ter sucesso. Se a repetição falhar, devem contactar o Microsoft Support. |Yes |Yes |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemas conhecidos na Atualização 4 de lançamentos anteriores

Não existem novos problemas conhecidos no Update 4. Para obter uma lista de problemas transitadas para a Atualização 4 de versões anteriores, aceda às [notas de lançamento do Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Atualizações de controlador e firmware SCSI (SAS) anexadas em série na Atualização 4

Esta versão tem o controlador SAS e as atualizações do controlador LSI e do firmware. Para obter mais informações sobre como instalar estas atualizações, consulte [a instalação do Update 4](./storsimple-8000-install-update-4.md) no seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Atualizações de dispositivos virtuais no Update 4

Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). Novos dispositivos virtuais terão de ser criados. 

## <a name="next-step"></a>Passo seguinte

Saiba como instalar o [Update 4](./storsimple-8000-install-update-4.md) no seu dispositivo StorSimple.