---
title: O que é o Snapshot Manager do StorSimple? | Microsoft Docs
description: Descreve o StorSimple Snapshot Manager, a sua arquitetura e as suas características.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267420"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Uma introdução ao StorSimple Snapshot Manager

## <a name="overview"></a>Descrição geral
StorSimple Snapshot Manager é um snap-in da Consola de Gestão da Microsoft (MMC) que simplifica a proteção de dados e a gestão de backup num ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, pode gerir os dados Do Microsoft Azure StorSimple no centro de dados e na nuvem como uma única solução de armazenamento integrada, simplificando assim os processos de backup e reduzindo custos.

Esta visão geral introduz o StorSimple Snapshot Manager, descreve as suas funcionalidades e explica o seu papel no Microsoft Azure StorSimple. 

Para uma visão geral de todo o sistema Microsoft Azure StorSimple, incluindo o dispositivo StorSimple, o serviço StorSimple Manager, o StorSimple Snapshot Manager e o StorSimple Adapter para O SharePoint, consulte a [série StorSimple 8000: uma solução híbrida](storsimple-overview.md)de armazenamento em nuvem. 

> [!NOTE]
> * Não é possível utilizar o StorSimple Snapshot Manager para gerir as Matrizes Virtuais Microsoft Azure StorSimple (também conhecidas como dispositivos virtuais StorSimple on-premises).
> * Se planeia instalar o StorSimple Update 2 no seu dispositivo StorSimple, certifique-se de que descarrega a versão mais recente do StorSimple Snapshot Manager e instale-o antes de instalar o **StorSimple Update 2**. A versão mais recente do StorSimple Snapshot Manager é compatível com o retrocesso e funciona com todas as versões lançadas do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do StorSimple Snapshot Manager, terá de atualizá-lo (não precisa de desinstalar a versão anterior antes de instalar a nova versão).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager propósito e arquitetura
O StorSimple Snapshot Manager fornece uma consola central de gestão que pode supor para criar cópias de backup consistentes e ponto-a-tempo de dados locais e cloud. Por exemplo, pode utilizar a consola para:

* Configure, recue e elimine volumes.
* Configure grupos de volume para garantir que os dados apoiados são consistentes com aplicações.
* Gerir as políticas de backup para que os dados sejam apoiados num horário pré-determinado.
* Crie instantâneos locais e em nuvem, que podem ser armazenados na nuvem e usados para recuperação de desastres.

O StorSimple Snapshot Manager recebe a lista de aplicações registadas com o fornecedor VSS no anfitrião. Em seguida, para criar cópias de segurança consistentes com aplicações, verifica os volumes utilizados por uma aplicação e sugere grupos de volume para configurar. O StorSimple Snapshot Manager utiliza estes grupos de volume para gerar cópias de backup consistentes com aplicações. (A consistência da aplicação existe quando todos os ficheiros e bases de dados relacionados são sincronizados e representam o verdadeiro estado da aplicação num determinado momento.) 

As cópias de segurança do StorSimple Snapshot Manager assumem a forma de instantâneos incrementais, que captam apenas as alterações desde a última cópia de segurança. Como resultado, as cópias de segurança requerem menos armazenamento e podem ser criadas e restauradas rapidamente. O StorSimple Snapshot Manager utiliza o Windows Volume Shadow Copy Service (VSS) para garantir que os instantâneos captam dados consistentes com aplicações. (Para mais informações, vá à Secção de Integração com o Serviço de Cópia de Sombra do Volume do Windows.) Com o StorSimple Snapshot Manager, pode criar horários de backup ou receber cópias de segurança imediatas conforme necessário. Se necessitar de restaurar os dados a partir de uma cópia de segurança, o StorSimple Snapshot Manager permite-lhe selecionar a partir de um catálogo de instantâneos locais ou em nuvem. O Azure StorSimple restaura apenas os dados necessários como é necessário, o que evita atrasos na disponibilidade de dados durante as operações de restauro.)

