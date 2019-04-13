---
title: Notas de versão do StorSimple 8000 Series Update 2.2 | Documentos da Microsoft
description: Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 Series Update 2.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527120"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>Notas de versão do StorSimple 8000 Series Update 2.2

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem os novos recursos e identificar os problemas em aberto críticos para a StorSimple 8000 Series Update 2.2. Também contêm uma lista das atualizações de software StorSimple incluídos nesta versão.

Atualização 2.2 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 por meio da atualização 2.1. A versão de dispositivo associada a atualização 2.2 é 6.3.9600.17708.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Atualização 2.2 tem únicas atualizações de software. Demora aproximadamente 1,5-2 horas para instalar esta atualização. 
> * Se estiver a executar atualização 2.1, recomendamos que aplique a atualização 2.2 logo que possível.
> * Para novas versões, poderá não ver atualizações imediatamente porque fazemos uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise para atualizações novamente como estes ficarão disponível em breve.
> 
> 

## <a name="whats-new-in-update-22"></a>O que há de novo na atualização 2.2
Foram efetuadas as seguintes principais melhorias na atualização 2.2.

* **Automatizada de otimização de recuperação de espaço** – quando os dados serão eliminados em volumes de aprovisionamento dinâmico, a necessidade de blocos de armazenamento não utilizados a recuperar. Esta versão melhorou o processo de recuperação de espaço da cloud, resultando em espaço não utilizado, tornando-se disponível mais rápido em comparação com versões anteriores.
* **Melhorias de desempenho do instantâneo** – atualização 2.2 melhorou o tempo a processar uma nuvem de instantâneos em determinados cenários em que grandes volumes que estão a ser utilizados e há mínima não existem alterações a dados. Um cenário que iria beneficiar desta melhoria seria os volumes de arquivo.
* **O sistema de proteção do pacote de suporte de recolha de** – foram melhorias na forma como o pacote de suporte é recolhido e carregado nesta versão. 
* **Atualizar melhorias na confiabilidade** – esta versão tem as correções de erros que resultem numa mais confiabilidade de atualização.

## <a name="issues-fixed-in-update-22"></a>Problemas corrigidos na atualização 2.2
As tabelas a seguir fornece um resumo dos problemas que foram corrigidas em atualizações 2.2 e 2.1.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Desempenho do anfitrião |Na versão anterior, os problemas de desempenho do lado do host foram observados durante a criação de um volume afixado localmente e durante a conversão de um volume em camadas para um volume afixado localmente. Estes problemas estiverem corrigidos nesta versão, deste modo, resultando numa melhoria no desempenho durante os procedimentos de criação e a conversão do volume anfitrião. |Sim |Não |
| 2 |volumes afixados localmente |Em situações raras, o sistema Parava durante a criação de um volume afixado localmente. Esse bug foi corrigido nesta versão. |Sim |Não |
| 3 |disposição em camadas |Ocorreram falhas esporádicos quando os metadados para a StorSimple Cloud Appliances (8010 e 8020) em camadas para a cloud. Este problema é corrigido nesta versão. |Não |Sim |
| 4 |Criação do instantâneo |Ocorreram os problemas relacionados à criação de instantâneos incrementais em cenários com grandes volumes e mínima não existem alterações a dados. Estes problemas estiverem corrigidos nesta versão. |Sim |Sim |
| 5 |Autenticação do Openstack |Quando utiliza o Openstack como o fornecedor de serviços cloud, o utilizador seria executado num bug pouco frequente relacionadas com a autenticação, em que o analisador JSON resultou numa pane. Esse bug é corrigido nesta versão. |Sim |Não |
| 6 |Cópia do lado do Host |Em versões anteriores do software, um bug pouco frequente relacionadas com a temporização de ODX foi visto quando copiar os dados a partir de um volume para outro volume. Isso poderia resultar numa ativação pós-falha de controlador e o sistema potencialmente pode entrar em modo de recuperação. Esse bug é corrigido nesta versão. |Sim |Não |
| 7 |Windows Management Instrumentation (WMI) |Nas versões anteriores do software, havia várias instâncias de falha de proxy da web com a exceção "\<uma exceção de gestão > Falha na carga do fornecedor". Esse bug foi atribuído a um vazamento de memória WMI e agora é fixo. |Sim |Não |
| 8 |Atualizar |Em determinadas circunstâncias raras, nas versões anteriores de software, o utilizador recebeu um "CisPowershellHcsscripterror" ao tentar analisar ou instalar atualizações. Este problema é corrigido nesta versão. |Sim |Sim |
| 9 |Pacote de suporte |Nesta versão, tem havido melhorias na forma como o pacote de suporte é recolhido e carregado. |Sim |Sim |

