---
title: Notas de versão 0.4 de atualização de matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve problemas em aberto críticos e resoluções para o StorSimple Virtual Array atualização 0.4 a executar.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60334799"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>Notas de versão 0.4 de atualização de matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

As seguintes notas de versão identificam os problemas em aberto críticos e os problemas resolvidos para atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são atualizadas continuamente e, à medida que são descobertos problemas críticos que requerem uma solução, eles são adicionados. Antes de implementar a sua matriz Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 0.4 corresponde à versão software **10.0.10289.0**.

> [!NOTE]
> As atualizações são disruptivas e reinicie o seu dispositivo. Se forem e/s em curso, o dispositivo incorre em tempo de inatividade.


## <a name="whats-new-in-the-update-04"></a>O que há de novo na atualização 0.4
Atualização 0.4 é principalmente uma compilação de correção de erro juntamente com algumas melhorias. Nesta versão, resultando em falhas de cópia de segurança na versão anterior de vários bugs foram solucionados. As principais melhorias e correções de erros são os seguintes:

- **Melhorias de desempenho de cópia de segurança** -nesta versão fez várias melhorias importantes para melhorar o desempenho de cópia de segurança. Como resultado, as cópias de segurança que envolvem um grande número de ficheiros de ver uma redução significativa no tempo para concluir, para as cópias de segurança completas e incrementais.

- **Melhorada a restaurar desempenho** -esta versão contém aprimoramentos que melhorar significativamente o desempenho de restauro, ao usar o grande número de ficheiros. Se utilizar 2-4 milhões de ficheiros, recomendamos que Aprovisiona uma matriz virtual com 16 GB de RAM para ver os aprimoramentos. Ao utilizar menos de 2 milhões de ficheiros, o requisito mínimo para a máquina virtual continua sendo 8 GB de RAM.

- **Melhorias ao pacote de suporte** -os melhoramentos incluem registo nas estatísticas de disco, CPU, memória, rede e na cloud para o pacote de suporte, melhorando assim o processo de diagnosticar/depuração de problemas de dispositivos.

- **Limite localmente afixado volumes de iSCSI para 200 GB** -para volumes afixados localmente, recomendamos que limite a um volume de iSCSI de 200 GB na sua matriz Virtual StorSimple. A reserva local em volumes em camadas continua a ser 10% do tamanho do volume aprovisionado, mas está limitada a 200 GB. 

- **Correções de erros relacionados com a cópia de segurança** - nas versões anteriores do software, ocorreram problemas relacionados com cópias de segurança que iriam causar falhas de cópia de segurança. Esses bugs foram solucionados nesta versão.


## <a name="issues-fixed-in-the-update-04"></a>Problemas corrigidos na atualização 0.4

A tabela seguinte fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Desempenho de cópia de segurança|Nas versões anteriores, as cópias de segurança que envolvem o grande número de arquivos levaria muito tempo a concluir (por ordem de dias). Nesta versão, as cópias de segurança completas e incrementais ver uma redução significativa no tempo até à conclusão. |
| 2 |Pacote de suporte|Disco, da CPU, memória, rede e estatísticas de cloud são agora sessão iniciadas para os registos de suporte, tornando os pacotes de suporte muito eficaz na resolução de problemas de qualquer dispositivo.|
| 3 |Cópia de segurança |Nas versões anteriores, as cópias de segurança de longa execução pode resultar num processe de espaço no dispositivo, resultando em falhas de cópia de segurança. Esse bug é abordado nesta versão, permitindo que mais do que 5 cópias de segurança para a fila de uma só vez.|
| 4 |iSCSI | Nas versões anteriores, a reserva local para volumes afixados localmente ou em camadas era de 10% do tamanho do volume aprovisionado. Nesta versão, a reserva local em todos os volumes de iSCSI (localmente afixado ou em camadas) está limitada a 10% com um máximo de 200 GB (para os volumes em camadas superiores a 2 TB), deste modo, dando mais espaço no disco local. Recomendamos que os volumes afixados localmente nesta versão ter um limite de 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Problemas conhecidos na atualização 0.4

A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas que anotou lançamento de versões anteriores. 

