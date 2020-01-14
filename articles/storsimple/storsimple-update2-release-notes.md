---
title: Notas de versão da atualização 2 para o StorSimple 8000 Series | Microsoft Docs
description: Descreve os novos recursos, problemas e soluções alternativas para a atualização 2 do StorSimple 8000 Series.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934063"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>Notas de versão da atualização 2 para o StorSimple 8000 Series

## <a name="overview"></a>Visão geral
As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos do StorSimple 8000 Series Update 2. Eles também contêm uma lista das atualizações de software, Driver e firmware de disco do StorSimple incluídas nesta versão. 

A atualização 2 pode ser aplicada a qualquer dispositivo StorSimple que executa a versão (GA) ou a atualização 0,1 até a atualização 1,2. A versão do dispositivo associada à atualização 2 é 6.3.9600.17673.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * Leva aproximadamente 4-7 horas para instalar essa atualização (incluindo as atualizações do Windows). 
> * A atualização 2 tem atualizações de software, driver LSI e firmware SSD.
> * Para novas versões, talvez você não veja as atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e, em seguida, verifique se há atualizações novamente, pois elas serão disponibilizadas em breve.
> 
> 

## <a name="whats-new-in-update-2"></a>O que há de novo na atualização 2
A atualização 2 apresenta os novos recursos a seguir.

* **Volumes fixados localmente** – em versões anteriores da série StorSimple 8000, blocos de dados eram em camadas para a nuvem com base no uso. Não havia como garantir que os blocos permaneçam no local. Na atualização 2, quando você cria um volume, pode designar um volume como fixado localmente e os dados primários desse volume não serão em camadas na nuvem. Os instantâneos de volumes fixados localmente ainda serão copiados para a nuvem para backup, de modo que a nuvem possa ser usada para fins de recuperação de desastres e mobilidade de dados. Além disso, você pode alterar o tipo de volume (ou seja, converter volumes em camadas em volumes fixados localmente e converter volumes localmente afixados em camadas). 
* **Aprimoramentos do dispositivo virtual storsimple** – anteriormente, a série storsimple 8000 posicionou o dispositivo virtual como uma solução de recuperação de desastre ou desenvolvimento/teste. Havia apenas um modelo de dispositivo virtual (modelo 1100). A atualização 2 apresenta dois modelos de dispositivo virtual: 
  
  * 8010 (anteriormente chamado de 1100) – sem alteração; o tem uma capacidade de 30 TB e usa o armazenamento standard do Azure.
  * 8020 – tem uma capacidade de 64 TB e usa o armazenamento Premium do Azure para melhorar o desempenho.
    
    Há um único VHD para ambos os modelos de dispositivo virtual (8010/8020). Quando você inicia o dispositivo virtual pela primeira vez, ele detecta os parâmetros da plataforma e aplica a versão do modelo correta.
* **Melhorias de rede** – a atualização 2 contém os seguintes aprimoramentos de rede:
  
  * Várias NICs podem ser habilitadas para a nuvem para que o failover possa ocorrer se uma NIC falhar.
  * Aprimoramentos de roteamento, com métricas fixas para blocos habilitados para nuvem.
  * Repetição online de recursos com falha antes de um failover.
  * Novos alertas para falhas de serviço.
* **Aprimoramentos de atualização** – na atualização 1,2 e anteriores, a série StorSimple 8000 foi atualizada por meio de dois canais: Windows Update para clustering, iSCSI e assim por diante, e Microsoft Update para binários e firmware.
    A atualização 2 usa Microsoft Update para todos os pacotes de atualização. Isso deve levar a um tempo menor de aplicação de patch ou de failover. 
* **Atualizações de firmware** – as seguintes atualizações de firmware estão incluídas:
  
  * LSI: lsi_sas2. sys versão do produto 2.00.72.10
  * Somente SSD (sem atualizações de HDD): XMGG, XGEG, KZ50, F6C2 e VR08
