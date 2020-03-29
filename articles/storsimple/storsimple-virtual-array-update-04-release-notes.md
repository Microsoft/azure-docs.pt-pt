---
title: Notas de lançamento do StorSimple Virtual Array Update 0.4 Microsoft Docs
description: Descreve questões e resoluções críticas abertas para o StorSimple Virtual Array executando update 0.4.
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
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 06a3469507631d032535bce62b01d964e99dc603
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60334799"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Notas de lançamento storSimple Virtual Array Update 0.4

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.4 corresponde à versão do software **10.0.10289.0**.

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se o I/O estiver em curso, o dispositivo incorre no tempo de inatividade.


## <a name="whats-new-in-the-update-04"></a>Novidades na Atualização 0.4
A atualização 0.4 é principalmente uma construção de correção de bugs juntamente com algumas melhorias. Nesta versão, foram abordados vários bugs que resultaram em falhas de backup na versão anterior. As principais melhorias e correções de erros são as seguintes:

- **Melhorias** no desempenho de backup - Esta versão fez várias melhorias chave para melhorar o desempenho da cópia de segurança. Como resultado, as cópias de segurança que envolvem um grande número de ficheiros vêem uma redução significativa no tempo de completar, para backups completos e incrementais.

- **Desempenho** de restauro melhorado - Esta versão contém melhorias que melhoram significativamente o desempenho da restauração ao utilizar um grande número de ficheiros. Se utilizar 2 - 4 milhões de ficheiros, recomendamos que forme um matriz virtual com RAM de 16 GB para ver as melhorias. Ao utilizar menos de 2 milhões de ficheiros, o requisito mínimo para a máquina virtual continua a ser de 8 GB de RAM.

- **Melhorias no pacote** de suporte - As melhorias incluem o registo nas estatísticas do disco, CPU, memória, rede e nuvem no pacote de Suporte, melhorando assim o processo de diagnóstico/depuração de problemas do dispositivo.

- **Limite os volumes iSCSI fixados localmente a 200 GB** - Para volumes fixados localmente, recomendamos que limite a um volume iSCSI de 200 GB na sua Matriz Virtual StorSimple. A reserva local para volumes hierárquicos continua a ser de 10 % do volume previsto, mas está limitada a 200 GB. 

- **Correções de bugs relacionadas** com backup - Em versões anteriores de software, existiam problemas relacionados com backups que iriam causar falhas de backup. Estes insetos foram abordados nesta libertação.


## <a name="issues-fixed-in-the-update-04"></a>Problemas corrigidos na Atualização 0.4

A tabela que se segue apresenta um resumo das questões fixadas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Desempenho de backup|Nos lançamentos anteriores, as cópias de segurança que envolvem um grande número de ficheiros levariam muito tempo a ser concluídas (na ordem dos dias). Nesta versão, tanto as cópias de segurança completas como incrementais vêem uma redução significativa no tempo até à conclusão. |
| 2 |Pacote de apoio|As estatísticas de disco, CPU, memória, rede e nuvem estão agora registadas nos registos de Suporte, tornando os pacotes de Suporte muito eficazes na resolução de problemas com qualquer problema do dispositivo.|
| 3 |Cópia de segurança |Em lançamentos anteriores, cópias de segurança de longa duração podem resultar numa crise de espaço no dispositivo, resultando em falhas de backup. Este bug é abordado nesta versão, permitindo que não mais de 5 backups façam fila de uma só vez.|
| 4 |iSCSI | Em lançamentos anteriores, a reserva local para volumes nicosos ou fixados localmente era de 10% do tamanho do volume provisionado. Nesta versão, a reserva local para todos os volumes iSCSI (fixados ou nihoscos localmente) está limitada a 10 % com um máximo de até 200 GB (para volumes nientes superiores a 2 TB) libertando assim mais espaço no disco local. Recomendamos que os volumes fixados localmente nesta versão sejam limitados a 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Questões conhecidas na Atualização 0.4

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
| **15.** |Servidor de ficheiros |Ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Windows Encrypting File System (EFS) quando copiados ou armazenados no servidor de ficheiros StorSimple Virtual Array resultam numa configuração não suportada.  | |

## <a name="next-step"></a>Passo seguinte
[Instale a Atualização 0.4](storsimple-virtual-array-install-update-04.md) no seu StorSimple Virtual Array.

## <a name="references"></a>Referências
À procura de uma nota de libertação mais antiga? Vá para: 

* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz virtual StorSimple](storsimple-ova-pp-release-notes.md)

