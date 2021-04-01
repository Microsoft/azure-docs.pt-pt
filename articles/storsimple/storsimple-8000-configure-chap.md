---
title: Configure CHAP para dispositivo da série StorSimple 8000 | Microsoft Docs
description: Descreve como configurar o Protocolo de Autenticação de Aperto de Mão de Desafio (CHAP) num dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766508"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Configure CHAP para o seu dispositivo StorSimple

Este tutorial explica como configurar o CHAP para o seu dispositivo StorSimple. O procedimento detalhado neste artigo aplica-se aos dispositivos da série StorSimple 8000.

CHAP significa Protocolo de Autenticação de Aperto de Mão de Desafio. Trata-se de um esquema de autenticação utilizado pelos servidores para validar a identidade dos clientes remotos. A verificação baseia-se numa senha partilhada ou em segredo. CHAP pode ser de uma forma (unidirecional) ou mútua (bidirecional). Uma maneira de CHAP é quando o alvo autentica um iniciador. Em CHAP mútuo ou invertido, o alvo autentica o iniciador e, em seguida, o iniciador autentica o alvo. A autenticação do iniciador pode ser implementada sem autenticação do alvo. No entanto, a autenticação-alvo só pode ser implementada se a autenticação do iniciador também for implementada.

Como uma boa prática, recomendamos que use CHAP para melhorar a segurança do iSCSI.

> [!NOTE]
> Tenha em mente que o IPSEC não é atualmente suportado em dispositivos StorSimple.

As definições CHAP no dispositivo StorSimple podem ser configuradas das seguintes formas:

* Autenticação unidirecional ou unidirecional
* Autenticação bidirecional ou mútua ou inversa

Em cada um destes casos, o portal para o dispositivo e o software de iniciação iSCSI do servidor tem de ser configurado. Os passos detalhados para esta configuração são descritos no seguinte tutorial.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidirecional ou unidirecional

Na autenticação unidirecional, o alvo autentica o iniciador. Esta autenticação requer que configufique as definições do iniciador CHAP no dispositivo StorSimple e no software iSCSI Iniciador no anfitrião. Os procedimentos detalhados para o seu dispositivo StorSimple e anfitrião do Windows são descritos em seguida.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar o seu dispositivo para autenticação unidireccionar

