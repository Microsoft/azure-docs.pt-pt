---
title: Tutorial - Pilot Azure AD Connect cloud provisioning for a existing a ad forest
description: Saiba como pilotar o provisionamento em nuvem para um teste ative directory forest que já está sincronizado usando a sincronização do Azure Ative Directory (Azure AD).
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b510464145f434f761cc28ba4e1c1361f983f8b
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237396"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Aprovisionamento na cloud piloto de uma floresta do AD sincronizada existente 

Este tutorial acompanha-o através da pilotagem de provisões em nuvem para um teste Ative Directory forest que já está sincronizado usando a sincronização do Azure Ative Directory (Azure AD).

![Criar](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Considerações
Antes de experimentar este tutorial, considere os seguintes itens:
1. Certifique-se de que está familiarizado com o básico do fornecimento de nuvens. 
2. Certifique-se de que está a executar a versão sincronizada Azure AD Connect versão 1.4.32.0 ou posterior e que tenha configurado as regras de sincronização conforme documentado. Ao pilotar, irá remover um teste ou grupo do âmbito de sincronização Azure AD Connect. Mover objetos fora de alcance leva à eliminação desses objetos em Azure AD. No caso de objetos do utilizador, os objetos em Azure AD são apagados suavemente e podem ser restaurados. No caso de objetos de grupo, os objetos em Azure AD são duramente eliminados e não podem ser restaurados. Foi introduzido um novo tipo de ligação na sincronização Azure AD Connect, que impedirá a eliminação em caso de cenário de pilotagem. 
3. Certifique-se de que os objetos no âmbito piloto têm ms-ds-consistênciaGUID povoado de modo que o fornecimento de nuvens combina com os objetos. 

   > [!NOTE]
   > A azure AD Connect sync não preencha *ms-ds-consistênciaGUID* por padrão para objetos de grupo.

4. Este é um cenário avançado. Certifique-se de que segue os passos documentados neste tutorial com precisão.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos necessários para completar este tutorial
- Um ambiente de teste com Azure AD Connect sync versão 1.4.32.0 ou mais tarde
- Um UO ou grupo que esteja no âmbito de sincronização e possa ser utilizado o piloto. Recomendamos começar com um pequeno conjunto de objetos.
- Um servidor que executa o Windows Server 2012 R2 ou mais tarde que irá hospedar o agente de provisionamento.  Este não pode ser o mesmo servidor que o servidor Azure AD Connect.
- Âncora de origem para a sincronização AZure AD Connect deve ser *objectGuid* ou *ms-ds-consistênciaGUID*

## <a name="update-azure-ad-connect"></a>Atualização Azure AD Connect

No mínimo, deverá ter [a Azure AD a ligar](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Para atualizar a sincronização Azure AD Connect, complete os passos em [Azure AD Connect: Upgrade para a versão mais recente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Pare o agendador
A azure AD Connect sincroniza as alterações ocorridas no seu diretório no local utilizando um programador. Para modificar e adicionar regras personalizadas, pretende desativar o programador para que as sincronizações não sejam executadas enquanto estiver a trabalhar nisto.  Utilize os passos seguintes:

1.  No servidor que está a executar Azure AD Connect sincronizar PowerShell com Privilégios Administrativos.
2.  Execute `Stop-ADSyncSyncCycle`.  Acerte entrar.
3.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Se estiver a executar o seu próprio programador personalizado para sincronização Azure AD Connect, desative o programador. 

## <a name="create-custom-user-inbound-rule"></a>Criar regra de entrada de utilizador personalizado

 1. Lance o editor de sincronização a partir do menu de aplicações no ambiente de trabalho, como mostrado abaixo:</br>
 ![Menu de editor de regras de sincronização](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. **Selecione 'Entrada'** na lista de espera para Direction e clique em **Adicionar nova regra**.
 ![Screenshot que mostra a janela "Ver e gerir as suas regras de sincronização" com "Entrada" e o botão "Adicionar nova regra" selecionado.](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Na página **Descrição,** insira o seguinte e clique em **Seguinte**:

    **Nome:** Dê à regra um nome significativo<br>
    **Descrição:** Adicione uma descrição significativa<br>
    **Sistema conectado:** Escolha o conector AD para o qual está a escrever a regra de sincronização personalizada para<br>
    **Tipo de objeto de sistema ligado:** Utilizador<br>
    **Tipo de objeto metaverso:** Pessoa<br>
    **Tipo de ligação:** Junte-se<br>
    **Precedência:** Fornecer um valor único no sistema<br>
    **Etiqueta:** Deixe isto vazio.<br>
    ![Screenshot que mostra a página "Criar regra de sincronização de entrada - Descrição" com valores introduzidos.](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Na página do **filtro Descodi,** insira o ou ou o grupo de segurança que deseja que o piloto seja baseado.  Para filtrar em OU, adicione a parte ou do nome distinto. Esta regra será aplicada a todos os utilizadores que se encontrem nessa U.  Assim, se o DN terminar com "OU=CPUsers,DC=contoso,DC=com, adicionaria este filtro.  Em seguida, clique em **Seguinte**. 

    |Regra|Atributo|Operador|Valor|
    |-----|----|----|-----|
    |Scoping OU|DN|ENDSWITH|Nome distinto da U.|
    |Grupo de scoping||ISMEMBEROF|Nome distinto do grupo de segurança.|

    ![Screenshot que mostra a página "Criar regra de sincronização de entrada - filtro de scoping" com um valor de filtro de deteção introduzido.](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Na página **'Unir** regras', clique em **Seguinte**.
 6. Na página **Transformações,** adicione uma transformação constante: fluir Fiel ao atributo CloudNoFlow. Clique em **Adicionar**.
 ![Screenshot que mostra a página "Criar regra de sincronização de entrada - Transformações" com um fluxo de "transformação constante" adicionado.](media/how-to-cloud-custom-user-rule/user4.png)</br>

Devem ser seguidos os mesmos passos para todos os tipos de objetos (utilizador, grupo e contacto). Repita os passos por conector AD configurado /por floresta AD. 

## <a name="create-custom-user-outbound-rule"></a>Criar regra de saída personalizada do utilizador

 1. Selecione **Outbound** da lista de drop-down para Direction e clique na **regra Adicionar**.
 ![Screenshot que mostra a Direção "Outbound" selecionada e o botão "Adicionar nova regra" realçado.](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Na página **Descrição,** insira o seguinte e clique em **Seguinte**:

    **Nome:** Dê à regra um nome significativo<br>
    **Descrição:** Adicione uma descrição significativa<br>
    **Sistema conectado:** Escolha o conector AD Azure para o qual está a escrever a regra de sincronização personalizada para<br>
    **Tipo de objeto de sistema ligado:** Utilizador<br>
    **Tipo de objeto metaverso:** Pessoa<br>
    **Tipo de ligação:** JoinNoFlow<br>
    **Precedência:** Fornecer um valor único no sistema<br>
    **Etiqueta:** Deixe isto vazio.<br>
    
    ![Screenshot que mostra a página "Descrição" com propriedades inseridas.](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Na página do **filtro de scoping,** escolha **cloudNoFlow** igual **True**. Em seguida, clique em **Seguinte**.
 ![Regra personalizada](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Na página **'Unir** regras', clique em **Seguinte**.
 5. Na página **Transformações,** clique em **Adicionar**.

Devem ser seguidos os mesmos passos para todos os tipos de objetos (utilizador, grupo e contacto).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Instale o agente de provisionamento Azure AD Connect
1. Faça o s-sete no servidor que utilizará com permissões de administração da empresa.  Se estiver a utilizar o tutorial  [básico de AD e ambiente Azure,](tutorial-basic-ad-azure.md) seria CP1.
2. Descarregue o agente de provisionamento de nuvem Azure AD Connect utilizando os passos [descritos aqui.](how-to-install.md#install-the-agent)
3. Executar o provisionamento em nuvem Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. No ecrã de respingo, **aceite** os termos de licenciamento e clique em **Instalar**.</br>
![Screenshot que mostra o ecrã de respingo "Microsoft Azure A D Connect Provisioning Agent".](media/how-to-install/install-1.png)</br>

4. Uma vez concluída esta operação, o assistente de configuração será lançado.  Inscreva-se na sua conta de administrador global Azure AD.
5. No ecrã **do Diretório Ativo Connect,** clique em **Adicionar diretório** e, em seguida, inscreva-se na sua conta de administrador Ative Directory.  Esta operação irá adicionar o seu diretório no local.  Clique em **Seguinte**.</br>
![Screenshot que mostra o ecrã "Connect Ative Directory" com um valor de diretório introduzido.](media/how-to-install/install-3a.png)</br>

6. No ecrã completo da **Configuração,** clique em **Confirmar**.  Esta operação registará e reiniciará o agente.</br>
![Screenshot que mostra o ecrã "Configuração completa" com o botão "Confirmar" selecionado.](media/how-to-install/install-4a.png)</br>

7. Uma vez concluída esta operação, deverá ver um aviso **de que foi verificado com sucesso.**  Pode clicar em **Sair.**</br>
![Ecrã de Boas-Vindas](media/how-to-install/install-5.png)</br>
8. Se ainda vir o ecrã inicial de respingo, clique em **Fechar**.

## <a name="verify-agent-installation"></a>Verificar instalação do agente
A verificação do agente ocorre no portal Azure e no servidor local que está a executar o agente.

### <a name="azure-portal-agent-verification"></a>Verificação do agente do portal Azure
Para verificar se o agente está a ser visto pela Azure siga estes passos:

1. Inicie sessão no portal do Azure.
2. À esquerda, selecione **Azure Ative Directory**, clique em **Azure AD Connect** e no center select **Manage provisioning (pré-visualização)**.</br>
![Portal do Azure](media/how-to-install/install-6.png)</br>

3.  No ecrã **Azure AD Provisioning (pré-visualização)** clique em **Rever todos os agentes**.
![Azure AD Provisioning](media/how-to-install/install-7.png)</br>
 
4. No **ecrã de agentes de provisionamento** no local, verá os agentes instalados.  Verifique se o agente em questão está lá e está marcado **como Desativado**.  O agente é desativado por ![ agentes de provisionamento predefinidos](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>No servidor local
Para verificar se o agente está em execução, siga estes passos:

1.  Inicie sessão no servidor com uma conta de administrador
2.  **Serviços abertos** navegando nele ou indo para Iniciar/Executar/Serviços.msc.
3.  Em **Serviços** certifique-se de que **o Microsoft Azure AD Connect Agent Updater** e o Microsoft **Azure AD Connect Provisioning Agent** estão lá e o estado está a **funcionar**.
![Serviços](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configurar a azure AD Connect provisões de nuvem
Utilize os seguintes passos para configurar o provisionamento:

 1. Inscreva-se no portal AD AZure.
 2. Clique **em Azure Ative Directory**
 3. Clique **em Azure Ad Connect**
 4. **Selecione Gerir o fornecimento (Pré-visualização)** Screenshot 
  ![ mostrando o link "Gerir o provisionamento (pré-visualização)".](media/how-to-configure/manage1.png)</br>
 5.  Clique em Novo Screenshot de **Configuração** 
  ![ do ecrã Azure AD Provisioning (Preview) com o link "Nova configuração" realçado.](media/tutorial-single-forest/configure1.png)</br>
 6.  No ecrã de configuração, introduza um **e-mail de Notificação,** mova o seletor para **Ativar** e clique em **Guardar**.
 ![Screenshot do ecrã configurar com e-mail de notificação preenchido e Ativar selecionado.](media/tutorial-single-forest/configure2.png)</br>
 7. Em **Configuração**, selecione **Todos os utilizadores** para alterar o âmbito da regra de configuração.
 ![Screenshot do ecrã configurar com "Todos os utilizadores" em destaque ao lado de "Utilizadores de Âmbito".](media/how-to-configure/scope2.png)</br>
 8. À direita, altere o âmbito para incluir o OU específico que acabou de criar "OU=CPUsers,DC=contoso,DC=com".
 ![Screenshot do ecrã dos utilizadores do Scope realçando o âmbito alterado para o OU que criou.](media/tutorial-existing-forest/scope2.png)</br>
 9.  Clique **em Fazer** e **Guardar**.
 10. O âmbito deve agora ser definido para uma unidade organizacional. 
 ![Screenshot do ecrã configurar com "1 unidade organizacional" em destaque ao lado de "Utilizadores de Âmbito".](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Verifique se os utilizadores são a provisionados por provisões em nuvem
Irá agora verificar se os utilizadores que tinha no nosso diretório no local foram sincronizados e agora existem no inquilino AD AZure.  Esteja ciente de que isto pode levar algumas horas para ser concluído.  Para verificar se os utilizadores estão a provisões em nuvem, siga estes passos:

1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Ative Directory**
3. Clique em **Azure AD Connect**
4. Clique em **Gerir o provisionamento (pré-visualização)**
5. Clique no botão **Registars**
6. Procure um nome de utilizador para confirmar que o utilizador é a provisionado por provisão em nuvem

Além disso, pode verificar se o utilizador e o grupo existem no Azure AD.

## <a name="start-the-scheduler"></a>Inicie o programador
A azure AD Connect sincroniza as alterações ocorridas no seu diretório no local utilizando um programador. Agora que modificou as regras, pode recomeçar o programador.  Utilize os passos seguintes:

1.  No servidor que está a executar Azure AD Connect sync open PowerShell com Privilégios Administrativos
2.  Execute `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Execute `Start-ADSyncSyncCycle`.  Acerte entrar.  

>[!NOTE] 
>Se estiver a executar o seu próprio programador personalizado para sincronização Azure AD Connect, por favor, ative o programador. 

Uma vez ativado o programador, o Azure AD Connect deixará de exportar quaisquer alterações nos objetos com `cloudNoFlow=true` os metaversos, a menos que qualquer atributo de referência (por exemplo. manager) está a ser atualizado. Caso exista alguma atualização de atributos de referência no objeto, o Azure AD Connect ignorará o `cloudNoFlow` sinal e exportará todas as atualizações do objeto.

## <a name="something-went-wrong"></a>Algo correu mal
Caso o piloto não funcione como esperado, pode voltar à configuração de sincronização Azure AD Connect seguindo os passos abaixo:
1.  Desativar a configuração de provisionamento no portal Azure. 
2.  Desative todas as regras de sincronização personalizadas criadas para o Fornecimento de Nuvens utilizando a ferramenta Sync Rule Editor. A desativação deve causar uma sincronização completa em todos os conectores.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Configure Azure AD Connect sync para excluir o piloto OU
Uma vez verificado que os utilizadores do piloto ou são geridos com sucesso através do provisionamento em nuvem, pode voltar a configurar o Azure AD Connect para excluir o piloto OU que foi criado acima.  O agente de provisionamento em nuvem tratará da sincronização para estes utilizadores.  Utilize os seguintes passos para o âmbito Azure AD Connect.

 1. No servidor que está a executar Azure AD Connect, clique duas vezes no ícone Azure AD Connect.
 2. Clique **em Configurar**
 3. **Selecione Personalizar opções de sincronização** e clique em seguida.
 4. Iniciar sação no AD AZure e clicar em **Seguinte**.
 5. No ecrã **'Ligar os directórios'** clique em **Seguinte**.
 6. No ecrã **de filtragem Domain e OU,** selecione **Sync domínios e OUs selecionados**.
 7. Expanda o seu domínio e **desescoda** os **CPUsers** OU.  Clique em **Seguinte**.
![âmbito](media/tutorial-existing-forest/scope1.png)</br>
 9. No ecrã **de funcionalidades opcionais,** clique em **Seguinte**.
 10. No ecrã **pronto a configurar** clique em **Configurar**.
 11. Uma vez concluído, clique em **Sair**. 

## <a name="next-steps"></a>Próximos passos 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

