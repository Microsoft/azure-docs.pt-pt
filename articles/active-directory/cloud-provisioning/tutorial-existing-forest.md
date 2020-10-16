---
title: Tutorial - Integre uma floresta existente e uma nova floresta com um único inquilino AZure AD usando o fornecimento de nuvem Azure AD Connect.
description: Saiba como adicionar o fornecimento de nuvens a um ambiente de identidade híbrida existente. .
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 803663f845272c6b1573fceed8025cc23483ec09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628745"
---
# <a name="integrate-an-existing-forest-and-a-new-forest-with-a-single-azure-ad-tenant"></a>Integrar uma floresta existente e uma nova floresta num único inquilino do Azure AD

Este tutorial acompanha-o através da adição de provisão de nuvens a um ambiente de identidade híbrida existente. 

![Criar](media/tutorial-existing-forest/existing-forest-new-forest.png)

Você pode usar o ambiente que você cria neste tutorial para testar ou para se familiarizar mais com o funcionamento de uma identidade híbrida. 

Neste cenário, existe uma floresta existente sincronizada utilizando a sincronização Azure AD Connect a um inquilino AD AZure. E você tem uma nova floresta que quer sincronizar com o mesmo inquilino da AD AZure. Vai configurar o fornecimento de nuvens para a nova floresta. 

## <a name="prerequisites"></a>Pré-requisitos
### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração Azure Ative Directory

1. Crie uma conta de administrador global apenas em nuvem no seu inquilino AZure AD. Desta forma, pode gerir a configuração do seu inquilino caso os seus serviços no local falhem ou fiquem indisponíveis. Saiba mais [sobre a adição de uma conta de administrador global apenas na nuvem.](../fundamentals/add-users-azure-active-directory.md) Completar este passo é fundamental para garantir que não fique trancado fora do seu inquilino.
2. Adicione um ou mais [nomes de domínio personalizados](../fundamentals/add-custom-domain.md) ao seu inquilino AZure AD. Os seus utilizadores podem iniciar sôms com um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor anfitrião ligado a domínio que executa o Windows Server 2012 R2 ou superior com um mínimo de 4 GB de RAM e .NET 4.7.1+ tempo de execução 

