---
title: StorSimple Virtual Array Update 0.5 notas de lançamento Microsoft Docs
description: Descreve problemas e resoluções abertos críticos para o StorSimple Virtual Array que executa a Atualização 0.5.
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
ms.openlocfilehash: f70cf96baed274eaf260832bd83ab0e2c91f3683
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967045"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0.5 notas de lançamento

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.5 corresponde à versão do software **10.0.10290.0**.

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se a I/O estiver em andamento, o dispositivo incorre em tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, aceda à [Atualização de Instalação 0.5](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>Novidades na Atualização 0.5
A atualização 0.5 é principalmente uma construção de correção de erros. As principais melhorias e correções de bugs são as seguintes:

- **Melhorias de resiliência de backup** - Este lançamento tem correções que melhoram a resiliência de backup. Nos lançamentos anteriores, os backups foram novamente julgados apenas para certas exceções. Esta libertação retriudi todas as exceções de backup e torna os backups mais resistentes.

- **Atualizações para monitorização do uso de armazenamento** - A partir de 30 de junho de 2017, a monitorização do uso de armazenamento para a Série de Dispositivos Virtuais StorSimple será retirada. Isto aplica-se aos gráficos de monitorização de todas as matrizes virtuais em execução de Update 0.4 ou inferior. Esta atualização contém as alterações necessárias para continuar a utilizar a monitorização de utilização de armazenamento no portal Azure. Instale esta atualização crítica antes de 30 de junho de 2017 para continuar a utilizar a funcionalidade de monitorização.


## <a name="issues-fixed-in-the-update-05"></a>Problemas fixados na Atualização 0.5

O quadro seguinte fornece um resumo das questões corrigidas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Resiliência de reserva| Nos lançamentos anteriores, os backups foram novamente julgados apenas para certas exceções. Esta versão contém uma correção para tornar as cópias de segurança mais resistentes, reformulando todas as exceções de backup.|
| 2 |Monitorização| A monitorização do uso de armazenamento para a Série de Dispositivos Virtuais StorSimple será depreciada a partir de 30 de junho de 2017. Esta ação tem impacto nas tabelas de monitorização do serviço StorSimple Device Manager em execução em StorSimple Virtual Arrays (modelo 1200). Esta versão tem atualizações que permitem ao utilizador continuar a utilizar a monitorização do uso de armazenamento nas matrizes virtuais para além de 30 de junho de 2017.|
| 3 |Servidor de ficheiros| Nas versões anteriores, um utilizador poderia copiar erroneamente ficheiros encriptados para a matriz virtual. Esta versão contém uma correção que não permitiria copiar ficheiros encriptados para matriz virtual. Se o seu dispositivo tiver ficheiros encriptados existentes antes da atualização, as cópias de segurança continuarão a falhar até que todos os ficheiros encriptados sejam eliminados do sistema. |


## <a name="known-issues-in-the-update-05"></a>Emissões conhecidas na Atualização 0.5

A tabela seguinte fornece um resumo de questões conhecidas para o StorSimple Virtual Array e inclui os problemas de lançamento anotados a partir das versões anteriores.

| Não. | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Os dispositivos virtuais criados na versão de pré-visualização não podem ser atualizados para uma versão suportada da Disponibilidade Geral. |Estes dispositivos virtuais devem ser falhados para a libertação de Disponibilidade Geral utilizando um fluxo de trabalho de recuperação de desastres (DR). |
| **2.** |Disco de dados provisionado |Uma vez que tenha fornecedo um disco de dados de um determinado tamanho especificado e criado o dispositivo virtual StorSimple correspondente, não deve expandir ou encolher o disco de dados. Tentar fazer resulta numa perda de todos os dados nos níveis locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo é unido ao domínio, a aplicação de uma política de grupo pode afetar negativamente o funcionamento do dispositivo. |Certifique-se de que a sua matriz virtual está na sua própria unidade organizacional (OU) para o Ative Directory e que não são aplicados objetos de política de grupo (GPO). |
| **4.** |Local web UI |Se as funcionalidades de segurança reforçadas estiverem ativadas no Internet Explorer (IE ESC), algumas páginas locais de UI web, tais como Resolução de Problemas ou Manutenção, podem não funcionar corretamente. Os botões nestas páginas também podem não funcionar. |Desligue as funcionalidades de segurança melhoradas no Internet Explorer. |
| **5.** |Local web UI |Numa máquina virtual Hyper-V, as interfaces de rede na UI web são apresentadas como interfaces de 10 Gbps. |Este comportamento é um reflexo do Hiper-V. O Hiper-V mostra sempre 10 Gbps para adaptadores de rede virtuais. |
| **6.** |Volumes ou ações hierárquicos |O bloqueio de alcance byte para aplicações que trabalhem com os volumes tiered StorSimple não é suportado. Se o bloqueio de alcance byte estiver ativado, o tiering StorSimple não funciona. |As medidas recomendadas incluem: <br></br>Desligue o bloqueio do alcance do byte na lógica da aplicação.<br></br>Opte por colocar dados para esta aplicação em volumes fixados localmente em oposição a volumes hierárquicos.<br></br>*Caveat*: Quando se utilizam volumes fixados localmente e o bloqueio de alcance byte está ativado, o volume fixado localmente pode estar on-line mesmo antes da restauração estar completa. Nestes casos, se uma restauração está em curso, então deve esperar que a restauração esteja concluída. |
| **7.** |Ações hierárquis |Trabalhar com ficheiros grandes pode resultar numa saída lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior ficheiro seja inferior a 3% do tamanho da ação. |
| **8.** |Capacidade utilizada para ações |Pode ver o consumo de ações quando não há dados sobre a parte. Este consumo deve-se ao facto de a capacidade utilizada para as ações incluir metadados. | |
| **9.** |Recuperação após desastre |Só é possível efetuar a recuperação de desastres de um servidor de ficheiros para o mesmo domínio que o do dispositivo de origem. A recuperação de desastres para um dispositivo alvo noutro domínio não é suportada nesta versão. |Isto é implementado num lançamento posterior. Para mais informações, vá ao [Failover e à recuperação de desastres para o seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do Azure PowerShell nesta versão. |Toda a gestão dos dispositivos virtuais deve ser feita através do portal Azure e da UI web local. |
| **11.** |Alteração da palavra-passe |A consola de dispositivos de matriz virtual apenas aceita a entrada no formato de teclado en-us. | |
| **12.** |CHAP |As credenciais CHAP uma vez criadas não podem ser removidas. Além disso, se modificar as credenciais CHAP, precisa de desligar os volumes e, em seguida, trazê-los on-line para que a mudança produza efeitos. |Esta questão é abordada numa versão posterior. |
| **13.** |servidor iSCSI |O "armazenamento usado" exibido para um volume iSCSI pode ser diferente no serviço StorSimple Device Manager e no anfitrião iSCSI. |O anfitrião iSCSI tem a vista do sistema de ficheiros.<br></br>O dispositivo vê os blocos atribuídos quando o volume estava no tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tiver um Fluxo de Dados Alternativo (ADS) associado a ele, o ADS não é apoiado ou restaurado através da recuperação de desastres, clone e recuperação do nível de item. | |
| **15.** |Servidor de ficheiros |As ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Sistema de Ficheiros encriptadores do Windows (EFS) quando copiados ou armazenados no servidor de ficheiros StorSimple Virtual Array resultam numa configuração não suportada.  | |

## <a name="next-step"></a>Passo seguinte
[Instale a Atualização 0.5](storsimple-virtual-array-install-update-05.md) no seu Conjunto Virtual StorSimple.

## <a name="references"></a>Referências
À procura de uma nota de lançamento mais antiga? Vá para:

* [StorSimple Virtual Array Update 0.4 Notas de lançamento](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [StorSimple Notas de lançamento de disponibilidade geral de matriz virtual](./storsimple-virtual-array-update-06-release-notes.md)