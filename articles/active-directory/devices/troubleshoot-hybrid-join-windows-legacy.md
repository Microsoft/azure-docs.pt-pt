---
title: O legado híbrido Azure Ative Directory juntou-se a dispositivos
description: O Azure Ative Directory híbrido de resolução de problemas juntou-se a dispositivos de nível inferior.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf936370b401a76479c7679499103d083e0752a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551752"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>O Azure Ative Directory híbrido de resolução de problemas juntou-se a dispositivos de nível inferior 

Este artigo aplica-se apenas aos seguintes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Para windows 10 ou Windows Server 2016, consulte [o Azure Ative Directory híbrido de resolução de problemas que aderiu aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

Este artigo pressupõe que [configuraste dispositivos híbridos Azure Ative Directory](hybrid-azuread-join-plan.md) para suportar os seguintes cenários:

- Acesso Condicional com base em dispositivos

Este artigo fornece-lhe orientações de resolução de problemas sobre como resolver potenciais problemas.  

**O que deve saber:** 

- A AD híbrida Azure junta-se a dispositivos Windows de nível inferior ao que funciona no Windows 10. Muitos clientes não percebem que precisam de FS AD (para domínios federados) ou SSO sem costura configurados (para domínios geridos).
- O Seamless SSO não funciona em modo de navegação privada nos navegadores Firefox e Microsoft Edge. Também não funciona no Internet Explorer se o navegador estiver a funcionar no modo Protegido Melhorado ou se a Configuração de Segurança Melhorada estiver ativada.
- Para clientes com domínios federados, se o Ponto de Ligação de Serviço (SCP) foi configurado de modo a apontar para o nome de domínio gerido (por exemplo, contoso.onmicrosoft.com, em vez de contoso.com), então o AD AD Híbrido Azure Join para dispositivos Windows de nível baixo não funcionará.
- O mesmo dispositivo físico aparece várias vezes no Azure AD quando vários utilizadores de domínios inscrevem os dispositivos híbridos Azure AD de nível baixo.  Por exemplo, se *jdoe* e *jharnett* iniciarem sing-in a um dispositivo, é criado um registo separado (DeviceID) para cada um deles no separador informações do **UTILIZADOR.** 
- Também pode obter várias entradas para um dispositivo no separador informações do utilizador devido a uma reinstalação do sistema operativo ou a um re-registo manual.
- O registo inicial/união de dispositivos está configurado para efetuar uma tentativa de iniciar sedus ou bloquear/desbloquear. Pode haver um atraso de 5 minutos desencadeado por uma tarefa de agendamento de tarefas. 
- Certifique-se de que [o KB4284842](https://support.microsoft.com/help/4284842) está instalado, no caso do Windows 7 SP1 ou do Windows Server 2008 R2 SP1. Esta atualização evita futuras falhas de autenticação devido à perda de acesso do cliente a chaves protegidas após a alteração da palavra-passe.
- A junção Híbrida AD pode falhar depois de um utilizador ter a sua UPN alterada, quebrando o processo de autenticação SSO sem emenda. Durante o processo de junção poderá ver que ainda está a enviar a antiga UPN para Azure AD, a menos que os cookies da sessão do navegador sejam limpos ou o utilizador explicitamente se inscreva e remova a UPN antiga.

## <a name="step-1-retrieve-the-registration-status"></a>Passo 1: Recuperar o estado de registo 

**Para verificar o estado do registo:**  

1. Inscreva-se na conta de utilizador que realizou uma junção híbrida Azure AD.
1. Abra o pedido de comando 
1. Escreva `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Este comando apresenta uma caixa de diálogo que lhe fornece detalhes sobre o estado de união.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Screenshot do Workplace Join for Windows dialog box. Texto que inclui um endereço de e-mail afirma que um determinado dispositivo é associado a um local de trabalho." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passo 2: Avaliar o estatuto de ad azure híbrido 

Se o dispositivo não foi híbrido Azure AD, pode tentar fazer a ad AD híbrida, clicando no botão "Join". Se a tentativa de fazer a adada híbrida Azure falhar, os detalhes sobre a falha serão mostrados.

**As questões mais comuns são:**

- Um FS AD mal configurado ou problemas de Ad ou Rede Azure

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Screenshot do Workplace Join for Windows dialog box. O texto informa que ocorreu um erro durante a autenticação da conta." border="false":::
    
   - Autoworkplace.exe é incapaz de autenticar silenciosamente com Azure AD ou AD FS. Isto pode ser causado por falta ou configuração errada AD FS (para domínios federados) ou falta ou configuração errada Azure AD Sem Emenda Sign-On (para domínios geridos) ou problemas de rede. 
   - Pode ser que a autenticação multi-factor (MFA) esteja ativada/configurada para o utilizador e a WIAORMULTIAUTHN não esteja configurada no servidor AD FS. 
   - Outra possibilidade é que a página home realm discovery (HRD) esteja à espera da interação do utilizador, o que impede **queautoworkplace.exe** solicitem silenciosamente um token.
   - Pode ser que os URLs AD FS e Azure AD estejam desaparecidos na zona intranet do IE no cliente.
   - Problemas de conectividade de rede podem estar a impedir **autoworkplace.exe** de chegar em AD FS ou nos URLs AD Azure. 
   - **Autoworkplace.exe** requer que o cliente tenha linha de visão direta do cliente para o controlador de domínio AD da organização, o que significa que a adada híbrida Azure só tem sucesso quando o cliente está ligado à intranet da organização.
   - A sua organização utiliza o Azure AD Seamless Single Sign-On `https://autologon.microsoftazuread-sso.com` ou não está presente nas `https://aadg.windows.net.nsatc.net` definições intranet do dispositivo e **permite que as atualizações para a barra de estado via script** não estejam ativadas para a zona Intranet.
- Não está inscrito como utilizador de domínio

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Screenshot do Workplace Join for Windows dialog box. O texto informa que ocorreu um erro durante a verificação da conta." border="false":::

   Existem algumas razões diferentes para que isto possa ocorrer:

   - O utilizador assinado no utilizador não é um utilizador de domínio (por exemplo, um utilizador local). A ad AD híbrida Azure junta-se a dispositivos de nível inferior é suportada apenas para utilizadores de domínio.
   - O cliente não é capaz de se ligar a um controlador de domínio.    
- Foi alcançada uma quota

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Screenshot do Workplace Join for Windows dialog box. O texto relata um erro porque o utilizador atingiu o número máximo de dispositivos associados." border="false":::

- O serviço não responde 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Screenshot do Workplace Join for Windows dialog box. O texto relata que ocorreu um erro porque o servidor não respondeu." border="false":::

Também pode encontrar as informações de estado no registo do evento em: **Aplicações e Serviços Log\Microsoft-Workplace Join**
  
**As causas mais comuns para uma adere híbrida falhada Azure AD são:** 

- O seu computador não está ligado à rede interna da sua organização ou a uma VPN com uma ligação ao controlador de domínio AD no local.
- Está ligado ao seu computador com uma conta de computador local. 
- Problemas de configuração do serviço: 
   - O servidor AD FS não foi configurado para suportar **WIAORMULTIAUTHN**. 
   - A floresta do seu computador não tem nenhum objeto de Ponto de Ligação de Serviço que indique o seu nome de domínio verificado em Azure AD 
   - Ou se o seu domínio for gerido, então o SSO sem emenda não foi configurado ou funcionando.
   - Um utilizador atingiu o limite dos dispositivos. 

## <a name="next-steps"></a>Passos seguintes

Para dúvidas, consulte a [faq de gestão](faq.yml) de dispositivos  
