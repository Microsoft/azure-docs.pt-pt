---
title: Administração de serviços StorSimple Device Manager | Microsoft Docs
description: Saiba como gerir o seu dispositivo StorSimple utilizando o serviço StorSimple Device Manager no portal Azure.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656826"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Utilize o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple

## <a name="overview"></a>Descrição Geral

Este artigo descreve a interface de serviço StorSimple Device Manager, incluindo como ligar-se a ele, as várias opções disponíveis, e liga-se aos fluxos de trabalho específicos que podem ser realizados através deste UI. Esta orientação é aplicável a ambos; o dispositivo físico StorSimple e o aparelho em nuvem.

Depois de ler este artigo, aprenderá a:

* Ligue-se ao serviço StorSimple Device Manager
* Administrar o seu dispositivo StorSimple através do serviço StorSimple Device Manager

## <a name="connect-to-storsimple-device-manager-service"></a>Ligue-se ao serviço StorSimple Device Manager

O serviço StorSimple Device Manager funciona no Microsoft Azure e liga-se a vários dispositivos StorSimple. Utiliza um portal central da Microsoft Azure a funcionar num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Navegue para [https://portal.azure.com/](https://portal.azure.com/) .
2. Utilizando as credenciais de conta microsoft, inicie sessão no portal Microsoft Azure (localizado no topo direito do painel).
3. Percorra o painel de navegação esquerdo para aceder ao serviço StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrar dispositivo StorSimple utilizando o serviço StorSimple Device Manager

A tabela a seguir mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser realizados dentro do serviço StorSimple Device Manager UI. Estas tarefas são organizadas com base nas lâminas de UI em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do Gestor de Dispositivos StorSimple

| Se quiser fazer isto... | Use este procedimento. |
| --- | --- |
| Criar um serviço</br>Apagar um serviço</br>Obtenha a chave de registo de serviço</br>Chave de registo de serviço regenerador |[Implementar um serviço StorSimple Device Manager](storsimple-8000-manage-service.md) |
| Ver os registos de atividade |[Utilize o resumo do serviço StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Alterar a chave de encriptação de dados de serviço</br>Ver os registos de operação |[Utilize o painel de assistência StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Desativar um dispositivo</br>Eliminar um dispositivo |[Desativar ou apagar um dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Conheça a recuperação de desastres e falha no dispositivo</br>Falha num dispositivo físico</br>Falha num dispositivo virtual</br>Recuperação de desastres de continuidade do negócio (BCDR) |[Falha e recuperação de desastres para o seu dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista de backups para um volume</br>Selecione um conjunto de backup</br>Excluir um conjunto de backup |[Gerir cópias de segurança](storsimple-8000-manage-backup-catalog.md) |
| Clonar um volume |[Clonar um volume](storsimple-8000-clone-volume-u2.md) |
| Restaurar um conjunto de backup |[Restaurar um conjunto de backup](storsimple-8000-restore-from-backup-set-u2.md) |
| Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage</br>Rotação chave das contas de armazenamento |[Gerir contas de armazenamento](storsimple-8000-manage-storage-accounts.md) |
| Sobre modelos de largura de banda</br>Adicione um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Elimine um modelo de largura de banda</br>Use um modelo de largura de banda padrão</br>Crie um modelo de largura de banda durante todo o dia que comece num momento especificado |[Gerir modelos de largura de banda](storsimple-8000-manage-bandwidth-templates.md) |
| Sobre registos de controlo de acessos</br>Criar um registo de controlo de acesso</br>Editar um registo de controlo de acesso</br>Apagar um registo de controlo de acesso |[Gerir registos de controlo de acessos](storsimple-8000-manage-acrs.md) |
| Ver detalhes da tarefa</br>Cancelar um emprego |[Gerir tarefas](storsimple-8000-manage-jobs-u2.md) |
| Receber notificações de alerta</br>Gerir alertas</br>Consultar alertas |[Ver e gerir alertas StorSimple](storsimple-8000-manage-alerts.md) |
| Criar gráficos de monitorização |[Monitorize o seu dispositivo StorSimple](./storsimple-8000-monitor-device.md) |
| Adicione um recipiente de volume</br>Modificar um recipiente de volume</br>Apagar um recipiente de volume |[Gerir contentores de volumes](storsimple-8000-manage-volume-containers.md) |
| Adicione um volume</br>Modificar um volume</br>Desative o volume</br>Apagar um volume</br>Monitorize um volume |[Gerir volumes](storsimple-8000-manage-volumes-u2.md) |
| Modificar as definições do dispositivo</br>Modificar as definições de tempo</br>Modificar as definições DNS.md</br>Configure interfaces de rede |[Modifique a configuração do dispositivo para o seu dispositivo StorSimple](storsimple-8000-modify-device-config.md) |
| Ver configurações de procuração web |[Configure o representante web para o seu dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modificar a palavra-passe do administrador do dispositivo</br>Modificar a palavra-passe do Gestor instantâneo StorSimple |[Alterar palavras-passe StorSimple](storsimple-8000-change-passwords.md) |
| Configure a gestão remota |[Ligue-se remotamente ao seu dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Configurar as definições de alertas |[Ver e gerir alertas StorSimple](storsimple-8000-manage-alerts.md) |
| Configure CHAP para o seu dispositivo StorSimple |[Configure CHAP para o seu dispositivo StorSimple](./storsimple-8000-configure-chap.md) |
| Adicionar uma política de cópias de segurança</br>Adicionar ou modificar um horário</br>Excluir uma política de backup</br>Pegue uma cópia de segurança manual</br>Crie uma política de backup personalizada com vários volumes e horários |[Gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md) |
| Parar controladores de dispositivos</br>Reiniciar controladores de dispositivos</br>Desligar controladores de dispositivos</br>Repôs o seu dispositivo em padrão de fábrica</br>(Acima são apenas para dispositivos no local) |[Gerir o controlador de dispositivo storSimple](storsimple-8000-manage-device-controller.md) |
| Saiba mais sobre os componentes de hardware StorSimple</br>Monitorizar o estado de hardware</br>(Acima são apenas para dispositivos no local) |[Monitorize componentes de hardware](storsimple-8000-monitor-hardware-status.md) |
| Criar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalar atualizações de software |[Atualizar o seu dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema com o funcionamento diário do seu dispositivo StorSimple ou com qualquer um dos seus componentes de hardware, consulte:

* [Resolução de problemas utilizando a ferramenta De diagnóstico](storsimple-8000-diagnostics.md)
* [Utilizar LED indicadores de monitorização StorSimple](storsimple-monitoring-indicators.md)

Se não conseguir resolver os problemas e necessitar de criar um pedido de serviço, consulte [o Contact Microsoft Support](storsimple-8000-contact-microsoft-support.md).