## <a name="known-issues-in-update-22"></a>Problemas conhecidos na atualização 2.2
A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários / solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 está ligado à Internet resultante no quórum sem disco, em seguida, o agrupamento de armazenamento irá ficar offline. Ele irá permanecer offline, mesmo que os discos são a ligação restabelecidos. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 2 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, o controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada a partir do nó de mesmo nível, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, esse comportamento também pode ser visto após um reinício do sistema. |Não é necessária nenhuma ação do utilizador. Esta situação será resolvido automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 3 |Contas de armazenamento |Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isso levará a uma situação em que não não possível obter os dados de utilizador. | |Sim |Sim |
| 4 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. Ativação pós-falha de um dispositivo inativo único para vários dispositivos fará com que os contentores de volumes no primeiro efetuar a ativação pós-falha do dispositivo perder a propriedade dos dados. Após a ativação pós-falha, estes contentores de volume irão aparecer ou ter um comportamento diferente quando o utilizador vê-los no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o adaptador do StorSimple para instalação do SharePoint, terá de fornecer um IP do dispositivo para que a instalação concluir com êxito. | |Sim |Não |
| 6 |Proxy da Web |Se a sua configuração de proxy da web tiver HTTPS como o protocolo especificado, em seguida, a comunicação de serviço a dispositivo será afetada e o dispositivo irá ficar offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy de web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Proxy da Web |Se configura e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador ativo no seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem de alta e a carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências de cloud muito elevada (ordem de segundos) e a carga de trabalho de e/s elevada, os volumes do dispositivo entram num Estado degradado e o e/s pode falhar com um erro de "dispositivo não está pronto". |Terá de reiniciar os controladores de dispositivo ou efetuar uma ativação pós-falha do dispositivo para recuperar a partir desta situação manualmente. |Sim |Não |
| 9 |Azure PowerShell |Quando utiliza o cmdlet do StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - primeiro 1 - Wait** para selecionar o primeiro objeto para que possa criar um novo **VolumeContainer** objeto, o cmdlet devolve todos os objetos. |Moldar o cmdlet parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sim |Sim |
| 10 |Migração |Quando vários contentores de volume são passados para a migração, a ETA para cópia de segurança mais recente é preciso apenas para o primeiro contentor de volume. Além disso, a migração paralela será iniciado depois das primeiros 4 cópias de segurança no contentor de volume do primeiro são migradas. |Recomendamos que migre um contentor de volumes cada vez. |Sim |Não |
| 11 |Migração |Após o restauro, os volumes de mensagens em fila não são adicionados para a política de cópia de segurança ou o grupo de disco virtual. |Terá de adicionar estes volumes para uma política de cópia de segurança para criar cópias de segurança. |Sim |Sim |
| 12 |Migração |Após a migração estar concluída, o dispositivo da 5000/7000 série não têm de aceder os contentores de dados migrados. |Recomendamos que elimine os contentores de dados migrados após a migração estar concluída e defendemos. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple em execução Update 1 não é possível clonar ou efetuar a recuperação após desastre para um dispositivo a executar o software de pré-atualização 1. |Terá de atualizar o dispositivo de destino para atualização 1 para permitir que essas operações |Sim |Sim |
| 14 |Migração |Cópia de segurança de configuração para a migração poderá falhar num dispositivo da série 5000-7000 quando existem grupos de volumes com nenhuma volumes associados. |Elimine todos os grupos de volume vazio com nenhuma volumes associados e, em seguida, repita a cópia de segurança de configuração. |Sim |Não |
| 15 |Cmdlets do PowerShell do Azure e os volumes afixados localmente |Não é possível criar um volume localmente afixado cmdlets do PowerShell do Azure. (Qualquer volume que criar através do PowerShell do Azure será colocado em camadas.) |Utilize sempre o serviço StorSimple Manager para configurar os volumes afixados localmente. |Sim |Não |
| 16 |Espaço disponível para volumes afixados localmente |Se eliminar um volume localmente afixado, o espaço disponível para novos volumes não pode ser atualizado imediatamente. O serviço StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |volumes afixados localmente |A tarefa de restauro expõe a cópia de segurança do instantâneo temporário no catálogo de cópia de segurança, mas apenas durante o período da tarefa de restauro. Além disso, ele expõe um grupo de disco virtual com o prefixo **tmpCollection** sobre o **políticas de cópia de segurança** página, mas somente durante o período da tarefa de restauro. |Esse comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, esse comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 18 |volumes afixados localmente |Se cancelar uma tarefa de restauro e uma ativação pós-falha de controlador ocorre imediatamente depois disso, a tarefa de restauro mostrará **falhada** em vez de **cancelado**. Se uma tarefa de restauro falhar e uma ativação pós-falha de controlador ocorre imediatamente depois disso, a tarefa de restauro mostrará **cancelado** em vez de **falha**. |Esse comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, esse comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 19 |volumes afixados localmente |Se cancelar uma tarefa de restauro ou se falha um restauro e, em seguida, ocorre a ativação pós-falha de um controlador, aparece uma tarefa de restauro adicionais a **tarefas** página. |Esse comportamento pode ocorrer se a tarefa de restauro tem apenas localmente afixado volumes ou uma combinação de volumes localmente afixados e em camadas. Se a tarefa de restauro inclui apenas os volumes em camadas, em seguida, esse comportamento não ocorrerá. Não é necessária nenhuma intervenção do utilizador. |Sim |Não |
| 20 |volumes afixados localmente |Se tentar converter um volume em camadas (criado e clonado com atualização 1.2 ou anterior) para um volume afixado localmente e o dispositivo está a ficar sem espaço ou uma falha de cloud, o clone(s) pode estar danificado. |Este problema ocorre apenas com volumes que foram criados e clonado com pré-atualização 2.1 de software. Deve ser um cenário de pouco frequente. | | |
| 21 |Conversão do volume |Não atualizar ACRs anexados a um volume enquanto uma conversão do volume está em curso (em camadas para localmente afixado ou vice-versa). A atualizar os ACRs pode resultar em danos em dados. |Se for necessário, atualize os ACRs antes da conversão do volume e não fazem qualquer outra atualizações ACR enquanto a conversão está em curso. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>Controlador e atualizações de firmware em atualização 2.2
Esta versão tem as atualizações apenas de software. No entanto, se estiver a atualizar a partir de uma versão anterior à atualização 2, precisará instalar controladores Storport, Spaceport e (em alguns casos) no seu dispositivo, as atualizações de firmware do disco.

Para obter mais informações sobre como instalar o controlador Storport, Spaceport e atualizações de firmware do disco, consulte [instalar atualização 2.2](storsimple-install-update-21.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-22"></a>Atualizações de dispositivo virtual no atualização 2.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtual tem de ser criado. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar atualização 2.2](storsimple-install-update-21.md) no dispositivo StorSimple.

