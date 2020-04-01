---
title: Notas de lançamento storSimple Virtual Array Update 0.3
description: Descreve questões e resoluções críticas abertas para o StorSimple Virtual Array executando update 0.3.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: f56c36f18379449409f4989eab9510da1f686d0d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397812"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>Notas de lançamento storSimple Virtual Array Update 0.3
## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.3 corresponde à versão do software **10.0.10288.0**.

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se o I/O estiver em curso, o dispositivo incorre no tempo de inatividade.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Novidades na Atualização 0.3
A atualização 0.3 é principalmente uma construção de correção de erros. Nesta versão, foram abordados vários bugs que resultaram em falhas de backup na versão anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas corrigidos na Atualização 0.3
A tabela que se segue apresenta um resumo das questões fixadas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Cópias de segurança |Um problema foi visto no lançamento anterior onde os backups não seriam concluídos para uma parte de arquivo. Se este problema ocorrer, o trabalho de backup falharia e foi levantado um alerta crítico no serviço StorSimple Manager para notificar o utilizador. Esta emissão não afetou os dados sobre as ações nem o acesso aos dados. A causa principal foi identificada e fixada nesta libertação. <br></br> A correção não se aplica retroativamente às ações que já estão a ver esta questão. Os clientes que estão a ver este problema devem primeiro aplicar o Update 0.3 e, em seguida, contactar o Microsoft Support para efetuar uma cópia de segurança completa do sistema para corrigir o problema. Em vez de contactar o Microsoft Support, os clientes também podem restaurar uma nova parte a partir de uma cópia de segurança saudável para as ações afetadas. |
| 2 |iSCSI |Um problema foi visto no lançamento anterior onde os volumes desapareceriam ao copiar dados para um volume no StorSimple Virtual Array. Esta questão foi corrigida nesta versão. <br></br> As correções só têm efeito em volumes recém-criados. As correções não se aplicam retroativamente aos volumes que já estão a ver esta questão. Os clientes são aconselhados a colocar os volumes afetados online através do portal clássico Azure, realizar uma cópia de segurança para estes volumes e, em seguida, restaurar esses volumes para novos volumes. |

## <a name="known-issues-in-the-update-03"></a>Questões conhecidas na Atualização 0.3
A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir dos lançamentos anteriores. 

| Não. | Funcionalidade | Problema | Seleção/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados no lançamento de pré-visualização não podem ser atualizados para uma versão de Disponibilidade Geral suportada. |Estes dispositivos virtuais devem ser falhados para a libertação de Disponibilidade Geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e ter criado o dispositivo virtual StorSimple correspondente, não deve expandir ou encolher o disco de dados. Tentar fazer resulta numa perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, aplicar uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança melhoradas forem ativadas no Internet Explorer (IE ESC), algumas páginas de UI web locais, como a Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são exibidas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hyper-V. O Hyper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |Não é suportado o bloqueio de gama byte para aplicações que funcionem com os volumes storSimple tiered. Se o bloqueio do alcance byte estiver ativado, o diferencial StorSimple não funciona. |As medidas recomendadas incluem: <br></br>Desligue o alcance do byte na sua lógica de aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição aos volumes hierárquicos.<br></br>*Caveat*: Ao utilizar volumes fixados localmente e o bloqueio de alcance byte está ativado, o volume fixado localmente pode estar on-line mesmo antes de a restauração estar completa. Nesses casos, se houver um restauro em curso, então deve esperar que o restauro esteja concluído. |
| **7.** |Ações hierárquias |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações quando não há dados sobre a parte. Isto porque a capacidade utilizada para ações inclui metadados. | |
| **9.** |Recuperação após desastre |Só é possível realizar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo-alvo noutro domínio não é suportada nesta versão. |Isto é implementado numa versão posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal clássico Azure e da UI web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual só aceita entrada em formato de teclado en-EUA. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, precisa de desligar os volumes e, em seguida, trazê-los online para que a mudança faça efeito. |Esta questão é abordada posteriormente. |
| **13.** |servidor iSCSI |O 'armazenamento usado' apresentado para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no anfitrião iSCSI. |O anfitrião do iSCSI tem a visão do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível do item. | |

## <a name="next-step"></a>Passo seguinte
[Instale a Atualização 0.3](storsimple-ova-install-update-01.md) no seu StorSimple Virtual Array.

## <a name="references"></a>Referências
À procura de uma nota de libertação mais antiga? Vá para: 

* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz virtual StorSimple](storsimple-ova-pp-release-notes.md)

