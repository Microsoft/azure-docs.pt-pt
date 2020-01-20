---
title: Criar tíquete de suporte ou caso para a série StorSimple 8000
description: Saiba como registrar em log a solicitação de suporte e iniciar uma sessão de suporte em seu dispositivo StorSimple da série 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277180"
---
# <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

O Device Manager do StorSimple fornece a capacidade de **registrar uma nova solicitação de suporte** dentro da folha de resumo do serviço. Se você encontrar problemas com sua solução StorSimple, poderá criar uma solicitação de serviço para suporte técnico. Em uma sessão online com seu engenheiro de suporte, talvez você também precise iniciar uma sessão de suporte em seu dispositivo StorSimple. Este artigo orienta você pelo seguinte:

* Como criar uma solicitação de suporte.
* Como gerenciar um ciclo de vida de solicitação de suporte de dentro do Portal.
* Como iniciar uma sessão de suporte na interface do Windows PowerShell de seu dispositivo StorSimple.

Examine os [SLAs e as informações de suporte da série 8000 do StorSimple](https://msdn.microsoft.com/library/mt433077.aspx) antes de criar uma solicitação de suporte.

## <a name="create-a-support-request"></a>Criar um pedido de suporte

Dependendo do plano de [suporte](https://azure.microsoft.com/support/plans/), você pode criar tíquetes de suporte para um problema em seu dispositivo storsimple diretamente da folha de resumo do serviço storsimple Device Manager. Execute as seguintes etapas para criar uma solicitação de suporte:

#### <a name="to-create-a-support-request"></a>Para criar uma solicitação de suporte

1. Aceda ao seu serviço Gestor de Dispositivos do StorSimple. Nas configurações da folha de resumo do serviço, vá para a seção **suporte + solução de problemas** e clique em **nova solicitação de suporte**.
     
    ![Entre em contato com o suporte MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. Na folha **nova solicitação de suporte** , selecione **básico**. Na folha **noções básicas** , execute as seguintes etapas:
   1. Na lista suspensa **tipo de problema** , selecione **técnico**.
   2. A **assinatura**atual, o tipo de **serviço** e o **recurso** (serviço StorSimple Device Manager) são escolhidos automaticamente. 
   3. Selecione um **plano de suporte** na lista suspensa se você tiver vários planos associados à sua assinatura. Você precisa de um plano de suporte pago para habilitar o suporte técnico.
   4. Clique em **Seguinte**.

       ![Entre em contato com o suporte MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. Na folha **nova solicitação de suporte** , selecione **etapa 2 problema**. Na folha **problema** , execute as seguintes etapas:
    
    1. Escolha a **severidade**.
    2. Especifique se o problema está relacionado ao dispositivo ou ao serviço StorSimple Device Manager.
    3. Escolha uma **categoria** para esse problema e forneça mais **detalhes** sobre o problema.
    4. Forneça a data e a hora de início para o problema.
    5. No **upload do arquivo**, clique no ícone de pasta para navegar até seu pacote de suporte.
    6. Marque **compartilhar informações de diagnóstico**.
    7. Clique em **Seguinte**.

       ![Entre em contato com o suporte MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. Na folha **nova solicitação de suporte** , clique em **etapa 3 informações de contato**. Na folha **informações de contato** , execute as seguintes etapas:

   1. Nas **Opções de contato**, forneça seu método de contato preferencial (telefone ou email) e o idioma. O tempo de resposta é selecionado automaticamente com base em seu plano de assinatura.
   2. Nas informações de contato, forneça seu nome, email, contato opcional, país/região. Marque a caixa de seleção **salvar alterações de contato para futuras solicitações de suporte** .
   3. Clique em **Criar**.
   
       ![Entre em contato com o suporte MS por meio do novo portal](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Suporte da Microsoft usará essas informações para entrar em contato com você para obter mais informações, diagnósticos e resolução.
      Depois de ter enviado sua solicitação, um engenheiro de suporte entrará em contato com você assim que possível para prosseguir com sua solicitação.

## <a name="manage-a-support-request"></a>Gerenciar uma solicitação de suporte

Depois de criar um pedido de suporte, pode gerir o ciclo de vida do mesmo no portal.

#### <a name="to-manage-your-support-requests"></a>Para gerenciar suas solicitações de suporte

1. Para acessar a página ajuda e suporte, navegue até **procurar > ajuda + suporte**.

    ![Gerenciar solicitações de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Uma listagem tabular de todas as solicitações de suporte é exibida na folha **ajuda + suporte** .

    ![Gerenciar solicitações de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Selecione e clique em uma solicitação de suporte. Você pode exibir o status e os detalhes dessa solicitação. Clique em **+ nova mensagem** se desejar acompanhar essa solicitação.

    ![Gerenciar solicitações de suporte](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar uma sessão de suporte no Windows PowerShell para StorSimple

Para solucionar quaisquer problemas que possam ocorrer com o dispositivo StorSimple, você precisará entrar com a equipe de Suporte da Microsoft. Suporte da Microsoft pode precisar usar uma sessão de suporte para fazer logon em seu dispositivo.

Execute as seguintes etapas para iniciar uma sessão de suporte:

#### <a name="to-start-a-support-session"></a>Para iniciar uma sessão de suporte

1. Acesse o dispositivo diretamente usando o console serial ou por meio de uma sessão Telnet de um computador remoto. Para fazer isso, siga as etapas em [usar a saída para se conectar ao console serial do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Na sessão que é aberta, pressione a tecla **Enter** para obter um prompt de comando.
3. No menu do console serial, selecione a opção 1, **faça logon com acesso completo**.
4. No prompt, digite a seguinte senha:
   
    `Password1`
5. Na linha de comandos, escreva o seguinte comando:
   
    `Enable-HcsSupportAccess`
6. Uma cadeia de caracteres criptografada será apresentada a você. Copie essa cadeia de caracteres em um editor de texto como o bloco de notas.
7. Salve essa cadeia de caracteres e envie-a em uma mensagem de email para Suporte da Microsoft.

> [!IMPORTANT]
> Você pode desabilitar o acesso de suporte executando `Disable-HcsSupportAccess`. O dispositivo StorSimple também tentará desabilitar o acesso de suporte 8 horas após o início da sessão. É uma prática recomendada alterar as credenciais do dispositivo StorSimple depois de iniciar uma sessão de suporte.


## <a name="next-steps"></a>Passos seguintes

Saiba como [diagnosticar e resolver problemas relacionados ao seu dispositivo StorSimple da série 8000](storsimple-8000-troubleshoot-deployment.md)
