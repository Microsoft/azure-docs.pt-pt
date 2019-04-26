---
title: Notas de versão do StorSimple 8000 Series Update 2 | Documentos da Microsoft
description: Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f23a507ab631be553613e22cafa037291548a8aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60530858"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de versão do StorSimple 8000 Series Update 2

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem os novos recursos e identificar os problemas em aberto críticos para a StorSimple 8000 Series Update 2. Também contêm uma lista do software do StorSimple, o controlador e as atualizações de firmware do disco incluídas nesta versão. 

Atualização 2 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA) ou atualização 0.1 por meio de atualização 1.2. A versão de dispositivo associada a atualização 2 é 6.3.9600.17673.

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple.

> [!IMPORTANT]
> * Demora cerca de 4 a 7 horas para instalar esta atualização (incluindo as atualizações do Windows). 
> * Atualização 2 tem o software, o driver de LSI e atualizações de firmware SSD.
> * Para novas versões, poderá não ver atualizações imediatamente porque fazemos uma implementação faseada de atualizações. Aguarde alguns dias e, em seguida, análise para atualizações novamente como estes ficarão disponível em breve.
> 
> 

## <a name="whats-new-in-update-2"></a>O que há de novo na atualização 2
Atualização 2 apresenta as seguintes novas funcionalidades.

* **Volumes afixados localmente** – em versões anteriores da série StorSimple 8000, blocos de dados foram em camadas para a cloud com base na utilização. Não ocorreu nenhuma forma de garantir que os blocos seriam permanecem no local. Na atualização 2, quando cria um volume, pode designar um volume como dados de nesse volume afixados localmente e primários não irão ser colocado em camadas para a cloud. Instantâneos de volumes localmente afixados ainda serão copiados para a cloud para cópia de segurança para que a cloud pode ser utilizada para fins de recuperação de desastre e mobilidade de dados. Além disso, pode alterar o tipo de volume (ou seja, converter em camadas volumes para volumes afixados localmente e volumes de convert afixado localmente para em camadas). 
* **Melhorias de dispositivo virtual StorSimple** – anteriormente, a série StorSimple 8000 posicionado o dispositivo virtual como uma solução de desenvolvimento/teste ou de recuperação após desastre. Não havia apenas um modelo do dispositivo virtual (modelo 1100). Atualização 2 apresenta dois modelos de dispositivo virtual: 
  
  * 8010 (anteriormente denominadas 1100) – sem alteração; Tem a capacidade de 30 TB e utiliza o armazenamento standard do Azure.
  * 8020 – tem a capacidade de 64 TB e utiliza o armazenamento Premium do Azure para um melhor desempenho.
    
    Há um único VHD para ambos os modelos de dispositivo virtual (8010/8020). Ao iniciar o dispositivo virtual, Deteta os parâmetros de plataforma e aplica-se a versão do modelo correto.
* **Melhorias de funcionamento em rede** – atualização 2 contém as seguintes melhorias de rede:
  
  * Várias NICs podem ser ativadas para a cloud, para que o failover pode ocorrer se um NIC falhar.
  * Aprimoramentos de encaminhamento, com a métrica fixo para cloud ativada blocos.
  * Repita online de recursos com falha antes de uma ativação pós-falha.
  * Novos alertas para falhas de serviço.
* **A atualizar melhorias** – atualizar 1.2 e anteriormente, a série 8000 do StorSimple foi atualizada por meio de dois canais: Atualização do Windows para clustering, o iSCSI e assim por diante e Microsoft Update para binários e firmware.
    Atualização 2 utiliza o Microsoft Update para todos os pacotes de atualização. Isso deve resultar em menos tempo a aplicação de patches ou fazendo as ativações pós-falha. 
* **Atualizações de firmware** – o firmware ao seguir as atualizações estão incluídas:
  
  * LSI: lsi_sas2.sys 2.00.72.10 de versão do produto
  * SSD apenas (não existem atualizações HDD): XMGG, XGEG, KZ50, F6C2 e VR08
* **Suporte pró-ativo** – atualização 2 permite que a Microsoft obter informações de diagnóstico adicionais do dispositivo. Quando a nossa equipa de operações identifica dispositivos que estão a ter problemas, estamos melhor equipados recolher informações do dispositivo e diagnosticar problemas. **Ao aceitar o Update 2, nos permite fornecer esse suporte proativo**.    

## <a name="issues-fixed-in-update-2"></a>Problemas corrigidos na atualização 2
As tabelas a seguir fornece um resumo dos problemas que foram corrigidas em 2 de atualizações.    

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Após uma atualização para atualização 1, o serviço StorSimple Manager reportou que as portas de Data2 e Data3 falhou num controlador. Este problema foi corrigido. |Sim |Não |
| 2 |Atualizações |Após uma atualização para atualização 1, os alertas de alarme sonoro ocorreu no portal clássico do Azure em vários dispositivos. Este problema foi corrigido. |Sim |Não |
| 3 |Autenticação do Openstack |Quando utiliza o Openstack como o fornecedor de serviços cloud, pode receber um erro que sua cadeia de caracteres de autenticação em nuvem era demasiado longa. Isto foi corrigido. |Sim |Não |

## <a name="known-issues-in-update-2"></a>Problemas conhecidos na atualização 2
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
| 20 |volumes afixados localmente |Se tentar converter um volume em camadas (criado e clonado com atualização 1.2 ou anterior) para um volume afixado localmente e o dispositivo está a ficar sem espaço ou uma falha de cloud, o clone(s) pode estar danificado. |Este problema ocorre apenas com volumes que foram criados e clonado com pré-atualização 2 de software. Deve ser um cenário de pouco frequente. | | |
| 21 |Conversão do volume |Não atualizar ACRs anexados a um volume enquanto uma conversão do volume está em curso (em camadas para localmente afixado ou vice-versa). A atualizar os ACRs pode resultar em danos em dados. |Se for necessário, atualize os ACRs antes da conversão do volume e não fazem qualquer outra atualizações ACR enquanto a conversão está em curso. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Atualizações de controlador e firmware na atualização 2
Esta versão atualiza o driver e o firmware do disco no seu dispositivo.

* Para obter mais informações sobre o firmware de LSI de atualização, consulte o artigo da base de dados de conhecimento da Microsoft 3121900. 
* Para obter mais informações sobre o firmware do disco de atualização, consulte o artigo da base de dados de conhecimento da Microsoft 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Atualizações do dispositivo virtual na atualização 2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtual tem de ser criado. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar atualização 2](storsimple-install-update-2.md) no dispositivo StorSimple.

