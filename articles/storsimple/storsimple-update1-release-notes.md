---
title: Notas de versão 1.2 do StorSimple 8000 Series Update | Documentos da Microsoft
description: Descreve os novos recursos, problemas e soluções alternativas para StorSimple 8000 Series Update 1.2.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531000"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Atualizar notas de versão 1.2 do seu dispositivo da série StorSimple 8000

## <a name="overview"></a>Descrição geral
As notas de versão seguintes descrevem os novos recursos e identificar os problemas em aberto críticos para a StorSimple 8000 Series Update 1.2. Também contêm uma lista de software StorSimple, driver e atualizações de firmware do disco incluídas nesta versão. 

Atualização 1.2 pode ser aplicada a todos os dispositivos StorSimple com o lançamento (GA), atualização 0.1, atualização 0.2 ou atualização 0.3 de software. Atualização 1.2 não está disponível se o seu dispositivo está a executar atualização 1 ou atualização 1.1. Se o seu dispositivo está em execução lançamento (GA), [contacte o Microsoft Support](storsimple-contact-microsoft-support.md) para ajudá-lo com a instalação desta atualização.

A tabela seguinte lista as versões de software de dispositivo correspondente às atualizações de 1, 1.1 e 1.2.

| Se executar a atualização... | Esta é a sua versão de software do dispositivo. |
| --- | --- |
| Atualização 1.2 |6.3.9600.17584 |
| Atualização 1.1 |6.3.9600.17521 |
| Atualização 1.0 |6.3.9600.17491 |

