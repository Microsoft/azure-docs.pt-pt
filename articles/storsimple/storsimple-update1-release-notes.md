---
title: StorSimple 8000 Series Update 1.2 notas de lançamento / Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções para storSimple 8000 Series Update 1.2.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60531000"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Atualizar 1.2 notas de lançamento para o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões críticas em aberto para o StorSimple 8000 Series Update 1.2. Também contêm uma lista das atualizações de software, controlador e firmware do storSimple incluídas nesta versão. 

A atualização 1.2 pode ser aplicada a qualquer dispositivo StorSimple que execute o Lançamento (GA), Atualização 0.1, Atualização 0.2 ou software Update 0.3. A atualização 1.2 não está disponível se o seu dispositivo estiver a executar o Update 1 ou o Update 1.1. Se o seu dispositivo estiver a funcionar, [contacte o Microsoft Support](storsimple-contact-microsoft-support.md) para o ajudar a instalar esta atualização.

A tabela seguinte lista as versões do software do dispositivo correspondentes às Atualizações 1, 1.1 e 1.2.

| Se a atualização em execução ... | esta é a versão do software do seu dispositivo. |
| --- | --- |
| Atualização 1.2 |6.3.9600.17584 |
| Atualização 1.1 |6.3.9600.17521 |
| Atualização 1.0 |6.3.9600.17491 |

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple. Para mais informações, consulte como instalar o [Update 1.2 no seu dispositivo StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Demora aproximadamente 5 a 10 horas a instalar esta atualização (incluindo as Atualizações do Windows). 
> * A atualização 1.2 tem software, atualizações de controlador LSI e firmware de disco. Para instalar, siga as instruções de [instalação Do Update 1.2 no seu dispositivo StorSimple](storsimple-install-update-1.md).
> * Para novos lançamentos, pode não ver as atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Procure novamente as atualizações dentro de alguns dias, uma vez que estas estarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2
Estas funcionalidades foram lançadas pela primeira vez com o Update 1 que foi disponibilizado a um conjunto limitado de utilizadores. Com o lançamento do Update 1.2, a maioria dos utilizadores do StorSimple veria as seguintes novas funcionalidades e melhorias:

* **Migração de 5000-7000 dispositivos** da série – Esta versão introduz uma nova funcionalidade de migração que permite aos utilizadores da série StorSimple 5000-7000 migrarem os seus dados para um aparelho físico da série StorSimple 8000 ou para um aparelho virtual. A funcionalidade de migração tem duas propostas de valor chave:                                                                  
  
  * **Continuidade do negócio**, permitindo a migração de dados existentes em 5000-7000 aparelhos da série para 8000 aparelhos da série.
  * **Ofertas melhoradas de funcionalidades dos aparelhos da série 8000**, como uma gestão centralizada eficiente de vários aparelhos através do serviço StorSimple Manager, melhor classe de hardware e firmware atualizado, eletrodomésticos virtuais, mobilidade de dados e funcionalidades no futuro roteiro.
    
    Consulte o guia de [migração](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) para obter detalhes sobre como migrar uma série StorSimple 5000-7000 para um dispositivo da série 8000. 
* **A disponibilidade no Portal do Governo Azure** – StorSimple já está disponível no portal do Governo Azure. Veja como [implementar um dispositivo StorSimple no Portal do Governo Azure](storsimple-deployment-walkthrough-gov.md).
* **Suporte para outros fornecedores** de serviços na nuvem – Os outros fornecedores de serviços na nuvem suportados são amazon S3, Amazon S3 com RRS, HP e OpenStack (beta).
* **Atualização para** as mais recentes APIs de Armazenamento – Com esta versão, o StorSimple foi atualizado para as mais recentes APIs do serviço de armazenamento Azure. Os dispositivos da série StorSimple 8000 que estão a executar versões de software pré-Actualização 1 (Lançamento, 0.1, 0.2 e 0.3) estão a utilizar versões do Serviço de Armazenamento Azure APIs mais antigas do que 17 de julho de 2009. Tal como indicado no anúncio atualizado sobre a [remoção das versões do serviço de armazenamento](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), até 1 de agosto de 2016, estas APIs serão depreciadas. É imperativo que aplique o StorSimple 8000 Series Update 1 antes de 1 de agosto de 2016. Se não o fizer, os dispositivos StorSimple deixarão de funcionar corretamente.
* **Suporte para Armazenamento Redundante de Zona (ZRS)** – Com a atualização para a versão mais recente das APIs de armazenamento, a série StorSimple 8000 irá suportar o Armazenamento Redundante da Zona Redundante (ZRS) para além do Armazenamento Redundante Local (LRS) e armazenamento geo-redundante (GRS). Consulte este artigo sobre as opções de [redundância](../storage/common/storage-redundancy.md) do Armazenamento Azure para detalhes do ZRS.
* Experiência de **implementação e atualização inicial melhorada** – Nesta versão, os processos de instalação e atualização foram melhorados. A instalação através do assistente de configuração é melhorada para fornecer feedback ao utilizador se a configuração da rede e as definições de firewall estiverem incorretas. Foram fornecidos cmdlets de diagnóstico adicionais para ajudá-lo com a colocação de problemas em rede do dispositivo. Consulte o artigo de implementação de [problemas](storsimple-troubleshoot-deployment.md) para obter mais informações sobre os novos cmdlets de diagnóstico utilizados para resolução de problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na Atualização 1.2
O quadro seguinte apresenta um resumo das questões que foram corrigidas nas Atualizações 1.2, 1.1 e 1.    

| Não. | Funcionalidade | Problema | Corrigido em Atualização | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell para StorSimple |Quando um utilizador acedeu remotamente ao dispositivo StorSimple utilizando o Windows PowerShell para o StorSimple e, em seguida, iniciou o assistente de configuração, ocorreu uma falha assim que o Data 0 IP foi entrada. Este bug encontra-se agora fixado na Atualização 1. |Atualização 1 |Sim |Sim |
| 2 |Reposição de fábrica |Em alguns casos, quando realizou um reset de fábrica, o dispositivo StorSimple ficou preso e exibiu esta mensagem: **O reset para a fábrica está em andamento (fase 8)**. Isto aconteceu se pressionassecte CTRL+C enquanto o cmdlet estava em andamento. Este inseto está agora arranjado. |Atualização 1 |Sim |Não |
| 3 |Reposição de fábrica |Após um reset falhado da fábrica de controlador esfarando, foi-lhe permitido proceder ao registo do dispositivo. Isto resultou numa configuração do sistema não suportado. No Update 1, é mostrada uma mensagem de erro e o registo é bloqueado num dispositivo que tem um reset de fábrica falhado. |Atualização 1 |Sim |Não |
| 4 |Reposição de fábrica |Em alguns casos, foram levantados falsos alertas positivos de incompatibilidade. Os alertas de desfasamento incorretos deixarão de ser gerados em dispositivos que executam o Update 1. |Atualização 1 |Sim |Não |
| 5 |Reposição de fábrica |Se um reset de fábrica foi interrompido antes da conclusão, o dispositivo entrou no modo de recuperação e não lhe permitiu aceder ao Windows PowerShell para o StorSimple. Este inseto está agora arranjado. |Atualização 1 |Sim |Não |
| 6 |Recuperação após desastre |Foi corrigido um bug de recuperação de desastres (DR) em que o DR falharia durante a descoberta de cópias de segurança no dispositivo-alvo. |Atualização 1 |Sim |Sim |
| 7 |LeDs de monitorização |Em certos casos, a monitorização dos LED na parte de trás do aparelho não indicou o estado correto. O LED azul foi desligado. DADOS 0 e DADOS 1 OS LEDs estavam a piscar mesmo quando estas interfaces não estavam configuradas. A questão foi corrigida e os LEDs de monitorização indicam agora o estado correto. |Atualização 1 |Sim |Não |
| 8 |LeDs de monitorização |Em certos casos, após a aplicação da Atualização 1, a luz azul do controlador ativo desligou-se, dificultando assim a identificação do controlador ativo. Esta questão foi corrigida nesta versão de patch. |Atualização 1.2 |Sim |Não |
| 9 |Interfaces de rede |Em versões anteriores, um dispositivo StorSimple configurado com um gateway não-resaída pode ficar offline. Nesta versão, a métrica de encaminhamento para dados 0 foi feita como a mais baixa; portanto, mesmo que outras interfaces de rede estejam ativadas pela nuvem, todo o tráfego em nuvem do dispositivo será encaminhado através do Data 0. |Atualização 1 |Sim |Sim |
| 10 |Cópias de segurança |Um bug no Update 1 que fez com que as cópias de segurança falhassem após 24 dias foi corrigido no lançamento do patch Update 1.1. |Atualização 1.1 |Sim |Sim |
| 11 |Cópias de segurança |Um bug em versões anteriores resultou num fraco desempenho para instantâneos em nuvem com baixas taxas de variação. Este bug foi corrigido nesta libertação de patch. |Atualização 1.2 |Sim |Sim |
| 12 |Atualizações |Um bug no Update 1 que reportou uma atualização falhada e fez com que os controladores entrassem no modo Recovery, foi corrigido nesta versão de patch. |Atualização 1.2 |Sim |Sim |

## <a name="known-issues-in-update-12"></a>Questões conhecidas na Atualização 1.2
A tabela que se segue apresenta um resumo das questões conhecidas nesta versão.

| Não. | Funcionalidade | Problema | Comentários/suposições | Aplica-se ao dispositivo físico | Aplica-se a dispositivovirtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum do disco |Em casos raros, se a maioria dos discos no recinto do EBOD de um dispositivo 8600 for desligado, resultando em nenhum quórum de disco, então o depósito estará offline. Permanecerá offline mesmo que os discos estejam reconectados. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte o Microsoft Support para os próximos passos. |Sim |Não |
| 2 |ID do controlador incorreto |Quando uma substituição do controlador é efetuada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de pares, o ID do controlador pode aparecer inicialmente como id do controlador de pares. Em casos raros, este comportamento também pode ser visto após um reboot do sistema. |Não é necessária qualquer ação do utilizador. Esta situação resolver-se-á após a substituição do controlador estar completa. |Sim |Não |
| 3 |Contas de armazenamento |A utilização do serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto conduzirá a uma situação em que os dados dos utilizadores não podem ser recuperados. |Sim |Sim | |
| 4 |Falha no dispositivo |Não são suportadas múltiplas falhas de um contentor de volume do mesmo dispositivo de origem para diferentes dispositivos-alvo. A falha do dispositivo de um único dispositivo morto para vários dispositivos fará com que os contentores de volume do primeiro falhado sobre o dispositivo percam a propriedade de dados. Após tal falha, estes contentores de volume aparecerão ou comportar-se-ão de forma diferente quando os vires no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o Adaptador StorSimple para a instalação do SharePoint, é necessário fornecer um IP do dispositivo para que a instalação termine com sucesso. | |Sim |Não |
| 6 |Proxy Web |Se a configuração do seu proxy web tiver HTTPS como protocolo especificado, então a sua comunicação dispositivo-a-serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. |Certifique-se de que o URL de procuração web tem HTTP como protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Proxy Web |Se configurar e ativar o proxy web num dispositivo registado, terá de reiniciar o controlador ativo no seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem alta e alta carga de trabalho em I/S |Quando o seu dispositivo StorSimple encontra uma combinação de latenciências de nuvem muito alta (ordem de segundos) e alta carga de trabalho em I/S, os volumes do dispositivo entram em estado degradado e o I/Os pode falhar com um erro "dispositivo não pronto". |Terá de reiniciar manualmente os controladores do dispositivo ou realizar uma falha no dispositivo para se recuperar desta situação. |Sim |Não |
| 9 |Azure PowerShell |Quando utilizar o StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** para selecionar o primeiro objeto para que possa criar um novo objeto **VolumeContainer,** o cmdlet devolve todos os objetos. |Embrulhe o cmdlet em parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sim |Sim |
| 10 |Migração |Quando vários contentores de volume são passados para migração, o ETA para a última cópia de segurança é preciso apenas para o primeiro recipiente de volume. Além disso, a migração paralela começará após a migração das primeiras 4 cópias de segurança no primeiro contentor de volume. |Recomendamos que emigra um recipiente de volume de cada vez. |Sim |Não |
| 11 |Migração |Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. |Terá de adicionar estes volumes a uma política de backup para criar backups. |Sim |Sim |
| 12 |Migração |Após a migração estar concluída, o dispositivo da série 5000/7000 não deve aceder aos recipientes de dados migrados. |Recomendamos que apague os recipientes de dados migrados após a migração estar completa e comprometida. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple que executa o Update 1 não pode clonar ou executar a Recuperação de Desastres num dispositivo que executa o software pré-actualização 1. |Terá de atualizar o dispositivo-alvo para atualizar 1 para permitir estas operações |Sim |Sim |
| 14 |Migração |A cópia de segurança de configuração para migração pode falhar num dispositivo da série 5000-7000 quando há grupos de volume sem volumes associados. |Elimine todos os grupos de volume vazios sem volumes associados e, em seguida, tente novamente a cópia de segurança da configuração. |Sim |Não |

## <a name="physical-device-updates-in-update-12"></a>Atualizações de dispositivos físicos no Update 1.2
Se a atualização de patch 1.2 for aplicada a um dispositivo físico (versões de execução antes do Update 1), a versão do software mudará para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Atualizações do controlador e do firmware no Update 1.2
Esta versão atualiza o controlador e o firmware do disco no seu dispositivo.

* Para obter mais informações sobre a atualização do controlador SAS, consulte [o Update 1 para controladores LSI SAS no Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 
* Para obter mais informações sobre a atualização do firmware do disco, consulte [o Firmware Disk Update 1 para o Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Atualizações de dispositivos virtuais no Update 1.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Serão necessários novos dispositivos virtuais. 

## <a name="next-steps"></a>Passos seguintes
* [Instale o Update 1.2 no seu dispositivo](storsimple-install-update-1.md).

