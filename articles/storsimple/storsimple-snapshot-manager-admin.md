---
title: Administração do Snapshot Manager do StorSimple | Documentos da Microsoft
description: Fornece uma descrição geral e ligações para obter mais informações sobre tarefas de administração de solução do Snapshot Manager do StorSimple e fluxos de trabalho.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872188"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Utilize o StorSimple Snapshot Manager para administrar a sua solução StorSimple

## <a name="overview"></a>Descrição geral
Snapshot Manager do StorSimple é um snap-in da consola de gestão da Microsoft (MMC), que simplifica a proteção de dados e gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir dados do Microsoft Azure StorSimple no Datacenter e na cloud como uma solução de armazenamento integrado único, assim, simplificando os processos de cópia de segurança e reduz os custos.

O console de gerenciamento central do Snapshot Manager do StorSimple permite-lhe criar cópias de segurança consistentes e de ponto no tempo de local e os dados da cloud. Por exemplo, pode utilizar a consola para:

* Configurar, criar cópias de segurança e elimine volumes.
* Configurar grupos de volumes para se certificar de que a segurança dos dados é consistente com a aplicação.
* Gerir políticas de cópia de segurança para que os dados de cópia de segurança com base numa agenda predeterminada.
* Crie cópias independentes de dados, que podem ser armazenados na cloud e utilizados para recuperação após desastre.

Este artigo fornece ligações para tutoriais que descrevem o Snapshot Manager do StorSimple e como usá-lo a concluir tarefas de administração do sistema e fluxos de trabalho.

* Para obter mais informações sobre os componentes do Snapshot Manager do StorSimple e da arquitetura, consulte [o que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) 
* Para transferir o Snapshot Manager do StorSimple, aceda a [a página de download do Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).
* Para obter os procedimentos de implementação do StorSimple Snapshot Manager, aceda a [Snapshot Manager do StorSimple implementar](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Não é possível utilizar o Snapshot Manager do StorSimple para gerir matrizes virtuais do Microsoft Azure StorSimple (também conhecido como StorSimple no local dispositivos virtuais).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Fluxos de trabalho e tarefas do Snapshot Manager do StorSimple
Pode utilizar o Snapshot Manager do StorSimple para monitorizar e gerir tarefas de cópia de segurança atuais, agendadas e concluídas. Além disso, o Snapshot Manager do StorSimple fornece um catálogo de até 64 cópias de segurança concluídas. Pode utilizar o catálogo para localizar e restaurar volumes ou ficheiros individuais. 

| SE QUISER FAZER ISSO... | UTILIZE ESTE TUTORIAL... |
|:--- |:--- |
| Saiba mais sobre o StorSimple Snapshot Manager |[O que é o Snapshot Manager do StorSimple?](storsimple-what-is-snapshot-manager.md) |
| Instalar o Snapshot Manager do StorSimple<br>Reinstalar o Snapshot Manager do StorSimple<br>Remover o Snapshot Manager do StorSimple |[Implementar o Snapshot Manager do StorSimple](storsimple-snapshot-manager-deployment.md) |
| Snapshot Manager do StorSimple utilização menus e funcionalidades:<ul><li>Barra de menus</li><li>Barra de ferramentas</li><li>Painel de âmbito</li><li>Painel de resultados</li><li>Painel de ações</li><li>Navegação do teclado e atalhos</li></ul> |[Interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md) |
| Utilize os recursos comuns do MMC, incluídos no Snapshot Manager do StorSimple:<ul><li>Vista</li><li>Janela novo a partir daqui</li><li>Atualizar</li><li>Exportar lista</li><li>Ajuda</li></ul> |[Utilizar as ações do menu MMC no Snapshot Manager do StorSimple](storsimple-snapshot-manager-mmc-menu.md) |
| Adicionar ou substituir um dispositivo<br>Ligar um dispositivo<br>Certifique-se de grupos de volumes importados<br>Atualizar dispositivos ligados<br>Autenticar um dispositivo<br>Ver detalhes do dispositivo<br>Eliminar uma configuração de dispositivo<br>Alterar uma senha de dispositivo<br>Substituir um dispositivo com falhas<br> |[Utilizar o Snapshot Manager do StorSimple para ligar e gerir os dispositivos do StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Montar volumes<br>Ver informações sobre volumes<br>Eliminar um volume<br>Reanalisar volumes<br>Configurar e efetuar cópias de segurança de um volume básico<br>Configurar e um volume espelhado dinâmico de cópia de segurança |[Utilizar o Snapshot Manager do StorSimple para ver e gerir volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Ver grupos de volume<br>Criar um grupo de volume<br>Fazer cópias de segurança de um grupo de volume<br>Editar um grupo de volume<br>Eliminar um grupo de volume |[Utilizar o Snapshot Manager do StorSimple para criar e gerir grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md) |
| Criar uma política de cópia de segurança <br>Editar uma política de cópia de segurança<br>Eliminar uma política de cópia de segurança |[Utilizar o Snapshot Manager do StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md) |
| Ver e gerir tarefas de cópia de segurança agendadas<br>Ver e gerir tarefas de cópia de segurança recentes<br>Ver e gerir tarefas de cópia de segurança atualmente em execução |[Utilizar o Snapshot Manager do StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurar um volume<br>Clonar um volume ou o grupo de volumes<br>Eliminar uma cópia de segurança<br>Recuperar um ficheiro<br>Restaurar a base de dados do Snapshot Manager do StorSimple |[Utilize o StorSimple Snapshot Manager para gerir o catálogo de cópia de segurança](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Passos Seguintes
[Baixe o Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

