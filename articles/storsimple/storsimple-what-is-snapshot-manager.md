---
title: O que é o Snapshot Manager do StorSimple? | Microsoft Docs
description: Descreve o StorSimple Snapshot Manager, a sua arquitetura e as suas características.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017989"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Uma introdução ao StorSimple Snapshot Manager

## <a name="overview"></a>Descrição geral
StorSimple Snapshot Manager é um snap-in da Microsoft Management Console (MMC) que simplifica a proteção de dados e a gestão de backup num ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir os dados do Microsoft Azure StorSimple no data center e na nuvem como uma única solução de armazenamento integrada, simplificando assim os processos de backup e reduzindo custos.

Esta visão geral introduz o StorSimple Snapshot Manager, descreve as suas funcionalidades e explica o seu papel no Microsoft Azure StorSimple. 

Para uma visão geral de todo o sistema Microsoft Azure StorSimple, incluindo o dispositivo StorSimple, o serviço StorSimple Manager, o StorSimple Snapshot Manager e o Adaptador StorSimple para o SharePoint, consulte [a série StorSimple 8000: uma solução de armazenamento em nuvem híbrida](storsimple-overview.md). 

> [!NOTE]
> * Não é possível utilizar o StorSimple Snapshot Manager para gerir as Matrizes Virtuais Microsoft Azure StorSimple (também conhecidas como dispositivos virtuais StorSimple no local).
> * Se planeia instalar o StorSimple Update 2 no seu dispositivo StorSimple, certifique-se de que descarrega a versão mais recente do StorSimple Snapshot Manager e **instale-o antes de instalar o StorSimple Update 2**. A versão mais recente do StorSimple Snapshot Manager é compatível com retro e funciona com todas as versões lançadas do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do StorSimple Snapshot Manager, terá de a atualizar (não precisa de desinstalar a versão anterior antes de instalar a nova versão).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager e arquitetura
O StorSimple Snapshot Manager fornece uma consola de gestão central que pode usar para criar cópias de backup consistentes e pontuais de dados locais e em nuvem. Por exemplo, pode utilizar a consola para:

* Configure, recue e elimine volumes.
* Configure grupos de volume para garantir que os dados com armazenamento são consistentes com a aplicação.
* Gerencie as políticas de backup para que os dados seja apoiado num horário pré-determinado.
* Crie instantâneos locais e em nuvem, que podem ser armazenados na nuvem e usados para a recuperação de desastres.

O StorSimple Snapshot Manager recolhe a lista de candidaturas registadas no fornecedor VSS no anfitrião. Em seguida, para criar cópias de segurança consistentes com aplicações, verifica os volumes utilizados por uma aplicação e sugere que os grupos de volume se confirem. O StorSimple Snapshot Manager utiliza estes grupos de volume para gerar cópias de backup que são consistentes com aplicações. (A coerência da aplicação existe quando todos os ficheiros e bases de dados relacionados são sincronizados e representam o verdadeiro estado da aplicação num determinado momento.) 

As cópias de segurança StorSimple Snapshot Manager assumem a forma de instantâneos incrementais, que capturam apenas as alterações desde a última cópia de segurança. Como resultado, as cópias de segurança requerem menos armazenamento e podem ser criadas e restauradas rapidamente. O StorSimple Snapshot Manager utiliza o Serviço de Cópia Sombra do Volume do Windows (VSS) para garantir que os instantâneos capturam dados consistentes com a aplicação. (Para mais informações, aceda à secção de Integração com o Windows Volume Shadow Copy Service.) Com o StorSimple Snapshot Manager, pode criar horários de backup ou fazer cópias de segurança imediatas conforme necessário. Se precisar de restaurar os dados a partir de uma cópia de segurança, o StorSimple Snapshot Manager permite-lhe selecionar a partir de um catálogo de instantâneos locais ou em nuvem. A Azure StorSimple restaura apenas os dados necessários conforme necessário, o que evita atrasos na disponibilidade de dados durante as operações de restauro.)

