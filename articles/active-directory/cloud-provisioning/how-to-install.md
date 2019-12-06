---
title: Instalando o agente de provisionamento de nuvem Azure AD Connect
description: Este tópico descreve passo a passo como instalar o agente de provisionamento.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846237"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>Instalar Azure AD Connect agente de provisionamento de nuvem
Este documento explicará o processo de instalação do agente de provisionamento do Azure AD Connect e como configurá-lo inicialmente no portal do Azure.

>[!IMPORTANT]
>As instruções de instalação a seguir pressupõem que todos os [pré-requisitos](how-to-prerequisites.md) foram atendidos.

Instalar e configurar Azure AD Connect provisionamento é feito nas seguintes etapas:
    
- [Instalar o agente](#install-the-agent)
- [Verificar a instalação do agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Instalar o agente

1. Entre no servidor que será usado com as permissões de administrador corporativo.
2. Navegue até a portal do Azure, selecione Azure Active Directory à esquerda.
3. Clique em **gerenciar provisionamento (versão prévia)** e selecione **examinar todos os agentes**.
3. Baixe o agente de provisionamento do Azure AD Connect do portal do Azure.
![Ecrã de Boas-Vindas](media/how-to-install/install9.png)</br>
3. Executar o provisionamento de Azure AD Connect (AADConnectProvisioningAgent. Installer)
3. Na tela inicial, **aceite** os termos de licenciamento e clique em **instalar**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install1.png)</br>

4. Quando essa operação for concluída, o assistente de configuração será iniciado.  Entre com sua conta de administrador global do Azure AD.
5. Na tela **conectar Active Directory** , clique em **Adicionar diretório** e entre com sua conta de administrador do Active Directory.  Esta operação adicionará seu diretório local.  Clique em **Seguinte**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install3.png)</br>

6. Na tela **configuração concluída** , clique em **confirmar**.  Esta operação registrará e reiniciará o agente.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install4.png)</br>

7. Após a conclusão dessa operação, você deverá ver um aviso **de que seu foi verificado com êxito.**  Você pode clicar em **sair**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install5.png)</br>
8. Se você ainda vir a tela de abertura inicial, clique em **fechar**.


## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre na portal do Azure e no servidor local que está executando o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente de portal do Azure
Para verificar se o agente está sendo visto pelo Azure, siga estas etapas:

1. Inicie sessão no Portal do Azure.
2. À esquerda, selecione **Azure Active Directory**, clique em **Azure ad Connect** e, no centro, selecione **gerenciar provisionamento (versão prévia)** .</br>
![Portal do Azure](media/how-to-install/install6.png)</br>

3.  Na tela **provisionamento do Azure AD (versão prévia)** , clique em **examinar todos os agentes**.
![o provisionamento do Azure AD](media/how-to-install/install7.png)</br>
 
4. Na **tela agentes de provisionamento locais** , você verá os agentes que você instalou.  Verifique se o agente em questão está selecionado e se está marcado como **ativo**.
![agentes de provisionamento](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verificar a porta
Para verificar se o Azure está escutando na porta 443 e se o agente pode se comunicar com ele, você pode usar o seguinte:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Esse teste verificará se os agentes podem se comunicar com o Azure pela porta 443.  Abra um navegador e navegue até a URL acima do servidor em que o agente está instalado.
![Serviços](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas:

1.  Faça logon no servidor com uma conta de administrador
2.  Abra os **Serviços** navegando até ele ou acessando iniciar/executar/Services. msc.
3.  Em **Serviços** , verifique se **Microsoft Azure ad atualizador do agente** e o **agente de provisionamento do Microsoft Azure ad Connect** estão lá e se o status está **em execução**.
![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>O agente foi instalado, mas deve ser configurado e habilitado antes de iniciar a sincronização de usuários.  Para configurar um novo agente, consulte [Azure ad Connect provisionamento de nova configuração de agente](how-to-configure.md).



## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
 
