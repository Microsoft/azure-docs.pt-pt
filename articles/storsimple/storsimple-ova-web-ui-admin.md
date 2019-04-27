---
title: Web do StorSimple Virtual Array administração de interface do Usuário | Documentos da Microsoft
description: Descreve como executar tarefas de administração de dispositivos básica através da interface do Usuário de web do StorSimple Virtual Array.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630463"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Utilizar a IU da Web para administrar a sua matriz Virtual StorSimple
![fluxo de processo de configuração](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Descrição geral
Os tutoriais neste artigo aplicam-se para o Microsoft Azure StorSimple Virtual Array (também conhecido como o local dispositivo virtual StorSimple) a executar a versão de disponibilidade geral (GA) de Março de 2016. Este artigo descreve alguns dos fluxos de trabalho complexos e tarefas de gestão que podem ser executadas na matriz Virtual StorSimple. Pode gerir a matriz Virtual do StorSimple com o Gestor de StorSimple (referido como a IU do portal) de interface do Usuário e da web local da interface do Usuário para o dispositivo de serviço. Este artigo se concentra nas tarefas que pode realizar com a IU web.

Este artigo inclui os seguintes tutoriais:

* Obter a chave de encriptação de dados do serviço
* Resolver erros de configuração de interface do Usuário de web
* Gerar um pacote de registo
* Encerrar ou reiniciar o dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obter a chave de encriptação de dados do serviço
Uma chave de encriptação de dados do serviço é gerada quando registar o seu primeiro dispositivo com o serviço StorSimple Manager. Esta chave, em seguida, é necessário com a chave de registo do serviço para registar dispositivos adicionais com o serviço StorSimple Manager.

Se tiver no local incorreto sua chave de encriptação de dados do serviço e a necessidade de recuperá-la, execute os seguintes passos da web local da interface do Usuário do dispositivo registado com o seu serviço.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obter a chave de encriptação de dados do serviço
1. Ligar à IU da web local. Aceda a **Configuration** > **definições da Cloud**.
2. Na parte inferior da página, clique em **chave de encriptação de dados de serviço de Get**. Será apresentada uma chave. Copie e guarde esta chave.
   
    ![obter a chave de encriptação de dados do serviço 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Resolver erros de configuração de interface do Usuário de web
Em alguns casos quando configurar o dispositivo através da web local da interface do Usuário, que podem ocorrer erros. Para diagnosticar e resolver problemas relacionados com esses erros, pode executar os testes de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Para executar os testes de diagnóstico
1. Na IU da web local, aceda a **resolução de problemas** > **testes de diagnóstico**.
   
    ![executar diagnósticos 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Na parte inferior da página, clique em **executar testes de diagnóstico**. Isto irá iniciar os testes para diagnosticar possíveis problemas com a sua rede, o dispositivo, o proxy da web, hora ou definições de nuvem. Será notificado que o dispositivo está a executar testes.
3. Depois de concluíram os testes, os resultados serão exibidos. O exemplo seguinte mostra os resultados de testes de diagnóstico. Tenha em atenção que as definições de proxy da web não foram configuradas neste dispositivo e, portanto, o teste de proxy da web não foi executado. Todos os outros testes para as definições de rede, servidor DNS e as definições de hora foram efetuada com êxito.
   
    ![executar diagnósticos 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Gerar um pacote de registo
Um pacote de log é composta por todos os registos relevantes que podem ajudá-lo em Microsoft Support resolver quaisquer problemas de dispositivos. Nesta versão, um pacote de log pode ser gerado através da IU da web local.

#### <a name="to-generate-the-log-package"></a>Para gerar o pacote de registo
1. Na IU da web local, aceda a **resolução de problemas** > **registos do sistema**.
   
    ![gerar o pacote de registo 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Na parte inferior da página, clique em **criar pacote de registo**. Será possível criar um pacote dos registos do sistema. Esta ação irá demorar alguns minutos.
   
    ![gerar o pacote de registo 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    Será notificado depois do pacote é criado com êxito e a página será atualizada para indicar a hora e data quando o pacote foi criado.
   
    ![gerar o pacote de registo 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Clique em **pacote de transferência do registo**. Um pacote zipado será transferido no seu sistema.
   
    ![gerar o pacote de registo 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. Pode deszipe o pacote transferido do registo e ver os ficheiros de registo do sistema.

## <a name="shut-down-and-restart-your-device"></a>Encerrar e reiniciar o seu dispositivo
Pode encerrar ou reiniciar o dispositivo virtual com a IU web local. Nós recomendamos que antes de reiniciar, levar os volumes ou compartilhamentos offline no anfitrião e, em seguida, o dispositivo. Isto irá minimizar qualquer possibilidade de Corrupção de dados. 

#### <a name="to-shut-down-your-virtual-device"></a>Para encerrar o dispositivo virtual
1. Na IU da web local, aceda a **manutenção** > **definições de energia**.
2. Na parte inferior da página, clique em **encerramento**.
   
    ![encerramento de dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Será apresentado um aviso a indicar que um encerramento do dispositivo irá interromper qualquer e/s que estavam em curso, resultando num período de indisponibilidade. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Aviso de encerramento do dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Será notificado que foi iniciado o encerramento.
   
    ![encerramento de dispositivo iniciado](./media/storsimple-ova-web-ui-admin/image38.png)
   
    O dispositivo irá agora encerrar. Se deseja iniciar o seu dispositivo, terá de fazê-lo através do Gestor de Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar o dispositivo virtual
1. Na IU da web local, aceda a **manutenção** > **definições de energia**.
2. Na parte inferior da página, clique em **reiniciar**.
   
    ![reinício do dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)
3. Será apresentado um aviso a indicar que a reiniciar o dispositivo irá interromper qualquer IOs que estavam em curso, resultando num período de indisponibilidade. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![Reinicie o aviso](./media/storsimple-ova-web-ui-admin/image37.png)
   
    Será notificado que o reinício foi iniciado.
   
    ![reinício iniciado](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Enquanto o reinício está em curso, irá perder a ligação para a interface do Usuário. Pode monitorizar o reinício atualizando a interface do Usuário periodicamente. Em alternativa, pode monitorizar o estado de reinício do dispositivo através do Gestor de Hyper-V.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [utilizar o serviço StorSimple Manager para gerir o seu dispositivo](storsimple-virtual-array-manager-service-administration.md).

