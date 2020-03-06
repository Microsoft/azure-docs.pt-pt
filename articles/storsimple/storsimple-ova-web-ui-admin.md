---
title: Administração de UI web StorSimple Virtual Array [ StorSimple Virtual Array ] Microsoft Docs
description: Descreve como executar tarefas básicas de administração de dispositivos através da Web UI storSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365722"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Use a UI Web para administrar o seu StorSimple Virtual Array
![fluxo de processo de configuração](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Descrição geral
Os tutoriais deste artigo aplicam-se ao Microsoft Azure StorSimple Virtual Array (também conhecido como o dispositivo virtual StorSimple on-premises) que executa o lançamento da disponibilidade geral (GA) de março de 2016. Este artigo descreve alguns dos complexos fluxos de trabalho e tarefas de gestão que podem ser realizadas no StorSimple Virtual Array. Pode gerir o StorSimple Virtual Array utilizando o serviço StorSimple Manager UI (referido como portal UI) e a UI web local para o dispositivo. Este artigo centra-se nas tarefas que pode executar através da UI web.

Este artigo inclui os seguintes tutoriais:

* Obtenha a chave de encriptação de dados de serviço
* Erros de configuração da Web UI de resolução de problemas
* Gerar um pacote de registo
* Encerrar ou reiniciar o dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obtenha a chave de encriptação de dados de serviço
Uma chave de encriptação de dados de serviço é gerada quando regista o seu primeiro dispositivo com o serviço StorSimple Manager. Esta chave é então necessária com a chave de registo de serviço para registar dispositivos adicionais com o serviço StorSimple Manager.

Se tiver perdido a chave de encriptação de dados do seu serviço e precisar de recuperá-la, execute os seguintes passos na UI web local do dispositivo registado no seu serviço.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obter a chave de encriptação de dados de serviço
1. Ligue-se à UI web local. Vá para **configurar** **definições** de nuvem > .
2. Na parte inferior da página, clique em Obter a chave de **encriptação**de dados do serviço . Uma chave aparecerá. Copie e guarde esta chave.
   
    ![obter chave de encriptação de dados de serviço 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Erros de configuração da Web UI de resolução de problemas
Em alguns casos, quando configurar o dispositivo através da UI web local, pode ter erros. Para diagnosticar e resolver tais erros, pode fazer os testes de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Para realizar os testes de diagnóstico
1. Na Web UI local, vá a testes de resolução de **problemas** > **diagnóstico.**
   
    ![executar diagnósticos 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Na parte inferior da página, clique em **Executar Testes de Diagnóstico**. Isto irá iniciar testes para diagnosticar quaisquer problemas possíveis com a sua rede, dispositivo, proxy web, tempo ou configurações de nuvem. Será notificado de que o dispositivo está a fazer testes.
3. Após a conclusão dos testes, os resultados serão apresentados. O exemplo que se segue mostra os resultados dos testes de diagnóstico. Note que as definições de procuração web não foram configuradas neste dispositivo e, portanto, o teste de procuração web não foi executado. Todos os outros testes para definições de rede, servidor DNS e definições de tempo foram bem sucedidos.
   
    ![executar diagnósticos 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Gerar um pacote de registo
Um pacote de registo é composto por todos os registos relevantes que podem ajudar o Microsoft Support a resolver problemas com qualquer problema do dispositivo. Nesta versão, um pacote de log pode ser gerado através da UI web local.

#### <a name="to-generate-the-log-package"></a>Para gerar o pacote de registo
1. Na web local UI, vá a registos de **resolução** de **problemas** > System .
   
    ![gerar pacote de log 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Na parte inferior da página, clique em **Criar pacote de registo**. Será criado um pacote de registos do sistema. Isto vai levar alguns minutos.
   
    ![gerar pacote de log 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Será notificado após a criação do pacote com sucesso, e a página será atualizada para indicar a hora e a data em que o pacote foi criado.
   
    ![gerar pacote de log 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Clique no **pacote de registo de descarregamento**. Um pacote com fecho será descarregado no seu sistema.
   
    ![gerar pacote de log 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Pode desapertar o pacote de registo descarregado e ver os ficheiros de registo do sistema.

## <a name="shut-down-and-restart-your-device"></a>Desligue e reinicie o seu dispositivo
Pode desligar ou reiniciar o seu dispositivo virtual utilizando a UI web local. Recomendamos que antes de reiniciar, desative os volumes ou partilhas offline no hospedeiro e, em seguida, no dispositivo. Isto minimizará qualquer possibilidade de corrupção de dados. 

#### <a name="to-shut-down-your-virtual-device"></a>Para desligar o seu dispositivo virtual
1. Na web local UI, vá para as **definições**de **Manutenção** > Energia .
2. Na parte inferior da página, clique em **Shutdown**.
   
    ![paragem do dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Aparecerá um aviso indicando que uma paragem do dispositivo interromperá qualquer IO que estivesse em curso, resultando numa paragem. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![aviso de paragem do dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Será notificado de que a paralisação foi iniciada.
   
    ![paragem do dispositivo começou](./media/storsimple-ova-web-ui-admin/image38.png)
   
    O dispositivo vai agora desligar-se. Se quiser ligar o seu dispositivo, terá de o fazer através do Hyper-V Manager.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar o seu dispositivo virtual
1. Na web local UI, vá para as **definições**de **Manutenção** > Energia .
2. Na parte inferior da página, clique em **Reiniciar**.
   
    ![reiniciar dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)
3. Um aviso será avisador afirmando que reiniciar o dispositivo interromperá todos os IOs que estavam em curso, resultando numa paragem. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![reiniciar aviso](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Será notificado de que o recomeço foi iniciado.
   
    ![reiniciar iniciado](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Enquanto o recomeço estiver em curso, perderá a ligação com a UI. Pode monitorizar o reinício refrescando periodicamente a UI. Em alternativa, pode monitorizar o estado de reinício do dispositivo através do Gestor De Hiper-V.

## <a name="next-steps"></a>Passos seguintes
Aprenda a [utilizar o serviço StorSimple Manager para gerir o seu dispositivo](storsimple-virtual-array-manager-service-administration.md).

