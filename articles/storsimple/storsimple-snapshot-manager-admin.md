---
title: Administração de Snapshot Manager do StorSimple | Microsoft Docs
description: Fornece uma visão geral e links para mais informações sobre fluxos de trabalho e tarefas de administração de solução do StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: twooley
ms.openlocfilehash: b8fe87a2df633af310bc661308813a60987e77d6
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933284"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Usar o Snapshot Manager StorSimple para administrar sua solução StorSimple

## <a name="overview"></a>Visão geral
O StorSimple Snapshot Manager é um snap-in do MMC (console de gerenciamento Microsoft) que simplifica a proteção de dados e o gerenciamento de backup em um ambiente de Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, você pode gerenciar Microsoft Azure StorSimple dados no data center e na nuvem como uma solução única de armazenamento integrado, simplificando assim os processos de backup e reduzindo os custos.

O console de gerenciamento central do StorSimple Snapshot Manager permite que você crie cópias de backup consistentes e pontuais de dados locais e na nuvem. Por exemplo, você pode usar o console do para:

* Configurar, fazer backup e excluir volumes.
* Configure grupos de volumes para garantir que os dados de backup sejam consistentes com o aplicativo.
* Gerencie políticas de backup para que os dados sejam copiados em um agendamento predeterminado.
* Crie cópias independentes de dados, que podem ser armazenadas na nuvem e usadas para recuperação de desastres.

Este artigo fornece links para tutoriais que descrevem o Snapshot Manager do StorSimple e como usá-lo para concluir tarefas de administração do sistema e fluxos de trabalho.

* Para obter mais informações sobre componentes e arquitetura do StorSimple Snapshot Manager, consulte [o que é o snapshot Manager storsimple?](storsimple-what-is-snapshot-manager.md) 
* Para baixar o Snapshot Manager do StorSimple, vá para [a página de download do storsimple snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Para os procedimentos de implantação do StorSimple Snapshot Manager, vá para [implantar o storsimple snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Você não pode usar o StorSimple Snapshot Manager para gerenciar Microsoft Azure StorSimple matrizes virtuais (também conhecidas como dispositivos virtuais locais do StorSimple).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Tarefas e fluxos de trabalho do StorSimple Snapshot Manager
Você pode usar o Snapshot Manager do StorSimple para monitorar e gerenciar trabalhos de backup atuais, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 backups concluídos. Você pode usar o catálogo para localizar e restaurar volumes ou arquivos individuais. 

| SE VOCÊ QUISER FAZER ISSO... | USE ESTE TUTORIAL... |
|:--- |:--- |
| Saiba mais sobre o StorSimple Snapshot Manager |[O que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) |
| Instalar o StorSimple Snapshot Manager<br>Reinstalar o StorSimple Snapshot Manager<br>Remover Snapshot Manager StorSimple |[Implantar Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md) |
| Use os menus e recursos do StorSimple Snapshot Manager:<ul><li>Barra de menus</li><li>Barra de ferramentas</li><li>Painel escopo</li><li>Painel de resultados</li><li>painel Actions</li><li>Navegação por teclado e atalhos</li></ul> |[Interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Use os recursos comuns do MMC incluídos no StorSimple Snapshot Manager:<ul><li>Vista</li><li>Nova Janela a Partir Daqui</li><li>Atualizar</li><li>Exportar lista</li><li>Ajuda</li></ul> |[Usar as ações de menu do MMC no StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Adicionar ou substituir um dispositivo<br>Ligar um dispositivo<br>Verificar grupos de volumes importados<br>Atualizar dispositivos conectados<br>Autenticar um dispositivo<br>Ver detalhes do dispositivo<br>Excluir uma configuração de dispositivo<br>Alterar uma senha de dispositivo<br>Substituir um dispositivo com falha<br> |[Usar o Snapshot Manager StorSimple para conectar e gerenciar dispositivos StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Montar volumes<br>Exibir informações sobre volumes<br>Excluir um volume<br>Examinar volumes novamente<br>Configurar e fazer backup de um volume básico<br>Configurar e fazer backup de um volume espelhado dinâmico |[Usar o Snapshot Manager do StorSimple para exibir e gerenciar volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Exibir grupos de volumes<br>Criar um grupo de volumes<br>Fazer backup de um grupo de volumes<br>Editar um grupo de volumes<br>Excluir um grupo de volumes |[Usar o Snapshot Manager do StorSimple para criar e gerenciar grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md) |
| Criar uma política de backup <br>Editar uma política de backup<br>Excluir uma política de backup |[Usar o Snapshot Manager do StorSimple para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md) |
| Exibir e gerenciar trabalhos de backup agendados<br>Exibir e gerenciar trabalhos de backup recentes<br>Exibir e gerenciar trabalhos de backup em execução no momento |[Usar o Snapshot Manager do StorSimple para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurar um volume<br>Clonar um volume ou grupo de volumes<br>Excluir um backup<br>Recuperar um ficheiro<br>Restaurar o banco de dados Snapshot Manager do StorSimple |[Usar o StorSimple Snapshot Manager para gerenciar o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Passos seguintes
[Baixar snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

