---
title: Administração de matriz Virtual do Microsoft Azure StorSimple Manager | Documentos da Microsoft
description: Saiba como gerir a sua matriz Virtual do StorSimple no local com o serviço StorSimple Device Manager no portal do Azure.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091873"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Utilizar o serviço StorSimple Device Manager para administrar a sua matriz Virtual StorSimple
![fluxo de processo de configuração](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Descrição geral
Este artigo descreve a interface de serviço do Gestor de dispositivos do StorSimple, incluindo como se conectar a ele e as várias opções disponíveis e fornece ligações para os fluxos de trabalho específicas que podem ser efetuadas por essa interface do Usuário.

Depois de ler este artigo, ficará a saber como:

* Ligar ao serviço StorSimple Device Manager
* Navegue até o Gestor de dispositivos do StorSimple da interface do Usuário
* Administrar a sua matriz Virtual do StorSimple através do serviço StorSimple Device Manager

> [!NOTE]
> Para ver as opções de gestão disponíveis para o dispositivo da série StorSimple 8000, aceda ao [utilizar o serviço StorSimple Manager para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ligar ao serviço StorSimple Device Manager
O serviço StorSimple Device Manager é executado no Microsoft Azure e liga-se a várias matrizes virtuais do StorSimple. Utilizar um portal central do Microsoft Azure em execução num browser para gerir estes dispositivos. Para ligar ao serviço StorSimple Device Manager, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço
1. Vá para [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Utilizar as credenciais da conta Microsoft, inicie sessão no portal do Microsoft Azure (localizado na parte superior direita do painel).
3. Navegar até procurar--> "Filtrar" no StorSimple Device Managers para ver todos os seus gestores de dispositivo numa determinada subscrição.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Utilizar o serviço StorSimple Device Manager para executar tarefas de gestão
A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados dentro do painel de resumo do serviço StorSimple Device Manager. Estas tarefas são organizadas com base nos painéis em que são iniciadas.

Para obter mais informações sobre cada fluxo de trabalho, clique o procedimento adequado na tabela.

#### <a name="storsimple-device-manager-workflows"></a>Fluxos de trabalho do StorSimple Device Manager
| Se quiser fazer isso... | Utilize este procedimento |
| --- | --- |
| Criar um serviço</br>Eliminar um serviço</br>Obter a chave de registo do serviço</br>Regenerar a chave de registo do serviço |[Implementar o serviço StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Ver os registos de atividade |[Utilize o resumo de serviço do StorSimple](storsimple-virtual-array-service-summary.md) |
| Desativar uma matriz Virtual</br>Eliminar uma matriz Virtual |[Desative ou elimine uma matriz virtual](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Ativação pós-falha de dispositivo e de recuperação de desastres</br>Pré-requisitos de ativação pós-falha</br>Recuperação de desastres de continuidade de negócio (BCDR)</br>Erros durante a recuperação após desastre |[Ativação pós-falha de dispositivo e de recuperação após desastre para a sua matriz Virtual StorSimple](storsimple-virtual-array-failover-dr.md) |
| Criar cópias de segurança compartilhamentos e volumes</br>Faça uma cópia de segurança manual</br>Alterar a agenda de cópia de segurança</br>Ver cópias de segurança existentes |[Criar cópias de segurança a sua matriz Virtual StorSimple](storsimple-virtual-array-backup.md) |
| Partilhas de clone a partir de um conjunto de cópia de segurança</br>Clonar volumes a partir de um conjunto de cópia de segurança</br>Ao nível do item de recuperação (apenas servidor de ficheiros) |[Clonar a partir de uma cópia de segurança da sua matriz Virtual do StorSimple](storsimple-virtual-array-clone.md) |
| Sobre as contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta do Storage |[Gerir contas de armazenamento para a matriz Virtual StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Sobre os registos de controlo de acesso</br>Adicionar ou modificar um registo de controlo de acesso </br>Eliminar um registo de controlo de acesso |[Gerir registos de controlo de acesso para a matriz Virtual StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Ver detalhes da tarefa |[Gerir tarefas de matriz Virtual StorSimple](storsimple-virtual-array-manage-jobs.md) |
| Configurar definições de alerta</br>Receber notificações de alerta</br>Gerir alertas</br>Rever alertas |[Ver e gerir alertas para a matriz Virtual StorSimple](storsimple-virtual-array-manage-alerts.md) |
| Modifique a palavra-passe de administrador do dispositivo |[Alterar a senha de administrador de dispositivo do StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Instalar atualizações de software |[Atualizar a sua matriz Virtual](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Tem de utilizar o [da IU web local](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
> 
> * [Obter a chave de encriptação de dados do serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Parar e reiniciar uma matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Para obter informações sobre a IU da web e como usá-lo, aceda a [utilizar a IU web do StorSimple para administrar a sua matriz Virtual StorSimple](storsimple-ova-web-ui-admin.md).

