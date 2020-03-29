---
title: Administração de serviços StorSimple Device Manager [ StorSimple Device Manager] Microsoft Docs
description: Aprenda a gerir o seu dispositivo StorSimple utilizando o serviço StorSimple Device Manager no portal Azure.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723311"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Utilize o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve a interface de serviço StorSimple Device Manager, incluindo como ligar-se ao mesmo, as várias opções disponíveis, e liga-se aos fluxos de trabalho específicos que podem ser realizados através deste UI. Esta orientação é aplicável a ambos; o dispositivo físico StorSimple e o aparelho de nuvem.

Depois de ler este artigo, aprenderá a:

* Ligue-se ao serviço StorSimple Device Manager
* Administrar o seu dispositivo StorSimple através do serviço StorSimple Device Manager

## <a name="connect-to-storsimple-device-manager-service"></a>Ligue-se ao serviço StorSimple Device Manager

O serviço StorSimple Device Manager funciona no Microsoft Azure e conecta-se a vários dispositivos StorSimple. Usa um portal central do Microsoft Azure a funcionar num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Navegar [https://portal.azure.com/](https://portal.azure.com/)para.
2. Utilizando as credenciais da sua conta Microsoft, inicie sessão no portal Microsoft Azure (localizado no canto superior direito do painel).
3. Percorra o painel de navegação esquerdo para aceder ao serviço StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrar dispositivo StorSimple utilizando o serviço StorSimple Device Manager

A tabela seguinte mostra um resumo de todas as tarefas comuns de gestão e fluxos de trabalho complexos que podem ser realizados dentro do serviço StorSimple Device Manager UI. Estas tarefas são organizadas com base nas lâminas ui nas quais são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento adequado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do Gestor de Dispositivos StorSimple

| Se quiser fazer isto... | Use este procedimento. |
| --- | --- |
| Criar um serviço</br>Apagar um serviço</br>Obtenha a chave de registo de serviço</br>Chave de registo de serviço regeneração |[Implementar um serviço StorSimple Device Manager](storsimple-8000-manage-service.md) |
| Ver os registos de atividade |[Utilize o resumo do serviço StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Alterar a chave de encriptação de dados do serviço</br>Ver os registos de operação |[Utilize o painel de assistência storSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Desativar um dispositivo</br>Eliminar um dispositivo |[Desativar ou eliminar um dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Saiba mais sobre recuperação de desastres e falha de dispositivo</br>Falha num dispositivo físico</br>Falha num dispositivo virtual</br>Recuperação de desastres de continuidade de negócios (BCDR) |[Falha e recuperação de desastres para o seu dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista rés-cópias de segurança para um volume</br>Selecione um conjunto de backup</br>Eliminar um conjunto de backup |[Gerir cópias de segurança](storsimple-8000-manage-backup-catalog.md) |
| Clonar um volume |[Clonar um volume](storsimple-8000-clone-volume-u2.md) |
| Restaurar um conjunto de backup |[Restaurar um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md) |
| Sobre contas de armazenamento</br>Adicione uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage</br>Rotação chave das contas de armazenamento |[Gerir contas de armazenamento](storsimple-8000-manage-storage-accounts.md) |
| Sobre modelos de largura de banda</br>Adicione um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Eliminar um modelo de largura de banda</br>Use um modelo de largura de banda padrão</br>Crie um modelo de largura de banda durante todo o dia que começa num momento especificado |[Gerir modelos de largura de banda](storsimple-8000-manage-bandwidth-templates.md) |
| Sobre registos de controlo de acesso</br>Criar um registo de controlo de acesso</br>Editar um registo de controlo de acesso</br>Eliminar um registo de controlo de acesso |[Gerir registos de controlo de acesso](storsimple-8000-manage-acrs.md) |
| Ver detalhes da tarefa</br>Cancelar um trabalho |[Gerir tarefas](storsimple-8000-manage-jobs-u2.md) |
| Receber notificações de alerta</br>Gerir alertas</br>Rever alertas |[Ver e gerir alertas StorSimple](storsimple-8000-manage-alerts.md) |
| Criar gráficos de monitorização |[Monitorize o seu dispositivo StorSimple](storsimple-monitor-device.md) |
| Adicione um recipiente de volume</br>Modificar um recipiente de volume</br>Eliminar um recipiente de volume |[Gerir contentores de volumes](storsimple-8000-manage-volume-containers.md) |
| Adicione um volume</br>Modificar um volume</br>Pegue um volume offline</br>Eliminar um volume</br>Monitorizar um volume |[Gerir volumes](storsimple-8000-manage-volumes-u2.md) |
| Modificar as definições do dispositivo</br>Modificar as definições de tempo</br>Modificar as configurações DNS.md</br>Configurar interfaces de rede |[Modificar a configuração do dispositivo para o seu dispositivo StorSimple](storsimple-8000-modify-device-config.md) |
| Ver definições de procuração web |[Configure procuração web para o seu dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modificar a palavra-passe do administrador do dispositivo</br>Modificar a palavra-passe do StorSimple Snapshot Manager |[Alterar palavras-passe StorSimple](storsimple-8000-change-passwords.md) |
| Configurar a gestão remota |[Ligue-se remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Configurar definições de alerta |[Ver e gerir alertas StorSimple](storsimple-8000-manage-alerts.md) |
| Configure CHAP para o seu dispositivo StorSimple |[Configure CHAP para o seu dispositivo StorSimple](storsimple-configure-chap.md) |
| Adicionar uma política de cópias de segurança</br>Adicionar ou modificar um horário</br>Eliminar uma política de backup</br>Pegue uma cópia de segurança manual</br>Criar uma política de backup personalizada com vários volumes e horários |[Gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md) |
| Parar controladores de dispositivos</br>Reiniciar controladores de dispositivos</br>Desligue os controladores do dispositivo</br>Redefinir o seu dispositivo para incumprimentos de fábrica</br>(Acima estão apenas para dispositivos no local) |[Gerir o controlador de dispositivoS StorSimple](storsimple-8000-manage-device-controller.md) |
| Saiba mais sobre componentes de hardware StorSimple</br>Monitorizar o estado do hardware</br>(Acima estão apenas para dispositivos no local) |[Monitorizar componentes de hardware](storsimple-8000-monitor-hardware-status.md) |
| Criar um pacote de apoio |[Criar e gerir um pacote de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalar atualizações de software |[Atualizar o seu dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema com o funcionamento do dia-a-dia do seu dispositivo StorSimple ou com qualquer um dos seus componentes de hardware, consulte:

* [Resolução de problemas utilizando a ferramenta Diagnósticos](storsimple-8000-diagnostics.md)
* [Utilize LEDs indicadores de monitorização StorSimple](storsimple-monitoring-indicators.md)

Se não conseguir resolver os problemas e precisar de criar um pedido de serviço, consulte o [Contact E.I.](storsimple-8000-contact-microsoft-support.md)

