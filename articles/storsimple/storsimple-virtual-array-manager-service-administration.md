---
title: Microsoft Azure StorSimple Manager Virtual Array administração / Microsoft Docs
description: Saiba como gerir o seu StorSimple no local Virtual Array utilizando o serviço StorSimple Device Manager no portal Azure.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 2395e3c6f2026bc1024de38122bc20959fdcf9b7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967096"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Utilize o serviço StorSimple Device Manager para administrar o seu StorSimple Virtual Array
![fluxo de processo de configuração](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Descrição geral
Este artigo descreve a interface de serviço StorSimple Device Manager, incluindo como ligar-se a ele e as várias opções disponíveis, e fornece links para os fluxos de trabalho específicos que podem ser realizados através deste UI.

Depois de ler este artigo, saberá como:

* Ligue-se ao serviço StorSimple Device Manager
* Navegue no UI do Gestor de Dispositivos StorSimple
* Administrar o seu StorSimple Virtual Array através do serviço StorSimple Device Manager

> [!NOTE]
> Para visualizar as opções de gestão disponíveis para o dispositivo da série StorSimple 8000, consulte [o serviço StorSimple Manager para administrar o seu dispositivo StorSimple](./storsimple-8000-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ligue-se ao serviço StorSimple Device Manager
O serviço StorSimple Device Manager funciona no Microsoft Azure e liga-se a várias Matrizes Virtuais StorSimple. Utiliza um portal central da Microsoft Azure a funcionar num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Vai [https://ms.portal.azure.com](https://ms.portal.azure.com) para.
2. Utilizando as credenciais de conta microsoft, inicie sessão no portal Microsoft Azure (localizado no topo direito do painel).
3. Navegue para Browse --> 'Filter' em StorSimple Device Managers para ver todos os seus gestores de dispositivos numa determinada subscrição.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Utilize o serviço StorSimple Device Manager para executar tarefas de gestão
A tabela a seguir mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser realizados dentro da lâmina de resumo do serviço StorSimple Device Manager. Estas tarefas são organizadas com base nas lâminas em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do Gestor de Dispositivos StorSimple
| Se quiser fazer isto... | Utilize este procedimento |
| --- | --- |
| Criar um serviço</br>Apagar um serviço</br>Obter a chave de registo do serviço</br>Regenerar a chave de registo de serviço |[Implementar o serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Ver os registos de atividade |[Utilize o resumo do serviço StorSimple](storsimple-virtual-array-service-summary.md) |
| Desativar uma Matriz Virtual</br>Excluir uma Matriz Virtual |[Desativar ou eliminar uma matriz virtual](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Recuperação de desastres e falha do dispositivo</br>Pré-requisitos de failover</br>Recuperação de desastres de continuidade do negócio (BCDR)</br>Erros durante a recuperação de desastres |[Recuperação de desastres e falha no dispositivo para o seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Apoio de ações e volumes</br>Pegue uma cópia de segurança manual</br>Alterar o horário de backup</br>Ver backups existentes |[Apoie o seu StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Clonar partilhas a partir de um conjunto de cópias de segurança</br>Clonar volumes a partir de um conjunto de cópias de segurança</br>Recuperação do nível de item (apenas servidor de ficheiros) |[Clone a partir de uma cópia de segurança do seu StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage |[Gerir contas de armazenamento para o StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| Sobre registos de controlo de acessos</br>Adicionar ou modificar um registo de controlo de acesso </br>Apagar um registo de controlo de acesso |[Gerir registos de controlo de acesso para o StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Ver detalhes da tarefa |[Gerir trabalhos de Matriz Virtual StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Configurar as definições de alertas</br>Receber notificações de alerta</br>Gerir alertas</br>Consultar alertas |[Ver e gerir alertas para o StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Modificar a palavra-passe do administrador do dispositivo |[Alterar a palavra-passe do administrador do dispositivo StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalar atualizações de software |[Atualize o seu Array Virtual](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Deve utilizar a [UI web local](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
> 
> * [Recuperar a chave de encriptação de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Parar e reiniciar uma Matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre a UI web e como usá-lo, vá ao [Uso da UI web StorSimple para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).