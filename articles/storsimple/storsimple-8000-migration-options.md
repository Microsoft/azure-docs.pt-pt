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
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471827"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Options to migrate data from StorSimple 5000-7000 series (Opções para migrar dados das séries 5000-7000 do StorSimple) 

> [!IMPORTANT]
> Em 9 de julho de 2019, a série StorSimple 5000/7000 chegará ao fim do estatuto de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migram para uma das alternativas descritas no documento.

A série StorSimple 5000-7000 está a chegar ao [fim do Suporte](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. Os clientes que estão a executar a série StorSimple 5000-7000 têm a opção de fazer upgrade para outros serviços híbridos da primeira parte do Azure. Este artigo descreve as opções híbridas Azure disponíveis para migrar dados. 

## <a name="migration-options"></a>Opções de migração

Os clientes que utilizam a série StorSimple 5000-7000 têm opções Azure ou de terceiros.

### <a name="azure-options"></a>Opções do Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade para StorSimple 8000 Series

Atualize para a série StorSimple 8000 e assim continue na plataforma StorSimple.  Este percurso de upgrade requer que os clientes substituam os seus dispositivos da série 5000-7000 por uma série de 8000. Os dados são migrados do dispositivo da série 5000-7000 utilizando a ferramenta de migração. Uma vez concluída a migração com sucesso, os dispositivos da série StorSimple 8000 continuarão a nidificar dados para o Armazenamento De Blob Azure. 

Para obter mais informações sobre como migrar dados usando uma série StorSimple 8000, vá para [migrar dados da série StorSimple 5000-7000 para o dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrar para o Azure File Sync

Esta nova opção de migração permite que os clientes armazenem as ações de ficheiros da sua organização nos Ficheiros Azure. Estas ações de ficheiros são então centralizadas para acesso no local utilizando o Azure File Sync (AFS). O AFS pode ser implantado num anfitrião do Windows Server. A migração real de dados é então realizada como uma cópia hospedeira ou usando a ferramenta de migração.

Para obter mais informações sobre como migrar dados para o Azure File Sync, vá a [migrar dados da série StorSimple 5000-7000 para o Azure File Sync](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Opções de terceiros

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrar para Panzura Freedom NAS

Os clientes StorSimple 5000-7000 podem optar por migrar para Panzura Freedom NAS para manter os seus dados em Azure. A solução Panzura Freedom fornece uma solução NAS que abrange datacenters, escritórios, nuvens públicas e privadas. A solução permite fluxos de trabalho de dados locais, híbridos e em nuvem para clientes NFS, SMB e mobile. 

Esta migração é apoiada por Panzura e os clientes podem começar solicitando apoio migratório do [site de Panzura.](https://panzura.com/storsimple-migration/)

#### <a name="migrate-to-cohesity"></a>Migrar para a Coesão

A Coesão permite-lhe migrar dados do seu Atual StorSimple 5000-7000 para a Plataforma de Dados coesos em Azure. A Plataforma de Dados coesos é uma solução de web definida por software que consolida ficheiros, backups, objetos e VMs numa única solução nativa da nuvem. Após a migração para a Plataforma de Dados, você pode gerir, proteger e fornecer dados e aplicações de nuvem para núcleo através de um único painel de vidro. Com coesão, comece com apenas três nós. 

Saiba mais sobre migração para a Plataforma de [Dados coesos.](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html)

#### <a name="migrate-to-nasuni"></a>Migrar para Nasuni

A Nasuni facilita a migração e a manutenção dos seus dados no Azure.  A Nasuni é uma solução líder de armazenamento nas naazur, dando aos clientes o desempenho e segurança que esperam das soluções on-prem, com economia na nuvem e escala.  Além do armazenamento de ficheiros de alto desempenho, a Nasuni e a Azure manuseiam backup e DR, permitindo-lhe partilhar e colaborar nos seus dados em todo o mundo com a gestão centralizada de armazenamento de ficheiros. 

Nasuni tem a experiência de facilitar a sua migração – começar hoje:https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrar para Talon FAST

Talon facilita que os clientes StorSimple 5000-7000 continuem a aproveitar os benefícios que tanto valorizavam na plataforma StorSimple (pequena pegada no local apoiada por recursos de nuvem ilimitados) com uma função ainda maior.  Com a solução Talon FAST, os clientes podem migrar e manter os seus dados no Azure, ao mesmo tempo que têm agora uma pegada de software ainda menor no local e adicionam benefícios como o bloqueio global de ficheiros, o espaço de nome global e a colaboração multi-site.  A Talon é uma solução líder do ecossistema Azure, trabalhando com clientes globais para migrar as suas cargas de trabalho de servidores de ficheiros no local para uma pegada consolidada, baseada no Azure, sem comprometer o fluxo de trabalho ou a experiência do utilizador.  

Saiba mais sobre como evoluir para uma https://www.talonstorage.com/alliances/microsoft-storsimpleempresa consolidada em nuvem em .


## <a name="migration---frequently-asked-questions"></a>Migração - Perguntas frequentes

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>P. Quando é que os dispositivos da série StorSimple 5000 e 7000 chegam ao fim do serviço? 

R. A série StorSimple 5000-7000 chega ao [fim do serviço](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. O fim do serviço implica que a Microsoft deixará de poder fornecer suporte tanto para hardware como para software destes dispositivos após julho de 2019. Recomendamos vivamente que comece a formular um plano para migrar os dados dos seus dispositivos agora.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>P. O que acontece com os dados que guardei em Azure?  

R. Pode continuar a utilizar os dados em Azure assim que os migrar para um serviço mais recente. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>P. O que acontece com os dados que guardei localmente no meu dispositivo StorSimple? 

R. Os dados que estão no dispositivo local podem ser copiados para o serviço mais recente, conforme descrito nos documentos de migração.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>P. O que acontece se eu quiser manter o meu aparelho da série StorSimple 5000/7000? 

R. Embora os serviços possam continuar a funcionar, a Microsoft deixará de poder fornecer suporte a hardware e software. A migração é fortemente recomendada para a continuidade das empresas.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>P. Que opções estão disponíveis para migrar dados dos dispositivos da série StorSimple 5000-7000? 

R. Dependendo do seu cenário, os utilizadores da série StorSimple 5000-7000 têm as seguintes opções de migração. 

 - **Upgrade para série 8000**: Use esta opção quando quiser continuar na plataforma StorSimple. 
 - **Migrar para O Sincronizado de Ficheiros Azure**: Utilize esta opção quando pretender mudar para o formato nativo do Azure. Pode utilizar o Azure File Sync para a gestão centralizada de ações de ficheiros. 

Pode contactar o Microsoft Support para discutir opções de migração não listadas aqui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>P. A migração para outras soluções de armazenamento é suportada?

R. Sim. A migração para outras soluções de armazenamento utilizando a cópia hospedeira dos dados é suportada.

### <a name="q-is-migration-supported-by-microsoft"></a>P. A migração é suportada pela Microsoft? 

R. Migrar de 5000 ou 7000 séries é uma operação totalmente apoiada. De facto, a Microsoft recomenda que se contacte para o Suporte antes de iniciar a migração. A migração é atualmente uma operação assistida. Se pretender migrar dados do seu dispositivo da série StorSimple 5000-7000, [abra um bilhete de suporte](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>P. Qual é o modelo de preços para ambas as opções de migração?

R. O custo da migração varia consoante a opção que escolher. Enquanto a migração em si é gratuita, se decidir fazer upgrade para uma série StorSimple 8000, haverá o custo do dispositivo de hardware. 

Da mesma forma, ao utilizar o Azure File Sync, podem aplicar-se as taxas de subscrição do serviço. Em cada caso, os clientes também terão de pagar os custos de armazenamento em curso. Consulte o seguinte para uma estimativa: 
- [Preços StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Preços AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>P.  Quanto tempo demora a completar uma migração?

R. O tempo para migrar os dados depende da quantidade dos dados e da opção de atualização selecionada. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>P. Qual é a data de fim de suporte para a série StorSimple 8000?

R. A data de fim de suporte para a série StorSimple 8000 é publicada [aqui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Passos seguintes
 - [Migrar dados de uma série StorSimple 5000-7000 para um dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrar dados de uma série StorSimple 5000-7000 para O Sincronizado de Ficheiros Azure](storsimple-5000-7000-afs-migration.md)
