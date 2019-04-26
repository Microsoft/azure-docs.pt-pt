---
title: Notas de versão do StorSimple 8000 Series Update 3 | Documentos da Microsoft
description: Descreve os novos recursos, problemas e soluções alternativas para o StorSimple 8000 Series Update 3.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d18feba4ded3dfccb8f774112a7dc8d42b12f1d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530958"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Atualização 3 notas de versão do seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem os novos recursos e identificar os problemas em aberto críticos para o StorSimple 8000 Series Update 3. Também contêm uma lista das atualizações de software StorSimple incluídos nesta versão. 

Atualização 3 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 por meio de atualização 2.2. A versão de dispositivo associada a atualização 3 é 6.3.9600.17759.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * A atualização 3 tem o software de dispositivo, driver de LSI e firmware e Storport e Spaceport de atualizações. Demora aproximadamente 1,5-2 horas para instalar esta atualização. 
> * Para novas versões, poderá não ver atualizações imediatamente porque fazemos uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise para atualizações novamente como estes ficarão disponível em breve.
> 
> 

## <a name="whats-new-in-update-3"></a>O que há de novo na atualização 3
Foram efetuadas as seguintes principais melhoramentos e correções de erros no Update 3.

* **Automatizada alterações de recuperação de espaço** – a partir de Update 3, os algoritmos de recuperação de espaço em execução no controlador de reserva do sistema, resultando em execução mais rápida. Para obter mais informações sobre as portas que são necessárias para trabalhar com a recuperação de espaço, consulte a [StorSimple requisitos de rede](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Melhorias de desempenho** – atualização 3 melhorou o desempenho de leitura / escrita para a cloud.
* **As melhorias relacionadas com a migração** – nesta versão, várias correções de erros e melhorias foram feitas para a funcionalidade de migração de dispositivos das 5000/7000 séries para dispositivos de 8000 série. Para obter mais informações sobre como utilizar a funcionalidade de migração, aceda a [migração do dispositivo da 5000/7000 série para o dispositivo da 8000 série](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitorização correções relacionadas** - nesta versão, bugs relacionados com a monitorização de gráficos, dashboard de serviço, e o dashboard do dispositivo foram corrigidas.

## <a name="issues-fixed-in-update-3"></a>Problemas corrigidos no Update 3
As tabelas a seguir fornece um resumo dos problemas que foram corrigidos no Update 3.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Migração de dados do lado do Host |Na versão anterior, o StorSimple Cloud Appliance foi ficar offline durante uma migração de dados do lado do host. Este problema é corrigido nesta versão. |Não |Sim |
| 2 |volumes afixados localmente |Na versão anterior, ocorreram problemas relacionados com falhas de e/s, falhas de conversão do volume e falhas de datapath para volumes afixados localmente. Esses problemas foram causados por raiz e corrigidos nesta versão. |Sim |Não |
| 3 |Monitorização |Ocorreram vários problemas relacionados com unidades de relatórios e monitorização, bem como gráficos de dashboard de dispositivo em que informações incorretas foi apresentadas para volumes afixados localmente. Estes problemas estiverem corrigidos nesta versão. |Sim |Não |
| 4 |E/s de escritas pesadas |Ao utilizar o StorSimple para cargas de trabalho que envolvem escritas pesadas, o utilizador encontrariam num pouco frequente bug em que o conjunto de trabalho foi a ser em camadas para a cloud. Esse bug é corrigido nesta versão. |Sim |Sim |
| 5 |Cópia de segurança |Em determinadas circunstâncias raras, nas versões anteriores de software, quando o utilizador realizar uma cópia de segurança de um clone remoto, eles seriam deparar com erros de cloud e a operação seria o erro. Nesta versão, o problema é resolvido e a operação for concluída com êxito. |Sim |Sim |
| 6 |Política de cópia de segurança |Em determinadas circunstâncias raras, nas versões anteriores do software, havia um bug relacionado com a eliminação da política de cópia de segurança. Este problema é corrigido nesta versão. |Sim |Sim |

## <a name="known-issues-in-update-3"></a>Problemas conhecidos no Update 3
A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários / solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 está ligado à Internet resultante no quórum sem disco, em seguida, o agrupamento de armazenamento irá ficar offline. Ele irá permanecer offline, mesmo que os discos são a ligação restabelecidos. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 2 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, o controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada a partir do nó de mesmo nível, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, esse comportamento também pode ser visto após um reinício do sistema. |Não é necessária nenhuma ação do utilizador. Esta situação será resolvido automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 3 |Contas de armazenamento |Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isso levará a uma situação em que não não possível obter os dados de utilizador. | |Sim |Sim |
| 4 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. Ativação pós-falha de um dispositivo inativo único para vários dispositivos fará com que os contentores de volumes no primeiro efetuar a ativação pós-falha do dispositivo perder a propriedade dos dados. Após a ativação pós-falha, estes contentores de volume irão aparecer ou ter um comportamento diferente quando o utilizador vê-los no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o adaptador do StorSimple para instalação do SharePoint, terá de fornecer um IP do dispositivo para que a instalação concluir com êxito. | |Sim |Não |
| 6 |Proxy da Web |Se a sua configuração de proxy da web tiver HTTPS como o protocolo especificado, em seguida, a comunicação de serviço a dispositivo será afetada e o dispositivo irá ficar offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy de web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-8000-configure-web-proxy.md). |Sim |Não |
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
| 22 |Atualizações |Ao aplicar a atualização 3, o **manutenção** página no portal clássico do Azure irá apresentar a seguinte mensagem relacionadas com a atualização 2 - "StorSimple 8000 series Update 2 inclui a capacidade para a Microsoft para recolher proativamente de registo informações do seu dispositivo quando detectamos problemas potenciais". Isso é equivocado, pois indica que o dispositivo está a ser atualizado para a atualização 2. Depois do dispositivo é atualizado com êxito para a atualização 3, esta mensagem irá desaparecer. |Este comportamento será corrigido numa versão futura. |Sim |Não |

## <a name="controller-and-firmware-updates-in-update-3"></a>Atualizações de controlador e o firmware no Update 3
Esta versão tem LSI atualizações de controladores e firmware. Para obter mais informações sobre como instalar o controlador de LSI e atualizações de firmware, consulte [instalar atualização 3](storsimple-install-update-3.md) no dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Atualizações do dispositivo virtual no Update 3
Esta atualização não é possível aplicar a StorSimple Cloud Appliance (também conhecido como o dispositivo virtual). Novos dispositivos virtual tem de ser criado. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar atualização 3](storsimple-install-update-3.md) no dispositivo StorSimple.

