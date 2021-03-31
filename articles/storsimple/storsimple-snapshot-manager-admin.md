---
title: StorSimple Snapshot Manager administração | Microsoft Docs
description: Fornece uma visão geral e links para mais informações sobre tarefas de administração de soluções solução StorSimple Snapshot Manager e fluxos de trabalho.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: alkohli
ms.openlocfilehash: 16d782c6a25d8d2d8e1323b7cb683f41d8c99649
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054981"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Utilize o StorSimple Snapshot Manager para administrar a sua solução StorSimple

## <a name="overview"></a>Descrição Geral
StorSimple Snapshot Manager é um snap-in da Microsoft Management Console (MMC) que simplifica a proteção de dados e a gestão de backup num ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir os dados do Microsoft Azure StorSimple no data center e na nuvem como uma única solução de armazenamento integrada, simplificando assim os processos de backup e reduzindo custos.

A consola central de gestão StorSimple Snapshot Manager permite-lhe criar cópias de backup consistentes e pontuais de dados locais e em nuvem. Por exemplo, pode utilizar a consola para:

* Configure, recue e elimine volumes.
* Configure grupos de volume para garantir que os dados com armazenamento são consistentes com a aplicação.
* Gerencie as políticas de backup para que os dados seja apoiado num horário pré-determinado.
* Crie cópias independentes de dados, que podem ser armazenados na nuvem e utilizados para a recuperação de desastres.

Este artigo fornece links para tutoriais que descrevem o StorSimple Snapshot Manager e como usá-lo para completar tarefas de administração do sistema e fluxos de trabalho.

* Para obter mais informações sobre os componentes e arquitetura do StorSimple Snapshot Manager, consulte [o que é storSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Para baixar o StorSimple Snapshot Manager, aceda [à página de descarregamento do StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Para os procedimentos de implementação do Gestor snapshot StorSimple, aceda ao [Gestor de Instantâneos Implementar StorSimple](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Não é possível utilizar o StorSimple Snapshot Manager para gerir as Matrizes Virtuais Microsoft Azure StorSimple (também conhecidas como dispositivos virtuais StorSimple no local).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Tarefas e fluxos de trabalho do StorSimple Snapshot Manager
Pode utilizar o StorSimple Snapshot Manager para monitorizar e gerir trabalhos de backup atuais, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 cópias de segurança completas. Pode utilizar o catálogo para encontrar e restaurar volumes ou ficheiros individuais. 

| SE QUISER FAZER ISTO... | USE ESTE TUTORIAL... |
|:--- |:--- |
| Saiba mais sobre o StorSimple Snapshot Manager |[O que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) |
| Instalar StorSimple Snapshot Manager<br>Reinstalar StorSimple Snapshot Manager<br>Remover StorSimple Snapshot Manager |[Implementar StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Use menus e funcionalidades do StorSimple Snapshot Manager:<ul><li>Barra de Menus</li><li>Barra de ferramentas</li><li>Painel de âmbito</li><li>Painel de resultados</li><li>Painel de ações</li><li>Navegação de teclado e atalhos</li></ul> |[Interface de utilizador StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Utilize as funcionalidades comuns do MMC incluídas no StorSimple Snapshot Manager:<ul><li>Vista</li><li>Nova Janela a Partir Daqui</li><li>Atualizar</li><li>Lista de Exportação</li><li>Help</li></ul> |[Utilize as ações do menu MMC no StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Adicionar ou substituir um dispositivo<br>Ligar um dispositivo<br>Verificar grupos de volume importados<br>Atualizar dispositivos conectados<br>Autenticar um dispositivo<br>Ver detalhes do dispositivo<br>Excluir uma configuração do dispositivo<br>Alterar uma palavra-passe do dispositivo<br>Substitua um dispositivo falhado<br> |[Utilize o StorSimple Snapshot Manager para ligar e gerir dispositivos StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Volumes de montagem<br>Ver informações sobre volumes<br>Apagar um volume<br>Volumes de rescan<br>Configurar e apoiar um volume básico<br>Configurar e apoiar um volume dinâmico espelhado |[Use StorSimple Snapshot Manager para visualizar e gerir volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Ver grupos de volume<br>Criar um grupo de volume<br>Apoiar um grupo de volume<br>Editar um grupo de volume<br>Excluir um grupo de volume |[Use storSimple Snapshot Manager para criar e gerir grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md) |
| Criar uma política de cópias de segurança <br>Editar uma política de backup<br>Excluir uma política de backup |[Use storSimple Snapshot Manager para criar e gerir políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ver e gerir trabalhos de backup programados<br>Ver e gerir trabalhos recentes de backup<br>Ver e gerir atualmente os trabalhos de backup |[Use storSimple Snapshot Manager para visualizar e gerir trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurar um volume<br>Clone um grupo de volume ou volume<br>Eliminar uma cópia de segurança<br>Recuperar um ficheiro<br>Restaurar a base de dados StorSimple Snapshot Manager |[Use storSimple Snapshot Manager para gerir o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Passos seguintes
[Baixar StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

