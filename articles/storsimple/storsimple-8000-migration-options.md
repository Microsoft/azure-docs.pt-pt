---
title: Avaliar opções para migrar dados do StorSimple 5000-7000 Series | Microsoft Docs
description: Fornece uma visão geral das opções para migrar dados do StorSimple 5000-7000 Series.
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
ms.openlocfilehash: d51891c076f1784fbddb88bfaed28ac1d889afdc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227770"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opções para migrar dados do StorSimple série 5000-7000 

> [!IMPORTANT]
> Em 9 de julho de 2019, a série StorSimple 5000/7000 atingirá o status de fim de suporte (EOS). Recomendamos que os clientes da série StorSimple 5000/7000 migrem para uma das alternativas descritas no documento.

A série StorSimple 5000-7000 está atingindo o [fim do suporte](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. Os clientes que estão executando o StorSimple 5000-7000 Series têm a opção de atualizar para outros serviços híbridos de terceiros do Azure. Este artigo descreve as opções híbridas do Azure disponíveis para migrar dados. 

## <a name="migration-options"></a>Opções de migração

Os clientes que usam a série StorSimple 5000-7000 têm opções do Azure ou de terceiros.

### <a name="azure-options"></a>Opções do Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Atualizar para a série StorSimple 8000

Atualize para a série StorSimple 8000 e, portanto, continue na plataforma StorSimple.  Este caminho de atualização exige que os clientes substituam seus dispositivos da série 5000-7000 por uma série 8000. Os dados são migrados do dispositivo da série 5000-7000 usando a ferramenta de migração. Depois que a migração for concluída com êxito, os dispositivos da série StorSimple 8000 continuarão a hierarquizar dados para o armazenamento de BLOBs do Azure. 

Para obter mais informações sobre como migrar dados usando uma série StorSimple 8000, vá para [migrar dados do storsimple 5000-7000 Series para o dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Migrar para o Azure File Sync

Essa nova opção de migração permite que os clientes armazenem os compartilhamentos de arquivos de sua organização nos arquivos do Azure. Esses arquivos compartilhamentos são então centralizados para acesso local usando Sincronização de Arquivos do Azure (AFS). O AFS pode ser implantado em um host do Windows Server. A migração de dados real é então executada como uma cópia de host ou usando a ferramenta de migração.

Para obter mais informações sobre como migrar dados para Sincronização de Arquivos do Azure, vá para [migrar dados do StorSimple 5000-7000 Series para sincronização de arquivos do Azure](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Opções de terceiros

#### <a name="migrate-to-panzura-freedom-nas"></a>Migrar para o NAS de Panzura Freedom

Os clientes do StorSimple 5000-7000 podem optar por migrar para o Panzura Freedom NAS para manter seus dados no Azure. A solução de Panzura Freedom fornece uma solução de NAS que abrange data centers, escritórios, nuvens públicas e privadas. A solução permite fluxos de trabalho de dados locais, híbridos e na nuvem para clientes NFS, SMB e móveis. 

Essa migração é suportada pelo Panzura e os clientes podem começar solicitando o suporte de migração do [site do Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Migrar para o Cohesity

O Cohesity permite que você migre dados do StorSimple 5000 – 7000 para a plataforma de dados Cohesity no Azure. A plataforma de dados Cohesity é uma solução de escala da Web definida por software que consolida arquivos, backups, objetos e VMs em uma única solução nativa de nuvem. Após a migração para a plataforma de dados, você pode gerenciar, proteger e provisionar dados e aplicativos da nuvem para o núcleo por meio de um único painel de vidro. Com o Cohesity, comece com até três nós. 

Saiba mais sobre [a migração para a plataforma de dados Cohesity](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrar para o Nasuni

O Nasuni facilita para os clientes do StorSimple 5000-7000 migrarem e manterem seus dados no Azure.  O Nasuni é uma solução líder de armazenamento NAS baseada no Azure, oferecendo aos clientes o desempenho e a segurança que eles esperam de soluções locais, com economia e escala de nuvem.  Além do armazenamento de arquivos de alto desempenho, o Nasuni e o Azure lidam com o backup e a recuperação de desastres, permitindo que você compartilhe e colabore em seus dados em todo o mundo com o gerenciamento centralizado de armazenamento de arquivos. 

A Nasuni tem a experiência de facilitar sua migração – comece hoje mesmo: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrar para o Talon FAST

O Talon facilita para os clientes do StorSimple 5000-7000 continuarem a aproveitar os benefícios que eles valorizam tanto na plataforma StorSimple (pequena superfície no local apoiada por recursos de nuvem ilimitados) com uma função ainda maior.  Com a solução Talon FAST, os clientes podem migrar e manter seus dados no Azure, embora agora tenham uma superfície de software apenas menor, e adicionando benefícios, como o bloqueio de arquivo global, o namespace global e a colaboração multissite.  O Talon é uma solução líder de ecossistema do Azure, trabalhando com clientes globais para migrar suas cargas de trabalho de servidor de arquivos locais para uma superfície consolidada e baseada no Azure sem comprometer o fluxo de trabalho do usuário ou a experiência.  

Saiba mais sobre como evoluir para uma empresa consolidada na nuvem em https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Migração-perguntas frequentes

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>P. Quando os dispositivos StorSimple 5000 e 7000 Series atingem o fim do serviço? 

A. A série StorSimple 5000-7000 atinge o [fim do serviço](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) em julho de 2019. O fim do serviço implica que a Microsoft não será mais capaz de fornecer suporte para hardware e software desses dispositivos depois de julho de 2019. É altamente recomendável que você inicie formular um plano para migrar os dados de seus dispositivos agora.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>P. O que acontece com os dados que armazenei no Azure?  

A. Você pode continuar a usar os dados no Azure depois de migrá-los para um serviço mais recente. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>P. O que acontece com os dados que armazenei localmente no meu dispositivo StorSimple? 

A. Os dados que estão no dispositivo local podem ser copiados para o serviço mais recente, conforme descrito nos documentos de migração.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>P. O que acontece se eu quiser manter meu dispositivo StorSimple série 5000/7000? 

A. Embora os serviços possam continuar funcionando, a Microsoft não será mais capaz de fornecer suporte a hardware e software. A migração é altamente recomendável para continuidade dos negócios.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>P. Quais opções estão disponíveis para migrar dados de dispositivos StorSimple da série 5000-7000? 

A. Dependendo de seu cenário, os usuários da série StorSimple 5000-7000 têm as opções de migração a seguir. 

 - **Atualizar para a série 8000**: Use essa opção quando desejar continuar na plataforma StorSimple. 
 - **Migrar para sincronização de arquivos do Azure**: Use essa opção quando desejar alternar para o formato nativo do Azure. Você pode usar Sincronização de Arquivos do Azure para o gerenciamento centralizado de compartilhamentos de arquivos. 

Você pode entrar em contato com Suporte da Microsoft para discutir as opções de migração não listadas aqui.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>P. Há suporte para a migração para outras soluções de armazenamento?

A. Sim. Há suporte para a migração para outras soluções de armazenamento usando a cópia de host dos dados.

### <a name="q-is-migration-supported-by-microsoft"></a>P. Há suporte para migração da Microsoft? 

A. A migração da série 5000 ou 7000 é uma operação totalmente suportada. Na verdade, a Microsoft recomenda acessar o suporte antes de iniciar a migração. Atualmente, a migração é uma operação assistida. Se você pretende migrar dados de seu dispositivo StorSimple da série 5000-7000, [abra um tíquete de suporte](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>P. Qual é o modelo de preços para ambas as opções de migração?

A. O custo da migração varia dependendo da opção escolhida. Embora a migração em si seja gratuita, se você decidir atualizar para uma série StorSimple 8000, haverá o custo do dispositivo de hardware. 

Da mesma forma, ao usar Sincronização de Arquivos do Azure, as tarifas de assinatura para o serviço podem ser aplicadas. Em cada caso, os clientes também terão de pagar custos contínuos de armazenamento. Consulte o seguinte para obter uma estimativa: 
- [Preços do StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Preços AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>P.  Quanto tempo leva para concluir uma migração?

A. O tempo para migrar dados depende da quantidade de dados e da opção de atualização selecionada. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>P. Qual é o fim da data de suporte da série StorSimple 8000?

A. O fim da data de suporte para a série StorSimple 8000 é publicado [aqui](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Passos seguintes
 - [Migre dados de uma série StorSimple 5000-7000 para um dispositivo da série 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrar dados de uma série StorSimple 5000-7000 para Sincronização de Arquivos do Azure](storsimple-5000-7000-afs-migration.md)
