---
title: Solucionar problemas de dispositivos ingressados Azure Active Directory híbridos herdados
description: Solução de problemas de Azure Active Directory híbrido ingressado em dispositivos de nível inferior.
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379107"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Solucionando problemas de Azure Active Directory híbrido ingressado em dispositivos de nível inferior 

Este artigo é aplicável somente aos seguintes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Para o Windows 10 ou o Windows Server 2016, consulte [solução de problemas híbrido Azure Active Directory ingressados em dispositivos Windows 10 e Windows server 2016](troubleshoot-hybrid-join-windows-current.md).

Este artigo pressupõe que você tenha [configurado dispositivos ingressados no Azure Active Directory híbrido](hybrid-azuread-join-plan.md) para dar suporte aos seguintes cenários:

- Acesso condicional com base no dispositivo

Este artigo fornece orientação para solução de problemas de como resolver possíveis questões.  

**O que você deve saber:** 

- O ingresso do Azure AD híbrido para dispositivos Windows de nível inferior funciona de forma ligeiramente diferente do que no Windows 10. Muitos clientes não percebem que precisam de AD FS (para domínios federados) ou o SSO contínuo configurado (para domínios gerenciados).
- Para clientes com domínios federados, se o SCP (ponto de conexão de serviço) tiver sido configurado de forma que ele aponte para o nome de domínio gerenciado (por exemplo, contoso.onmicrosoft.com, em vez de contoso.com), o ingresso híbrido do Azure AD para dispositivos Windows de nível inferior será Não funciona.
- O número máximo de dispositivos por usuário, atualmente, também se aplica a dispositivos ingressados no Azure AD híbridos de nível inferior. 
- O mesmo dispositivo físico aparece várias vezes no Azure AD quando vários usuários de domínio entram nos dispositivos ingressados no Azure AD híbridos de nível inferior.  Por exemplo, se o *jdoe* e o *jharnett* entrarem em um dispositivo, um registro separado (DeviceID) será criado para cada um deles na guia informações do **usuário** . 
- Você também pode obter várias entradas para um dispositivo na guia informações do usuário devido a uma reinstalação do sistema operacional ou a um novo registro manual.
- O registro/junção inicial de dispositivos é configurado para executar uma tentativa de entrada ou bloqueio/desbloqueio. Pode haver um atraso de 5 minutos disparado por uma tarefa do Agendador de tarefas. 
- Verifique se o [KB4284842](https://support.microsoft.com/help/4284842) está instalado, no caso do Windows 7 SP1 ou do windows Server 2008 R2 SP1. Essa atualização impede falhas de autenticação futuras devido à perda de acesso do cliente às chaves protegidas após a alteração da senha.

## <a name="step-1-retrieve-the-registration-status"></a>Etapa 1: recuperar o status do registro 

**Para verificar o status do registro:**  

1. Faça logon com a conta de usuário que executou uma junção híbrida do Azure AD.
1. Abrir o prompt de comando 
1. Escreva `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Esse comando exibe uma caixa de diálogo que fornece detalhes sobre o status de junção.

![Workplace Join para Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Etapa 2: avaliar o status de ingresso do Azure AD híbrido 

Se o dispositivo não tiver ingressado no Azure AD híbrido, você poderá tentar fazer uma junção híbrida do Azure AD clicando no botão "ingressar". Se a tentativa de fazer uma junção híbrida do Azure AD falhar, os detalhes sobre a falha serão mostrados.

**Os problemas mais comuns são:**

- Um AD FS configurado incorretamente ou problemas de rede ou do Azure AD

    ![Workplace Join para Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
   - O autoworkplace. exe não pode se autenticar silenciosamente com o Azure AD ou AD FS. Isso pode ser causado por uma AD FS ausente ou configurada incorretamente (para domínios federados) ou o logon único contínuo do Azure AD (para domínios gerenciados) ou problemas de rede ausentes ou configurados incorretamente. 
   - Pode ser que a MFA (autenticação multifator) esteja habilitada/configurada para o usuário e o WIAORMULTIAUTHN não esteja configurado no servidor de AD FS. 
   - Outra possibilidade é que a página HRD (descoberta de realm inicial) esteja aguardando a interação do usuário, o que impede que o **autoworkplace. exe** solicite um token silenciosamente.
   - Pode ser que AD FS e as URLs do Azure AD estejam ausentes na zona de intranet do IE no cliente.
   - Problemas de conectividade de rede podem estar impedindo que o **autoworkplace. exe** atinja AD FS ou as URLs do Azure AD. 
   - O **autoworkplace. exe** exige que o cliente tenha uma linha de visão direta do cliente para o controlador de domínio do AD local da organização, o que significa que a junção híbrida do Azure AD é realizada com sucesso somente quando o cliente está conectado à intranet da organização.
   - Sua organização usa o logon único contínuo do Azure AD, `https://autologon.microsoftazuread-sso.com` ou `https://aadg.windows.net.nsatc.net` não estão presentes nas configurações de intranet do IE do dispositivo, e **permitir que as atualizações da barra de status por meio do script** não estejam habilitadas para a zona da intranet.
- Você não está conectado como um usuário de domínio

   ![Workplace Join para Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)

   Há algumas razões diferentes pelas quais isso pode ocorrer:

   - O usuário conectado não é um usuário de domínio (por exemplo, um usuário local). O ingresso no Azure AD híbrido em dispositivos de nível inferior tem suporte apenas para usuários de domínio.
   - O cliente não é capaz de se conectar a um controlador de domínio.    
- Uma cota foi atingida

    ![Workplace Join para Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- O serviço não está respondendo 

    ![Workplace Join para Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Você também pode encontrar as informações de status no log de eventos em: **aplicativos e serviços Log\Microsoft-Workplace Join**
  
**As causas mais comuns para uma junção híbrida do Azure AD com falha são:** 

- O computador não está conectado à rede interna da sua organização ou a uma VPN com uma conexão com o controlador de domínio do AD local.
- Você está conectado ao computador com uma conta de computador local. 
- Problemas de configuração de serviço: 
   - O servidor de AD FS não foi configurado para dar suporte a **WIAORMULTIAUTHN**. 
   - A floresta do seu computador não tem um objeto de ponto de conexão de serviço que aponte para seu nome de domínio verificado no Azure AD 
   - Ou, se o seu domínio for gerenciado, o SSO contínuo não foi configurado ou está funcionando.
   - Um usuário atingiu o limite de dispositivos. 

## <a name="next-steps"></a>Passos seguintes

Para perguntas, consulte as [perguntas frequentes sobre gerenciamento de dispositivo](faq.md)  
