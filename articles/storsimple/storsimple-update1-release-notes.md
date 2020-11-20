---
title: StorSimple 8000 Series Update 1.2 notas de lançamento Microsoft Docs
description: Descreve as novas funcionalidades, problemas e soluções alternativas para StorSimple 8000 Series Update 1.2.
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
ms.openlocfilehash: 3a5ba4b27ae19999237edb75c50db36ef57a10f1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956692"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Atualizar notas de lançamento 1.2 para o seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
As seguintes notas de lançamento descrevem as novas funcionalidades e identificam as questões abertas críticas para storSimple 8000 Series Update 1.2. Também contêm uma lista das atualizações de software, controlador e firmware StorSimple incluídas nesta versão. 

A atualização 1.2 pode ser aplicada a qualquer dispositivo StorSimple que esteja a executar o lançamento (GA), a atualização 0.1, a atualização 0.2 ou o software Update 0.3. A atualização 1.2 não está disponível se o seu dispositivo estiver a executar a Atualização 1 ou a Atualização 1.1. Se o seu dispositivo estiver a executar O Lançamento (GA), [contacte o Microsoft Support](./storsimple-8000-contact-microsoft-support.md) para o ajudar a instalar esta atualização.

A tabela que se segue lista as versões de software do dispositivo correspondentes às Atualizações 1, 1.1 e 1.2.

| Se a atualização de execução... | esta é a versão do software do seu dispositivo. |
| --- | --- |
| Atualização 1.2 |6.3.9600.17584 |
| Atualização 1.1 |6.3.9600.17521 |
| Atualização 1.0 |6.3.9600.17491 |

Por favor, reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple. Para obter mais informações, consulte como [instalar o Update 1.2 no seu dispositivo StorSimple](./storsimple-8000-install-update-5.md). 

> [!IMPORTANT]
> * Demora aproximadamente 5 a 10 horas a instalar esta atualização (incluindo as Atualizações do Windows). 
> * A atualização 1.2 tem atualizações de software, controlador LSI e firmware de disco. Para instalar, siga as instruções na [instalação Update 1.2 no seu dispositivo StorSimple](./storsimple-8000-install-update-5.md).
> * Para novas versões, pode não ver atualizações imediatamente porque fazemos um lançamento faseado das atualizações. Procure novamente atualizações dentro de alguns dias, uma vez que estas ficarão disponíveis em breve.
> 
> 

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2
Estas funcionalidades foram lançadas pela primeira vez com o Update 1 que foi disponibilizado a um conjunto limitado de utilizadores. Com a versão Update 1.2, a maioria dos utilizadores do StorSimple veria as seguintes novas funcionalidades e melhorias:

* **Migração de 5000-7000 para 8000 dispositivos da série** – Este lançamento introduz uma nova funcionalidade de migração que permite aos utilizadores de aparelhos da série StorSimple 5000-7000 migrarem os seus dados para um aparelho físico da série StorSimple 8000 ou um aparelho virtual. A funcionalidade de migração tem duas propostas de valor chave:                                                                  
  
  * **Continuidade do negócio,** permitindo a migração de dados existentes em aparelhos da série 5000-7000 para 8000 aparelhos da série.
  * **Ofertas melhoradas de recursos dos aparelhos da série 8000**, tais como uma gestão centralizada eficiente de vários aparelhos através do serviço StorSimple Manager, melhor classe de hardware e firmware atualizado, eletrodomésticos virtuais, mobilidade de dados e funcionalidades no futuro roteiro.
    
    Consulte o [guia de migração](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) para obter detalhes sobre como migrar uma série StorSimple 5000-7000 para um dispositivo da série 8000. 
* **A disponibilidade no Portal do Governo Azure** – StorSimple está agora disponível no portal do Governo Azure. Veja como [implantar um dispositivo StorSimple no Portal do Governo de Azure](./index.yml).
* **Suporte a outros fornecedores de serviços na nuvem** – Os outros fornecedores de serviços em nuvem suportados são o Amazon S3, o Amazon S3 com RRS, HP e OpenStack (beta).
* **Atualização para as mais recentes APIs de Armazenamento** – Com esta versão, o StorSimple foi atualizado para as mais recentes APIs do serviço de armazenamento Azure. Os dispositivos da série StorSimple 8000 que estão a executar versões de software pré-Actualização 1 (Lançamento, 0.1, 0.2 e 0.3) estão a utilizar versões do Serviço de Armazenamento Azure APIs mais antigos que 17 de julho de 2009. Conforme afirmado no anúncio atualizado sobre a [remoção das versões de serviço de Armazenamento](/archive/blogs/windowsazurestorage/microsoft-azure-storage-service-version-removal-update-extension-to-2016), até 1 de agosto de 2016, estas APIs serão depreciadas. É imperativo que aplique a StorSimple 8000 Series Update 1 antes de 1 de agosto de 2016. Se não o fizer, os dispositivos StorSimple deixarão de funcionar corretamente.
* **Suporte para armazenamento redundante de zona (ZRS)** – Com a atualização para a versão mais recente das APIs de armazenamento, a série StorSimple 8000 apoiará o Armazenamento Redundante da Zona (ZRS) para além do Armazenamento LocalMente Redundante (LRS) e do Armazenamento Geo-redundante (GRS). Consulte este [artigo sobre as opções de redundância do Azure Storage](../storage/common/storage-redundancy.md) para detalhes do ZRS.
* **Experiência de implementação e atualização inicial melhoradas** – Nesta versão, os processos de instalação e atualização foram melhorados. A instalação através do assistente de configuração é melhorada para fornecer feedback ao utilizador se a configuração da rede e as definições de firewall estiverem incorretas. Foram fornecidos cmdlets de diagnóstico adicionais para ajudá-lo na resolução de problemas de rede do dispositivo. Consulte o [artigo de resolução de problemas](./storsimple-8000-troubleshoot-deployment.md) para obter mais informações sobre os novos cmdlets de diagnóstico utilizados para a resolução de problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas fixados na Atualização 1.2
O quadro seguinte fornece um resumo das questões que foram corrigidas nas Atualizações 1.2, 1.1 e 1.    

