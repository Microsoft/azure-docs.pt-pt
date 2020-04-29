---
title: Notas de lançamento da Série StorSimple 8000 Update 4 Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções para o StorSimple 8000 Series Update 4.
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
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254589"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Notas de lançamento da Série 4 storSimple 8000

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões críticas em aberto para o StorSimple 8000 Series Update 4. Também contêm uma lista das atualizações de software StorSimple incluídas nesta versão. 

A atualização 4 pode ser aplicada a qualquer dispositivo StorSimple que execute o Lançamento (GA) ou o Update 0.1 através do Update 3.1. A versão do dispositivo associada ao Update 4 é de 6.3.9600.17820.

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * O Update 4 tem software de dispositivos, firmware USM, controlador LSI e firmware, firmware de disco, Storport e Spaceport, segurança e outras atualizações de SO. Demora aproximadamente 4 horas a instalar esta atualização. A atualização do firmware do disco é uma atualização disruptiva e resulta numa paragem para o seu dispositivo. Recomendamos que aplique o Update 4 para manter o seu dispositivo atualizado. 
> * Para novos lançamentos, pode não ver as atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Aguarde alguns dias e, em seguida, procure novamente as atualizações, uma vez que estas estarão disponíveis em breve.

## <a name="whats-new-in-update-4"></a>Novidades na Atualização 4

As seguintes melhorias de teclas e correções de erros foram feitas no Update 4.

* **Algoritmos** de recuperação de espaço automatizados mais inteligentes – No Update 4, os algoritmos automatizados de recuperação de espaço são melhorados para ajustar os ciclos de recuperação do espaço com base no espaço recuperado esperado disponível na nuvem. 

* **Melhorias de desempenho para volumes fixados localmente** – A Atualização 4 melhorou o desempenho de volumes fixados localmente em cenários com elevada ingestão de dados (dados comparáveis ao tamanho do volume).

* **Restauro baseado em mapas de calor** - Nos lançamentos anteriores, após uma recuperação de desastres (DR), os dados foram restaurados da nuvem com base nos padrões de acesso que resultaram num desempenho lento. 

    Uma nova funcionalidade é implementada no Update 4 que rastreia dados frequentemente acedidos para criar um mapa de calor quando o dispositivo está em uso antes de DR (a maioria dos pedaços de dados usados têm calor elevado, enquanto os pedaços menos utilizados têm baixo calor). Depois de DR, o StorSimple utiliza o mapa de calor para restaurar e hidratar automaticamente os dados da nuvem. 

    Todos os restauros são agora restaurados baseados em mapa térmico. Para obter mais informações sobre como consultar e cancelar trabalhos de restauro e reidratação baseados em mapas de calor, vá ao [Windows PowerShell para referência storSimple cmdlet](https://technet.microsoft.com/library/dn688168.aspx).

* **Ferramenta StorSimple Diagnostics** – No Update 4, está a ser lançada uma ferramenta StorSimple Diagnostics para permitir um diagnóstico fácil e resolução de problemas relacionados com a saúde do sistema, rede, desempenho e componente de hardware. Esta ferramenta é executada através do Windows PowerShell para storSimple. Para mais informações, vá a problemas utilizando a [ferramenta StorSimple Diagnostics](storsimple-8000-diagnostics.md).

* **Ferramenta de migração StorSimple** baseada na UI - Antes desta versão, a migração de dados da série 5000-7000 exigiu que os utilizadores executassem uma parte do fluxo de trabalho migratório utilizando a interface Azure PowerShell. Nesta versão, uma ferramenta de migração StorSimple baseada em UI é disponibilizada para suporte para facilitar o mesmo fluxo de trabalho migratório. Esta ferramenta também permitiria a consolidação de baldes de recuperação. 

* **Alterações relacionadas com** o FIPS - Esta versão, O FIPS é ativado por padrão em todos os dispositivos da série StorSimple 8000 tanto para o Governo do Microsoft Azure como para as contas de nuvem pública Azure.

* **Alterações na atualização** - Neste lançamento, foram corrigidos os bugs relacionados com falhas de atualização.

* **Alerta para falhas** no disco - Um novo alerta que avisa o utilizador de falhas iminentes do disco é adicionado nesta versão. Se encontrar este alerta, contacte o Microsoft Support para enviar um disco de substituição. Para mais informações, vá a alertas de [hardware no seu dispositivo StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Alterações** de substituição do controlador - Um cmdlet que permite ao utilizador consultar o estado do processo de substituição do controlador é adicionado nesta versão. Para mais informações, vá ao [cmdlet para consulta o estado](https://technet.microsoft.com/library/dn688168.aspx)de substituição do controlador .


## <a name="issues-fixed-in-update-4"></a>Problemas corrigidos na Atualização 4

A tabela seguinte apresenta um resumo das questões que foram corrigidas na Atualização 4.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- |
| 1 |Ativação pós-falha |No comunicado anterior, após o failover, houve um problema relacionado com a limpeza observada no site do cliente. Esta questão está corrigida nesta versão. |Sim |Sim |
| 2 |volumes afixados localmente |No lançamento anterior, havia uma questão de criação de volume relacionada para volumes fixados localmente que resultariam em falhas de criação de volume. Esta questão foi causada e corrigida nesta libertação. |Sim |Não |
| 3 |Pacote de apoio |No lançamento anterior, existiam problemas relacionados com o pacote de Suporte que resultariam numa exceção system.OutOfMemory ou outros erros que resultaram numa falha na criação de pacotes de suporte. Estes insetos estão fixos nesta libertação. |Sim |Sim |
| 4 |Monitorização |Em versão anterior, existe uma questão relacionada com gráficos de monitorização para volumes fixados localmente onde o consumo foi mostrado no EB. Este bug está resolvido nesta libertação. |Sim |Sim |
| 5 |Migração |Em lançamentos anteriores, houve vários problemas relacionados com a fiabilidade da migração de 5000-7000 dispositivos da série para 8000 dispositivos da série. Estas questões foram abordadas nesta versão. |Sim |Sim |
| 6 |Atualizar |Em lançamentos anteriores, se houvesse uma falha de atualização, os controladores entrariam em modo de recuperação e, portanto, o utilizador não poderia avançar com a atualização e teria de contactar o Microsoft Support. <br> Este comportamento foi alterado nesta libertação. Se o utilizador tiver uma falha de atualização depois de ambos os controladores estarem a executar a mesma versão (Update 4), os controladores não entram no modo de recuperação. Se o utilizador encontrar esta falha, recomendamos que aguardem um pouco e, em seguida, voltem a tentar a atualização. A retentativa pode ter sucesso. Se a retentativa falhar, deverá contactar o Microsoft Support. |Sim |Sim |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problemas conhecidos no Update 4 de lançamentos anteriores

Não há novos problemas conhecidos na Atualização 4. Para obter uma lista de problemas transportados para a Atualização 4 a partir de lançamentos anteriores, vá a notas de [lançamento do Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>As atualizações do controlador SCSI (SAS) e do firmware em série na Atualização 4

Esta versão tem o controlador SAS e as atualizações de controlador LSI e firmware. Para obter mais informações sobre como instalar estas atualizações, consulte [a instalação do Update 4](storsimple-install-update-4.md) no seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Atualizações de dispositivos virtuais no Update 4

Esta atualização não pode ser aplicada ao StorSimple Cloud Appliance (também conhecido como dispositivo virtual). Serão necessários novos dispositivos virtuais. 

## <a name="next-step"></a>Passo seguinte

Saiba como instalar o [Update 4](storsimple-install-update-4.md) no seu dispositivo StorSimple.

