---
title: Configurar o CHAP para o dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Descreve como configurar o protocolo de autenticação de Handshake de desafio (CHAP) num dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: efc116c278bfe72419800603a3b365f461fe0a28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095165"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Configurar o CHAP para o dispositivo StorSimple

Este tutorial explica como configurar o CHAP para o dispositivo StorSimple. O procedimento detalhado neste artigo aplica-se aos dispositivos da série StorSimple 8000.

CHAP significa Challenge Handshake Authentication Protocol. É um esquema de autenticação utilizado pelos servidores para validar a identidade de clientes remotos. A verificação baseia-se uma palavra-passe partilhada ou um segredo. CHAP pode ser uma forma (unidirecional) ou mútua (bidirecional). Uma forma CHAP é quando o destino autentica um iniciador. CHAP inverso ou mútua, o destino autentica o iniciador e, em seguida, o iniciador autentica o destino. Autenticação de iniciador pode ser implementada sem a autenticação de destino. No entanto, a autenticação de destino pode ser implementada apenas se a autenticação de iniciador também é implementada.

Como melhor prática, recomendamos que utilize CHAP para melhorar a segurança de iSCSI.

> [!NOTE]
> Tenha em atenção que IPSEC não é atualmente suportado em dispositivos do StorSimple.

As definições de CHAP no dispositivo StorSimple podem ser configuradas da seguinte maneira:

* Autenticação unidirecional ou unidirecional
* Bidirecional ou autenticação mútua ou inversa

Em cada um desses casos, o portal para o dispositivo e o software de iniciador de iSCSI do servidor tem de ser configurado. Os passos detalhados para esta configuração estão descritos no tutorial seguinte.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidirecional ou unidirecional

Na autenticação unidirecional, o destino autentica o iniciador. Esta autenticação requer que configure as definições de iniciador CHAP no dispositivo StorSimple e o iSCSI software iniciador do anfitrião. Os procedimentos detalhados para o seu dispositivo StorSimple e o anfitrião do Windows são descritos a seguir.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar o seu dispositivo para a autenticação unidirecional

1. No portal do Azure, aceda ao seu serviço StorSimple Device Manager. Clique em **dispositivos** e selecione e clique num dispositivo que pretende configurar o CHAP para. Aceda a **definições do dispositivo > segurança**. Na **definições de segurança** painel, clique em **CHAP**.
   
    ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Na **CHAP** painel e, no **iniciador CHAP** secção:
   
   1. Forneça um nome de utilizador para o iniciador CHAP.
   2. Forneça uma palavra-passe para o iniciador CHAP.
      
      > [!IMPORTANT]
      > O nome de utilizador CHAP tem de conter menos de 233 carateres. A palavra-passe CHAP tem de ter entre 12 a 16 carateres. Um nome de utilizador ou palavra-passe mais tempo resulta numa falha de autenticação do anfitrião do Windows.
   
   3. Confirme a palavra-passe.

       ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação unidirecional no servidor de anfitrião do Windows
