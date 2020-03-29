---
title: Notas de lançamento do StorSimple Virtual Array Update 1.1 Microsoft Docs
description: Descreve questões e resoluções críticas abertas para o StorSimple Virtual Array executando update 1.1.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: 55103d6307614f8796c41c35d6345e1fc3aca261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60789642"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Notas de lançamento storSimple Virtual Array Update 1.1

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 1.1 corresponde à versão do software **10.0.10307.0**.

> [!IMPORTANT]
> - As atualizações são disruptivas e reiniciam o seu dispositivo. Se o I/O estiver em curso, o dispositivo incorre no tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, vá à [Instalação da Atualização 1.1](storsimple-virtual-array-install-update-11.md).
>
> - A atualização 1.1 só está disponível através do portal Azure se o seu dispositivo estiver a executar o Update 1.0.

## <a name="whats-new-in-update-11"></a>Novidades na Atualização 1.1

Esta atualização contém as seguintes correções de melhoramento e bugs:

 - **Falhas** de backup - foram melhoradas aumentando a resiliência às falhas na nuvem e ao elevado consumo de CPU.
 - **Sessão de registo** - Foram feitas alterações ao modo verbose de exploração quando o dispositivo está em sessão de suporte.


## <a name="issues-fixed-in-update-11"></a>Problemas corrigidos na Atualização 1.1

A tabela que se segue apresenta um resumo das questões fixadas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Cópias de segurança| Esta versão contém alterações que melhoraram as falhas de backup, aumentando a resiliência às falhas na nuvem e ao elevado uso de CPU.|
| 2 |Registo| Esta versão contém alterações ao registo enquanto o dispositivo está em sessão de suporte no modo verbose.|


## <a name="known-issues-in-update-11"></a>Questões conhecidas na Atualização 1.1

A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir dos lançamentos anteriores.

| Não. | Funcionalidade | Problema | Seleção/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |As matrizes virtuais criadas no lançamento de pré-visualização não podem ser atualizadas para uma versão de Disponibilidade Geral suportada. |Estas matrizes virtuais devem ser reprovadas para a libertação de disponibilidade geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Depois de ter aprovisionado um disco de dados de um determinado tamanho especificado e ter criado o StorSimple Virtual Array correspondente, não deve expandir ou encolher o disco de dados. Tentar fazer resulta numa perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, aplicar uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança melhoradas forem ativadas no Internet Explorer (IE ESC), algumas páginas de UI web locais, como a Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são exibidas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hyper-V. O Hyper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |Não é suportado o bloqueio de gama byte para aplicações que funcionem com os volumes storSimple tiered. Se o bloqueio do alcance byte estiver ativado, o diferencial StorSimple não funciona. |As medidas recomendadas incluem: <br></br>Desligue o alcance do byte na sua lógica de aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição aos volumes hierárquicos.<br></br>*Caveat*: Ao utilizar volumes fixados localmente e o bloqueio de alcance byte está ativado, o volume fixado localmente pode estar on-line mesmo antes de a restauração estar completa. Nesses casos, se houver um restauro em curso, então deve esperar que o restauro esteja concluído. |
| **7.** |Ações hierárquias |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações quando não há dados sobre a parte. Este consumo deve-se ao facto de a capacidade utilizada para as ações incluir metadados. | |
| **9.** |Recuperação após desastre |Só é possível realizar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo-alvo noutro domínio não é suportada nesta versão. |Isto é implementado numa versão posterior. Para mais informações, vá a [Failover e recuperação de desastres para o seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Os Arrays Virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal Azure e da ui web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual só aceita a entrada em formato de teclado en-us. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, precisa de desligar os volumes e, em seguida, trazê-los online para que a mudança faça efeito. |Esta questão é abordada posteriormente. |
| **13.** |servidor iSCSI |O 'armazenamento usado' apresentado para um volume iSCSI pode ser diferente no serviço StorSimple Device Manager e no anfitrião iSCSI. |O anfitrião do iSCSI tem a visão do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível do item. | |
| **15.** |Servidor de ficheiros |Ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Windows Encrypting File System (EFS) quando copiados ou armazenados no servidor de ficheiros StorSimple Virtual Array resultam numa configuração não suportada.  | |
| **17.** |Atualizações |Se vir o código de erro: 2359302 (hex 0x240006) ao tentar instalar um hotfix através da UI local, então isto implica que o hotfix já está instalado no seu dispositivo.   | |
| **18.** |Atualizações |Se utilizar o UI web local para instalar o Update 1 na sua matriz virtual, tem de se certificar de que está a executar o Update 0.6. Se estiver a executar uma versão inferior à Atualização 0.6, tem de instalar o Update 0.6 primeiro e depois aplicar o Update 1. Se instalar diretamente o Update 1.0 a partir de uma versão pré-Actualização 0.6, perderá algumas atualizações e as tabelas de monitorização não funcionarão.   | |


## <a name="next-steps"></a>Passos seguintes
[Instale a Atualização 1.1](storsimple-virtual-array-install-update-11.md) no seu StorSimple Virtual Array.

## <a name="references"></a>Referências
À procura de uma nota de libertação mais antiga? Vá para:
* [Notas de lançamento da StorSimple Virtual Array Update 1.0](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Notas de lançamento](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Notas de lançamento](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Notas de lançamento](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz virtual StorSimple](storsimple-ova-pp-release-notes.md)
