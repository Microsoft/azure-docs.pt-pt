---
title: Notas de lançamento do StorSimple Virtual Array Update 0.5 Microsoft Docs
description: Descreve questões e resoluções críticas abertas para o StorSimple Virtual Array executando update 0.5.
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
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870677"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>Notas de lançamento storSimple Virtual Array Update 0.5

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas e, à medida que são descobertas questões críticas que exigem uma suver, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.5 corresponde à versão do software **10.0.10290.0**.

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se o I/O estiver em curso, o dispositivo incorre no tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, vá à [Instalação da Atualização 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Novidades na Atualização 0.5
A atualização 0.5 é principalmente uma construção de correção de erros. As principais melhorias e correções de erros são as seguintes:

- **Melhorias** de resiliência de backup - Esta libertação tem correções que melhoram a resiliência de backup. Nos lançamentos anteriores, os backups foram novamente julgados apenas por certas exceções. Esta libertação retenta todas as exceções de backup e torna as cópias de segurança mais resistentes.

- **Atualizações para monitorização** de utilização de armazenamento - A partir de 30 de junho de 2017, será retirada a monitorização de utilização de armazenamento para a StorSimple Virtual Device Series. Isto aplica-se aos gráficos de monitorização de todas as matrizes virtuais que executam a Atualização 0.4 ou inferior. Esta atualização contém as alterações necessárias para que continue a utilizar a monitorização do uso do armazenamento no portal Azure. Instale esta atualização crítica antes de 30 de junho de 2017 para continuar a utilizar a função de monitorização.


## <a name="issues-fixed-in-the-update-05"></a>Problemas corrigidos na Atualização 0.5

A tabela que se segue apresenta um resumo das questões fixadas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Resiliência de backup| Nos lançamentos anteriores, os backups foram novamente julgados apenas por certas exceções. Esta libertação contém uma correção para tornar as cópias de segurança mais resistentes, reexperimentando todas as exceções de backup.|
| 2 |Monitorização| A monitorização de utilização de armazenamento para a Série de Dispositivos Virtuais StorSimple será depreciada a partir de 30 de junho de 2017. Esta ação impacta as tabelas de monitorização no serviço StorSimple Device Manager em execução em StorSimple Virtual Arrays (modelo 1200). Esta versão tem atualizações que permitem ao utilizador continuar a utilizar a monitorização de utilização de armazenamento nas matrizes virtuais para além de 30 de junho de 2017.|
| 3 |Servidor de ficheiros| Nos lançamentos anteriores, um utilizador poderia copiar erradamente ficheiros encriptados para a matriz virtual. Esta versão contém uma correção que não permitiria copiar ficheiros encriptados para a matriz virtual. Se o seu dispositivo tiver ficheiros encriptados existentes antes da atualização, as cópias de segurança continuarão a falhar até que todos os ficheiros encriptados sejam eliminados do sistema. |


## <a name="known-issues-in-the-update-05"></a>Questões conhecidas na Atualização 0.5

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
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações quando não há dados sobre a parte. Este consumo deve-se ao facto de a capacidade utilizada para as ações incluir metadados. | |
| **9.** |Recuperação após desastre |Só é possível realizar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo-alvo noutro domínio não é suportada nesta versão. |Isto é implementado numa versão posterior. Para mais informações, vá a [Failover e recuperação de desastres para o seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal Azure e da ui web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual só aceita a entrada em formato de teclado en-us. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, precisa de desligar os volumes e, em seguida, trazê-los online para que a mudança faça efeito. |Esta questão é abordada posteriormente. |
| **13.** |servidor iSCSI |O 'armazenamento usado' apresentado para um volume iSCSI pode ser diferente no serviço StorSimple Device Manager e no anfitrião iSCSI. |O anfitrião do iSCSI tem a visão do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível do item. | |
| **15.** |Servidor de ficheiros |Ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Windows Encrypting File System (EFS) quando copiados ou armazenados no servidor de ficheiros StorSimple Virtual Array resultam numa configuração não suportada.  | |

## <a name="next-step"></a>Passo seguinte
[Instale a Atualização 0.5](storsimple-virtual-array-install-update-05.md) no seu StorSimple Virtual Array.

## <a name="references"></a>Referências
À procura de uma nota de libertação mais antiga? Vá para:

* [StorSimple Virtual Array Update 0.4 Notas de lançamento](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz virtual StorSimple](storsimple-ova-pp-release-notes.md)

