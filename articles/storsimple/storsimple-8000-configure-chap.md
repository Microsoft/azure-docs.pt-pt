---
title: Configure CHAP para dispositivo da série StorSimple 8000 [ Microsoft Docs
description: Descreve como configurar o Protocolo de Autenticação do Aperto de Mão (CHAP) num dispositivo StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267966"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Configure CHAP para o seu dispositivo StorSimple

Este tutorial explica como configurar chap para o seu dispositivo StorSimple. O procedimento detalhado neste artigo aplica-se aos dispositivos da série StorSimple 8000.

CHAP significa Protocolo de Autenticação de Aperto de Mão Desafio. Trata-se de um esquema de autenticação utilizado pelos servidores para validar a identidade dos clientes remotos. A verificação baseia-se numa palavra-passe partilhada ou em segredo. O CHAP pode ser de uma forma (unidirecional) ou mútua (bidirecional). Uma maneira do CHAP é quando o alvo autentica um iniciador. Em CHAP mútuo ou inverso, o alvo autentica o iniciador e, em seguida, o iniciador autentica o alvo. A autenticação do iniciador pode ser implementada sem autenticação do alvo. No entanto, a autenticação-alvo só pode ser implementada se a autenticação do iniciador também for implementada.

Como uma boa prática, recomendamos que use CHAP para aumentar a segurança iSCSI.

> [!NOTE]
> Tenha em mente que o IPSEC não é atualmente suportado em dispositivos StorSimple.

As definições chap no dispositivo StorSimple podem ser configuradas das seguintes formas:

* Autenticação unidirecional ou unidirecional
* Autenticação bidirecional ou mútua ou inversa

Em cada um destes casos, o portal para o dispositivo e o software de iniciador iSCSI do servidor precisam de ser configurados. Os passos detalhados para esta configuração são descritos no seguinte tutorial.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidirecional ou unidirecional

Na autenticação unidirecional, o alvo autentica o iniciador. Esta autenticação requer que configure as definições do iniciador CHAP no dispositivo StorSimple e no software iSCSI Initiator no hospedeiro. Os procedimentos detalhados para o seu dispositivo StorSimple e anfitrião do Windows são descritos em seguida.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar o seu dispositivo para autenticação de ida

1. No portal Azure, vá ao seu serviço StorSimple Device Manager. Clique em **Dispositivos** e selecione e clique num dispositivo para o quais pretende configurar o CHAP. Vá às **definições do dispositivo > Security**. Na lâmina de **definições de Segurança,** clique em **CHAP**.
   
    ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Na lâmina **CHAP,** e na secção **CHAP Initiator:**
   
   1. Forneça um nome de utilizador para o seu iniciador CHAP.
   2. Forneça uma senha para o seu iniciador CHAP.
      
      > [!IMPORTANT]
      > O nome de utilizador CHAP deve conter menos de 233 caracteres. A palavra-passe CHAP deve estar entre 12 e 16 caracteres. Um nome de utilizador ou palavra-passe mais longo resulta numa falha de autenticação no anfitrião do Windows.
   
   3. Confirme a palavra-passe.

       ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação de ida no servidor de anfitriões do Windows
1. No servidor de anfitriões do Windows, inicie o iSCSI Iniciante.
2. Na janela **iSCSI Initiator Properties,** execute os seguintes passos:
   
   1. Clique no separador **Discovery.**
      
       ![Propriedades do iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Clique no **Portal discover**.
3. Na caixa de diálogo **Discover Target Portal:**
   
   1. Especifique o endereço IP do seu dispositivo.
   2. Clique em **Avançadas**.
      
       ![Descubra o portal alvo](./media/storsimple-configure-chap/IC740945.png)
4. Na caixa de diálogo **Definições Avançadas:**
   
   1. Selecione o **log 'Ativar CHAP' na** caixa de verificação.
   2. No campo **Nome,** forneça o nome de utilizador especificado para o CHAP Initiator no portal Azure.
   3. No campo **secreto Target,** forneça a palavra-passe que especificou para o CHAP Initiator no portal Azure.
   4. Clique em **OK**.
      
       ![Configurações avançadas gerais](./media/storsimple-configure-chap/IC740946.png)
5. No separador **Targets** da janela **iSCSI Initiator Properties,** o estado do dispositivo deve aparecer como **Connected**. Se estiver a utilizar um dispositivo StorSimple 1200, cada volume é montado como um alvo iSCSI. Por conseguinte, os passos 3-4 terão de ser repetidos para cada volume.
   
    ![Volumes montados como alvos separados](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Se alterar o nome iSCSI, o novo nome é usado para novas sessões iSCSI. As novas definições não são utilizadas para as sessões existentes até iniciar sessão e iniciar sessão novamente.

Para obter mais informações sobre a configuração do CHAP no servidor de anfitriões do Windows, vá a [considerações adicionais](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Autenticação bidirecional ou mútua

Na autenticação bidirecional, o alvo autentica o iniciador e, em seguida, o iniciador autentica o alvo. Este procedimento requer que o utilizador configure as definições do iniciador CHAP, inverta as definições de CHAP no dispositivo e o software iSCSI Initiator no hospedeiro. Os seguintes procedimentos descrevem os passos para configurar a autenticação mútua no dispositivo e no anfitrião do Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar o seu dispositivo para autenticação mútua

1. No portal Azure, vá ao seu serviço StorSimple Device Manager. Clique em **Dispositivos** e selecione e clique num dispositivo para o quais pretende configurar o CHAP. Vá às **definições do dispositivo > Security**. Na lâmina de **definições de Segurança,** clique em **CHAP**.
   
    ![Alvo CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Percorra esta página e na secção **CHAP Target:**
   
   1. Forneça um nome de **utilizador inversa** chap para o seu dispositivo.
   2. Forneça uma **senha de chap a ver o** seu dispositivo.
   3. Confirme a palavra-passe.
3. Na secção **CHAP Iniciante:**
   
   1. Forneça um nome de **utilizador** para o seu dispositivo.
   2. Forneça uma **senha** para o seu dispositivo.
   3. Confirme a palavra-passe.

       ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação bidirecional no servidor anfitrião do Windows

1. No servidor de anfitriões do Windows, inicie o iSCSI Iniciante.
2. Na janela **iSCSI Initiator Properties,** clique no separador **Configuração.**
3. Clique **em CHAP**.
4. Na caixa de diálogo **iSCSI Mutual CHAP Secret:**
   
   1. Digite a **palavra-passe do CHAP invertido** que configuraste no portal Azure.
   2. Clique em **OK**.
      
       ![iSCSI iniciante segredo de CHAP mútuo](./media/storsimple-configure-chap/IC740949.png)
5. Clique no separador **Targets.**
6. Clique no botão **Ligar.** 
7. Na caixa de diálogo **Connect To Target,** clique **em Advanced**.
8. Na caixa de diálogo **Advanced Properties:**
   
   1. Selecione o **log 'Ativar CHAP' na** caixa de verificação.
   2. No campo **Nome,** forneça o nome de utilizador especificado para o CHAP Initiator no portal Azure.
   3. No campo **secreto Target,** forneça a palavra-passe que especificou para o CHAP Initiator no portal Azure.
   4. Selecione a caixa de verificação de **autenticação mútua Executar.**
      
       ![Definições avançadas de autenticação mútua](./media/storsimple-configure-chap/IC740950.png)
   5. Clique **EM OK** para completar a configuração CHAP

Para obter mais informações sobre a configuração do CHAP no servidor de anfitriões do Windows, vá a [considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais

A função **Quick Connect** não suporta ligações que tenham CHAP ativada. Quando o CHAP estiver ativado, certifique-se de que utiliza o botão **Connect** disponível no separador **Targets** para se ligar a um alvo.

![Ligar ao alvo](./media/storsimple-configure-chap/IC740947.png)

Na caixa de diálogo **Connect to Target** que é apresentada, selecione a **ligação Adicionar esta ligação à lista de alvos favoritos.** Esta seleção garante que sempre que o computador reinicia, é feita uma tentativa de restaurar a ligação aos alvos favoritos do iSCSI.

## <a name="errors-during-configuration"></a>Erros durante a configuração

Se a configuração chap estiver incorreta, é provável que veja uma mensagem de erro de **falha de autenticação.**

## <a name="verification-of-chap-configuration"></a>Verificação da configuração CHAP

Pode verificar se o CHAP está a ser utilizado preenchendo os seguintes passos.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar a sua configuração CHAP
1. Clique em **Alvos Favoritos**.
2. Selecione o alvo para o qual ativou a autenticação.
3. Clique em **Detalhes**.
   
    ![iSCSI propriedades de iniciador alvos favoritos](./media/storsimple-configure-chap/IC740951.png)
4. Na caixa de diálogo De detalhes de **destino favorito,** note a entrada no campo **de Autenticação.** Se a configuração foi bem sucedida, deve dizer **CHAP**.
   
    ![Detalhes favoritos do alvo](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [segurança StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

