---
title: Tutorial – integrar uma única floresta a um único locatário do Azure AD
description: Este tópico descreve os pré-requisitos e os requisitos de hardware do provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f7c348e29fa2234627a53095a99e913a6389a27
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280973"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Tutorial: integrar uma única floresta a um único locatário do Azure AD

Este tutorial orienta você pela criação de um ambiente de identidade híbrida usando o provisionamento de nuvem do Azure Active Directory (Azure AD) Connect.

![Create](media/tutorial-single-forest/diagram1.png)

Você pode usar o ambiente criado neste tutorial para teste ou para se familiarizar mais com o provisionamento na nuvem.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../active-directory-users-create-azure-portal.md). A conclusão desta etapa é essencial para garantir que você não fique bloqueado do seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identificar um servidor de host ingressado no domínio que executa o Windows Server 2012 R2 ou superior com o mínimo de 4 GB de RAM e do tempo de execução do .NET 4.7.1 + 

2. Se houver um firewall entre seus servidores e o Azure AD, configure os seguintes itens:
   - Verifique se os agentes podem fazer solicitações de *saída* para o Azure ad nas seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Baixa as listas de certificados revogados (CRLs) ao validar o certificado SSL |
     | **443** | Manipula toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os agentes relatarão seu status a cada 10 minutos pela porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do AD do Azure. |
     
     Se o firewall impõe regras de acordo com os usuários de origem, abra essas portas para o tráfego de serviços do Windows que são executados como um serviço de rede.
   - Se o seu firewall ou proxy permitir que você especifique os sufixos seguros, adicione as conexões t a **\*. msappproxy.net** e **\*. ServiceBus.Windows.net**. Caso contrário, permita o acesso aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Seus agentes precisam de acesso a **login.Windows.net** e **login.microsoftonline.com** para o registro inicial. Abra seu firewall para essas URLs também.
   - Para a validação de certificado, desbloqueie as seguintes URLs: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**e **www\.Microsoft.com:80**. Como essas URLs são usadas para validação de certificado com outros produtos da Microsoft, você pode já ter essas URLs desbloqueadas.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalar o agente de provisionamento do Azure AD Connect
1. Entre no servidor ingressado no domínio.  Se você estiver usando o tutorial [básico do ambiente do AD e do Azure](tutorial-basic-ad-azure.md) , seria DC1.
2. Entre no portal do Azure usando credenciais de administrador global somente em nuvem.
3. À esquerda, selecione **Azure Active Directory**, clique em **Azure ad Connect**e, no centro, selecione **gerenciar provisionamento (versão prévia)** .

   ![Portal do Azure](media/how-to-install/install6.png)

4. Clique em **baixar agente**.
5. Execute o agente de provisionamento do Azure AD Connect.
6. Na tela inicial, **aceite** os termos de licenciamento e clique em **instalar**.

   ![Tela de boas-vindas](media/how-to-install/install1.png)

7. Quando essa operação for concluída, o assistente de configuração será iniciado.  Entre com sua conta de administrador global do Azure AD.  Observe que, se você tiver a segurança aprimorada do IE habilitada, isso bloqueará a entrada.  Se esse for o caso, feche a instalação, desabilite a segurança aprimorada do IE no Gerenciador do Servidor e clique no **Assistente do agente de provisionamento do AAD Connect** para reiniciar a instalação.
8. Na tela **conectar Active Directory** , clique em **Adicionar diretório** e entre com sua conta de administrador de domínio do Active Directory.  Observação: a conta de administrador de domínio não deve ter requisitos de alteração de senha. Caso a senha expire ou seja alterada, será necessário reconfigurar o agente com as novas credenciais. Esta operação adicionará seu diretório local.  Clique em **Seguinte**.

   ![Tela de boas-vindas](media/how-to-install/install3.png)

9. Na tela **configuração concluída** , clique em **confirmar**.  Esta operação registrará e reiniciará o agente.

   ![Tela de boas-vindas](media/how-to-install/install4.png)

10. Quando essa operação for concluída, você deverá ver um aviso: **a configuração do agente foi verificada com êxito.**  Você pode clicar em **sair**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install5.png)</br>
11. Se você ainda vir a tela de abertura inicial, clique em **fechar**.


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

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas:

1.  Faça logon no servidor com uma conta de administrador
2.  Abra os **Serviços** navegando até ele ou acessando iniciar/executar/Services. msc.
3.  Em **Serviços**, verifique se **Microsoft Azure ad atualizador do agente** e **Microsoft Azure ad agente de provisionamento do Connect** estão presentes e se o status está **em execução**.
![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configurar Azure AD Connect provisionamento de nuvem
 Use as etapas a seguir para configurar o provisionamento

1.  Entre no portal do AD do Azure.
2.  Clique em **Azure Active Directory**
3.  Clique em **Azure ad Connect**
4.  Selecione **gerenciar provisionamento (visualização)** 
![](media/how-to-configure/manage1.png)
5.  Clique em **nova configuração**
![](media/tutorial-single-forest/configure1.png)
7.  Na tela configuração, insira um **email de notificação**, mova o seletor para **habilitar** e clique em **salvar**.
![](media/tutorial-single-forest/configure2.png)
1.  O status da configuração agora deve estar **íntegro**.
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verificar se os usuários são criados e a sincronização está ocorrendo
Agora, você verificará se os usuários que você tinha em nosso diretório local foram sincronizados e agora existem em nosso locatário do Azure AD.  Lembre-se de que isso pode levar algumas horas para ser concluído.  Para verificar se os usuários estão sincronizados, faça o seguinte.


1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Active Directory**
3. Em **Gerir**, selecione **Utilizadores**.
4. Verifique se você vê os novos usuários em nosso locatário</br>
Sincronização de ![](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testar a entrada com um de nossos usuários

1. Navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Entre com uma conta de usuário que foi criada em nosso novo locatário.  Você precisará entrar usando o seguinte formato: (user@domain.onmicrosoft.com). Use a mesma senha que o usuário usa para entrar no local.</br>
   ![verificar](media/tutorial-single-forest/verify1.png)</br>

Agora você configurou com êxito um ambiente de identidade híbrida que pode ser usado para testar e se familiarizar com o que o Azure tem a oferecer.


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
