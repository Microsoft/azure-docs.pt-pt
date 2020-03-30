---
title: Administração storSimple Snapshot Manager [ StorSimple Snapshot Manager] Microsoft Docs
description: Fornece uma visão geral e links para mais informações sobre as tarefas e fluxos de trabalho da storSimple Snapshot Manager.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933284"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Use o StorSimple Snapshot Manager para administrar a sua solução StorSimple

## <a name="overview"></a>Descrição geral
StorSimple Snapshot Manager é um snap-in da Consola de Gestão da Microsoft (MMC) que simplifica a proteção de dados e a gestão de backup num ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir os dados Do Microsoft Azure StorSimple no centro de dados e na nuvem como uma única solução de armazenamento integrada, simplificando assim os processos de backup e reduzindo custos.

A consola central de gestão StorSimple Snapshot Manager permite-lhe criar cópias de backup consistentes e ponto-a-tempo de dados locais e cloud. Por exemplo, pode utilizar a consola para:

* Configure, recue e elimine volumes.
* Configure grupos de volume para garantir que os dados apoiados são consistentes com aplicações.
* Gerir as políticas de backup para que os dados sejam apoiados num horário pré-determinado.
* Crie cópias independentes de dados, que podem ser armazenados na nuvem e usados para a recuperação de desastres.

Este artigo fornece links para tutoriais que descrevem o StorSimple Snapshot Manager e como usá-lo para completar tarefas de administração do sistema e fluxos de trabalho.

* Para obter mais informações sobre componentes e arquitetura StorSimple Snapshot Manager, consulte [o Que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Para baixar o StorSimple Snapshot Manager, vá à página de [descarregamento do StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Para procedimentos de implementação do StorSimple Snapshot Manager, vá ao [StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Não é possível utilizar o StorSimple Snapshot Manager para gerir as Matrizes Virtuais Microsoft Azure StorSimple (também conhecidas como dispositivos virtuais StorSimple on-premises).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Tarefas e fluxos de trabalho do StorSimple Snapshot Manager
Pode utilizar o StorSimple Snapshot Manager para monitorizar e gerir trabalhos de backup atuais, programados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 cópias de segurança concluídas. Pode utilizar o catálogo para encontrar e restaurar volumes ou ficheiros individuais. 

| SE QUISER FAZER ISTO... | USE ESTE TUTORIAL... |
|:--- |:--- |
| Saiba mais sobre o StorSimple Snapshot Manager |[O que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) |
| Instale o Gestor de Snapshot StorSimple<br>Reinstalar O Gestor de Snapshot StorSimple<br>Remover StorSimple Snapshot Manager |[Implementar O Gestor de Snapshot StorSimple](storsimple-snapshot-manager-deployment.md) |
| Utilize menus e funcionalidades do StorSimple Snapshot Manager:<ul><li>Barra de menu</li><li>Barra de ferramentas</li><li>Painel de mira</li><li>Painel de resultados</li><li>Painel de ações</li><li>Navegação de teclado e atalhos</li></ul> |[Interface de utilizador do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Utilize as funcionalidades comuns do MMC incluídas no StorSimple Snapshot Manager:<ul><li>Vista</li><li>Nova Janela a Partir Daqui</li><li>Atualizar</li><li>Lista de Exportações</li><li>Ajuda</li></ul> |[Utilize as ações do menu MMC no StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Adicione ou substitua um dispositivo<br>Ligar um dispositivo<br>Verificar grupos de volume importados<br>Atualizar dispositivos conectados<br>Autenticar um dispositivo<br>Ver detalhes do dispositivo<br>Eliminar uma configuração do dispositivo<br>Alterar uma palavra-passe do dispositivo<br>Substitua um dispositivo falhado<br> |[Utilize o StorSimple Snapshot Manager para ligar e gerir dispositivos StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Volumes de montagem<br>Ver informações sobre volumes<br>Eliminar um volume<br>Volumes rescanos<br>Configure e volte a fazer um volume básico<br>Configure e recompor um volume espelhado dinâmico |[Use o StorSimple Snapshot Manager para visualizar e gerir volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Ver grupos de volume<br>Criar um grupo de volume<br>Apoiar um grupo de volume<br>Editar um grupo de volume<br>Eliminar um grupo de volume |[Use o StorSimple Snapshot Manager para criar e gerir grupos de volume](storsimple-snapshot-manager-manage-volume-groups.md) |
| Criar uma política de backup <br>Editar uma política de backup<br>Eliminar uma política de backup |[Use o StorSimple Snapshot Manager para criar e gerir políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ver e gerir trabalhos de backup programados<br>Ver e gerir trabalhos de backup recentes<br>Ver e gerir atualmente a executar trabalhos de backup |[Use o StorSimple Snapshot Manager para visualizar e gerir trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurar um volume<br>Clonar um volume ou grupo de volume<br>Eliminar uma cópia de segurança<br>Recuperar um ficheiro<br>Restaurar a base de dados do StorSimple Snapshot Manager |[Use o StorSimple Snapshot Manager para gerir o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Passos seguintes
[Baixar StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

