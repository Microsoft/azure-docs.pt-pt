---
title: Notas de versão 1.1 de atualização de matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve problemas em aberto críticos e resoluções para o StorSimple Virtual Array atualização 1.1 a executar.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789642"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>Notas de versão 1.1 de atualização de matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

As seguintes notas de versão identificam os problemas em aberto críticos e os problemas resolvidos para atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são atualizadas continuamente e, à medida que são descobertos problemas críticos que requerem uma solução, eles são adicionados. Antes de implementar a sua matriz Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 1.1 corresponde à versão software **10.0.10307.0**.

> [!IMPORTANT]
> - As atualizações são disruptivas e reinicie o seu dispositivo. Se forem e/s em curso, o dispositivo incorre em tempo de inatividade. Para obter instruções detalhadas sobre como aplicar a atualização, aceda a [instalar 1.1 atualizar](storsimple-virtual-array-install-update-11.md).
>
> - Atualização 1.1 está disponível através do portal do Azure apenas se o dispositivo é executar a atualização 1.0.

## <a name="whats-new-in-update-11"></a>O que há de novo na atualização 1.1

Esta atualização contém as seguintes aprimoramento e correções de erros:

 - **Falhas de cópia de segurança** -foram melhorados ao aumentar a resiliência a falhas de cloud e elevado consumo de CPU.
 - **Registo** -foram feitas alterações ao modo verboso registo quando o dispositivo estiver numa sessão de suporte.


## <a name="issues-fixed-in-update-11"></a>Problemas corrigidos na 1.1 de atualização

A tabela seguinte fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Cópias de segurança| Esta versão contém alterações que melhoraram as falhas de cópia de segurança ao aumentar a resiliência a falhas de nuvem e de elevada utilização da CPU.|
| 2 |Registo| Esta versão contém alterações ao registo enquanto o dispositivo estiver numa sessão de suporte no modo verboso.|


## <a name="known-issues-in-update-11"></a>Problemas conhecidos na 1.1 de atualização

A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas que anotou lançamento de versões anteriores.

| Não. | Funcionalidade | Problema | Solução ou enviar comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |As matrizes virtuais criadas na versão de pré-visualização não não possível atualizar para uma versão suportada de disponibilidade geral. |Estas matrizes virtuais devem efetuar a ativação pós-falha para a versão de disponibilidade geral através de um fluxo de trabalho de recuperação (DR) após desastre. |
| **2.** |Disco de dados aprovisionados |Uma vez aprovisionou um disco de dados de um determinado tamanho especificado e criado correspondente a matriz Virtual StorSimple, tem não expandir ou reduzir o disco de dados. Tentar fazê-lo resulta numa perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está associado a um domínio, aplicar uma política de grupo o pode afetar negativamente a operação de dispositivo. |Certifique-se de que a sua matriz virtual está em sua própria unidade organizacional (UO) para o Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo. |
| **4.** |IU web local |Se os recursos de segurança avançados estão ativados no Internet Explorer (IE ESC), algumas páginas de interface do Usuário da local web, como resolução de problemas ou manutenção pode não funcionar corretamente. Botões nestas páginas também podem não funcionar. |Desative a recursos de segurança avançada do Internet Explorer. |
| **5.** |IU web local |Numa máquina virtual de Hyper-V, as interfaces de rede na web interface do Usuário são exibidos como 10 Gbps interfaces. |Este comportamento é um reflexo do Hyper-V. Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes em camadas ou partilhas |Intervalo de bytes de bloqueio para aplicações que funcionam com o StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de bytes é ativado, disposição em camadas do StorSimple não funciona. |Medidas recomendadas incluem: <br></br>Desative bloqueio em sua lógica de aplicativo de intervalo de bytes.<br></br>Opte por colocar os dados para esta aplicação em volumes afixados localmente em vez de volumes em camadas.<br></br>*Limitação*: Quando localmente utilizando volumes afixados e bloqueio de intervalo de bytes é ativado, o volume afixado localmente pode estar online, mesmo antes do restauro ser concluído. Em tais casos, se um restauro está em curso, em seguida, tem de aguardar o restauro concluir. |
| **7.** |Partilhas em camadas |Trabalhar com ficheiros grandes pode resultar em horizontalmente a camada lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior arquivo for menor que 3% do tamanho da partilha. |
| **8.** |Capacidade para partilhas de utilizado |Poderá ver partilhar consumo, quando não houver dados na partilha. Esse consumo é porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação após desastre para um dispositivo de destino no outro domínio não é suportada nesta versão. |Isso é implementado numa versão posterior. Para obter mais informações, aceda a [ativação pós-falha e recuperação após desastre para a sua matriz Virtual StorSimple](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |As matrizes virtuais do StorSimple não podem ser geridas através do PowerShell do Azure nesta versão. |Todas as a gestão dos dispositivos virtual deve ser feita através do portal do Azure e da web local da interface do Usuário. |
| **11.** |Alteração da palavra-passe |Na consola de dispositivos da matriz virtual só aceita a entrada em en-us teclado formato. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criada. Além disso, se modificar as credenciais CHAP, terá de colocar os volumes offline e, em seguida, colocá-los online para que a alteração tenha efeito. |Esse problema é corrigido numa versão posterior. |
| **13.** |servidor iSCSI |O 'utilizado armazenamento"exibidos para um volume iSCSI pode ser diferente no serviço StorSimple Device Manager e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos alocados quando o volume foi o tamanho máximo. |
| **14.** |Servidor de ficheiros |Se um ficheiro numa pasta tem uma alternativa dados Stream (ADS) associados a ele, o ADS não é uma cópia de segurança ou restaurar através de recuperação após desastre, clonar e recuperação de nível de Item. | |
| **15.** |Servidor de ficheiros |Links simbólicos não são suportados. | |
| **16.** |Servidor de ficheiros |Ficheiros protegidos por Windows Encrypting File System (EFS) quando copiado através de ou armazenados no resultado do servidor de ficheiros StorSimple Virtual Array numa configuração não suportada.  | |
| **17.** |Atualizações |Se vir o erro de código: 2359302 (hex 0x240006) ao tentar instalar uma correção por meio da interface do Usuário local, em seguida, isso implica que a correção já está instalada no seu dispositivo.   | |
| **18.** |Atualizações |Se utilizar a IU da web local para instalar a atualização 1 em sua matriz virtual, certifique-se de que está a executar atualização 0.6. Se estiver a executar uma versão inferior de atualização 0.6, tem de instalar a atualização 0.6 primeiro e, em seguida, aplique o Update 1. Se instalou a atualização 1.0 diretamente de uma versão de 0,6 de pré-atualização, em seguida, não terá acesso algumas atualizações e os gráficos de monitorização não irão funcionar.   | |


## <a name="next-steps"></a>Passos Seguintes
[Instalar atualização 1.1](storsimple-virtual-array-install-update-11.md) na sua matriz Virtual StorSimple.

## <a name="references"></a>Referências
À procura de uma nota de versão mais antiga? Vá para:
* [Notas de versão 1.0 do StorSimple Virtual Array atualização](storsimple-virtual-array-update-1-release-notes.md)
* [Notas de versão 0,6 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão 0,5 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão 0.4 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão 0.3 de atualização de matriz Virtual StorSimple](storsimple-ova-update-03-release-notes.md)
* [Notas de versão do StorSimple Virtual Array atualização 0,1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade de geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)
