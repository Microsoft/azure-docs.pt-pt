---
title: O que é o Snapshot Manager do StorSimple? | Microsoft Docs
description: Descreve o Snapshot Manager do StorSimple, sua arquitetura e as respetivas funcionalidades.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789619"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Uma introdução ao StorSimple Snapshot Manager

## <a name="overview"></a>Descrição geral
Snapshot Manager do StorSimple é um snap-in da consola de gestão da Microsoft (MMC), que simplifica a proteção de dados e gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir dados do Microsoft Azure StorSimple no Datacenter e na cloud como uma solução de armazenamento integrado único, assim, simplificando os processos de cópia de segurança e reduz os custos.

Esta visão geral introduz o Snapshot Manager do StorSimple, descreve as funcionalidades do mesmo e explica a sua função no Microsoft Azure StorSimple. 

Para uma visão geral do sistema do Microsoft Azure StorSimple inteiro, incluindo o dispositivo StorSimple, o serviço StorSimple Manager, o Snapshot Manager do StorSimple e o adaptador do StorSimple para SharePoint, consulte [série StorSimple 8000: uma cloud híbrida solução de armazenamento](storsimple-overview.md). 

> [!NOTE]
> * Não é possível utilizar o Snapshot Manager do StorSimple para gerir matrizes virtuais do Microsoft Azure StorSimple (também conhecido como StorSimple no local dispositivos virtuais).
> * Se planear instalar atualização 2 do StorSimple no dispositivo StorSimple, certifique-se de que transferir a versão mais recente do Snapshot Manager do StorSimple e instalá-lo **antes de instalar a atualização 2 do StorSimple**. A versão mais recente do Snapshot Manager do StorSimple é compatível com versões anteriores e funciona com todas as versões do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do Snapshot Manager do StorSimple, terá de atualizá-lo (não é necessário desinstalar a versão anterior antes de instalar a nova versão).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Objetivo do Snapshot Manager do StorSimple e arquitetura
Snapshot Manager do StorSimple fornece um console de gerenciamento central, que pode utilizar para criar consistente, cópias de segurança de ponto no tempo de local e dados na cloud. Por exemplo, pode utilizar a consola para:

* Configurar, criar cópias de segurança e elimine volumes.
* Configurar grupos de volumes para se certificar de que a segurança dos dados é consistente com a aplicação.
* Gerir políticas de cópia de segurança para que os dados de cópia de segurança com base numa agenda predeterminada.
* Criar local e na cloud instantâneos, que podem ser armazenados na cloud e utilizados para recuperação após desastre.

O StorSimple Snapshot Manager obtém a lista de aplicações registadas com o fornecedor VSS no host. Em seguida, para criar cópias de segurança consistentes com aplicações, verifica os volumes utilizados por uma aplicação e sugere grupos de volumes para configurar. Snapshot Manager do StorSimple utiliza estes grupos de volumes para gerar as cópias de segurança que são consistentes com aplicações. (Consistência de aplicação existe quando todos os arquivos relacionados e bases de dados são sincronizados e representam o verdadeiro estado do aplicativo num ponto específico no tempo.) 

Cópias de segurança do Snapshot Manager do StorSimple assumir a forma de instantâneos incrementais, o que capturar apenas as alterações desde a última cópia de segurança. Como resultado, as cópias de segurança necessitam de menos armazenamento e podem ser criadas e restauradas rapidamente. Snapshot Manager do StorSimple utiliza o serviço de cópia do Windows Volume sombra (VSS) para se certificar de que os instantâneos capturam dados consistentes com aplicações. (Para obter mais informações, vá para a integração com a seção de serviço de cópia de sombra de volumes do Windows.) Com o StorSimple Snapshot Manager, pode criar agendas de cópia de segurança ou efetuar cópias de segurança de imediato, conforme necessário. Se tiver de restaurar dados a partir de uma cópia de segurança, permite do Snapshot Manager do StorSimple selecione um catálogo de local ou instantâneos de cloud. O Azure StorSimple restaura apenas os dados que é necessária, conforme necessário, que impede que os atrasos na disponibilidade dos dados durante as operações de restauro.)

