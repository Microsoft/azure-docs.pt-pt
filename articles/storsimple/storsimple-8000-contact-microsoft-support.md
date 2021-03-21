---
title: Crie bilhete de suporte ou caso para série StorSimple 8000
description: Saiba como registar o pedido de suporte e inicie uma sessão de suporte no seu dispositivo da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: c288f59b1cb9a85ed4bc978d1c3fba18ba30b572
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017207"
---
# <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

O Gestor de Dispositivos StorSimple fornece a capacidade de **registar um novo pedido** de suporte dentro da lâmina de resumo do serviço. Se encontrar algum problema com a sua solução StorSimple, pode criar um pedido de serviço de suporte técnico. Numa sessão online com o seu engenheiro de suporte, poderá também ter de iniciar uma sessão de suporte no seu dispositivo StorSimple. Este artigo acompanha-o:

* Como criar um pedido de apoio.
* Como gerir um ciclo de vida de pedido de suporte a partir do portal.
* Como iniciar uma sessão de suporte na interface Windows PowerShell do seu dispositivo StorSimple.

Reveja as [StorSimple 8000 Series Support SLAs e informações](./storsimple-8000-support-options.md) antes de criar um pedido de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Dependendo do seu [plano de suporte,](https://azure.microsoft.com/support/plans/)pode criar bilhetes de suporte para um problema no seu dispositivo StorSimple diretamente a partir da lâmina de resumo do serviço StorSimple Device Manager. Execute os seguintes passos para criar um pedido de apoio:

#### <a name="to-create-a-support-request"></a>Para criar um pedido de apoio

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Nas definições da lâmina de resumo de serviço, vá à secção **SUPPORT + TROUBLESHOOTING** e, em seguida, clique em **Novo pedido de suporte**.
     
    ![Contacte o Apoio aos Ms através de um novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Na lâmina de **pedido de novo suporte,** selecione **Basics**. Na lâmina **Basics,** faça os seguintes passos:
   1. A partir da lista de drop-down tipo emissão de **emissão,** selecione **Technical**.
   2. A **subscrição** atual, o tipo **de serviço** e o **recurso** (serviço StorSimple Device Manager) são automaticamente escolhidos. 
   3. Selecione um plano de **suporte** da lista de drop-down se tiver vários planos associados à sua subscrição. Precisa de um plano de apoio pago para permitir o Apoio Técnico.
   4. Clique em **Seguinte**.

       ![Contacte o Ms Support através do novo portal 2](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Na lâmina de **pedido de novo suporte,** selecione **Problema do Passo 2**. Na lâmina **Problem,** faça os seguintes passos:
    
    1. Escolha a **Severidade.**
    2. Especifique se o problema está relacionado com o aparelho ou com o serviço StorSimple Device Manager.
    3. Escolha uma **categoria** para esta edição e forneça mais **detalhes** sobre o assunto.
    4. Forneça a data e hora de início para o problema.
    5. No **upload do Ficheiro,** clique no ícone da pasta para navegar no seu pacote de suporte.
    6. Verifique **Informações de diagnóstico de partilhar**.
    7. Clique em **Seguinte**.

       ![Contacte o Ms Support através do novo portal 3](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Na lâmina de **pedido de novo suporte,** clique em **informações de contacto do Passo 3**. Na lâmina **de informação de contacto,** faça os seguintes passos:

   1. Nas **opções De Contacto,** forneça o seu método de contacto preferido (telefone ou e-mail) e o idioma. O tempo de resposta é automaticamente selecionado com base no seu plano de subscrição.
   2. Nas informações de Contacto, forneça o seu nome, e-mail, contacto opcional, país/região. Selecione as **alterações de contacto Save para futuras caixas de** verificação de pedidos de apoio.
   3. Clique em **Criar**.
   
       ![Contacte o Ms Support através do novo portal 4](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      O Microsoft Support utilizará estas informações para lhe contactar para obter mais informações, diagnóstico e resolução.
      Depois de ter apresentado o seu pedido, um engenheiro de Suporte entrará em contacto consigo o mais rapidamente possível para prosseguir com o seu pedido.

## <a name="manage-a-support-request"></a>Gerir um pedido de apoio

Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal.

#### <a name="to-manage-your-support-requests"></a>Para gerir os seus pedidos de apoio

1. Para chegar à página de ajuda e suporte, navegue para **procurar > suporte Help +**.

    ![Gerir pedidos de apoio](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Uma listagem tabular de todos os pedidos de suporte é apresentada na lâmina **de suporte Help +.**

    ![Gerir pedidos de apoio 2](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecione e clique num pedido de suporte. Pode ver o estado e os detalhes deste pedido. Clique **em + Nova mensagem** se quiser acompanhar este pedido.

    ![Gerir pedidos de apoio 3](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Inicie uma sessão de suporte no Windows PowerShell para StorSimple

Para resolver quaisquer problemas que possa experimentar com o dispositivo StorSimple, terá de se envolver com a equipa de Suporte do Microsoft. O Microsoft Support poderá ter de utilizar uma sessão de suporte para iniciar sessão no seu dispositivo.

Execute os seguintes passos para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de apoio

1. Aceda diretamente ao dispositivo utilizando a consola em série ou através de uma sessão de telnet a partir de um computador remoto. Para isso, siga os passos em [Use PuTTY para ligar à consola em série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Na sessão que abre, prima a tecla **Entrar** para obter um pedido de comando.
3. No menu de consola em série, selecione a opção 1, **faça login com acesso total**.
4. Ao solicitar, escreva a seguinte palavra-passe:
   
    `Password1`
5. No pedido, digite o seguinte comando:
   
    `Enable-HcsSupportAccess`
6. Será-lhe apresentada uma cadeia encriptada. Copie esta cadeia num editor de texto como o Notepad.
7. Guarde esta cadeia e envie-a numa mensagem de correio eletrónico para o Microsoft Support.

> [!IMPORTANT]
> Pode desativar o acesso ao suporte executando `Disable-HcsSupportAccess` . O dispositivo StorSimple também tentará desativar o acesso de suporte 8 horas após o início da sessão. É uma boa prática alterar as credenciais do dispositivo StorSimple depois de iniciar uma sessão de suporte.


## <a name="next-steps"></a>Passos seguintes

Saiba como [diagnosticar e resolver problemas relacionados com o seu dispositivo da série StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)