Reveja as informações contidas nas notas de versão antes de implementar a atualização na sua solução StorSimple. Para obter mais informações, consulte como [instalar atualização 1.2 no dispositivo StorSimple](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Demora cerca de 5 a 10 horas para instalar esta atualização (incluindo as atualizações do Windows). 
> * Atualização 1.2 tem o software, o driver de LSI e atualizações de firmware do disco. Para instalar, siga as instruções em [instalar atualização 1.2 no dispositivo StorSimple](storsimple-install-update-1.md).
> * Para novas versões, poderá não ver atualizações imediatamente porque fazemos uma implementação faseada de atualizações. Análise de atualizações em alguns dias novamente como estes ficarão disponível em breve.
> 
> 

## <a name="whats-new-in-update-12"></a>O que há de novo na atualização 1.2
Esses recursos foram lançado pela primeira vez com o Update 1 que foi disponibilizada para um conjunto limitado de utilizadores. Com o lançamento de atualização 1.2, a maioria dos usuários StorSimple veria as seguintes novas funcionalidades e melhorias:

* **A migração de séries 5000 a 7000 para dispositivos de 8000 série** – essa versão apresenta uma nova funcionalidade de migração, que permite o StorSimple 5000 a 7000 série da aplicação aos utilizadores migrar os seus dados para uma virtual ou de uma aplicação física de série StorSimple 8000 aplicação. A funcionalidade de migração tem dois propostas de valor da chave:                                                                  
  
  * **Continuidade do negócio**, ao ativar a migração dos dados existentes em aplicações das séries 5000 a 7000 para aplicações de 8000 série.
  * **Melhorada a ofertas de funcionalidade das aplicações de 8000 série de**, como o gerenciamento centralizado eficiente de várias aplicações através do serviço StorSimple Manager, melhor classe de hardware e atualizar o firmware, de aplicações virtuais, de mobilidade de dados, as funcionalidades e o mapa de futuros.
    
    Consulte a [guia de migração](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) para detalhes sobre como migrar um StorSimple 5000-7000 série para um dispositivo da 8000 série. 
* **Disponibilidade no Portal do Azure Government** – StorSimple está agora disponível no portal do Azure Government. Veja como [implementar um dispositivo StorSimple no Portal do Azure Government](storsimple-deployment-walkthrough-gov.md).
* **Suporte para outros fornecedores de serviços cloud** – as outros fornecedores de serviços cloud suportados são Amazon S3, Amazon S3 com RRS, HP e OpenStack (beta).
* **Atualização para APIs de armazenamento mais recente** – com esta versão, o StorSimple foi atualizado para as APIs do serviço armazenamento do Azure mais recente. Dispositivos da série StorSimple 8000 que executem versões do software de pré-atualização 1 (versão, 0,1 e 0,3 0,2) estiver a utilizar versões das APIs do serviço de armazenamento do Azure com mais de 17 de Julho de 2009. Conforme indicado na atualizada [anúncio sobre remoção das versões de serviço de armazenamento](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), por dia 1 de Agosto de 2016, essas APIs serão preteridas. É imperativo que aplique o StorSimple 8000 Series Update 1 antes de 1 de Agosto de 2016. Se não conseguir fazê-lo, dispositivos StorSimple deixará de funcionar corretamente.
* **Suporte para com redundância de armazenamento zona (ZRS)** – com a atualização para a versão mais recente das APIs de armazenamento, a série StorSimple 8000 irá suportar com redundância de armazenamento zona (ZRS), além de armazenamento localmente redundante (LRS) e o armazenamento georredundante (GRS ). Consulte este [artigo sobre as opções de redundância de armazenamento do Azure](../storage/common/storage-redundancy.md) para obter detalhes ZRS.
* **Melhorada a experiência de implantação e atualização inicial** – nesta versão, os processos de instalação e atualização foram aprimorados. A instalação através do Assistente de configuração foi melhorada para fornecer comentários ao utilizador se a configuração de rede e as definições da firewall estão incorretas. Foram fornecidos cmdlets de diagnóstico adicionais para ajudá-lo com a resolução de problemas de rede do dispositivo. Consulte a [artigo sobre implementação de resolução de problemas](storsimple-troubleshoot-deployment.md) para obter mais informações sobre os novos cmdlets de diagnóstico utilizados para resolução de problemas.

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na atualização 1.2
A tabela seguinte fornece um resumo dos problemas que foram corrigidas em atualizações de 1, 1.1 e 1.2.    

| Não. | Funcionalidade | Problema | Corrigido na atualização | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell para StorSimple |Quando um utilizador aceder remotamente o dispositivo StorSimple com o Windows PowerShell para StorSimple e, em seguida, iniciar o Assistente de configuração, uma falha ocorreu logo que dados 0 IP foi introduzido. Esse bug agora é corrigido em atualização 1. |Atualização 1 |Sim |Sim |
| 2 |Reposição de fábrica |Em alguns casos, quando efetuar uma reposição de fábrica, o dispositivo StorSimple tornou-se com dificuldades e apresentada esta mensagem: **Reposição de fábrica está em curso (fase 8)** . Isto aconteceu se premir CTRL + C, enquanto o cmdlet estava em curso. Esse bug agora é corrigido. |Atualização 1 |Sim |Não |
| 3 |Reposição de fábrica |Depois de repor um alocador de controlador dupla com falha, eram permitidos para continuar o registo do dispositivo. Isso resultou numa configuração de sistema não suportado. Na atualização 1, é apresentada uma mensagem de erro e registo é bloqueado num dispositivo que tem uma falha na reposição de fábrica. |Atualização 1 |Sim |Não |
| 4 |Reposição de fábrica |Em alguns casos, foram geradas alertas de erro de correspondência de positivo FALSO. Já não serão gerados alertas de erro de correspondência incorreto em dispositivos com o Update 1. |Atualização 1 |Sim |Não |
| 5 |Reposição de fábrica |Se uma reposição de fábrica foi interrompida antes da conclusão, o dispositivo introduziu o modo de recuperação e não permitia que acesse Windows PowerShell para StorSimple. Esse bug agora é corrigido. |Atualização 1 |Sim |Não |
| 6 |Recuperação após desastre |Foi corrigido um erro de (DR recuperação) de desastre em que DR falharia durante a deteção de cópias de segurança no dispositivo de destino. |Atualização 1 |Sim |Sim |
| 7 |LEDs de monitorização |Em determinadas circunstâncias, LEDs de monitorização remissivo da aplicação não havia indicação Estado correto. O LED azul foi desativada. DATA 0 e LEDs de 1 de dados foram piscar, mesmo quando estas interfaces não foram configuradas. Foi corrigido o problema e LEDs de monitorização agora indicam o estado correto. |Atualização 1 |Sim |Não |
| 8 |LEDs de monitorização |Em certos casos, depois de aplicar a atualização 1, a luz azul no controlador ativo desativada, deste modo, dificultando identificar o controlador ativo. Este problema foi corrigido nesta versão de patch. |Atualização 1.2 |Sim |Não |
| 9 |Interfaces de rede |Nas versões anteriores, um dispositivo StorSimple configurado com um gateway não encaminháveis internos pode ficar offline. Nesta versão, a métrica de encaminhamento para Data 0 tiver sido feitas as mais baixas; Por conseguinte, mesmo que outras interfaces de rede são a capacidade de cloud, todo o tráfego de nuvem do dispositivo será encaminhado através de dados 0. |Atualização 1 |Sim |Sim |
| 10 |Cópias de segurança |Foi corrigido um erro no Update 1, o que causou a falhar após 24 dias cópias de segurança na versão de patch 1.1 de atualização. |Atualização 1.1 |Sim |Sim |
| 11 |Cópias de segurança |Um bug em versões anteriores resultou num fraco desempenho para instantâneos de cloud com taxas de baixa alteração. Esse bug foi corrigido nesta versão de patch. |Atualização 1.2 |Sim |Sim |
| 12 |Atualizações |Foi corrigido um erro no Update 1, que comunicou uma atualização falhada e causado os controladores entrar no modo de recuperação, nesta versão de patch. |Atualização 1.2 |Sim |Sim |

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na atualização 1.2
A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Comentários/solução | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
| --- | --- | --- | --- | --- | --- |
| 1 |Quórum de disco |Em situações raras, se a maioria dos discos no bastidor EBOD de um dispositivo 8600 está ligado à Internet resultante no quórum sem disco, em seguida, o agrupamento de armazenamento será offline. Ele irá permanecer offline, mesmo que os discos são a ligação restabelecidos. |Terá de reiniciar o dispositivo. Se o problema persistir, contacte a Microsoft Support para passos seguintes. |Sim |Não |
| 2 |ID de controlador incorreto |Quando é efetuada uma substituição de controlador, o controlador 0 pode aparecer como controlador 1. Durante a substituição de controlador, quando a imagem é carregada a partir do nó de mesmo nível, o ID de controlador pode aparecer inicialmente como ID de. o controlador de ponto a ponto Em situações raras, esse comportamento também pode ser visto após um reinício do sistema. |Não é necessária nenhuma ação do utilizador. Esta situação será resolvido automaticamente depois de concluída a substituição de controlador. |Sim |Não |
| 3 |Contas de armazenamento |Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isso levará a uma situação em que não não possível obter os dados de utilizador. |Sim |Sim | |
| 4 |Ativação pós-falha do dispositivo |Não é suportada a várias ativações pós-falha de um contentor de volume do mesmo dispositivo de origem para os dispositivos de destino diferente. Ativação pós-falha do dispositivo de um dispositivo inativo único para vários dispositivos fará com que os contentores de volumes no primeiro efetuar a ativação pós-falha do dispositivo perder a propriedade dos dados. Após a ativação pós-falha, estes contentores de volume irão aparecer ou ter um comportamento diferente quando o utilizador vê-los no portal clássico do Azure. | |Sim |Não |
| 5 |Instalação |Durante o adaptador do StorSimple para instalação do SharePoint, terá de fornecer um IP do dispositivo para que a instalação concluir com êxito. | |Sim |Não |
| 6 |Proxy da Web |Se a sua configuração de proxy da web tiver HTTPS como o protocolo especificado, em seguida, a comunicação de serviço a dispositivo será afetada e o dispositivo irá ficar offline. Pacotes de suporte também serão gerados no processo de consumir recursos significativos no seu dispositivo. |Certifique-se de que o URL de proxy de web tem HTTP como o protocolo especificado. Para obter mais informações, veja [Configure web proxy for your device (Configurar o proxy Web para o seu dispositivo)](storsimple-configure-web-proxy.md). |Sim |Não |
| 7 |Proxy da Web |Se configura e ativar o proxy da web num dispositivo registado, terá de reiniciar o controlador ativo no seu dispositivo. | |Sim |Não |
| 8 |Latência de nuvem de alta e a carga de trabalho de e/s elevada |Quando o dispositivo StorSimple encontra uma combinação de latências de cloud muito elevada (ordem de segundos) e a carga de trabalho de e/s elevada, os volumes do dispositivo entram num Estado degradado e o e/s pode falhar com um erro de "dispositivo não está pronto". |Terá de reiniciar os controladores de dispositivo ou efetuar uma ativação pós-falha do dispositivo para recuperar a partir desta situação manualmente. |Sim |Não |
| 9 |Azure PowerShell |Quando utiliza o cmdlet do StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - primeiro 1 - Wait** para selecionar o primeiro objeto para que possa criar um novo **VolumeContainer** objeto, o cmdlet devolve todos os objetos. |Moldar o cmdlet parênteses da seguinte forma: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Sim |Sim |
| 10 |Migração |Quando vários contentores de volume são passados para a migração, a ETA para cópia de segurança mais recente é preciso apenas para o primeiro contentor de volume. Além disso, a migração paralela será iniciado depois das primeiros 4 cópias de segurança no contentor de volume do primeiro são migradas. |Recomendamos que migre um contentor de volumes cada vez. |Sim |Não |
| 11 |Migração |Após o restauro, os volumes de mensagens em fila não são adicionados para a política de cópia de segurança ou o grupo de disco virtual. |Terá de adicionar estes volumes para uma política de cópia de segurança para criar cópias de segurança. |Sim |Sim |
| 12 |Migração |Após a migração estar concluída, o dispositivo da 5000/7000 série não têm de aceder os contentores de dados migrados. |Recomendamos que elimine os contentores de dados migrados após a migração estar concluída e defendemos. |Sim |Não |
| 13 |Clone e DR |Um dispositivo StorSimple em execução Update 1 não é possível clonar ou efetuar a recuperação após desastre para um dispositivo a executar o software de pré-atualização 1. |Terá de atualizar o dispositivo de destino para atualização 1 para permitir que essas operações |Sim |Sim |
| 14 |Migração |Cópia de segurança de configuração para a migração poderá falhar num dispositivo da série 5000-7000 quando existem grupos de volumes com nenhuma volumes associados. |Elimine todos os grupos de volume vazio com nenhuma volumes associados e, em seguida, repita a cópia de segurança de configuração. |Sim |Não |

## <a name="physical-device-updates-in-update-12"></a>Atualizações do dispositivo físico no atualização 1.2
Se atualizar o patch 1.2 é aplicada a um dispositivo físico (executando versões anteriores ao Update 1), a versão do software será alterado para 6.3.9600.17584.

## <a name="controller-and-firmware-updates-in-update-12"></a>Controlador e atualizações de firmware em atualização 1.2
Esta versão atualiza o driver e o firmware do disco no seu dispositivo.

* Para obter mais informações sobre a atualização do controlador SAS, consulte [Update 1 para controladores de SAS de LSI na aplicação do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3043005). 
* Para obter mais informações sobre a atualização de firmware do disco, consulte [firmware do disco Update 1 para a aplicação do Microsoft Azure StorSimple](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>Atualizações de dispositivo virtual no atualização 1.2
Esta atualização não pode ser aplicada ao dispositivo virtual. Novos dispositivos virtual tem de ser criado. 

## <a name="next-steps"></a>Passos Seguintes
* [Instalar atualização 1.2 no seu dispositivo](storsimple-install-update-1.md).

