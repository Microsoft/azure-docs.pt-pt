---
title: Criar pedido de suporte ou caso da série StorSimple 8000 | Documentos da Microsoft
description: Saiba como iniciar o pedido de suporte e iniciar uma sessão de suporte no seu dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: fb8cfd4767f6bb9afe1b5731d3b4db1c68a73056
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107132"
---
# <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

O StorSimple Device Manager fornece a capacidade de **inicie um novo pedido de suporte** no painel de resumo do serviço. Se encontrar algum problema com a solução StorSimple, pode criar um pedido de serviço para obter suporte técnico. Numa sessão online com o engenheiro de suporte, também poderá iniciar uma sessão de suporte no dispositivo StorSimple. Este artigo orienta-o através de:

* Como criar um pedido de suporte.
* Como gerir uma pedido de vida de suporte no portal.
* Como iniciar uma sessão de suporte na interface do Windows PowerShell do dispositivo StorSimple.

Reveja os [SLAs de suporte do StorSimple 8000 Series e informações](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar um pedido de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de apoio

Dependendo da sua [plano de suporte](https://azure.microsoft.com/support/plans/), pode criar pedidos de suporte para um problema no dispositivo StorSimple diretamente a partir do painel de resumo do serviço StorSimple Device Manager. Execute os seguintes passos para criar um pedido de suporte:

#### <a name="to-create-a-support-request"></a>Para criar um pedido de suporte

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Nas definições do painel de resumo do serviço, ir para **suporte + resolução de problemas** secção e, em seguida, clique em **novo pedido de suporte**.
     
    ![MS de contacto de suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Na **novo pedido de suporte** painel, selecione **Noções básicas**. Na **Noções básicas** painel, siga os passos abaixo:
   1. Partir do **tipo de problema** na lista pendente, selecione **técnica**.
   2. O atual **subscrição**, **Service** tipo e o **recurso** (serviço de Gestor de dispositivos do StorSimple) são escolhidos automaticamente. 
   3. Selecione um **plano de suporte** na lista pendente, se tiver vários esquemas de associados à subscrição. Precisa de um plano de suporte pagas para permitir o suporte técnico.
   4. Clique em **Seguinte**.

       ![MS de contacto de suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Na **novo pedido de suporte** painel, selecione **passo 2 problema**. Na **problema** painel, siga os passos abaixo:
    
    1. Escolha o **gravidade**.
    2. Especifique se o problema está relacionado com a aplicação ou o serviço StorSimple Device Manager.
    3. Escolher uma **categoria** para este problema e fornecer mais **detalhes** sobre o problema.
    4. Forneça a data de início e hora para o problema.
    5. Na **carregamento de ficheiros**, clique no ícone de pasta para navegar para o pacote de suporte.
    6. Verifique **partilhar informações de diagnóstico**.
    7. Clique em **Seguinte**.

       ![MS de contacto de suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Na **novo pedido de suporte** painel, clique em **informações de contacto do passo 3**. Na **informações de contacto** painel, siga os passos abaixo:

   1. Na **opções de contacto**, forneça o seu método preferencial de contacto (telefone ou e-mail) e o idioma. O tempo de resposta é selecionado automaticamente com base no seu plano de subscrição.
   2. As informações de contacto, fornece seu nome, e-mail, contato opcional, país. Selecione o **guardar alterações de contactos para futuros pedidos de suporte** caixa de verificação.
   3. Clique em **Criar**.
   
       ![MS de contacto de suporte através do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Support da Microsoft irá utilizar estas informações para contactá-lo para obter mais informações, diagnóstico e resolução.
      Depois de ter submetido o seu pedido, um engenheiro de suporte irá contactá-lo assim que possível para continuar com o seu pedido.

## <a name="manage-a-support-request"></a>Gerir pedidos de suporte

Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal.

#### <a name="to-manage-your-support-requests"></a>Para gerir os pedidos de suporte

1. Para chegar à página de ajuda e suporte, navegue para **Procurar > ajuda + suporte**.

    ![Gerir pedidos de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Uma lista tabular de todos os pedidos de suporte é apresentada na **ajuda + suporte** painel.

    ![Gerir pedidos de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecione e clique num pedido de suporte. Pode ver o estado e os detalhes para este pedido. Clique em **+ nova mensagem** se quiser dar seguimento este pedido.

    ![Gerir pedidos de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para resolver quaisquer problemas que podem ocorrer com o dispositivo StorSimple, terá de interagir com a equipa de Support da Microsoft. Support da Microsoft poderá ter de utilizar uma sessão de suporte para iniciar sessão seu dispositivo.

Execute os seguintes passos para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte

1. Aceda ao dispositivo diretamente através da consola de série ou através de uma sessão telnet de um computador remoto. Para tal, siga os passos em [utilizar o PuTTY para ligar a consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Na sessão apresentada, prima a **Enter** chave para obter uma linha de comandos.
3. No menu da consola de série, selecione a opção 1, **iniciar sessão com acesso total**.
4. Na linha de comandos, escreva a palavra-passe seguinte:
   
    `Password1`
5. Na linha de comandos, escreva o seguinte comando:
   
    `Enable-HcsSupportAccess`
6. Uma cadeia encriptada será apresentada para. Copie essa cadeia de caracteres para um editor de texto, como o bloco de notas.
7. Guarde esta cadeia e enviá-la numa mensagem de e-mail ao Support da Microsoft.

> [!IMPORTANT]
> Pode desativar o acesso ao suporte ao executar `Disable-HcsSupportAccess`. Também irá tentar desativar o acesso de suporte de 8 horas após o início da sessão do dispositivo StorSimple. É melhor prática para alterar as credenciais de dispositivo do StorSimple após iniciar uma sessão de suporte.


## <a name="next-steps"></a>Passos Seguintes

Saiba como [diagnosticar e resolver problemas relacionados com o seu dispositivo da série StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)
