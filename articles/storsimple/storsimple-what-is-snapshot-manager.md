---
title: O que é o Snapshot Manager do StorSimple? | Microsoft Docs
description: Descreve o Snapshot Manager do StorSimple, sua arquitetura e seus recursos.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: twooley
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e58e2d929dd1e4db16ce495ad54045e9dc3a6fb1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933640"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Uma introdução ao StorSimple Snapshot Manager

## <a name="overview"></a>Visão geral
O StorSimple Snapshot Manager é um snap-in do MMC (console de gerenciamento Microsoft) que simplifica a proteção de dados e o gerenciamento de backup em um ambiente de Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, você pode gerenciar Microsoft Azure StorSimple dados no data center e na nuvem como uma solução única de armazenamento integrado, simplificando assim os processos de backup e reduzindo os custos.

Esta visão geral apresenta o Snapshot Manager do StorSimple, descreve seus recursos e explica sua função no Microsoft Azure StorSimple. 

Para obter uma visão geral de todo o sistema de Microsoft Azure StorSimple, incluindo o dispositivo StorSimple, o serviço StorSimple Manager, o StorSimple Snapshot Manager e o adaptador StorSimple para SharePoint, consulte [storsimple 8000 Series: uma solução de armazenamento de nuvem híbrida](storsimple-overview.md). 

> [!NOTE]
> * Você não pode usar o StorSimple Snapshot Manager para gerenciar Microsoft Azure StorSimple matrizes virtuais (também conhecidas como dispositivos virtuais locais do StorSimple).
> * Se você planeja instalar o StorSimple atualização 2 em seu dispositivo StorSimple, certifique-se de baixar a versão mais recente do StorSimple Snapshot Manager e instalá-lo **antes de instalar a atualização 2 do storsimple**. A versão mais recente do StorSimple Snapshot Manager é compatível com versões anteriores e funciona com todas as versões lançadas do Microsoft Azure StorSimple. Se você estiver usando a versão anterior do StorSimple Snapshot Manager, será necessário atualizá-la (não é necessário desinstalar a versão anterior antes de instalar a nova versão).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Finalidade e arquitetura do StorSimple Snapshot Manager
O StorSimple Snapshot Manager fornece um console de gerenciamento central que você pode usar para criar cópias de backup consistentes e point-in-time de dados locais e na nuvem. Por exemplo, você pode usar o console do para:

* Configurar, fazer backup e excluir volumes.
* Configure grupos de volumes para garantir que os dados de backup sejam consistentes com o aplicativo.
* Gerencie políticas de backup para que os dados sejam copiados em um agendamento predeterminado.
* Crie instantâneos locais e na nuvem, que podem ser armazenados na nuvem e usados para recuperação de desastres.

O Snapshot Manager StorSimple busca a lista de aplicativos registrados com o provedor VSS no host. Em seguida, para criar backups consistentes com o aplicativo, ele verifica os volumes usados por um aplicativo e sugere que os grupos de volumes sejam configurados. O StorSimple Snapshot Manager usa esses grupos de volumes para gerar cópias de backup consistentes com o aplicativo. (A consistência do aplicativo existe quando todos os arquivos e bancos de dados relacionados são sincronizados e representam o estado verdadeiro do aplicativo em um ponto específico no tempo.) 

Os backups do StorSimple Snapshot Manager assumem a forma de instantâneos incrementais, que capturam apenas as alterações desde o último backup. Como resultado, os backups exigem menos armazenamento e podem ser criados e restaurados rapidamente. O StorSimple Snapshot Manager usa o Windows Serviço de Cópias de Sombra de Volume (VSS) para garantir que os instantâneos capturam dados consistentes com o aplicativo. (Para obter mais informações, acesse a seção integração com o Windows Serviço de Cópias de Sombra de Volume.) Com o StorSimple Snapshot Manager, você pode criar agendas de backup ou fazer backups imediatos, conforme necessário. Se você precisar restaurar dados de um backup, o StorSimple Snapshot Manager permite que você selecione em um catálogo de instantâneos locais ou na nuvem. O Azure StorSimple restaura somente os dados necessários conforme necessário, o que evita atrasos na disponibilidade de dados durante operações de restauração.)

