---
title: Microsoft Azure StorSimple Manager Virtual Array administração [ Microsoft Azure StorSimple Manager Virtual Array] Microsoft Docs
description: Saiba como gerir o seu StorSimple on-premises Virtual Array utilizando o serviço StorSimple Device Manager no portal Azure.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "62123810"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Utilize o serviço StorSimple Device Manager para administrar o seu StorSimple Virtual Array
![fluxo de processo de configuração](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Descrição geral
Este artigo descreve a interface de serviço StorSimple Device Manager, incluindo como ligar-se a ele e as várias opções disponíveis, e fornece links para os fluxos de trabalho específicos que podem ser realizados através deste UI.

Depois de ler este artigo, saberá como:

* Ligue-se ao serviço StorSimple Device Manager
* Navegue no StorSimple Device Manager UI
* Administrar o seu StorSimple Virtual Array através do serviço StorSimple Device Manager

> [!NOTE]
> Para ver as opções de gestão disponíveis para o dispositivo da série StorSimple 8000, vá utilizar [o serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ligue-se ao serviço StorSimple Device Manager
O serviço StorSimple Device Manager funciona no Microsoft Azure e conecta-se a várias Matrizes Virtuais StorSimple. Usa um portal central do Microsoft Azure a funcionar num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Vai [https://ms.portal.azure.com](https://ms.portal.azure.com)para.
2. Utilizando as credenciais da sua conta Microsoft, inicie sessão no portal Microsoft Azure (localizado no canto superior direito do painel).
3. Navegue para navegar --> 'Filter' em StorSimple Device Managers para visualizar todos os gestores do seu dispositivo numa determinada subscrição.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Utilize o serviço StorSimple Device Manager para executar tarefas de gestão
A tabela seguinte mostra um resumo de todas as tarefas comuns de gestão e fluxos de trabalho complexos que podem ser realizados dentro da lâmina de resumo do serviço StorSimple Device Manager. Estas tarefas são organizadas com base nas lâminas em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique no procedimento adequado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do Gestor de Dispositivos StorSimple
| Se quiser fazer isto... | Use este procedimento |
| --- | --- |
| Criar um serviço</br>Apagar um serviço</br>Obter a chave de registo do serviço</br>Regenerar a chave de registo de serviço |[Implementar o serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Ver os registos de atividade |[Utilize o resumo do serviço StorSimple](storsimple-virtual-array-service-summary.md) |
| Desativar uma Matriz Virtual</br>Eliminar um Matriz Virtual |[Desativar ou eliminar uma matriz virtual](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Recuperação de desastres e falha no dispositivo</br>Pré-requisitos de failover</br>Recuperação de desastres de continuidade de negócios (BCDR)</br>Erros durante a recuperação de desastres |[Recuperação de desastres e falha no dispositivo para o seu StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Back up shares and volumes</br>Pegue uma cópia de segurança manual</br>Alterar o horário de backup</br>Ver backups existentes |[Volte a subir o seu StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Clonar partilhas a partir de um conjunto de cópias de segurança</br>Clonar volumes a partir de um conjunto de cópias de segurança</br>Recuperação ao nível do item (apenas servidor de ficheiros) |[Clone a partir de uma cópia de segurança do seu StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Sobre contas de armazenamento</br>Adicione uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage |[Gerir contas de armazenamento para o StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| Sobre registos de controlo de acesso</br>Adicione ou modifique um registo de controlo de acesso </br>Eliminar um registo de controlo de acesso |[Gerir os registos de controlo de acesso para o StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Ver detalhes da tarefa |[Gerir trabalhos de matriz virtual StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Configurar definições de alerta</br>Receber notificações de alerta</br>Gerir alertas</br>Rever alertas |[Ver e gerir alertas para o StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Modificar a palavra-passe do administrador do dispositivo |[Alterar a palavra-passe do administrador do dispositivo StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalar atualizações de software |[Atualize o seu Matriz Virtual](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Deve utilizar a [UI web local](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
> 
> * [Recuperar a chave de encriptação de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Criar um pacote de apoio](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Parar e reiniciar uma Matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o UI web e como usá-lo, vá utilizar [o StorSimple web UI para administrar o seu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

