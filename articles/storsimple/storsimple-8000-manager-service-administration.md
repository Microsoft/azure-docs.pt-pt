---
title: Administração do serviço StorSimple Device Manager | Documentos da Microsoft
description: Saiba como gerir o dispositivo StorSimple através do serviço StorSimple Device Manager no portal do Azure.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723311"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve a interface de serviço do Gestor de dispositivos do StorSimple, incluindo como se conectar a ele, as várias opções disponíveis e ligações para os fluxos de trabalho específicas que podem ser efetuadas por essa interface do Usuário. Esta orientação se aplica a ambos; o dispositivo físico StorSimple e a aplicação da cloud.

Depois de ler este artigo, irá aprender a:

* Ligar ao serviço StorSimple Device Manager
* Administrar o seu dispositivo StorSimple através do serviço StorSimple Device Manager

## <a name="connect-to-storsimple-device-manager-service"></a>Ligar ao serviço StorSimple Device Manager

O serviço StorSimple Device Manager é executado no Microsoft Azure e liga-se a diversos dispositivos StorSimple. Utilizar um portal central do Microsoft Azure em execução num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Navegue para [ https://portal.azure.com/ ](https://portal.azure.com/).
2. Utilizar as credenciais da conta Microsoft, inicie sessão no portal do Microsoft Azure (localizado na parte superior direita do painel).
3. Role para baixo no painel de navegação à esquerda para aceder ao serviço StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrar o dispositivo com StorSimple através do serviço StorSimple Device Manager

A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados no serviço StorSimple Device Manager da interface do Usuário. Estas tarefas são organizadas com base nos painéis de interface do Usuário no qual são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique o procedimento adequado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do StorSimple Device Manager

| Se quiser fazer isso... | Utilize este procedimento. |
| --- | --- |
| Criar um serviço</br>Eliminar um serviço</br>Obter chave de registo do serviço</br>Regenerar chave de registo do serviço |[Implementar um serviço StorSimple Device Manager](storsimple-8000-manage-service.md) |
| Ver os registos de atividade |[Utilize o resumo do serviço StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Alterar a chave de encriptação de dados do serviço</br>Ver os registos de operação |[Utilize o dashboard do serviço StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Desativar um dispositivo</br>Eliminar um dispositivo |[Desativar ou eliminar um dispositivo](storsimple-8000-deactivate-and-delete-device.md) |
| Saiba mais sobre a ativação pós-falha de dispositivo e de recuperação de desastres</br>Ativação pós-falha para um dispositivo físico</br>Ativação pós-falha para um dispositivo virtual</br>Recuperação de desastres de continuidade de negócio (BCDR) |[Ativação pós-falha e recuperação após desastre para o dispositivo StorSimple](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista as cópias de segurança para um volume</br>Selecione um conjunto de cópias de segurança</br>Eliminar um conjunto de cópia de segurança |[Gerir cópias de segurança](storsimple-8000-manage-backup-catalog.md) |
| Clonar um volume |[Clonar um volume](storsimple-8000-clone-volume-u2.md) |
| Restaurar um conjunto de cópias de segurança |[Restaurar um conjunto de cópias de segurança](storsimple-8000-restore-from-backup-set-u2.md) |
| Sobre as contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage</br>Rotação de chaves de contas de armazenamento |[Gerir contas de armazenamento](storsimple-8000-manage-storage-accounts.md) |
| Sobre os modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Eliminar um modelo de largura de banda</br>Utilizar um modelo de largura de banda predefinido</br>Criar um modelo de largura de banda de dia inteiro que começa num tempo especificado |[Gerir modelos de largura de banda](storsimple-8000-manage-bandwidth-templates.md) |
| Sobre os registos de controlo de acesso</br>Criar um registo de controlo de acesso</br>Editar um registo de controlo de acesso</br>Eliminar um registo de controlo de acesso |[Gerir registos de controlo de acesso](storsimple-8000-manage-acrs.md) |
| Ver detalhes da tarefa</br>Cancelar uma tarefa |[Gerir tarefas](storsimple-8000-manage-jobs-u2.md) |
| Receber notificações de alerta</br>Gerir alertas</br>Rever alertas |[Ver e gerir alertas do StorSimple](storsimple-8000-manage-alerts.md) |
| Criar gráficos de monitorização |[Monitorizar o dispositivo StorSimple](storsimple-monitor-device.md) |
| Adicionar um contentor de volumes</br>Modificar um contentor de volume</br>Eliminar um contentor de volumes |[Gerir contentores de volumes](storsimple-8000-manage-volume-containers.md) |
| Adicionar um volume</br>Modificar um volume</br>Colocar um volume offline</br>Eliminar um volume</br>Monitorizar um número |[Gerir volumes](storsimple-8000-manage-volumes-u2.md) |
| Modificar definições do dispositivo</br>Modificar as definições de hora</br>Modificar as definições de DNS.md</br>Configurar as interfaces de rede |[Modificar configuração do dispositivo para o dispositivo StorSimple](storsimple-8000-modify-device-config.md) |
| Ver definições de proxy da web |[Configurar o proxy web para o seu dispositivo](storsimple-8000-configure-web-proxy.md) |
| Modifique a palavra-passe de administrador do dispositivo</br>Modifique a palavra-passe do Snapshot Manager do StorSimple |[Alterar palavras-passe do StorSimple](storsimple-8000-change-passwords.md) |
| Configurar a gestão remota |[Ligar remotamente ao dispositivo StorSimple](storsimple-8000-remote-connect.md) |
| Configurar definições de alerta |[Ver e gerir alertas do StorSimple](storsimple-8000-manage-alerts.md) |
| Configurar o CHAP para o dispositivo StorSimple |[Configurar o CHAP para o dispositivo StorSimple](storsimple-configure-chap.md) |
| Adicionar uma política de cópias de segurança</br>Adicionar ou modificar uma agenda</br>Eliminar uma política de cópia de segurança</br>Faça uma cópia de segurança manual</br>Criar uma política de cópia de segurança personalizada com vários volumes e agendas |[Gerir políticas de cópia de segurança](storsimple-8000-manage-backup-policies-u2.md) |
| Parar os controladores de dispositivo</br>Reiniciar os controladores de dispositivo</br>Encerrar os controladores de dispositivo</br>Repor o dispositivo para as predefinições de fábrica</br>(Acima destinam-se apenas a dispositivos no local) |[Gerir o controlador de dispositivo do StorSimple](storsimple-8000-manage-device-controller.md) |
| Saiba mais sobre os componentes de hardware do StorSimple</br>Monitorizar o estado de hardware</br>(Acima destinam-se apenas a dispositivos no local) |[Componentes de hardware do monitor](storsimple-8000-monitor-hardware-status.md) |
| Criar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Instalar atualizações de software |[Atualizar o seu dispositivo](storsimple-update-device.md) |

## <a name="next-steps"></a>Passos Seguintes

Se encontrar algum problema com a operação diária do seu dispositivo StorSimple ou com qualquer um dos respetivos componentes de hardware, consulte:

* [Resolver problemas com a ferramenta de diagnóstico](storsimple-8000-diagnostics.md)
* [Utilizar o StorSimple indicador LEDs de monitorização](storsimple-monitoring-indicators.md)

Se não consegue resolver os problemas e tem de criar um pedido de serviço, consulte [contacte o suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).

