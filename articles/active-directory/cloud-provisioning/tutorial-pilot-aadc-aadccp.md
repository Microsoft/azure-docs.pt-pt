---
title: Tutorial-Azure AD Connect o provisionamento de nuvem de um piloto para uma floresta do AD sincronizada existente
description: destina.
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
ms.openlocfilehash: 812f9bc71cde26b6f32a1259984bb0859ba49d54
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868767"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Provisionamento de nuvem piloto para uma floresta do AD sincronizada existente 

Este tutorial orienta você pelo provisionamento de nuvem piloto para um teste Active Directory floresta já sincronizada usando a sincronização do Azure Active Directory (Azure AD) Connect.

![Create](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Considerações
Antes de tentar este tutorial, considere os seguintes itens:
1. Verifique se você está familiarizado com noções básicas do provisionamento de nuvem. 
2. Verifique se você está executando Azure AD Connect a versão de sincronização 1.4.32.0 ou posterior e configurou as regras de sincronização conforme documentado. Ao executar o piloto, você removerá uma UO ou grupo de teste de Azure AD Connect escopo de sincronização. Mover objetos para fora do escopo leva à exclusão desses objetos no Azure AD. No caso de objetos de usuário, os objetos no Azure AD são excluídos de forma reversível e podem ser restaurados. No caso de objetos de grupo, os objetos no Azure AD são excluídos e não podem ser restaurados. Um novo tipo de link foi introduzido na sincronização Azure AD Connect, o que impedirá a exclusão no caso de um cenário piloto. 
3. Verifique se os objetos no escopo do piloto têm o MS-DS-consistencyGUID populado para que o provisionamento de nuvem corresponda aos objetos. 

   > [!NOTE]
   > A sincronização de Azure AD Connect não preenche *MS-DS-consistencyGUID* por padrão para objetos de grupo. Siga as etapas documentadas nesta [postagem de blog](https://blogs.technet.microsoft.com/markrenoden/2017/10/13/choosing-a-sourceanchor-for-groups-in-multi-forest-sync-with-aad-connect/) para preencher o *MS-DS-consistencyGUID* para objetos de grupo.

4. Este é um cenário avançado. Certifique-se de seguir as etapas documentadas neste tutorial com precisão.

## <a name="prerequisites"></a>Pré-requisitos
Veja a seguir os pré-requisitos necessários para concluir este tutorial
- Um ambiente de teste com Azure AD Connect versão de sincronização 1.4.32.0 ou posterior
- Uma UO ou grupo que está no escopo da sincronização e pode ser usado pelo piloto. É recomendável começar com um pequeno conjunto de objetos.
- Um servidor que executa o Windows Server 2012 R2 ou posterior que irá hospedar o agente de provisionamento.  Este não pode ser o mesmo servidor que o servidor de Azure AD Connect.
- A âncora de origem para a sincronização do AAD Connect deve ser *objectGUID* ou *MS-DS-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Atualizar Azure AD Connect

No mínimo, você deve ter o [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Para atualizar Azure AD Connect sincronização, conclua as etapas em [Azure ad Connect: atualizar para a versão mais recente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Parar o Agendador
Azure AD Connect sincronização sincroniza as alterações que ocorrem em seu diretório local usando um Agendador. Para modificar e adicionar regras personalizadas, você deseja desabilitar o Agendador para que as sincronizações não sejam executadas enquanto você estiver trabalhando nele.  Utilize os passos seguintes:

1.  No servidor que está executando Azure AD Connect sincronização, abra o PowerShell com privilégios administrativos.
2.  Execute `Stop-ADSyncSyncCycle`.  Pressione Enter.
3.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Se você estiver executando seu próprio Agendador personalizado para a sincronização do AAD Connect, desabilite o Agendador. 

## <a name="create-custom-user-inbound-rule"></a>Criar regra de entrada de usuário personalizada

 1. Inicie o editor de sincronização no menu do aplicativo na área de trabalho, conforme mostrado abaixo:</br>
 ![menu do editor de regras de sincronização](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Selecione **entrada** na lista suspensa para direção e clique em **Adicionar nova regra**.
 ![regra personalizada](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Na página **Descrição** , insira o seguinte e clique em **Avançar**:

    **Nome:** Dê um nome significativo à regra<br>
    **Descrição:** Adicionar uma descrição significativa<br> 
    **sistema conectado:** escolha o conector do AD para o qual você está gravando a regra de sincronização personalizada<br>
    **Tipo de objeto do sistema conectado:** Usuário<br>
    **Tipo de objeto do metaverso:** Pessoa<br>
    **Tipo de link:** Em<br>
    **Precedência:** Forneça um valor que seja exclusivo no sistema<br>
    **Marca:** Deixar este vazio<br>
    ![regra personalizada](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Na página **filtro de escopo** , insira a UO ou o grupo de segurança que você deseja que o piloto se baseie.  Para filtrar na UO, adicione a parte da UO do nome distinto. Essa regra será aplicada a todos os usuários que estiverem nessa UO.  Portanto, se DN terminar com "OU = CPUs, DC = contoso, DC = com, você adicionará esse filtro.  Clique depois em **Seguinte**. 

    |Regra|Atributo|Operador|Valor|
    |-----|----|----|-----|
    |UO de escopo|DN|ENDSWITH|Nome diferenciado da UO.|
    |Grupo de escopo||ISMEMBEROF|Nome distinto do grupo de segurança.|

    ![Regra personalizada](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Na página regras de **junção** , clique em **Avançar**.
 6. Na página **transformações** , adicione uma transformação constante: Flow true para o atributo cloudNoFlow. Clique em **Adicionar**.
 ![regra personalizada](media/how-to-cloud-custom-user-rule/user4.png)</br>

As mesmas etapas precisam ser seguidas para todos os tipos de objeto (usuário, grupo e contato). Repita as etapas por AD Connector configurado/por floresta do AD. 

## <a name="create-custom-user-outbound-rule"></a>Criar regra de saída de usuário personalizada

 1. Selecione **saída** na lista suspensa para direção e clique em **Adicionar regra**.
 ![regra personalizada](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Na página **Descrição** , insira o seguinte e clique em **Avançar**:

    **Nome:** Dê um nome significativo à regra<br>
    **Descrição:** Adicionar uma descrição significativa<br> 
    **sistema conectado:** escolha o conector do AAD para o qual você está gravando a regra de sincronização personalizada<br>
    **Tipo de objeto do sistema conectado:** Usuário<br>
    **Tipo de objeto do metaverso:** Pessoa<br>
    **Tipo de link:** JoinNoFlow<br>
    **Precedência:** Forneça um valor que seja exclusivo no sistema<br>
    **Marca:** Deixar este vazio<br>
    
    ![Regra personalizada](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Na página **filtro de escopo** , escolha **cloudNoFlow** igual **true**. Clique depois em **Seguinte**.
 ![regra personalizada](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Na página regras de **junção** , clique em **Avançar**.
 5. Na página **transformações** , clique em **Adicionar**.

As mesmas etapas precisam ser seguidas para todos os tipos de objeto (usuário, grupo e contato).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instalar o agente de provisionamento do Azure AD Connect
1. Entre no servidor que será usado com as permissões de administrador corporativo.  Se você estiver usando o tutorial [básico do ambiente do AD e do Azure](tutorial-basic-ad-azure.md) , seria CP1.
2. Baixe o agente de provisionamento de nuvem Azure AD Connect [aqui](https://go.microsoft.com/fwlink/?linkid=2109037).
3. Executar o provisionamento Azure AD Connect Cloud (AADConnectProvisioningAgent. Installer)
3. Na tela inicial, **aceite** os termos de licenciamento e clique em **instalar**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install1.png)</br>

4. Quando essa operação for concluída, o assistente de configuração será iniciado.  Entre com sua conta de administrador global do Azure AD.
5. Na tela **conectar Active Directory** , clique em **Adicionar diretório** e entre com sua conta de administrador do Active Directory.  Esta operação adicionará seu diretório local.  Clique em **Seguinte**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install3.png)</br>

6. Na tela **configuração concluída** , clique em **confirmar**.  Esta operação registrará e reiniciará o agente.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install4.png)</br>

7. Após a conclusão dessa operação, você deverá ver um aviso **de que seu foi verificado com êxito.**  Você pode clicar em **sair**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install5.png)</br>
8. Se você ainda vir a tela de abertura inicial, clique em **fechar**. 1. Entre no servidor que será usado com as permissões de administrador corporativo.
2. Baixe o agente de provisionamento de nuvem Azure AD Connect [aqui](https://go.microsoft.com/fwlink/?linkid=2109037).
3. Executar o provisionamento Azure AD Connect Cloud (AADConnectProvisioningAgent. Installer)
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
 
4. Na **tela agentes de provisionamento locais** , você verá os agentes que você instalou.  Verifique se o agente em questão está selecionado e se está marcado como **desabilitado**.  O agente é desabilitado por padrão ![agentes de provisionamento](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estas etapas:

1.  Faça logon no servidor com uma conta de administrador
2.  Abra os **Serviços** navegando até ele ou acessando iniciar/executar/Services. msc.
3.  Em **Serviços** , verifique se **Microsoft Azure ad atualizador do agente** e o **agente de provisionamento do Microsoft Azure ad Connect** estão lá e se o status está **em execução**.
![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configurar Azure AD Connect provisionamento de nuvem
Use as seguintes etapas para configurar o provisionamento:

 1. Entre no portal do AD do Azure.
 2. Clique em **Azure Active Directory**
 3. Clique em **Azure ad Connect**
 4. Selecione **gerenciar provisionamento (visualização)** 
 ![](media/how-to-configure/manage1.png)</br>
 5.  Clique em **nova configuração**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  Na tela configuração, insira um **email de notificação**, mova o seletor para **habilitar** e clique em **salvar**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. Em **Configurar**, selecione **todos os usuários** para alterar o escopo da regra de configuração.
 ![](media/how-to-configure/scope2.png)</br>
 8. À direita, altere o escopo para incluir a UO específica que você acabou de criar "OU = CPUs, DC = contoso, DC = com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Clique em **concluído** e em **salvar**.
 10. O escopo agora deve ser definido como uma unidade organizacional. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Verificar se os usuários são provisionados pelo provisionamento de nuvem
Agora, você verificará se os usuários que você tinha em nosso diretório local foram sincronizados e agora existem no locatário do Azure AD.  Lembre-se de que isso pode levar algumas horas para ser concluído.  Para verificar se os usuários estão Provisionando por provisionamento de nuvem, siga estas etapas:

1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Active Directory**
3. Clique em **Azure ad Connect**
4. Clique em **gerenciar provisionamento (versão prévia)**
5. Botão clicar no **log**
6. Procurar um nome de usuário para confirmar se o usuário foi provisionado pelo provisionamento de nuvem

Além disso, você pode verificar se o usuário e o grupo existem no Azure AD.

## <a name="start-the-scheduler"></a>Iniciar o Agendador
Azure AD Connect sincronização sincroniza as alterações que ocorrem em seu diretório local usando um Agendador. Agora que você modificou as regras, você pode reiniciar o Agendador.  Utilize os passos seguintes:

1.  No servidor que está executando o Azure AD Connect sincronizar abrir o PowerShell com privilégios administrativos
2.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Execute `Start-ADSyncSyncCycle`.  Pressione Enter.  

>[!NOTE] 
>Se você estiver executando seu próprio Agendador personalizado para a sincronização do AAD Connect, habilite o Agendador. 

## <a name="something-went-wrong"></a>Algo correu mal
Caso o piloto não funcione conforme o esperado, você poderá voltar para a configuração de sincronização do Azure AD Connect seguindo as etapas abaixo:
1.  Desabilite a configuração de provisionamento no portal do Azure. 
2.  Desabilite todas as regras de sincronização personalizadas criadas para provisionamento de nuvem usando a ferramenta sincronizar editor de regra. A desabilitação deve causar sincronização completa em todos os conectores.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Configurar a sincronização de Azure AD Connect para excluir a UO piloto
Depois de verificar se os usuários da UO piloto são gerenciados com êxito pelo provisionamento de nuvem, você pode reconfigurar o Azure AD Connect para excluir a UO piloto que foi criada acima.  O agente de provisionamento de nuvem tratará a sincronização para esses usuários no futuro.  Use as etapas a seguir para Azure AD Connect de escopo.

 1. No servidor que está executando o Azure AD Connect, clique duas vezes no ícone de Azure AD Connect.
 2. Clique em **Configurar**
 3. Selecione **Personalizar opções de sincronização** e clique em Avançar.
 4. Entre no Azure AD e clique em **Avançar**.
 5. Na tela **conectar seus diretórios** , clique em **Avançar**.
 6. Na tela **filtragem de domínio e UO** , selecione **sincronizar domínios e UOs selecionados**.
 7. Expanda seu domínio e **desmarque** a UO de **CPUs** .  Clique em **Seguinte**.
![scope](media/tutorial-existing-forest/scope1.png)</br>
 9. Na tela **recursos opcionais** , clique em **Avançar**.
 10. Na tela **pronto para configurar** , clique em **Configurar**.
 11. Após a conclusão, clique em **sair**. 

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)