| Não. | Funcionalidade | Problema | Solução ou enviar comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Não não possível atualizar os dispositivos virtuais criados na versão de pré-visualização para uma versão suportada de disponibilidade geral. |Estes dispositivos virtuais devem efetuar a ativação pós-falha para a versão de disponibilidade geral através de um fluxo de trabalho de recuperação (DR) após desastre. |
| **2.** |Disco de dados aprovisionados |Assim que aprovisionou um disco de dados de um determinado tamanho especificado e criar dispositivo virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. Tentar fazê-lo resulta numa perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está associado a um domínio, aplicar uma política de grupo o pode afetar negativamente a operação de dispositivo. |Certifique-se de que a sua matriz virtual está em sua própria unidade organizacional (UO) para o Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo. |
| **4.** |IU web local |Se os recursos de segurança avançados estão ativados no Internet Explorer (IE ESC), algumas páginas de interface do Usuário da local web, como resolução de problemas ou manutenção pode não funcionar corretamente. Botões nestas páginas também podem não funcionar. |Desative a recursos de segurança avançada do Internet Explorer. |
| **5.** |IU web local |Numa máquina virtual de Hyper-V, as interfaces de rede na web interface do Usuário são exibidos como 10 Gbps interfaces. |Este comportamento é um reflexo do Hyper-V. Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes em camadas ou partilhas |Intervalo de bytes de bloqueio para aplicações que funcionam com o StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de bytes é ativado, disposição em camadas do StorSimple não funciona. |Medidas recomendadas incluem: <br></br>Desative bloqueio em sua lógica de aplicativo de intervalo de bytes.<br></br>Opte por colocar os dados para esta aplicação em volumes afixados localmente em vez de volumes em camadas.<br></br>*Limitação*: Quando localmente utilizando volumes afixados e bloqueio de intervalo de bytes é ativado, o volume afixado localmente pode estar online, mesmo antes do restauro ser concluído. Em tais casos, se um restauro está em curso, em seguida, tem de aguardar o restauro concluir. |
| **7.** |Partilhas em camadas |Trabalhar com ficheiros grandes pode resultar em horizontalmente a camada lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior arquivo for menor que 3% do tamanho da partilha. |
| **8.** |Capacidade para partilhas de utilizado |Poderá ver partilhar consumo, quando não houver dados na partilha. Isto acontece porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação após desastre para um dispositivo de destino no outro domínio não é suportada nesta versão. |Isso é implementado numa versão posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do PowerShell do Azure nesta versão. |Todas as a gestão dos dispositivos virtual deve ser feita através do portal clássico do Azure e da web local da interface do Usuário. |
| **11.** |Alteração da palavra-passe |Na consola de dispositivos da matriz virtual só aceita a entrada no formato de teclado de en-US. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criada. Além disso, se modificar as credenciais CHAP, terá de colocar os volumes offline e, em seguida, colocá-los online para que a alteração tenha efeito. |Esse problema é corrigido numa versão posterior. |
| **13.** |servidor iSCSI |O 'utilizado armazenamento"exibidos para um volume iSCSI pode ser diferente no serviço StorSimple Manager e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos alocados quando o volume foi o tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tem uma alternativa dados Stream (ADS) associados a ele, o ADS não é uma cópia de segurança ou restaurar através de recuperação após desastre, clonar e recuperação de nível de Item. | |
| **15.** |Servidor de ficheiros |Links simbólicos não são suportados. | |
| **16.** |Servidor de ficheiros |Ficheiros protegidos por Windows Encrypting File System (EFS) quando copiado através de ou armazenados no resultado do servidor de ficheiros StorSimple Virtual Array numa configuração não suportada.  | |

## <a name="next-step"></a>Passo seguinte
[Instalar atualização 0.4](storsimple-virtual-array-install-update-04.md) na sua matriz Virtual StorSimple.

## <a name="references"></a>Referências
À procura de uma nota de versão mais antiga? Vá para: 

* [Notas de versão 0.3 de atualização de matriz Virtual StorSimple](storsimple-ova-update-03-release-notes.md)
* [Notas de versão do StorSimple Virtual Array atualização 0,1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade de geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)