![Arquitetura de Snapshot Manager do StorSimple](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura de Snapshot Manager do StorSimple** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume
Você pode usar o Snapshot Manager do StorSimple para configurar e fazer backup dos seguintes tipos de volumes: 

* **Volumes básicos** – um volume básico é uma partição única em um disco básico. 
* **Volumes simples** – um volume simples é um volume dinâmico que contém o espaço em disco de um único disco dinâmico. Um volume simples consiste em uma única região em um disco ou em várias regiões que estão vinculadas juntas no mesmo disco. (Você pode criar volumes simples somente em discos dinâmicos.) Os volumes simples não são tolerantes a falhas.
* **Volumes dinâmicos** – um volume dinâmico é um volume criado em um disco dinâmico. Discos dinâmicos usam um banco de dados para rastrear informações sobre volumes contidos em discos dinâmicos em um computador. 
* **Volumes dinâmicos com espelhamento** – volumes dinâmicos com espelhamento são criados na arquitetura RAID 1. Com o RAID 1, dados idênticos são gravados em dois ou mais discos, produzindo um conjunto espelhado. Uma solicitação de leitura pode ser tratada por qualquer disco que contenha os dados solicitados.
* **Volumes compartilhados de cluster** – com CSVs (volumes compartilhados de cluster), vários nós em um cluster de failover podem ler ou gravar simultaneamente no mesmo disco. O failover de um nó para outro nó pode ocorrer rapidamente, sem a necessidade de uma alteração na propriedade da unidade ou montagem, desmontagem e remoção de um volume. 

> [!IMPORTANT]
> Não misture CSVs e não CSVs no mesmo instantâneo. Não há suporte para a combinação de CSVs e não CSVs em um instantâneo. 
> 
> 

Você pode usar o StorSimple Snapshot Manager para restaurar grupos de volume inteiros ou clonar volumes individuais e recuperar arquivos individuais.

* [Volumes e grupos de volumes](#volumes-and-volume-groups) 
* [Tipos de backup e políticas de backup](#backup-types-and-backup-policies) 

Para obter mais informações sobre os recursos de Snapshot Manager do StorSimple e como usá-los, consulte a [interface do usuário do storsimple snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volumes
Com o StorSimple Snapshot Manager, você cria volumes e os configura em grupos de volumes. 

O StorSimple Snapshot Manager usa grupos de volumes para criar cópias de backup consistentes com o aplicativo. A consistência do aplicativo existe quando todos os arquivos e bancos de dados relacionados são sincronizados e representam o estado verdadeiro de um aplicativo em um ponto específico no tempo. Os grupos de volumes (que também são conhecidos como *grupos de consistência*) formam a base de um trabalho de backup ou restauração.

Os grupos de volumes não são iguais aos contêineres de volume. Um contêiner de volume contém um ou mais volumes que compartilham uma conta de armazenamento em nuvem e outros atributos, como criptografia e consumo de largura de banda. Um único contêiner de volume pode conter até 256 volumes StorSimple com provisionamento dinâmico. Para obter mais informações sobre contêineres de volume, vá para [gerenciar seus contêineres de volume](storsimple-manage-volume-containers.md). Grupos de volumes são coleções de volumes que você configura para facilitar as operações de backup. Se você selecionar dois volumes que pertencem a contêineres de volume diferentes, coloque-os em um único grupo de volumes e, em seguida, crie uma política de backup para esse grupo de volumes, será feito backup de cada volume no contêiner de volume apropriado, usando a conta de armazenamento apropriada.

> [!NOTE]
> Todos os volumes em um grupo de volumes devem vir de um único provedor de serviços de nuvem.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o Windows Serviço de Cópias de Sombra de Volume
O StorSimple Snapshot Manager usa o Windows Serviço de Cópias de Sombra de Volume (VSS) para capturar dados consistentes com o aplicativo. O VSS facilita a consistência do aplicativo comunicando-se com aplicativos com reconhecimento de VSS para coordenar a criação de instantâneos incrementais. O VSS garante que os aplicativos estejam temporariamente inativos ou inativados quando os instantâneos são feitos. 

O StorSimple Snapshot Manager implementação do VSS funciona com SQL Server e volumes NTFS genéricos. O processo é o seguinte: 

1. Um solicitante, que normalmente é uma solução de gerenciamento e proteção de dados (como o StorSimple Snapshot Manager) ou um aplicativo de backup, invoca o VSS e solicita que ele colete informações do software do gravador no aplicativo de destino.
2. O VSS contata o componente do gravador para recuperar uma descrição dos dados. O gravador retorna a descrição dos dados a serem copiados em backup. 
3. O VSS sinaliza o gravador para preparar o aplicativo para backup. O gravador prepara os dados para backup, concluindo transações abertas, atualizando logs de transações e assim por diante e notifica o VSS.
4. O VSS instrui o gravador a parar temporariamente os armazenamentos de dados do aplicativo e garantir que nenhum dado seja gravado no volume enquanto a cópia de sombra é criada. Esta etapa garante a consistência dos dados e não leva mais do que 60 segundos.
5. O VSS instrui o provedor a criar a cópia de sombra. Provedores, que podem ser baseados em software ou hardware, gerenciam os volumes que estão em execução no momento e criam cópias de sombra deles sob demanda. O provedor cria a cópia de sombra e notifica o VSS quando ele é concluído.
6. O VSS contata o gravador para notificar o aplicativo que a e/s pode retomar e também confirmar que a e/s foi pausada com êxito durante a criação da cópia de sombra. 
7. Se a cópia foi bem-sucedida, o VSS retorna o local da cópia para o solicitante. 
8. Se os dados foram gravados enquanto a cópia de sombra foi criada, o backup será inconsistente. O VSS exclui a cópia de sombra e notifica o solicitante. O solicitante pode repetir o processo de backup automaticamente ou notificar o administrador para tentar novamente mais tarde.

Consulte a ilustração a seguir.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo do Windows Serviço de Cópias de Sombra de Volume** 

## <a name="backup-types-and-backup-policies"></a>Tipos de backup e políticas de backup
Com o StorSimple Snapshot Manager, você pode fazer backup de dados e armazená-los localmente e na nuvem. Você pode usar o StorSimple Snapshot Manager para fazer backup de dados imediatamente ou pode usar uma política de backup para criar um agendamento para fazer backups automaticamente. As políticas de backup também permitem que você especifique quantos instantâneos serão retidos. 

### <a name="backup-types"></a>Tipos de cópia de segurança
Você pode usar o StorSimple Snapshot Manager para criar os seguintes tipos de backups:

* **Instantâneos locais** – instantâneos locais são cópias point-in-time de dados de volume armazenados no dispositivo StorSimple. Normalmente, esse tipo de backup pode ser criado e restaurado rapidamente. Você pode usar um instantâneo local como faria com uma cópia de backup local.
* **Instantâneos de nuvem** – instantâneos de nuvem são cópias point-in-time de dados de volume que são armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo replicado em um sistema de armazenamento diferente, fora do local. Os instantâneos de nuvem são particularmente úteis em cenários de recuperação de desastres.

### <a name="on-demand-and-scheduled-backups"></a>Backups agendados e sob demanda
Com o StorSimple Snapshot Manager, você pode iniciar um backup único para ser criado imediatamente ou pode usar uma política de backup para agendar operações de backup recorrentes.

Uma política de backup é um conjunto de regras automatizadas que você pode usar para agendar backups regulares. Uma política de backup permite que você defina a frequência e os parâmetros para tirar instantâneos de um grupo de volumes específico. Você pode usar políticas para especificar datas de início e expiração, horários, frequências e requisitos de retenção, para instantâneos locais e na nuvem. Uma política é aplicada imediatamente depois de você defini-la. 

Você pode usar o StorSimple Snapshot Manager para configurar ou reconfigurar políticas de backup sempre que necessário. 

Você configura as seguintes informações para cada política de backup criada:

* **Nome** – o nome exclusivo da política de backup selecionada.
* **Tipo** – o tipo de política de backup; instantâneo local ou instantâneo de nuvem.
* **Grupo de volumes** – o grupo de volumes ao qual a política de backup selecionada é atribuída.
* **Retenção** – o número de cópias de backup a serem retidas. Se você marcar a caixa **tudo** , todas as cópias de backup serão retidas até que o número máximo de cópias de backup por volume seja atingido; nesse ponto, a política falhará e gerará uma mensagem de erro. Como alternativa, você pode especificar um número de backups a serem retidos (entre 1 e 64).
* **Data** – a data em que a política de backup foi criada.

Para obter informações sobre como configurar políticas de backup, acesse [usar o StorSimple snapshot Manager para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Monitoramento e gerenciamento de trabalhos de backup
Você pode usar o Snapshot Manager do StorSimple para monitorar e gerenciar trabalhos de backup futuros, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 backups concluídos. Você pode usar o catálogo para localizar e restaurar volumes ou arquivos individuais. 

Para obter informações sobre como monitorar trabalhos de backup, vá para [usar o StorSimple snapshot Manager para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre como [usar o StorSimple snapshot Manager para administrar sua solução storsimple](storsimple-snapshot-manager-admin.md).
* Baixar [snapshot Manager do StorSimple](https://www.microsoft.com/download/details.aspx?id=44220).