* **Suporte proativo** – a atualização 2 permite que a Microsoft extraia informações de diagnóstico adicionais do dispositivo. Quando nossa equipe de operações identifica dispositivos que estão tendo problemas, estamos mais bem equipados para coletar informações do dispositivo e diagnosticar problemas. Ao **aceitar a atualização 2, você nos permite fornecer esse suporte proativo**.    

## <a name="issues-fixed-in-update-2"></a>Problemas corrigidos na atualização 2
As tabelas a seguir fornecem um resumo dos problemas que foram corrigidos nas atualizações 2.    

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Interfaces de rede |Após uma atualização para a atualização 1, o serviço de StorSimple Manager relatou que as portas Dados2 e data3 falharam em um controlador. Este problema foi corrigido. |Sim |Não |
| 2 |Atualizações |Após uma atualização para a atualização 1, alertas de alarme audível ocorreram no portal clássico do Azure em vários dispositivos. Este problema foi corrigido. |Sim |Não |
| 3 |Autenticação Openstack |Ao usar o Openstack como seu provedor de serviços de nuvem, você pode receber um erro de que sua cadeia de caracteres de autenticação de nuvem era muito longa. Isto foi corrigido. |Sim |Não |

## <a name="known-issues-in-update-2"></a>Problemas conhecidos na atualização 2
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quorum de disco |Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 for desconectada, resultando em nenhum quorum de disco, o pool de armazenamento ficará offline. Ele permanecerá offline mesmo que os discos sejam reconectados. |Será necessário reinicializar o dispositivo. Se o problema persistir, entre em contato com Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |ID do controlador incorreta |Quando uma substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada do nó par, a ID do controlador pode aparecer inicialmente como a ID do controlador de par. Em raras circunstâncias, esse comportamento também pode ser visto após a reinicialização do sistema. |Não é necessária nenhuma ação do utilizador. Essa situação será resolvida depois que a substituição do controlador for concluída. |Sim |Não |
| 3 |Contas de armazenamento |O uso do serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso resultará em uma situação em que os dados do usuário não podem ser recuperados. | |Sim |Sim |
| 4 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para dispositivos de destino diferentes. O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após esse failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando você os exibir no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante a instalação do adaptador StorSimple para SharePoint, você precisa fornecer um IP de dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 6 |Proxy Web |Se sua configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de dispositivo para serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. |Verifique se a URL do proxy Web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Proxy Web |Se você configurar e habilitar o proxy Web em um dispositivo registrado, será necessário reiniciar o controlador ativo em seu dispositivo. | |Sim |Não |
| 8 |Alta latência de nuvem e alta carga de trabalho de e/s |Quando o dispositivo StorSimple encontra uma combinação de latências de nuvem muito altas (ordem de segundos) e alta carga de trabalho de e/s, os volumes do dispositivo entram em um estado degradado e as e/SS podem falhar com um erro "o dispositivo não está pronto". |Será necessário reinicializar manualmente os controladores de dispositivo ou executar um failover de dispositivo para se recuperar dessa situação. |Sim |Não |
| 9 |Azure PowerShell |Quando você usa o cmdlet do StorSimple **Get- &#124; AzureStorSimpleStorageAccountCredential Select-Object-First 1-Wait** para selecionar o primeiro objeto para que você possa criar um novo objeto **associado volumecontainer** , o cmdlet retorna todos os objetos. |Coloque o cmdlet entre parênteses da seguinte maneira: **(Get-Azure-StorSimpleStorageAccountCredential &#124; ) Select-Object-First 1-Wait** |Sim |Sim |
| 10 |Migração |Quando vários contêineres de volume são passados para migração, o ETA para backup mais recente é preciso apenas para o primeiro contêiner de volume. Além disso, a migração paralela será iniciada depois que os quatro primeiros backups no primeiro contêiner de volume forem migrados. |Recomendamos que você migre um contêiner de volume de cada vez. |Sim |Não |
| 11 |Migração |Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. |Será necessário adicionar esses volumes a uma política de backup para criar backups. |Sim |Sim |
| 12 |Migração |Após a conclusão da migração, o dispositivo da série 5000/7000 não deve acessar os contêineres de dados migrados. |Recomendamos que você exclua os contêineres de dados migrados depois que a migração for concluída e confirmada. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple que executa A atualização 1 não pode clonar ou executar a recuperação de desastre em um dispositivo que executa o software anterior à atualização 1. |Será necessário atualizar o dispositivo de destino para a atualização 1 para permitir essas operações |Sim |Sim |
| 14 |Migração |O backup de configuração para migração poderá falhar em um dispositivo da série 5000-7000 quando houver grupos de volumes sem volumes associados. |Exclua todos os grupos de volume vazios sem volumes associados e repita o backup de configuração. |Sim |Não |
| 15 |Azure PowerShell cmdlets e volumes localmente afixados |Você não pode criar um volume fixado localmente por meio de cmdlets Azure PowerShell. (Qualquer volume criado por meio de Azure PowerShell será colocado em camadas.) |Sempre use o serviço de StorSimple Manager para configurar volumes fixados localmente. |Sim |Não |
| 16 |Espaço disponível para volumes fixados localmente |Se você excluir um volume fixado localmente, o espaço disponível para novos volumes poderá não ser atualizado imediatamente. O serviço de StorSimple Manager atualiza o espaço local disponível aproximadamente a cada hora. |Aguarde uma hora antes de tentar criar o novo volume. |Sim |Não |
| 17 |volumes afixados localmente |O trabalho de restauração expõe o backup de instantâneo temporário no catálogo de backup, mas apenas durante o trabalho de restauração. Além disso, ele expõe um grupo de discos virtuais com o prefixo **tmpCollection** na página de **políticas de backup** , mas apenas durante o trabalho de restauração. |Esse comportamento pode ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados localmente e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Sim |Não |
| 18 |volumes afixados localmente |Se você cancelar um trabalho de restauração e um failover de controlador ocorrer imediatamente depois, o trabalho de restauração mostrará **falha** em vez de **cancelado**. Se um trabalho de restauração falhar e um failover de controlador ocorrer imediatamente depois, o trabalho de restauração mostrará **cancelado** em vez de **falha**. |Esse comportamento pode ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados localmente e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Sim |Não |
| 19 |volumes afixados localmente |Se você cancelar um trabalho de restauração ou se uma restauração falhar e um failover de controlador ocorrer, um trabalho de restauração adicional será exibido na página **trabalhos** . |Esse comportamento pode ocorrer se o trabalho de restauração tiver apenas volumes fixados localmente ou uma combinação de volumes fixados localmente e em camadas. Se o trabalho de restauração incluir apenas volumes em camadas, esse comportamento não ocorrerá. Não é necessária a intervenção do utilizador. |Sim |Não |
| 20 |volumes afixados localmente |Se você tentar converter um volume em camadas (criado e clonado com a atualização 1,2 ou anterior) em um volume fixado localmente e o dispositivo estiver ficando sem espaço ou houver uma interrupção de nuvem, os clones poderão ser corrompidos. |Esse problema ocorre apenas com volumes que foram criados e clonados com o software anterior à atualização 2. Isso deve ser um cenário infrequente. | | |
| 21 |Conversão de volume |Não atualize o ACRs anexado a um volume enquanto uma conversão de volume estiver em andamento (em camadas para fixadas localmente ou vice-versa). A atualização do ACRs pode resultar em dados corrompidos. |Se necessário, atualize o ACRs antes da conversão do volume e não faça nenhuma atualização adicional do ACR enquanto a conversão estiver em andamento. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Atualizações de controlador e firmware na atualização 2
Esta versão atualiza o driver e o firmware do disco em seu dispositivo.

* Para obter mais informações sobre a atualização do firmware LSI, consulte o artigo 3121900 da base de dados de conhecimento Microsoft. 
* Para obter mais informações sobre a atualização de firmware de disco, consulte o artigo 3121899 da base de dados de conhecimento Microsoft.

## <a name="virtual-device-updates-in-update-2"></a>Atualizações de dispositivo virtual na atualização 2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar a atualização 2](storsimple-install-update-2.md) em seu dispositivo StorSimple.

