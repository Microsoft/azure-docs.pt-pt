---
title: Tutorial - Integre uma floresta existente e uma nova floresta com um único inquilino Azure AD utilizando o fornecimento de nuvem Azure AD Connect.
description: tutorial.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa612ad30ae0faa42071613be15c1d91fb96b8f6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332272"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Integrar uma floresta existente e uma nova floresta num único inquilino do Azure AD

Este tutorial acompanha-o através da adição de fornecimento de nuvens a um ambiente de identidade híbrida existente. 

![Criar](media/tutorial-existing-forest/existing-forest-new-forest.png)

Você pode usar o ambiente que você cria neste tutorial para testar ou para se familiarizar mais com o funcionamento de uma identidade híbrida. 

Neste cenário, existe uma floresta existente sincronizada usando a sincronização Azure AD Connect a um inquilino da AD Azure. E você tem uma nova floresta que você quer sincronizar com o mesmo inquilino azure ad. Vai preparar o fornecimento de nuvens para a nova floresta. 

## <a name="prerequisites"></a>Pré-requisitos
### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Diretório Ativo Azure

1. Crie uma conta de administrador global apenas em nuvem no seu inquilino Azure AD. Desta forma, pode gerir a configuração do seu inquilino caso os seus serviços no local falhem ou fiquem indisponíveis. Saiba adicionar uma conta de [administrador global só para a nuvem.](../active-directory-users-create-azure-portal.md) Completar este passo é fundamental para garantir que não seja trancado fora do seu inquilino.
2. Adicione um ou mais nomes de [domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu inquilino Azure AD. Os seus utilizadores podem iniciar sessão com um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identidade um servidor de anfitriões associado ao domínio executando O Windows Server 2012 R2 ou maior com um mínimo de 4 GB de RAM e .NET 4.7.1+ tempo de execução 

2. Se houver uma firewall entre os seus servidores e a AD Azure, configure os seguintes itens:
   - Certifique-se de que os agentes podem fazer pedidos de *saída* à Azure AD sobre as seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Descarrega as listas de revogação do certificado (CRLs) enquanto valida o certificado TLS/SSL |
     | **443** | Lida com toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os agentes reportam o seu estado a cada 10 minutos sobre o porto 8080, se a porta 443 não estiver disponível. Este estado é apresentado no portal Azure AD. |
     
     Se a sua firewall aplicar regras de acordo com os utilizadores originários, abra estas portas para tráfego a partir de serviços Windows que funcionam como um serviço de rede.
   - Se a sua firewall ou procuração lhe permitir especificar sufixos seguros, adicione ligações a ** \*.msappproxy.net** e ** \*.servicebus.windows.net**. Caso contrário, permita o acesso às gamas IP do Centro de [Dados Azure,](https://www.microsoft.com/download/details.aspx?id=41653)que são atualizadas semanalmente.
   - Os seus agentes precisam de acesso a **login.windows.net** e **login.microsoftonline.com** para registo inicial. Abra a sua firewall para os URLs também.
   - Para validação de certificados, desbloqueie os seguintes URLs: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**e **\.www microsoft.com:80**. Uma vez que estes URLs são utilizados para validação de certificados com outros produtos da Microsoft, pode já ter estes URLs desbloqueados.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instale o agente de provisionamento Azure AD Connect
1. Inscreva-se no servidor de domínio.  Se estiver a usar o tutorial [de ambiente Basic AD e Azure,](tutorial-basic-ad-azure.md) seria DC1.
2. Inscreva-se no portal Azure usando credenciais de administração global apenas em nuvem.
3. À esquerda, selecione **Azure Ative Directory,** clique em **Azure AD Connect** e no centro selecione **Gerir o fornecimento (pré-visualização)**.</br>
![Portal do Azure](media/how-to-install/install6.png)</br>
4. Clique em "Descarregamento agente"
5. Executar o agente de provisionamento Azure AD Connect
6. No ecrã de salpicos, **Aceite** os termos de licenciamento e clique em **Instalar**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install1.png)</br>

7. Assim que esta operação estiver concluída, o assistente de configuração será lançado.  Inscreva-se na sua conta de administrador global da AD Azure.  Note que se tiver uma segurança reforçada da IE, esta medida bloqueará o início de sessão.  Se for esse o caso, feche a instalação, desative a segurança reforçada do IE no Gestor do Servidor e clique no Assistente de Provisionamento de **Ligação AAD** para reiniciar a instalação.
8. No ecrã **Connect Ative Diretório,** clique em **Adicionar diretório** e, em seguida, iniciar sessão com a sua conta de administrador de domínio de Diretório Ativo.  NOTA: A conta de administrador de domínio não deve ter requisitos de alteração de palavra-passe. Caso a palavra-passe expire ou mude, terá de reconfigurar o agente com as novas credenciais. Esta operação irá adicionar o seu diretório no local.  Clique em **Seguinte**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install3.png)</br>

