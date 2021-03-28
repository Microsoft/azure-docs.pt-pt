---
title: Opções de migração de dados de dispositivos da série StorSimple 5000-7000
description: Fornece uma visão geral das opções para migrar dados da série StorSimple 5000-7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: cc67b678fabeb2a7878490a4ea713acb5709cdbc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643793"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Options to migrate data from StorSimple 5000-7000 series (Opções para migrar dados das séries 5000-7000 do StorSimple) 

> [!IMPORTANT]
> No dia 9 de julho de 2019, a série StorSimple 5000/7000 chegará ao fim do estado de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migram para uma das alternativas descritas no documento.

A série StorSimple 5000-7000 está a chegar [ao fim do Support](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. Os clientes que estão a executar a série StorSimple 5000-7000 têm a opção de fazer upgrade para outros serviços híbridos da primeira parte da Azure. Este artigo descreve as opções híbridas Azure disponíveis para migrar dados. 

## <a name="migration-options"></a>Opções de migração

Os clientes que usam a série StorSimple 5000-7000 têm opções Azure ou de terceiros.

### <a name="azure-options"></a>Opções do Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade para StorSimple Série 8000

Upgrade para a série StorSimple 8000 e, assim, continue na plataforma StorSimple.  Esta trajetória de upgrade requer que os clientes substituam os seus dispositivos da série 5000-7000 por uma série de 8000. Os dados são migrados do dispositivo da série 5000-7000 utilizando a ferramenta de migração. Uma vez concluída a migração com sucesso, os dispositivos da série StorSimple 8000 continuarão a nivelar os dados para o Azure Blob Storage. 

Para obter mais informações sobre como migrar dados utilizando uma série StorSimple 8000, vá para [os dados migratórios da série StorSimple 5000-7000 para o dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrar para o Azure File Sync

Esta nova opção de migração permite que os clientes armazenem as ações de ficheiros da sua organização nos Ficheiros Azure. Estas partilhas de ficheiros são então centralizadas para acesso no local utilizando o Azure File Sync (AFS). O AFS pode ser implantado num anfitrião do Windows Server. A migração de dados real é então realizada como uma cópia hospedeira ou utilizando a ferramenta de migração.

Para obter mais informações sobre como migrar dados para Azure File Sync, vá a [migrar dados da série StorSimple 5000-7000 para Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Opções de terceiros

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrar para Panzura Freedom NAS

Os clientes StorSimple 5000-7000 podem optar por migrar para a Panzura Freedom NAS para manter os seus dados em Azure. A solução Panzura Freedom fornece uma solução NAS que abrange datacenters, escritórios, nuvens públicas e privadas. A solução permite fluxos de trabalho de dados locais, híbridos e em nuvem para clientes NFS, SMB e mobile. 

Esta migração é apoiada pela Panzura e os clientes podem começar solicitando apoio à migração a partir do [site panzura.](https://panzura.com/storsimple-migration/)

#### <a name="migrate-to-cohesity"></a>Migrar para a Coesidade

A coesidade permite-lhe migrar dados do seu StorSimple 5000-7000 para a Plataforma de Dados de Coesidade em Azure. A Plataforma de Dados de Coesidade é uma solução à escala web definida por software que consolida ficheiros, backups, objetos e VMs numa única solução nativa em nuvem. Após a migração para a Plataforma de Dados, você pode gerir, proteger e providenciar dados e aplicações da nuvem para o núcleo através de um único painel de vidro. Com a Coesidade, comece com apenas três nós. 

Saiba mais sobre [a migração para a Plataforma de Dados de Coesidade.](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)

#### <a name="migrate-to-nasuni"></a>Migrar para Nasuni

A Nasuni facilita a migração dos seus dados em Azure para os clientes StorSimple 5000-7000.  A Nasuni é uma das principais soluções de armazenamento nas baseadas em Azure, dando aos clientes o desempenho e segurança que esperam de soluções on-prem, com economia e escala em nuvem.  Além do armazenamento de ficheiros de alto desempenho, a Nasuni e a Azure manuseiam backup e DR, permitindo-lhe partilhar e colaborar nos seus dados em todo o mundo com a gestão centralizada de armazenamento de ficheiros. 

Nasuni tem a experiência de facilitar a sua migração – começa hoje: https://www.nasuni.com/blog-migrating-off-storsimple/

#### <a name="migrate-to-talon-fast"></a>Migrar para Talon FAST

A Talon facilita aos clientes StorSimple 5000-7000 que continuem a aproveitar os benefícios que tanto valorizavam na plataforma StorSimple (pequena pegada no local apoiada por recursos de nuvem ilimitados) com uma função ainda maior.  Com a solução Talon FAST, os clientes podem migrar e manter os seus dados em Azure, ao mesmo tempo que têm uma pegada de software ainda menor no local e adicionam benefícios como o bloqueio global de ficheiros, o espaço de nome global e a colaboração em vários locais.  A Talon é uma solução de ecossistema Azure líder, trabalhando com clientes globais para migrar as suas cargas de trabalho de servidor de ficheiros no local para uma pegada consolidada, baseada em Azure, sem comprometer o fluxo de trabalho ou experiência dos utilizadores.  

Saiba mais sobre como evoluir para uma empresa consolidada em nuvem.

## <a name="migration---frequently-asked-questions"></a>Migração - Perguntas frequentes

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>P. Quando é que os dispositivos da série StorSimple 5000 e 7000 chegam ao fim do serviço? 

A. A série StorSimple 5000-7000 chega [ao fim do serviço](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. O fim do serviço implica que a Microsoft deixará de ser capaz de fornecer suporte tanto para hardware como para software destes dispositivos após julho de 2019. Recomendamos vivamente que comece a formular um plano para migrar os dados dos seus dispositivos agora.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>P. O que acontece com os dados que guardei em Azure?  

A. Pode continuar a utilizar os dados em Azure assim que os migrar para um serviço mais recente. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>P. O que acontece com os dados que armazenei localmente no meu dispositivo StorSimple? 

A. Os dados que se encontrem no dispositivo local podem ser copiados para o serviço mais recente, conforme descrito nos documentos de migração.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>P. O que acontece se eu quiser manter o meu aparelho da série StorSimple 5000/7000? 

A. Embora os serviços possam continuar a funcionar, a Microsoft deixará de ser capaz de fornecer suporte a hardware e software. A migração é fortemente recomendada para a continuidade do negócio.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>P. Que opções estão disponíveis para migrar dados de dispositivos da série StorSimple 5000-7000? 

A. Dependendo do seu cenário, os utilizadores da série StorSimple 5000-7000 têm as seguintes opções de migração. 

 - **Upgrade para série 8000**: Utilize esta opção quando quiser continuar na plataforma StorSimple. 
 - **Migrar para Azure File Sync**: Utilize esta opção quando pretender mudar para o formato nativo do Azure. Pode utilizar o Azure File Sync para uma gestão centralizada de ações de ficheiros. 

Pode contactar o Microsoft Support para discutir as opções de migração não listadas aqui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>P. A migração para outras soluções de armazenamento é suportada?

A. Sim. A migração para outras soluções de armazenamento utilizando a cópia hospedeira dos dados é suportada.

### <a name="q-is-migration-supported-by-microsoft"></a>P. A migração é suportada pela Microsoft? 

A. Migrar de séries 5000 ou 7000 é uma operação totalmente suportada. De facto, a Microsoft recomenda que se chegue ao Suporte antes de iniciar a migração. A migração é atualmente uma operação assistida. Se pretender migrar dados do seu dispositivo da série StorSimple 5000-7000, [Abra um bilhete de suporte](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>P. Qual é o modelo de preços para ambas as opções de migração?

A. O custo da migração varia consoante a opção que escolher. Embora a migração em si seja gratuita, se decidir fazer upgrade para uma série StorSimple 8000, haverá o custo do dispositivo de hardware. 

Da mesma forma, ao utilizar o Azure File Sync, podem aplicar-se as taxas de subscrição do serviço. Em cada caso, os clientes também terão de pagar os custos de armazenamento em curso. Consulte o seguinte para uma estimativa: 
- [Preços StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Preços da AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>P.  Quanto tempo demora a concluir uma migração?

A. O tempo para migrar dados depende da quantidade dos dados e da opção de upgrade selecionada. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>P. Qual é a data de fim de suporte para a série StorSimple 8000?

A. A data de fim de suporte para a série StorSimple 8000 é publicada [aqui.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)


## <a name="next-steps"></a>Passos seguintes
 - [Migrar dados de uma série StorSimple 5000-7000 para um dispositivo de série 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrar dados de uma série StorSimple 5000-7000 para Azure File Sync](../storage/files/storage-files-migration-storsimple-8000.md)
