---
title: Notas de versão da atualização 3 para o StorSimple 8000 Series
description: Descreve os novos recursos, problemas e soluções alternativas para o StorSimple 8000 Series Update 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275337"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Notas de versão da atualização 3 para seu dispositivo StorSimple série 8000

## <a name="overview"></a>Visão geral
As notas de versão a seguir descrevem os novos recursos e identificam os problemas críticos abertos do StorSimple 8000 Series Update 3. Eles também contêm uma lista das atualizações de software do StorSimple incluídas nesta versão. 

A atualização 3 pode ser aplicada a qualquer dispositivo StorSimple que executa a versão (GA) ou a atualização 0,1 até a atualização 2,2. A versão do dispositivo associada à atualização 3 é 6.3.9600.17759.

Examine as informações contidas nas notas de versão antes de implantar a atualização em sua solução do StorSimple.

> [!IMPORTANT]
> * A atualização 3 tem o software do dispositivo, o driver LSI e o firmware e as atualizações Storport e Spaceport. Leva aproximadamente 1,5 a 2 horas para instalar essa atualização. 
> * Para novas versões, talvez você não veja as atualizações imediatamente porque fazemos uma distribuição em fases das atualizações. Aguarde alguns dias e, em seguida, verifique se há atualizações novamente, pois elas serão disponibilizadas em breve.
> 
> 

## <a name="whats-new-in-update-3"></a>O que há de novo na atualização 3
As principais melhorias e correções de bugs a seguir foram feitas na atualização 3.