2. Se houver uma firewall entre os seus servidores e a Azure AD, configure os seguintes itens:
   - Certifique-se de que os agentes podem fazer pedidos *de saída* à Azure AD nas seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Descarrega as listas de revogação de certificados (CRLs) ao mesmo tempo que valida o certificado TLS/SSL |
     | **443** | Lida com toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os agentes reportam o seu estado a cada 10 minutos sobre o porto 8080, se a porta 443 não estiver disponível. Este estado é apresentado no portal AD Azure. |
     
     Se o seu firewall aplicar as regras de acordo com os utilizadores originários, abra estas portas para tráfego a partir de serviços Windows que funcionam como um serviço de rede.
   - Se a sua firewall ou procuração permitir que especifique sufixos seguros, adicione ligações a ** \* .msappproxy.net** e ** \* .servicebus.windows.net**. Caso contrário, permita o acesso aos intervalos IP do [datacenter Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Os seus agentes precisam de acesso a **login.windows.net** e **login.microsoftonline.com** para o registo inicial. Abra a firewall para os URLs também.
   - Para validação de certificados, desbloqueie os seguintes URLs: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**e **www \. microsoft.com:80**. Uma vez que estes URLs são utilizados para validação de certificados com outros produtos da Microsoft, pode já ter estes URLs desbloqueados.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instale o agente de provisionamento Azure AD Connect
1. Inscreva-se no servidor de união de domínio.  Se estiver a usar o tutorial  [básico de AD e ambiente Azure,](tutorial-basic-ad-azure.md) seria DC1.
2. Inscreva-se no portal Azure usando credenciais de administração globais apenas em nuvem.
3. À esquerda, selecione **Azure Ative Directory**, clique em **Azure AD Connect** e no center select **Manage provisioning (pré-visualização)**.</br>
![Portal do Azure](media/how-to-install/install6.png)</br>
4. Clique em "Transferir agente"
5. Executar o agente de provisionamento Azure AD Connect
6. No ecrã de respingo, **aceite** os termos de licenciamento e clique em **Instalar**.</br>
![Screenshot que mostra o ecrã de splash "Microsoft Azure AD Connect Provisioning Agent Package".](media/how-to-install/install1.png)</br>

7. Uma vez concluída esta operação, o assistente de configuração será lançado.  Inscreva-se na sua conta de administrador global Azure AD.  Note que se tiver segurança reforçada iE ativada, isto bloqueará a inscrição.  Se for esse o caso, feche a instalação, desative a segurança reforçada do IE no Server Manager e clique no Assistente de **Provisionamento de Ligação AAD** para reiniciar a instalação.
8. No ecrã **do Diretório Ativo Connect,** clique em **Adicionar diretório** e, em seguida, inscreva-se na sua conta de administrador de domínio ative Directy.  NOTA: A conta do administrador de domínio não deve ter requisitos de alteração de palavra-passe. Caso a palavra-passe expire ou se altera, terá de voltar a configurar o agente com as novas credenciais. Esta operação irá adicionar o seu diretório no local.  Clique em **Seguinte**.</br>
![Screenshot que mostra o ecrã "Connect Ative Directory".](media/how-to-install/install3.png)</br>

9. No ecrã completo da **Configuração,** clique em **Confirmar**.  Esta operação registará e reiniciará o agente.</br>
![Screenshot que mostra o ecrã "Configuração completa".](media/how-to-install/install4.png)</br>

10. Uma vez concluída esta operação, deverá ver um aviso: **A configuração do seu agente foi verificada com sucesso.**  Pode clicar em **Sair.**</br>
![Ecrã de Boas-Vindas](media/how-to-install/install5.png)</br>
11. Se ainda vir o ecrã inicial de respingo, clique em **Fechar**.


## <a name="verify-agent-installation"></a>Verificar instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente do portal Azure
Para verificar se o agente está a ser visto pela Azure siga estes passos:

1. Inicie sessão no Portal do Azure.
2. À esquerda, selecione **Azure Ative Directory**, clique em **Azure AD Connect** e no center select **Manage provisioning (pré-visualização)**.</br>
![Portal do Azure](media/how-to-install/install6.png)</br>

3.  No ecrã **Azure AD Provisioning (pré-visualização)** clique em **Rever todos os agentes**.
![Azure AD Provisioning](media/how-to-install/install7.png)</br>
 
4. No **ecrã de agentes de provisionamento** no local, verá os agentes instalados.  Verifique se o agente em questão está lá e está marcado **ativo**.
![Agentes de provisionamento](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estes passos:

1.  Inicie sessão no servidor com uma conta de administrador
2.  **Serviços abertos** navegando nele ou indo para Iniciar/Executar/Serviços.msc.
3.  Em **Serviços**, certifique-se de que **o Microsoft Azure AD Connect Agent Updater** e o Microsoft **Azure AD Connect Provisioning Agent** estão presentes e o estado está a **funcionar**.
![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configurar a azure AD Connect provisões de nuvem
 Utilize os seguintes passos para configurar o provisionamento

1.  Inscreva-se no portal AD AZure.
2.  Clique **em Azure Ative Directory**
3.  Clique **em Azure Ad Connect**
4.  **Selecione Gerir o fornecimento (Pré-visualização)** Screenshot 
 ![ mostrando o link "Gerir o provisionamento (pré-visualização)".](media/how-to-configure/manage1.png)
5.  Clique em Novo Screenshot de **Configuração** 
 ![ do ecrã Azure AD Provisioning (Preview) com o link "Nova configuração" realçado.](media/tutorial-single-forest/configure1.png)
7.  No ecrã de configuração, introduza um **e-mail de Notificação,** mova o seletor para **Ativar** e clique em **Guardar**.
![Screenshot do ecrã configurar com e-mail de notificação preenchido e Ativar selecionado.](media/how-to-configure/configure2.png)
1.  O estado de configuração deve agora ser **saudável**.
![Screenshot do ecrã Azure AD Provisioning (Preview) mostrando o estado saudável.](media/how-to-configure/manage4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifique se os utilizadores são criados e a sincronização está a ocorrer
Irá agora verificar se os utilizadores que tinha no nosso diretório no local foram sincronizados e agora existem no nosso inquilino AZure AD.  Esteja ciente de que isto pode levar algumas horas para ser concluído.  Para verificar se os utilizadores estão sincronizados, faça o seguinte.


1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Ative Directory**
3. Em **Gerir**, selecione **Utilizadores**.
4. Verifique se vê os novos utilizadores no nosso inquilino</br>
![Sincronização](media/tutorial-single-forest/synchronize1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Iniciar sessão de teste com um dos nossos utilizadores

1. Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inscreva-se com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sção utilizando o seguinte formato: user@domain.onmicrosoft.com (). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sins insição no local.</br>
   ![Verificação](media/tutorial-single-forest/verify1.png)</br>

Criou agora com sucesso um ambiente de identidade híbrida que pode usar para testar e familiarizar-se com o que o Azure tem para oferecer.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)