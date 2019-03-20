---
title: Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível inferior | Documentos da Microsoft
description: Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível inferior.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e79b0fc0ef23287c8871b7a7634d28d129b900e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101147"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Resolução de problemas híbrida do Azure Active Directory dispositivos associados a um nível inferior 

Este artigo é aplicável apenas aos seguintes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 ou Windows Server 2016, veja [híbrido de resolução de problemas do Azure Active Directory associou-se os dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

Este artigo pressupõe que tenha [dispositivos associados ao configurado híbrida do Azure Active Directory](hybrid-azuread-join-plan.md) para suportar os cenários seguintes:

- Acesso condicional baseado no dispositivo


Este artigo fornece orientações sobre como resolver problemas potenciais de resolução de problemas.  

**O que deve saber:** 

- Associação do Azure AD híbrido de nível inferior funciona de dispositivos do Windows forma um pouco diferente do que faz no Windows 10. Muitos clientes não perceber que precisam do AD FS (para domínios federados) ou o SSO totalmente integrado configurado (para domínios geridos).

- Para clientes com domínios federados, se o ponto de ligação de serviço (SCP) tiver sido configurado, de modo a que aponte para o nome de domínio geridos (por exemplo, contoso.onmicrosoft.com, em vez de contoso.com), em seguida, híbrido do Azure AD Join para dispositivos do Windows de nível inferior será não funcionam.

- O número máximo de dispositivos por utilizador atualmente também se aplica a dispositivos de associados ao Azure AD híbrido de nível inferior. 

- É apresentada no mesmo dispositivo físico várias vezes no Azure AD quando vários usuários de domínio início de sessão os dispositivos de associados ao Azure AD híbrido nível inferior.  Por exemplo, se *jdoe* e *jharnett* início de sessão a um dispositivo, um registo separados (DeviceID) é criada para cada uma no **utilizador** separador informações. 

- Também pode obter várias entradas para um dispositivo no separador de informações de utilizador devido a uma reinstalação do sistema operativo ou um novo registo manual.

- O registo inicial / associação de dispositivos é configurada para executar uma tentativa de início de sessão ou bloqueio / desbloqueio. Pode haver atraso de 5 minutos, acionado por uma tarefa do agendador de tarefas. 

- Certifique-se [KB4284842](https://support.microsoft.com/help/4284842) estiver instalado, no caso do Windows 7 SP1 ou Windows Server 2008 R2 SP1. Esta atualização impede que as falhas de autenticação de futuras devido a perda de acesso do cliente para chaves protegidas depois de alterar a palavra-passe.

## <a name="step-1-retrieve-the-registration-status"></a>Passo 1: Obter o estado do registo 

**Para verificar o estado do registo:**  

1. Início de sessão com a conta de utilizador que efetuou a associação do Azure AD híbrido.

2. Abra a linha de comandos 

3. Tipo `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Este comando apresenta uma caixa de diálogo que lhe fornece detalhes sobre o estado de associação.

![Associação à área de trabalho para Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passo 2: Avaliar a Estado de associação ao Azure AD de híbrida 

Se o dispositivo não tiver sido associado ao Azure AD híbrido, pode tentar fazer a associação ao Azure AD híbrido ao clicar no botão "Associar". Se a tentativa de fazer a associação do híbrida do Azure AD falhar, os detalhes sobre a falha serão apresentados.


**Os problemas mais comuns são:**

- Um configurado incorretamente do AD FS ou o Azure AD ou problemas de rede

    ![Associação à área de trabalho para Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
  - Autoworkplace.exe não conseguiu autenticar silenciosamente com o Azure AD ou AD FS. Isto pode dever-se em falta ou configurado incorretamente o AD FS (para domínios federados) ou em falta ou incorrectamente configurado do Azure AD totalmente integrada início de sessão único (para domínios geridos) ou problemas de rede. 
    
    - É possível que a autenticação multifator (MFA) é ativada ou não está configurado para o utilizador e WIAORMUTLIAUTHN não está configurado no servidor do AD FS. 
     
    - Outra possibilidade é essa página de deteção (HRD) de realm inicial está a aguardar interação do usuário, que impede **autoworkplace.exe** silenciosamente solicitem um token.
     
    - É possível que o AD FS e URLs do Azure AD estão em falta na zona de intranet do IE no cliente.
     
    - Problemas de conectividade de rede podem estar a impedir **autoworkplace.exe** chegue do AD FS ou os URLs de AD do Azure. 
     
    - **Autoworkplace.exe** exige que o cliente ter direta linha de visão do cliente com local a organização do controlador de domínio do AD, que significa que essa associação do Azure AD híbrido é bem-sucedida apenas quando o cliente está ligado à intranet da organização.
     
    - Sua organização utiliza o Azure AD totalmente integrada início de sessão único, `https://autologon.microsoftazuread-sso.com` ou `https://aadg.windows.net.nsatc.net` não estão presentes nas definições de intranet de IE do dispositivo, e **permitir atualizações à barra de estado por meio de script** não está ativada para a zona da Intranet.

- Não tem sessão iniciada como um utilizador de domínio

    ![Associação à área de trabalho para Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Há alguns motivos diferentes, por que isso pode ocorrer:
    
    - O utilizador com sessão iniciada não é um utilizador de domínio (por exemplo, um utilizador local). Associação do Azure AD híbrido em dispositivos de nível inferior é suportada apenas para utilizadores de domínio.
    
    - O cliente não é possível estabelecer ligação com um controlador de domínio.    

- Foi atingida uma quota

    ![Associação à área de trabalho para Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- O serviço não está a responder 

    ![Associação à área de trabalho para Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Também pode encontrar as informações de estado no registo de eventos em: **Aplicações e serviços a associação à área de trabalho a Log\Microsoft**
  
**As causas mais comuns para uma associação do Azure AD híbrido de falha são:** 

- O computador não está ligado à rede interna da sua organização ou para uma VPN com uma ligação com o local do controlador de domínio do AD.

- Tiver sessão iniciada seu computador com uma conta de computador local. 

- Problemas de configuração do serviço: 

  - Servidor do AD FS não foi configurado para suportar **WIAORMULTIAUTHN**. 

  - Floresta do seu computador não tem qualquer objeto de ponto de ligação de serviço que aponta para o seu nome de domínio verificado no Azure AD 
  
  - Ou se o seu domínio gerido, não foi configurado o SSO totalmente integrado ou está a funcionar.

  - Um utilizador atingir o limite de dispositivos. 

## <a name="next-steps"></a>Passos Seguintes

Para perguntas, consulte o [perguntas frequentes de gestão de dispositivos](faq.md)  