* **Alterações de recuperação de espaço automatizadas** – iniciando a atualização 3, os algoritmos de reclamação de espaço são executados no controlador em espera do sistema, resultando em uma execução mais rápida. Para obter mais informações sobre as portas necessárias para trabalhar com a reclamação de espaço, consulte os [requisitos de rede do StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Aprimoramentos de desempenho** – a atualização 3 melhorou o desempenho de leitura/gravação na nuvem.
* **Melhorias relacionadas à migração** – nesta versão, foram feitas várias correções de bugs e melhorias para o recurso de migração de dispositivos da série 5000/7000 para dispositivos da série 8000. Para obter mais informações sobre como usar o recurso de migração, acesse [migração do dispositivo da série 5000/7000 para o dispositivo da série 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Monitoramento de correções relacionadas** – nesta versão, bugs relacionados a gráficos de monitoramento, painel de serviço e painel de dispositivos foram corrigidos.

## <a name="issues-fixed-in-update-3"></a>Problemas corrigidos na atualização 3
As tabelas a seguir fornecem um resumo dos problemas que foram corrigidos na atualização 3.    

| Não | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- |
| 1 |Migração de dados no lado do host |Na versão anterior, o dispositivo de nuvem StorSimple estava ficando offline durante uma migração de dados no lado do host. Esse problema foi corrigido nesta versão. |Não |Sim |
| 2 |volumes afixados localmente |Na versão anterior, havia problemas relacionados a falhas de e/s, falhas de conversão de volume e falhas de caminho de DataPath para volumes fixados localmente. Esses problemas foram causados pela raiz e corrigidos nesta versão. |Sim |Não |
| 3 |Monitorização |Havia vários problemas relacionados a unidades de relatório e monitoramento, bem como a gráficos do painel de dispositivos em que informações incorretas foram exibidas para volumes localmente afixados. Esses problemas foram corrigidos nesta versão. |Sim |Não |
| 4 |Gravações pesadas e/s |Ao usar o StorSimple para cargas de trabalho que envolvem gravações pesadas, o usuário encontraria um bug incomum em que o conjunto de trabalhos estava sendo colocado em camadas na nuvem. Esse bug foi corrigido nesta versão. |Sim |Sim |
| 5 |Backup |Em determinadas instâncias raras, nas versões anteriores do software, quando o usuário realizou um backup de um clone remoto, ele teria erros de nuvem e a operação geraria um erro. Nesta versão, o problema é corrigido e a operação é concluída com êxito. |Sim |Sim |
| 6 |Política de cópia de segurança |Em determinadas instâncias raras, nas versões anteriores do software, houve um bug relacionado à exclusão da política de backup. Esse problema foi corrigido nesta versão. |Sim |Sim |

## <a name="known-issues-in-update-3"></a>Problemas conhecidos na atualização 3
A tabela a seguir fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários/solução alternativa | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quorum de disco |Em casos raros, se a maioria dos discos no compartimento EBOD de um dispositivo 8600 for desconectada, resultando em nenhum quorum de disco, o pool de armazenamento ficará offline. Ele permanecerá offline mesmo que os discos sejam reconectados. |Será necessário reinicializar o dispositivo. Se o problema persistir, entre em contato com Suporte da Microsoft para as próximas etapas. |Sim |Não |
| 2 |ID do controlador incorreta |Quando uma substituição do controlador é executada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada do nó par, a ID do controlador pode aparecer inicialmente como a ID do controlador de par. Em raras circunstâncias, esse comportamento também pode ser visto após a reinicialização do sistema. |Não é necessária nenhuma ação do utilizador. Essa situação será resolvida depois que a substituição do controlador for concluída. |Sim |Não |
| 3 |Contas de armazenamento |O uso do serviço de armazenamento para excluir a conta de armazenamento é um cenário sem suporte. Isso resultará em uma situação em que os dados do usuário não podem ser recuperados. | |Sim |Sim |
| 4 |Failover de dispositivo |Não há suporte para vários failovers de um contêiner de volume do mesmo dispositivo de origem para dispositivos de destino diferentes. O failover de um único dispositivo inativo para vários dispositivos fará com que os contêineres de volume no primeiro dispositivo com failover percam a propriedade dos dados. Após esse failover, esses contêineres de volume serão exibidos ou se comportarão de maneira diferente quando você os exibir no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante a instalação do adaptador StorSimple para SharePoint, você precisa fornecer um IP de dispositivo para que a instalação seja concluída com êxito. | |Sim |Não |
| 6 |Proxy Web |Se sua configuração de proxy Web tiver HTTPS como o protocolo especificado, a comunicação de dispositivo para serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos em seu dispositivo. |Verifique se a URL do proxy Web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-8000-configure-web-proxy.md). |Sim |Não |
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
| 20 |volumes afixados localmente |Se você tentar converter um volume em camadas (criado e clonado com a atualização 1,2 ou anterior) em um volume fixado localmente e o dispositivo estiver ficando sem espaço ou houver uma interrupção de nuvem, os clones poderão ser corrompidos. |Esse problema ocorre apenas com volumes que foram criados e clonados com o software anterior à atualização 2,1. Isso deve ser um cenário infrequente. | | |
| 21 |Conversão de volume |Não atualize o ACRs anexado a um volume enquanto uma conversão de volume estiver em andamento (em camadas para fixadas localmente ou vice-versa). A atualização do ACRs pode resultar em dados corrompidos. |Se necessário, atualize o ACRs antes da conversão do volume e não faça nenhuma atualização adicional do ACR enquanto a conversão estiver em andamento. | | |
| 22 |Atualizações |Ao aplicar a atualização 3, a página **manutenção** no portal clássico do Azure exibirá a seguinte mensagem relacionada à atualização 2-"o StorSimple 8000 Series Update 2 inclui a capacidade da Microsoft de coletar proativamente informações de log do seu dispositivo quando detectamos possíveis problemas". Isso é enganoso, pois indica que o dispositivo está sendo atualizado para a atualização 2. Depois que o dispositivo for atualizado com êxito para a atualização 3, essa mensagem desaparecerá. |Esse comportamento será corrigido em uma versão futura. |Sim |Não |

## <a name="controller-and-firmware-updates-in-update-3"></a>Atualizações de controlador e firmware na atualização 3
Esta versão tem atualizações de firmware e driver LSI. Para obter mais informações sobre como instalar o driver LSI e as atualizações de firmware, consulte [instalar a atualização 3](storsimple-install-update-3.md) em seu dispositivo StorSimple.

## <a name="virtual-device-updates-in-update-3"></a>Atualizações de dispositivo virtual na atualização 3
Esta atualização não pode ser aplicada ao dispositivo de nuvem StorSimple (também conhecido como o dispositivo virtual). Novos dispositivos virtuais precisarão ser criados. 

## <a name="next-step"></a>Passo seguinte
Saiba como [instalar a atualização 3](storsimple-install-update-3.md) em seu dispositivo StorSimple.