| Não. | Funcionalidade | Problema | Fixo em Atualização | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell para StorSimple |Quando um utilizador acedeu remotamente ao dispositivo StorSimple utilizando o Windows PowerShell para StorSimple e, em seguida, iniciou o assistente de configuração, ocorreu uma falha assim que o Data 0 IP foi introduzido. Este bug está agora corrigido no Update 1. |Atualização 1 |Yes |Yes |
| 2 |Reposição de fábrica |Em alguns casos, quando realizou um reset de fábrica, o dispositivo StorSimple ficou preso e apresentou esta mensagem: **O reset para a fábrica está em andamento (fase 8)**. Isto aconteceu se pressionasse ctRL+C enquanto o cmdlet estava em andamento. Este inseto está agora arranjado. |Atualização 1 |Yes |No |
| 3 |Reposição de fábrica |Depois de um reset falhado da fábrica de controlador duplo, foi-lhe permitido proceder ao registo do dispositivo. Isto resultou numa configuração do sistema não suportada. No Update 1, é mostrada uma mensagem de erro e o registo é bloqueado num dispositivo que tem um reset de fábrica falhado. |Atualização 1 |Yes |No |
| 4 |Reposição de fábrica |Em alguns casos, foram levantados falsos alertas de incompatibilidade positiva. Os alertas incorretos de incompatibilidade deixarão de ser gerados em dispositivos em execução de Update 1. |Atualização 1 |Yes |No |
| 5 |Reposição de fábrica |Se um reset de fábrica foi interrompido antes da conclusão, o dispositivo entrou no modo de recuperação e não lhe permitiu aceder ao Windows PowerShell para storSimple. Este inseto está agora arranjado. |Atualização 1 |Yes |No |
| 6 |Recuperação após desastre |Foi corrigido um erro de recuperação de desastres (DR) em que a DR falharia durante a descoberta de cópias de segurança no dispositivo alvo. |Atualização 1 |Yes |Yes |
| 7 |LeDs de monitorização |Em certos casos, a monitorização dos LED na parte de trás do aparelho não indicava o estado correto. O LED azul foi desligado. Os DADOS 0 e DATA 1 LEDs estavam a piscar mesmo quando estas interfaces não estavam configuradas. A questão foi corrigida e os LED de monitorização indicam agora o estado correto. |Atualização 1 |Yes |No |
| 8 |LeDs de monitorização |Em certos casos, após a aplicação da Atualização 1, a luz azul do controlador ativo desligou-se, dificultando assim a identificação do controlador ativo. Esta questão foi corrigida neste lançamento de patch. |Atualização 1.2 |Yes |No |
| 9 |Interfaces de rede |Em versões anteriores, um dispositivo StorSimple configurado com um gateway não-roteado pode ficar offline. Nesta versão, a métrica de encaminhamento para os Dados 0 foi a mais baixa; portanto, mesmo que outras interfaces de rede estejam ativadas na nuvem, todo o tráfego em nuvem do dispositivo será encaminhado através de Dados 0. |Atualização 1 |Yes |Yes |
| 10 |Cópias de segurança |Um bug na Atualização 1 que fez com que as cópias de segurança falhassem após 24 dias foi corrigida no lançamento do patch Update 1.1. |Atualização 1.1 |Yes |Yes |
| 11 |Cópias de segurança |Um bug em versões anteriores resultou num fraco desempenho para instantâneos em nuvem com baixas taxas de mudança. Este bug foi corrigido neste lançamento de patch. |Atualização 1.2 |Yes |Yes |
| 12 |Atualizações |Um bug no Update 1 que relatou uma atualização falhada e fez com que os controladores entrassem no modo Recovery, foi corrigido neste lançamento de patch. |Atualização 1.2 |Yes |Yes |

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na Atualização 1.2
A tabela seguinte fornece um resumo de questões conhecidas nesta versão.