1. No portal Azure, aceda ao seu serviço StorSimple Device Manager. Clique **em Dispositivos** e selecione e clique num dispositivo para o quais deseja configurar CHAP. Aceda às **definições do Dispositivo > Segurança**. Na lâmina de **definições de segurança,** clique em **CHAP**.
   
    ![Iniciador CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Na lâmina **CHAP,** e na secção **de Iniciador CHAP:**
   
   1. Forneça um nome de utilizador para o seu iniciador CHAP.
   2. Forneça uma senha para o seu iniciador CHAP.
      
      > [!IMPORTANT]
      > O nome de utilizador CHAP deve conter menos de 233 caracteres. A palavra-passe CHAP deve ter entre 12 e 16 caracteres. Um nome de utilizador ou palavra-passe mais longo resulta numa falha de autenticação no anfitrião do Windows.
   
   3. Confirme a palavra-passe.

       ![Iniciador CHAP 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação unidirecionais no servidor anfitrião do Windows
1. No servidor anfitrião do Windows, inicie o iniciador iSCSI.
2. Na janela **iSCSI Initiator Properties,** execute os seguintes passos:
   
   1. Clique no separador **Discovery.**
      
       ![Propriedades do iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Clique **em Descobrir Portal.**
3. Na caixa de diálogo **Do Portal do Destino Discover:**
   
   1. Especifique o endereço IP do seu dispositivo.
   2. Clique em **Avançadas**.
      
       ![Descubra o portal alvo](./media/storsimple-configure-chap/IC740945.png)
4. Na caixa de diálogo **De Definições Avançadas:**
   
   1. Selecione o **registo DE Enable CHAP na** caixa de verificação.
   2. No campo **Nome,** forneça o nome de utilizador especificado para o Iniciador CHAP no portal Azure.
   3. No campo **secreto Target,** forneça a palavra-passe que especificou para o Iniciador CHAP no portal Azure.
   4. Clique em **OK**.
      
       ![Configurações avançadas gerais](./media/storsimple-configure-chap/IC740946.png)
5. No **separador Alvos** da janela **iSCSI Initiator Properties,** o estado do dispositivo deve aparecer como **Conectado**. Se estiver a utilizar um dispositivo StorSimple 1200, então cada volume é montado como alvo iSCSI. Assim, os passos 3-4 terão de ser repetidos para cada volume.
   
    ![Volumes montados como alvos separados](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Se alterar o nome iSCSI, o novo nome é utilizado para novas sessões iSCSI. As novas definições não são utilizadas para as sessões existentes até que faça login e faça login novamente.

Para obter mais informações sobre a configuração do CHAP no servidor anfitrião do Windows, aceda a [considerações adicionais](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Autenticação bidirecional ou mútua

Na autenticação bidirecional, o alvo autentica o iniciador e, em seguida, o iniciador autentica o alvo. Este procedimento requer que o utilizador configufique as definições do iniciador CHAP, as definições DE CHAP invertidas no dispositivo e o software iSCSI Iniciador no anfitrião. Os seguintes procedimentos descrevem os passos para configurar a autenticação mútua no dispositivo e no anfitrião do Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar o seu dispositivo para autenticação mútua

1. No portal Azure, aceda ao seu serviço StorSimple Device Manager. Clique **em Dispositivos** e selecione e clique num dispositivo para o quais deseja configurar CHAP. Aceda às **definições do Dispositivo > Segurança**. Na lâmina de **definições de segurança,** clique em **CHAP**.
   
    ![Alvo CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Percorra esta página e na secção **CHAP Target:**
   
   1. Forneça um **nome de utilizador REVERso CHAP** para o seu dispositivo.
   2. Forneça uma **palavra-passe CHAP reversa** para o seu dispositivo.
   3. Confirme a palavra-passe.
3. Na secção **de Iniciador CHAP:**
   
   1. Forneça um **nome de utilizador** para o seu dispositivo.
   2. Forneça uma **senha** para o seu dispositivo.
   3. Confirme a palavra-passe.

       ![Iniciador CHAP 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Clique em **Guardar**. É apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação bidirecional no servidor anfitrião do Windows

1. No servidor anfitrião do Windows, inicie o iniciador iSCSI.
2. Na janela **iSCSI Initiator Properties,** clique no **separador Configuração.**
3. Clique **em CHAP**.
4. Na caixa de diálogo **iSCSI Initiator Mutual CHAP Secret:**
   
   1. Digite a **palavra-passe CHAP reversa** que configura no portal Azure.
   2. Clique em **OK**.
      
       ![iSCSI iniciador mútuo CHAP secret](./media/storsimple-configure-chap/IC740949.png)
5. Clique no **separador Alvos.**
6. Clique no botão **Ligar.** 
7. Na caixa de diálogo **'Ligar ao alvo',** clique em **Avançado**.
8. Na caixa de diálogo **Propriedades Avançadas:**
   
   1. Selecione o **registo DE Enable CHAP na** caixa de verificação.
   2. No campo **Nome,** forneça o nome de utilizador especificado para o Iniciador CHAP no portal Azure.
   3. No campo **secreto Target,** forneça a palavra-passe que especificou para o Iniciador CHAP no portal Azure.
   4. Selecione a caixa **de verificação de autenticação mútua** Perform.
      
       ![Autenticação mútua de configurações avançadas](./media/storsimple-configure-chap/IC740950.png)
   5. Clique **em OK** para completar a configuração CHAP

Para obter mais informações sobre a configuração do CHAP no servidor anfitrião do Windows, aceda a [considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais

A **função Quick Connect** não suporta ligações que tenham CHAP ativado. Quando chap estiver ativado, certifique-se de que utiliza o botão **'Ligar'** que está disponível no **separador Destinos** para ligar a um alvo.

![Ligar ao alvo](./media/storsimple-configure-chap/IC740947.png)

Na caixa de diálogo **'Ligar ao Alvo'** apresentada, selecione a **ligação Adicionar esta ligação à lista de alvos favoritos.** Esta seleção garante que sempre que o computador reinicia, é feita uma tentativa de restaurar a ligação aos alvos favoritos do iSCSI.

## <a name="errors-during-configuration"></a>Erros durante a configuração

Se a sua configuração CHAP estiver incorreta, é provável que veja uma mensagem de erro **de autenticação.**

## <a name="verification-of-chap-configuration"></a>Verificação da configuração CHAP

Pode verificar se o CHAP está a ser utilizado completando os seguintes passos.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar a sua configuração CHAP
1. Clique em **Alvos Favoritos**.
2. Selecione o alvo para o qual ativou a autenticação.
3. Clique **em Detalhes.**
   
    ![ISCSI iniciador propriedades alvos favoritos](./media/storsimple-configure-chap/IC740951.png)
4. Na caixa de diálogo **'Detalhes's Favoritos,** note a entrada no campo **de autenticação.** Se a configuração foi bem sucedida, deve dizer **CHAP**.
   
    ![Detalhes favoritos do alvo](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a segurança StorSimple](storsimple-8000-security.md).
* Saiba mais sobre [a utilização do serviço StorSimple Device Manager para administrar o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