![Arquitetura StorSimple Snapshot Manager](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura StorSimple Snapshot Manager** 

## <a name="support-for-multiple-volume-types"></a>Suporte para vários tipos de volume
Pode utilizar o StorSimple Snapshot Manager para configurar e fazer o apoio aos seguintes tipos de volumes: 

* **Volumes básicos** – Um volume básico é uma única divisória num disco básico. 
* **Volumes simples** – Um volume simples é um volume dinâmico que contém espaço de disco a partir de um único disco dinâmico. Um volume simples consiste numa única região num disco ou em várias regiões que estão ligadas no mesmo disco. (Pode criar volumes simples apenas em discos dinâmicos.) Volumes simples não são tolerantes a falhas.
* **Volumes dinâmicos** – Um volume dinâmico é um volume criado num disco dinâmico. Os discos dinâmicos utilizam uma base de dados para rastrear informações sobre volumes que estão contidos em discos dinâmicos num computador. 
* **Volumes dinâmicos com espelhamento** – Volumes dinâmicos com espelhamento são construídos na arquitetura RAID 1. Com raid 1, dados idênticos são escritos em dois ou mais discos, produzindo um conjunto espelhado. Um pedido de leitura pode então ser tratado por qualquer disco que contenha os dados solicitados.
* **Volumes partilhados por cluster** – Com volumes partilhados por cluster (CSVs), vários nós num cluster failover podem simultaneamente ler ou escrever para o mesmo disco. A falha de um nó para outro nó pode ocorrer rapidamente, sem exigir uma mudança na propriedade de unidade ou montagem, desmontagem e remoção de um volume. 

> [!IMPORTANT]
> Não misture CSVs e não-CSVs no mesmo instantâneo. Não é suportado misturar CSVs e não-CSVs num instantâneo. 
> 
> 

Pode utilizar o StorSimple Snapshot Manager para restaurar grupos de volume inteiros ou clonar volumes individuais e recuperar ficheiros individuais.

* [Volumes e grupos de volume](#volumes-and-volume-groups) 
* [Tipos de backup e políticas de backup](#backup-types-and-backup-policies) 

Para obter mais informações sobre as funcionalidades do StorSimple Snapshot Manager e como utilizá-las, consulte a interface de [utilizador do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volume
Com o StorSimple Snapshot Manager, cria volumes e, em seguida, configurá-los em grupos de volume. 

O StorSimple Snapshot Manager utiliza grupos de volume para criar cópias de backup consistentes com aplicações. A consistência da aplicação existe quando todos os ficheiros e bases de dados relacionados são sincronizados e representam o verdadeiro estado de uma aplicação num determinado momento. Os grupos de volume (que também são conhecidos como grupos de *consistência)* formam a base de um trabalho de backup ou restauro.

Os grupos de volume não são os mesmos que os contentores de volume. Um recipiente de volume contém um ou mais volumes que partilham uma conta de armazenamento em nuvem e outros atributos, tais como encriptação e consumo de largura de banda. Um único recipiente de volume pode conter até 256 volumes StorSimple finamente provisionados. Para mais informações sobre contentores de volume, vá gerir [os seus recipientes](storsimple-manage-volume-containers.md)de volume . Grupos de volume são coleções de volumes que configura para facilitar operações de backup. Se selecionar dois volumes que pertencem a diferentes contentores de volume, colocá-los num único grupo de volume e, em seguida, criar uma política de backup para esse grupo de volume, cada volume será apoiado no recipiente de volume apropriado, utilizando a conta de armazenamento apropriada.

> [!NOTE]
> Todos os volumes de um grupo de volume devem provir de um único fornecedor de serviços na nuvem.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com serviço de cópia de sombra de volume do Windows
O StorSimple Snapshot Manager utiliza o Windows Volume Shadow Copy Service (VSS) para capturar dados consistentes com aplicações. O VSS facilita a consistência da aplicação comunicando com aplicações conscientes do VSS para coordenar a criação de instantâneos incrementais. O VSS garante que as aplicações são temporariamente inativas, ou quiescentes, quando são tiradas fotografias. 

A implementação do StorSimple Snapshot Manager do VSS funciona com o SQL Server e os volumes genéricos ntfS. O processo é o seguinte: 

1. Um requestor, que é tipicamente uma solução de gestão e proteção de dados (como o StorSimple Snapshot Manager) ou uma aplicação de backup, invoca o VSS e pede-lhe que recolha informações do software do escritor na aplicação-alvo.
2. A VSS contacta o componente do escritor para obter uma descrição dos dados. O escritor devolve a descrição dos dados a serem apoiados. 
3. O VSS sinaliza o escritor para preparar a aplicação para backup. O escritor prepara os dados para backup, completando transações abertas, atualizando registos de transações, e assim por diante, e depois notifica vsS.
4. O VSS instrui o escritor a parar temporariamente as lojas de dados da aplicação e a certificar-se de que não são escritos dados ao volume enquanto a cópia-sombra é criada. Este passo garante a consistência dos dados e não demora mais de 60 segundos.
5. O VSS instrui o fornecedor a criar a cópia da sombra. Os fornecedores, que podem ser baseados em software ou hardware, gerem os volumes que estão atualmente a funcionar e criam cópias-sombra dos mesmos a pedido. O fornecedor cria a cópia-sombra e notifica o VSS quando está concluído.
6. A VSS contacta o escritor para notificar a aplicação que o I/O pode retomar e também para confirmar que o I/O foi interrompido com sucesso durante a criação de cópias de sombra. 
7. Se a cópia tiver sido bem sucedida, a VSS devolve a localização da cópia ao solicitador. 
8. Se os dados foram escritos enquanto a cópia da sombra foi criada, então a cópia de segurança será inconsistente. VsS elimina a cópia da sombra e notifica o solicitador. O solicitador pode repetir automaticamente o processo de cópia de segurança ou notificar o administrador para o voltar a tentar mais tarde.

Veja a seguinte ilustração.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo de serviço de cópia de sombra de volume do windows** 

## <a name="backup-types-and-backup-policies"></a>Tipos de backup e políticas de backup
Com o StorSimple Snapshot Manager, pode fazer o back-up de dados e armazená-lo localmente e na nuvem. Pode utilizar o StorSimple Snapshot Manager para fazer cópias de segurança imediatamente, ou pode utilizar uma política de backup para criar um calendário para a recolha automática de backups. As políticas de backup também lhe permitem especificar quantas imagens serão mantidas. 

### <a name="backup-types"></a>Tipos de cópia de segurança
Pode utilizar o StorSimple Snapshot Manager para criar os seguintes tipos de backups:

* **Instantâneos locais** – Os instantâneos locais são cópias ponto-a-tempo de dados de volume que são armazenados no dispositivo StorSimple. Tipicamente, este tipo de backup pode ser criado e restaurado rapidamente. Pode usar uma foto local como faria com uma cópia de reserva local.
* **Imagens em nuvem** – Os instantâneos em nuvem são cópias ponto-a-tempo dos dados de volume que são armazenados na nuvem. Um instantâneo em nuvem é equivalente a um instantâneo replicado num sistema de armazenamento diferente, fora do local. Os instantâneos em nuvem são particularmente úteis em cenários de recuperação de desastres.

### <a name="on-demand-and-scheduled-backups"></a>Reforços a pedido e programados
Com o StorSimple Snapshot Manager, pode iniciar uma cópia de segurança única para ser criada imediatamente, ou pode utilizar uma política de backup para agendar operações de backup recorrentes.

Uma política de backup é um conjunto de regras automatizadas que pode usar para agendar backups regulares. Uma política de backup permite definir a frequência e os parâmetros para tirar fotos de um grupo de volume específico. Pode utilizar políticas para especificar datas de início e expiração, horas, frequências e requisitos de retenção, tanto para instantâneos locais como em nuvem. Uma política é aplicada imediatamente após a definição. 

Pode utilizar o StorSimple Snapshot Manager para configurar ou reconfigurar as políticas de backup sempre que necessário. 

Configura as seguintes informações para cada política de backup que cria:

* **Nome** – O nome único da política de backup selecionada.
* **Tipo** – O tipo de política de backup; ou instantâneo local ou instantâneo de nuvem.
* **Grupo de volume** – O grupo de volume ao qual é atribuída a política de backup selecionada.
* **Retenção** – O número de cópias de reserva a reter. Se verificar a caixa **All,** todas as cópias de backup são retidas até que o número máximo de cópias de backup por volume seja atingido, altura em que a apólice falhará e gerará uma mensagem de erro. Em alternativa, pode especificar uma série de cópias de segurança para reter (entre 1 e 64).
* **Data** – A data em que a política de backup foi criada.

Para obter informações sobre a configuração de políticas de backup, vá ao [StorSimple Snapshot Manager para criar e gerir políticas](storsimple-snapshot-manager-manage-backup-policies.md)de backup .

### <a name="backup-job-monitoring-and-management"></a>Monitorização e gestão de trabalho de backup
Você pode usar o StorSimple Snapshot Manager para monitorizar e gerir os próximos trabalhos de backup, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 cópias de segurança concluídas. Pode utilizar o catálogo para encontrar e restaurar volumes ou ficheiros individuais. 

Para obter informações sobre a monitorização de trabalhos de backup, vá ao [StorSimple Snapshot Manager para visualizar e gerir trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [a utilização do StorSimple Snapshot Manager para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).
* Baixar [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