![Arquitetura do Snapshot Manager do StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura do Snapshot Manager do StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume
Pode utilizar o Snapshot Manager do StorSimple para configurar e efetuar cópias de segurança de volumes, os seguintes tipos: 

* **Volumes básicas** – um volume básico é uma única partição num disco básico. 
* **Volumes simples** – um volume simple é um volume dinâmico que contém o espaço em disco de um único disco dinâmico. Um volume simple consiste numa única região num disco ou de várias regiões que estão ligados em conjunto no mesmo disco. (Pode criar volumes simples apenas em discos dinâmicos.) Volumes simples não são tolerante a falhas.
* **Volumes dinâmicos** – um volume dinâmico é um volume criado num disco dinâmico. Discos dinâmicos utilizam uma base de dados para rastrear informações sobre volumes que estão contidas em discos dinâmicos num computador. 
* **Volumes dinâmicos com o espelhamento** – volumes dinâmicos com o espelhamento baseiam-se na arquitetura do RAID 1. Com o RAID 1, os dados idênticos são escritos no disco de duas ou mais, produzir um conjunto espelhado. Uma solicitação de leitura, em seguida, pode ser manipulada por qualquer disco que contém os dados solicitados.
* **Volumes partilhados de cluster** – com volumes partilhados de cluster (CSVs), vários nós numa lata de cluster de ativação pós-falha em simultâneo de leitura ou escrita para o mesmo disco. Ativação pós-falha de um nó para outro nó pode ocorrer rapidamente, sem exigir uma alteração na propriedade de unidade ou montar, desmontar e remoção de um volume. 

> [!IMPORTANT]
> Não misture CSVs e não CSVs no mesmo instantâneo. Misturar CSVs e não CSVs num instantâneo não é suportada. 
> 
> 

Pode utilizar o Snapshot Manager do StorSimple para restaurar os grupos de todo o volume ou clonar volumes individuais e recuperar ficheiros individuais.

* [Volumes e grupos de volumes](#volumes-and-volume-groups) 
* [Tipos de cópia de segurança e políticas de cópia de segurança](#backup-types-and-backup-policies) 

Para obter mais informações sobre as funcionalidades do Snapshot Manager do StorSimple e como usá-las, consulte [interface de utilizador do Snapshot Manager do StorSimple](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volumes
Com o StorSimple Snapshot Manager, pode criar volumes e, em seguida, configurá-los em grupos de volume. 

Snapshot Manager do StorSimple utiliza grupos de volumes para criar cópias de segurança que são consistentes com aplicações. Consistência de aplicação existe quando todos os arquivos relacionados e bases de dados são sincronizados e representam o verdadeiro estado de um aplicativo num ponto específico no tempo. Grupos de volumes (que também são conhecidos como *grupos de consistência*) formam a base de uma cópia de segurança ou restaurar a tarefa.

Grupos de volumes não são os mesmos que os contentores de volume. Um contentor de volume contém um ou mais volumes que partilham uma conta de armazenamento na cloud e outros atributos, como o consumo de largura de banda e de encriptação. Um contentor de volume único pode conter até 256 volumes do StorSimple com aprovisionamento dinâmico. Para obter mais informações sobre contentores de volumes, aceda a [gerir os seus contentores de volume](storsimple-manage-volume-containers.md). Grupos de volumes são coleções de volumes que pode configurar para facilitar as operações de cópia de segurança. Se selecionar dois volumes que pertencem a contentores de volumes diferentes, colocá-los num grupo de único volume e, em seguida, criar uma política de cópia de segurança para esse grupo de volume, cada volume será efetuada no contentor de volume adequado, com a conta de armazenamento adequado.

> [!NOTE]
> Todos os volumes num grupo de volume devem vir de um fornecedor de serviços de nuvem única.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o serviço de cópia de sombra de volumes do Windows
Snapshot Manager do StorSimple utiliza o serviço de cópia do Windows Volume sombra (VSS) para capturar dados consistentes com aplicações. VSS facilita a consistência de aplicação através da comunicação com aplicativos que reconhecem VSS para coordenar a criação de instantâneos incrementais. VSS garante que as aplicações estão temporariamente inativa ou inativo, quando são tirados instantâneos. 

A implementação do StorSimple Snapshot Manager do VSS funciona com o SQL Server e volumes NTFS genéricos. O processo é o seguinte: 

1. Um requerente, o que é normalmente um gerenciamento de dados e solução de proteção (por exemplo, o StorSimple Snapshot Manager) ou um aplicativo de backup, invoca o VSS e pergunta-lo para coletar informações de software de gravação no aplicativo de destino.
2. VSS entra em contacto com o componente escritor para obter uma descrição dos dados. O escritor devolve a descrição dos dados para a cópia de segurança. 
3. VSS sinaliza o escritor de preparar o aplicativo para cópia de segurança. O escritor prepara os dados para cópia de segurança ao concluir as transações abertas, atualizar os registos de transações e assim por diante e, em seguida, notifica o VSS.
4. VSS instrui o escritor temporariamente parar arquivos de dados da aplicação e certifique-se de que nenhum dado é escrito para o volume enquanto a cópia de sombra é criada. Este passo garante a consistência de dados e demora mais do que 60 segundos.
5. VSS instrui o fornecedor para criar a cópia de sombra. Provedores, que podem ser software ou hardware baseados em-, faça a gestão de volumes que estão atualmente em execução e criar cópias de sombra de-los a pedido. O fornecedor cria a cópia de sombra e notifica o VSS quando for concluído.
6. VSS entra em contacto com o escritor para notificar a aplicação que pode retomar a e/s e também para confirmar que e/s foi colocada em pausa com êxito durante a criação de cópias sombra. 
7. Se a cópia foi concluída com êxito, o VSS devolve a localização da cópia para o requerente. 
8. Se a dados tenha sido escritos enquanto a cópia de sombra foi criada, a cópia de segurança será inconsistente. VSS elimina a cópia de sombra e notifica o requerente. O solicitante pode repetir o processo de cópia de segurança automaticamente ou notificar o administrador para repeti-lo mais tarde.

Ver a ilustração seguinte.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo de serviço de cópia de sombra de volumes do Windows** 

## <a name="backup-types-and-backup-policies"></a>Tipos de cópia de segurança e políticas de cópia de segurança
Com o StorSimple Snapshot Manager, pode criar cópias de segurança e armazená-lo localmente e na cloud. Pode utilizar o Snapshot Manager do StorSimple para criar cópias de segurança imediatamente, ou pode utilizar uma política de cópia de segurança para criar um agendamento para fazer cópias de segurança automaticamente. Políticas de cópia de segurança também permitem-lhe especificar o número de instantâneos irão ser mantidos. 

### <a name="backup-types"></a>Tipos de cópia de segurança
Pode utilizar o Snapshot Manager do StorSimple para criar os seguintes tipos de cópias de segurança:

* **Os instantâneos locais** – os instantâneos locais são cópias de ponto no tempo dos dados do volume que estão armazenados no dispositivo StorSimple. Normalmente, este tipo de cópia de segurança pode ser criado e restaurado rapidamente. Pode usar um instantâneo local, tal como faria com uma cópia de segurança local.
* **Instantâneos de cloud** – os instantâneos de Cloud são cópias de ponto no tempo dos dados de volume que estão armazenados na cloud. Um instantâneo de cloud é equivalente a um instantâneo replicado num sistema de armazenamento diferentes, fora das instalações. Instantâneos de cloud são particularmente úteis em cenários de recuperação após desastre.

### <a name="on-demand-and-scheduled-backups"></a>Cópias de segurança a pedido e agendadas
Com o StorSimple Snapshot Manager, pode iniciar uma cópia de segurança de uso individual a ser criado imediatamente, ou pode utilizar uma política de cópia de segurança para agendar a operações de cópia de segurança periódicas.

Uma política de cópia de segurança é um conjunto de regras automatizados que pode utilizar para agendar cópias de segurança regulares. Uma política de cópia de segurança permite-lhe definir a frequência e parâmetros para obtenção de instantâneos de um grupo de volume específico. Pode utilizar políticas para especificar as datas de início e de expiração, horas, frequências e requisitos de retenção, para ambos os locais e instantâneos da cloud. Uma política é aplicada imediatamente depois de defini-lo. 

Pode utilizar o Snapshot Manager do StorSimple para configurar ou reconfigurar as políticas de cópia de segurança sempre que necessário. 

Configure as seguintes informações para cada política de cópia de segurança que criou:

* **Nome** – o nome exclusivo da política de cópia de segurança selecionada.
* **Tipo de** – o tipo de política de cópia de segurança; instantâneo local ou instantâneo de cloud.
* **Grupo de volumes** – o grupo de volume para o qual é atribuída a política de cópia de segurança selecionada.
* **Retenção** – o número de cópias de segurança para manter. Se verificar o **todos os** caixa, todas as cópias de segurança são mantidas até que seja atingido o número máximo de cópias de segurança por volume, altura em que a política irá falhar e gerar uma mensagem de erro. Em alternativa, pode especificar um número de cópias de segurança para reter (entre 1 e 64).
* **Data** – a data quando a política de cópia de segurança foi criada.

Para informações sobre como configurar políticas de cópia de segurança, aceda a [utilização StorSimple Snapshot Manager para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitorização de tarefa de cópia de segurança e gestão
Pode utilizar o Snapshot Manager do StorSimple para monitorizar e gerir tarefas de cópia de segurança futuras, agendadas e concluídas. Além disso, o Snapshot Manager do StorSimple fornece um catálogo de até 64 cópias de segurança concluídas. Pode utilizar o catálogo para localizar e restaurar volumes ou ficheiros individuais. 

Para informações sobre a monitorização de tarefas de cópia de segurança, aceda a [utilização StorSimple Snapshot Manager para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [através do Snapshot Manager do StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Baixe [Snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