9. No ecrã completo da **Configuração,** clique em **Confirmar**.  Esta operação registar-se-á e reiniciará o agente.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install4.png)</br>

10. Uma vez concluída esta operação, deverá ver um aviso: **a configuração do seu agente foi verificada com sucesso.**  Pode clicar em **Sair**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install5.png)</br>
11. Se ainda vir o ecrã de respingo inicial, clique em **Fechar**.


## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação de agente do portal Azure
Para verificar se o agente está a ser visto pelo Azure siga estes passos:

1. Inicie sessão no Portal do Azure.
2. À esquerda, selecione **Azure Ative Directory,** clique em **Azure AD Connect** e no centro selecione **Gerir o fornecimento (pré-visualização)**.</br>
![Portal do Azure](media/how-to-install/install6.png)</br>

3.  No ecrã **azure AD Provisioning (pré-visualização)** clique em **Rever todos os agentes**.
![Provisionamento de AD Azure](media/how-to-install/install7.png)</br>
 
4. No ecrã dos **agentes de provisionamento no local,** verá os agentes instalados.  Verifique se o agente em questão está lá e está marcado **ativo**.
![Agentes de provisionamento](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução siga estes passos:

1.  Inicie sessão no servidor com uma conta de administrador
2.  Serviços **Abertos** navegando até ele ou indo para Start/Run/Services.msc.
3.  No âmbito **dos Serviços,** certifique-se de que o **Microsoft Azure AD Connect Agent Updater** e o **Microsoft Azure AD Connect Provisioning Agent** estão presentes e o estado está em **execução**.
![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configure Azure AD Conectar o provisionamento de nuvem
 Utilize os seguintes passos para configurar o provisionamento

1.  Inscreva-se no portal Azure AD.
2.  Clique em **Diretório Ativo Azure**
3.  Clique em **Azure AD Connect**
4.  Selecione **gerir o fornecimento (Pré-visualização)**
![](media/how-to-configure/manage1.png)
5.  Clique em **Nova Configuração**
![](media/tutorial-single-forest/configure1.png)
7.  No ecrã de configuração, introduza um **e-mail de Notificação,** mova o seletor para **ativar** e clique em **Guardar**.
![](media/tutorial-single-forest/configure2.png)
1.  O estado de configuração deve agora ser **saudável.**
![](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifique se os utilizadores são criados e a sincronização está a ocorrer
Irá agora verificar se os utilizadores que teve no nosso diretório no local foram sincronizados e existem agora no nosso inquilino Azure AD.  Esteja ciente de que isto pode levar algumas horas para ser concluído.  Para verificar se os utilizadores estão sincronizados, faça o seguinte.


1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Ative Diretório**
3. Em **Gerir**, selecione **Utilizadores**.
4. Verifique se vê os novos utilizadores no nosso inquilino</br>
![Sincronia](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Teste de sessão com um dos nossos utilizadores

1. Navegue para[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inscreva-se numa conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sessão utilizando ouser@domain.onmicrosoft.comseguinte formato: ( ). Utilize a mesma palavra-passe que o utilizador usa para iniciar sessão no local.</br>
   ![Verificar](media/tutorial-single-forest/verify1.png)</br>

Criou agora com sucesso um ambiente de identidade híbrida que pode usar para testar e familiarizar-se com o que o Azure tem para oferecer.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
