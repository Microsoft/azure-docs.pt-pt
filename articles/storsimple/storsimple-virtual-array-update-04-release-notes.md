---
title: StorSimple Virtual Array Update 0.4 notas de lançamento| Microsoft Docs
description: Descreve problemas e resoluções abertos críticos para o StorSimple Virtual Array que executa a Atualização 0.4.
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
ms.openlocfilehash: 392e930dcb69392b6f9425abba0efa516d5c82b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967062"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0.4 notas de lançamento

## <a name="overview"></a>Descrição Geral

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 0.4 corresponde à versão do software **10.0.10289.0**.

> [!NOTE]
> As atualizações são disruptivas e reiniciam o seu dispositivo. Se a I/O estiver em andamento, o dispositivo incorre em tempo de inatividade.


## <a name="whats-new-in-the-update-04"></a>Novidades na Atualização 0.4
A atualização 0.4 é principalmente uma construção de correção de erros juntamente com algumas melhorias. Nesta versão foram abordados vários bugs que resultam em falhas de backup na versão anterior. As principais melhorias e correções de bugs são as seguintes:

- **Melhorias no desempenho de backup** - Este lançamento fez várias melhorias chave para melhorar o desempenho de backup. Como resultado, as cópias de segurança que envolvem um grande número de ficheiros vêem uma redução significativa no tempo para completar, para cópias de segurança completas e incrementais.

- **Desempenho de restauro melhorado** - Esta versão contém melhorias que melhoram significativamente o desempenho da restauração quando se utiliza um grande número de ficheiros. Se utilizar 2 - 4 milhões de ficheiros, recomendamos que você forisse um conjunto virtual com 16 GB de RAM para ver as melhorias. Ao utilizar menos de 2 milhões de ficheiros, o requisito mínimo para a máquina virtual continua a ser 8 GB de RAM.

- **Melhorias ao pacote de suporte** - As melhorias incluem o registo nas estatísticas do disco, CPU, memória, rede e nuvem no pacote de Suporte, melhorando assim o processo de diagnóstico/depuração de problemas do dispositivo.

- **Limite os volumes iSCSI fixados localmente a 200 GB** - Para volumes fixados localmente, recomendamos que limite a um volume iSCSI de 200 GB no seu StorSimple Virtual Array. A reserva local para volumes hierárquicos continua a ser de 10 % do volume previsto, mas está limitada a 200 GB. 

- **Correções de bugs relacionadas com cópias** de segurança - Em versões anteriores do software, existiam problemas relacionados com cópias de segurança que iriam causar falhas de backup. Estes insetos foram abordados nesta versão.


## <a name="issues-fixed-in-the-update-04"></a>Problemas corrigidos na Atualização 0.4

O quadro seguinte fornece um resumo das questões corrigidas nesta versão.

| N.º | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Desempenho de backup|Nos lançamentos anteriores, as cópias de segurança que envolvem um grande número de ficheiros demorariam muito tempo a ser concluídas (na ordem dos dias). Nesta versão, tanto os backups completos como incrementais vêem uma redução significativa no tempo de conclusão. |
| 2 |Pacote de apoio|As estatísticas de disco, CPU, memória, rede e nuvem estão agora registadas nos registos de Suporte, tornando os pacotes de Suporte muito eficazes na resolução de problemas de qualquer problema do dispositivo.|
| 3 |Backup |Em lançamentos anteriores, as cópias de segurança de longa duração podem resultar numa crise de espaço no dispositivo, resultando em falhas de backup. Este bug é abordado nesta versão, não permitindo que mais de 5 backups façam fila de cada vez.|
| 4 |iSCSI | Em lançamentos anteriores, a reserva local para volumes tiered ou localmente fixados era de 10% do tamanho do volume a provisionado. Nesta versão, a reserva local para todos os volumes iSCSI (fixados localmente ou nivelados) é limitada a 10 % com um máximo de 200 GB (para volumes hierárquicos superiores a 2 TB), libertando assim mais espaço no disco local. Recomendamos que os volumes fixados localmente nesta versão sejam limitados a 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Emissões conhecidas na Atualização 0.4

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
| **15.** |Servidor de ficheiros |As ligações simbólicas não são suportadas. | |
| **16.** |Servidor de ficheiros |Os ficheiros protegidos pelo Sistema de Ficheiros encriptadores do Windows (EFS) quando copiados ou armazenados no servidor de ficheiros StorSimple Virtual Array resultam numa configuração não suportada.  | |

## <a name="next-step"></a>Passo seguinte
[Instale a Atualização 0.4](storsimple-virtual-array-install-update-04.md) no seu Conjunto Virtual StorSimple.

## <a name="references"></a>Referências
À procura de uma nota de lançamento mais antiga? Vá para: 

* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [StorSimple Notas de lançamento de disponibilidade geral de matriz virtual](./storsimple-virtual-array-update-06-release-notes.md)