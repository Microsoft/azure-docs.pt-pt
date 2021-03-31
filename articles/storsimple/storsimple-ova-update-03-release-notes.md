---
title: StorSimple Virtual Array Update 0.3 notas de lançamento
description: Descreve problemas e resoluções abertos críticos para o StorSimple Virtual Array que executa a Atualização 0.3.
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
ms.openlocfilehash: 8301b45fe778bd3df7fc665db2662ba81d3e644f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966110"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0.3 notas de lançamento
## <a name="overview"></a>Descrição Geral
As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.3 corresponde à versão do software **10.0.10288.0**.

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se a I/O estiver em andamento, o dispositivo incorre em tempo de inatividade.
> 
> 

## <a name="whats-new-in-the-update-03"></a>Novidades na Atualização 0.3
A atualização 0.3 é principalmente uma construção de correção de erros. Nesta versão foram abordados vários bugs que resultam em falhas de backup na versão anterior.

## <a name="issues-fixed-in-the-update-03"></a>Problemas fixados na Atualização 0.3
O quadro seguinte fornece um resumo das questões corrigidas nesta versão.

| N.º | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Cópias de segurança |Um problema foi visto no lançamento anterior, onde os backups não seriam concluídos para uma partilha de ficheiros. Se esta questão ocorresse, o trabalho de backup falharia e foi levantado um alerta crítico no serviço StorSimple Manager para notificar o utilizador. Esta questão não afetou os dados sobre as ações nem o acesso aos dados. A causa principal foi identificada e corrigida nesta libertação. <br></br> A correção não se aplica retroativamente às ações que já estão a ver esta questão. Os clientes que estão a ver este problema devem primeiro aplicar o Update 0.3 e, em seguida, contactar o Microsoft Support para executar uma cópia de segurança completa do sistema para corrigir o problema. Em vez de contactar o Microsoft Support, os clientes também podem restaurar uma nova parte de uma cópia de segurança saudável para as ações afetadas. |
| 2 |iSCSI |Um problema foi visto na versão anterior onde os volumes desapareceriam ao copiar dados para um volume no StorSimple Virtual Array. Esta questão foi corrigida nesta versão. <br></br> As correções só têm efeito nos volumes recém-criados. As correções não se aplicam retroativamente aos volumes que já estão a ver esta questão. Os clientes são aconselhados a trazer os volumes afetados on-line através do portal clássico Azure, realizar uma cópia de segurança para estes volumes e, em seguida, restaurar estes volumes para novos volumes. |

## <a name="known-issues-in-the-update-03"></a>Emissões conhecidas na Atualização 0.3
A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir das versões anteriores. 

| N.º | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados na versão de pré-visualização não podem ser atualizados para uma versão suportada da Disponibilidade Geral. |Estes dispositivos virtuais devem ser falhados para a libertação de Disponibilidade Geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Uma vez que tenha fornecedo um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, não deve expandir ou encolher o disco de dados. Tentar fazer resulta numa perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, a aplicação de uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e que não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança reforçadas estiverem ativadas no Internet Explorer (IE ESC), algumas páginas locais de UI web, tais como Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são apresentadas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hiper-V. O Hiper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |O bloqueio de alcance byte para aplicações que trabalhem com os volumes tiered StorSimple não é suportado. Se o bloqueio de alcance byte estiver ativado, o tiering StorSimple não funciona. |As medidas recomendadas incluem: <br></br>Desligue o bloqueio do alcance do byte na lógica da aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição a volumes hierárquicos.<br></br>*Caveat*: Quando se utilizam volumes fixados localmente e o bloqueio de alcance byte está ativado, o volume fixado localmente pode estar on-line mesmo antes da restauração estar completa. Nestes casos, se uma restauração está em curso, então deve esperar que a restauração esteja concluída. |
| **7.** |Ações hierárquis |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações quando não há dados sobre a parte. Isto porque a capacidade utilizada para ações inclui metadados. | |
| **9.** |Recuperação após desastre |Só é possível efetuar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo alvo noutro domínio não é suportada nesta versão. |Isto é implementado num lançamento posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal clássico Azure e da UI web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual apenas aceita a entrada no formato de teclado en-US. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, precisa de desligar os volumes e, em seguida, trazê-los on-line para que a mudança produza efeitos. |Esta questão é abordada numa versão posterior. |
| **13.** |servidor iSCSI |O "armazenamento usado" exibido para um volume iSCSI pode ser diferente no serviço StorSimple Manager e no anfitrião iSCSI. |O anfitrião iSCSI tem a vista do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível de item. | |

## <a name="next-step"></a>Passo seguinte
[Instale a atualização 0.3](./storsimple-virtual-array-install-update-06.md) no seu Conjunto Virtual StorSimple.

## <a name="references"></a>Referências
À procura de uma nota de lançamento mais antiga? Vá para: 

* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [StorSimple Notas de lançamento de disponibilidade geral de matriz virtual](./storsimple-virtual-array-update-06-release-notes.md)