| Não. | Funcionalidade | Problema | Comentários/soluções alternativas | Aplica-se ao dispositivo físico | Aplica-se a dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em casos raros, se a maioria dos discos no recinto EBOD de um dispositivo 8600 forem desligados, resultando em nenhum quórum de disco, então a piscina de armazenamento estará offline. Permanecerá offline mesmo que os discos estejam novamente ligados. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte o Microsoft Support para os próximos passos. |Yes |No |
| 2 |ID do controlador incorreto |Quando uma substituição do controlador é efetuada, o controlador 0 pode aparecer como controlador 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de pares, o ID do controlador pode aparecer inicialmente como ID do controlador de pares. Em casos raros, este comportamento também pode ser visto após um reboot do sistema. |Não é necessária qualquer ação do utilizador. Esta situação resolver-se-á após a substituição do controlador. |Yes |No |
| 3 |Contas de armazenamento |A utilização do serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto conduzirá a uma situação em que os dados dos utilizadores não possam ser recuperados. |Yes |Yes | |
| 4 |Falha do dispositivo |Não são suportadas múltiplas falhas de um recipiente de volume do mesmo dispositivo de origem para diferentes dispositivos-alvo. A falha do dispositivo de um único dispositivo morto para vários dispositivos fará com que os recipientes de volume do primeiro dispositivo falhados percam a propriedade dos dados. Após tal falha, estes recipientes de volume aparecerão ou comportar-se-ão de forma diferente quando os vê no portal clássico do Azure. | |Yes |No |
| 5 |Instalação |Durante o Adaptador StorSimple para a instalação do SharePoint, é necessário fornecer um IP do dispositivo para que a instalação termine com sucesso. | |Yes |No |
| 6 |Proxy Web |Se a sua configuração de procuração web tiver HTTPS como o protocolo especificado, então a sua comunicação dispositivo-a-serviço será afetada e o dispositivo ficará offline. Os pacotes de suporte também serão gerados no processo, consumindo recursos significativos no seu dispositivo. |Certifique-se de que o URL de procuração web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](./storsimple-8000-configure-web-proxy.md). |Yes |No |
| 7 |Proxy Web |Se configurar e ativar o representante web num dispositivo registado, terá de reiniciar o controlador ativo no seu dispositivo. | |Yes |No |
| 8 |Alta latência em nuvem e alta carga de trabalho de E/O |Quando o seu dispositivo StorSimple encontra uma combinação de latências de nuvem muito elevadas (ordem de segundos) e carga de trabalho de E/S elevada, os volumes do dispositivo entram num estado degradado e o I/Os pode falhar com um erro de "dispositivo não pronto". |Terá de reiniciar manualmente os controladores do dispositivo ou executar um dispositivo que falhe para recuperar desta situação. |Yes |No |
| 9 |Azure PowerShell |Quando utilizar o cmdlet StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Aguarde** para selecionar o primeiro objeto para que possa criar um novo objeto **VolumeContainer,** o cmdlet devolve todos os objetos. |Embrulhe o cmdlet em parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Yes |Yes |
| 10 |Migração |Quando vários recipientes de volume são passados para migração, o ETA para a cópia de segurança mais recente é preciso apenas para o primeiro recipiente de volume. Além disso, a migração paralela começará após a migração dos primeiros 4 backups do primeiro recipiente de volume. |Recomendamos que emigre um recipiente de volume de cada vez. |Yes |No |
| 11 |Migração |Após a restauração, os volumes não são adicionados à política de backup ou ao grupo de discos virtuais. |Terá de adicionar estes volumes a uma política de backup para criar cópias de segurança. |Yes |Yes |
| 12 |Migração |Após a conclusão da migração, o dispositivo da série 5000/7000 não deve aceder aos recipientes de dados migrados. |Recomendamos que elimine os recipientes de dados migrados após a migração estar completa e comprometida. |Yes |No |
| 13 |Clone e DR |Um dispositivo StorSimple que executa o Update 1 não pode clonar ou executar a Recuperação de Desastres para um dispositivo que executa o software pré-actualização 1. |Terá de atualizar o dispositivo-alvo para atualizar 1 para permitir estas operações |Yes |Yes |
| 14 |Migração |A cópia de segurança de configuração para migração pode falhar num dispositivo da série 5000-7000 quando existem grupos de volume sem volumes associados. |Elimine todos os grupos de volume vazios sem volumes associados e, em seguida, re-teste a cópia de segurança da configuração. |Yes |No |

## <a name="physical-device-updates-in-update-12"></a>Atualizações de dispositivos físicos na Atualização 1.2
Se a atualização do patch 1.2 for aplicada a um dispositivo físico (versões em execução antes do Update 1), a versão do software mudará para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Atualizações de controlador e firmware na Atualização 1.2
Esta versão atualiza o controlador e o firmware do disco no seu dispositivo.

* Para obter mais informações sobre a atualização do controlador SAS, consulte [Update 1 para controladores LSI SAS no Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 
* Para obter mais informações sobre a atualização do firmware do disco, consulte [o firmware de disco Update 1 para o Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Atualizações de dispositivos virtuais no Update 1.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtuais terão de ser criados. 

## <a name="next-steps"></a>Passos seguintes
* [Instale a Atualização 1.2 no seu dispositivo](./storsimple-8000-install-update-5.md).