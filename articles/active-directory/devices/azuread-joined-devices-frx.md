---
title: Associe um novo dispositivo do Windows 10 ao Azure AD durante a primeira execução | Microsoft Docs
description: Um tópico que explica como os utilizadores podem configurar a Associação do Azure AD durante a primeira experiência de execução.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/03/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: a79c5f89b14d15ffe4f3c582ac7e1e4cabbdc611
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60296661"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Tutorial: Junte-se a um novo dispositivo Windows 10 com o Azure AD durante uma execução primeiro

Com a gestão de dispositivos no Azure Active Directory (Azure AD), pode assegurar que os seus utilizadores acedem aos seus recursos a partir de dispositivos que cumpram as suas normas de segurança e conformidade. Para obter mais informações, veja [introdução à gestão de dispositivos no Azure Active Directory](overview.md).

Com o Windows 10, pode associar um novo dispositivo ao Azure AD durante a experiência de primeira execução (FRX).  
Isto permite-lhe distribuir dispositivos compactados pelos seus colaboradores ou estudantes.

Se tiver o Windows 10 Professional ou o Windows 10 Enterprise instalado num dispositivo, a experiência predefine o processo de instalação dos dispositivos da empresa.

Na *experiência pronta para utilizar* do Windows, não é suportado associar um domínio do Active Directory (AD) no local. Se pretender associar um computador a um domínio do AD durante a configuração, selecione a ligação **Configurar o Windows com uma conta local**. Em seguida, pode associar o domínio a partir das definições do seu computador.
 
Neste tutorial, vai aprender a associar um dispositivo ao Azure AD durante FRX:
 > [!div class="checklist"]
> * Pré-requisitos
> * Associar um dispositivo
> * Verificação

## <a name="prerequisites"></a>Pré-requisitos

Para associar um dispositivo do Windows 10, o serviço de registo do dispositivo tem de ser configurado para que possa registar dispositivos. Além de ter permissão para associar dispositivos no seu inquilino do Azure AD, tem de ter menos dispositivos registados do que o máximo configurado. Para obter mais informações, veja [configurar definições do dispositivo](device-management-azure-portal.md#configure-device-settings).

Além disso, se o seu inquilino é federado, o Fornecedor de identidade tem de suportar o ponto final de nome de utilizador/palavra-passe do WS-Fed e WS-Trust. Isto pode ser a versão 1.3 ou 2005. Este suporte de protocolo é exigido para associar o dispositivo ao Azure AD e iniciar sessão no dispositivo com uma palavra-passe.

## <a name="joining-a-device"></a>Associar um dispositivo

**Para associar um dispositivo do Windows 10 ao Azure AD durante o FRX:**


1. Quando ativar o seu dispositivo novo e iniciar o processo de configuração, deverá ver a mensagem **Em Preparação**. Siga as instruções para configurar o dispositivo.

2. Comece por personalizar a sua região e idioma. Em seguida, aceite os Termos de Licenciamento de Software da Microsoft.
 
    ![Personalizar para a sua região](./media/azuread-joined-devices-frx/01.png)

3. Selecione a rede que pretende utilizar para ligar à Internet.

4. Clique em **Este dispositivo pertence à minha organização**. 

    ![A quem pertence este ecrã de PC](./media/azuread-joined-devices-frx/02.png)

5. Introduza as credenciais que foram dadas pela sua organização e, em seguida, clique em **Iniciar sessão**.

    ![Ecrã de início de sessão](./media/azuread-joined-devices-frx/03.png)

6. O seu dispositivo localiza um inquilino correspondente no Azure AD. Se estiver num domínio federado, será redirecionado para o seu servidor de Serviço de Token Seguro (STS) no local, por exemplo, o Active Directory Federation Services (AD FS).

7. Se for um utilizador num domínio não federado, introduza as credenciais diretamente na página alojada do Azure AD. 

8. É desafiado para a autenticação multifator. 
 
9. O Azure AD verifica se é precisa uma inscrição na gestão de dispositivos móveis.

10. O Windows regista o dispositivo no diretório da organização no Azure AD e inscreve-o na gestão de dispositivos móveis, se aplicável.

11. Se for:
    - Um utilizador gerido, o Windows leva-o para o ambiente de trabalho através do processo de início de sessão automático.

    - Um utilizador federado, será direcionado para o ecrã de início de sessão do Windows para introduzir as suas credenciais.

## <a name="verification"></a>Verificação

Para verificar se um dispositivo está associado ao Azure AD, reveja a caixa de diálogo **Acesso profissional ou escolar** no seu dispositivo Windows. A caixa de diálogo deve indicar que está ligado ao seu diretório do Azure AD.

![Acesso escolar ou profissional](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações, veja [introdução à gestão de dispositivos no Azure Active Directory](overview.md).

- Para obter mais informações sobre a gestão de dispositivos no portal do Azure AD, veja [gerir dispositivos com o portal do Azure](device-management-azure-portal.md).
