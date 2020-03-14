---
title: Criar bilhete de suporte ou caso para a série StorSimple 8000
description: Saiba como registar o pedido de suporte e inicie uma sessão de suporte no seu dispositivo da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254992"
---
# <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

O StorSimple Device Manager fornece a capacidade de **registar um novo pedido** de suporte dentro da lâmina de resumo do serviço. Se encontrar algum problema com a sua solução StorSimple, pode criar um pedido de serviço de suporte técnico. Numa sessão online com o seu engenheiro de suporte, poderá também ter de iniciar uma sessão de suporte no seu dispositivo StorSimple. Este artigo acompanha-o através de:

* Como criar um pedido de apoio.
* Como gerir um suporte solicitar ciclo de vida dentro do portal.
* Como iniciar uma sessão de suporte na interface Windows PowerShell do seu dispositivo StorSimple.

Reveja o [StorSimple 8000 Series Support SLAs e informações](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar um pedido de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Dependendo do seu plano de [suporte,](https://azure.microsoft.com/support/plans/)pode criar bilhetes de suporte para um problema no seu dispositivo StorSimple diretamente a partir da lâmina de resumo do serviço StorSimple Device Manager. Execute os seguintes passos para criar um pedido de apoio:

#### <a name="to-create-a-support-request"></a>Para criar um pedido de apoio

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Nas definições de lâmina de resumo de serviço, vá para a secção **SUPORTE + RESOLUÇÃO DE PROBLEMAS** e, em seguida, clique em **Novo pedido**de suporte .
     
    ![Contacte o Suporte de MS através de um novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Na nova lâmina de pedido de **suporte,** selecione **Basics**. Na lâmina Basics, faça os **seguintes passos:**
   1. A partir da lista de drop-down do **tipo Emissão,** selecione **Técnico**.
   2. A **subscrição**atual, o tipo de **serviço** e o **recurso** (serviço StorSimple Device Manager) são automaticamente escolhidos. 
   3. Selecione um plano de **suporte** da lista de drop-down se tiver vários planos associados à sua subscrição. Precisa de um plano de apoio pago para permitir o Apoio Técnico.
   4. Clique em **Seguinte**.

       ![Contacte o Suporte de MS através de um novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Na nova lâmina de pedido de **suporte,** selecione **Problema passo 2**. Na lâmina **Problema,** faça os seguintes passos:
    
    1. Escolha a **Gravidade.**
    2. Especifique se o problema está relacionado com o aparelho ou com o serviço StorSimple Device Manager.
    3. Escolha uma **Categoria** para esta edição e forneça mais **detalhes** sobre o assunto.
    4. Forneça a data e a hora de início para o problema.
    5. No upload do **Ficheiro,** clique no ícone da pasta para navegar no seu pacote de suporte.
    6. Verifique **partilhar informações de diagnóstico**.
    7. Clique em **Seguinte**.

       ![Contacte o Suporte de MS através de um novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Na nova lâmina de pedido de **suporte,** clique em **informações de contacto do Passo 3**. Na lâmina de informação do **Contacto,** faça os seguintes passos:

   1. Nas **opções**de Contacto, forneça o seu método de contacto preferido (telefone ou e-mail) e o idioma. O tempo de resposta é automaticamente selecionado com base no seu plano de subscrição.
   2. Nas informações de Contacto, forneça o seu nome, e-mail, contacto opcional, país/região. Selecione as alterações de **contacto Save para futuros pedidos de suporte,** verifique a caixa.
   3. Clique em **Criar**.
   
       ![Contacte o Suporte de MS através de um novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      O Microsoft Support utilizará estas informações para lhe contactar para mais informações, diagnóstico e resolução.
      Depois de ter apresentado o seu pedido, um engenheiro de suporte entrará em contacto consigo o mais rapidamente possível para prosseguir com o seu pedido.

## <a name="manage-a-support-request"></a>Gerir um pedido de apoio

Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal.

#### <a name="to-manage-your-support-requests"></a>Para gerir os seus pedidos de apoio

1. Para chegar à página de ajuda e suporte, navegue para **Navegar > Ajuda + suporte**.

    ![Gerir pedidos de apoio](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Uma listagem tabular de todos os pedidos de suporte é exibida na lâmina **de suporte Ajuda +.**

    ![Gerir pedidos de apoio](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecione e clique num pedido de suporte. Pode ver o estado e os detalhes para este pedido. Clique **+ Nova mensagem** se quiser acompanhar este pedido.

    ![Gerir pedidos de apoio](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Inicie uma sessão de suporte no Windows PowerShell para storSimple

Para resolver problemas que possa experimentar com o dispositivo StorSimple, terá de se envolver com a equipa de Suporte do Microsoft. O Microsoft Support poderá ter de utilizar uma sessão de suporte para iniciar sessão no seu dispositivo.

Execute os seguintes passos para iniciar uma sessão de apoio:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de apoio

1. Aceda diretamente ao dispositivo utilizando a consola em série ou através de uma sessão de telnet a partir de um computador remoto. Para isso, siga os passos em [Use PuTTY para ligar à consola em série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Na sessão que abre, prima a tecla **Enter** para obter um pedido de comando.
3. No menu de consola em série, selecione a opção 1, **inicie sessão com acesso total**.
4. No momento, digite a seguinte palavra-passe:
   
    `Password1`
5. Na linha de comandos, escreva o seguinte comando:
   
    `Enable-HcsSupportAccess`
6. Será-lhe apresentada uma corda encriptada. Copie esta cadeia num editor de texto como o Notepad.
7. Guarde esta corda e envie-a numa mensagem de e-mail para o Microsoft Support.

> [!IMPORTANT]
> Pode desativar o acesso ao suporte executando `Disable-HcsSupportAccess`. O dispositivo StorSimple também tentará desativar o acesso ao suporte 8 horas após o início da sessão. É uma boa prática alterar as credenciais do seu dispositivo StorSimple depois de ter dado início a uma sessão de suporte.


## <a name="next-steps"></a>Passos seguintes

Saiba diagnosticar [e resolver problemas relacionados com o seu dispositivo da série StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)