![Arquitetura StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume
Pode utilizar o StorSimple Snapshot Manager para configurar e fazer o back up dos seguintes tipos de volumes: 

* **Volumes básicos** – Um volume básico é uma única partição num disco básico. 
* **Volumes simples** – Um volume simples é um volume dinâmico que contém espaço em disco a partir de um único disco dinâmico. Um volume simples consiste numa única região num disco ou em várias regiões que estão ligadas no mesmo disco. (Só pode criar volumes simples em discos dinâmicos.) Volumes simples não são tolerantes a falhas.
* **Volumes dinâmicos** – Um volume dinâmico é um volume criado num disco dinâmico. Os discos dinâmicos utilizam uma base de dados para rastrear informações sobre volumes que estão contidos em discos dinâmicos num computador. 
* **Volumes dinâmicos com espelhamento** – Volumes dinâmicos com espelhamento são construídos sobre a arquitetura RAID 1. Com o RAID 1, os dados idênticos são escritos em dois ou mais discos, produzindo um conjunto espelhado. Um pedido de leitura pode então ser tratado por qualquer disco que contenha os dados solicitados.
* **Volumes partilhados pelo cluster** – Com volumes partilhados por cluster (CSVs), vários nós num cluster de failover podem simultaneamente ler ou escrever para o mesmo disco. A falha de um nó para outro nó pode ocorrer rapidamente, sem exigir uma alteração na propriedade da unidade ou na montagem, desmontagem e remoção de um volume. 

> [!IMPORTANT]
> Não misture CSVs e não CSVs na mesma imagem. A mistura de CSVs e não-CSVs num instantâneo não é suportada. 
> 
> 

Pode utilizar o StorSimple Snapshot Manager para restaurar grupos de volume inteiros ou clonar volumes individuais e recuperar ficheiros individuais.

* [Volumes e grupos de volume](#volumes-and-volume-groups) 
* [Tipos de backup e políticas de backup](#backup-types-and-backup-policies) 

Para obter mais informações sobre as funcionalidades do StorSimple Snapshot Manager e como usá-las, consulte a [interface de utilizador do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volume
Com o StorSimple Snapshot Manager, cria-se volumes e, em seguida, configura-os em grupos de volume. 

O StorSimple Snapshot Manager utiliza grupos de volume para criar cópias de backup que são consistentes com aplicações. A consistência da aplicação existe quando todos os ficheiros e bases de dados relacionados são sincronizados e representam o verdadeiro estado de uma aplicação num determinado momento. Os grupos de volume (que também são conhecidos como *grupos de consistência*) formam a base de um trabalho de backup ou restauro.

Os grupos de volume não são os mesmos que os recipientes de volume. Um recipiente de volume contém um ou mais volumes que partilham uma conta de armazenamento em nuvem e outros atributos, tais como encriptação e consumo de largura de banda. Um único recipiente de volume pode conter até 256 volumes StorSimple pouco abastetados. Para obter mais informações sobre recipientes de volume, vá ao [Gerir os seus recipientes de volume](./storsimple-8000-manage-volume-containers.md). Os grupos de volume são coleções de volumes que configura para facilitar as operações de backup. Se selecionar dois volumes pertencentes a diferentes recipientes de volume, colocá-los num único grupo de volume e, em seguida, criar uma política de backup para esse grupo de volume, cada volume será apoiado no recipiente de volume apropriado, utilizando a conta de armazenamento apropriada.

> [!NOTE]
> Todos os volumes de um grupo de volume devem ser provenientes de um único fornecedor de serviços em nuvem.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o Serviço de Cópia Sombra de Volume do Windows
O StorSimple Snapshot Manager utiliza o Serviço de Cópia Sombra do Volume do Windows (VSS) para capturar dados consistentes com aplicações. A VSS facilita a consistência da aplicação comunicando com aplicações conscientes do VSS para coordenar a criação de instantâneos incrementais. A VSS garante que as aplicações são temporariamente inativas, ou quiescentes, quando as imagens são tiradas. 

A implementação do StorSimple Snapshot Manager funciona com o SQL Server e volumes genéricos de NTFS. O processo é o seguinte: 

1. Um solicitador, que é tipicamente uma solução de gestão e proteção de dados (como o StorSimple Snapshot Manager) ou uma aplicação de backup, invoca a VSS e pede-lhe que recolha informações do software do escritor na aplicação-alvo.
2. A VSS contacta o componente do escritor para recuperar uma descrição dos dados. O escritor devolve a descrição dos dados a serem apoiados. 
3. VSS sinaliza o escritor para preparar o pedido de cópia de segurança. O escritor prepara os dados para cópia de segurança, completando transações abertas, atualizando registos de transações, e assim por diante, e depois notifica o VSS.
4. A VSS instrui o escritor a parar temporariamente as lojas de dados da aplicação e a certificar-se de que nenhum dado é escrito para o volume enquanto a cópia-sombra é criada. Este passo garante a consistência dos dados e não demora mais de 60 segundos.
5. A VSS instrui o fornecedor a criar a cópia-sombra. Os fornecedores, que podem ser baseados em software ou hardware, gerem os volumes que estão atualmente em execução e criam cópias-sombra dos mesmos a pedido. O fornecedor cria a cópia-sombra e notifica o VSS quando este estiver concluído.
6. A VSS contacta o escritor para notificar a aplicação que a I/O pode retomar e também para confirmar que a I/O foi interrompida com sucesso durante a criação de cópias de sombra. 
7. Se a cópia tiver sido bem sucedida, a VSS devolve a localização da cópia ao solicitador. 
8. Se os dados foram escritos enquanto a cópia-sombra foi criada, então a cópia de segurança será inconsistente. O VSS elimina a cópia de sombra e notifica o solicitador. O solicitador pode repetir automaticamente o processo de backup ou notificar o administrador para o voltar a tentar posteriormente.

Veja a seguinte ilustração.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo de serviço de cópia sombra do volume do Windows Volume** 

## <a name="backup-types-and-backup-policies"></a>Tipos de backup e políticas de backup
Com o StorSimple Snapshot Manager, pode fazer o back up de dados e armazená-lo localmente e na nuvem. Pode utilizar o StorSimple Snapshot Manager para fazer o backup de dados imediatamente, ou pode utilizar uma política de backup para criar um calendário para a recolha automática de backups. As políticas de backup também permitem especificar quantas imagens serão mantidas. 

### <a name="backup-types"></a>Tipos de cópia de segurança
Pode utilizar o StorSimple Snapshot Manager para criar os seguintes tipos de backups:

* **Snapshots locais** – As imagens locais são cópias pontuais dos dados de volume que são armazenados no dispositivo StorSimple. Normalmente, este tipo de backup pode ser criado e restaurado rapidamente. Pode usar uma foto local como uma cópia de reserva local.
* **Snapshots em nuvem** – As imagens em nuvem são cópias pontuais dos dados de volume que são armazenados na nuvem. Um instantâneo em nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferente e fora do local. As imagens em nuvem são particularmente úteis em cenários de recuperação de desastres.

### <a name="on-demand-and-scheduled-backups"></a>Backups a pedido e programados
Com o StorSimple Snapshot Manager, pode iniciar uma cópia de segurança única a ser criada imediatamente, ou pode utilizar uma política de backup para agendar operações de backup recorrentes.

Uma política de backup é um conjunto de regras automatizadas que pode usar para agendar backups regulares. Uma política de backup permite-lhe definir a frequência e os parâmetros para tirar fotos de um grupo de volume específico. Pode utilizar políticas para especificar datas de início e validade, horários, frequências e requisitos de retenção, tanto para instantâneos locais como para imagens de nuvem. Uma política é aplicada imediatamente após defini-la. 

Pode utilizar o StorSimple Snapshot Manager para configurar ou reconfigurar as políticas de backup sempre que necessário. 

Configurar as seguintes informações para cada política de backup que cria:

* **Nome** – O nome único da política de backup selecionada.
* **Tipo** – O tipo de política de backup; ou instantâneo local ou instantâneo em nuvem.
* **Grupo de** volume – O grupo de volume ao qual é atribuída a política de backup selecionada.
* **Retenção** – O número de cópias de reserva a reter. Se verificar a caixa **All,** todas as cópias de backup são mantidas até que o número máximo de cópias de cópias de cópias de segurança por volume seja atingido, altura em que a política falhará e gerará uma mensagem de erro. Em alternativa, pode especificar uma série de backups para reter (entre 1 e 64).
* **Data** – A data em que a política de backup foi criada.

Para obter informações sobre a configuração das políticas de backup, vá ao [Use StorSimple Snapshot Manager para criar e gerir políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitorização e gestão de trabalho de reserva
Pode utilizar o StorSimple Snapshot Manager para monitorizar e gerir os próximos trabalhos de backup, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 cópias de segurança completas. Pode utilizar o catálogo para encontrar e restaurar volumes ou ficheiros individuais. 

Para obter informações sobre a monitorização de trabalhos de backup, vá ao [Use StorSimple Snapshot Manager para visualizar e gerir trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a utilização do StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Baixar [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).