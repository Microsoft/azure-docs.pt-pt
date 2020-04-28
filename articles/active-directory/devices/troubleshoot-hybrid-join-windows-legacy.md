---
title: O legacy azure ative diretório da Troubleshoot juntou-se a dispositivos
description: O híbrido Azure Ative Directory juntou-se a dispositivos de nível inferior.
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
ms.openlocfilehash: e168deea1ba442d48f483264c1e97ce618040f18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74379107"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos de nível inferior 

Este artigo é aplicável apenas aos seguintes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Para windows 10 ou Windows Server 2016, consulte [o Diretório Ativo Azure Ative de Resolução de Problemas que se juntou aos dispositivos Windows 10 e Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

Este artigo pressupõe que configuraste o [Azure Ative Directory híbrido juntou-se](hybrid-azuread-join-plan.md) a dispositivos para suportar os seguintes cenários:

- Acesso Condicional baseado em dispositivos

Este artigo fornece-lhe orientações de resolução de problemas sobre como resolver potenciais problemas.  

**O que deve saber:** 

- A ad ida a Hybrid Azure para dispositivos Windows de nível inferior funciona de forma ligeiramente diferente da do Windows 10. Muitos clientes não percebem que precisam de AD FS (para domínios federados) ou SSO Seamless configurado (para domínios geridos).
- Para os clientes com domínios federados, se o Service Connection Point (SCP) foi configurado de tal forma que aponta para o nome de domínio gerido (por exemplo, contoso.onmicrosoft.com, em vez de contoso.com), então o Hybrid Azure AD Join para dispositivos Windows de nível inferior não funcionará.
- O número máximo de dispositivos por utilizador aplica-se atualmente também aos dispositivos híbridos azure ad de nível inferior. 
- O mesmo dispositivo físico aparece várias vezes em Azure AD quando vários utilizadores de domínio assinam o híbrido azure ad de nível inferior a dispositivos.  Por exemplo, se *jdoe* e *jharnett* iniciars sessão num dispositivo, é criado um registo separado (DeviceID) para cada um deles no separador **USER** info. 
- Também pode obter várias entradas para um dispositivo no separador informação do utilizador devido a uma reinstalação do sistema operativo ou a uma reinscrição manual.
- O registo/adesão inicial dos dispositivos está configurado para realizar uma tentativa de iniciar sessão ou bloqueio/desbloqueio. Pode haver um atraso de 5 minutos desencadeado por uma tarefa de programador de tarefas. 
- Certifique-se de que o [KB4284842](https://support.microsoft.com/help/4284842) está instalado, no caso de Windows 7 SP1 ou Windows Server 2008 R2 SP1. Esta atualização evita futuras falhas de autenticação devido à perda de acesso do cliente às chaves protegidas após a alteração da palavra-passe.

## <a name="step-1-retrieve-the-registration-status"></a>Passo 1: Recuperar o estado de registo 

**Para verificar o estado de registo:**  

1. Inscreva-se na conta de utilizador que realizou um Anúncio Azure híbrido.
1. Abra o pedido de comando 
1. Escreva `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Este comando exibe uma caixa de diálogo que lhe fornece detalhes sobre o estado da união.

![Adere ao local de trabalho para janelas](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passo 2: Avaliar o estatuto de adesão híbrida azure 

Se o dispositivo não foi híbrido Azure AD aderiu, pode tentar fazer a adesão híbrida azure aquecê-lo clicando no botão "Join". Se a tentativa de fazer a adi híbrida Azure falhar, os detalhes sobre a falha serão mostrados.

**As questões mais comuns são:**

- Um AD FS mal configurado ou aD Azure ou problemas de rede

    ![Adere ao local de trabalho para janelas](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - Autoworkplace.exe é incapaz de autenticar silenciosamente com Azure AD ou AD FS. Isto pode ser causado por falta ou configuração ad FS (para domínios federados) ou falta ou mal configurada Azure AD Seamless Single Sign-On (para domínios geridos) ou problemas de rede. 
   - Pode ser que a autenticação de vários fatores (MFA) esteja ativada/configurada para o utilizador e wiAORMULTIAUTHN não esteja configurada no servidor AD FS. 
   - Outra possibilidade é que a página de descoberta de reino doméstico (HRD) esteja à espera da interação do utilizador, o que impede o **autoworkplace.exe** de pedir silenciosamente um símbolo.
   - Pode ser que os URLs AD FS e Azure estejam desaparecidos na zona intranet da IE sobre o cliente.
   - Os problemas de conectividade da rede podem estar a impedir que o **autoworkplace.exe** chegue a AD FS ou aos URLs AD Azure. 
   - **Autoworkplace.exe** exige que o cliente tenha linha de visão direta do cliente para o controlador de domínio AD da organização no local, o que significa que a adesão híbrida azure só tem sucesso quando o cliente está ligado à intranet da organização.
   - A sua organização utiliza o Azure `https://autologon.microsoftazuread-sso.com` AD Seamless Single Sign-On, ou `https://aadg.windows.net.nsatc.net` não está presente nas definições intranet iE do dispositivo, e **permitir atualizações para a barra** de estado através do script não está ativada para a zona intranet.
- Não está inscrito como utilizador de domínio

   ![Adere ao local de trabalho para janelas](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Existem algumas razões diferentes para que isto possa ocorrer:

   - O utilizador assinado não é um utilizador de domínio (por exemplo, um utilizador local). A adesão da Hybrid Azure a dispositivos de nível inferior é suportada apenas para utilizadores de domínio.
   - O cliente não é capaz de se ligar a um controlador de domínio.    
- Uma quota foi alcançada

    ![Adere ao local de trabalho para janelas](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- O serviço não responde 

    ![Adere ao local de trabalho para janelas](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Também pode encontrar as informações de estado no registo do evento em: **Applications and Services Log\Microsoft-Workplace Join**
  
**As causas mais comuns para uma adesão híbrida híbrida falida do Azure AD são:** 

- O seu computador não está ligado à rede interna da sua organização ou a uma VPN com ligação ao seu controlador de domínio AD no local.
- Está ligado ao seu computador com uma conta de computador local. 
- Problemas de configuração do serviço: 
   - O servidor AD FS não foi configurado para suportar **wiAORMULTIAUTHN**. 
   - A floresta do seu computador não tem objeto de Ponto de Ligação de Serviço que aponta para o seu nome de domínio verificado em Azure AD 
   - Ou se o seu domínio for gerido, então o SSO SSO sem emenda não foi configurado ou funcionando.
   - Um utilizador atingiu o limite dos dispositivos. 

## <a name="next-steps"></a>Passos seguintes

Para perguntas, consulte as FAQ de [gestão de dispositivos](faq.md)  
