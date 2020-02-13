---
title: Tutorial - Pilot Azure AD Connect cloud provisioning para uma floresta aD sincronizada existente
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
ms.openlocfilehash: f47f55252c6274360f200a4bce0ed0b739e3d8a3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166577"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Aprovisionamento na cloud piloto de uma floresta do AD sincronizada existente 

Este tutorial acompanha-o através da pilotagem do fornecimento de nuvem para um teste de floresta de Diretório Ativo que já está sincronizado usando o Azure Ative Directory (Azure AD) Connect sync.

![Criar](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Considerações
Antes de experimentar este tutorial, considere os seguintes itens:
1. Certifique-se de que está familiarizado com os fundamentos do fornecimento de nuvens. 
2. Certifique-se de que está a executar a versão 1.4.32.0 do Azure AD Connect e configurar as regras de sincronização conforme documentado. Ao pilotar, irá remover um ou grupo de teste ou grupo do âmbito de sincronização Azure AD Connect. Mover objetos fora do alcance leva à eliminação desses objetos em Azure D.C. No caso de objetos de utilizador, os objetos em AD Azure são eliminados suavemente e podem ser restaurados. No caso de objetos de grupo, os objetos em AD Azure são duramente eliminados e não podem ser restaurados. Foi introduzido um novo tipo de ligação no sincronizacro Azure AD Connect, o que impedirá a eliminação em caso de um cenário de pilotagem. 
3. Certifique-se de que os objetos no âmbito piloto têm ms-ds-consistênciaGUID povoados de modo a que o fornecimento de nuvens corresponda duramente aos objetos. 

   > [!NOTE]
   > O sincronizado Azure AD Connect não povoa *ms-ds-consistênciaGUID* por padrão para objetos de grupo. Siga os passos documentados [neste post](https://blogs.technet.microsoft.com/markrenoden/2017/10/13/choosing-a-sourceanchor-for-groups-in-multi-forest-sync-with-aad-connect/) de blog para povoar *ms-ds-consistênciaGUID* para objetos de grupo.

4. Este é um cenário avançado. Certifique-se de que segue os passos documentados neste tutorial com precisão.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos necessários para completar este tutorial
- Um ambiente de teste com a versão 1.4.32.0 ou posterior
- Um ou grupo que esteja no âmbito da sincronização e possa ser usado o piloto. Recomendamos começar com um pequeno conjunto de objetos.
- Um servidor que executa o Windows Server 2012 R2 ou posteriormente que irá alojar o agente de provisionamento.  Este não pode ser o mesmo servidor do servidor Azure AD Connect.
- A âncora de origem para a sincronização aAD Connect deve ser *objectGuid* ou *ms-ds-consistênciaGUID*

## <a name="update-azure-ad-connect"></a>Atualizar ligação Azure AD

No mínimo, deve ter [Azure AD ligado](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Para atualizar o sincronizado Azure AD Connect, complete os passos no [Azure AD Connect: Upgrade para a versão mais recente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Pare o programador
O sincronização do Azure AD Connect sincroniza as alterações que ocorrem no seu diretório no local utilizando um programador. Para modificar e adicionar regras personalizadas, pretende desativar o programador para que as sincronizações não sejam executadas enquanto estiver a trabalhar nisso.  Utilize os passos seguintes:

1.  No servidor que está a executar o Azure AD Connect sincroniza powerShell aberto com privilégios administrativos.
2.  Execute `Stop-ADSyncSyncCycle`.  Bata no Enter.
3.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Se estiver a executar o seu próprio programador personalizado para sincronização aAD Connect, desative o programador. 

## <a name="create-custom-user-inbound-rule"></a>Criar regra de entrada personalizada do utilizador

 1. Lance o editor de sincronização a partir do menu de aplicações no ambiente de trabalho, como mostrado abaixo:</br>
 ![Sincronização Do menu do editor](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. **Selecione Inbound** a partir da lista de "drop-down" para Direction e clique em **Adicionar nova regra**.
 ![](media/how-to-cloud-custom-user-rule/user1.png) de regras personalizadas</br>
 
 3. Na página **Descrição,** introduza o seguinte e clique **em Seguinte:**

    **Nome:** Dê à regra um nome significativo<br>
    **Descrição:** Adicione uma descrição significativa<br>
    **Sistema Conectado:** Escolha o conector AD para o qual está a escrever a regra de sincronização personalizada para<br>
    Tipo de objeto de **sistema conectado:** Utilizador<br>
    Tipo de **objeto metaverso:** Pessoa<br>
    **Tipo de ligação:** Junte-se<br>
    **Precedência:** Fornecer um valor único no sistema<br>
    **Etiqueta:** Deixe isto vazio.<br>
    ![](media/how-to-cloud-custom-user-rule/user2.png) de regras personalizadas</br>
 
 4. On the **Scoping filter** page, enter the OU or security group that you want the pilot based off.  Para filtrar em Ou, adicione a porção ou do nome distinto. Esta regra será aplicada a todos os utilizadores que estejam nessa U.  Assim, se o DN terminar com "OU=CPUsers,DC=contoso,DC=com, adicionaria este filtro.  Clique depois em **Seguinte**. 

    |Regra|Atributo|Operador|Valor|
    |-----|----|----|-----|
    |Scoping OU|DN|ENDSWITH|Distinto nome da U.|
    |Grupo de deteção||ISMEMBEROF|Nome distinto do grupo de segurança.|

    ![Regra personalizada](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Na página regras **De Juntar,** clique em **Seguinte**.
 6. Na página **Transformações,** adicione uma transformação constante: fluxo verdadeiro para cloudNoFlow atributo. Clique em **Adicionar**.
 ![](media/how-to-cloud-custom-user-rule/user4.png) de regras personalizadas</br>

Devem ser seguidos os mesmos passos para todos os tipos de objetos (utilizador, grupo e contacto). Repita os passos por Conector AD configurado / por floresta AD. 

## <a name="create-custom-user-outbound-rule"></a>Criar regra de saída personalizada do utilizador

 1. Selecione **Outbound** a partir da lista de "drop-down" para Direction e clique na **regra adicionar**.
 ![](media/how-to-cloud-custom-user-rule/user5.png) de regras personalizadas</br>
 
 2. Na página **Descrição,** introduza o seguinte e clique **em Seguinte:**

    **Nome:** Dê à regra um nome significativo<br>
    **Descrição:** Adicione uma descrição significativa<br>sistema **conectado  
    :** Escolha o conector AAD para o qual está a escrever a regra de sincronização personalizada para<br>
    Tipo de objeto de **sistema conectado:** Utilizador<br>
    Tipo de **objeto metaverso:** Pessoa<br>
    **Tipo de ligação:** JoinnoFlow<br>
    **Precedência:** Fornecer um valor único no sistema<br>
    **Etiqueta:** Deixe isto vazio.<br>
    
    ![Regra personalizada](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Na página do **filtro Scoping,** escolha **cloudNoFlow** igual **True**. Clique depois em **Seguinte**.
 ![](media/how-to-cloud-custom-user-rule/user7.png) de regras personalizadas</br>
 
 4. Na página regras **De Juntar,** clique em **Seguinte**.
 5. Na página **Transformações,** clique em **Adicionar**.

Devem ser seguidos os mesmos passos para todos os tipos de objetos (utilizador, grupo e contacto).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instale o agente de provisionamento Azure AD Connect
1. Inscreva-se no servidor que utilizará com permissões de administração da empresa.  Se estiver a usar o tutorial [de ambiente Basic AD e Azure,](tutorial-basic-ad-azure.md) seria CP1.
2. Descarregue aqui o [agente](https://go.microsoft.com/fwlink/?linkid=2109037)de fornecimento de nuvem Azure AD Connect .
3. Executar o fornecimento de nuvem Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. No ecrã de salpicos, **Aceite** os termos de licenciamento e clique em **Instalar**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install1.png)</br>

4. Assim que esta operação estiver concluída, o assistente de configuração será lançado.  Inscreva-se na sua conta de administrador global da AD Azure.
5. No ecrã **Connect Ative Diretório,** clique em **Adicionar diretório** e, em seguida, iniciar sessão com a sua conta de administrador de Diretório Ativo.  Esta operação irá adicionar o seu diretório no local.  Clique em **Seguinte**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install3.png)</br>

6. No ecrã completo da **Configuração,** clique em **Confirmar**.  Esta operação registar-se-á e reiniciará o agente.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install4.png)</br>

7. Assim que esta operação estiver concluída, deve ver um aviso **De que foi verificado com sucesso.**  Pode clicar em **Sair**.</br>
![Ecrã de Boas-Vindas](media/how-to-install/install5.png)</br>
8. Se ainda vir o ecrã de respingo inicial, clique em **Fechar**.

## <a name="verify-agent-installation"></a>Verificar a instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação de agente do portal Azure
Para verificar se o agente está a ser visto pelo Azure siga estes passos:

1. Inicie sessão no Portal do Azure.
2. À esquerda, selecione **Azure Ative Directory,** clique em **Azure AD Connect** e no centro selecione **Gerir o fornecimento (pré-visualização)** .</br>
![Portal do Azure](media/how-to-install/install6.png)</br>

3.  No ecrã **azure AD Provisioning (pré-visualização)** clique em **Rever todos os agentes**.
![](media/how-to-install/install7.png) de fornecimento de anúncios Azure</br>
 
4. No ecrã dos **agentes de provisionamento no local,** verá os agentes instalados.  Verifique se o agente em questão está lá e está marcado **para deficientes**.  O agente é desativado por padrão ![agentes de provisionamento](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução siga estes passos:

1.  Inicie sessão no servidor com uma conta de administrador
2.  Serviços **Abertos** navegando até ele ou indo para Start/Run/Services.msc.
3.  No âmbito **dos Serviços,** certifique-se de que o **Microsoft Azure AD Connect Agent Updater** e o **Microsoft Azure AD Connect Provisioning Agent** estão lá e o estado está a **funcionar**.
![Serviços](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configure Azure AD Conectar o provisionamento de nuvem
Utilize os seguintes passos para configurar o fornecimento:

 1. Inscreva-se no portal Azure AD.
 2. Clique em **Diretório Ativo Azure**
 3. Clique em **Azure AD Connect**
 4. Selecione **Gerir o fornecimento (Pré-visualização)** 
 ![](media/how-to-configure/manage1.png)</br>
 5.  Clique em **nova configuração**
 ![](media/tutorial-single-forest/configure1.png)</br>
 6.  No ecrã de configuração, introduza um **e-mail de Notificação,** mova o seletor para **ativar** e clique em **Guardar**.
 ![](media/tutorial-single-forest/configure2.png)</br>
 7. Em **Configurar, selecione** **Todos os utilizadores** para alterar o âmbito da regra de configuração.
 ![](media/how-to-configure/scope2.png)</br>
 8. À direita, altere o âmbito para incluir o OU específico que acabou de criar "OU=CPUsers,DC=contoso,DC=com".
 ![](media/tutorial-existing-forest/scope2.png)</br>
 9.  Clique em **Feito** e **Salvar**.
 10. O âmbito deve agora ser definido para uma unidade organizacional. 
 ![](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Verifique se os utilizadores são aprovisionados por fornecimento de nuvem
Irá agora verificar se os utilizadores que tinha no nosso diretório no local foram sincronizados e existem agora no inquilino da AD Azure.  Esteja ciente de que isto pode levar algumas horas para ser concluído.  Para verificar se os utilizadores estão a fornecer através do fornecimento de nuvem, siga estas etapas:

1. Navegue no [portal Azure](https://portal.azure.com) e inscreva-se com uma conta que tenha uma subscrição Azure.
2. À esquerda, selecione **Azure Ative Diretório**
3. Clique em **Azure AD Connect**
4. Clique em **Gerir o provisionamento (pré-visualização)**
5. Clique no botão **Logs**
6. Procure um nome de utilizador para confirmar que o utilizador é aprovisionado por fornecimento de nuvem

Além disso, pode verificar se o utilizador e o grupo existem em Azure AD.

## <a name="start-the-scheduler"></a>Inicie o programador
O sincronização do Azure AD Connect sincroniza as alterações que ocorrem no seu diretório no local utilizando um programador. Agora que modificou as regras, pode repor o programador.  Utilize os passos seguintes:

1.  No servidor que está a executar o Azure AD Connect sincronizar powerShell aberto com privilégios administrativos
2.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Execute `Start-ADSyncSyncCycle`.  Bata no Enter.  

>[!NOTE] 
>Se estiver a executar o seu próprio programador personalizado para sincronização aAD Connect, por favor, ative o programador. 

## <a name="something-went-wrong"></a>Alguma coisa correu mal.
Caso o piloto não funcione como esperado, pode voltar à configuração da sincronização Azure AD Connect seguindo os passos abaixo:
1.  Desative a configuração de provisionamento no portal Azure. 
2.  Desative todas as regras de sincronização personalizadas criadas para o Fornecimento de Cloud utilizando a ferramenta Sync Rule Editor. A desativação deve causar uma sincronização total em todos os conectores.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Configure Azure AD Connect sincronização para excluir o piloto ou
Uma vez verificado que os utilizadores da Ou piloto são geridos com sucesso através do fornecimento de nuvem, pode reconfigurar o Azure AD Connect para excluir o ou piloto que foi criado acima.  O agente de fornecimento de nuvem irá lidar com a sincronização para estes utilizadores.  Utilize os seguintes passos para o alcance do Azure AD Connect.

 1. No servidor que está a executar o Azure AD Connect, clique duas vezes no ícone Azure AD Connect.
 2. Clique em **Configurar**
 3. **Selecione Personalizar opções** de sincronização e clicar em seguida.
 4. Inscreva-se no Azure AD e clique em **Next**.
 5. No **ecrã Connect your directdirecties** clique **em Seguinte**.
 6. No ecrã de **filtragem Domain e U,** selecione **Domínios selecionados**sync e OUs .
 7. Expanda o seu domínio e **desselecione** os **CPUsers** OU.  Clique em **Seguinte**.
](media/tutorial-existing-forest/scope1.png) de âmbito ![</br>
 9. No ecrã **de funcionalidades Opcionais,** clique em **Next**.
 10. No **pronto para configurar o** ecrã clique **em Configurar**.
 11. Uma vez concluído, clique em **Saída**. 

## <a name="next-steps"></a>Passos seguintes 

- [O que é o provisionamento?](what-is-provisioning.md)
- [O que é o fornecimento de nuvem Azure AD Connect?](what-is-cloud-provisioning.md)

