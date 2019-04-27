---
title: Notas de versão de atualizações de matriz Virtual StorSimple | Documentos da Microsoft
description: Descreve problemas em aberto críticos e resoluções para o StorSimple Virtual Array atualização 0.2 e 0.1 a executar.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: aad60024187ca180c002f119f4b975e8f69796e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629293"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Notas de versão do StorSimple Virtual matriz atualização 0.2 e 0.1
## <a name="overview"></a>Descrição geral
As seguintes notas de versão identificam os problemas em aberto críticos e os problemas resolvidos para atualizações do Microsoft Azure StorSimple Virtual Array. (Microsoft Azure StorSimple Virtual Array é também conhecido como dispositivo virtual StorSimple no local ou o dispositivo virtual StorSimple.) 

As notas de versão são atualizadas continuamente e, à medida que são descobertos problemas críticos que requerem uma solução, eles são adicionados. Antes de implementar o dispositivo virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 0.2 corresponde à versão software **10.0.10280.0**; Atualização 0.1 é a versão **10.0.10279.0**. As secções abaixo listam as alterações para cada atualização. 

> [!NOTE]
> As atualizações são disruptivas e irão reiniciar o seu dispositivo. Se forem e/s em curso, o dispositivo irá incorrer em períodos de indisponibilidade.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>Problemas corrigidos na atualização 0.2
Atualização 0.2 inclui todas as alterações da atualização 0.1, além da correção descrita na tabela a seguir:

| Funcionalidade | Problema |
| --- | --- |
| Atualizações |Na última versão, as atualizações não foram detetadas automaticamente no portal clássico do Azure, para que tinha de utilizar a IU Web local para instalar atualizações. Este problema é corrigido nesta versão. Depois de instalar a atualização 0.2, pode instalar as atualizações futuras através do portal clássico do Azure. |

## <a name="whats-new-in-the-update-01"></a>O que há de novo na atualização 0.1
Atualização 0.1 contém as seguintes correções de erros e melhorias. 

* **Resiliência melhorada para as falhas de cloud**: Esta versão tem várias correções de erros em torno de recuperação após desastre, cópia de segurança, restauro e criação de camadas em caso de uma interrupção de conectividade de cloud. 
* **Melhor desempenho de restauro**: Esta versão tem as correções de erros que têm significativamente a reduzir o tempo de conclusão das tarefas de restauro.
* **Automatizada de otimização de recuperação de espaço**: Quando dados são eliminados em volumes de aprovisionamento dinâmico, os blocos de armazenamento não utilizado tem de ser recuperada. Esta versão melhorou o processo de recuperação de espaço da cloud, resultando em espaço não utilizado, tornando-se disponível mais rápido em comparação com versões anteriores.
* **Novas imagens de disco virtual**: Novo VHD, VHDX e VMDK estão agora disponíveis no portal clássico do Azure. Pode baixar essas imagens para aprovisionar novos dispositivos de atualização 0.1.
* **Melhorar a precisão do Estado de tarefas no portal do**: Na versão anterior do software, o estado da tarefa relatórios no portal de não era granular. Este problema é resolvido nesta versão.
* **Experiência de associação de domínio**: Correções de erros relacionados com a associação ao domínio e mudar o nome do dispositivo.

## <a name="issues-fixed-in-the-update-01"></a>Problemas corrigidos na atualização 0.1
A tabela seguinte fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |VMDK |Em algumas versões do VMware, o disco do SO foi visto como esparsos fazendo com que alertas e interromper as operações normais. Isto foi corrigido nesta versão. |
| 2 |servidor iSCSI |Na última versão, foi pedido ao utilizador para especificar um gateway para cada interface de rede ativadas do seu dispositivo virtual StorSimple. Este comportamento é alterado nesta versão, para que o utilizador tem de configurar pelo menos um gateway para todas as interfaces de rede ativada. |
| 3 |Pacote de suporte |Na versão anterior do software, falha na recolha de pacote de suporte quando os tamanhos de pacote foram superiores a 1 GB. Este problema é corrigido nesta versão. |
| 4 |Acesso à nuvem |Na última versão, se a matriz Virtual StorSimple não tem conectividade de rede e foi reiniciada, a interface do Usuário local teria problemas de conectividade. Este problema esteja corrigido nesta versão. |
| 5 |Gráficos de monitorização |Na versão anterior, seguindo uma ativação pós-falha do dispositivo, os gráficos de utilização de capacidade de nuvem apresentados valores incorretos no portal clássico do Azure. Isso é corrigido na versão atual. |

## <a name="known-issues-in-the-update-01"></a>Problemas conhecidos na atualização 0.1
A tabela seguinte fornece um resumo dos problemas conhecidos para a matriz Virtual StorSimple e inclui os problemas que anotou lançamento de versões anteriores. **A versão de problemas indicada nesta versão estão marcados com um asterisco. Quase todos os problemas nesta lista tem transitar da versão de disponibilidade geral da matriz Virtual StorSimple.**