1. No servidor de anfitrião do Windows, inicie o iniciador iSCSI.
2. Na **propriedades do iniciador iSCSI** janela, execute os seguintes passos:
   
   1. Clique nas **deteção** separador.
      
       ![Propriedades do iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Clique em **detetar Portal**.
3. Na **detetar Portal de destino** caixa de diálogo:
   
   1. Especifique o endereço IP do seu dispositivo.
   2. Clique em **Advanced**.
      
       ![Detetar portal de destino](./media/storsimple-configure-chap/IC740945.png)
4. Na **definições avançadas** caixa de diálogo:
   
   1. Selecione o **ativar CHAP logon** caixa de verificação.
   2. Na **nome** campo, forneça o nome de utilizador que especificou para o iniciador de CHAP no portal do Azure.
   3. Na **segredo de destino** campo, forneça a palavra-passe que especificou para o iniciador de CHAP no portal do Azure.
   4. Clique em **OK**.
      
       ![Definições avançadas gerais](./media/storsimple-configure-chap/IC740946.png)
5. Sobre o **destinos** separador da **propriedades do iniciador iSCSI** janela, o estado do dispositivo deve aparecer como **ligado**. Se estiver a utilizar um dispositivo StorSimple 1200, em seguida, cada volume está montado como um destino iSCSI. Por este motivo, os passos 3 a 4 terá de ser repetido para cada volume.
   
    ![Volumes montados como destinos separados](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Se alterar o nome de iSCSI, o novo nome é utilizado para novas sessões iSCSI. Novas definições não são utilizadas para existente sessões até que terminar a sessão e início de sessão novamente.

Para obter mais informações sobre como configurar o CHAP no servidor de anfitrião do Windows, aceda a [considerações adicionais](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Autenticação bidirecional ou mútua

Na autenticação de bidirecional, o destino autentica o iniciador e, em seguida, o iniciador autentica o destino. Este procedimento requer que o utilizador configurar as definições de iniciador CHAP, definições do CHAP inverso no dispositivo e iSCSI software iniciador do anfitrião. Os procedimentos seguintes descrevem os passos para configurar a autenticação mútua no dispositivo e do anfitrião do Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar o seu dispositivo para autenticação mútua

1. No portal do Azure, aceda ao seu serviço StorSimple Device Manager. Clique em **dispositivos** e selecione e clique num dispositivo que pretende configurar o CHAP para. Aceda a **definições do dispositivo > segurança**. Na **definições de segurança** painel, clique em **CHAP**.
   
    ![Destino do CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Desloque para baixo nesta página e, na **destino do CHAP** secção:
   
   1. Fornecer um **nome de utilizador de CHAP inverso** para o seu dispositivo.
   2. Fornecer um **palavra-passe do CHAP inverso** para o seu dispositivo.
   3. Confirme a palavra-passe.
3. Na **iniciador CHAP** secção:
   
   1. Fornecer um **nome de utilizador** para o seu dispositivo.
   2. Fornecer um **palavra-passe** para o seu dispositivo.
   3. Confirme a palavra-passe.

       ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação de bidirecional no servidor de anfitrião do Windows

1. No servidor de anfitrião do Windows, inicie o iniciador iSCSI.
2. Na **propriedades do iniciador iSCSI** janela, clique nas **configuração** separador.
3. Clique em **CHAP**.
4. Na **segredo de CHAP mútua de iniciador de iSCSI** caixa de diálogo:
   
   1. Tipo de **inverter a palavra-passe de CHAP** que configurou no portal do Azure.
   2. Clique em **OK**.
      
       ![segredo CHAP mútuo iniciador iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Clique nas **destinos** separador.
6. Clique nas **Connect** botão. 
7. Na **ligar para o destino** caixa de diálogo, clique em **avançadas**.
8. Na **propriedades avançadas** caixa de diálogo:
   
   1. Selecione o **ativar CHAP logon** caixa de verificação.
   2. Na **nome** campo, forneça o nome de utilizador que especificou para o iniciador de CHAP no portal do Azure.
   3. Na **segredo de destino** campo, forneça a palavra-passe que especificou para o iniciador de CHAP no portal do Azure.
   4. Selecione o **realize a autenticação mútua** caixa de verificação.
      
       ![Autenticação mútua definições avançadas](./media/storsimple-configure-chap/IC740950.png)
   5. Clique em **OK** para concluir a configuração do CHAP

Para obter mais informações sobre como configurar o CHAP no servidor de anfitrião do Windows, aceda a [considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais

O **ligar rápida** funcionalidade não suporta ligações que têm CHAP ativada. Quando CHAP estiver ativado, certifique-se de que utiliza a **Connect** botão está disponível na **destinos** separador para ligar a um destino.

![Estabelecer ligação ao destino](./media/storsimple-configure-chap/IC740947.png)

Na **ligue-se ao destino** caixa de diálogo que é apresentada, selecione o **adicione esta ligação para a lista de destinos favorito** caixa de verificação. Esta seleção garante que sempre que o computador é reiniciado, é feita uma tentativa para restabelecer a ligação aos destinos iSCSI favorito.

## <a name="errors-during-configuration"></a>Erros durante a configuração

Se a sua configuração CHAP está incorreta, então é provável que ver uma **falha de autenticação** mensagem de erro.

## <a name="verification-of-chap-configuration"></a>Verificação da configuração do CHAP

Pode verificar que o CHAP está sendo usado, concluindo os passos seguintes.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar a configuração do CHAP
1. Clique em **destinos favoritos**.
2. Selecione o destino para o qual ativou a autenticação.
3. Clique em **detalhes**.
   
    ![destinos favorito de propriedades do iniciador de iSCSI](./media/storsimple-configure-chap/IC740951.png)
4. Na **detalhes do destino favorito** caixa de diálogo caixa, tenha em atenção a entrada na **autenticação** campo. Se a configuração foi concluída com êxito, deverá indicar **CHAP**.
   
    ![Detalhes do destino favorito](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [StorSimple segurança](storsimple-8000-security.md).
* Saiba mais sobre [utilizar o serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