| Não. | Funcionalidade | Problema | Solução ou enviar comentários |
| --- | --- | --- | --- |
| **1.** |Atualizações |Não não possível atualizar os dispositivos virtuais criados na versão de pré-visualização para uma versão suportada de disponibilidade geral. |Estes dispositivos virtuais devem efetuar a ativação pós-falha para a versão de disponibilidade geral através de um fluxo de trabalho de recuperação (DR) após desastre. |
| **2.** |Disco de dados aprovisionados |Assim que aprovisionou um disco de dados de um determinado tamanho especificado e criar dispositivo virtual StorSimple correspondente, tem não expandir ou reduzir o disco de dados. Tentar fazê-lo resultará numa perda de todos os dados nas camadas locais do dispositivo. | |
| **3.** |Política de grupo |Quando um dispositivo está associado a um domínio, aplicar uma política de grupo o pode afetar negativamente a operação de dispositivo. |Certifique-se de que a sua matriz virtual está em sua própria unidade organizacional (UO) para o Active Directory e não existem objetos de política de grupo (GPO) são aplicados ao mesmo. |
| **4.** |IU web local |Se os recursos de segurança avançados estão ativados no Internet Explorer (IE ESC), algumas páginas de interface do Usuário da local web, como resolução de problemas ou manutenção pode não funcionar corretamente. Botões nestas páginas também podem não funcionar. |Desative a recursos de segurança avançada do Internet Explorer. |
| **5.** |IU web local |Numa máquina virtual de Hyper-V, as interfaces de rede na web interface do Usuário são exibidos como 10 Gbps interfaces. |Este comportamento é um reflexo do Hyper-V. Hyper-V sempre mostra 10 Gbps para adaptadores de rede virtual. |
| **6.** |Volumes em camadas ou partilhas |Intervalo de bytes de bloqueio para aplicações que funcionam com o StorSimple volumes em camadas não é suportada. Se o bloqueio de intervalo de bytes é ativado, a criação de camadas do StorSimple não irá funcionar. |Medidas recomendadas incluem: <br></br>Desative bloqueio em sua lógica de aplicativo de intervalo de bytes.<br></br>Opte por colocar os dados para esta aplicação em volumes afixados localmente em vez de volumes em camadas.<br></br>*Limitação*: Se utilizar localmente volumes afixados e bloqueio de intervalo de bytes é ativado, lembre-se de que o volume afixado localmente pode estar online, mesmo antes do restauro ser concluído. Em tais casos, se um restauro está em curso, em seguida, tem de aguardar o restauro concluir. |
| **7.** |Partilhas em camadas |Trabalhar com ficheiros grandes pode resultar em horizontalmente a camada lenta. |Ao trabalhar com ficheiros grandes, recomendamos que o maior arquivo for menor que 3% do tamanho da partilha. |
| **8.** |Capacidade para partilhas de utilizado |Poderá ver partilhar consumo na ausência de quaisquer dados na partilha. Isto acontece porque a capacidade utilizada para partilhas inclui metadados. | |
| **9.** |Recuperação após desastre |Só pode efetuar a recuperação após desastre de um servidor de ficheiros ao mesmo domínio que o dispositivo de origem. Recuperação após desastre para um dispositivo de destino no outro domínio não é suportada nesta versão. |Isso vai ser implementado numa versão posterior. |
| **10.** |Azure PowerShell |Os dispositivos virtuais StorSimple não podem ser geridos através do PowerShell do Azure nesta versão. |Todas as a gestão dos dispositivos virtual deve ser feita através do portal clássico do Azure e da web local da interface do Usuário. |
| **11.** |Alteração da palavra-passe |Na consola de dispositivos da matriz virtual só aceita a entrada no formato de teclado de en-US. | |
| **12.** |CHAP |Não não possível remover as credenciais CHAP depois de criada. Além disso, se modificar as credenciais CHAP, terá de colocar os volumes offline e, em seguida, colocá-los online para que a alteração tenha efeito. |Estes serão resolvidos numa versão posterior. |
| **13.** |servidor iSCSI |O 'utilizado armazenamento"exibidos para um volume iSCSI pode ser diferente no serviço StorSimple Manager e o anfitrião de iSCSI. |O anfitrião iSCSI tem a vista de sistema de ficheiros.<br></br>O dispositivo vê os blocos alocados quando o volume foi o tamanho máximo. |
| **14.** |Servidor de ficheiros * |Se um ficheiro numa pasta tem uma alternativa dados Stream (ADS) associados a ele, o ADS não é uma cópia de segurança ou restaurar através de recuperação após desastre, clonar e recuperação de nível de Item. | |

## <a name="next-step"></a>Passo seguinte
[Instalar atualizações](storsimple-ova-install-update-01.md) na sua matriz Virtual StorSimple.

